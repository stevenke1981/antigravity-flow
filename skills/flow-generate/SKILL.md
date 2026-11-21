---
name: flow-generate
description: >-
  Generate video shots in Google Flow (Video -> Ingredients or Video -> Frames)
  via Antigravity's built-in browser. Supports Omni 1.2 Flash and Veo 3.1 model
  selection, resolution/duration/output count configuration, exact pre-flight credit
  checks, batch storyboard execution, visual inspection, and downloading clips in
  checkpoint or full-auto mode. Use when the user wants to render one or more shots,
  run a storyboard.md batch, re-roll a take, or download generated clips from Google Flow.
---

# Flow Generate (Shot Generation & Iteration)

Drive Google Flow (`https://flow.google.com`) via Antigravity's built-in `browser` subagent to generate individual video shots or execute a full multi-shot batch from `storyboard.md`.

## Prerequisites & Shared Rules

Follow `rules/AGENTS.md`:
- Reuse the active Flow `browser` subagent (`send_message`) whenever one is already open; otherwise invoke a new `browser` subagent (`Model: "flash"`) directly at `https://flow.google.com/` and apply the Fast-Path Authentication Check before pausing for login.
- Respect the detected autonomy mode (`checkpoint` by default; `auto` when the user prompt requests full-auto execution).
- Always pause for Google sign-in, CAPTCHA, or credit/payment blocks (`PAYMENT_REQUIRED`).

## Workflow

### Step 1: Resolve Shot Specification(s) & Autonomy Mode

Determine what is being generated:
- **Single shot**: Prompt (including explicit camera direction and audio cues, since Flow has no separate Camera Control menu), Video type (`Ingredients` for Text-to-Video / Ingredients-to-Video, or `Frames` for Start/End keyframe animation), Ingredients (`@handles`), keyframes (if any), aspect ratio (`16:9` or `9:16`), model (`Omni 1.2 Flash`, `Veo 3.1 - Lite [Lower Priority]`, `Veo 3.1 - Lite`, etc.), resolution (`360p` / `720p` for Omni), duration (`4s` / `6s` / `8s` / `10s`), and output count (`x1`–`x4`).
- **Batch from `storyboard.md`**: Read the shot list (`S01..Sn`) and select only shots whose `Status` is `pending` or `failed`, so an interrupted batch resumes where it stopped.
- **Autonomy mode**:
  - Check the user's current prompt (and `storyboard.md` header) for full-auto cues in any language (e.g., `"auto mode"`, `"full-auto"`, `"run unattended"`, `"batch without asking"`).
  - Default to `checkpoint` mode if unspecified. A plain batch request without full-auto cues stays in `checkpoint` mode.
  - Note any credit ceiling from the prompt or the storyboard's `Credit Budget` header.

### Step 2: Stage Local Keyframes (If Using `Video` -> `Frames`)

For any shot requiring local `Start` or `End` keyframes:
1. Copy each keyframe image to `<Artifact Directory Path>/browser_sandbox/uploads/<filename>` (single-segment filename).
2. Pass only the bare `<filename>` to the `browser` subagent.

### Step 3: Configure Manual Mode & Check Exact Credit Cost

Instruct the `browser` subagent to prepare the composer (without clicking `Start generation` yet in `checkpoint` mode):
1. Ensure the target Flow project is open.
2. **Switch to Manual Mode**: Verify the bottom composer's **`Flow Agent` toggle is OFF** (`aria-pressed="false"`), so direct generation controls are visible.
3. Click **`Settings trigger`** and configure:
   - **`Mode`**: `Video`
   - **`Video type`**:
     - `Ingredients` (for pure Text-to-Video with nothing attached, or Ingredients-to-Video using `+` `Add ingredients to the prompt box` / typing `@` -> filter tabs `All` / `Images` / `Videos` / `Voices` / `Characters` / `Avatars` / `Uploads` -> `Add to prompt`)
     - `Frames` (exposes `Start`, `Swap first and last frames`, and `End` slots in the prompt bar to upload or pick keyframes)
   - **Aspect ratio**: `16:9` or `9:16`
   - **Model (`Select model family`)**: e.g., `Omni 1.2 Flash`, `Veo 3.1 - Lite [Lower Priority]`, `Veo 3.1 - Lite`, or another requested model. Never select a model marked `Leaving <Date>`; if the storyboard names one, flag it and propose `Omni 1.2 Flash` instead.
   - **Resolution & Length / Duration**: e.g., `360p` or `720p` and `Length` slider for `Omni 1.2 Flash`, or `4s` / `6s` / `8s` for `Veo 3.1`
   - **Output count**: `x1`, `x2`, `x3`, or `x4`
