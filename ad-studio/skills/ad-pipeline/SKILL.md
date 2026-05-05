---
name: ad-pipeline
description: End-to-end AI advertising pipeline — orchestrates a structured intake conversation, optional visual brand book/product cleanup, product or brand analysis, creative direction, one visual storyboard board, one Seedance 2.0 Reference-to-Video prompt, and one final video using the storyboard image as reference_image_url. Supports two modes (single product OR full brand campaign) and three brand book scenarios (provided / sparse / to-create / skip). Use this skill as the MASTER ORCHESTRATOR when the user wants the whole pipeline run end-to-end. Trigger when the user says things like "make me an ad", "lance le pipeline", "full ad for this product", "pub pour cette marque", "campaign for my brand", or simply uploads visuals and asks for "une publicité" / "an ad" / "a campaign". This skill chains six sub-skills in order — pipeline-intake → (brand-book-builder if needed) → product-deconstructor → ad-creative-director → ad-storyboard-builder → video-prompt-builder — and produces exactly two final deliverables: one storyboard image and one final video. Do NOT use this skill if the user wants only one stage (use the relevant sub-skill directly instead).
---

# AI Advertising Pipeline — Master Orchestrator

This skill chains six sub-skills to take a request from raw input (a photo, a brand idea, a brief) all the way to exactly two final deliverables: one visual storyboard board and one Seedance 2.0 Reference-to-Video output that uses that storyboard image as `reference_image_url`.

## Single-deliverable contract

Each pipeline run produces exactly **one final storyboard image** and exactly **one final video**.

- Stage 1 may propose 3 advertising angles as strategic options.
- Stage 2 selects or receives exactly one angle and turns it into one creative brief.
- Stage 3 produces exactly one visual storyboard board: one generated image containing shared choices, character/product references, environment/set design, floor plan, 5 cuts, text, audio, lighting, mood, and camera notes.
- Stage 4 produces exactly one Seedance 2.0 Reference-to-Video prompt that uses the storyboard image as the primary `reference_image_url`, then one final video generation when the runtime credentials/tooling are available.
- Do not generate multiple storyboards, multiple competing scripts, or multiple final videos for the same request. If the user explicitly asks for variants, treat each variant as a separate pipeline run with its own selected angle, storyboard, prompt, and video.
- Optional product-image cleanup, improved packshot generation, or visual brand book generation can happen before Stage 1/2 when the user requests it. Those are preparation assets, not final deliverables.

## Pipeline overview

```
USER REQUEST
  │
  ▼
[0] pipeline-intake             → conversation 30s, locks: MODE, BRAND_BOOK_STATUS, ASSETS, RUNTIME, ASPECT_RATIO
  │
  ▼   (if BRAND_BOOK_STATUS = TO_CREATE or SPARSE)
  │
[0b] brand-book-builder         → full brand book (10 sections) — ONLY if needed
  │
  ▼
[1] product-deconstructor       → intelligence sheet (9 sections, 3 angles)
  │                                — SINGLE_PRODUCT or BRAND_CAMPAIGN mode
  │                                — inherits brand book if provided
  │
  ▼   user picks angle (or default = best fit for tier + archetype)
  │
[2] ad-creative-director        → creative brief (8 sections)
  │
  ▼
[3] ad-storyboard-builder       → ONE visual storyboard image
  │                                — shared choices + refs + floor plan + 5 cuts + audio
  │                                — generated with GPT Image 2 / imagen in Codex
  │
  ▼
[4] video-prompt-builder        → single Seedance 2.0 R2V prompt
  │                                — storyboard image passed as reference_image_url
  │
  ▼
Seedance R2V generation        → one final 15s video
  │
  ▼
OUTPUT: one storyboard image + one final video
```

## How this skill works

### Stage 0 — Intake (always first)

