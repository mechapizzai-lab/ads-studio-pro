---
name: product-deconstructor
description: Analyse a product OR a whole brand and produce a structured intelligence sheet. Operates in two modes — SINGLE_PRODUCT (one product analysed in depth) or BRAND_CAMPAIGN (a brand's product line analysed as a coherent set, identifying the hero product and the connective tissue between them). Use this skill as the FIRST analytical step of the AI advertising pipeline, AFTER pipeline-intake has determined mode and gathered assets, AFTER brand-book-builder if a brand book had to be created. Trigger when the user provides a product photo / name / URL (single mode), or several brand visuals (brand mode), or when pipeline-intake routes here. Also trigger on phrases like "analyse ce produit", "fiche produit", "deconstruct this product", "analyse cette marque", "brand analysis", "pub pour la marque entière". Outputs a complete intelligence sheet covering identity, price tier, archetype, persona, jobs-to-be-done, sensory signature, brand world, anti-patterns, and 3 advertising angles. Honors any provided brand book by treating its constraints (palette, typography, tone, anti-patterns) as locked — the analysis must align with them, not contradict them. Do NOT use this skill for creative writing, storyboarding, or video generation — those are downstream skills.
---

# Product Deconstructor

Turn a product OR a brand into a structured intelligence sheet that downstream creative skills (creative-director, storyboard-builder, video-prompt-builder) can act on. This is the strategic foundation of the pipeline — every creative decision later flows from this analysis.

## Operating modes

This skill operates in two modes, determined by `pipeline-intake`:

### MODE A — SINGLE_PRODUCT
The user wants an ad for one specific product (e.g. *Volcán Reposado mezcal*, *the John GT coupé*, *the Aesop Marrakech parfum*). The intelligence sheet focuses on this one item: its identity, its sensory footprint, its jobs-to-be-done, its place in the brand. The 3 advertising angles produced at the end are 3 different ways to advertise THIS product.

### MODE B — BRAND_CAMPAIGN
The user wants an ad for a brand as a whole (e.g. a Volcán brand film, an Aesop seasonal campaign, a launch trailer for a new label). The intelligence sheet focuses on the brand's coherent universe: what unites the products, the brand's archetype and worldview, what the campaign should signify. The skill identifies a **HERO PRODUCT** — the one item that gets the most screen time as a vessel for the brand idea — but the goal is brand resonance, not product sale. The 3 advertising angles are 3 different ways to advertise the BRAND, each centred on a different hero product or hero idea.

## Brand book ingestion (mandatory if provided)

If `pipeline-intake` has flagged `BRAND_BOOK_STATUS = PROVIDED` (or completed via `brand-book-builder`), the brand book is treated as **locked constraint material**. Specifically:

- **Palette** from brand book §4 → use exactly. Do not invent new colours.
- **Typography** from brand book §5 → reference it; downstream stages will use it.
- **Archetype** from brand book §3 → carry through; do not reassign a different archetype to the product.
- **Photography direction & environment fingerprints** from brand book §8 → use as the product's brand world (Section 7 of this skill's output) verbatim or with minor product-specific adaptation.
- **Tone of voice** from brand book §7 → influences the 3 angles in Section 9.
- **Anti-patterns** from brand book §10 → merged with the product-level anti-patterns in Section 8.

If BRAND_BOOK_STATUS = SKIP, generate brand world / palette / archetype from scratch based on the product alone. Note this clearly at the top of the output: *"NO BRAND BOOK PROVIDED — analysis derived from product alone. Downstream visual decisions are best-guess and may diverge from any actual brand identity."*

## How this skill works

1. Read the inputs: product photo(s) / brand visuals / URL / description, plus the brand book if present.
2. If the input is a URL, fetch the product or brand page.
3. If the input is one or more images, describe each exhaustively: shape, materials, colours, finishes, ergonomics, scale cues, visible branding, packaging.
4. Determine mode (single / brand) — or honor the mode set by `pipeline-intake`.
5. Generate the complete intelligence sheet in the structure below. ALL nine sections are mandatory. Sections adapt slightly based on mode (see annotations below).

## Output structure

### Section 1: IDENTITY

**Mode SINGLE_PRODUCT — output exactly:**

