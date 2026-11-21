---
name: flow-tools
description: >-
  Discover, launch, and operate Google Flow's built-in Tools (/project/<id>/tools:
  My Tools, Community spotlight creatives, and curated Templates) and build custom
  interactive creative mini-apps with Create tool (/project/<id>/create-tool) via
  Antigravity's built-in browser. Use when the user wants to apply specialized visual
  effects, build posters, resize/stitch clips, explore camera angles, or create a
  custom zero-credit creative tool inside Google Flow.
---

# Flow Tools (Explore Tools & Create Custom Tools)

Operate Google Flow's **`Explore tools`** ecosystem (`/project/<projectId>/tools`) and **`Create tool`** builder (`/project/<projectId>/create-tool`) via Antigravity's built-in `browser` subagent.

## Prerequisites & Shared Rules

Follow `rules/AGENTS.md`:
- Reuse the active Flow `browser` subagent (`send_message`) whenever one is open; otherwise open `https://flow.google.com/` directly (`Model: "flash"`) and run the Fast-Path Authentication Check.
- Respect `checkpoint` vs. `auto` mode:
  - Browsing tools and **building a new custom tool in `Create tool`** currently cost **0 credits** (`"Creating tools does not currently cost credits"`).
  - Running generative steps inside an opened tool may consume credits depending on the underlying model — confirm first in `checkpoint` mode when a tool action triggers paid generation; in `auto` mode, count it against the run's credit ceiling.

## Workflow A: Run an Existing Flow Tool (`Explore tools`)

### Step 1: Select the Right Tool

Consult [Flow Tools Catalog](./references/tools-catalog.md) to match the user's goal to the right tab and tool name:
- **`Templates`** (34 curated tools across `Image`, `Video`, `Prompting`, and `Experimental`) — e.g., `Shot Explorer`, `Mask Magic`, `Transition Machine`, `Stringout Creator`, `Video Resizer`, `Type Overlays`, `Storyboard Studio`, `Whisk`.
- **`Community`** (`Spotlight creatives` + community gallery) — e.g., `Character Poster`, `Character Arc`, `Pixel Machine`, `Lucid Glass`, `Pocket Studio`, `CaptionCast`, `ThumbnailForge`.
- **`My Tools`** — previously created custom tools (`My creations`).

### Step 2: Stage Local Media (If Needed)

If the tool requires uploading a local image or video:
1. Copy the file to `<Artifact Directory Path>/browser_sandbox/uploads/<filename>` (single-segment filename).
2. Pass only the bare `<filename>` to the `browser` subagent.

### Step 3: Open & Run the Tool in Browser

Instruct the `browser` subagent:
1. Inside the active Flow project, click **`Tools`** in the left sidebar (navigates to `/project/<projectId>/tools`).
2. Select the target tab (`My Tools`, `Community`, or `Templates`) in the top radio group.
3. Locate and open the requested tool card.
4. Upload staged media (or select existing project assets) and configure the tool's controls according to the user's goal.
5. In `checkpoint` mode, pause before clicking any credit-consuming generation action; in `auto` mode, run end-to-end.
6. Verify the output visually, save to the project or download to local disk if requested, and return the result.

## Workflow B: Build a Custom Tool (`Create tool`)

Google Flow lets users generate custom interactive creative mini-apps from a plain-English description at zero credit cost (`"Creating tools does not currently cost credits."`).

### Step 1: Draft the Tool Specification

Before opening `Create tool`, clarify or draft a crisp specification covering:
- **Input / Drop zone**: What media or prompt inputs the tool accepts (e.g., video upload, image upload, microphone voice-over, text fields).
- **Interactive controls**: Sliders, preset buttons, style selectors, or sidebar parameters.
- **Output & export**: Preview player/canvas and save/download behavior.

### Step 2: Generate the Tool in Browser

Instruct the `browser` subagent:
1. Click **`Create tool`** in the left sidebar (or **`Create New`** under `My Tools`), opening `/project/<projectId>/create-tool` (`New tool`).
2. Either click one of the starter presets (`Voice over`, `Time stretcher`, `Style morph`) or enter the custom specification into the bottom prompt box (`"Build a retro pixel effect app with a large upload drop zone and a settings sidebar."`).
3. Click **`Start generation`** (0 credits) and wait for the generated mini-app to render.
4. Visually inspect the generated tool UI, report any missing controls or layout issues, and offer to iterate on the tool prompt or run it on the user's project media.
