---
name: ad-creative-director
description: Develop a chosen advertising angle into a complete creative brief — the document a director, DP, and stylist would use to actually shoot. Use this skill as the SECOND step of the AI advertising pipeline, AFTER product-deconstructor has produced a product intelligence sheet and the user has selected one of the three advertising angles. Trigger when the user says things like "develop angle 2", "pick the heritage angle", "let's go with the nostalgic one", "build the brief for this angle", "creative direction for this product", or provides a product sheet and asks for a single creative concept. Outputs a structured creative brief covering hero idea, narrative spine, tone-of-voice, visual treatment, voice-over copy, music/sound brief, and casting notes. Do NOT use this skill to analyse a product from scratch (use product-deconstructor) or to produce a shot-by-shot storyboard (use ad-storyboard-builder).
---

# Ad Creative Director

Take a product intelligence sheet and a chosen angle, and produce a complete creative brief — the kind of document a real ad agency would deliver to a director before pre-production.

## How this skill works

1. The user provides (or has just generated upstream) a **product intelligence sheet** from `product-deconstructor`, plus an indication of which of the three angles to develop.
2. If the user hasn't picked an angle, ask which one (1, 2, or 3) — but only ask this single question, then proceed.
3. Generate a complete creative brief in the structure below. ALL eight sections are mandatory.

## Output structure

### Section 1: HERO IDEA

One paragraph (4-6 sentences) describing the central creative idea. This is what the brief is "about" — the single thought that any shot, any line of voice-over, any music cue must serve.

Format:
```
HERO IDEA — [3-WORD CAMPAIGN TITLE]

[paragraph]
```

The title is internal-only and exists to anchor the team. Examples of strong titles: *Inherited Light*, *The Last Honest Curve*, *Quiet Acceleration*, *Salt and Memory*.

### Section 2: NARRATIVE SPINE

The story arc in 3 beats. Even a 15-second ad has a spine.

```
BEAT 1 — SETUP: [what we establish in the first third]
BEAT 2 — TENSION OR REVELATION: [the turn — emotional, visual, or narrative]
BEAT 3 — RESOLUTION: [what we leave the viewer with — image, line, or feeling]
```

Use the AIDA / hook-problem-solution framework only as a sanity check, not as a template. Luxury / lifestyle work usually lives in **establish → contemplate → resolve**, not **problem → solution**.

### Section 3: TONE OF VOICE

Pick the tonal register and lock it. Use this exact format:

```
PRIMARY REGISTER: [one of: solemn / sensual / contemplative / aspirational / nostalgic / defiant / playful / restrained]
SECONDARY REGISTER: [optional — adds nuance]
TONAL ANCHORS: [3 specific cultural references — films, ads, photographers, musicians — that the team can point to]
TONAL ANTI-ANCHORS: [2 things this is NOT, to prevent drift]
```

Example anchors for a heritage GT car: *In the Mood for Love (Wong Kar-wai, 2000) / Helmut Newton's Riviera portfolio / a Patek Philippe print campaign*. Anti-anchor: *Fast & Furious; any modern auto manufacturer ad with drone shots over a city*.

### Section 4: VISUAL TREATMENT

The image-language rules. Every shot must obey these.

```
LENS LANGUAGE: [primary focal lengths and why — e.g. "40-75mm anamorphic for restraint; one 100mm macro insert for tactile detail"]
CAMERA RHYTHM: [restrained / kinetic / handheld / locked-off / mixed — and what the rule is]
LIGHTING DOCTRINE: [time of day + quality + sources — be specific]
COLOUR GRADE: [reference grade — e.g. "warm-to-cool magic-hour fall, low-contrast highlights, gentle halation"]
GRAIN / TEXTURE: [film stock reference if any — e.g. "Kodak 250D feel, subtle grain, no digital sharpness"]
NEGATIVE SPACE: [how much breathing room — luxury usually wants more]
WARDROBE / SET: [what stays consistent across all shots — character fingerprint, environment fingerprint]
```

### Section 5: VOICE-OVER COPY