```
NAME:           [actual or inferred product name]
PARENT BRAND:   [the brand it belongs to]
CATEGORY:       [primary category → subcategory → niche]
FORMAT:         [physical format — bottle/can/garment/device/etc + dimensions]
MATERIALS:      [every material visible or implied]
COLOURS:        [primary, secondary, accent — proper colour names]
FINISHES:       [matte / gloss / brushed / patinated / hand-stitched / etc]
PACKAGING:      [box, sleeve, dust bag, none]
```

**Mode BRAND_CAMPAIGN — output exactly:**

```
BRAND NAME:        [exact spelling and capitalisation]
HERO PRODUCT:      [the one item that anchors the campaign — see selection logic below]
PRODUCT LINE:      [list of products provided / detected: "Volcán Joven, Reposado, Añejo"]
SHARED MATERIALS:  [materials common to the line]
SHARED PALETTE:    [palette common to the line — proper colour names]
SHARED FINISHES:   [finishes common to the line]
PACKAGING SYSTEM:  [how packaging unifies the line]
```

**Hero product selection logic (BRAND mode only):**
The hero product is the one that best embodies the brand archetype AND has the strongest visual signature on screen. Default heuristic:
1. If one product is clearly the flagship (price tier, line position, brand naming hierarchy) → that one.
2. Otherwise pick the one with the most distinctive silhouette — the product that reads as "the brand" in a single frame.
3. If still tied, ask the user once: *"Pour cette pub de marque, quel produit veux-tu mettre au centre du film ?"*

### Section 2: PRICE TIER & POSITIONING

Place the product on this scale and justify in one sentence:
- **MASS** (Walmart / Amazon basics)
- **PREMIUM** (Sephora, Apple, Aesop entry)
- **LUXURY** (Hermès, Loro Piana, Patek)
- **ULTRA-LUXURY / BESPOKE**

Then write the positioning statement using this canonical framework:

> *For [target audience] who [insight or unmet need], [product] is the [frame of reference] that delivers [primary benefit] and [secondary benefit], unlike [main competitor or commodity alternative].*

### Section 3: BRAND ARCHETYPE

Pick ONE primary archetype from this list and ONE secondary if relevant. State why.
- **The Heritage** — old-money, inherited, time-tested (e.g. Hermès, Patek)
- **The Outlaw** — disruptive, anti-establishment (e.g. Harley, Off-White)
- **The Artisan** — craft-obsessed, slow, hand-made (e.g. Aesop, Le Labo)
- **The Innovator** — future-forward, technical (e.g. Apple, Dyson)
- **The Hedonist** — pleasure-first, sensual (e.g. Tom Ford, Gucci)
- **The Minimalist** — restraint, essentialism (e.g. Muji, COS)
- **The Adventurer** — performance, outdoor, freedom (e.g. Patagonia, Yeti)
- **The Ritualist** — daily ceremony, mindfulness (e.g. La Mer, Diptyque)

### Section 4: TARGET PERSONA

```
PRIMARY PERSONA:
• Age range: [e.g. 32-45]
• Income tier: [e.g. top 10% household income]
• Cultural references: [3-5 brands/media this person already consumes]
• Aesthetic vocabulary: [3-5 words this person uses to describe what they like]
• Where they discover products: [TikTok, Substack, IG, word-of-mouth, retail, editorial]
• Emotional driver: [the one feeling the purchase produces — status / peace / belonging / mastery / freedom]
```

### Section 5: JOBS-TO-BE-DONE

Three jobs the product is hired to do, ranked:
1. **Functional job** — what it physically does
2. **Emotional job** — what it makes the buyer feel
3. **Social job** — what it signals to others

Use this to identify the ONE job advertising should lead with. Most luxury / lifestyle ads lead with #2 or #3, never #1.

### Section 6: SENSORY SIGNATURE

The product's full sensory footprint. Even if the product isn't sensory by nature, describe what it would feel like in use.

```
VISUAL: [silhouette, weight in the hand, light behaviour]
TACTILE: [texture, weight, temperature]
AUDITORY: [sound it makes — click, hush, snap, hum, none]
OLFACTIVE: [scent if any — leather, paper, ambroxan, citrus, none]
KINETIC: [how it moves or is moved — drape, pour, snap, glide]
```

This section directly feeds the storyboard's foley/sound design and macro-shot decisions.

### Section 7: BRAND WORLD

The visual universe the product lives in. Be specific — no generic "luxury feel".

**If a brand book is provided** (`BRAND_BOOK_STATUS = PROVIDED`), populate this section by **copying directly from brand book §4 (palette) and §8 (photography & film direction)**. The brand book is the source of truth — your job here is to confirm fit and add product-specific nuance, not to invent a new visual world.

