# AI Advertising Pipeline — Skill System

Système complet de skills Codex pour transformer n'importe quel produit OU n'importe quelle marque (avec ou sans brand book existant) en publicité 1080p prête à générer, en passant par toutes les étapes pro d'une vraie agence : intake conversationnel, brand book si besoin, analyse, direction créative, **storyboard visuel unique**, et prompt Seedance 2.0 Reference-to-Video basé sur cette planche storyboard.

## Structure du projet

- `SKILL.md` : skill d'intake installé à la racine du pack.
- `skills/` : les skills du pipeline à installer ou partager.
- `VISUAL_STORYBOARD_WORKFLOW.md` : règle courte du workflow validé.
- `outputs/` : dossier volontairement propre pour les nouvelles runs.
- `examples/mechapizzai-watch/` : exemple validé complet avec storyboard board, prompt Seedance, vidéo et métadonnées fal.ai.

## Règle de livraison : 1 storyboard visuel / 1 vidéo

Chaque demande de publicité produit ou marque produit exactement **deux livrables finaux** :

1. **Une image storyboard unique** : une planche de production complète, dans l'esprit des exemples fournis, qui contient les shared choices, références personnage/produit, décors, floor plan, 5 cuts, textes, audio, mood, lighting, notes caméra.
2. **Une vidéo finale unique** : générée avec Seedance 2.0 Reference-to-Video en utilisant l'image storyboard comme référence principale (`reference_image_url`).

- `product-deconstructor` peut proposer 3 angles pour aider au choix stratégique.
- `ad-creative-director` en retient un seul et le transforme en un seul brief créatif.
- `ad-storyboard-builder` produit une seule **planche storyboard image** : 5 cuts, 15s, ratio choisi, toutes les infos visibles dans la planche.
- `video-prompt-builder` produit un seul prompt Seedance 2.0 Reference-to-Video qui référence cette planche storyboard.
- Le pipeline ne doit pas générer plusieurs storyboards, plusieurs scripts concurrents, ni plusieurs vidéos pour une même demande, sauf si l'utilisateur demande explicitement des variantes comme des jobs séparés.
- Les images produit améliorées, packshots propres ou brand books visuels sont des étapes préparatoires optionnelles, faites avant le storyboard si l'utilisateur les demande. Elles ne remplacent pas le livrable storyboard.

## Architecture du pipeline

```
┌────────────────────────────────────────────────────────────────────┐
│  USER REQUEST                                                       │
└────────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌────────────────────────────────────────────────────────────────────┐
│  [0] pipeline-intake                                                │
│  → Conversation 30s, 3 messages max                                 │
│  → Lock : MODE / BRAND_BOOK / ASSETS / RUNTIME                      │
└────────────────────────────────────────────────────────────────────┘
                              │
                              ▼  (si BRAND_BOOK_STATUS = TO_CREATE / SPARSE)
┌────────────────────────────────────────────────────────────────────┐
│  [0b] brand-book-builder        — CONDITIONNEL                      │
│  → Brand book complet (10 sections)                                 │
│  → Manifesto, palette, typo, photo direction, do/don't…             │
└────────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌────────────────────────────────────────────────────────────────────┐
│  [1] product-deconstructor                                          │
│  → Mode SINGLE_PRODUCT ou BRAND_CAMPAIGN                            │
│  → Hérite du brand book si fourni                                   │
│  → Fiche d'intelligence (9 sections) + 3 angles divergents          │
└────────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌────────────────────────────────────────────────────────────────────┐
│  [2] ad-creative-director                                           │
│  → Brief créatif complet (8 sections)                               │
│  → Format livré : 15s / 5 cuts / ratio choisi                       │
└────────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌────────────────────────────────────────────────────────────────────┐
│  [3] ad-storyboard-builder                                          │
│  → UNE planche storyboard visuelle : 5 cuts × 15s                   │
│  → Shared choices + références + décors + floor plan + audio        │
│  → Générée avec GPT Image 2 / imagen dans Codex                     │
└────────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌────────────────────────────────────────────────────────────────────┐
│  [4] video-prompt-builder                                           │
│  → UN SEUL prompt Seedance 2.0 R2V                                  │
│  → 1 référence principale : storyboard_image_url                    │
└────────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌────────────────────────────────────────────────────────────────────┐
│  OUTPUT : 1 storyboard + 1 vidéo finale + checklist                 │
└────────────────────────────────────────────────────────────────────┘
```

