---
name: concept-reviewer
description: Reviews the content quality of a Markdown concept file or README in the pendo-io/data-sync-for-developers repo. Evaluates clarity, completeness, structure logic, audience calibration, and cross-link coverage — things a mechanical linter cannot catch. Use this skill when someone asks for a content review, editorial feedback, or a deeper review of a draft beyond formatting. Also use when reviewing a community PR where the contributor may not be familiar with Pendo Data Sync internals. Run the style-reviewer first for mechanical issues; this skill focuses on whether the content itself is clear, complete, and useful to a data engineer.
---

# Concept Reviewer

This skill evaluates the content quality of a concept file or README draft. It
does not check formatting rules — run the `style-reviewer` skill for that first.
This skill asks: is the content clear, complete, and calibrated for a data
engineering audience?

The output is a structured review with specific findings and actionable
suggestions. It does not rewrite the file.

## Input

The user will either paste content directly or share a file path or GitHub URL.
If a URL, fetch it before proceeding. If a path, read it.

If the parent section README is available, read it too — it tells you what this
file is supposed to cover, which informs the completeness check.

---

## Review dimensions

Work through each dimension below. Only report findings where there is a genuine
content problem. Do not manufacture issues for the sake of thoroughness — an
empty dimension means the file handles that concern well.

### 1. Opening paragraph

A good opening paragraph answers three questions for a data engineer arriving
at this page:
- What is this concept?
- Why does it matter to my pipeline?
- What will I understand or be able to do after reading this?

Flag if:
- The opening paragraph takes more than three sentences to get to the point
- It states what the file covers rather than what the concept is (e.g. "This
  file explains finalized data" vs "When Pendo re-exports a day's events after
  ~7–9 days of processing, that re-export is called finalized data...")
- A reader skimming only the first paragraph would not know whether this file
  is relevant to their problem

### 2. Completeness

For the topic named in the H1, consider what a data engineer building a Pendo
Data Sync pipeline would need to know. Flag significant gaps — concepts
introduced but not explained, or questions a reader would obviously ask that
go unanswered.

Examples of completeness gaps:
- A file on **finalized data** that explains what it is but not how to handle
  it in the load pipeline
- A file on **retroactive exports** that describes the trigger but not the
  data handling implications
- A file on **exclude lists** that explains the concept but not how to apply
  it at query time

Do not flag gaps for content that belongs in a different concept file and is
cross-linked. Only flag genuinely missing content.

### 3. Structure logic

The heading hierarchy should reflect how a reader would naturally move through
the concept — from "what is this" to "why does it matter" to "how do I handle
it". Flag if:
- The order of sections makes a reader encounter implications before the concept
  is established
- A section contains content that belongs under a different heading
- Two sections cover the same ground from different angles without a clear reason
- A long section should be split, or short sections should be merged

### 4. Audience calibration

This repo is written for data engineers who know ETL, Avro, and data warehouse
concepts. Flag if:
- The file over-explains standard data engineering concepts (e.g. spending a
  paragraph explaining what a foreign key is)
- The file under-explains concepts specific to Pendo that a reader would not
  know from general data engineering experience (e.g. assuming the reader knows
  what a `periodId` represents without establishing it)
- Examples use unrealistic values or oversimplify in ways that would mislead
  someone implementing against real data

### 5. Callout judgement

Flag callouts where the visual weight is not earned:
- A callout that restates something already clear from the surrounding prose
- A `[!NOTE]` or `[!TIP]` that could be a sentence in the main flow
- A missing callout where a genuinely dangerous assumption is buried in prose
  (e.g. "the delete step is important" in a paragraph, when a `[!WARNING]` would
  prevent real data corruption)

Do not flag callouts that are already well-placed.

### 6. Cross-links

Read the Related line and any inline links. Flag if:
- A concept is explained inline that already has its own concept file and should
  be linked instead
- The Related line is missing a file that a reader would naturally want next
- A link is present but the linked file is not actually relevant to the content
  around the link

### 7. Section fit

If you have access to the parent README, check whether the file's actual content
matches its entry in the concept table. Flag if:
- The one-line description in the README does not accurately reflect the file
- The file covers territory that belongs in a different section

---

## Output format

```
## Content Review: [filename or "pasted content"]

### Summary
[One or two sentences on the overall quality. Be direct — "solid draft with
one completeness gap" or "the structure needs reordering before this is ready
to publish".]

### Findings

**[Dimension name]**
[Specific finding. Quote the relevant excerpt if it helps locate the issue.
Explain what the problem is and why it matters to the reader. Give a concrete
suggestion — not a rewrite, but enough direction that the author knows exactly
what to change.]

**[Next dimension with findings]**
...

### Ready to publish
[Only include this if there are no findings across all dimensions.]
```

If there are no findings in a dimension, skip it entirely. If the file is
genuinely ready to publish, say so in one sentence and stop — do not pad.

---

## Tone

This is an editorial review of technical documentation. Be direct and specific.
Explain why each finding matters to the reader, not just that it violates a
rule. The goal is to help the author make the file more useful — not to be
exhaustive or to demonstrate thoroughness.