**If no brand book is provided** (`BRAND_BOOK_STATUS = SKIP`), generate this from scratch based on the product, the price tier, and the archetype identified in Section 3.

Output format (same in both cases):

```
ENVIRONMENTS:    [3 specific locations the product belongs in — e.g. "1965 Tuscan cliffside villa" not "luxury home". Inherit from brand book §8 if provided.]
LIGHTING:        [time of day + quality. Inherit from brand book §8 SIGNATURE LIGHT if provided.]
COLOUR PALETTE:  [4-5 specific colours with proper names. INHERIT EXACTLY from brand book §4 if provided.]
TYPOGRAPHY:      [reference to brand book §5 if provided, or "TBD by downstream stages" if not]
WARDROBE:        [if a person appears — inherit casting direction from brand book §8 if provided]
TEXTURE PALETTE: [stone, linen, walnut burl, brushed brass, etc]
SOUND WORLD:     [V8 purr, distant waves, cello undercurrent, wind, none. Inherit from brand book §9 if provided.]
PACE:            [contemplative / kinetic / restrained / urgent. Inherit tone from brand book §7 if provided.]
SOURCE:          [BRAND_BOOK_INHERITED / DERIVED_FROM_PRODUCT — be explicit]
```

### Section 8: ANTI-PATTERNS

What this product is NOT. List five. This protects the creative from drifting into clichés or wrong-tier visual language.

Example for a heritage GT car:
- NOT a track car (no apex, no telemetry)
- NOT new wealth (no Dubai skyline, no helicopters)
- NOT a family car (no kids, no shopping bags)
- NOT a tech demo (no UI screens, no specs on display)
- NOT urban (no city, no traffic)

### Section 9: THREE ADVERTISING ANGLES

Three distinct creative angles for the same product. Each one is a different campaign concept. Format each as:

```
ANGLE [N]: [SHORT TITLE — 3-5 WORDS]
• Hook line (the one sentence that opens the ad): [one sentence]
• Narrative pillar: [the story being told in 1 line]
• Tonal register: [solemn / playful / sensual / aspirational / nostalgic / defiant]
• Best-fit format: [15s social / 30s brand film / 60s manifesto / 6s pre-roll]
• What makes it different from angles 2 and 3
```

The three angles should genuinely diverge — not three flavours of the same idea. One should typically be the safe / category-typical play; one should push tone harder (more emotional, more nostalgic); one should be a contrarian or unexpected angle.

## Creative principles

1. **Specificity beats generality, always.** "Cypress-lined Tuscan road" not "Mediterranean setting". "Caramel leather" not "warm brown". "Solo cello undercurrent" not "emotional music".
2. **The product is a noun, the ad is a verb.** Identify what the product DOES to the buyer's life — not what it IS.
3. **Anti-patterns are as important as patterns.** Naming what something isn't sharpens what it is.
4. **Tier discipline.** A mass product dressed as luxury reads as fake; a luxury product dressed as mass loses pricing power. Calibrate visual language to the actual price tier identified in Section 2.
5. **Three angles must diverge.** If all three angles could share the same storyboard, you haven't done the work.

## Tone and style

- Write like a brand strategist, not a copywriter. No hype words.
- Use proper nouns and named references wherever possible (brands, locations, materials, colour names).
- Keep each section tight — this is a working document for downstream skills, not a deliverable for a client.
- Output in plain text with the section headers in CAPS. No emojis.

## Handoff

The output of this skill is the canonical input for:
- `ad-creative-director` (picks one of the 3 angles and develops it into a full creative brief)
- `ad-storyboard-builder` (uses the brand world, sensory signature, and chosen angle to build the storyboard)
- `video-prompt-builder` (final stage — turns the storyboard into Seedance-ready prompts)

The brand book (if any) flows through this skill into all downstream stages — its palette, typography, archetype, environment fingerprints, and tone are propagated as locked constraints.

Always end the output with one of these two lines:
- SINGLE_PRODUCT mode: *"Ready for ad-creative-director. Pick angle 1, 2, or 3 to develop further. Brand book status: [PROVIDED via brand-book-builder / PROVIDED by user / SKIP]."*
- BRAND_CAMPAIGN mode: *"Ready for ad-creative-director. Hero product: [name]. Pick angle 1, 2, or 3 to develop further. Brand book status: [PROVIDED via brand-book-builder / PROVIDED by user / SKIP]."*
