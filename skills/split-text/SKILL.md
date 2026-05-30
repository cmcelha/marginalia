---
name: split-text
description: Split a public-domain text into hierarchical study units (short / medium / long tiers) for use in the Marginalia plugin's daily-reading workflow. Use when the user asks to "split a text", "prepare a text for Marginalia", "add a book to the library", "make study units from this text", or hands over a raw text file or URL to be turned into a study sequence. Also use to "regroup" an already-split file by adding chapter wrappers and medium/long groupings without re-splitting the existing short units.
---

# split-text

Turn a raw public-domain text into a hierarchical Marginalia study file with three lesson cadences baked in: short (~3-5 min), medium (~10-15 min), long (~20-30 min).

## When this skill runs

- User asks to split a text, prepare a text for Marginalia, or add a book to the library
- User pastes raw text, points at a URL (Project Gutenberg, sacred-texts.com, archive.org), or names a file path
- User asks to "regroup" an existing split file (preserve unit boundaries, add the chapter wrappers and groupings table)

## What to produce

A single markdown file with three things:

1. **YAML frontmatter** containing the text's metadata + the groupings table
2. **Chapter headings** (`# Chapter N — Title`) wrapping the body
3. **Unit headings** (`## Unit N — <Chapter>, <verse-or-section range>: <Theme>`) wrapping each short-tier passage

The short-tier units are the only physical splits in the file. Medium and long tiers exist as *groupings of short units*, declared in the frontmatter — no duplicated text.

### Frontmatter schema

```yaml
---
title: Art of War
author: Sun Tzu
translator: Lionel Giles
translation_year: 1910
source_url: https://www.gutenberg.org/files/132/132-0.txt
license: public domain
short_units: 59
medium_units: 13
long_units: 13
groupings:
  medium:
    - {units: [1, 2, 3, 4], title: "Laying Plans"}
    - {units: [5, 6, 7], title: "Waging War"}
    - {units: [8, 9, 10], title: "Attack by Stratagem"}
    # ...
  long: by-chapter   # shorthand when long tier == chapter boundaries
---
```

`groupings.long` may be either the shorthand `by-chapter` (use chapter headings as long-tier boundaries) or an explicit list with the same shape as `medium`. Use the shorthand whenever it applies — most texts will use it.

### Body format

```markdown
# Chapter 1 — Laying Plans

## Unit 1 — Laying Plans, verses 1-3: War as a vital art

> 1. Sun Tzŭ said: The art of war is of vital importance to the State.
>
> 2. It is a matter of life and death...
>
> 3. The art of war, then, is governed by five constant factors...

## Unit 2 — Laying Plans, verses 4-11: The five factors

> 4. These are: (1) The Moral Law; (2) Heaven; (3) Earth...
> ...

# Chapter 2 — Waging War

## Unit 5 — Waging War, verses 1-7: The cost of war
> ...
```

Quote the passage as a blockquote, preserving verse numbers from the source. Keep one blank line between verses inside the blockquote.

## How to decide unit boundaries

### Short-tier units

Each short unit should be **3-5 minutes of reading** — roughly 200-500 words of source text.

Choose boundaries that:

- **Respect natural breaks first.** Most public-domain texts have verse numbers, section breaks, or paragraph structure. Cut on those, not inside them.
- **Hold one idea per unit.** A unit that contains "the question is X" should also contain Sun Tzŭ's answer to X, not stop mid-argument.
- **Pair setup with payoff where short enough.** "Here are the five factors" + the enumeration of the five factors belong together if they fit in the word budget. If not, split between "what are the factors" and "what each factor means."
- **Avoid orphan verses.** Don't leave a single verse as its own unit unless it's load-bearing (a definition, a thesis statement). Better to merge.

Aim for the count to fall in **~50-70 units per book** for typical philosophical texts. Very short texts (Enchiridion at ~52 chapters) can be 1 chapter per unit. Very long texts (Bible per book) need their own breakdown — see Special cases below.

### Unit titles

Format: `Unit N — <Chapter or Book Name>, <range>: <One-line theme>`

- Range: `verses X-Y` for verse-numbered texts; `sections X-Y` for section-numbered; `pp. X-Y` only as a last resort.
- Theme: a 3-7 word noun phrase or short clause naming what the unit is *about*, not summarizing it. "The cost of war" not "Sun Tzu argues that war is costly."
- No spoilers in the title. "Why deception works" is fine; "Deception works because both sides use the seven factors" is not — that's the lesson, not the title.

### Medium-tier groupings

