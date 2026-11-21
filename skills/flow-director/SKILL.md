---
name: flow-director
description: >-
  Plan a Google Flow AI filmmaking production: break a story idea, brief, or script
  into a structured storyboard with consistent Ingredients (characters, objects, styles),
  cinematic shot prompts (including in-prompt camera direction), audio cues, and
  Google Flow model/resolution choices (Omni 1.2 Flash, Veo 3.1, Nano Banana).
  Use when the user wants to plan a video, draft a shot list, or design prompts for Google Flow.
---

# Flow Director (Storyboard & Prompt Planner)

Turn a creative idea, script, or reference brief into a structured, execution-ready **Google Flow Production Plan** (`storyboard.md`) without spending any Flow credits.

## When to Use

- The user wants to plan a short film, commercial, music video, or multi-shot sequence for Google Flow.
- The user wants to refine or translate rough ideas into Flow-optimized cinematic prompts.
- The user wants a structured `storyboard.md` that downstream skills (`flow-ingredients`, `flow-generate`, `flow-scene-builder`) can execute in either `checkpoint` or `auto` mode.

## Workflow

### Step 1: Capture Creative Brief & Constraints

Extract from the user's request (ask briefly only if critical parameters are missing and cannot be reasonably inferred):

- **Concept / Narrative**: Logline, mood, genre, target length, and pacing.
- **Aspect Ratio**: `16:9` (landscape, default) or `9:16` (vertical) for video; `16:9`, `4:3`, `1:1`, `3:4`, or `9:16` for image Ingredients.
- **Budget / Model Tier Strategy**:
  - **Low-cost drafting**: `Veo 3.1 - Lite [Lower Priority]` (cheapest tier; may be 0 credits on some plans), `Veo 3.1 - Lite`, or `Omni 1.2 Flash` at `360p` for fast blocking.
  - **Hero / final video**: `Omni 1.2 Flash` at `720p` (best character and scene consistency, fewer jump cuts).
  - **Never plan with retiring models**: Do not assign any model marked `Leaving <Date>` in the Flow picker (currently `Omni 1.1 Flash`, `Veo 3.1 - Fast`, and `Veo 3.1 - Quality`, retiring 2026-12-05).
  - **Image Ingredients**: `Nano Banana 2.1` (default), `Nano Banana 2 Lite`, or `Nano Banana Pro`; set `Thinking level` (`Low` / `Medium` / `High`) only when the selected model exposes it in the Settings popover.
- **Existing Local Assets**: Any local character sheets, product photos, voices, or keyframes the user already has on disk.
- **Autonomy Preference**: Check whether the user requested `auto` mode (e.g., `"auto mode"`, `"full-auto"`, `"run unattended"`, or equivalent phrasing in any language) or default `checkpoint` mode, and record it in the plan header.
- **Credit Budget**: Any credit ceiling the user mentions (e.g., `"max 200 credits"`); record it in the plan header.

### Step 2: Design Consistent Ingredients

Identify recurring visual/audio anchors that must stay consistent across shots (matching Flow's Ingredients categories: `Characters`, `Images`, `Videos`, `Voices`, `Avatars`, `Uploads`):

- **Characters** (e.g., `@lin_detective`) — can be uploaded, generated as an Image Ingredient (`Nano Banana 2.1`), or created via Flow's `Create character` flow.
- **Objects / Props & Environments / Styles** (e.g., `@brass_compass`, `@rainy_neo_tokyo_style`).

For each Ingredient, specify:
- Handle (`@slug`, lowercase ASCII with underscores)
- Category (`Characters`, `Images`, `Uploads`, `Voices`, etc.)
- Source: `upload` (with local file path) or `generate` (with a crisp, neutral-background `Nano Banana 2.1` prompt)
- Visual traits to keep invariant across shots

### Step 3: Break Down the Shot List

Read [Flow Prompt Guide](./references/veo-prompt-guide.md) when crafting cinematic prompts, in-prompt camera choreography, or native audio cues.

> **Important UI calibration**: Current Google Flow does **not** have a separate Camera Control button or dropdown; always write camera framing, lens, and motion explicitly inside the prompt text.

For each shot (`S01`, `S02`, ...), define:
- **Video Type**: `Ingredients` (for Text-to-Video with no attachments, or Ingredients-to-Video with `@` mentions) or `Frames` (for Start / End keyframe animation)
- **Referenced Ingredients / Keyframes**: `@handles` or local `Start` / `End` frame paths (or `Save frame` from the previous shot)
- **Model, Resolution & Duration**: e.g., `Omni 1.2 Flash` (`360p` draft / `720p` final, `8s`) or `Veo 3.1 - Lite [Lower Priority]` (`4s` / `6s` / `8s`)
- **Output Count**: `x1`, `x2`, `x3`, or `x4`
- **Prompt**: Subject + action + setting + lighting/lens + explicit camera movement + style
- **Audio / Dialogue**: Ambient soundscape, SFX, and spoken dialogue
- **Scene Transition**: Cut (`Add to scene` / `Add clip`), `Extend` in the scene editor, or inline video edit (`Describe how to edit this video...`)
- **Status**: `pending` (updated by downstream skills to `generated`, `favorited`, `added-to-scene`, `downloaded`, `blocked`, or `failed`, so an interrupted batch can resume)

### Step 4: Write `storyboard.md`

Write the plan to a clean Markdown file (e.g., `storyboard.md` in the project workspace or artifact directory) using this structure:

```markdown
# Flow Production Plan: <Title>

- **Project Name**: <Flow project name>
- **Aspect Ratio**: 16:9
- **Default Video Model**: Omni 1.2 Flash (720p, 8s) / Veo 3.1 - Lite [Lower Priority] (draft)
- **Default Image Model**: Nano Banana 2.1 (Thinking: Medium, if exposed)
- **Autonomy Mode**: checkpoint | auto
- **Credit Budget**: <N> credits | none (auto mode then uses the announced run estimate as the ceiling)

## Ingredients

| Handle | Category | Source | Local Path / Generation Prompt |
| :--- | :--- | :--- | :--- |
| `@hero` | Characters | upload / generate | ... |

## Shot List

### S01 — <Shot Title>
- **Video Type**: Ingredients | Frames
- **Ingredients / Frames**: `@hero`
- **Model**: Omni 1.2 Flash (720p, 8s, x2)
- **Prompt**: ... (includes explicit camera movement & framing)
- **Audio**: ...
- **Scene Transition**: Add to scene -> Cut to S02 (or Extend)
- **Status**: pending
```

### Step 5: Hand Off to Execution

Present the storyboard summary and clearly offer the next step:
- Proceed to `flow-ingredients` (to create/upload the Ingredients in Google Flow) and `flow-generate` (to render the shots).
- Remind the user they can proceed in **checkpoint mode** (confirming exact credit cost before clicking `Start generation`) or reply with **"run in auto mode"** to execute the full pipeline end-to-end, optionally with a credit ceiling (e.g., **"run in auto mode, max 200 credits"**).
