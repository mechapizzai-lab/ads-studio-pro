# Seedance 2.0 Reference-to-Video Prompt - MechaPizzAI Watch

## SEEDANCE REFERENCE

mode: reference_to_video  
reference_image_url: `UPLOAD_OR_PUBLIC_URL_FOR_mechapizzai-watch-storyboard-board.png`  
local_storyboard_image: `ad-studio/examples/mechapizzai-watch/mechapizzai-watch-storyboard-board.png`  
duration: 15s  
aspect_ratio: 16:9  
cut_count: 5  
rhythm: 3 + 4 + 2 + 3 + 3  

## FINAL PROMPT

Create a single 15-second 16:9 landscape advertisement for the MechaPizzAI Watch using the uploaded storyboard board image as the primary reference. Follow the storyboard board exactly: product identity, color palette, environment, floor plan, cut order, cut durations, camera notes, mood notes, text, and audio direction.

The product is the MechaPizzAI Watch: glossy dark blue metal case, turquoise sunburst dial, blue textured strap, warm cream metallic indices and hands, smiling cyan robot mascot at 12 o'clock, small pizza icon in the lower subdial. Keep this design consistent in every shot.

Brand world: fun, tech, friendly, simple, authentic, relaxed, clean, memorable, cool. Use the MechaPizzAI palette: cyan #29C7E0, tech blue #1E90B8, deep navy #0D3B4E, warm cream #F5E7C8, near black #111317.

Cut 1, 00:00-00:03, DOLLY-IN WIDE:
Open on a clean creator desk at night. The watch rests on a black satin tabletop, partly in shadow. Cyan screen glow slowly reveals the blue case edge and turquoise dial. Camera pushes in smoothly from a low tabletop angle.

Cut 2, 00:03-00:07, PRODUCT ROTATION HERO:
The watch rotates toward camera in a premium product shot. The turquoise dial catches the cyan rim light. The robot mascot and pizza icon become readable. Keep the movement smooth, polished, and slow enough for product recognition.

Cut 3, 00:07-00:09, MACRO SLIDE:
Extreme macro detail. Glide across the blue textured strap, then land on the pizza icon in the lower subdial. The moment is tactile and near-silent, with shallow depth of field and crisp material detail.

Cut 4, 00:09-00:12, WRIST TRACK HERO SHOT:
The watch is worn on a wrist emerging from a blue MechaPizzAI hoodie sleeve. The wrist moves through cyan screen light. The dial flashes cleanly, and the hoodie pizza patch is visible in frame. This is the hero lifestyle shot: confident, friendly, creator-world energy.

Cut 5, 00:12-00:15, STATIC PACKSHOT:
Final packshot of the watch facing camera on a deep navy background. Cyan glow settles around the case. Final title appears: "MAKE TIME COOL." End cleanly with the MechaPizzAI mascot/mark feeling.

Camera style:
Premium product ad wearing a hoodie. Smooth intentional camera moves. Crisp macro detail. No chaotic handheld. No old-money luxury watch seriousness. No cheap gadget clutter. No childish slapstick mascot animation.

Lighting:
Deep navy base, cyan rim light, soft screen glow, subtle warm cream glints on metal details. Keep the visual world blue/cyan and clean.

Audio direction:
Soft electronic pulse, subtle watch tick, glass sweep, strap flex, UI chime, clean final bass pulse. Voice-over, if supported: "Un peu de tech. Un peu de fun. Et le temps devient plus cool."

Avoid:
Do not change the watch design. Do not remove the robot mascot or pizza icon. Do not introduce unrelated products, extra brand names, luxury cigar-lounge aesthetics, messy desk clutter, purple neon dominance, or cartoon chaos. Keep the result realistic, polished, friendly, and social-ad ready.

## FAL.AI PAYLOAD SHAPE

Use the current fal.ai Seedance 2.0 Reference-to-Video endpoint shape for your account. The important fields are:

```json
{
  "mode": "reference_to_video",
  "reference_image_url": "UPLOAD_OR_PUBLIC_URL_FOR_mechapizzai-watch-storyboard-board.png",
  "prompt": "[FINAL PROMPT ABOVE]",
  "aspect_ratio": "16:9",
  "duration": 15
}
```

Do not pass separate character sheets, product sheets, environment plates, or per-cut seed frames by default. The storyboard board image is the main reference.