Run `pipeline-intake` to conduct the structured opening conversation. This locks five decisions:
- **MODE**: SINGLE_PRODUCT vs BRAND_CAMPAIGN
- **BRAND_BOOK_STATUS**: PROVIDED / SPARSE / TO_CREATE / SKIP
- **ASSETS**: list of visual material received
- **RUNTIME**: CODEX / FAL_AI / KIE_AI
- **ASPECT_RATIO**: 16:9 / 9:16 / 1:1 / other supported ratio

The intake outputs a structured INTAKE SUMMARY block. The orchestrator reads it and routes accordingly.

### Stage 0b — Brand book (conditional)

If `BRAND_BOOK_STATUS = TO_CREATE` or `SPARSE` → run `brand-book-builder` before going further. The user must approve the generated brand book before proceeding (one explicit confirmation: *"Brand book OK pour toi ? On continue ?"*). If they want changes, iterate once. After approval, treat `BRAND_BOOK_STATUS` as PROVIDED for the rest of the pipeline.

If `BRAND_BOOK_STATUS = PROVIDED` → ingest the brand book the user shared. Confirm key fields in one line.

If `BRAND_BOOK_STATUS = SKIP` → proceed without brand inheritance. Note clearly in the final output: *"No brand book — visual decisions derived from product alone."*

### Optional preparation before Stage 1

If the user's product image is poor, inconsistent, cropped, or not usable as a source, offer to create or improve a clean product packshot first. If the user has no visual brand book and wants one, run `brand-book-builder` as a visual brand book preparation step. These preparation assets are allowed, but they are not the final storyboard deliverable.

### Stages 1-4 — Analysis to storyboard image to video

1. Run **stage 1** (`product-deconstructor`) end-to-end in the mode locked at intake. The brand book (if any) is passed as constraint material. Output the full intelligence sheet.
2. **Angle decision:** if the user hasn't specified an angle, pick the one that best fits the price tier, archetype, and (if applicable) brand book tone. State the choice and reasoning in one line. Don't ask — keep the pipeline running.
3. Run **stage 2** (`ad-creative-director`) using the chosen angle. Output the full creative brief. Brand book tone of voice (§7) and anti-patterns (§10) must be respected here — flag any conflict before proceeding.
4. Run **stage 3** (`ad-storyboard-builder`). Output is **one visual storyboard board image only**. It is a single generated image containing all production information: shared choices, character/product references, environment/set design, floor plan, 5 cuts totalling exactly 15 seconds, text/VO/audio, lighting/mood/style notes, and cinematography notes. In Codex, generate this image with the built-in imagen / GPT Image 2 tool.
5. Run **stage 4** (`video-prompt-builder`) — generate **one single Seedance 2.0 Reference-to-Video prompt** using the storyboard board image as `reference_image_url`. Do NOT generate 7 separate references by default, do NOT create per-cut image-to-video prompts, and do NOT create multiple video concepts.
6. Generate the final Seedance 2.0 Reference-to-Video output when runtime credentials/tooling are available. If the environment cannot execute video, save the exact prompt/API payload and clearly mark the video as pending external generation.
7. Append the final **GENERATION CHECKLIST** adapted to runtime + Seedance R2V mode.

## Final GENERATION CHECKLIST

A condensed action list the user can hand to a junior producer or run themselves. The image phase creates one storyboard board image. The video phase is one Seedance Reference-to-Video call using that storyboard image as `reference_image_url`.

### If Path A (Codex with bundled imagen):

Use this path whenever the pipeline is running inside Codex. In Codex, image generation is always GPT Image 2 via the built-in imagen tool and requires no API key. Do not route image generation through fal.ai or kie.ai in Codex; fal.ai may still be used later for the Seedance 2.0 video call.

