---
name: start-reading
description: Onboard a new Marginalia reader. Pick a text from the bundled library or by name (fetched from the library repo), interview the user to build their reading profile, choose cadence and schedule, create their reading folder with progress.json + student_notes.md + a copy of the text, and optionally register a scheduled task for daily lesson delivery. Use when the user says "start a new reading", "begin a Marginalia text", "set up Marginalia", "I want to read [text]", "let's start the Art of War", or first opens the plugin.
---

# start-reading

The onboarding skill. Sets the user up with their first (or next) Marginalia reading. Conversational where it matters, defaults where it doesn't.

## When this skill runs

- User asks to start a new reading, begin a text, or set up Marginalia
- User opens the plugin for the first time
- User wants to start an additional text after completing or pausing another

## What this skill produces

A new reading folder on disk containing:

- `progress.json` — position, cadence, paths, completion log
- `student_notes.md` — profile + (empty) lesson log
- `text.md` — copy of the selected text's hierarchical split file
- `lessons/` — empty directory ready to receive daily lessons

And updates the user's marginalia config:

- `~/.marginalia/active-reading-path` — pointer file containing the absolute path to this reading folder
- Optionally registers a scheduled task for daily lesson delivery

## Procedure

### Step 1 — Pick the text

Ask the user what they want to read. Three input shapes:

- **Name a text in the library.** Check the bundled `library/` folder first (currently: Art of War). Then check the repo library by reading `https://raw.githubusercontent.com/cmcelha/marginalia-library/main/library.json` (one bash curl). Match against `title` and known aliases. If found, use it.
- **Provide a URL or file.** Invoke the `split-text` skill on it. The output lands in the user's reading folder as `text.md`.
- **Don't know yet.** Show the available library (bundled + repo) as a short list with one-line descriptions. Let them pick.

If a named text exists in the library but isn't yet bundled or downloaded, fetch the split markdown from the library repo via raw URL and save it as `text.md` in the new reading folder.

### Step 2 — Choose the reading folder location

Default: `~/Marginalia/<text-slug>/` where `<text-slug>` is the kebab-cased title.

Confirm with the user. They can pick a different location if they have a preferred folder for reading material (e.g. an existing `~/Documents/Reading/` or a cloud-sync folder like Dropbox) — let them keep their habit.

Create the folder and a `lessons/` subdirectory inside it. Copy or move the text file there as `text.md`.

### Step 3 — Build the student profile

This is the **calibration interview** — the single biggest determinant of how good the lessons will feel. Take it seriously, but keep it short. Aim for 4-6 questions, not 20.

Ask (with examples to help the user answer; let them skip any):

