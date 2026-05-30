# Marginalia

A Cowork plugin for reading great public-domain texts the way they deserve to be read: slowly, daily, in conversation.

Marginalia delivers one short passage per session (3-5 minutes by default), with a context paragraph calibrated to what you already know and two Socratic questions that earn their depth. When you answer, the conversation goes in your `student_notes.md` — and the next day's lesson reaches back to it. After a few weeks, the system knows the shape of how you read, names patterns in your thinking, and pre-empts your blind spots before you reach them.

The product isn't the verses. It's the *marginalia* — the running notes that accumulate around your reading.

## What you get

- **Six skills** that handle the daily ritual end-to-end:
  - `start-reading` — pick a text, set up your profile, choose cadence
  - `deliver-lesson` — the daily passage + context + questions
  - `discuss-answer` — when you reply, it evaluates and logs to your notes
  - `add-text` — bring any text into your library (curated repo, URL, file, or paste)
  - `split-text` — turn raw text into hierarchical study units
  - `export-progress` / `import-progress` — back up and move your reading state
- **Three reading cadences** — short (~3-5 min), medium (~10-15 min), long (~20-30 min) — picked at setup, change anytime
- **A growing curated library** — public-domain texts hand-split into hierarchical units, hosted at [github.com/cmcelha/marginalia-library](https://github.com/cmcelha/marginalia-library)
- **Plain-text portability** — your entire reading state is markdown + json in a folder you own. No proprietary format. Hand the folder to a friend and they have your reading.

## What's bundled

The plugin ships with **The Art of War** (Sun Tzu, Lionel Giles 1910 translation, public domain) pre-split into 59 short-tier units. Everything else loads on demand via `add-text`.

## Currently in the curated library

- The Art of War — Sun Tzu (Giles 1910) — *bundled with the plugin*

**On the roadmap** (use `add-text` with a URL or file in the meantime):

- Meditations — Marcus Aurelius (Long)
- Tao Te Ching — Lao Tzu (Legge)
- Analects — Confucius (Legge)
- Enchiridion — Epictetus (Higginson)
- Nicomachean Ethics — Aristotle (Ross)
- Bhagavad Gita — Edwin Arnold's *Song Celestial* (1885)
- The Prince — Machiavelli (Marriott)
- Thus Spoke Zarathustra — Nietzsche (Common)
- World English Bible (per book, with a sub-index)
- Quran (Sale or Pickthall)
- Torah (JPS 1917)
- Principal Upanishads — Max Müller translations

Once a text lands in the library: `add-text "Meditations"`.

To add anything else (a PDF you have, a Wittgenstein text, your company's strategy memo, the Federalist Papers): `add-text` followed by the URL, file path, or pasted content.

## Install

From the marketplace:

```
/plugin marketplace add cmcelha/marginalia
/plugin install marginalia@marginalia
```

Then start your first reading:

```
start-reading
```

## How the three benefits compound

The product earns its keep on three timescales:

- **Within a session** — live Socratic back-and-forth. The lesson is short, but the conversation around it can go anywhere.
- **Across sessions** — your `student_notes.md` accumulates. The lesson tomorrow knows what you said today, last week, and three weeks ago. After ~5 sessions, you'll notice the system naming patterns in your reading you didn't realize you had.
- **Across humans** — because two readers using Marginalia on the same text get *different* questions tuned to their profiles, when they meet to discuss they have specific shared reference points rather than generalities. This isn't a feature we built — it's what happens when the system makes each reader's thinking legible. Read with a friend if you want.

## Privacy and portability

Your reading folder is plain markdown + json on your own machine. Nothing leaves your computer except for the small library-index fetches when you use `add-text`. If you want to back up or move to another device, `export-progress` produces a zip; `import-progress` restores it. If you want to read your own lesson log without Marginalia, just open the markdown file in any editor.

## Contributing

The curated library lives in a [separate repo](https://github.com/cmcelha/marginalia-library). To contribute a new split — particularly for niche public-domain texts not yet covered — see that repo's `CONTRIBUTING.md`.

Plugin issues, feature requests, and skill improvements go in this repo's issue tracker.

## License

Plugin code: MIT.

Bundled texts: each text's translation is public domain. See the YAML frontmatter in each `library/<slug>/<slug>.md` file for translator and year information.
