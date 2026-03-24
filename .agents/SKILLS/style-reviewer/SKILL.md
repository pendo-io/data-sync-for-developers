---
name: style-reviewer
description: Reviews Markdown files in the pendo-io/data-sync-for-developers repo against the conventions in AGENTS.md. Use this skill whenever someone asks to review, check, lint, proofread, or audit a concept file or README for style, formatting, voice, or terminology issues — even if they just paste a draft and say "does this look right?" or "can you check this?". Also use when reviewing a PR diff that touches .md files in the concepts/ directory.
---

# Style Reviewer

This skill audits a Markdown file or draft against the conventions in `AGENTS.md`
and produces a structured report of violations, each with a line reference and a
concrete suggested fix. It does not rewrite the file — it gives the author
actionable findings they can apply themselves.

## Input

The user will either:
- Paste the Markdown content directly into the chat
- Share a file path or GitHub URL to a specific file
- Describe a PR or diff to review

If the content is a URL, fetch it before proceeding. If it is a diff, review only
the added lines (lines prefixed with `+`).

---

## Review checklist

Work through every category below in order. For each finding, record:
- The **category** (e.g. Terminology, Lists)
- The **line or excerpt** from the file
- The **violation**
- The **suggested fix**

If a category has no findings, skip it in the output — do not list it as "✅ No issues".

### 1. Terminology

Check every instance of the following against the approved terms. Flag any variant
that does not exactly match the "Use" column.

| Use | Flag if you see |
| --- | --- |
| **Data Sync** | DataSync, data sync, datasync |
| **export manifest** | manifest file, export index, the manifest |
| **bill of materials** | BOM, materials file, bom |
| `periodId` | period ID, period_id, PeriodId |
| `matchableId` | matchable ID, matchable_id, MatchableId |
| **retroactive export** | retroactive processing export, retro export |
| **finalized data** | finalized export, final data |
| **schema** (warehouse layer) | database, namespace (when referring to a warehouse schema layer) |

### 2. Voice and tone

Flag any of the following:
- "the developer", "the user", "the engineer" — should be "you" or "your"
- Sentences opening with "In this document", "This file covers", "This document
  explains", "This section describes"
- Hedging phrases: "it's worth noting", "keep in mind that", "note that",
  "please note", "it should be mentioned"
- Passive constructions where active is clearly possible (flag only clear cases —
  do not over-flag)

### 3. Lists

Flag any unordered list item that uses `-` instead of `*`.

Flag any definition-style list item where the term is not bolded or an em dash
is not used as the separator (e.g. uses `:` or `-` instead of ` — `).

### 4. Headings

Flag any of the following:
- More than one H1 in the file
- Headings at H4 (`####`) or deeper
- YAML frontmatter (any content between `---` delimiters at the top of the file)

### 5. Inline formatting

Flag these patterns:
- A key Pendo or Data Sync term appears for the first time in the file without
  bold formatting (e.g. "export manifest" without `**export manifest**`)
- A field name, file name, table name, or path that is not wrapped in backticks
  (e.g. `periodId` written as plain text, `allevents.avro` without backticks)
- A table or schema name not in `ALL_CAPS` (e.g. `allevents` instead of `ALLEVENTS`)
- A field name not in `camelCase` (e.g. `Period_Id` instead of `periodId`)

### 6. Code blocks

Flag any fenced code block that has a language specifier when it contains SQL,
a directory tree, or an ASCII flow diagram (these should use bare triple backticks).

### 7. Callouts

Flag any of the following:
- Two or more callouts in the same section with no prose between them (stacking)
- A `[!NOTE]` or `[!TIP]` used for something that is genuinely critical to data
  correctness (should be `[!IMPORTANT]` or `[!WARNING]`)
- A `[!IMPORTANT]` or `[!WARNING]` used for something that is supplementary or
  optional (should be `[!NOTE]` or `[!TIP]`)
- HTML used instead of the GitHub alert syntax

### 8. Links

Flag any internal link that uses a relative path instead of a full GitHub URL
(e.g. `[Finalized Data](../01-dataload/finalized-data.md)` should use the full
`https://github.com/pendo-io/...` URL).

### 9. Navigation footer (concept files only — skip for README.md)

Flag if:
- The file does not end with a `← [Section Name](URL)` back-navigation line
- The back-navigation line is not preceded by a `---` divider
- The URL in the back-navigation line is a relative path

### 10. README structure (README.md files only — skip for concept files)

Flag if:
- The file does not have a `## Learn More` section at the end
- The `## Learn More` section uses `-` for list items instead of `*`
- The link format in `## Learn More` uses `:` instead of ` — ` as a separator

---

## Output format

Produce a report with this structure:

```
## Style Review: [filename or "pasted content"]

### Summary
X issues found across Y categories.

### Findings

**[Category name]**

| # | Excerpt | Issue | Suggested fix |
| --- | --- | --- | --- |
| 1 | `the user should...` | "the user" — use second person | `you should...` |
| 2 | `- Export manifest` | `-` list marker | `* Export manifest` |

**[Next category with findings]**
...

### No issues found
[Only include this section if the file is fully clean.]
```

Keep excerpts short — enough to locate the line, not the full sentence. Suggested
fixes should be the minimal change that resolves the issue, not a rewrite.

If the file is fully clean across all categories, say so directly and briefly.
Do not pad the report.

---

## Tone

Be direct and specific. This is a technical review, not an editorial critique.
Do not compliment the draft or soften findings — just report what needs fixing
and how to fix it. The goal is to make the author's next edit as fast as possible.
