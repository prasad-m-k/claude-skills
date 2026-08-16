---
name: pizza-builder
description: Use when a request has no single obvious interpretation and a wrong guess would waste a real generation cycle, on any topic ("write a novel," "design a logo," "tune my API performance," "improve my UI/UX," "give me a prompt for X" are illustrations, not the full list). Trigger is structural: no obvious interpretation, a wrong guess costs a real redo, 3+ real decision points. ROUTING RULE, check first: when met, build the interactive HTML artifact immediately. Do NOT call ask_user_input_v0 first and escalate later, that's the most common failure here, and more domain names won't fix it. ask_user_input_v0 is only for the narrow exception: 1-2 real decision points. If the user's words name the deliverable as a "prompt" or something to hand off elsewhere, that alone triggers the artifact regardless of decision count. Do NOT use for simple, low-cost requests or when constraints are already given.
---

# Pizza Builder

## What this is

A "build your own pizza" counter works because someone already figured out the handful of decisions that matter (base, sauce, cheese, toppings) and narrowed each to a small set of good options. You don't invent a pizza from nothing, you compose one from a menu that was designed well.

This skill applies the same structure to vague creative requests. Instead of asking "can you clarify what you mean?" (which puts the thinking burden back on the user) or silently guessing (which risks a full wasted generation), it does the hard part itself: identifying the small number of dimensions that actually shape the outcome, and filling each with specific, imaginative options the user can react to instantly.

The creative work is in building a good menu, not in making the user fill out a form.

## When to use it

Trigger on open-ended creative or design requests where:
- There's no single obvious interpretation
- Getting it wrong would cost a real redo (a full draft, an image, a multi-slide deck, a designed layout)
- The request has 3+ independent decision points, not just one missing detail

Do NOT trigger for:
- Requests with a clear default ("write a landing page headline" → just write 2-3 and move on)
- Requests where the user already specified the parameters
- Low-cost, cheap-to-redo tasks (a tweet, a one-line rename, a quick answer)
- Judgment calls where the user wants your actual opinion, not a menu ("should I use Python or JavaScript?")

If in doubt, ask yourself: would a wrong guess here waste more of the user's time than one tap-to-answer screen? If yes, use this skill.

### Routing to the interactive artifact, decided by structure, never by domain

Don't route on whether a request "sounds technical" or matches a remembered list of domains, keyword or domain lists always miss the next thing (UI/UX got missed this way once, and the next miss would just be some other unlisted domain, Mars rover or otherwise). Route on structure instead, using the same criteria "When to use it" already defines: is there no single obvious interpretation, would a wrong guess cost a real redo, and are there 3+ real decision points. Nothing about that test names a domain, so it doesn't need a domain list to stay correct on a topic nobody's thought of yet.

**When those criteria are met at all, the interactive HTML artifact is the default output, full stop.** `ask_user_input_v0` is the narrow exception, reserved only for requests that structurally have just 1-2 real decision points, small enough that stacked buttons cost nothing extra. It is not a first draft to try before "escalating" to the artifact, and it is never chosen because the topic doesn't obviously look technical. If a request clears the 3+ decision point bar, build the artifact directly, first move, no intermediate step.

One structural signal is always sufficient on its own regardless of decision-point count: if the user's own words say "prompt" as the deliverable, or otherwise name something meant to hand off elsewhere (to another model, an API, a teammate, a ticket), that alone means build the artifact, since a prompt handed to someone else is exactly the case the bottom panel exists for.

## How to build the menu

### Step 1: Identify the dimensions that matter

Don't guess generically. Think like the domain expert would. For a novel, a chef doesn't ask "what toppings?" in the abstract, they know pizza has base/sauce/cheese/toppings as the load-bearing dimensions. Do the same:

- **Novel/story**: genre, protagonist's core drive (motive), central theme or tension, tone/voice
- **Visual/image**: subject or setting, style (photoreal / illustrated / cartoon / painterly), palette or mood, composition or framing
- **Landing page / marketing**: audience, primary emotional hook, tone (playful / authoritative / urgent), visual style
- **Trip planning**: pace (packed / relaxed), focus (food / culture / nature / nightlife), budget tier, group type
- **Room/space design**: era or style, color direction, mood, functional priority
- **Song/music**: genre, mood, subject matter, vocal/instrumental character
- **Code architecture**: scale target, consistency vs. availability tradeoff, deployment constraint, team size

