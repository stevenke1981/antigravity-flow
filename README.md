# Antigravity Flow (`antigravity-flow`)

An Antigravity plugin that packages **Google Flow** ([flow.google.com](https://flow.google.com)) as a modular AI filmmaking and creative-tools workflow powered by Antigravity's built-in `browser` subagent — with zero external MCP servers or local Playwright daemons required.

## Installation

### From GitHub (Recommended)

```bash
agy plugin install https://github.com/stevenke1981/antigravity-flow
```

### Local Development (Symlink)

```bash
git clone https://github.com/stevenke1981/antigravity-flow.git
ln -s "$(pwd)/antigravity-flow" ~/.gemini/config/plugins/antigravity-flow
```

On Windows (PowerShell), use a directory junction instead (no admin rights required):

```powershell
git clone https://github.com/stevenke1981/antigravity-flow.git
cmd /c mklink /J "$env:USERPROFILE\.gemini\config\plugins\antigravity-flow" "$PWD\antigravity-flow"
```

Validate the plugin manifest and structure locally:

```bash
agy plugin validate . --strict
```

Then manage or toggle the plugin on Antigravity's Customizations page.

## Codex skill

This repository also includes [`$antigravity-flow`](codex/skills/antigravity-flow/SKILL.md), a standalone Codex skill for storyboards, Characters and Ingredients, generation, scene editing and export, Tools, and read-only UI audits. In a connected Codex browser session it can operate Google Flow. Storyboards and prompt drafts also work offline.

Copy the whole `codex/skills/antigravity-flow` directory into your user skills directory (`$CODEX_HOME/skills`, or `~/.codex/skills` if `CODEX_HOME` is unset). From this repository in PowerShell:

```powershell
$codexRoot = $env:CODEX_HOME
if (-not $codexRoot) { $codexRoot = Join-Path $HOME '.codex' }
$skillsDir = Join-Path $codexRoot 'skills'
$destination = Join-Path $skillsDir 'antigravity-flow'
if (Test-Path -LiteralPath $destination) { throw "Skill already installed: $destination" }
New-Item -ItemType Directory -Path $skillsDir -Force | Out-Null
Copy-Item -LiteralPath './codex/skills/antigravity-flow' -Destination $destination -Recurse
```

Start a Codex chat and use `$antigravity-flow`, for example: “Use $antigravity-flow to plan a four-shot Google Flow scene with a consistent character.”

## Included Customizations

### Rules (`rules/AGENTS.md`)
Always-on operating contract when the plugin is enabled:
- Opens `https://flow.google.com/` directly (avoiding legacy redirect hops) and delegates all Flow UI operations to Antigravity's built-in `browser` subagent (`Model: "flash"`), reusing the same tab/session via `send_message`.
- Runs a **Fast-Path Authentication Check** using above-the-fold visual, DOM (`Account details`, `+ New project`, `/project/<id>`), and network (`batchexecute` HTTP `200`) signals to skip unnecessary sign-in prompts when already signed in.
- Supports **Checkpoint Mode** (default: reads live `Generating will use <N> credits` and confirms before clicking `Start generation` or paid `4K Upscaled · 50 credits` exports) and **Full-Auto Mode** (automatically triggered from prompt cues like `"auto mode"`, `"full-auto"`, `"run unattended"`, or equivalent phrasing in any language).
- Enforces a **credit budget**: costs are always read live from Flow (never assumed free), and Full-Auto runs announce an estimated total and pause before exceeding it or a user-stated ceiling (e.g., `"max 200 credits"`).
- Enforces hard safety pauses for Google sign-in, CAPTCHA, and quota/payment walls. Policy-blocked prompts are logged and skipped, never rephrased to get around the filter.
- Standardizes local file staging for uploads (`browser_sandbox/uploads/`) and visual references (`browser_sandbox/references/`).

### Skills (`skills/`)
1. **`flow-director`** — Turn a story idea, brief, or script into a structured `storyboard.md` with consistent Characters/Ingredients, cinematic prompts (with in-prompt camera choreography), native audio cues, and Flow model choices (`Omni 1.2 Flash`, `Veo 3.1`, `Nano Banana 2.1`; includes `references/veo-prompt-guide.md`).
2. **`flow-ingredients`** — Create/open a Flow project, organize Collections (`New collection`), create consistent Characters (`/project/<id>/character` with 6 archetypes), upload local reference media (`Upload` / `Upload media`), or generate reusable Image Ingredients in Manual Mode (`Flow Agent` OFF, `Nano Banana 2.1` / `Pro`).
3. **`flow-generate`** — Generate video shots (`Video` -> `Ingredients` or `Video` -> `Frames`) single-shot or in batch from `storyboard.md`, read exact live credit costs (`Generating will use <N> credits`), visually inspect outputs, and download clips across the 4-tier Download submenu (`270p Animated GIF`, `720p Original size`, `1080p Upscaled`, `4K Upscaled · 50 credits`).
4. **`flow-scene-builder`** — Sequence generated clips in Flow's `Scenes` view and Clip/Scene Editor (`/project/<id>/edit/<clipId>`), extract frames (`Save frame`), extend or edit clips (`Extend` / inline video edit), and download the finished video (`Download media` / `Download project`).
5. **`flow-tools`** — Discover and operate Flow's `Explore tools` gallery (`/project/<id>/tools`: `My Tools`, `Community` spotlight creatives, and 34 curated `Templates` cataloged in `references/tools-catalog.md`) and build custom zero-credit interactive creative apps in `Create tool` (`/project/<id>/create-tool`).
6. **`flow-sync`** *(manual-only, `disable-model-invocation: true`)* — Explicitly triggered by the user (e.g., `/flow-sync`) to run a strictly read-only, zero-credit live audit of `https://flow.google.com` across 7 canonical surfaces (including `/changelogs`, model pickers, Characters, Scenes/Editor, Download tiers, and Tools; tracked in `references/audit-checklist.md`), diff against this plugin, add newly launched features, fix UI drift, and prune deprecated or removed capabilities.

## Autonomy Modes

- **Checkpoint Mode (default)**: The agent plans prompts, stages local assets, switches Flow to Manual Mode (`Flow Agent` OFF), configures model/resolution/duration, and reports the exact credit cost (`Generating will use <N> credits`) before clicking `Start generation` or choosing `4K Upscaled · 50 credits`.
- **Full-Auto Mode**: Mention `"auto mode"`, `"full-auto"`, `"run unattended"`, or `"without asking"` (or the equivalent in any language) in your prompt, or reply `"switch to auto mode"` mid-session, to execute Character/Ingredient setup, multi-shot generation, Scene assembly, and downloads end-to-end. The agent first announces the estimated run total and pauses before exceeding it (or a ceiling you state, e.g., `"auto mode, max 200 credits"`); it also still pauses whenever manual Google sign-in, CAPTCHA, or credit/payment intervention is required. A plain batch request (`"generate all shots"`) without these cues stays in Checkpoint Mode with a single batch-level confirmation.
