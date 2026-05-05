---
name: ad-storyboard-builder
description: Build one complete visual storyboard board image for a product advertisement — a single production sheet containing shared choices, character/product references, environment/set design, floor plan, 5 cut frames with camera specs and text, lighting/mood/style notes, voice-over/audio, and cinematography notes. Use this skill as the THIRD step of the AI advertising pipeline, AFTER product-deconstructor and ad-creative-director. The output is one generated storyboard image that Seedance 2.0 Reference-to-Video will consume as reference_image_url. Do NOT output separate reference sheets and seed frames by default. Do NOT use this skill to generate raw video prompts (use video-prompt-builder) or to analyse a product (use product-deconstructor).
---

# Ad Storyboard Builder

Take a creative brief and produce **one visual storyboard board image** — the production sheet that drives Seedance 2.0 Reference-to-Video. The board should look like a polished agency storyboard sheet: dense but readable, divided into labelled sections, with reference images, floor plan, 5 cut panels, text/VO/audio, lighting/mood/style, and camera notes all visible in one image.

## Single-storyboard image contract

This skill produces exactly **one generated storyboard image** for the selected creative brief.

- One selected angle enters this skill.
- One visual storyboard board exits this skill.
- The storyboard board contains exactly 5 cuts, totaling exactly 15 seconds.
- The storyboard board itself is the main reference image for Seedance and must be saved as a project asset.
- The storyboard board feeds exactly one final Seedance 2.0 Reference-to-Video prompt downstream as `reference_image_url`.
- Do not produce separate character sheets, separate product sheets, separate environment plates, or separate seed frames by default. Those are optional preparation assets only if the user asks for them before the storyboard.
- Do not produce alternate storyboards, optional storyboard routes, multiple scripts, or multiple video concepts inside the same run. If the user explicitly asks for variants, each variant must be treated as a separate run with its own storyboard image.

## What the storyboard image must contain

The generated storyboard board must follow the visual grammar shown in the examples:

1. **Top bar / shared choices**
   - Cut count
   - Runtime / rhythm pattern
   - Color palette swatches and names
   - Environment fingerprint
   - Product / brand name

2. **Section 1: Character + product / hero prop reference**
   - Character or hand/wrist/wardrobe reference if talent appears
   - Product reference panels: hero angle, side/details, material macro, logo/mascot/detail
   - Palette swatches
   - Short notes for identity locks

3. **Section 2: Environment / set design + floor plan**
   - 1-3 environment panels
   - A top-down floor plan / route diagram with numbered camera positions
   - Movement arrows and cut numbers

4. **Section 3: Storyboard**
   - Exactly 5 cut panels
   - Each panel includes cut number, duration, lens/camera movement/framing, and 1-3 lines of action/text
   - The hero shot is visibly marked

5. **Section 4: Lighting / mood / style notes**
   - 3-5 lighting or mood panels
   - Mood keywords
   - Cinematography notes
   - Voice-over text excerpt and sound design notes

The board is not a plain text document. It is an image deliverable, designed to be passed into Seedance as the visual reference.

## ⚠ IMAGE GENERATION MANDATE — NON-NEGOTIABLE

The storyboard board image MUST be generated with **GPT Image 2**. No other image model is acceptable for this skill — not Midjourney, not Flux, not Imagen 3, not SDXL, not Recraft. Optional preparation assets, such as improved product packshots or visual brand books, also use GPT Image 2.

There are two — and only two — valid execution paths:

### Path A — Codex environment (no API key required)
If the user is running this pipeline inside an OpenAI Codex agent, use the **imagen tool bundled with Codex**. The Codex agent calls GPT Image 2 directly through its built-in image generation capability — no external API key is needed. This is the default and preferred path when available.

If the runtime is Codex, this path is mandatory for images. Do not use fal.ai or kie.ai for image generation while inside Codex, even if the user has provided a fal.ai key for video. In Codex runs, fal.ai is reserved for Seedance 2.0 video generation only.

### Path B — External API (requires API key)
If the user is NOT in Codex, GPT Image 2 must be called via one of these two providers:
- **fal.ai** — `fal-ai/gpt-image-2` (or current equivalent endpoint)
- **kie.ai** — GPT Image 2 endpoint

The user must have an API key for whichever provider they choose. The skill output references whichever path applies and produces prompts in a format that path expects.

### Prompt format discipline

