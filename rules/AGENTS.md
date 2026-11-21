# Google Flow Plugin Operating Rules

These rules apply whenever the user works with **Google Flow** (`https://flow.google.com`) using this plugin.

## 1. Browser Delegation, Entry URL & Session Reuse

- Always drive Google Flow through Antigravity's built-in `browser` subagent (`invoke_subagent` with `TypeName: "browser"`, `Model: "flash"` unless the user specifies another model). Do not use `curl`, Playwright scripts, or external browser launchers to access Flow.
- Always open **`https://flow.google.com/`** directly as the canonical entry URL. Avoid navigating through legacy redirect URLs such as `https://labs.google/fx/tools/flow`, which can trigger an unnecessary `accounts.google.com` SSO hop even when the user is already signed in.
- Within a Flow production session, reuse the same `browser` subagent via `send_message` using its `conversationId` so the open project tab and authenticated session stay intact. Do not kill an idle `browser` subagent between steps.
- Give the `browser` subagent **semantic UI goals** (visible labels, controls, visual verification criteria) rather than brittle CSS selectors, so workflows remain resilient across Flow UI updates.

## 2. Fast-Path Authentication Check (Signed-In Shortcut)

Before assuming the user needs to sign in, always verify whether an authenticated Flow session is already active:

- **Check the current tab first** (if already on `https://flow.google.com/...`); otherwise navigate directly to `https://flow.google.com/`.
- **Verify signed-in state using visual, DOM, and network signals together**:
  - **Above-the-fold visual & DOM signals**: Presence of the top-right `Account details` button (user profile avatar and optional plan badge such as `ULTRA`/`PRO`), the floating `+ New project` button, the `Sort projects` control, or existing `/project/<id>` project cards (`Open project`), with no `Sign in` button.
  - **URL & network signals**: Page remains on `https://flow.google.com/` (for example `/?pli=1` or `/project/...`) and Flow's frontend RPC (`POST https://flow.google.com/_/AiSandboxAngularFrontend/data/batchexecute`) returns HTTP `200`.
  - **Sandbox constraint**: Do not attempt to read `document.cookie`, `localStorage`, or `sessionStorage` via `page.evaluate`, as those APIs are blocked by the sandboxed browser runtime.
- **Shortcut**: As soon as the signed-in signals are confirmed, skip all authentication prompts and proceed directly to the user's task.
- Only pause with `LOGIN_REQUIRED` when opening `https://flow.google.com/` directly blocks on `accounts.google.com` or renders a logged-out page with a `Sign in` button.

## 3. Autonomy Mode (Checkpoint vs. Full-Auto)

Two execution modes govern credit-consuming actions (generating images/Ingredients when credits > 0, generating video shots, extending clips, inline video edits, and `4K Upscaled · 50 credits` downloads):

- **`checkpoint` mode (default)**: Prepare prompts, stage assets, open the Flow project, and configure parameters automatically (reading the live `Generating will use <N> credits` indicator when shown), then **pause and ask the user to confirm** before clicking `Start generation` or triggering a paid `4K Upscaled` export.
- **`auto` mode (full-auto)**: Execute preparation, Ingredient/Character creation, multi-shot generation, visual verification, Scene assembly, and downloads end-to-end without per-shot confirmation.

### Detecting Mode from the User's Prompt

- Automatically enable `auto` mode whenever the user's prompt explicitly signals unattended execution without confirmation in any language — for example: `"auto mode"`, `"full-auto"`, `"run unattended"`, `"without asking"`, or `"don't ask for confirmation"`.
- A request to batch-generate (e.g., `"generate all shots"`) does **not** by itself enable `auto` mode. Stay in `checkpoint` mode, present the whole batch with its total credit cost, and confirm once before starting.
- If the user does not specify a mode, use `checkpoint` mode by default and mention that they can switch to `auto` mode at any time (e.g., by replying `"switch to auto mode"`).

### Credit Budget & Live Pricing (Both Modes)

- Credit costs depend on the model, the user's plan, and Flow's current pricing. Never assume an action is free; always read the live `Generating will use <N> credits` readout (or the price on a menu item such as `4K Upscaled · 50 credits`) before acting.
- **Run ceiling in `auto` mode**: Before the first credit-consuming action, sum the live per-action readouts for the planned work (shots × outputs, Ingredients, Characters, Extends, edits, paid exports) and announce the estimated run total. If the user stated a budget (e.g., `"max 200 credits"`), that is the ceiling; otherwise the announced estimate is the ceiling.
- Track cumulative spend, including automatic retries. Pause and ask the user before any action that would push cumulative spend past the ceiling.

## 4. Hard Safety & Platform Boundaries (Both Modes)

Even in `auto` mode, always pause and hand control to the user when the `browser` subagent encounters:

- **Google Sign-In / Authentication**: Protected auth origins cannot be automated. Ask the user to sign in inside the browser pane, then resume the same `browser` subagent via `send_message`.
- **CAPTCHA / Anti-Bot Verification**: Never attempt to solve or bypass challenges. Pause and ask the user to complete verification.
- **Credit / Quota Exhaustion or Payment Pages (`PAYMENT_REQUIRED`)**: Pause and ask the user to top up or adjust the model tier (e.g., switch from `Omni 1.2 Flash` at `720p` to `360p` or `Veo 3.1 - Lite`).

**Content policy / safety filter refusals** (both modes): Never rephrase, obfuscate, or otherwise alter a prompt to get around a Flow policy refusal. Log the blocked item with its refusal message, skip it, continue with the remaining work, and report every blocked item at the end so the user can decide how to revise it.

## 5. Local File Staging Contract

The `browser` subagent is sandboxed and cannot read arbitrary workspace paths directly:

- **For web uploads** (Ingredients images, start/end keyframes): Copy each file to `<Artifact Directory Path>/browser_sandbox/uploads/<filename>` (single-segment filename) and pass the bare `<filename>` to the `browser` subagent.
- **For visual reference inspection**: Copy reference images to `<Artifact Directory Path>/browser_sandbox/references/<filename>` and pass the full path.
- **For downloaded outputs**: Record the downloaded file paths returned by the `browser` subagent and copy/organize final assets into the user's workspace output directory when requested.
