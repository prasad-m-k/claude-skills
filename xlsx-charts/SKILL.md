---
name: xlsx-charts
description: "Use this skill whenever a user wants to add, create, or modify charts inside an Excel (.xlsx) file. Triggers include: 'add a chart', 'create a bar/line/pie/scatter/area chart', 'plot this data in Excel', 'visualize in spreadsheet', 'embed a chart', 'chart on its own sheet', or any request where the deliverable is an Excel file containing a rendered chart object. Also triggers when the user asks to change chart type, recolor series, add data labels, add a secondary axis, or create a combo chart. Do NOT trigger for: HTML charts, matplotlib/plotly output, chart images, or cases where the deliverable is not an .xlsx file."
---

# xlsx-charts Skill

## Quick Reference

| Task | Key API |
|---|---|
| Bar / Column | `BarChart()` — `bc.type="col"` or `"bar"` |
| Line | `LineChart()` — optionally `smooth=True` |
| Pie | `PieChart()` — explode with `DataPoint(explosion=N)` |
| Scatter / XY | `ScatterChart()` + `Series(y, x)` |
| Area | `AreaChart()` |
| Combo (bar + line) | `bar += line` after setting `line.y_axis.axId` |
| Data reference | `Reference(ws, min_col, min_row, max_row[, max_col])` |
| Place chart | `ws.add_chart(chart, "A9")` — cell = top-left corner |

---

## Environment

```python
# openpyxl 3.1.5 — always available
from openpyxl import Workbook, load_workbook
from openpyxl.chart import (
    BarChart, LineChart, PieChart, ScatterChart, AreaChart,
    Reference, Series
)
from openpyxl.chart.series import DataPoint
from openpyxl.chart.label import DataLabelList
from openpyxl.chart.marker import Marker
from openpyxl.drawing.spreadsheet_drawing import TwoCellAnchor, AnchorMarker
```

---

## The #1 Rule: Reference Row Alignment

This is the most common source of broken charts. Follow this pattern exactly:

```python
# Data layout (ws):
#   Row 1  →  header: "Month", "Sales", "Cost"
#   Row 2+ →  data:   "Jan",   100,     60
#   ...
#   Row N  →  last data row

# ✅ CORRECT
data = Reference(ws, min_col=2, max_col=3, min_row=1, max_row=N)  # includes header row
chart.add_data(data, titles_from_data=True)                         # header → series title
cats = Reference(ws, min_col=1, min_row=2, max_row=N)              # skip header for categories
chart.set_categories(cats)

# ❌ WRONG — header becomes a data point (shows as 0 or garbled)
data = Reference(ws, min_col=2, max_col=3, min_row=1, max_row=N)
chart.add_data(data, titles_from_data=False)   # header treated as data
```

**Rule:** `data` Reference always starts at `min_row=1` (header) **and** `titles_from_data=True`. Categories Reference always starts at `min_row=2` (first data row).

---

## Chart Types

### Bar / Column Chart

```python
from openpyxl.chart import BarChart, Reference

bc = BarChart()
bc.type      = "col"        # "col" = vertical bars, "bar" = horizontal bars
bc.grouping  = "clustered"  # "clustered" | "stacked" | "percentStacked" | "standard"
bc.overlap   = 100          # required when grouping="stacked" or "percentStacked"
bc.title     = "Monthly Sales"
bc.y_axis.title = "Units"
bc.x_axis.title = "Month"
bc.style     = 10           # 1–48; 10 is a clean default
bc.width     = 20           # cm
bc.height    = 14           # cm

data = Reference(ws, min_col=2, max_col=4, min_row=1, max_row=6)
bc.add_data(data, titles_from_data=True)
bc.set_categories(Reference(ws, min_col=1, min_row=2, max_row=6))
ws.add_chart(bc, "A9")
```

**Grouping options:**

| `grouping` | `type` | Effect |
|---|---|---|
| `"clustered"` | `"col"` | Side-by-side vertical bars |
| `"stacked"` | `"col"` | Stacked vertical bars |
| `"percentStacked"` | `"col"` | 100% stacked; set `y_axis.numFmt = "0%"` |
| `"clustered"` | `"bar"` | Side-by-side horizontal bars |
| `"stacked"` | `"bar"` | Stacked horizontal bars |

---

### Line Chart

