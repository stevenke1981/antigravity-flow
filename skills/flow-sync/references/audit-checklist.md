# Google Flow Live UI Audit Checklist

- **Last Calibrated**: `2026-11-20`
- **Latest Changelog Observed**: `2026-11-19 — Faster Flow Agent and smarter search in your projects` (`https://flow.google.com/changelogs`)
- **Active Deprecations to Watch (Retiring 2026-12-05)**:
  - `Omni 1.1 Flash` (`Leaving Dec 5`) — prune after 2026-12-05
  - `Veo 3.1 - Fast` (`Leaving Dec 5`) — prune after 2026-12-05
  - `Veo 3.1 - Quality` (`Leaving Dec 5`) — prune after 2026-12-05

## 7 Canonical Audit Surfaces (Strictly Read-Only, Zero Credits)

1. **Dashboard (`https://flow.google.com/`)**
   - Verify canonical URL (no forced `accounts.google.com` hop when signed in).
   - Verify signed-in signals: `Account details` button (avatar + tier badge), `+ New project`, `Sort projects`, `/project/<id>` cards, `batchexecute` HTTP `200`.
   - Record promotional banners and top-level nav links (`Flow Music`, `Flow TV`, etc.).

2. **Changelogs (`https://flow.google.com/changelogs`)**
   - Record all entries newer than `Last Calibrated`.
   - Flag new models, UI redesigns, credit pricing changes, and deprecation announcements.

3. **Project Workspace (`/project/<id>`)** — use an existing project; never create projects, Characters, or scenes during a sync
   - Top bar: `Home`, editable title, `More options for the project`, `Search`, `Filtering and sorting options`, `+` (`Add media menu`: `Upload`, `New collection`, `Create character`, `New scene`), `Settings`, `More options` (`Download project`, `View all changelogs`, etc.), `Account details` (credits & watermark toggle).
   - Left sidebar: `All media`, `Videos`, `Characters`, `Scenes`, `Library` (`+ Create collection`), `Tools`, `Create tool`, `Trash`.
   - Composer: `Flow Agent` toggle (`aria-pressed`) vs. Manual Mode.

4. **Generation Settings (`Settings trigger` in Manual Mode)**
   - **`Image` mode**: Aspect ratios (`16:9`, `4:3`, `1:1`, `3:4`, `9:16`), Models (`Nano Banana Pro`, `Nano Banana 2 Lite`, `Nano Banana 2.1`), `Thinking level` (`Low`/`Medium`/`High`; record which models expose it), output counts (`x1`–`x4`), live credit readout.
   - **`Video` mode**: `Video type` (`Frames`, `Ingredients`), Aspect ratios (`16:9`, `9:16`), Models (`Omni 1.2 Flash`, `Veo 3.1 - Lite`, `Veo 3.1 - Lite [Lower Priority]`, plus check deprecation status of `Omni 1.1 Flash`, `Veo 3.1 - Fast`, `Veo 3.1 - Quality`), resolutions (`360p`/`720p`), durations (`4s`–`10s` / slider), output counts (`x1`–`x4`), live credit readout.
   - Check if any dedicated Camera Control or Audio panel has been introduced.

5. **Characters & Ingredients (`/project/<id>/character` & `+` / `@` Picker)**
   - `@` picker filter tabs: `All`, `Images`, `Videos`, `Voices`, `Characters`, `Avatars`, `Uploads`, plus `Upload media` and `Add to prompt`.
   - Character creator (`/project/<id>/character`): 6 archetypes, model selector, `Upload`, `Add from project`, `Done`.

6. **Scenes, Clip Editor (`/project/<id>/edit/<clipId>`) & Download Submenu**
   - Card `More options`: `Favorite`, `Reuse prompt`, `Add to scene`, `Add to prompt`, `Download`, `Copy`, `Rename`, `Share`, `Publish to YouTube`, `Set project cover`, `Flag output`, `Move to trash`.
   - `Download` submenu: `270p Animated GIF`, `720p Original size`, `1080p Upscaled`, `4K Upscaled · 50 credits`.
   - Editor controls: `Save frame`, timeline `Add clip` / `Extend`, bottom `"Describe how to edit this video..."` models, `Download media`, `Done editing scene`.

7. **Tools (`/project/<id>/tools`) & Create tool (`/project/<id>/create-tool`)**
   - `Explore tools` tabs: `My Tools`, `Community`, `Templates` (`Image`, `Video`, `Prompting`, `Experimental`).
   - `Create tool` starter presets (`Voice over`, `Time stretcher`, `Style morph`) and credit cost notice (`"Creating tools does not currently cost credits."`).