This list is a starting point, not a lookup table. For anything not covered above, reason it out the same way: what are the load-bearing dimensions a domain expert would consider, the ones where a different choice produces a genuinely different deliverable, not just a cosmetic variation?

**The dimension people forget: who's this actually for, and what's it going to be used for.** A "beautiful sunset" for a children's book cover, a gallery art print, a corporate slide background, and a phone wallpaper are four different images, not variations on one image. Audience and end-use change the rendering style, the complexity, the safety/tone register, and sometimes the aspect ratio itself, before a single color choice matters. This isn't always its own dimension, sometimes it folds into "style" (as it does in the visual/image example above), but check explicitly: would a professional doing this for a living ask "who's this for?" before starting? If a domain expert would ask that question first, on paper or out loud, it belongs on the menu, don't let it get silently assumed.

Most requests genuinely have 3-4 load-bearing dimensions, and that's a reasonable place to stop by default. Some requests, deliberately technical handoffs or an explicit ask for maximum extraction, have more, and Step 1b through the sections below cover how to handle that honestly. Either way, stop adding dimensions once they stop being load-bearing, more than that and it stops feeling like a menu and starts feeling like paperwork.

**Bias toward one thorough round, not toward a short list.** The whole point of this skill is to replace a chain of round trips with one screen. That fails quietly if the dimension list is merely "good enough for a decent menu" instead of "everything a domain expert would need before starting real work." Before finalizing the dimension list, run a second pass and ask: if I handed this exact combination of picks to a professional in this field and told them to start immediately, what would they still have to stop and ask me? Context and environment facts are the most common thing left off, not because they're hard to think of, but because they don't feel as "creative" as style or tone choices, so they get silently skipped. For a home, that's the site (urban, suburban, rural, sloped) and orientation constraints. For a novel, that's point of view and target length. For a marketing page, that's the platform it'll run on and any brand constraints already locked. For code architecture, that's the existing stack it has to integrate with. If a domain expert would ask it in the first five minutes of a real engagement, it belongs on the menu now, not in a follow-up question after the first draft ships. A dimension discovered after the fact costs the user exactly the round trip this skill exists to remove.

**Known numeric or named values are a free-text field, not a rounded-off label.** When a dimension has a real quantity behind it (a budget, a square footage, a headcount, a deadline), don't collapse it into 3-4 fuzzy size buckets ("small / medium / large") as the only option. Someone who already knows the number shouldn't have to reverse-engineer which bucket it falls into, and someone who doesn't know it yet shouldn't be forced to fake precision. Give the free-text field per Step 1b's own "Free text" shape, with a placeholder showing the expected format, and let "leave blank" resolve to a stated working range in the synthesis, not a silent guess.

### Step 1b: For each dimension, decide its shape honestly

Don't default every dimension to "pick one of four labels." A real domain expert's menu has some items that are optional, some that mix, some that are on/off, and some that are genuinely exclusive. This classification is topic-agnostic, run it fresh for every dimension on every request regardless of subject, never by matching the request against a remembered domain. Before filling in options, classify each dimension into one of these shapes:

- **Boolean (toggle)**: a plain yes/no, on/off, include/exclude fact about the thing being built, "does this need to survive a reboot," "should this be reversible," "is backward compatibility required." Render as a two-state toggle or switch, not a radio pair dressed up as two options, since the binary nature is itself information the user should see at a glance.
- **Exclusive pick (radio)**: a short list (roughly 2-5) of mutually incompatible options, picking one rules out the others. A database is strict-consistency or eventual, not both. Most dimensions land here by default.
- **Scenario (dropdown)**: the same exclusive-pick logic as radio, but the option set is long (roughly 6 or more genuinely distinct choices) or open-ended enough that a vertical list of radio cards would overwhelm the screen, a cloud region, a specific named technology out of a long field, a country. Use a `<select>` dropdown instead of stacked cards so the picker stays scannable, but keep the same one-clear-label-per-option standard.
- **Optional / can be skipped ("none")**: something a domain expert would sometimes actively recommend *against* having at all. Cache is the clean example: a real architect doesn't always add a cache, sometimes the honest, load-bearing recommendation is "don't, here's why, here's when you'd revisit." If a dimension has a real "skip this" answer that isn't just laziness, include it as its own labeled option (e.g. "No cache, query the database directly", not just a blank/default state), and give it a genuine recommendation, not a placeholder. Test: would omitting this dimension entirely ever be the *better* answer, not just the lazier one? If yes, it needs its own option, not a silent default.
- **Range / blend (slider, with combination percentages)**: a spectrum rather than a discrete pick. This covers two related cases: a two-flavor blend in proportion, "mostly noir with a little slapstick," "70% tomato, 30% pesto" (see the blend dimensions section below), and a single-axis intensity or degree dial with no second flavor, "how aggressive should this fix be," "how conservative vs experimental." Either way, render it as a slider with the live proportion or degree shown as a percentage, not a fixed list of pre-mixed labels standing in for a continuum.
- **Multi-select / additive (checkbox)**: several options can genuinely coexist and the deliverable should reflect all of them at once, "which constraints apply," "which downstream systems are involved." Render this as a checkbox group.
- **Free text**: a number, name, path, or anything else that isn't naturally a short categorical list, a target metric, an endpoint name, a budget figure. Don't force this into buttons for visual consistency, a text field is the honest shape.

Reasoning like this for every dimension, and not defaulting everything to exclusive-radio, is what separates an expert's menu from a form. When in doubt about whether a dimension needs a "none" or "skip" option, ask what a senior practitioner in that domain would say if a junior asked "do I always need X?", if the honest answer is "no, sometimes you shouldn't," that possibility belongs on the menu.

### Step 1c: Every dimension gets a "you decide" option, and it has to actually decide something

Not everyone has an opinion on every axis. Forcing a pick on a dimension the user doesn't care about is friction the skill exists to remove. So every dimension, whatever shape it took in Step 1b, boolean, radio, dropdown, checkbox, or range, gets one more option beyond the domain-specific ones. The exact shape of that fallback follows the dimension's own shape:

- Boolean: a third state alongside on/off, "not sure, you decide" (not just defaulting silently to off).
- Radio / dropdown: an explicit extra option, "No preference, pick what works best."
- Checkbox: the group's exclusive "none/not sure" option per Step 1b, which also clears other picks in that group.
- Range / blend: a "not sure, land wherever fits best" endpoint or default marker on the slider itself, not just leaving it untouched at whatever the initial position happens to be, since an untouched slider looks like a deliberate pick.

The bar for that option is high: it cannot be a cop-out that dodges the decision back onto a second round-trip. When picked, the synthesis step (Step 4) has to make a real, specific, stated choice on the user's behalf, and say what it picked and one line on why, the same way a good waiter says "I'll bring you the fish, it's the better one tonight" instead of shrugging. If a "you decide" pick would leave the final deliverable vague on that axis, the option is being implemented wrong, fix the synthesis logic, not the option.

In the interactive picker mode below, this means `BASE` needs a real entry (or a clear resolution rule) for the "you decide" id on every axis pairing, not a blank or a placeholder like "TBD, ask Claude." In the extended intake form, an unresolved "not sure" instead tells the downstream agent to investigate and decide for itself, per that section's own guidance below.

### Step 2: Fill each dimension with specific, imaginative options, not generic labels

This is where Claude's creativity actually shows up, and it's the part that's easy to do lazily. Compare:

Weak: "Genre: Fantasy / Sci-fi / Mystery / Other"
Better: "Genre: Cozy small-town mystery / Hard sci-fi with a ticking clock / Literary fiction about a family secret / Epic fantasy with a political core"

The second version gives the user something to actually react to, an imagined story rather than a category label. Options should be concrete enough that picking one already tells you something about tone, stakes, and scope. Aim for 3-4 options per dimension, each one specific enough to spark a mental image, not so narrow that it reads as a random detail.

### Step 3: Present the menu

Per the routing rule above, how you present the menu depends on the decision-point count, not the topic:

- **3+ real decision points (the common case): build the interactive HTML artifact** (per "Building the interactive artifact" below) and present it directly. This is the default presentation for anything that reaches this step with real dimensions to show.
- **Only 1-2 real decision points**: use `ask_user_input_v0`, one question per dimension, keeping each question's options short (a few words each) since they render as tappable buttons, with the imaginative detail in the question text or a brief label rather than a long option string.

