---
name: deliver-lesson
description: Deliver today's Marginalia reading lesson — read the user's progress, pick the next passage at the chosen cadence (short / medium / long), generate a context paragraph and two questions calibrated to the user's profile and prior thinking, and save the lesson. Use when the user says "give me today's lesson", "what's today's reading", "next lesson", "deliver the lesson", or when a scheduled task fires the daily reading. Runs deterministically — no clarifying questions to the user before delivery.
---

# deliver-lesson

The daily reading runbook. One short, calm delivery — no fanfare, no clarifying questions.

## Required inputs

The user's **reading folder** must exist and contain:

- `progress.json` — current position, cadence, paths, completion log
- `student_notes.md` — profile + per-unit answer log
- `text.md` — the hierarchical split text (with frontmatter groupings)
- `lessons/` — where today's lesson will land

If any of these is missing, stop and instruct the user to run `start-reading` first.

## Finding the reading folder

In priority order:

1. **Explicit argument** — if invoked with a folder path, use it.
2. **Pointer file** at `~/.marginalia/active-reading-path` — a text file containing the absolute path to the active reading folder. Created and maintained by `start-reading`.
3. **Current working directory** — only if cwd contains a valid `progress.json` with a `marginalia_version` field.

If none of these resolves to a valid reading folder, surface a short message: "No active reading found. Run `start-reading` to begin." Do not generate a lesson.

## Procedure

1. **Resolve the reading folder** per the rules above.

2. **Read `progress.json`.** Required fields:
   - `current_unit_index` — the next short-unit number to deliver
   - `total_short_units` — the text's short-tier count
   - `cadence` — `"short"`, `"medium"`, or `"long"`
   - `started_date` — ISO date
   - `last_lesson_date` — ISO date or null
   - `completed_units` — array of `{units: [...], date: "..."}`
   - `marginalia_version` — schema version, currently `1`

   If `current_unit_index > total_short_units`, the course is complete. Send a short congratulations to the user — name the text, the start date, the days elapsed, and one specific observation from `student_notes.md` about how their reading evolved. Do NOT generate a lesson or modify the tracker.

3. **Read `student_notes.md`** completely. This is the profile + history. You'll use it for calibration in steps 6-7.

4. **Check for skip-day.** Get today's date via bash (`date +%Y-%m-%d`). If `lessons/YYYY-MM-DD.md` already exists, stop — don't double-fire. Report briefly to the user that today's lesson was already delivered, with a link to it.

5. **Read `text.md`** and parse the frontmatter (especially `groupings`). Identify which short units to deliver today based on cadence:
   - **`cadence: short`** → deliver one short unit, the one at `current_unit_index`
   - **`cadence: medium`** → find the medium grouping in the frontmatter whose `units` array contains `current_unit_index`. Deliver all units in that grouping.
   - **`cadence: long`** → if `groupings.long` is `by-chapter`, deliver all units in the same chapter as `current_unit_index`. If `groupings.long` is an explicit array, find the grouping containing `current_unit_index` and deliver all its units.

   Extract the actual unit headings and blockquoted passage text from the body of `text.md` for each unit in scope.

6. **Calibrate the context paragraph** using the student profile in `student_notes.md`:
   - Read the profile sections (Background, Question style preferences, Calibration).
   - The context paragraph is **ONE paragraph, 4-6 sentences**. Never two paragraphs.
   - If the profile lists philosophical/historical references the user knows (e.g. Heidegger, Nietzsche, Warring States history), anchor the context in *one* of those when the passage genuinely invites it. Don't survey several — pick one.
   - If the profile indicates the user is a re-reader or has advanced background, **skip basic framing** (no "ancient China" hand-waving). If they're a first-time reader of this text, give the basic framing once and only once.
   - End with one specific thing to watch for as they read. Don't summarize the conclusion — the questions will reach toward it.
   - **Don't include a "modern parallel" section.** That's not the format.
   - **Don't leak the answer to the prediction questions** in the context paragraph. If a question asks them to predict what comes next, the context shouldn't telegraph the answer.

7. **Generate the two questions** using the profile's `Question style preferences`. Pair two of {comprehension, prediction/exploration, application} unless the passage strongly calls for doubling up. Calibrate difficulty to the profile's `Calibration` line. Avoid pure textual/rhetorical interpretation questions unless requested.

8. **Reach back into prior threads.** Read the most recent few entries in `student_notes.md`'s Lesson log. Look for:
   - **Direct callbacks** — anything the user predicted in a prior unit that this unit answers or contradicts. Reference it specifically: "Two days ago you predicted X; here's how the text answers that."
   - **Cross-day patterns** — if you've named a pattern in the user's thinking (e.g. "binary tic in Q2 answers on Days 1, 2, 5"), and today's passage is a likely trigger for it, prime the context paragraph or the question to surface that pattern.
   - **Open threads** — if the lesson log marked something for a specific future unit number, and that number is today, deliver on it.

   Don't force callbacks if none fits. A clean lesson is better than a strained reference.

9. **Compose the lesson file** at `lessons/YYYY-MM-DD.md` with this exact structure:

   ```markdown
   # Marginalia — Day {N of total} ({YYYY-MM-DD})

   **{Unit title line — for multi-unit lessons, use the grouping's title from the frontmatter or compose one}**

   ---

   ## Today's passage

   > [verses, blockquoted, preserving verse numbers from the source]

   ## Context

   [ONE paragraph, calibrated per step 6]

   ## Questions

   1. [First question]
   2. [Second question]

   ---

   *Answer in chat when you have a few minutes.*
   ```

10. **Update `progress.json`:**
    - Set `current_unit_index` to the next unit AFTER the highest one delivered today (e.g., if today delivered units 5-7, set it to 8).
    - Set `last_lesson_date` to today's date.
    - If `started_date` is null, set it to today's date.
    - Append to `completed_units`: `{units: [...delivered today], date: "YYYY-MM-DD"}`.
    - Do not touch `cadence`, `total_short_units`, `marginalia_version`, or other fields.

11. **Surface to the user** with:
    - Day number ("Day 5 of 59")
    - Unit title or grouping title
    - A direct link to the lesson file: `[Today's lesson](computer://{absolute-path-to-lesson-file})`
    - The passage itself (quoted, inline) so they see it without opening the file
    - The two questions inline

    Tone: steady, unhurried, no fanfare. This is a quiet daily ritual. Do not ask the user anything in the delivery message; just deliver.

## What not to do

- **Don't generate a lesson on a skip-day.** If `lessons/YYYY-MM-DD.md` exists, stop.
- **Don't modify `student_notes.md`** in this skill. That's `discuss-answer`'s job.
- **Don't change `cadence` mid-text** without an explicit user request.
- **Don't ask the user clarifying questions** in the delivery. The skill is designed to fire silently from a scheduled task.
- **Don't reach back to threads that don't fit.** A forced callback is worse than no callback.
- **Don't summarize the passage's conclusion** in the context paragraph. The questions exist to make them reach.

## Course-complete behavior

When `current_unit_index > total_short_units`:

- Read `student_notes.md` end-to-end.
- Compose a short closing message: text name, dates of first and last lesson, total lesson days, and **one specific intellectual move** the user made that recurred across the text. Cite the unit numbers where it appeared.
- Offer to either re-read with a different cadence, start a new text via `add-text`, or export their progress.
- Do not modify `progress.json` or generate another lesson.

See `references/lesson-format-examples.md` for two worked examples of the lesson format at short and medium cadence.
