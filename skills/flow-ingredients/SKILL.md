---
name: flow-ingredients
description: >-
  Create, open, and manage Google Flow projects and visual Ingredients (characters,
  objects, environments, style references) via Antigravity's built-in browser.
  Use when the user wants to set up a Flow project, upload local reference images
  as Ingredients, or generate new reusable visual assets inside Google Flow.
---

# Flow Ingredients (Project & Asset Manager)

Manage Google Flow projects and build a consistent library of **Ingredients** (`Characters`, `Images`, `Videos`, `Voices`, `Avatars`, `Uploads`) via the built-in `browser` subagent.

## Prerequisites & Shared Rules

Follow the plugin rules in `rules/AGENTS.md` for:
- Browser subagent delegation (`TypeName: "browser"`, `Model: "flash"`) and session reuse via `send_message`.
- Fast-Path Authentication Check directly at `https://flow.google.com/`.
- Autonomy mode (`checkpoint` by default; `auto` when requested in the prompt).
- Hard pauses for Google sign-in, CAPTCHA, or credit/payment blocks.

## Workflow

### Step 1: Prepare Local Assets (If Uploading)

For every local image/media file to upload as an Ingredient:
1. Verify the local file exists and has a supported format (`.png`, `.jpg`, `.jpeg`, `.webp`, or video/audio where applicable).
2. Copy it to `<Artifact Directory Path>/browser_sandbox/uploads/<filename>` using a clean single-segment filename (e.g., `hero_front.png`).
3. Keep a mapping of `<filename>` -> desired Ingredient `@handle` / asset name.

### Step 2: Open Flow, Verify Signed-In Shortcut & Select/Create Project

Delegate to the `browser` subagent (or reuse the active Flow `browser` subagent via `send_message`):

- **Target URL**: `https://flow.google.com/` (never use legacy `labs.google/fx/tools/flow` redirects)
- **Instructions for the browser subagent**:
  1. If not already on `https://flow.google.com/`, navigate directly to `https://flow.google.com/`.
  2. Run the **Fast-Path Authentication Check** from `rules/AGENTS.md`: inspect the above-the-fold screenshot, DOM (`Account details` avatar button, floating `+ New project` button, `Open project` cards, absence of `Sign in`), and network (`batchexecute` HTTP `200`).
     - If signed in, proceed immediately without pausing.
     - Only stop and report `LOGIN_REQUIRED` if directly blocked on `accounts.google.com` or shown a logged-out `Sign in` screen.
  3. On the Flow dashboard, either open the requested existing project (`/project/<id>`) or click **`+ New project`** and edit the top-left project title textbox to rename it.
  4. Report the active project title, URL, and current credit balance (visible in `Account details` if needed).

### Step 3: Check Autonomy Mode (If Generating New Ingredients)

- **Uploading existing media** does not consume generation credits and can proceed immediately.
- **Generating new Image Ingredients** (`Mode: Image`, e.g., `Nano Banana 2.1`):
  - Check the live credit readout (`Generating will use <N> credits`) in the `Settings trigger` popover; the cost depends on the model and the user's plan, so never assume it is free.
  - In `checkpoint` mode: If credits > 0 (or if the user wants approval before creating assets), present the Ingredient prompt(s), model (`Nano Banana 2.1` / `Pro`), aspect ratio, and credit cost, and wait for confirmation.
  - In `auto` mode: Proceed directly, counting the cost against the run's credit ceiling (see `rules/AGENTS.md`).

### Step 4: Upload or Generate Ingredients

Send a follow-up instruction to the same `browser` subagent:

- **For dedicated `Characters` (`/project/<id>/character`)**:
  1. Click **`Characters`** in the left sidebar (when empty, opens **`New character`** directly) or top-right **`+` (`Add media menu`)** -> **`Create character`**.
  2. Either pick one of the 6 starter archetypes (`The Eccentric`, `The Professional`, `The Wildcard`, `The Familiar`, `The Wicked`, `The Fantastical`) or write a custom prompt in `"Describe your character..."`, optionally attaching `+` Ingredients, clicking **`Upload`**, or clicking **`Add from project`**.
  3. Confirm the model (`Nano Banana 2.1`) and read the live credit cost (in `checkpoint` mode, confirm with the user first if it is above 0), click **`Start generation`**, review the character sheet, and click **`Done`** to save it into the project's `Characters` library.
- **For `Collections` (organizing project media)**:
  - Use left sidebar **`Library`** -> **`+` (`Create collection`)** or top-right **`+` (`Add media menu`)** -> **`New collection`** to group related Ingredients and takes cleanly.
- **For `upload` Ingredients**:
  1. Click the top-right **`+` (`Add media menu`)** -> **`Upload`**, OR click **`+` (`Add ingredients to the prompt box`)** -> **`Upload media`**.
  2. Upload each staged `<filename>` via file chooser (`setInputFiles`).
  3. Rename the uploaded asset via its card `More options` -> **`Rename`** if needed so it is easy to find by name, and verify it appears under `All media` / `Uploads` (or the `@` picker).
- **For `generate` Image Ingredients**:
  1. Ensure the bottom composer's **`Flow Agent` toggle is OFF** (Manual Mode) for deterministic settings control.
  2. Click the **`Settings trigger`** button -> select **`Image`** -> configure Aspect ratio (`16:9`, `4:3`, `1:1`, `3:4`, `9:16`), Model (`Nano Banana 2.1`, `Nano Banana 2 Lite`, or `Nano Banana Pro`), `Thinking level` (`Low` / `Medium` / `High`, only if the selected model exposes it), and Output count (`x1`–`x4`).
  3. Enter the asset prompt in `"What do you want to create?"` and click **`Start generation`**.
  4. Wait for generation to finish, visually inspect candidates, **`Favorite`** or **`Rename`** the cleanest pick (and optionally use `Add to prompt` or `+` -> `Create character` for character sheets).

### Step 5: Record Ingredient Inventory & Hand Off

Summarize the verified Ingredients back to the user (and update `storyboard.md` if one is active):
- Active Flow project name, URL, and browser subagent `conversationId` (kept alive for downstream skills)
- Table of ready Ingredients (asset name / `@handle`, category, source, verified status)
- Ready to proceed to `flow-generate` to render shots using these Ingredients.
