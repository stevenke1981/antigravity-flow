---
name: flow-scene-builder
description: >-
  Assemble, sequence, extend, edit, and export multi-shot videos inside Google Flow's
  Scenes view and Clip/Scene Editor (/project/<id>/edit/<clipId>) via Antigravity's
  built-in browser. Use when the user wants to combine generated Flow clips into a
  scene timeline, extend a shot, extract a frame with Save frame, run in-place video
  edits, or download the finished video.
---

# Flow Scene Builder (Scenes & Timeline Editor)

Operate Google Flow's **`Scenes`** view and **Clip/Scene Editor** (`/project/<projectId>/edit/<clipId>`) via Antigravity's built-in `browser` subagent to turn individual generated shots into a cohesive edited scene and download the final video.

> **UI calibration note**: Current Google Flow does not use a literal `"Scene Builder"` tab. Scenes are managed via the left sidebar **`Scenes`** view, top-right `+` -> **`New scene`**, clip card `More options` -> **`Add to scene`**, and clicking any clip/scene to enter the timeline editor (`/project/<projectId>/edit/<clipId>`).

## Prerequisites & Shared Rules

Follow `rules/AGENTS.md`:
- Reuse the active Flow `browser` subagent (`send_message`) so the project tab and generated clip library remain open.
- Respect `checkpoint` vs. `auto` mode:
  - Creating a scene, adding/reordering existing clips (`Add to scene`, `Add clip`), `Save frame`, and `Download media` do **not** consume credits and can run directly.
  - **`Extend`** and inline video edits (`"Describe how to edit this video..."`) invoke video generation and **do** consume credits — confirm first in `checkpoint` mode, or run directly in `auto` mode within the run's credit ceiling.

## Workflow

### Step 1: Confirm Timeline Order & Edit Operations

From `storyboard.md` or the user's request, identify:
- Ordered list of shots (`S01` -> `S02` -> ...) to include in the scene.
- Any clips that need AI **`Extend`** on the timeline, along with the continuation prompt.
- Any clips that need an in-place video edit (`"Describe how to edit this video..."` with `Omni 1.2 Flash` and optional `+` Ingredients).
- Any frame extractions needed for continuity (**`Save frame`** in the player controls).
- Desired export destination in the local workspace (e.g., `./outputs/final/<project-name>.mp4`).

### Step 2: Assemble Clips into a Scene

Instruct the `browser` subagent:
1. Inside the active Flow project:
   - Either hover each selected clip card in storyboard order -> click `More options` -> **`Add to scene`**, OR
   - Create a fresh scene via top-right `+` (`Add media menu`) -> **`New scene`** (or open an existing scene from the left sidebar **`Scenes`** tab), then add clips via the timeline's **`Add clip`** menu -> **`Add clip`**.
2. Click the scene / clip to open the **Clip/Scene Editor** (`/project/<projectId>/edit/<clipId>`).
3. Verify all intended clips appear on the timeline in the requested sequence (use `Zoom out` / `Zoom in` if needed to inspect the full timeline).

### Step 3: Extend, Edit, or Extract Frames

- **Save frame** (no credits): Scrub to the desired timestamp in the player and click **`Save frame`** to capture a clean keyframe asset for downstream `Frames` generation.
- **Extend** (consumes credits):
  1. Open the timeline **`Add clip`** menu -> select **`Extend`** and enter the continuation prompt.
  2. Read the live credit cost. In `checkpoint` mode, pause and confirm the prompt, model, and cost with the user; in `auto` mode, check it against the run's credit ceiling.
  3. Trigger generation and verify the new segment attaches cleanly to the timeline.
- **In-place Video Edit** (consumes credits):
  1. In the editor's bottom composer (`"Describe how to edit this video..."`), enter the edit instruction, optionally attach Ingredients via `+`, and choose `Select model family` (`Omni 1.2 Flash`).
  2. Read the live credit cost. In `checkpoint` mode, pause and confirm with the user; in `auto` mode, check it against the run's credit ceiling.
  3. Click **`Start generation`** and verify the edited result appears without an error badge.

### Step 4: Preview & Download Final Video

Instruct the `browser` subagent:
1. Use the player controls (`Play`, `Skip to previous clip`, `Skip to next clip`) to verify clip transitions and check for any error badges.
2. Click **`Download media`** in the top bar of the editor, or click **`Done editing scene`** / **`Done`** and use `More options` -> **`Download`** on the card to select from the 4-option submenu (`270p Animated GIF`, `720p Original size`, `1080p Upscaled`, or `4K Upscaled · 50 credits` — confirm first in `checkpoint` mode if selecting `4K Upscaled · 50 credits`). Project-wide export is also available via top-right `More options` -> **`Download project`**.
3. Wait for the download to finish and return the downloaded file path.

### Step 5: Organize Local Output & Summarize

- Copy the downloaded final video from the browser download path into the user's requested workspace output directory (e.g., `./outputs/final/<title>.mp4`).
- Report:
  - Final local file path (clickable markdown link)
  - Included shots, extensions/edits performed (with credits spent), and scene URL
  - Any shots that were skipped or need manual fine-tuning in the browser pane.