GENERATION FRAME blocks (Section 3) and reference sheet prompts (Sections 1.1, 1.2, 2.1) must be written so they paste directly into either Codex's imagen tool OR a fal.ai / kie.ai API call without rewriting. Keep prompts in plain natural language — no model-specific syntax (no `--ar`, no Midjourney parameters, no SDXL weights, no negative prompt blocks). GPT Image 2 reads prose and instructions natively.

For multi-image references (locking a character across cuts), specify in the prompt: *"Use the attached character reference sheet and product reference sheet as visual anchors. Match face, hair, wardrobe, and product materials exactly."* Both Codex's imagen tool and fal.ai / kie.ai support attached reference images — the prompt phrasing is identical across paths.

### Decision rule

If the user's execution path is unclear at runtime, ask exactly once:
*"Are you running this inside Codex (imagen included, no key needed), or do you have an API key for fal.ai or kie.ai?"*
Then proceed and don't ask again.

## ⚠ SEEDANCE 2.0 USAGE MANDATE — REFERENCE-TO-VIDEO MODE

The video generation step that consumes this storyboard MUST use Seedance 2.0 in **Reference-to-Video mode**, not classic image-to-video-per-cut. The storyboard image is passed as `reference_image_url`.

**What this means concretely:**
- The single storyboard board image contains the character/product/environment/cut references Seedance needs.
- Seedance 2.0 receives the storyboard board as `reference_image_url` plus the final video prompt.
- Seedance generates all 5 cuts in a single 15s pass, following the board's product, character, palette, environment, cut order, and audio/text notes.

**Storyboard implications:**
- The storyboard board must be visually coherent enough for Seedance to read it as a production plan.
- The VIDEO PROMPT describes all 5 cuts in sequence with their durations, transitions, and how to interpret the storyboard board.
- Add a line to the handoff: *"Seedance 2.0 mode: REFERENCE_TO_VIDEO with storyboard_image_url as the primary reference."*

## ⚠ BRAND BOOK INHERITANCE

If a brand book has been provided (via `brand-book-builder` or directly by the user via `pipeline-intake`), this storyboard skill treats the brand book as **locked source material**. Specifically:

- The **COLOUR PALETTE** in SHARED CHOICES → copy verbatim from brand book §4.
- The **GRAIN / FILM LOOK** in SHARED CHOICES → copy from brand book §8 SIGNATURE FILM LOOK.
- The **LENS SET** in SHARED CHOICES → copy from brand book §8 SIGNATURE LENS.
- The **ENVIRONMENT FINGERPRINT** → copy from brand book §8 ENVIRONMENT FINGERPRINTS, picking the 1 (or up to 3) most relevant for this specific spot.
- The **CHARACTER FINGERPRINT** → copy from brand book §8 CAST DIRECTION.
- The **MOOD KEYWORDS** in §4 → derived from brand book §7 (tone) and §3 (archetype).
- The **anti-patterns** referenced when writing GENERATION FRAMES → cross-check against brand book §10 DON'Ts. A storyboard that violates a brand book DON'T must be rebuilt.

If no brand book is provided, generate these from scratch based on the product intelligence sheet's Section 7 (BRAND WORLD).

The header SHARED CHOICES block must include a line:
```
BRAND BOOK SOURCE:       [PROVIDED — name of brand / SKIP — derived from product]
```

## How this skill works

1. The user provides a creative brief (output of `ad-creative-director`) plus the upstream product intelligence sheet and any product/brand assets.
2. **The output is ALWAYS one storyboard image only.** Do not branch into alternate storyboards or multiple final video concepts.
3. **The storyboard always describes 5 cuts totalling EXACTLY 15 seconds.** This is a hard constraint imposed by Seedance 2.0's 15-second generation budget.
4. Write a concise storyboard-board generation prompt, then generate the storyboard board image with GPT Image 2.
5. Save the generated storyboard image into the project outputs folder.
6. Output the saved image path plus a compact handoff note for `video-prompt-builder`.

## 15-SECOND RHYTHM CONTRACT — HARD CONSTRAINT

The storyboard is ALWAYS:
- **one final storyboard image**
- **5 cuts**
- **15 seconds total exactly** (sum of cut durations = 15.0s, not 14, not 16)
- Cut durations vary **asymmetrically** to build rhythm — never 5 cuts × 3s mechanically.

### Canonical rhythm patterns

