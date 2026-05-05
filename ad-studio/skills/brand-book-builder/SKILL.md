---
name: brand-book-builder
description: Build a complete, production-grade brand book from minimal inputs — brand name, category, founder story, and optionally a logo or product photos. Use this skill when the user does NOT have a brand book yet but needs one to feed the AI advertising pipeline. Trigger when the user says "I don't have a brand book", "create a brand book", "build me a brand identity", "we don't have brand guidelines yet", "design the brand", "je n'ai pas de charte", "crée-moi une charte de marque", or when the pipeline-intake skill detects no brand book is available. Outputs a structured brand book covering brand essence, positioning, archetype, visual identity (palette, typography, logo direction), tone of voice, photography direction, and do/don't rules. Do NOT use this skill to analyse a product (use product-deconstructor) or to apply an existing brand book (that's done downstream by ad-storyboard-builder).
---

# Brand Book Builder

Generate a complete brand book — the kind of document a real brand agency would deliver after a 6-week strategy engagement, condensed to a single working document the rest of the pipeline can consume.

## How this skill works

1. The user provides whatever minimal inputs they have. Common starting points:
   - **Brand name only** (e.g. "I'm calling it Volcán")
   - **Brand name + category** (e.g. "Volcán, a niche fragrance brand")
   - **Brand name + product photos** (a few packshots and the name)
   - **Brand name + founder story / origin** (the why behind the brand)
   - **Vague brief** (e.g. "I want to launch a coffee brand for office workers")

2. If inputs are sparse, ask **maximum 3 questions** to fill critical gaps. Never more. Use the question hierarchy below.

3. Generate the complete brand book in the structure below. ALL ten sections are mandatory. The output is designed to feed `product-deconstructor` (which uses it as a constraint when analysing products) and `ad-storyboard-builder` (which uses it to lock palette, typography, tone, and visual rules).

## Question hierarchy (ask only what's missing, max 3)

In priority order:
1. **What does the brand sell?** — category and price tier
2. **What's the core promise?** — the one thing the brand stands for
3. **Who is it for?** — primary persona in one line

Skip questions whose answers are clearly inferable from the inputs. If the user said "luxury Mexican mezcal brand named Volcán", you don't need to ask the category or tier — only the promise and persona if they're missing.

## Output structure

### Section 1: BRAND ESSENCE

```
NAME:           [exact spelling, capitalisation, accents]
PRONUNCIATION:  [phonetic if non-obvious — e.g. "vol-KAHN"]
ETYMOLOGY:      [why the name, what it means or evokes]
ONE-LINER:      [the brand in one sentence — what it does, for whom, why it matters]
MANIFESTO:      [3-5 sentences. The brand's worldview. First-person plural. Written like a creed, not a description.]
```

The manifesto is the document's spiritual centre. It should be quotable. Avoid adjectives like "premium", "innovative", "passionate" — use specific images and verbs instead.

### Section 2: POSITIONING

Use the canonical framework:

```
POSITIONING STATEMENT:
For [target audience] who [insight or unmet need], [brand] is the [frame of reference]
that delivers [primary benefit] and [secondary benefit], unlike [main competitor or commodity alternative].

PRICE TIER: [MASS / PREMIUM / LUXURY / ULTRA-LUXURY]
CATEGORY: [primary → subcategory → niche]
COMPETITIVE SET: [3-5 brands the buyer mentally compares this to]
WHITE SPACE: [what the brand owns that others don't — in one sentence]
```

### Section 3: BRAND ARCHETYPE

Pick ONE primary and ONE secondary from this list. Justify in 2 sentences each.

- **The Heritage** — old-money, inherited, time-tested
- **The Outlaw** — disruptive, anti-establishment
- **The Artisan** — craft-obsessed, slow, hand-made
- **The Innovator** — future-forward, technical
- **The Hedonist** — pleasure-first, sensual
- **The Minimalist** — restraint, essentialism
- **The Adventurer** — performance, outdoor, freedom
- **The Ritualist** — daily ceremony, mindfulness

### Section 4: VISUAL IDENTITY — PALETTE

```
PRIMARY PALETTE (3 colours — the brand's non-negotiable core):
• [Colour 1 name] — [HEX] — [usage: e.g. "primary brand colour, used on logo, hero packshot backgrounds"]
• [Colour 2 name] — [HEX] — [usage]
• [Colour 3 name] — [HEX] — [usage]

SECONDARY PALETTE (2-3 colours — for accents and editorial flexibility):
• [Colour name] — [HEX] — [usage]
• [Colour name] — [HEX] — [usage]

NEUTRAL BASE:
• [Off-white / warm grey / etc] — [HEX]
• [Black / charcoal / etc] — [HEX]

PALETTE PRINCIPLES:
[2-3 lines on how the palette is used — e.g. "primary palette dominates 80% of any composition; secondary used for accents only; never use all three primaries at full saturation in the same frame."]
```

Use **named colours** (oxford blue, terracotta, ivory, dusk slate) not vague terms (dark blue, orange, white, grey).

### Section 5: VISUAL IDENTITY — TYPOGRAPHY

```
PRIMARY TYPEFACE:    [name + foundry, e.g. "GT Sectra (Grilli Type)" — used for headlines, logo wordmark]
SECONDARY TYPEFACE:  [name + foundry — used for body copy, captions]
DISPLAY / ACCENT:    [optional — name + foundry — used sparingly for editorial moments]

TYPE PRINCIPLES:
[2-3 lines — e.g. "Primary typeface always set in tracked-out caps for headlines, never below 24pt. Secondary used for any text under 18pt. No more than two weights in a single composition."]
```