4. Read the exact live credit indicator at the bottom of the Settings popover: **`Generating will use <N> credits`**.

### Step 4: Pre-Generation Checkpoint (In `checkpoint` Mode Only)

- **In `checkpoint` mode**: Present a concise pre-flight table (Shot ID, Video Type, Model, Resolution/Duration, Output Count, **exact credit cost from Flow UI**, Ingredients/Frames, Prompt summary) and ask the user to confirm before clicking **`Start generation`**. Also mention they can reply `"switch to auto mode"` to run the remaining shots without per-shot prompts. For a batch, present all shots in one table with the total cost and confirm once.
- **In `auto` mode**: Skip per-shot confirmation, but first announce the estimated run total (sum of live readouts) and the ceiling, per the Credit Budget rule in `rules/AGENTS.md`. Track cumulative spend through Step 6 and pause before any shot or retry that would exceed the ceiling.

### Step 5: Execute Shot Generation in Browser

Send semantic instructions to the `browser` subagent for each shot (or sequential batch):
1. Attach any required Ingredients (`+` -> `Add to prompt` or `@` mention) or upload `Start` / `End` frames (`Frames` mode).
2. Enter the prompt text into `"What do you want to create?"`.
3. Click **`Start generation`** (arrow button).
4. Wait for the generation progress indicator to complete (video generation typically takes 30 seconds to several minutes; wait patiently without refreshing unless stuck).
5. Verify the rendered video card(s) appear under `All media` / `Videos` with no error banner, policy block, or credit exhaustion notice.

### Step 6: Inspect, Iterate, or Download

- **Visual verification**: Ask the `browser` subagent to inspect the rendered video card thumbnails/playback and report any obvious defects (broken anatomy, wrong Ingredient identity, cut-off framing, policy refusal).
- **Card actions available**: Hover for `Favorite`, `Reuse prompt`, or `More options` (`Add to scene`, `Add to prompt`, `Download`, `Rename`, `Copy`, `Share`, `Publish to YouTube`, `Set project cover`, `Flag output`, `Move to trash`).
- **Download submenu (`More options` -> `Download`)**: Clicking/hovering `Download` opens a 4-option format/resolution submenu:
  - `270p Animated GIF` (0 credits)
  - `720p Original size` (0 credits)
  - `1080p Upscaled` (0 credits — recommended default for clean local exports)
  - `4K Upscaled · 50 credits` (consumes 50 credits — confirm first in `checkpoint` mode before selecting)
- **In `checkpoint` mode**: Report the generated takes and ask whether to `Favorite` / keep, `Reuse prompt` to re-roll, switch model/resolution (e.g., `360p` -> `720p` on `Omni 1.2 Flash`), `Add to scene`, or `Download` (specifying resolution tier).
- **In `auto` mode**:
  - If a shot fails with a transient error, retry once automatically (counting the retry against the credit ceiling); mark it `failed` if the retry also fails.
  - If a shot is blocked by a safety/policy filter, do **not** rephrase the prompt to get around it. Mark it `blocked`, continue with the remaining shots, and list every blocked shot with its refusal message in the final report.
  - `Favorite` the best take per shot (or click `More options` -> `Add to scene` if assembling next) and `Download` clips if local export was requested.

### Step 7: Sync Outputs & Status

- Update each shot's `Status` field (`generated`, `favorited`, `added-to-scene`, `downloaded`, `blocked`, `failed`) in `storyboard.md` if present, and report the credits actually spent.
- If clips were downloaded, copy/organize them into the requested local output directory (e.g., `./outputs/shots/S01.mp4`) with clean shot-numbered filenames.
- Hand off to `flow-scene-builder` if the user wants to sequence, extend, edit, and export the final film.