These are the four valid rhythm signatures. Pick one based on the creative brief's energy arc:

| Pattern | Sum | Use when                                                        |
|---------|-----|-----------------------------------------------------------------|
| 3-4-2-3-3 | 15s | Default. Long beat in cut 2 carries the main moment, cut 3 is a 2s tactile macro insert |
| 4-3-2-3-3 | 15s | Slow open — establishes geography first, then accelerates       |
| 3-3-2-4-3 | 15s | Long beat lives in cut 4 (the climax / hero shot)               |
| 3-3-3-3-3 | 15s | Use ONLY when the creative truly demands metronomic regularity (rare — usually feels mechanical) |

### Cut roles in a 15s spot

Each of the 5 cuts has a typical narrative role. This isn't rigid, but it's the grammar that works in tes 3 exemples and in pro work generally:

```
CUT 1 (3-4s)  ESTABLISH       — geography / wide / scale-setting / the world we're entering
CUT 2 (3-4s)  PROTAGONIST     — character or product enters with intent / motion
CUT 3 (2-3s)  INSERT / MACRO  — tactile detail / hand / texture / the close-up that grounds the spot
CUT 4 (3-4s)  CLIMAX          — the hero moment, often with the strongest motion or emotional peak
CUT 5 (3s)    RESOLVE         — release, distance, silhouette, breath. The image we leave with.
```

### Validation rule

Before finalising the storyboard, verify in writing:
- **Cut count = 5** ✓
- **Sum of cut durations = 15.0s exactly** ✓
- **No single cut exceeds 4s** (Seedance handles longer single shots poorly within multi-shot mode)
- **No single cut is below 2s** (anything shorter reads as a glitch, not a cut)
- **At least one cut is 2s and at least one cut is 4s** (this is what creates the asymmetric rhythm)

If any of these fail, rebalance before output. Never deliver a storyboard that doesn't sum to 15s — this breaks the entire downstream pipeline.

### Why this is non-negotiable

Seedance 2.0 generates up to 15 seconds in a single multi-shot pass. That 15s budget is the model's native canvas. A 14s spot wastes a second of the budget; a 16s spot can't be generated in one pass and forces a second generation that breaks character/environment continuity. The 15s contract is what makes the cohesion gains of Seedance 2.0 actually usable.

## Output structure

The skill output is short because the main deliverable is the generated image:

```
STORYBOARD IMAGE DELIVERABLE
Saved path: [project path to storyboard image]
Seedance reference field: reference_image_url

BOARD CONTENT SUMMARY
- Shared choices: [...]
- Section 1: character + product references [...]
- Section 2: environment + floor plan [...]
- Section 3: 5 cuts [...]
- Section 4: lighting / mood / style / audio [...]

HANDOFF TO VIDEO-PROMPT-BUILDER
[5-cut summary + exact path/URL of storyboard image]
```

The storyboard board image itself should contain the following layout:

### STORYBOARD BOARD LAYOUT — SHARED CHOICES

This is the contract every cut must obey. Output exactly:

```
═══════════════════════════════════════════════════════════════
SHARED CHOICES
═══════════════════════════════════════════════════════════════
CUT COUNT:               5
TOTAL DURATION:          15s (locked)
RHYTHM PATTERN:          [one of: 3-4-2-3-3 / 4-3-2-3-3 / 3-3-2-4-3 / 3-3-3-3-3]
ASPECT RATIO:            [selected aspect ratio from intake]
COLOUR PALETTE:          [4-5 named colours, hex optional — INHERIT from brand book §4 if provided]
ENVIRONMENT FINGERPRINT: [3 specific locations separated by semicolons — INHERIT from brand book §8 if provided]
CHARACTER FINGERPRINT:   [if applicable — INHERIT from brand book §8 if provided]
LENS SET:                [INHERIT from brand book §8 SIGNATURE LENS if provided]
GRAIN / FILM LOOK:       [INHERIT from brand book §8 SIGNATURE FILM LOOK if provided]
BRAND BOOK SOURCE:       [PROVIDED — name of brand / SKIP — derived from product]
IMAGE GENERATION TARGET: GPT Image 2 — [Codex imagen / fal.ai / kie.ai]
SEEDANCE MODE:           REFERENCE_TO_VIDEO — single generation using storyboard_image_url
═══════════════════════════════════════════════════════════════
```

### Section 1: CHARACTER + HERO PROP REFERENCE

