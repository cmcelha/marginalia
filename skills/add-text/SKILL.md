---
name: add-text
description: Add a text to the user's Marginalia library so it can be read with start-reading. Accepts a text name (looked up in the curated library repo), a URL (Project Gutenberg, sacred-texts.com, archive.org, etc.), a local file path, or pasted content. Uses the split-text skill to produce the hierarchical study format when the source isn't already pre-split. Use when the user says "add a text", "add a book to my library", "I want to read [text not in library]", "import this text", or wants to expand their available reading material without starting it immediately.
---

# add-text

The library-management skill. Makes a text available for `start-reading` to pick.

## When this skill runs

- User wants to add a new text to their library without starting it yet
- User mentions a text by name and it isn't bundled or already in their local library
- User has a URL, file, or pasted text they want to turn into a Marginalia reading

## Where texts live

Three locations, checked in this order when resolving a text name:

1. **Plugin bundled library** at the plugin's own `library/` directory — read-only, shipped with the plugin (currently just Art of War)
2. **User library** at `~/.marginalia/library/<slug>/<slug>.md` — texts the user has added via this skill
3. **Curated library repo** at `https://raw.githubusercontent.com/cmcelha/marginalia-library/main/library.json` — fetched on demand, downloaded to user library when chosen

## Procedure

### Step 1 — Resolve the source

Identify what the user has handed you:

- **Bare text name** ("add Meditations" / "add the Gita") — look it up in:
  1. Curated library repo (fetch `library.json`, match against `title` and `aliases`)
  2. If found, get the download URL and proceed to step 3
  3. If not found, tell the user it isn't in the curated library and ask if they have a URL/file or want help finding a PD source
- **URL** — proceed to step 2 with the URL as input to `split-text`
- **Local file path** — proceed to step 2 with the file path as input to `split-text`
- **Pasted text** — proceed to step 2 with the pasted content as input to `split-text`

### Step 2 — For non-library sources: invoke split-text

When the user provided raw input (URL, file, paste), invoke the `split-text` skill. Pass through:
- The source (URL, path, or content)
- Output location: `~/.marginalia/library/<slug>/<slug>.md` where `<slug>` is the kebab-cased title

`split-text` will verify the PD status, fetch and clean if needed, produce the hierarchical split, and save the file. If `split-text` refuses (e.g., copyrighted translation), surface its refusal message and ask the user for an alternative source.

### Step 3 — For curated-library texts: fetch from the repo

When the text is in `library.json`, get the `source_path` field (path in the github repo) and fetch the pre-split markdown:

```bash
curl -L -o ~/.marginalia/library/<slug>/<slug>.md \
  https://raw.githubusercontent.com/cmcelha/marginalia-library/main/<source_path>
```

Create the `~/.marginalia/library/<slug>/` directory first. These texts are already split with hierarchical units and frontmatter groupings — no `split-text` invocation needed.

### Step 4 — Register and confirm

Update `~/.marginalia/library/index.json` — a small local index of texts the user has added:

```json
{
  "added_texts": [
    {
      "slug": "meditations",
      "title": "Meditations",
      "author": "Marcus Aurelius",
      "translator": "George Long",
      "added_date": "2026-05-30",
      "source": "curated-library",
      "short_units": 130
    }
  ]
}
```

Confirm to the user with a single line: title, short unit count, and a suggestion to either start it now (`start-reading`) or come back later.

Do NOT start a reading automatically — that's `start-reading`'s job. If the user asked to "add and start" in the same message, invoke `start-reading` after this skill completes.

## What not to do

- **Don't split a text into the active reading's folder.** Texts go to the library; readings are created from texts. Conflating the two breaks the model.
- **Don't fetch the curated library repo on every operation.** Cache `library.json` for the session.
- **Don't bypass `split-text` for non-library sources.** PD verification + format consistency depends on going through it.
- **Don't overwrite an existing user-library entry** without asking. If `<slug>` already exists, confirm whether to replace or use a different slug.

See `references/library-json-schema.md` for the full `library.json` schema used by the curated github repo and the local index.
