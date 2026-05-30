---
name: export-progress
description: Package the user's active Marginalia reading folder (progress.json, student_notes.md, text.md, lessons/) into a single zip archive for backup or cross-device move. Use when the user says "export my reading", "back up my Marginalia progress", "I'm switching computers", "save my notes", or wants to take their reading state with them.
---

# export-progress

Bundle the user's active reading into a portable archive.

## When this skill runs

- User wants to back up their reading state
- User is moving to a different computer and wants to bring their progress
- User wants to share their reading-in-progress with a friend (the zip is plain markdown + json; the friend can open and read it without Marginalia)

## Procedure

1. **Resolve the active reading folder** using the standard rules: explicit path argument → `~/.marginalia/active-reading-path` → cwd. If no active reading, surface a short message and stop.

2. **Decide the output location.** Default: the user's downloads folder (`~/Downloads/`). Confirm with the user — they may want to put it in their cloud-sync folder (Dropbox, iCloud, OneDrive) so the round-trip happens automatically.

3. **Generate the archive name.** Format: `marginalia-{text-slug}-{YYYY-MM-DD}.zip` — e.g. `marginalia-art-of-war-2026-05-30.zip`. The date makes multiple exports of the same reading distinguishable.

4. **Create the zip** using bash:

   ```bash
   cd "$(dirname '<reading-folder-absolute-path>')" && \
   zip -r "<output-path>" "$(basename '<reading-folder-absolute-path>')" \
     -x "*.DS_Store" -x ".git/*"
   ```

   The archive contains the reading folder as its top-level directory, so `import-progress` knows where to put it back.

5. **Confirm to the user** with the absolute path to the zip and a one-line note that they can drop the zip on another machine and run `import-progress` to restore it. Also note that the zip is plain markdown + json — they can open it and read their lesson log without Marginalia if they want.

## What not to do

- **Don't include the user library (`~/.marginalia/library/`)** in the export. That's separate from a specific reading — it's the pool of available texts, not the active reading state. If they want to share the library, that's a separate `library-export` action (not implemented in v1).
- **Don't delete the original.** Export is copy + zip, not move.
- **Don't include OS junk** (`.DS_Store`, `Thumbs.db`, `.git/`) in the archive.