This is a section inside the single storyboard board image, not a separate generated sheet by default.

```
1.1 — CHARACTER REFERENCE SHEET (if talent in spot)
Generate a single sheet containing:
• Front view — full body
• Back view — full body
• Side profile — full body
• Facial close-up — neutral expression, soft daylight
• Side close-up — three-quarter
• Relaxed pose — hand to hair or similar natural gesture

Locking specs:
• Hair: [colour, length, texture]
• Skin: [tone, finish]
• Wardrobe: [exact garments, colours, materials, fit]
• Jewellery / accessories: [list, with metal + stone references]
• Posture: [the one defining cue — "relaxed weight on left hip" etc]

1.2 — HERO PROP / PRODUCT REFERENCE SHEET
Generate a single sheet containing:
• Front 3/4 view
• Side profile
• Back 3/4 view
• Material macro (one extreme close-up of the signature texture/finish)
• Detail macro (logo, hardware, signature feature)
• In-context shot (product placed in environment)

Locking specs:
• Materials: [as per product intelligence sheet]
• Colours: [exact named colours]
• Hardware / branding: [precise position]
```

### Section 2: ENVIRONMENT / SET DESIGN

```
2.1 — ENVIRONMENT REFERENCE PLATES
For each of the [N] environments named in ENVIRONMENT FINGERPRINT, generate one wide reference plate:
• [Environment 1 name]: [one paragraph describing exact visual specifics — geography, time of day, light direction, weather, key landmarks. Be specific enough that two artists generating from this would converge.]
• [Environment 2 name]: [same]
• [Environment 3 name]: [same]

2.2 — FLOOR PLAN / ROUTE DIAGRAM
A top-down map showing camera position for each cut and any character/product movement. Specify:
• Geography orientation (which way is the sea / sun / road)
• Camera position for each cut, numbered 1 to N
• Movement arrows (where the subject travels between cuts)
• Direction of light per cut

Output as a textual top-down description, e.g.:
"North = open sea. Road runs east-west along cliff. Cut 1 camera: aerial east of subject. Cut 2 camera: ground level, south of road, 50m east of subject. Cut 3: in-vehicle. Cut 4: south-west of villa, eye level. Cut 5: ground level inside villa courtyard, looking out."
```

### Section 3: STORYBOARD — CUT BY CUT

The heart of the document. One block per cut, exactly this format:

```
─────────────────────────────────────────────────────────
CUT [N]  ▸  [duration]s  ▸  [shot type — WIDE / MEDIUM / CU / EXTREME-CU / INSERT]
─────────────────────────────────────────────────────────
LENS:        [e.g. 40mm anamorphic]
MOVEMENT:    [STATIC / TRACK / DOLLY-IN / DOLLY-OUT / CRANE-UP / CRANE-DOWN / STEADICAM / HANDHELD / WHIP-PAN]
HEIGHT:      [ground level / waist / eye level / overhead / aerial]
FRAMING:     [what's in frame — composition note, where the subject sits in the rule-of-thirds]

ACTION:
[2-3 sentences describing exactly what happens in the cut. Present tense. Visual only — no inner monologue.]

LIGHTING:
[direction + quality + colour temperature — e.g. "low golden side-light from camera-right, warm 3200K, gentle halation"]

CONTINUITY ANCHORS:
[the 1-2 elements that must match the previous and next cuts — colour palette element, sound bridge, motion direction, prop position]

GENERATION FRAME (for GPT Image 2):
[A compact still-frame description that appears as the visual cut panel inside the storyboard board image.]

TRANSITION OUT:
[how this cut exits — straight cut / motion match / sound bridge / cross-fade / whip / blur]
```

### Section 4: LIGHTING / MOOD / STYLE NOTES

A small library (3-5 entries) of named lighting setups used across the spot. Each named so the storyboard can reference them shorthand.

```
LIGHT SETUP A — [NAME, e.g. "GOLDEN-HOUR COASTAL"]
[2-3 sentences describing direction, quality, colour, what it does for the mood]

LIGHT SETUP B — [NAME]
[same]

LIGHT SETUP C — [NAME]
[same]
```

Then a block called MOOD KEYWORDS — 6-10 adjectives lifted directly from the creative brief.

Then a block called CINEMATOGRAPHY NOTES — 3-5 lines on the camera doctrine (rhythm, restraint, signature move).

