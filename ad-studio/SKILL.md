---
name: pipeline-intake
description: Conduct a structured intake conversation at the start of any AI advertising request to determine mode (single product / full brand campaign), brand book status (provided / to-create / skip), assets available (product photos / brand assets / logos), and image-generation runtime (Codex / fal.ai / kie.ai). Use this skill as the FIRST step of any new ad creation request — BEFORE product-deconstructor, BEFORE brand-book-builder, BEFORE anything else. Trigger when the user says "I want to create an ad", "make me a publicity", "lance le pipeline", "j'ai un produit à mettre en avant", "I have a brand", or any opening request that initiates the AI ad pipeline. Outputs a structured intake summary that the orchestrator (ad-pipeline) uses to route to the correct downstream stages. Do NOT use this skill mid-pipeline — it's an opening gambit, not a recovery tool.
---

# Pipeline Intake

The conversation that sets up everything else. This skill spends 30 seconds with the user up front to make sure the next 5 minutes of pipeline work hit the right target.

## How this skill works

This skill conducts a **short, structured conversation** with the user — never more than 4 exchanges. The goal is to lock five decisions:

1. **MODE** — single product ad, or full brand campaign?
2. **BRAND BOOK** — does the user have one? need one created? or skip entirely?
3. **ASSETS** — what visual material does the user provide?
4. **RUNTIME** — Codex (imagen included) or external API (fal.ai / kie.ai)?
5. **ASPECT RATIO** — 16:9, 9:16, 1:1, or other supported ratio?

Once these four are locked, the skill outputs an intake summary that `ad-pipeline` reads to route work.

## Conversation flow

Open with a single welcoming message that asks the **first two questions together** (efficient, friendly, no over-ceremony). Then in the next exchange, ask the remaining clarifications.

### Opening message — exactly this structure (adapt language to user's language):

```
Avant de lancer le pipeline, deux ou trois choses à caler ensemble.

1. MODE
   Tu veux faire une pub pour :
   (a) UN produit spécifique
   (b) UNE marque entière (avec plusieurs produits possibles)

2. BRAND BOOK / CHARTE
   Est-ce que tu as déjà :
   (a) une charte de marque / brand book complète à me partager (palette, typo, ton, photo direction…)
   (b) quelques éléments épars (juste le nom, un logo, des couleurs…)
   (c) rien du tout — dans ce cas je peux te la créer

Réponds en quelques mots, pas besoin de tout détailler — on précisera après.
```

Use `ask_user_input_v0` if available in the environment to make this tappable. Otherwise plain text.

### Second message — based on answers, ask the missing pieces:

Three remaining things to clarify, in order:

**A. Assets**
- If MODE = single product → "Envoie-moi la photo du produit (packshot ou contexte), ou un lien vers la fiche."
- If MODE = brand → "Envoie-moi 3-5 visuels représentatifs : photos produits, lifestyle, packaging, logo. Plus tu en partages, plus la pub sera juste."

**B. Brand book continuation (only if option (b) — sparse — was chosen)**
- "Partage-moi ce que tu as : nom, logo, palette, typo, ton de voix… même partiellement. Je complète ce qui manque."

If option (c) — none — was chosen, route to `brand-book-builder` first. Don't proceed with the ad pipeline until the brand book is generated and approved by the user.

**C. Runtime**
- "Tu lances ça depuis Codex (imagen inclus, pas de clé API) ou tu utilises fal.ai / kie.ai (avec clé API) ?"

**D. Aspect ratio**
- "Tu veux la vidéo finale en quel ratio : 16:9, 9:16, 1:1, ou autre ? Si tu ne sais pas, dis-moi la plateforme principale."

### Third message (only if needed) — confirm and proceed:

Recap in 4 lines and proceed. Example:

