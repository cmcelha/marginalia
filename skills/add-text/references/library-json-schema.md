# library.json schema

The curated library is a github repo (`github.com/cmcelha/marginalia-library`) containing pre-split, hand-vetted PD texts. `library.json` at the repo root is the index of what's available.

## Curated repo `library.json` schema

```json
{
  "schema_version": 1,
  "updated_at": "2026-06-15",
  "texts": [
    {
      "slug": "meditations",
      "title": "Meditations",
      "aliases": ["Marcus Aurelius", "The Meditations"],
      "author": "Marcus Aurelius",
      "translator": "George Long",
      "translation_year": 1862,
      "license": "public domain",
      "source_url": "https://www.gutenberg.org/files/2680/2680-0.txt",
      "source_path": "meditations/meditations.md",
      "description": "Stoic reflections written as a private notebook. Twelve books of aphoristic passages on self-discipline, mortality, and the nature of the rational mind.",
      "tradition": "Stoic / Hellenistic philosophy",
      "short_units": 130,
      "medium_units": 24,
      "long_units": 12,
      "recommended_cadence": "short",
      "estimated_duration_days": {"short": 130, "medium": 24, "long": 12},
      "split_status": "curated",
      "added_date": "2026-06-15"
    }
  ]
}
```

### Field definitions

| Field | Type | Required | Notes |
|---|---|---|---|
| `slug` | string | yes | kebab-case, unique, becomes folder name |
| `title` | string | yes | Display title |
| `aliases` | string[] | no | Alternative names users might use to refer to the text |
| `author` | string | yes | Original author |
| `translator` | string | yes (for translated works) | Critical for PD verification |
| `translation_year` | int | yes (for translated works) | Critical for PD verification |
| `license` | string | yes | "public domain" or a specific PD license name (e.g. WEB Bible's license) |
| `source_url` | string | yes | The Gutenberg or sacred-texts.com URL the split was derived from |
| `source_path` | string | yes | Path within the repo to the split markdown file |
| `description` | string | yes | 1-2 sentence description shown in the library picker |
| `tradition` | string | no | "Stoic", "Daoist", "Christian theology", etc. — used for grouping in the picker |
| `short_units` | int | yes | Total short-tier unit count |
| `medium_units` | int | yes | Total medium-tier grouping count |
| `long_units` | int | yes | Total long-tier grouping count |
| `recommended_cadence` | string | no | Hint for `start-reading`: "short", "medium", or "long" |
| `estimated_duration_days` | object | no | Mapping cadence → estimated days to complete |
| `split_status` | string | yes | "curated" (hand-vetted) or "auto" (split by AI, pending review) |
| `added_date` | string | yes | ISO date the text was added to the curated library |

## Local user index schema (`~/.marginalia/library/index.json`)

A smaller subset, tracking what the user has personally added:

```json
{
  "schema_version": 1,
  "added_texts": [
    {
      "slug": "meditations",
      "title": "Meditations",
      "author": "Marcus Aurelius",
      "translator": "George Long",
      "added_date": "2026-05-30",
      "source": "curated-library",
      "short_units": 130
    },
    {
      "slug": "company-strategy-2026",
      "title": "Company Strategy 2026",
      "author": "Internal",
      "translator": null,
      "added_date": "2026-06-02",
      "source": "user-provided",
      "short_units": 18
    }
  ]
}
```

`source` is one of:
- `bundled` — shipped with the plugin
- `curated-library` — fetched from the github library repo
- `user-provided` — split from a user URL/file/paste via `split-text`

## Repo layout

```
github.com/cmcelha/marginalia-library/
├── library.json              # the index above
├── README.md
├── CONTRIBUTING.md           # how to submit new splits
├── meditations/
│   └── meditations.md        # hierarchical split with frontmatter groupings
├── tao-te-ching/
│   └── tao-te-ching.md
├── analects/
│   └── analects.md
├── enchiridion/
│   └── enchiridion.md
├── nicomachean-ethics/
│   └── nicomachean-ethics.md
├── bhagavad-gita/
│   └── bhagavad-gita.md
├── the-prince/
│   └── the-prince.md
├── thus-spoke-zarathustra/
│   └── thus-spoke-zarathustra.md
├── bible-web/
│   ├── library.json          # sub-index — Bible splits per book
│   └── books/
│       ├── genesis.md
│       ├── matthew.md
│       └── ...
├── quran/
│   └── quran.md
├── torah-jps/
│   └── torah-jps.md
└── principal-upanishads/
    └── principal-upanishads.md
```

The Bible is a special case — too large for one library entry. Each book gets its own split file with a sub-index. `add-text "Genesis"` and `add-text "Bible"` both resolve via this structure.
