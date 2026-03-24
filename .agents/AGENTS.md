# Contributor Guide for AI Agents

This is a documentation-only repository for Pendo Data Sync. It teaches data
engineers how to load Pendo product analytics exports into a lakehouse and query
that data to produce metrics that match the Pendo UI. There is no application
code here — the deliverable is accurate, well-structured Markdown.

## Repo structure

```
concepts/
├── 00-pendo/          # Pendo platform overview
├── 01-dataload/       # ETL patterns for loading exports
└── 02-querying/       # Querying loaded data to match the Pendo UI

.data/                 # Sample Avro exports mirroring real export structure
```

Each section has a `README.md` index and individual concept files in kebab-case
(e.g. `export-overview.md`, `finalized-data.md`).

## Writing style

Write for a data engineering audience. Assume the reader knows ETL, Avro, and
data warehouse concepts — do not over-explain them.

* Use second person throughout: `you` and `your`, never "the developer" or "the user"
* Use active voice and imperative mood for instructions
* Open every file with a direct paragraph immediately after the H1 — no preamble
* Do not start sentences with "In this document...", "This file covers...",
  "It's worth noting...", or "Keep in mind that..."

## Formatting conventions

### Headings

* One H1 per file (`#`), matching the topic name exactly
* H2 (`##`) for major sections, H3 (`###`) for subsections — do not go deeper
* No YAML frontmatter

### Emphasis and inline code

* **Bold** key terms on first introduction: `**export manifest**`, `**retroactive export**`
* Use backticks for all literals a reader would reference or type: field names
  (`periodId`), file names (`billofmaterials.json`), table names (`ALLEVENTS`), paths
* Table and schema names in `ALL_CAPS`; field names in `camelCase`

### Lists

Use `*` for unordered lists, not `-`. For definition-style lists, bold the term
and separate it from the definition with an em dash:

```
* **Export manifest** — JSON file that lists recent exports; used by your ETL pipeline to discover new data.
* **Bill of materials** — JSON file that lists all Avro files within a given export.
```

### Tables

Use tables for structured comparisons (export types, cloud destinations, table
mappings). Bold the key identifier in the first column. Keep header names short.

### Code blocks

Use fenced code blocks for directory trees, SQL, and ASCII flow diagrams. Do not
add a language specifier — use bare triple backticks:

````
```
SELECT periodId, COUNT(*) FROM ALLEVENTS GROUP BY 1;
```
````

### Callouts

GitHub supports five alert types. Use them sparingly — one per section at most.
Do not stack multiple alerts consecutively; rewrite the prose instead.

```
> [!NOTE]
> The export manifest only shows a rolling 30-day window of exports.

> [!TIP]
> Filtering anonymous visitors at load time reduces storage costs compared
> to filtering at query time.

> [!IMPORTANT]
> Retroactive exports do not include `allevents.avro`. Only matched event
> files are re-exported.

> [!WARNING]
> Loading without the delete step will produce duplicate rows for any
> `periodId` that has been re-exported.

> [!CAUTION]
> Resetting the export counter will cause your pipeline to reprocess all
> historical exports from the beginning.
```

Pick the right level:

| Type | Use when |
| --- | --- |
| `NOTE` | Supplementary context a reader might miss but won't break anything if they do |
| `TIP` | An optional practice that meaningfully improves the outcome |
| `IMPORTANT` | Information a reader must have to succeed — missing it causes silent failure |
| `WARNING` | An action that will cause errors, duplicates, or data corruption |
| `CAUTION` | An irreversible or destructive action with no easy recovery |

### Horizontal rules

Use `---` as a visual divider in longer concept files when a break helps. Always
place `---` before the navigation footer at the end of a file.

## File and folder conventions

* Concept files: `kebab-case.md` — no spaces, no uppercase
* Section index files: `README.md`
* New concept sections: add a numbered prefix folder matching the pattern (`03-next-topic/`)

## Navigation and cross-links

Use full GitHub URLs for all internal links — not relative paths:

```
[Finalized Data](https://github.com/pendo-io/data-sync-for-developers/blob/dataload/concepts/01-dataload/finalized-data.md)
```

End every concept file (non-README) with a back-navigation line after a `---`:

```
← [Data Load Overview](https://github.com/pendo-io/data-sync-for-developers/blob/dataload/concepts/01-dataload/README.md)
```

When multiple cross-links are useful, add a `**Related:**` line before the back-nav,
separated by ` · `:

```
**Related:** [Export Overview](URL) · [Finalized Data](URL) · [Data Load Strategy](URL)

---

← [Data Load Overview](URL)
```

End every `README.md` with a `## Learn More` section — a `*` list of external
links in the format `* [Link text](URL) — Short description`.

## Domain terminology

Use these terms exactly as written. Do not invent variants.

| Use | Not |
| --- | --- |
| **Data Sync** | DataSync, data sync |
| **export manifest** (`exportmanifest.json`) | manifest file, export index |
| **bill of materials** (`billofmaterials.json`) | BOM, materials file |
| `periodId` | period ID, period_id |
| `matchableId` | matchable ID, matchable_id |
| **retroactive export** | retroactive processing export, retro export |
| **finalized data** | finalized export, final data |
| **schema** (warehouse layer) | database, namespace |

## Skills

Reusable agent skills for common authoring tasks live in `.agents/SKILLS/`. Load
the relevant skill before starting a task:

* `.agents/SKILLS/concept-file-author/` — scaffolds new concept files and section
  READMEs following the conventions in this file
* `.agents/SKILLS/style-reviewer/` — audits a Markdown file or PR diff for
  terminology, voice, formatting, and navigation violations; returns a structured
  report with line references and suggested fixes
* `.agents/SKILLS/concept-reviewer/` — reviews content quality of a concept file
  or README for clarity, completeness, structure logic, audience calibration, and
  cross-link coverage; run after style-reviewer, not instead of it

## Boundaries

**Always:**
* Link to existing concept files rather than re-explaining a concept inline
* Update the parent `README.md` concept table when adding a new file to a section

**Ask first:**
* Adding a new numbered concept section (`03-...`)
* Changing terminology that appears across multiple files

**Never:**
* Add YAML frontmatter
* Use HTML in Markdown files
* Use relative paths for internal cross-references
* Use `-` for unordered list items
* Create files with uppercase letters or spaces in the name