1. **First time with this text, or a re-read?** Re-readers get sharper questions and less basic framing.
2. **What references can I draw on?** Philosophical schools, historical periods, prior thinkers, fields of work — anything you'd want the context paragraphs to lean on. (For Sun Tzu: Daoism / Warring States history / military theory / contemporary strategy work. For Meditations: Stoicism / Roman history / Buddhist parallels / modern psychology.)
3. **Any context you specifically want me to skip?** ("Don't keep telling me what the Warring States period was — I know.")
4. **Question modes you prefer?** Offer the three (comprehension / prediction / application) and let them pick which they want most of. Default is a mix of all three.
5. **How hard should I push?** Three levels — *gentle* (warm Socratic, mostly affirming), *moderate* (push back when you have something to push on), *sharp* (treat me like a re-reader; question my framings, name patterns, don't soften).
6. **Anything else worth knowing about how you read?** Free-form, optional.

If the user wants to skip the interview entirely, default the profile to: first-time reader, no listed references, all three question modes, moderate pushback. They can update later via `manage-reading`.

### Step 4 — Choose the cadence

Three options. Ask, no default — the choice meaningfully changes the experience.

- **Short** — one short unit per session, ~3-5 minutes. The Cassie-and-Tim mode. Best for daily ritual reading over lunch or coffee.
- **Medium** — one chapter-sized grouping per session, ~10-15 minutes. Best for evening reading or a more substantial weekly cadence.
- **Long** — one chapter (or larger) per session, ~20-30 minutes. Best for weekend deep reads or readers who want fewer, denser sessions.

### Step 5 — Choose the reading mode

How does the reader want to do the daily ritual? Ask ONE question with a one-line explanation of each. Record the answer as `delivery_mode` in `progress.json` (Step 7).

- **Desktop** — "You'll read and answer here in Cowork on this computer." No phone, no pairing. This is the default; if the reader is unsure, pick it.
- **Dispatch** — "You'll do the daily reading from your phone via Cowork Dispatch; this computer does the work in the background." Works on iPhone **and Android** (pair by scanning a QR with the Claude mobile app). Requires a Pro/Max Cowork plan and that this desktop is on/awake when the lesson fires.

Do not walk the reader through Dispatch pairing inline. If they pick Dispatch and aren't paired yet, point them at `MOBILE_USAGE.md` and continue — pairing can happen after onboarding.

Mode is not permanent: tell the reader they can switch Desktop↔Dispatch anytime with `manage-reading`, with no loss of position or notes.

### Step 6 — Choose the schedule

Two options:

- **Scheduled** — register a scheduled task that fires `deliver-lesson` at a chosen time. Ask for day-of-week pattern (weekdays / daily / specific days) and time. Use `mcp__scheduled-tasks__create_scheduled_task` with an appropriate cron expression.
- **On demand** — no scheduled task. User asks for the next lesson when they want it.

If the user picks scheduled, confirm the cron and create the task. The task's prompt should be: *"Run the deliver-lesson skill for the active Marginalia reading."*

**Interaction with mode:** Dispatch mode needs a morning push to be useful, so when `delivery_mode` is `dispatch`, default to **scheduled** and only fall back to on-demand if the reader explicitly says they'll pull each lesson manually from their phone. Desktop mode is free to pick either.

### Step 7 — Write the reading folder files

**`progress.json`:**

```json
{
  "marginalia_version": 1,
  "text_title": "Art of War",
  "text_slug": "art-of-war",
  "cadence": "short",
  "delivery_mode": "desktop",
  "schedule": "weekdays at noon",
  "current_unit_index": 1,
  "total_short_units": 59,
  "started_date": null,
  "last_lesson_date": null,
  "completed_units": [],
  "notes": ""
}
```

`delivery_mode` is `"desktop"` or `"dispatch"` from Step 5. `deliver-lesson` and `discuss-answer` read it to choose their formatting budget; `manage-reading` rewrites it on a switch. Folders written before this field existed are treated as `"desktop"`.

`started_date` and `last_lesson_date` stay null until the first lesson actually delivers.

**`student_notes.md`** — render the profile from step 3 into the standard format:

```markdown
## Student profile — {user's name or "the reader"}

Running record of answers and where the thinking went on each unit. Future lessons use this to call back to prior reasoning.

### Background

{re-read or first time, plus references and skip-list from the interview}

### Question style preferences

{the modes the user picked}

### Calibration

- Difficulty: {gentle / moderate / sharp}
- {Anything else from step 3 free-form}

---

## Lesson log

*(populated as lessons are delivered and answered)*
```

**`~/.marginalia/active-reading-path`** — a text file containing the absolute path to the new reading folder. If the file already exists (user is starting an additional reading), ask before overwriting: "You have an active reading of {previous text}. Switch to this new one, or keep the old one active?" If they keep both, the new one is created but not activated — they can switch later.

### Step 8 — Welcome message

A short, warm confirmation to the user:

- The text they're starting, the cadence, the mode, and the schedule
- When the first lesson will arrive (next scheduled fire, or "say 'today's lesson' whenever you're ready" for on-demand)
- A one-line note about what to do when they want to answer ("just reply in chat — I'll evaluate, take notes, and pick up the thread in future lessons")
- **Mode-specific line:**
  - Desktop mode — "You can move this to your phone anytime — say 'switch to Dispatch' and I'll set it up."
  - Dispatch mode — "Open the Claude app on your phone and scan the Dispatch QR to read on the go; see `MOBILE_USAGE.md` if you haven't paired yet."
- One sentence on how to change anything later (e.g. "say 'change my profile', 'switch cadence', or 'switch mode' anytime — that's `manage-reading`")

Do NOT lecture about the design philosophy or features. The first impression should be calm, not breathless.

## What not to do

- **Don't make the profile interview feel like a form.** Conversational tone, one question at a time if the user prefers, or all together if they want to power through.
- **Don't overwrite an existing reading folder.** If `~/Marginalia/<slug>/` already exists, ask whether to resume, archive, or pick a different slug.
- **Don't generate a lesson during onboarding.** The first lesson fires from `deliver-lesson` on its scheduled trigger or on the user's first request.
- **Don't ask about every possible preference.** The defaults are good; the profile is editable. Keep the interview to 4-6 questions max.

See `references/profile-examples.md` for two worked examples of completed student profiles (Cassie's warm-Socratic profile and Tim's sharp-philosophical-rereader profile).