```python
from openpyxl.chart import LineChart, Reference

line = LineChart()
line.title   = "Revenue Trend"
line.style   = 10
line.smooth  = True          # bezier curves; omit or False for straight lines
line.y_axis.title = "$ (k)"
line.width   = 22
line.height  = 14

data = Reference(ws, min_col=2, max_col=4, min_row=1, max_row=6)
line.add_data(data, titles_from_data=True)
line.set_categories(Reference(ws, min_col=1, min_row=2, max_row=6))

# Optional: style individual series
line.series[0].graphicalProperties.line.solidFill = "4472C4"   # hex color, no #
line.series[0].graphicalProperties.line.width     = 25000      # EMUs; 25000 ≈ 2pt
line.series[1].graphicalProperties.line.dashDot   = "dash"     # dashed line

ws.add_chart(line, "A9")
```

**Dash styles:** `"solid"` `"dot"` `"dash"` `"dashDot"` `"lgDash"` `"lgDashDot"`

---

### Pie Chart

```python
from openpyxl.chart import PieChart, Reference
from openpyxl.chart.series import DataPoint

pie = PieChart()
pie.title  = "Market Share"
pie.style  = 10
pie.width  = 18
pie.height = 14

data = Reference(ws, min_col=2, min_row=1, max_row=5)   # header + 4 data rows
cats = Reference(ws, min_col=1, min_row=2, max_row=5)
pie.add_data(data, titles_from_data=True)
pie.set_categories(cats)

# Explode a slice (pull it out from center)
pie.series[0].data_points = [DataPoint(idx=0, explosion=20)]  # idx=0 = first slice

# Color individual slices
colors = ["4472C4", "ED7D31", "A9D18E", "FF0000"]
pie.series[0].data_points = [DataPoint(idx=i) for i in range(4)]
for i, dp in enumerate(pie.series[0].data_points):
    dp.graphicalProperties.solidFill = colors[i]

ws.add_chart(pie, "A8")
```

> **Note:** Pie charts take exactly one data series. If `max_col > min_col`, only the first series is used.

---

### Scatter / XY Chart

```python
from openpyxl.chart import ScatterChart, Reference, Series
from openpyxl.chart.marker import Marker

scatter = ScatterChart()
scatter.title   = "Correlation"
scatter.style   = 10
scatter.x_axis.title = "X"
scatter.y_axis.title = "Y"
scatter.width   = 20
scatter.height  = 14

# Scatter requires explicit Series(yvalues, xvalues) — NOT add_data()
xvalues = Reference(ws, min_col=1, min_row=2, max_row=11)
yvalues = Reference(ws, min_col=2, min_row=2, max_row=11)
series  = Series(yvalues, xvalues, title="Dataset A")

# Markers only (no connecting line)
series.marker = Marker(symbol="circle", size=7)
series.graphicalProperties.line.noFill = True

scatter.series.append(series)
ws.add_chart(scatter, "D2")
```

**Marker symbols:** `"circle"` `"diamond"` `"square"` `"triangle"` `"plus"` `"star"` `"x"` `"dot"` `"dash"` `"none"` `"auto"`

---

### Area Chart

```python
from openpyxl.chart import AreaChart, Reference

area = AreaChart()
area.title    = "Cumulative Growth"
area.style    = 10
area.grouping = "stacked"   # "standard" | "stacked" | "percentStacked"
area.width    = 22
area.height   = 14

data = Reference(ws, min_col=2, max_col=3, min_row=1, max_row=6)
area.add_data(data, titles_from_data=True)
area.set_categories(Reference(ws, min_col=1, min_row=2, max_row=6))
ws.add_chart(area, "A9")
```

---

## Combo Chart (Bar + Line, Dual Axis)

A combo chart requires creating two charts and merging with `+=`. The secondary axis must get a distinct `axId`.

```python
from openpyxl.chart import BarChart, LineChart, Reference

# --- Data layout ---
# Col 1: Month, Col 2: Revenue, Col 3: Units, Col 4: Margin%

# Primary chart: bar for Revenue
bar = BarChart()
bar.type      = "col"
bar.grouping  = "clustered"
bar.title     = "Revenue & Margin"
bar.style     = 10
bar.width     = 22
bar.height    = 14
bar.y_axis.title = "Revenue ($k)"

rev_ref = Reference(ws, min_col=2, min_row=1, max_row=N)
bar.add_data(rev_ref, titles_from_data=True)
cats = Reference(ws, min_col=1, min_row=2, max_row=N)
bar.set_categories(cats)

# Secondary chart: line for Margin% on right axis
line = LineChart()
margin_ref = Reference(ws, min_col=4, min_row=1, max_row=N)
line.add_data(margin_ref, titles_from_data=True)
line.set_categories(cats)

line.y_axis.axId   = 200            # must differ from bar's axId (default 100)
line.y_axis.title  = "Margin %"
line.y_axis.crosses = "max"         # plot secondary axis on right side
line.y_axis.numFmt = "0%"

# Style the line
line.series[0].graphicalProperties.line.solidFill = "FF0000"
line.series[0].graphicalProperties.line.width     = 25000

# Merge: line overlaid on bar
bar += line
ws.add_chart(bar, "A9")
```

