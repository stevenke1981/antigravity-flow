---
name: antigravity-flow
description: Plan and produce Google Flow videos from a storyboard or selected media, manage Characters and Ingredients, assemble and export Scenes, or operate Flow Tools and audit the Flow UI when requested.
---

# Google Flow

Use the workflow matching the user's request. A storyboard or prompt request can be completed offline. When the user asks for production, continue through the requested stages and verify the resulting artifacts.

- [Storyboard and prompts](references/storyboard.md) for creative briefs, shot lists, camera, sound, and continuity planning.
- [Projects and Ingredients](references/ingredients.md) to select a project, upload media, and create or reuse Characters.
- [Shot generation](references/generation.md) for Flow Agent or the standard composer, single clips, batches, and resuming interrupted work.
- [Scenes and export](references/scenes.md) for ordering, trimming, extending, editing, downloading, and checking transitions.
- [Flow Tools](references/tools.md) for the Tools gallery and custom tool builder.
- [Flow audit](references/audit.md) only for an explicitly requested UI audit or maintenance of this skill.

## Operating Flow in Codex

1. Use an available first-party Flow connector if it supports the action. Otherwise use Codex's supported browser interface in the user's connected session. Read its tool docs and reuse the verified project tab. Start from [Google Flow](https://flow.google.com/) or the user's supplied project link. Identify controls by their current visible labels and state. Use the browser tool's documented file chooser and download workflow. Follow any stop instructions from the browser or service. If authenticated browser access is unavailable, finish the requested offline work that can still be done and report the missing capability.
2. Before submitting, uploading, or editing, verify the signed-in Google account and the project title or URL in Flow. Recheck after account or project changes. Keep the user's requested account, project, model, and settings. Handle sign-in and verification prompts according to host rules. Keep browser credentials and private project data out of logs and shared reports.
3. Check the live model, mode, resolution, duration, aspect ratio, output count, attachments, and credit cost. Follow the user's authorized scope and budget. Price batches and variants, including paid retries, edits, extensions, and exports. If cost or authorization is unresolved, prepare the concrete action and resolve it at submission time. An app's automatic approval setting is a UI preference and does not extend user authorization. See [Flow credits](https://support.google.com/flow/answer/16526234?hl=en).
4. Treat CAPTCHA, access warnings, policy refusals, and exhausted credits as separate states. Honor protection and access boundaries. For a policy refusal, record the message and proceed with independent authorized work. For a generic error, confirm that no result or active job exists before retrying. Skip automatic duplicates and record an ambiguous job for reconciliation.
5. Record verified outcomes. Distinguish planned, submitted, generating, generated, downloaded, and accepted. Link the final file only after locating and checking it. Use existing project conventions for prompts, progress, and assets.