Write the actual VO. Three options, each labelled.

```
VO OPTION A — DECLARATIVE
[1-3 lines, the safe / on-brief version]

VO OPTION B — POETIC
[1-3 lines, more abstract, more rhythm]

VO OPTION C — ABSENT
[no VO — describe what carries the emotion instead: a single line of dialogue, a sound, a card]
```

Each option must be deliverable in the target duration (assume 15s = ~30 words max, 30s = ~60 words max). Specify the speaker:
```
SPEAKER: [gender, age range, accent, register — e.g. "female, 40s, refined British, low and intimate"]
```

### Section 6: MUSIC & SOUND BRIEF

```
MUSIC ROLE: [foreground / underscore / absent — pick one]
GENRE / INSTRUMENTATION: [specific — e.g. "solo cello with sparse piano" not "emotional music"]
TEMPO: [BPM range or descriptor — "slow, ~60 BPM, breathing"]
REFERENCE TRACKS: [2-3 actual tracks the composer can study — name artist + title]
SOUND DESIGN PRIORITIES: [3 foley/diegetic sounds that matter — these often matter more than music in luxury work]
SILENCE BEATS: [where silence (or near-silence) does the work — be specific by timestamp]
```

### Section 7: CASTING NOTES

If a person appears in the ad:

```
CHARACTER: [age, gender, ethnicity — only specify if creatively essential, otherwise write "open"]
PHYSICAL FINGERPRINT: [hair, build, skin — only the cues that matter for visual continuity across shots]
PERFORMANCE NOTE: [the one direction the actor needs — e.g. "she never looks at the product, never smiles to camera, presence comes from stillness"]
WHAT WE NEVER SEE: [eye contact / dialogue / branded close-up — the prohibitions]
```

If no person appears, replace this section with:

```
NO TALENT — the product is the protagonist. Every shot must give it presence.
```

### Section 8: DELIVERABLES & DURATION TARGETS

This section is locked for duration and cut count. The aspect ratio is selected during intake or by the user before storyboard generation.

```
HERO CUT:             15s in selected aspect ratio
CUT COUNT:            5 cuts
RHYTHM PATTERN:       [pick one: 3-4-2-3-3 / 4-3-2-3-3 / 3-3-2-4-3 / 3-3-3-3-3]
ASPECT RATIO:         [selected ratio — e.g. 16:9 landscape, 9:16 vertical, 1:1 square]
PLATFORM PRIMARY USE: [YouTube / TikTok / Reels / Shorts / Meta Ads / landing page / other]
SECONDARY EXPORTS:    optional crops after the hero cut is approved
```

The 15s / 5-cut format is non-negotiable in this pipeline. The aspect ratio is not fixed: it must match the user's intended platform and be carried unchanged into the visual storyboard board and Seedance prompt. The creative brief should be written to that duration and cut-count constraint — don't propose a 30s spot here.

## Creative principles

1. **One idea, ruthlessly held.** A creative brief that contains two ideas contains zero. If a section feels "and also", cut it.
2. **Reference real work.** Tonal anchors must be specific, citable, looked-up-able. Vague references = vague output.
3. **Casting and wardrobe are continuity tools.** In AI video, character fingerprint discipline is not optional — Seedance and HappyHorse will drift unless you lock it here.
4. **Silence is a creative choice.** The best luxury work uses VO sparingly or not at all. Always evaluate VO Option C (absent) seriously.
5. **The brief is for the next skill.** Every section here will be consumed by `ad-storyboard-builder`. Write it so the next skill has zero ambiguity.

## Tone and style

- Write like a creative director briefing a director — confident, specific, no over-explanation.
- Use proper nouns. Name films, photographers, tracks, locations.
- Avoid hype adjectives ("stunning", "breathtaking", "captivating"). Describe; don't sell.
- Keep paragraphs tight. This document is read on set, not curled up with.

## Handoff

End the output with:
*"Ready for ad-storyboard-builder. This brief will produce a 5-cut storyboard at exactly 15 seconds (rhythm pattern: [chosen pattern])."*