```
═══════════════════════════════════════════════════════════════
GENERATION CHECKLIST — Codex / imagen path
═══════════════════════════════════════════════════════════════

PHASE A — REFERENCE IMAGES (GPT Image 2 via Codex imagen tool)
  □ Generate ONE visual storyboard board image
    • Includes shared choices, character/product refs, environment/set design, floor plan, 5 cuts, audio/VO, lighting/mood/style, cinematography notes
    • Built with GPT Image 2 via Codex imagen, no API key needed
    • Save the final storyboard image into the project outputs folder

PHASE B — VIDEO GENERATION (Seedance 2.0 Reference-to-Video — SINGLE CALL)
  □ Submit ONE Seedance 2.0 Reference-to-Video generation:
    • Mode:           REFERENCE_TO_VIDEO (multi-shot, 15s)
    • Reference:      storyboard_image_url
    • Prompt:         the multi-shot prompt from video-prompt-builder
    • Aspect ratio:   selected aspect ratio from intake
    • Duration:       15s (5 cuts following rhythm pattern from Storyboard SHARED CHOICES)
    → Output: a single 15s 1080p MP4 with all 5 cuts assembled, character/product/palette locked

PHASE C — AUDIO
  □ Voice-over recorded (or generated via ElevenLabs with speaker spec from brief)
  □ Music selected from reference tracks in brief
  □ Foley layered per Storyboard §5
   (Note: Seedance 2.0 generates native audio synced to the video. Layer extras only if needed.)

PHASE D — EDIT
  □ Apply colour grade if needed (reference: Storyboard SHARED CHOICES palette)
  □ Mix audio at -14 LUFS for social
  □ Export selected hero aspect ratio, plus secondary crops if needed
═══════════════════════════════════════════════════════════════
```

### If Path B (external API — fal.ai or kie.ai):

```
═══════════════════════════════════════════════════════════════
GENERATION CHECKLIST — fal.ai / kie.ai API path
═══════════════════════════════════════════════════════════════

PREREQUISITE
  □ API keys set as env variables
      → fal.ai:   FAL_KEY      (for GPT Image 2 + Seedance 2.0)
      → kie.ai:   KIE_API_KEY  (for GPT Image 2 if using kie.ai)

PHASE A — STORYBOARD IMAGE (GPT Image 2 via fal.ai or kie.ai)
  □ POST to gpt-image-2 endpoint — generate ONE visual storyboard board image
    • Includes shared choices, character/product refs, environment/set design, floor plan, 5 cuts, audio/VO, lighting/mood/style, cinematography notes

PHASE B — VIDEO GENERATION (Seedance 2.0 Reference-to-Video via fal.ai — SINGLE CALL)
  □ POST to Seedance 2.0 endpoint with:
    • mode:           "reference_to_video"
    • reference_image_url: storyboard_image_url
    • prompt:         multi-shot prompt from video-prompt-builder
    • aspect_ratio:   selected aspect ratio, e.g. "16:9" or "9:16"
    • duration:       15
    → Output: a single 15s 1080p MP4 with all 5 cuts assembled

PHASE D — AUDIO
  □ Voice-over recorded (or generated via ElevenLabs with speaker spec from brief)
  □ Music selected from reference tracks in brief
  □ Foley layered per Storyboard §5

PHASE E — EDIT
  □ Apply colour grade if needed
  □ Mix audio at -14 LUFS for social
  □ Export selected hero aspect ratio, plus secondary crops if needed
═══════════════════════════════════════════════════════════════
```

**Important** : whichever path is used:
- Image generation MUST be **GPT Image 2**. No substitution.
- Video generation MUST be **Seedance 2.0 in Reference-to-Video mode** — a single call using the visual storyboard board as `reference_image_url`, NOT 5 individual image-to-video calls.

## Operating principles