### Section 5: AUDIO

```
VOICE-OVER:
SPEAKER: [as per creative brief]
COPY: [the final VO text, locked]
PLACEMENT: [which cuts the VO sits over — e.g. "starts mid-Cut 2, ends mid-Cut 5"]

MUSIC:
[as per creative brief — locked tempo, instrumentation, role]

SOUND DESIGN:
[a list of 5-8 specific diegetic sounds, mapped to cuts where they appear]
e.g.
• Cut 1: distant waves, gulls
• Cut 2: V8 idle to acceleration, gravel under tyres
• Cut 3: wind through open window, leather creak on shifter
• Cut 4: silence, then single chrome reflection ping
• Cut 5: gravel footsteps, soft cello entrance
```

### Section 6: HANDOFF NOTES TO VIDEO-PROMPT-BUILDER

A condensed brief the next skill will consume. It restates:
- Total cut count and duration: 5 cuts, 15s exactly, rhythm pattern [X-X-X-X-X]
- The character fingerprint (one line)
- The environment fingerprint (one line)
- The brand book source (one line)
- The signature visual moment (the ★ HERO SHOT ★ cut)
- The energy arc in one sentence
- Seedance 2.0 mode: **REFERENCE_TO_VIDEO** — single generation call, 15s output, with `reference_image_url` set to the saved storyboard board image.

End with:
*"Ready for video-prompt-builder. Generate a single Reference-to-Video prompt for Seedance 2.0 — 5 cuts, 15s, rhythm [pattern], reference_image_url = [storyboard image]."*

## Creative principles

1. **The fingerprints are sacred.** Character fingerprint and environment fingerprint must be identical wording in every cut where they apply. Drift kills cohesion in AI video.
2. **Continuity anchors are the glue.** Every cut needs at least one explicit anchor to the next — a colour, a motion direction, a sound bridge. Without anchors, the spot feels like a slideshow.
3. **Generation frames are seeds, not descriptions.** When you write the GENERATION FRAME block, write it as if you were prompting the image directly — concrete nouns, no abstractions, no marketing language.
4. **Specs over moods.** "50mm anamorphic, 3s, dolly-in, low-angle" is information. "Cinematic and emotional" is noise. Use specs.
5. **Cuts breathe.** Resist filling every second with motion. Static and slow cuts create the contrast that makes movement land.
6. **The hero shot is named.** Every storyboard has one cut that's the "if-you-only-remember-one-image" shot. Mark it explicitly with a note: *★ HERO SHOT ★* on its line.

## Tone and style

- Production-document register. Bullet-tight, present tense, no hedging.
- Use the exact section dividers shown (em dashes and box characters) so the document is visually scannable.
- No emojis except the single ★ used to mark the hero shot.
- Output as plain text. The document is meant to be readable in a terminal, a Notion page, or a printed call sheet.

## Duration is locked

There is no duration calibration table. The storyboard is ALWAYS 5 cuts totalling exactly 15 seconds, with one of the canonical rhythm patterns (3-4-2-3-3 default, or 4-3-2-3-3 / 3-3-2-4-3 / 3-3-3-3-3 in justified cases). See the **15-SECOND RHYTHM CONTRACT** above. Do not attempt to deliver other durations from this skill — that's a different pipeline.

## Final pre-output checklist

Before delivering the storyboard, verify ALL of these:

- [ ] Output contains exactly one storyboard image, not multiple options or variants
- [ ] Cut count = exactly 5
- [ ] Sum of cut durations = exactly 15.0s
- [ ] Rhythm pattern is one of: 3-4-2-3-3 / 4-3-2-3-3 / 3-3-2-4-3 / 3-3-3-3-3
- [ ] At least one cut is 2s (the macro/insert beat)
- [ ] At least one cut is 4s (the long beat) — except in the 3-3-3-3-3 case
- [ ] One cut is explicitly marked ★ HERO SHOT ★
- [ ] SHARED CHOICES block includes RHYTHM PATTERN and IMAGE GENERATION TARGET
- [ ] The storyboard board image contains shared choices, character/product references, environment/floor plan, 5 cuts, lighting/mood/style, audio/VO, and cinematography notes
- [ ] The saved storyboard image path is included for `reference_image_url`
- [ ] Character fingerprint and environment fingerprint use identical wording across cuts where they apply

If any item fails, fix before output. Never deliver a partially-compliant storyboard.