Each medium grouping should be **10-15 minutes of reading** — roughly 1500-3000 words. Typically this means 3-5 short units, often aligned with a chapter.

Choose groupings that:

- **Default to chapter boundaries.** A chapter is usually the right grain — it's a coherent argumentative unit by design.
- **Sub-divide long chapters.** If a chapter is more than ~5 short units, split it into 2-3 medium groupings on internal thematic breaks. Name each grouping after its theme, not "Chapter 1 part 1."
- **Combine short chapters where coherent.** Two short chapters that argue the same point can join into one medium grouping.

### Long-tier groupings

Each long grouping should be **20-30 minutes of reading** — roughly 3000-6000 words. For most philosophical texts this equals one chapter; use the `long: by-chapter` shorthand in that case.

Override to explicit groupings when:

- Chapters are too short (group 2-3 chapters into one long-tier passage)
- Chapters are too long (split into 2 long-tier passages — rare; usually the medium grouping does this work)

## Procedure

1. **Acquire the source.**
   - URL: fetch with bash `curl`, strip Gutenberg license headers/footers, keep only the translated text body
   - File path: read it
   - Pasted content: use what was given
   - Existing split file ("regroup" mode): read it, verify the `## Unit N` structure, skip to step 4

2. **Identify the text's metadata.** Title, author, translator (critical — never invent), translation year (critical for PD verification), and license. Confirm public domain status before proceeding. If the translator/year is unclear or in copyright, stop and ask the user.

3. **Identify the structural skeleton.** Find chapter/book boundaries first, then verse/section numbering. Most PD philosophical texts have this clearly marked. If a text has multiple structural systems (e.g. Bible has book/chapter/verse), use the two most useful levels.

4. **Cut the short-tier units.** Move through the text section by section, applying the boundary rules above. Number sequentially across the whole text (not per-chapter).

5. **Compose the groupings table.** Group short units into medium-tier passages. Decide whether long tier is `by-chapter` shorthand or needs explicit groupings.

6. **Write the output file.** Frontmatter, then body with chapter and unit headings, then a blockquote of each unit's text preserving verse numbers.

7. **Save** to `library/<slug>/<slug>.md` inside the Marginalia plugin folder, where `<slug>` is the lowercased, hyphenated text title (`art-of-war`, `meditations`, `principal-upanishads`). Create the directory if needed.

8. **Report back** with: total short units, total medium groupings, total long groupings, and any judgment calls the user should review.

## Regroup mode (for already-split files)

When the user provides a file that already has `## Unit N` headings (e.g. Cassie's existing Art of War 59-unit split):

- **Do not re-split.** Preserve every existing unit boundary and unit title exactly.
- Add chapter headings (`# Chapter N — Title`) at the appropriate boundaries between units.
- Add or update the YAML frontmatter, including the groupings table.
- Verify the unit count matches `short_units` in frontmatter.

## Special cases

- **Very large religious texts (Bible, Quran, Vedic texts):** these don't fit one-pass treatment. For the Bible, treat each book as its own splittable text — produce one library entry per book or per natural cluster (e.g. "Gospels", "Pauline epistles"). For the Quran, surah is the right unit; treat the whole text as one library entry with ~114 short units (one surah each), grouped into ~30 medium passages (juz') and ~7 long passages. For the Principal Upanishads, treat the 10-13 principal Upanishads as a single library entry, with each Upanishad as 1-3 short units depending on length.
- **Texts with very short component pieces (Tao Te Ching's 81 chapters):** one chapter per short unit, ~4-8 chapters per medium grouping by theme, ~chapter-cluster per long grouping. The whole text yields ~81 short, ~12-15 medium, ~5-7 long.
- **Already-aphoristic texts (Meditations, Enchiridion):** group aphorisms into thematic short units rather than one-per-unit.

## What not to do

- **Do not invent metadata.** If the translator name isn't in the source, search for it; don't guess.
- **Do not modernize or paraphrase the source text.** Reproduce it verbatim, including archaic spellings and verse numbering.
- **Do not write context paragraphs or questions.** That's the daily lesson skill's job. This skill produces only the source-text split.
- **Do not include the Gutenberg license boilerplate** in the output file. The frontmatter `license: public domain` line and `source_url` are sufficient attribution.
- **Do not split copyrighted translations.** If the user provides a translation that's still in copyright (e.g. Walter Kaufmann's Nietzsche, Victor Harris's Five Rings, post-1929 modern translations), stop and ask them to provide a PD translation instead. See `references/public-domain-check.md` for guidance on the most-cited PD translations.