1. **Never skip a stage.** Each sub-skill produces structure that the next consumes. Skipping = ambiguity = bad output. Stage 0 (intake) is always first; stage 0b (brand book) is conditional.
2. **Keep all stage outputs in the final response** — don't summarise or condense upstream documents. The user (or their downstream tools) needs the full record.
3. **Locked format: 15s exactly, 5 cuts, selected aspect ratio.** Not a default — a hard constraint. Seedance 2.0 generates up to 15s in a single multi-shot pass; the rhythm patterns are calibrated to that canvas.
4. **Reference-to-Video, not image-to-video.** The video generation is ONE call with the storyboard board image as `reference_image_url` and one multi-shot prompt — not 5 sequential calls. This is the cohesion mechanism.
5. **Two final deliverables.** One selected angle becomes one creative brief, one visual storyboard image, and one final video. The pipeline must not branch into alternate storyboards or alternate videos unless the user requests separate variant runs.
6. **Brand book inheritance.** When a brand book is in play, palette / typography / archetype / environment fingerprints / tone / anti-patterns are inherited — they are not re-invented at later stages.
7. **The hero shot is preserved end-to-end.** The cut marked ★ HERO SHOT in stage 3 must receive priority emphasis in the multi-shot prompt at stage 4.
8. **Tier discipline holds across stages.** A mass product entering as "premium positioning" must not balloon into a Hermès-tier brief by stage 2. Recheck against Section 2 of the intelligence sheet at every stage.
9. **Naming consistency.** Character names, environment names, colour palette names — once locked in stages 0b/1, they appear identically in stages 2/3/4. Copy-paste discipline.

## Output formatting

The full pipeline output is long (8-15 pages depending on whether brand book was generated). Structure as:

```
# AD PIPELINE OUTPUT — [Brand / Product Name] — [Campaign Title]

## STAGE 0 — INTAKE SUMMARY
[output of pipeline-intake — the 12-line structured block]

## STAGE 0b — BRAND BOOK   [if applicable]
[full output of brand-book-builder]

## STAGE 1 — INTELLIGENCE SHEET
[full output of product-deconstructor — SINGLE_PRODUCT or BRAND_CAMPAIGN mode]

## STAGE 2 — CREATIVE BRIEF (Angle [N])
[full output of ad-creative-director]

## STAGE 3 — VISUAL STORYBOARD
[storyboard board spec + saved storyboard image path — one image containing all production references and 5 cuts]

## STAGE 4 — SEEDANCE 2.0 R2V PROMPT
[full output of video-prompt-builder — single prompt using storyboard_image_url]

## FINAL VIDEO
[saved video path/URL if generated, or exact pending API payload if the current environment cannot execute video]

## GENERATION CHECKLIST
[as per template above — adapted to runtime]
```

When a stage produces a particularly long sub-skill output, that's expected and correct — do not truncate.

## Tone and style

- The pipeline output is a working document, not a deliverable for a client. Treat it as internal.
- Maintain each sub-skill's voice — they're designed to read differently (strategist → director → DP → prompt engineer). Don't homogenise them.
- The only "voice" this orchestrator adds is the GENERATION CHECKLIST and the section dividers.

## When to stop and ask the user

The intake stage (stage 0, `pipeline-intake`) is designed to ask all the necessary questions up front. After intake, the pipeline runs without further questions except in these specific cases:

1. **Brand book approval** (only if stage 0b was triggered): one explicit confirmation before proceeding to stage 1 — *"Brand book OK pour toi ? On continue ?"*
2. **Hero product tiebreaker** (only in BRAND_CAMPAIGN mode if no clear flagship): one question to identify which product anchors the campaign.
3. **Tier-goal contradiction** (e.g. user asks for a luxury campaign for a clearly mass product): flag once and propose two paths.

In all other cases — run the pipeline.

The duration is NOT negotiable: every spot from this pipeline is exactly 15 seconds across 5 cuts in selected aspect ratio. If the user asks for a 30s or 60s spot, explain that this pipeline is purpose-built for Seedance 2.0's 15s multi-shot canvas. Do not split a single request into multiple videos automatically; only create additional 15s variants if the user explicitly asks for separate variant runs.