> **Key:** `line.y_axis.crosses = "max"` moves the secondary axis to the right. Without it, both axes overlap on the left.

---

## Formatting Reference

### Series Colors

```python
# Solid fill for bar series
chart.series[0].graphicalProperties.solidFill = "4472C4"   # no # prefix

# Line color
chart.series[0].graphicalProperties.line.solidFill = "ED7D31"
chart.series[0].graphicalProperties.line.width     = 25000  # EMUs (25000 ≈ 2pt)
```

**Common palette (Office defaults):**

| Color | Hex |
|---|---|
| Blue | `4472C4` |
| Orange | `ED7D31` |
| Grey | `A5A5A5` |
| Yellow | `FFC000` |
| Light blue | `5B9BD5` |
| Green | `70AD47` |

---

### Axis Formatting

```python
chart.y_axis.numFmt         = "$#,##0"   # currency
chart.y_axis.numFmt         = "0%"       # percentage
chart.y_axis.numFmt         = "#,##0"    # thousands with comma
chart.y_axis.majorGridlines = None       # suppress gridlines
chart.x_axis.title          = "Quarter"
chart.y_axis.title          = "Revenue"
chart.y_axis.scaling.min    = 0          # force axis to start at 0
chart.y_axis.scaling.max    = 100        # cap axis at 100
```

---

### Legend Position

```python
chart.legend.position = "b"   # b=bottom, l=left, r=right, t=top, tr=top-right
```

---

### Data Labels

```python
from openpyxl.chart.label import DataLabelList

chart.dataLabels           = DataLabelList()
chart.dataLabels.showVal   = True    # show value
chart.dataLabels.showCatName = True  # show category name
chart.dataLabels.showSerName = False # hide series name
chart.dataLabels.showPercent = True  # % — useful on pie charts
```

---

### Chart Sizing & Placement

**Simple placement** — chart top-left at a cell, width/height in cm:

```python
chart.width  = 20   # cm; default ≈ 15
chart.height = 14   # cm; default ≈ 7.5
ws.add_chart(chart, "E2")   # top-left corner at E2
```

**Precise sizing** — anchor to an exact cell range (TwoCellAnchor):

```python
from openpyxl.drawing.spreadsheet_drawing import TwoCellAnchor, AnchorMarker

anchor        = TwoCellAnchor()
anchor._from  = AnchorMarker(col=4, row=1, colOff=0, rowOff=0)  # E2 (0-indexed)
anchor.to     = AnchorMarker(col=15, row=19, colOff=0, rowOff=0) # P20
chart.anchor  = anchor
ws.add_chart(chart)   # no cell string when using TwoCellAnchor
```

> `AnchorMarker` col and row are **0-indexed** (col=0 → A, row=0 → row 1).

---

## Multiple Charts on One Sheet

Place each chart at a different non-overlapping cell. Width/height in cm guide spacing:

```python
ws.add_chart(chart1, "A1")   # chart1 occupies roughly A1:P20 at 20cm wide × 14cm tall
ws.add_chart(chart2, "Q1")   # chart2 starts after chart1
ws.add_chart(chart3, "A22")  # chart3 below chart1
```

Estimate columns needed: `width_cm / 0.71 ≈ columns`. Estimate rows: `height_cm / 0.53 ≈ rows`.

---

## Cross-Sheet Charts (Data on Different Sheet)

```python
wb = Workbook()
ws_data  = wb.active;          ws_data.title  = "Data"
ws_chart = wb.create_sheet("Charts")

# ... populate ws_data ...

bc = BarChart()
# Reference points to ws_data even though chart lives on ws_chart
data = Reference(ws_data, min_col=2, min_row=1, max_row=10)
cats = Reference(ws_data, min_col=1, min_row=2, max_row=10)
bc.add_data(data, titles_from_data=True)
bc.set_categories(cats)

ws_chart.add_chart(bc, "A1")   # add to the chart sheet
```

---

## Pitfalls & Fixes