If a dimension genuinely benefits from an open-ended answer instead of a fixed menu, it's fine to phrase it as a free-text field (in the artifact) or an explicit "something else, I'll describe it" option (in the rare `ask_user_input_v0` case).

### Step 4: Use the picks to generate something specific, not generic

The payoff of this skill is the output, not the questionnaire. Once the user picks, don't just acknowledge their choices, fold them together into something that wouldn't have existed without that specific combination. "Cozy small-town mystery" + "protagonist driven by guilt over a past mistake" + "theme of small lies compounding" should produce a premise and opening that clearly reflects all three, not a bland mashup that mentions each keyword once.

This is the actual demonstration of skill: a good menu plus a sharp synthesis of the picks. Either half done badly wastes the other.

### Step 4b: The generated prompt must be an expert prompt, not a restated form

When the deliverable is a `PROMPT_TEMPLATE` the user pastes back into a chat (the interactive picker mode below), that prompt is itself a piece of work and needs the same bar as any other output. A generic prompt just restates the picks in a sentence ("I want X with Y under Z, please help"). An expert prompt does what a skilled practitioner asking a peer for help would do:

- States the picks as firm constraints, not vague preferences, using the domain's own vocabulary.
- Names what's already decided so the reader doesn't waste effort re-litigating it, and names specifically what's still open and needs real reasoning (edge cases, tradeoffs, the parts a static lookup table can't answer).
- Asks for the deliverable in the form the user will actually use (code, not a description of code, a shot list, not a description of a shot list) when the domain calls for that.
- Surfaces the failure mode or stress case relevant to the specific combination picked, not a generic "what should I watch out for." If the picks were "multi-instance" and "cache," the prompt should ask about cache coherence across instances by name, not "any gotchas?"
- Is something the user would be a little impressed to have not had to write themselves, it should read like it came from someone who already understood the problem, not like a mail-merged template.

If the generated prompt would work unchanged for a different set of picks, it's too generic. Rewrite it so it's clearly load-bearing on the exact combination chosen.

**Check the prompt for self-sabotage, not just genericness.** A prompt can be specific and still produce a bad result if it over-indexes on one technical instruction at the expense of the thing that actually makes the output recognizable. For image or design prompts specifically, technical language about gradients, bands, layers, or transitions can cause a generator to produce a literal abstract pattern instead of the recognizable subject, if nothing in the prompt anchors the subject itself (a horizon line and light source for a landscape, a face for a portrait, a silhouette for an object). Before finalizing a `PROMPT_TEMPLATE`, reread it and ask: if a generator followed this with no common sense, would it still produce the actual subject, or could the technical instructions alone produce something unrecognizable? If there's a real anchor the subject depends on, state it as a non-negotiable up front, don't let it get displaced by whatever technical detail comes later in the prompt.

### Step 4c: Pre-flight the prompt before you ship it, every time

This is not optional and not a one-time lesson learned from one bad sunset. Before presenting any `PROMPT_TEMPLATE` output to the user, mentally execute it as if you were the dumbest possible literal-minded generator with zero common sense, no domain knowledge, no ability to infer what was "obviously meant." Walk it end to end and ask: does every sentence in this prompt still point at the intended subject, or does any sentence, read in isolation, license a result that technically satisfies the words but loses the actual thing being asked for?

Concretely, check for these three failure shapes:

1. **Missing anchor.** The prompt describes texture, technique, or transitions in detail but never states the one or two structural facts that make the subject recognizable (a horizon and light source for a landscape, a face for a portrait, a readable silhouette for an object, working code that compiles for a programming task). Fix: state the anchor first, as a non-negotiable, before any stylistic detail.
2. **Self-contradiction.** One instruction quietly undercuts another (told to have "no horizon element" while also asking for "bands from horizon to zenith" is a real example that shipped and produced flat stripes instead of a sunset). Fix: read the whole prompt as one paragraph, not as separate bullet points, and resolve any tension explicitly rather than trusting the reader to reconcile it.
3. **Punt-back.** The generated text asks the reader to do the actual work instead of doing it ("can you write me a detailed prompt for this," "give me a prompt with camera terms, aspect ratio, and palette," "flag anything I should reconsider") rather than stating those specifics itself. This is a real regression that shipped: a sunset-image `PROMPT_TEMPLATE` generated the sentence "Can you write me a detailed, ready-to-use image generation prompt for this... and flag anything about the combination I should reconsider?", which passed every syntax and rendering check because the code ran fine, the text itself was just empty of content. Mechanical test: does the generated string contain a question mark aimed at the reader, or a phrase like "can you," "please help," "write me," "give me"? If so, it's asking for the work instead of delivering it, rewrite it as the direct specification (exact camera/rendering terms, exact aspect ratio, exact palette, stated outright) with no request embedded in it. The one exception is a request for a *deeper* pass beyond what a static lookup table can give (per Step 4b's "what's still open and needs real reasoning"), that's a legitimate ask for more, not a substitute for what the picks already determined.