## Les 6 skills + l'orchestrateur

| Skill                    | Rôle                                                         | Quand l'utiliser seul                                  |
|--------------------------|--------------------------------------------------------------|--------------------------------------------------------|
| **pipeline-intake**      | Conversation d'ouverture, lock 4 décisions                  | Toujours en premier dans une nouvelle demande          |
| **brand-book-builder**   | Crée un brand book complet from scratch                     | Quand l'utilisateur n'a pas de charte                  |
| **product-deconstructor**| Analyse stratégique produit OU marque + 3 angles            | Pour comprendre avant tout brief créatif               |
| **ad-creative-director** | Développe UN angle en brief complet                          | Quand tu as déjà la stratégie et veux la direction     |
| **ad-storyboard-builder**| Planche storyboard visuelle complète (15s / 5 cuts / ratio choisi) | Quand tu as un brief et veux l'image storyboard finale |
| **video-prompt-builder** | Prompt Seedance 2.0 Reference-to-Video                       | Quand tu as l'image storyboard et veux le prompt final |
| **ad-pipeline** (master) | Chaîne TOUT end-to-end                                       | Usage par défaut pour une demande complète             |

## Comment l'utiliser

### Mode 1 — Pipeline complet (recommandé)

```
Utilisateur : [upload des photos / nomme une marque]
              "Crée-moi une pub"

Codex      : invoque ad-pipeline
              → stage 0 : intake (conversation 3 messages max)
              → stage 0b : brand book si besoin
              → stages 1-4 : analyse → brief → storyboard visuel → prompt R2V
              → GENERATION CHECKLIST finale
```

### Mode 2 — Étape par étape

Tu peux invoquer chaque skill individuellement si tu veux du contrôle granulaire à chaque étape.

### Mode 3 — Skip intake si tu as déjà tout

Si tu fournis tout le contexte d'entrée (mode + brand book + assets + runtime) dans ton prompt, l'orchestrateur saute le stage 0 et démarre directement au stage 1.

## Les deux modes de production

### MODE A — SINGLE_PRODUCT
Une pub pour UN produit spécifique. La pub vend ce produit. Les 3 angles sont 3 façons différentes de vendre CE produit.

### MODE B — BRAND_CAMPAIGN
Une pub pour la MARQUE entière. La pub vend l'univers, pas un SKU. Le skill identifie un **HERO PRODUCT** qui sert de véhicule visuel à l'idée de marque, mais le but est la résonance brand. Les 3 angles sont 3 façons de raconter LA MARQUE.

## Les quatre statuts de brand book

| Status        | Déclencheur                                                | Effet sur le pipeline                                |
|---------------|------------------------------------------------------------|------------------------------------------------------|
| PROVIDED      | L'user a une charte qu'il partage                          | Ingestion → propagation aux stages 1-4               |
| SPARSE        | L'user a quelques éléments (logo, nom, couleurs partielles)| Run brand-book-builder pour compléter → PROVIDED     |
| TO_CREATE     | L'user n'a rien                                            | Run brand-book-builder from scratch → PROVIDED       |
| SKIP          | L'user veut s'en passer                                    | Pipeline procède sans contraintes brand              |

## ⚠ Format imposé : 15 secondes / 5 cuts / ratio choisi

Toutes les pubs produites par ce pipeline font **exactement 15 secondes**, en **5 cuts**, dans le **ratio choisi par l'utilisateur**.

Le ratio n'est pas verrouillé par le pipeline. Il peut être `16:9`, `9:16`, `1:1` ou un autre ratio supporté par le runtime vidéo, mais il doit être verrouillé avant la génération de la planche storyboard.

**Pourquoi 15s ?** Seedance 2.0 génère jusqu'à 15 secondes en un seul pass multi-shot. C'est le canvas natif du modèle. Une pub de 14s gaspille du budget ; une pub de 16s force une seconde génération qui casse la cohérence.

