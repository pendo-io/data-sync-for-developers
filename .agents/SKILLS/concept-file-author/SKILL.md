---
name: concept-file-author
description: Creates new Markdown concept files and README indexes for the pendo-io/data-sync-for-developers repo, following the exact structure, voice, formatting, and navigation conventions established in AGENTS.md. Use this skill whenever someone asks to write, draft, scaffold, or add a new concept doc, topic file, or section README in this repo — even if they just say "write a doc on X" or "add a page about Y" without mentioning the skill by name.
---

# Concept File Author

This skill produces new Markdown files for the `pendo-io/data-sync-for-developers`
repo that conform to the conventions in `AGENTS.md`. There are two output types:
**concept files** (e.g. `export-overview.md`) and **section READMEs** (e.g.
`concepts/01-dataload/README.md`).

Before writing, confirm with the user:
1. The topic or title of the file
2. Which section it belongs to (e.g. `01-dataload/`, `02-querying/`)
3. Whether it is a concept file or a section README
4. Any specific content points, cross-links, or related files to include

If you can reasonably infer these from context, proceed and note your assumptions
at the top of your response.

---

## Concept file template

Use this structure for every concept file (non-README):

```
# [Topic Title]

[Direct opening paragraph. No preamble. Second person. Get straight to the point.]

## [Major Section]

[Content. Use `*` for lists. Bold key terms on first use. Backticks for all literals.]

### [Subsection if needed]

[Do not go deeper than H3.]

---

**Related:** [File A](FULL_GITHUB_URL) · [File B](FULL_GITHUB_URL)

---

← [Section Name](FULL_GITHUB_URL_TO_PARENT_README)
```

The `**Related:**` line is optional — include it when two or more other concept
files are closely relevant. The back-nav line is always required.

---

## Section README template

Use this structure for every `README.md` index file:

```
# [Section Title] Overview

[Direct opening paragraph explaining what this section covers and why it matters
to a data engineer building a Pendo Data Sync pipeline.]

## [Flow or Key Concepts]

[A short orientation — an ASCII flow diagram or a bullet list of key concepts.
Match the style of existing READMEs.]

## Topics in this section

| Topic | Description |
| --- | --- |
| [File Title](FULL_GITHUB_URL) | One-sentence description of what the file covers. |

## Learn More

* [Link text](URL) — Short description of external resource.
```

The concept table must link every file in the section. Update it whenever a new
file is added.

---

## Formatting rules (apply to both types)

**Headings**
* One H1 per file, matching the topic name exactly
* H2 for major sections, H3 for subsections — never deeper
* No YAML frontmatter

**Emphasis and inline code**
* **Bold** key terms on first introduction
* Backticks for all literals: field names (`periodId`), file names
  (`billofmaterials.json`), table names (`ALLEVENTS`), paths
* Table and schema names in `ALL_CAPS`; field names in `camelCase`

**Lists**
* Always `*`, never `-`
* Definition-style items: bold the term, em dash, then the definition

**Callouts**

GitHub supports five alert types. Use at most one per section; never stack
multiple alerts consecutively.

```
> [!NOTE]
> Supplementary context a reader might miss but won't break anything if they do.

> [!TIP]
> An optional practice that meaningfully improves the outcome.

> [!IMPORTANT]
> Information a reader must have to succeed — missing it causes silent failure.

> [!WARNING]
> An action that will cause errors, duplicates, or data corruption.

> [!CAUTION]
> An irreversible or destructive action with no easy recovery.
```

Pick the lowest severity that is accurate. Most files need at most one callout.
If you find yourself adding more than two in a single file, rewrite the prose
to surface those concerns in the main flow instead.

**Horizontal rules**
* Use `---` as a visual break in longer files where a section change benefits
  from whitespace
* Always place `---` before the navigation footer

**Internal links**
* Always full GitHub URLs — never relative paths:
  `https://github.com/pendo-io/data-sync-for-developers/blob/dataload/concepts/...`

---

## Voice and tone

* Second person throughout: `you` and `your`
* Active voice, imperative mood for instructions
* Audience knows ETL, Avro, and data warehouse concepts — do not over-explain them
* No preamble: do not open with "In this document...", "This file covers...",
  "It's worth noting...", or "Keep in mind that..."

---

## Domain terminology

Use these exactly. Do not invent variants.

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

---

## File naming

* Concept files: `kebab-case.md` — no spaces, no uppercase
* New section folders: `NN-topic-name/` matching the numbered prefix pattern
* Do not create files with uppercase letters or spaces in the name
