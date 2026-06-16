---
name: discuss-answer
description: When a Marginalia reader replies in chat with their answers to today's lesson questions, evaluate the response with the calibrated pushback level from their profile, append a structured entry to student_notes.md, and surface observations back to the reader. Use when the user is answering Marginalia questions, responding to today's reading, or saying things like "here's my answer", "for question 1...", or simply replying after a lesson was delivered. Also use when the user wants to revisit a prior unit's answer or revise their notes.
---

# discuss-answer

The conversation skill. Where the longitudinal pedagogy lives — this is what turns a daily-verse newsletter into Marginalia.

## When this skill runs

- The user replies in chat after a lesson was delivered (the lesson file exists, the user's message engages with the questions)
- The user explicitly says they're answering ("here's my answer", "Q1:", "for question 1...")
- The user wants to revisit and revise an answer to a prior unit

## What this skill produces

Two things:

1. **A new lesson-log entry** appended to `student_notes.md` — quoted answers, observations on reasoning, and threads for future units
2. **A reply to the user** — calibrated pushback per their profile, callbacks where they fit, and one or two specific observations they can chew on

## Procedure

### Step 1 — Resolve the reading folder and load context

Use the same resolution rules as `deliver-lesson`: explicit path argument → `~/.marginalia/active-reading-path` → cwd. If no active reading, surface a short message asking which reading they're answering for.

Read:
- `progress.json` — to identify the most recently delivered unit(s), the user's cadence, and `delivery_mode` (`"desktop"` or `"dispatch"`; absent → `"desktop"`). The mode sets the reply-length budget in Step 6.
- `student_notes.md` — the full profile + lesson log (you'll need both calibration and cross-day pattern context)
- `lessons/YYYY-MM-DD.md` for the most recent delivered date — to see the questions and the passage
- `text.md` — if a deep textual point comes up, you may need to look at the actual verses

### Step 2 — Parse the user's answers

Match their reply to the lesson's questions. They may answer both questions, one, neither (with a tangent that's still worth logging), or both plus extra commentary. Be flexible:

- **Numbered answers** ("Q1: ... Q2: ...") — easy
- **Unstructured prose** — figure out which question each part addresses; ask if genuinely unclear
- **Tangents** — log them; they're often the most interesting thinking
- **Pushback on the question itself** — log this explicitly; it's a signal about how the user reads

If the user clearly only addressed one question and the other was substantive, note it but don't badger. Ask once, gently, if they want to come back to it; otherwise move on.

### Step 3 — Evaluate the reasoning

This is the analytical work. For each question, ask yourself:

- **What did they actually claim?** Strip away hedges, restate the core position.
- **Where is it textually well-supported?** Name the verses.
- **Where is it doing original work?** Look for moves the text doesn't make but the user does (Cassie's "othering" on Day 1, Tim's "benevolent deceiver" on Day 4). These are the gold — name them specifically.
- **Where is it strained?** Misreadings, leaps, importing assumptions the text doesn't carry. Distinguish "wrong" from "interesting in a different direction."
- **Does it connect to prior threads?** Read the lesson log. If they extended or contradicted something they said earlier, name it.
- **Does it instantiate a recurring pattern?** This is the cross-day pattern-tracking move (see below).

### Step 4 — Look for cross-day patterns

Read the last 5+ lesson-log entries with this question: *is the user doing something today that they've done before, in a way worth naming?*

Examples of patterns that have appeared in real users:

- **Binary collapse** — when the text invites a third axis (situatedness, comparison-grid, redirection), the user resolves to a binary instead. (Tim, Days 1/2/5.)
- **Anthropocentric framing** — leaving non-human factors out, then later re-marginalizing them as "unknowable." (Tim, Days 1/2.)
- **Pre-emption** — repeatedly intuiting later-chapter material (e.g. Sun Tzu's "subdue without fighting") in early-chapter answers. (Tim, four pre-emptions.)
- **Original thematic move** — the user keeps reaching for a concept the text doesn't supply (Cassie's "othering" returning as a quiet through-line). Call it back when it fits.

The rule for naming a pattern: **third sighting is when you flag it lightly; fourth is when it has to land.** First and second sightings just go in the lesson-log notes. Don't name patterns prematurely — it's brittle when you do it from one data point.

### Step 5 — Calibrate pushback

Read the `Calibration` line in the student profile:

- **Gentle** — affirm specific moves they made well, note one thing they could push further, no contradiction unless the text actively requires it.
- **Moderate** — affirm + push back on at least one strain. Surface misreadings explicitly but warmly. "X is the natural read of this passage, but here's a pressure point: ..."
- **Sharp** — name strains directly, contradict where the text contradicts, surface patterns aggressively when they recur. "Here's the move you keep making; here's what the text is doing that resists it." Don't soften.

Across all three levels: never invent praise. If they didn't make an original move, don't pretend they did. The pedagogy depends on honest signal.

### Step 6 — Compose the reply

Structure (loose — not a template, just a check):

1. **Engage with their actual answer first.** Don't open with meta-commentary about the question. Get into the substance.
2. **Affirm what's specific and right** — a particular move, a noticed structure, a sharp phrasing. Use their words when possible.
3. **Push** at the calibrated level — one or two pressure points, not five. Quality over quantity.
4. **Callback or pattern-name** if today's answer fits a thread or a recurring move.
5. **Foreshadow the next unit's hook** if there's a natural setup (e.g. "Tomorrow's verses do something with this exact tension — hold the thought").

Keep it shorter than the lesson itself. The reply isn't another mini-essay; it's the conversational beat that earns the next day's lesson the right to call back.

**Mode-aware length:** in `desktop` mode the reply can run a few short paragraphs. In `dispatch` mode keep it to **3-5 sentences total** — one specific affirmation, one pressure point, at most one callback. Phone readers won't scroll a wall of text, and the full evaluation still lands in `student_notes.md` either way.

### Step 7 — Append to student_notes.md

Add a new entry under "## Lesson log" using this exact structure:

```markdown
### Unit {N or range} — {YYYY-MM-DD} — {Chapter, vv. X-Y or theme}

**Q1 ({short paraphrase of question}):**
> "{User's verbatim answer, quoted}"

**Q2 ({short paraphrase}):**
> "{User's verbatim answer, quoted}"

**Observations on reasoning:**
- {What they got cleanly}
- {Original moves — name them specifically}
- {Strains, misreadings, the productive wrong turns}
- {Pattern instances if applicable — flag the count: "third instance of X across Days N, M, N+M"}

**Threads to pick up in future units:**
- **{Thread name}** (immediate / Unit N / recurring): {what specifically to do with it later}
```

If the user answered only one question, log only that one. Don't fabricate.

If you noticed something specific about the **session itself** (e.g. "delivered today after a 3-day gap; user noticed the gap"), include a brief "Note on cadence" line at the top of the entry.

If you spotted a small self-correction worth tracking (something you yourself did imperfectly — e.g. "context paragraph leaked the prediction"), include a brief "My small mis-step" item under threads. This kind of self-tracking is what makes the system improve over time.

### Step 8 — Save and confirm

Write the updated `student_notes.md`. Briefly mention to the user that you've logged the notes (one line, e.g. "Logged. Two threads marked for the next few units.") so they know the longitudinal record is alive. Don't recite what you wrote — they can read it if they want.

## What not to do

- **Don't open with "Great answer!"** Generic praise dilutes the signal. Either be specific or skip the praise entirely.
- **Don't manufacture cross-day patterns from one data point.** Wait for the third sighting before flagging; fourth before it has to land.
- **Don't write a lecture in the reply.** This is conversation, not commentary. Keep it shorter than the lesson.
- **Don't summarize the user's answer back to them.** They know what they wrote. Engage with it.
- **Don't modify the lesson file or progress.json.** This skill only writes to `student_notes.md`.
- **Don't ask follow-up questions just to seem engaged.** Ask one only when the answer is genuinely incomplete or the follow-up would unlock real depth.

See `references/log-entry-examples.md` for two real entries showing the format applied to a "first-time reader" and a "sharp re-reader" profile.
