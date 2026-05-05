# Visual Storyboard Workflow

Current production rule:

1. Optional preparation
   - Improve product images if the user asks.
   - Create a visual brand book if the user asks or has none.
   - These assets are preparation only.

2. Final deliverable A: one visual storyboard board
   - Generated with GPT Image 2.
   - In Codex, use the built-in imagen tool. No API key is needed for images.
   - The board must contain shared choices, character/product references, environment/set design, floor plan, exactly 5 cuts, text/VO/audio, lighting/mood/style, and cinematography notes.
   - The board must use the selected aspect ratio from intake.
   - The board is saved as a project image asset.

3. Final deliverable B: one video
   - Generated with Seedance 2.0 Reference-to-Video.
   - The storyboard board image is passed as `reference_image_url`.
   - The video is one 15-second ad with exactly 5 cuts in the selected aspect ratio.

Do not generate separate character sheets, separate environment plates, or separate cut seed frames by default. Only do that if the user explicitly asks for preparation assets before the storyboard.