If the user has no typography preference, propose typefaces that fit the archetype:
- Heritage → GT Sectra, Larken, Söhne Mono
- Outlaw → Druk, IBM Plex Mono, custom-distressed
- Artisan → ABC Diatype, Ogg, Reckless
- Innovator → Söhne, Inter, GT America Mono
- Hedonist → Canela, Saol Display, Migra
- Minimalist → Söhne Buch, ABC Diatype, Helvetica Now
- Adventurer → GT America Compressed, Suisse Int'l, Fraktion
- Ritualist → Tiempos, Söhne, Lyon Display

### Section 6: VISUAL IDENTITY — LOGO DIRECTION

```
LOGO TYPE:        [wordmark / lettermark / monogram / combination mark / pictorial]
TONE:             [3-4 adjectives — e.g. "restrained, weighty, literary"]
CONSTRUCTION:     [brief description — e.g. "wordmark in primary typeface, custom-drawn ligature on the 'lc' pairing, no tagline"]
DO:               [3 rules — e.g. "always lockup with 1x cap-height of clear space"]
DON'T:            [3 rules — e.g. "never on a busy photographic background, never at angles, never recoloured outside primary palette"]
```

If the user has uploaded a logo or wants the skill to design one, describe the logo as a **GPT Image 2 generation prompt** at the end of this section, ready to be passed to the imagen tool.

### Section 7: TONE OF VOICE

```
PRIMARY REGISTER:   [pick: solemn / sensual / contemplative / aspirational / nostalgic / defiant / playful / restrained]
SECONDARY REGISTER: [optional]
SENTENCE LENGTH:    [short and clipped / medium and rhythmic / long and flowing]
PERSON:             [first-person singular / first-person plural / second-person / third-person]

VOCABULARY RULES:
• Words we use: [5-7 specific words this brand uses naturally]
• Words we never use: [5-7 banned words — usually category clichés]
• Punctuation: [house style — em dashes? semicolons? Oxford comma?]

TAGLINE OPTIONS (3):
• [Option A — declarative]
• [Option B — poetic]
• [Option C — provocative]
```

### Section 8: PHOTOGRAPHY & FILM DIRECTION

This is the section that locks the visual world for `ad-storyboard-builder`. Be concrete.

```
SIGNATURE LIGHT:        [e.g. "magic-hour cool blue cross-faded with tungsten interiors"]
SIGNATURE COMPOSITION:  [e.g. "off-centre subject, 60% negative space, horizon line low"]
SIGNATURE LENS:         [focal length range — e.g. "40-75mm, occasional 100mm macro"]
GRAIN / FILM LOOK:      [reference stock — e.g. "Kodak Portra 400, subtle halation"]

ENVIRONMENT FINGERPRINTS (3 worlds the brand lives in — be ultra-specific):
• [Environment 1 — e.g. "1965 Tuscan cliffside villa at magic hour"]
• [Environment 2 — e.g. "cypress-lined gravel road in Provence, golden dust"]
• [Environment 3 — e.g. "stone interior with tungsten practicals, evening blue hour"]

CAST DIRECTION (if humans appear):
• [Age range, ethnicity policy, performance note — what they DO and DON'T do on camera]

ANTI-PATTERNS (5 things this brand is photographically NEVER):
• [e.g. "never drone shots over cities"]
• [e.g. "never staged smiling-to-camera"]
• [e.g. "never high-saturation digital look"]
• [e.g. "never on-trend Instagram filters"]
• [e.g. "never product on white seamless"]
```

### Section 9: SOUND / MUSIC DIRECTION

```
MUSIC ROLE:            [foreground / underscore / absent — pick the brand's default]
GENRE / INSTRUMENTATION: [e.g. "solo cello with sparse piano; contemporary classical, never electronic"]
TEMPO:                 [BPM range descriptor]
SOUND PALETTE (foley priorities): [3-5 diegetic sounds the brand world contains — e.g. "leather creak, distant church bell, cellar silence"]
VOICE CASTING:         [if VO is part of the brand — gender, age, accent, register]
SILENCE POLICY:        [how comfortable the brand is with silence — luxury usually leans into it]
```

### Section 10: BRAND DO / DON'T (THE LAW)

A 10-rule manifesto, half DO half DON'T. These are the rules every downstream creative decision must obey.

```
DO:
1. [...]
2. [...]
3. [...]
4. [...]
5. [...]

DON'T:
1. [...]
2. [...]
3. [...]
4. [...]
5. [...]
```

Make these specific and enforceable. "Don't be inconsistent" is useless. "Never show the product on a white seamless background" is enforceable.

## Creative principles

1. **Specificity over generality, always.** "Cypress-lined Provence road" beats "Mediterranean countryside". Named colours beat colour families. Named typefaces beat "serif".
2. **The brand book is a constraint document, not a moodboard.** Every section must enable downstream decisions. If a section doesn't constrain anything, it's noise.
3. **Anti-patterns matter as much as patterns.** Naming what the brand isn't sharpens what it is.
4. **Tier discipline.** A premium brand pretending to be luxury reads as fake. Calibrate every visual decision to the price tier in Section 2.
5. **Quotability is a feature.** Brand books that read like Wikipedia don't get used. Brand books with quotable manifestos do.

## Tone and style

- Production-document register. Plain text. No emojis.
- Use proper nouns (typeface foundries, colour names, music genres, photographer references) wherever possible.
- Keep paragraphs tight. This document gets read on Mondays at 9am, not curled up with.

## Handoff

End the output with:
*"Brand book locked. Ready for product-deconstructor (single-product mode) or ad-pipeline (full ad mode). All downstream stages will inherit palette, typography, archetype, environment fingerprints, anti-patterns, and tone-of-voice from this document."*
