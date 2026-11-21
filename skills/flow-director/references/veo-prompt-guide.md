# Google Flow Prompt Craft Guide (Omni 1.2 & Veo 3.1)

Use this reference when authoring shot and Ingredient prompts for Google Flow (`https://flow.google.com`).

## 1. Six-Part Cinematic Prompt Formula

Structure each video shot prompt clearly so `Omni 1.2 Flash` or `Veo 3.1` locks onto subject, motion, and atmosphere without ambiguity:

1. **Shot Framing & Lens**: e.g., `Medium close-up, 50mm anamorphic lens, shallow depth of field`
2. **Subject & Ingredients**: Reference Flow assets explicitly with `@handle` (e.g., `@lin_detective wearing a wet trench coat`)
3. **Action & Timing**: Concrete physical motion tailored to the selected clip length (`4s`–`10s`; avoid packing 3+ plot beats into one clip)
4. **Setting & Environment**: Specific location, weather, background life, textures
5. **Lighting & Color**: Key light direction, practical lights, color palette (e.g., `cool cyan neon reflections against warm tungsten streetlamps`)
6. **In-Prompt Camera Movement**: Because current Google Flow has no separate Camera Control menu, always write one clear primary camera move directly in the prompt

## 2. Camera Movement Vocabulary

Prefer single, deliberate movements over conflicting instructions:

- **Establishing**: `Slow crane down`, `Aerial wide push-in`, `Locked-off wide shot`
- **Intimacy / Tension**: `Slow dolly in`, `Subtle handheld breathing`, `Over-the-shoulder close-up`
- **Momentum**: `Tracking shot alongside subject`, `Low-angle follow cam`, `Whip pan to reveal`
- **Transition**: `Orbit 90 degrees around subject`, `Rack focus from foreground object to background`

## 3. Native Audio & Dialogue Cues

- **Ambient bed**: Describe the acoustic space (`distant city rain hitting metal awnings, low subway rumble`).
- **Foley / SFX**: Anchor sounds to visible actions (`heavy boots splashing through a puddle, metallic click of a lighter`).
- **Dialogue**: Keep lines short (1–2 sentences per clip), name the speaker, and specify vocal tone (`@lin_detective speaks in a calm, gravelly whisper: "We only get one shot at this."`).

## 4. Ingredients Consistency Tips

- Generate reference Ingredients in `Image` mode (`Nano Banana 2.1` or `Nano Banana Pro`) with neutral, even lighting and an uncluttered background so Flow can re-light the subject cleanly across scenes.
- Limit each shot to **1–3 Ingredients** (`@character`, `@prop`, `@style`) to avoid visual blending artifacts.
- For strict spatial continuity between two shots, either use **`Save frame`** inside the clip editor and feed it into `Video` -> `Frames` (`Start` slot), or use **`Extend`** directly on the clip's timeline.

## 5. Model & Credit Strategy (Google Flow)

Always check the live credit readout (`Generating will use <N> credits`) inside Flow's Settings popover before generating — costs vary by model and plan, so never assume an action is free:

- **Image Ingredients**:
  - `Nano Banana 2.1` (default) — fast, high quality; set `Thinking level` (`Low` / `Medium` / `High`) when the Settings popover exposes it for the selected model.
  - `Nano Banana 2 Lite` — lightweight drafts.
  - `Nano Banana Pro` — complex compositions or typography.
- **Video Generation**:
  - `Omni 1.2 Flash` — newest flagship for character/scene consistency and fewer jump cuts; supports `360p` (faster drafts) and `720p` (final), with adjustable `Length` slider.
  - `Veo 3.1 - Lite [Lower Priority]` — cheapest tier (may be 0 credits on some plans); ideal for low-cost blocking and timing tests (`4s` / `6s` / `8s`).
  - `Veo 3.1 - Lite` — low-cost faster iteration (`4s` / `6s` / `8s`).
  - Retiring tiers (`Omni 1.1 Flash`, `Veo 3.1 - Fast`, `Veo 3.1 - Quality`, marked `Leaving Dec 5`) — retiring 2026-12-05; do not use them in new plans.