| Pitfall | Symptom | Fix |
|---|---|---|
| `titles_from_data=False` with header in range | Header row shows as a `0` bar / extra data point | Always use `titles_from_data=True` when `min_row` includes the header |
| Categories include header row | X-axis label says "Month" as first tick | Start categories at `min_row=2` |
| Scatter using `add_data()` | Points don't plot correctly | Use `Series(y_ref, x_ref)` + `series.append()` |
| Combo: secondary axis on left | Both axes on left, overlapping | Set `line.y_axis.crosses = "max"` |
| Combo: missing `axId` | Lines render but share axis | Set `line.y_axis.axId = 200` (any value ≠ primary) |
| Color set with `#` prefix | Color ignored silently | Strip `#`: `"4472C4"` not `"#4472C4"` |
| `TwoCellAnchor` with cell string | Chart misplaced | Call `ws.add_chart(chart)` with no cell arg |
| `DataPoint` colors on bar | Nothing changes | Use `series.graphicalProperties.solidFill` not `DataPoint` for bar series |

---

## Complete Working Examples

### Example 1: Clustered Column from Existing File

```python
from openpyxl import load_workbook
from openpyxl.chart import BarChart, Reference

wb = load_workbook("sales.xlsx")
ws = wb.active

# Assume: row 1 = headers, rows 2-13 = monthly data, col 1 = month, cols 2-4 = products
last_row = ws.max_row

bc = BarChart()
bc.type      = "col"
bc.grouping  = "clustered"
bc.title     = "Product Sales by Month"
bc.style     = 10
bc.width     = 22
bc.height    = 14
bc.y_axis.title = "Units Sold"
bc.x_axis.title = "Month"

data = Reference(ws, min_col=2, max_col=4, min_row=1, max_row=last_row)
bc.add_data(data, titles_from_data=True)
bc.set_categories(Reference(ws, min_col=1, min_row=2, max_row=last_row))

ws.add_chart(bc, "F2")
wb.save("sales_with_chart.xlsx")
```

### Example 2: Combo Chart (Bar + Line, Dual Axis) from Scratch

```python
from openpyxl import Workbook
from openpyxl.chart import BarChart, LineChart, Reference

wb = Workbook()
ws = wb.active
ws.title = "Dashboard"

rows = [
    ["Month", "Revenue", "Costs", "Margin%"],
    ["Jan",   500,       380,     0.24],
    ["Feb",   620,       460,     0.26],
    ["Mar",   580,       420,     0.28],
    ["Apr",   700,       500,     0.29],
    ["May",   750,       530,     0.29],
]
for row in rows:
    ws.append(row)

N = len(rows)  # 6
cats = Reference(ws, min_col=1, min_row=2, max_row=N)

# Bar: Revenue & Costs
bar = BarChart()
bar.type     = "col"
bar.grouping = "clustered"
bar.title    = "Revenue, Costs & Margin"
bar.style    = 10
bar.width    = 24
bar.height   = 14
bar.y_axis.title = "Amount ($)"
bar.add_data(Reference(ws, min_col=2, max_col=3, min_row=1, max_row=N), titles_from_data=True)
bar.set_categories(cats)

# Line: Margin% on secondary axis
line = LineChart()
line.add_data(Reference(ws, min_col=4, min_row=1, max_row=N), titles_from_data=True)
line.set_categories(cats)
line.y_axis.axId    = 200
line.y_axis.title   = "Margin %"
line.y_axis.crosses = "max"
line.y_axis.numFmt  = "0%"
line.series[0].graphicalProperties.line.solidFill = "FF0000"
line.series[0].graphicalProperties.line.width     = 28000

bar += line
ws.add_chart(bar, "A9")
wb.save("dashboard.xlsx")
```

### Example 3: Pie Chart with Exploded Slice and Data Labels

```python
from openpyxl import Workbook
from openpyxl.chart import PieChart, Reference
from openpyxl.chart.series import DataPoint
from openpyxl.chart.label import DataLabelList

wb = Workbook()
ws = wb.active
ws.append(["Region",  "Revenue"])
ws.append(["North",   35])
ws.append(["South",   25])
ws.append(["East",    22])
ws.append(["West",    18])

pie = PieChart()
pie.title  = "Revenue by Region"
pie.style  = 10
pie.width  = 18
pie.height = 14

pie.add_data(Reference(ws, min_col=2, min_row=1, max_row=5), titles_from_data=True)
pie.set_categories(Reference(ws, min_col=1, min_row=2, max_row=5))

# Explode top region
pie.series[0].data_points = [DataPoint(idx=0, explosion=15)]

# Show % labels
pie.dataLabels             = DataLabelList()
pie.dataLabels.showPercent = True
pie.dataLabels.showCatName = True

ws.add_chart(pie, "D2")
wb.save("regional_pie.xlsx")
```