**Pourquoi 5 cuts ?** Permet la grammaire narrative complète (établir → protagoniste → insert macro → climax → resolve) avec des cuts entre 2 et 4s — la fenêtre où Seedance produit ses meilleurs résultats.

### Patterns rythmiques canoniques

| Pattern   | Total | Usage                                                              |
|-----------|-------|--------------------------------------------------------------------|
| 3-4-2-3-3 | 15s   | **Défaut.** Long beat en cut 2, macro insert tactile en cut 3       |
| 4-3-2-3-3 | 15s   | Ouverture lente — on pose la géographie avant d'accélérer           |
| 3-3-2-4-3 | 15s   | Climax en cut 4 (le hero shot tient le long beat)                   |
| 3-3-3-3-3 | 15s   | Métronomique — uniquement quand le créa l'exige (rare, mécanique)   |

## ⚠ Modèle d'images : GPT Image 2 imposé

Toute génération d'image utilisée par le pipeline **doit** se faire avec **GPT Image 2**. Cela inclut surtout la planche storyboard finale. Pas de Midjourney, pas de Flux, pas d'Imagen 3.

Deux modes d'exécution :

| Path | Environnement                | Comment GPT Image 2 est appelé           | Clé API ?                      |
|------|------------------------------|------------------------------------------|--------------------------------|
| A    | Codex agent OpenAI           | Outil imagen intégré à Codex             | Non                            |
| B    | Tout autre environnement     | API fal.ai (`fal-ai/gpt-image-2`) ou kie.ai | Oui (`FAL_KEY` ou `KIE_API_KEY`) |

**Règle d'exécution obligatoire :**
Si le pipeline tourne dans Codex, la planche storyboard et toute image préparatoire demandée par l'utilisateur utilisent toujours l'outil imagen intégré à Codex / GPT Image 2. Aucune clé API n'est nécessaire pour les images dans Codex, et fal.ai ne doit pas être utilisé pour les images dans ce cas. fal.ai sert alors uniquement à la vidéo Seedance 2.0 R2V. Les APIs fal.ai / kie.ai pour GPT Image 2 ne sont utilisées que hors environnement Codex.

## ⚠ Mode Seedance 2.0 : Reference-to-Video (single call)

Le pipeline n'utilise PAS Seedance 2.0 en mode classique image-to-video-par-cut. Il utilise **Reference-to-Video** :

- **1 planche storyboard image** générée par GPT Image 2, contenant toutes les références et les 5 cuts.
- Cette planche est transmise à Seedance en `reference_image_url`.
- Seedance génère **les 5 cuts en une seule passe de 15s**, en suivant la planche storyboard comme source visuelle et narrative principale.

C'est la killer feature de Seedance 2.0. Générer cut-par-cut casse la cohérence. La planche storyboard unique donne à Seedance le casting, le produit, les décors, les cuts, la lumière, le texte et l'audio dans une seule référence.

## Frameworks intégrés

- **Positioning statement canonique** : *"For [audience] who [insight], [product] is the [frame of reference] that delivers [benefits], unlike [competitor]."* — standard luxury branding.
- **8 archétypes de marque** : Heritage / Outlaw / Artisan / Innovator / Hedonist / Minimalist / Adventurer / Ritualist (Jung-Pearson + adaptations modernes).
- **Jobs-to-be-done** (functional / emotional / social) — framework Christensen.
- **3-act energy arc** : establish → contemplate → resolve (luxury / lifestyle), pas hook/problem/solution.
- **Anti-patterns explicites** à chaque niveau (produit + brand book) pour empêcher les dérives clichées.

## Coût estimé d'une pub complète

- Brand book généré (si besoin) : ~0,10 € (texte uniquement)
- Phase A : 1 planche storyboard GPT Image 2 : coût variable selon runtime
- Phase B : 1 génération Seedance 2.0 R2V (15s 1080p) : ~3-5 €
- Phase D : audio (ElevenLabs voice + music license) : ~0,50 €
- **Total : ~5-7 € par pub finie**

À comparer à 2000-5000 € pour un spot équivalent en agence traditionnelle.
