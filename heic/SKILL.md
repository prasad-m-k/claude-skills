---
name: heic
description: >
  Use this skill whenever the user uploads or references a HEIC or HEIF file, or asks to
  work with iPhone/Apple photos. Triggers include: any mention of .heic, .heif, "iPhone photo",
  "Apple photo format", or when an uploaded file has a .heic/.heif extension. Use this skill
  to read, convert, resize, crop, analyze, or otherwise process HEIC/HEIF images — even if
  the user just says "here's a photo from my phone" and the file is HEIC. Always use this
  skill before attempting any image operation on a HEIC file, as standard tools won't work
  without the setup steps described here.
---

# HEIC / HEIF Image Skill

Apple's HEIC (High Efficiency Image Container) format requires special handling on Linux.
This skill covers everything needed to read, process, and convert HEIC/HEIF files.

---

## Step 1: Environment Setup (always do this first)

```python
import pillow_heif
pillow_heif.register_heif_opener()  # Teaches Pillow to open .heic/.heif files
from PIL import Image
```

- `pillow-heif` is pre-installed in this environment (version 1.3.0, libheif 1.21.2).
- Calling `register_heif_opener()` is **required** before any `Image.open()` on a HEIC file.
- After registration, standard Pillow operations work normally on HEIC images.

---

## Step 2: Reading HEIC Files

```python
pillow_heif.register_heif_opener()
from PIL import Image

img = Image.open("/mnt/user-data/uploads/photo.heic")
print(img.size, img.mode)  # e.g. (4032, 3024), 'RGB'
```

**EXIF / metadata:**
```python
exif_data = img.getexif()  # Returns Pillow Exif object
# For richer EXIF (GPS, camera model, etc.), use the exifread or piexif libraries
```

---

## Step 3: Common Operations

### Convert to JPEG or PNG
```python
pillow_heif.register_heif_opener()
from PIL import Image

img = Image.open("/mnt/user-data/uploads/photo.heic")
if img.mode in ("RGBA", "P"):
    img = img.convert("RGB")  # JPEG doesn't support alpha
img.save("/mnt/user-data/outputs/photo.jpg", "JPEG", quality=90)
# or: img.save(..., "PNG")
```

### Resize
```python
img_resized = img.resize((1280, 960), Image.LANCZOS)
img_resized.save("/mnt/user-data/outputs/photo_small.jpg", quality=85)
```

### Crop
```python
# (left, upper, right, lower)
cropped = img.crop((100, 100, 800, 600))
```

### Thumbnail (preserves aspect ratio)
```python
img.thumbnail((1024, 1024), Image.LANCZOS)
```

### Batch convert a folder
```python
import os
pillow_heif.register_heif_opener()
from PIL import Image

input_dir = "/mnt/user-data/uploads/"
output_dir = "/mnt/user-data/outputs/"

for fname in os.listdir(input_dir):
    if fname.lower().endswith((".heic", ".heif")):
        img = Image.open(os.path.join(input_dir, fname))
        if img.mode not in ("RGB", "L"):
            img = img.convert("RGB")
        out_name = os.path.splitext(fname)[0] + ".jpg"
        img.save(os.path.join(output_dir, out_name), "JPEG", quality=90)
        print(f"Converted: {fname} → {out_name}")
```

---

## Step 4: Working Directly on HEIC (no conversion needed)

After `register_heif_opener()`, you can apply **any Pillow operation** directly on the
opened HEIC image without saving as JPEG first:

- Filters: `img.filter(ImageFilter.BLUR)`
- Color adjustments: `ImageEnhance.Brightness(img).enhance(1.5)`
- Compositing, drawing, analysis — all work normally
- Save back as HEIC: `img.save("out.heic")` *(requires encode support; JPEG output is safer)*

---

## Troubleshooting

| Problem | Fix |
|---|---|
| `UnidentifiedImageError` | Forgot `register_heif_opener()` — add it before `Image.open()` |
| `OSError: encoder error` saving HEIC | Save as JPEG/PNG instead; HEIC encoding needs x265 |
| Green/corrupt colors | Convert mode: `img.convert("RGB")` before saving |
| Missing EXIF GPS data | Use `pillow-heif`'s native API: `heif_file = pillow_heif.open_heif(path)` |

---

## Dependencies

- `pillow-heif` — already installed (`pip install pillow-heif --break-system-packages`)
- `Pillow` — already installed
- No system packages needed (libheif bundled in the wheel)
