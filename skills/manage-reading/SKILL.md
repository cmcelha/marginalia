---
name: manage-reading
description: Change an existing Marginalia reading without re-onboarding. Use when the user wants to switch delivery mode (Desktop ↔ Dispatch), pause or resume daily delivery, reschedule or change the time/days, change cadence (short/medium/long), switch which reading is active, or adopt an existing reading folder (e.g. one imported from another machine or a prior version of the plugin) into the current setup. Trigger phrases: "switch to dispatch", "switch to desktop", "read on my phone", "pause my reading", "resume", "reschedule", "change my cadence", "change the time", "switch readings", "use my old art of war notes", "import my prior place".
---

# manage-reading

The control panel for a reading that already exists. `start-reading` creates a reading; `manage-reading` changes one. It never re-runs the profile interview and never resets position or notes unless the user explicitly asks.

## When this skill runs

- Switch **delivery mode**: Desktop ↔ Dispatch
- **Pause** / **resume** daily delivery
- **Reschedule** — change the days/time, or move between scheduled and on-demand
- Change **cadence** — short / medium / long
- **Switch** which reading is the active one (the user has more than one)
- **Adopt** an existing reading folder that isn't yet wired into this install (imported from another device, or a folder created by an older plugin version that has no `delivery_mode` field)

## Finding the reading

Same resolution rules as `deliver-lesson`:

1. Explicit folder path argument, if given.
2. Pointer file `~/.marginalia/active-reading-path`.
3. Current working directory, if it holds a valid `progress.json`.

For **adopt** (a folder not yet active), the user supplies the path; validate it has `progress.json` + `student_notes.md` + `text.md` before doing anything.

Read `progress.json` and confirm the current state back to the user in one line before changing it ("Right now: Art of War, short cadence, Desktop mode, weekdays at noon"). Then make only the change they asked for.

## Operations

### Switch delivery mode (Desktop ↔ Dispatch)

This is the headline feature of v0.2. A mode switch changes **only the surface** — position, cadence, profile, and `student_notes.md` are never touched.

1. Rewrite `delivery_mode` in `progress.json` to `"desktop"` or `"dispatch"`.
2. Reconcile the schedule:
   - **→ Dispatch:** Dispatch needs a morning push to be useful. If no scheduled task exists, offer to create one (ask days + time) and create it via `mcp__scheduled-tasks__create_scheduled_task` with the prompt *"Run the deliver-lesson skill for the active Marginalia reading."* If a task already exists, keep it.
   - **→ Desktop:** keep the existing scheduled task if the reader still wants scheduled delivery; offer to cancel it if they'd rather pull lessons on demand. Never silently delete a task.
3. If switching **to Dispatch** and the reader hasn't paired a phone, point them to `MOBILE_USAGE.md`: open the Claude mobile app (iPhone or **Android**), scan the Dispatch QR, confirm the desktop shows as paired. Pairing is a Cowork-level action, not a file change — do not attempt it from inside the skill.
4. Confirm in one line where the next lesson will appear ("Next lesson fires weekdays 8am and will arrive in your phone's Dispatch thread").

### Pause / resume

- **Pause:** disable or delete the scheduled task (prefer disable/cancel so resume is clean). Set `progress.json` `"schedule"` to a paused marker (e.g. `"paused"`). Do not change `current_unit_index`. Tell the reader on-demand lessons still work ("say 'today's lesson' anytime").
- **Resume:** recreate/re-enable the scheduled task at the previous or a new time; restore the `"schedule"` string.

### Reschedule

Update the scheduled task's cron (or create one if moving from on-demand to scheduled; cancel it if moving the other way). Mirror the human-readable schedule into `progress.json` `"schedule"`.

### Change cadence

Set `"cadence"` in `progress.json` to `"short"`, `"medium"`, or `"long"`. Explain the change takes effect on the next lesson. Do not retroactively rewrite past lessons or position.

### Switch active reading

Point `~/.marginalia/active-reading-path` at the chosen reading's folder. Confirm the newly active text. The previously active reading is untouched and can be switched back to later.

### Adopt an existing reading folder

For bringing a prior reading into this install — e.g. the user's earlier Art of War folder, or one restored via `import-progress`:

1. Validate the folder has `progress.json` + `student_notes.md` + `text.md`.
2. **Backfill missing fields** without disturbing data: if `delivery_mode` is absent, add it (ask Desktop or Dispatch; default Desktop). Leave `current_unit_index`, `completed_units`, `started_date`, and `student_notes.md` exactly as they are — this is the user's real place and history.
3. Set this folder as active via `~/.marginalia/active-reading-path`.
4. Offer to set up delivery (mode + schedule) using the operations above.

This is the path for "import my prior place and notes, but deliver via Dispatch now": adopt the existing folder, set `delivery_mode: "dispatch"`, create the scheduled task, point the reader at Dispatch pairing.

## What not to do

- **Don't reset position or notes on a mode/schedule change.** Only `current_unit_index` advancement happens in `deliver-lesson`; this skill leaves it alone unless the user explicitly asks to reset.
- **Don't silently delete a scheduled task.** Cancelling delivery is always confirmed with the user.
- **Don't re-run the profile interview.** Changing how the reader reads is `start-reading`'s "change my profile" path or a direct edit to `student_notes.md`, not a reading-management action.
- **Don't attempt Dispatch pairing programmatically.** Pairing is done by the user in the Claude mobile app; this skill only sets `delivery_mode` and points them to `MOBILE_USAGE.md`.
- **Don't change more than the user asked for.** Confirm current state, make the one change, confirm the result.