Do this check silently as part of writing the template, the user should never see "let me verify this prompt," they should just get a prompt that already survived the check.

### Verification must include reading the generated text, not just running it

The mandatory verification section below checks that the code executes cleanly (no syntax errors, no `undefined`, controls work). That is necessary but not sufficient, code that runs perfectly can still generate a punt-back sentence, exactly what happened here. After running the headless simulation, actually read the resulting prompt text against the three failure shapes above before presenting the artifact. A prompt that runs clean and reads empty still fails.

### Step 5: Keep it skippable

Always allow a fast path. If the user says "just pick something good" or answers only some of the questions, generate a strong default for whatever's unanswered rather than re-prompting. The grid speeds things up, it should never become a gate.

## Worked example

User: "Let's write a novel."

Dimensions chosen: genre, protagonist's core motive, central theme, tone. Four dimensions, all load-bearing, all radio (exclusive pick) shaped, no boolean or long enumeration among them. Per the routing rule above, this clears 3+ decision points on its own topic-agnostic merits, novel-writing isn't on any special list, it just structurally qualifies, so it gets the interactive artifact, not stacked questions:

```
Section: What kind of story? (radio)
  - Cozy small-town mystery
  - Hard sci-fi with a ticking clock
  - Literary fiction, a family secret unraveling
  - Epic fantasy with a political core

Section: What drives your protagonist? (radio)
  - Guilt over something they did (or didn't do)
  - Ambition that costs them relationships
  - Revenge for a specific wrong
  - Simple survival, the stakes escalate around them

Section: What tone are you going for? (radio)
  - Wry and character-driven
  - Tense and propulsive
  - Slow-burn and atmospheric
  - Dark comedy

Section: Central theme (radio)
  - Small lies compounding
  - What's owed between family
  - The cost of ambition
  - Identity under pressure
```

If the user picks "literary fiction / guilt / slow-burn atmospheric / small lies compounding," the artifact's recommendation panel and generated prompt should reflect an actual premise and a sample opening line built from that exact combination, not a generic "great choice, let's begin" followed by boilerplate. This is true whether the synthesis happens in the artifact's `BASE` table or in a follow-up turn once the user pastes the generated prompt back.

## Building the interactive artifact (always built fresh, no shipped template)

There used to be a bundled `assets/grid-template.html` here. It's been removed. It kept causing real problems: its fixed renderers only supported 3 of the 6 dimension shapes in Step 1b, so every request had to be checked for compatibility against a file's specific limitations, and that check kept getting skipped or gotten wrong. A shipped template is one more thing that can silently drift out of sync with the rest of this skill. Building fresh every time means there's nothing to drift, the artifact is only ever built against the current rules in this document, not against a cached file's older assumptions.

This means every request gets a purpose-built HTML file, written from scratch, using the dimension shapes from Step 1b directly: boolean toggles, radio, dropdown, checkbox, range/blend sliders, free text, whichever shapes the actual dimensions call for, not whichever shapes a fixed file happened to implement. There's no separate "3-axis grid vs extended form" decision to make anymore, just:

1. **Identify the real dimensions** (Step 1, Step 1a, Step 1b) and classify each one's honest shape. However many that turns out to be, 3 or 8, build sections for exactly that many, don't pad and don't compress.
2. **Give every dimension a "you decide" fallback** (Step 1c) that resolves to something specific, or, when the deliverable hands off to another agent, an instruction for that agent to investigate rather than guess.
3. **Write specific, concrete options** for each dimension (Step 2), not generic category labels.
4. **Write the synthesis logic**: a data structure mapping picks to real, specific recommendations (not a placeholder table), and a function that assembles the final generated prompt from whatever was filled in, skipping anything left blank cleanly.
5. **Write that generated prompt to the standard in Step 4b and 4c**: firm constraints in the domain's own vocabulary, the real anchor stated first, no self-contradiction, and no punt-back asking the reader to do the work the prompt was supposed to do.
6. **Style it**: dark, functional, one clear label beside every control, no horizontal scroll, mobile-safe. Apply `frontend-design` judgment for anything domain-specific in the palette. Also include the pizza-slice progress indicator described below, it's the one signature visual this skill always carries regardless of domain.
7. **Verify it before presenting** (see below), every time, no exceptions.
8. **Present it** with `present_files` and a short explanation of what the dimensions are, so the person isn't left guessing what the form means before opening it.

Blend sliders (a ratio between two flavors, or a single intensity dial), checkbox groups with a clearing "none" option, and an extra dimension beyond the first few, all of this is still exactly the guidance in Step 1b and the sections below, it just gets implemented directly in whatever you're building this time, not copied from a file that has to match this one's exact variable names.

### The pizza-slice progress indicator (always included, built fresh, no bundled image assets)

This skill is named for the idea that a good menu is composed, not invented, so the artifact should carry a small, literal nod to that: a round pizza divided into as many slices as there are dimensions, drawn as inline SVG. Each slice starts empty (outline only) and fills in with color the moment its dimension gets a pick, whether that pick is a specific option or the explicit "no preference" choice, since both are real resolutions. When every slice is filled, the whole pizza does a brief celebratory beat (a short CSS pulse or a "ready!" label next to it), signaling the brief and prompt below are complete and ready to copy.

Build it inline every time, the same way the rest of the artifact is built fresh:
- Draw an SVG circle divided into N pie slices using `<path>` wedges (one per dimension, computed from `360 / N` degrees), not a raster image or an emoji font stand-in, so it always matches the actual dimension count instead of looking like a generic decoration.
- Unfilled slice: stroke only, transparent or muted fill (`var(--border)`-ish tone).
- Filled slice: solid fill in the artifact's accent color (or a small rotation of 2-3 accent tones so the pizza doesn't look flat), transitioning with a short CSS `fill` transition rather than snapping instantly.
- Wire it to the same `picks` state already driving the synthesis logic, don't keep a second parallel tracker, the slice fill state is a direct read of whether `picks[dim]` is set, free-text dimensions count as filled once the field is non-empty (or immediately if the field is genuinely optional and blank is a valid resolution).
- On full completion, add one small celebratory touch, a brief scale/opacity pulse on the whole SVG, or a "Your pizza's ready" line above the output section, kept tasteful and quick (under half a second), not a distracting animation loop.
- Keep it small (roughly 80-120px), positioned near the top or as a persistent corner element, it's a progress affordance, not the artifact's focal point, the actual menu and generated output stay the priority.

This is a styling instruction like the rest of Step 6, not a new bundled file, there's still no `assets/pizza.svg` or shipped template to drift out of sync. The wedge count and colors regenerate from that request's own dimension list and palette every single time.

### Blend dimensions (ratio sliders instead of single picks)

Some choices aren't really either/or, they're a mix: pesto and tomato sauce at 70/30, crime with a pinch of humor at 80/20. When a dimension is naturally a spectrum between two flavors rather than a set of mutually exclusive picks, implement it as two dropdowns (primary/secondary) plus a ratio slider with a live proportion bar, per Step 1b's range/blend shape.

Use this when the user's own language signals a blend, not a choice: "a bit of X with Y," "mostly A but some B," "50-50," "a pinch of." Don't force it onto genuinely exclusive options (a database is either strict or eventual consistency, not 70% of each), that's still a discrete pick.

The blended recommendation should look up both flavors' base recommendations and combine them into one note that turns the ratio into natural phrasing ("mostly, with a real undercurrent of," "as just a hint against") rather than a bare percentage. The generated prompt should inherit the exact ratio, so the ask is precise: "crime (80%) + dark comedy (20%)," not a vague "mix of crime and comedy."