```
OK, on a tout :
• MODE : pub pour UN produit (le mezcal Volcán Reposado)
• BRAND BOOK : à créer (je m'en occupe en stage 0)
• ASSETS : 1 packshot bouteille + 2 photos lifestyle
• RUNTIME : fal.ai
• ASPECT RATIO : 16:9

Je démarre le pipeline. Première étape : génération de la brand book.
```

## Output structure — INTAKE SUMMARY

After the conversation, generate this structured block. The orchestrator reads it directly:

```
═══════════════════════════════════════════════════════════════
PIPELINE INTAKE — SUMMARY
═══════════════════════════════════════════════════════════════
MODE:               [SINGLE_PRODUCT / BRAND_CAMPAIGN]
PRIMARY_SUBJECT:    [name of the product OR name of the brand]

BRAND_BOOK_STATUS:  [PROVIDED / SPARSE / TO_CREATE / SKIP]
BRAND_BOOK_SOURCE:  [if PROVIDED: file/text reference. If SPARSE: list of fragments. If TO_CREATE: "via brand-book-builder". If SKIP: "user opted out, downstream skills use defaults"]

ASSETS:
• [list of every visual asset received — file paths or descriptions]
• [...]

RUNTIME:            [CODEX / FAL_AI / KIE_AI]
ASPECT_RATIO:       [16:9 / 9:16 / 1:1 / other supported ratio]

NEXT STAGE:         [BRAND_BOOK_BUILDER / PRODUCT_DECONSTRUCTOR / AD_CREATIVE_DIRECTOR]
═══════════════════════════════════════════════════════════════
```

## Routing logic — what comes next based on intake

| Brand Book Status | Mode             | Next Stage                                    |
|-------------------|------------------|------------------------------------------------|
| PROVIDED          | SINGLE_PRODUCT   | `product-deconstructor` (with brand book attached) |
| PROVIDED          | BRAND_CAMPAIGN   | `product-deconstructor` in BRAND mode (with brand book attached) |
| SPARSE            | any              | `brand-book-builder` first, completing fragments → then route as PROVIDED |
| TO_CREATE         | any              | `brand-book-builder` first → then route as PROVIDED |
| SKIP              | any              | `product-deconstructor` directly (user opted out of brand consistency) |

## Conversation principles

1. **Maximum 3 exchanges.** Onboarding fatigue kills momentum. If a question can be deduced or skipped, skip it.
2. **Group questions efficiently.** Ask 2-3 questions per message, never one at a time.
3. **Use the user's language.** If they wrote in French, conduct intake in French. If they wrote in English, English. Mirror tone (casual / formal).
4. **No marketing language.** This is an intake, not a pitch. Don't say "amazing", "exciting", or "let's craft together". Be direct.
5. **No premature reassurance.** Don't say "great choice!" after each answer. Just proceed.
6. **The runtime question is non-negotiable.** Always ask it — it changes the GENERATION CHECKLIST format downstream.

## Edge cases

- **User uploads a product photo with no other context.** Default assumption: SINGLE_PRODUCT mode, BRAND_BOOK_STATUS = SKIP unless they object. Still ask for runtime.
- **User provides a brand book PDF.** Read it (use pdf-reading skill if needed). Confirm key fields (palette, typography, archetype) in one line and proceed.
- **User wants to skip intake entirely.** Allow it once they've explicitly said so. Use defaults: SINGLE_PRODUCT, SKIP brand book, ask runtime only.
- **User is ambiguous between modes.** Ask one tiebreaker question: "Cette pub doit servir UNIQUEMENT ce produit, ou potentiellement servir d'autres produits de la marque (réutilisation des plans, du décor, du personnage) ?" — re-use = BRAND mode.

## Tone and style

- Conversational but tight. No filler.
- Use bullet points sparingly — only for the structured options in the opening message.
- The intake summary block (the 12-line structured output) is plain text, not chat — it's machine-readable for the orchestrator.

## Handoff

End the intake conversation with the structured summary block, followed by exactly one line:
*"Pipeline ready. Routing to: [NEXT_STAGE]."*
