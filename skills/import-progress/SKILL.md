---
name: import-progress
description: Restore a Marginalia reading folder from a previously exported zip archive. Use when the user says "import my reading", "restore my Marginalia progress", "I exported from my other computer", or attaches/points at a marginalia-*.zip file. Handles conflicts (existing reading state) gracefully by asking before overwriting.
---

# import-progress

Restore an exported reading into the right location and activate it.

## When this skill runs

- User has a `marginalia-*.zip` file (from `export-progress`) and wants to restore it
- User is setting up Marginalia on a new device and bringing their old reading over
- User points at a zip file with their reading state

## Procedure

1. **Locate the zip file.** Three possible sources:
   - User attached it to the conversation — use the upload path
   - User gave a file path — use it
   - User said "the export I made" without specifics — check `~/Downloads/` for the most recent `marginalia-*.zip`

   If none resolves, ask the user to provide the file.

2. **Inspect the zip** to find the reading folder name. The archive should contain one top-level directory matching the original reading folder (e.g. `Art of War/`):

   ```bash
   unzip -l "<zip-path>" | head -20
   ```

   Validate that the top-level directory contains `progress.json`, `student_notes.md`, `text.md`, and `lessons/`. If structure looks wrong, surface a clear error.

3. **Decide the destination.** Default: `~/Marginalia/<reading-folder-name>/`. Confirm with the user — they may want it somewhere else if they have a specific reading-material folder convention.

4. **Handle conflicts.** If the destination folder already exists:
   - If it contains a `progress.json` for the same text and matches the export's data, treat as a "no-op" — the user may have re-imported their own export. Confirm and stop.
   - If it contains a *different* reading (same slug but different content), ask the user: replace, rename the import (`<slug>-imported`), or cancel.
   - If it contains a partial folder (incomplete state), ask whether to back it up first and then overwrite.

5. **Unzip into the destination.**

   ```bash
   unzip "<zip-path>" -d "<destination-parent>"
   ```

6. **Update the active-reading pointer.** Ask the user if this restored reading should become the active one. If yes, write the destination path to `~/.marginalia/active-reading-path`. If no, leave the previous active reading in place — the user can switch later.

7. **Confirm to the user** with:
   - The text restored
   - Current position ("Day 5 of 59" — read from progress.json)
   - Whether it was set as active
   - A one-line note: "Reply 'today's lesson' (or wait for the schedule) to continue where you left off."

## What not to do

- **Don't silently overwrite an existing reading folder.** Always confirm.
- **Don't import zips with the wrong structure.** If the archive doesn't look like a Marginalia export, refuse cleanly rather than risk dumping arbitrary files into the user's home.
- **Don't recreate the scheduled task automatically.** Schedules are per-device. If the user wants daily delivery on the new machine, they re-run the schedule-setup portion of `start-reading` (or we add a `manage-reading` skill later).
- **Don't delete the source zip.** Leave it; the user might want to keep the archive.