### Radio vs. checkbox dimensions

Render each dimension as real radio buttons or checkboxes, not text labels, so the exclusivity of the choice is visible before the user taps anything.

- **Radio (default)**: only one option can be true within that dimension. Use this whenever the options are genuinely mutually exclusive, one database engine, one genre, one architecture path. Most dimensions land here.
- **Checkbox**: multiple options can be true at once within that dimension. Use this when the dimension is naturally additive rather than either/or, "which constraints apply to this project," "which features go in v1," "which audiences does this need to serve." The synthesis step then has to reconcile every selected combination into one coherent plan, not just list each pick back to back.

Decide per-dimension, not per-artifact: reason about whether a domain expert would say "pick one" or "check all that apply" for that specific dimension, the same way Step 1 reasons about which dimensions matter. When in doubt, default to radio, forcing an exclusive pick on a genuinely additive topic loses information, but presenting a genuinely exclusive topic as checkboxes lets the user select combinations that don't actually make sense together (e.g. "strict" and "eventual" consistency at once).

A blend dimension (above) is always radio-like by construction, the ratio slider already represents one exclusive pick of two flavors.

**Checkbox dimensions need their own exclusivity rules, not just a free-for-all.** When a dimension is additive (multiple frame elements, multiple constraints), it usually still has one or two options that are exclusive with everything else on that same dimension, "no foreground at all" can't coexist with "add a boat," and "no preference, you choose" can't coexist with a specific hand-picked item either. Build that exclusivity into the selection logic itself (picking "none" clears other selections on that dimension and vice versa), don't leave it for the user to accidentally create a nonsensical combination and don't leave it for the synthesis step to silently ignore. And when several genuinely compatible items are selected together, the synthesis has to compose them into one coherent scene with real spatial or logical reasoning (what's foreground vs background, what's compatible with what), not just list each one's individual note back to back and call it combined.

### When there are more dimensions than fit comfortably on one screen

However many real dimensions a request turns out to have, build sections for exactly that many, per the unified approach above. When the count gets large (roughly 5+), group related dimensions under clear section headers in the order a practitioner would actually ask about them (symptom or goal first, then system facts, then what changed, then scope), rather than presenting a flat, undifferentiated wall of questions. This is a presentation choice, not a cap, don't drop a genuinely load-bearing dimension just to keep the section count small.

### Verification before shipping (mandatory, every time)

Before presenting either artifact type with `present_files`, verify it actually works, don't ship on the strength of the code looking right. Three checks are mandatory every time:

1. **Syntax check the script.** Extract the inline `<script>` block and run it through a JS syntax checker (e.g. `node --check`) before presenting.
2. **Simulate real usage headlessly**, not just a syntax pass. Load the file in a headless DOM (e.g. jsdom), programmatically click/select through several representative combinations, including the fully-empty state and the fully-filled state, and inspect the generated output text for `undefined`, empty required fields, or broken HTML. For the extended intake form specifically, also test that exclusive checkbox options clear their group correctly in both directions (picking "none" after other picks, and picking another option after "none").
3. **Read the generated prompt text itself against Step 4c's three failure shapes** (missing anchor, self-contradiction, punt-back), for every combination checked in step 2, not just one. A prompt that passes checks 1 and 2 can still fail check 3, that's exactly what shipped in the sunset-image case: clean syntax, working buttons, and a generated prompt that just asked the reader to write the prompt. Checks 1 and 2 catch broken code. Only check 3 catches empty content.

If any check fails, fix the artifact and re-run all three before presenting. Never present an artifact you haven't actually executed and read.

### Why the bottom panel matters

The grid gives fast, wide comparison. It does not replace a real answer, since the recommendations are a hand-written table, not live reasoning about the user's specific situation. The bottom panel exists to close that gap honestly: it tells the user this is a starting point, and it hands them a well-formed prompt (not a blank "what next?") so getting the deeper answer costs them one paste, not a re-explanation from scratch.

## A note on restraint

This skill is a tool for genuinely open-ended requests, not a default behavior for every ambiguous message. Overusing it turns every creative request into a form to fill out, which is the opposite of what makes "build your own pizza" work: the menu only feels good because it's fast and optional, not mandatory. When a request already has enough signal to just produce a strong first attempt, do that instead and let the user redirect from there.
