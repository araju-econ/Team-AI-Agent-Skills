---
name: explore-folder
description: Dive into a folder and identify its contents, structure, key files, and purpose. Use when the user wants to understand what's in a folder — triggers on "explore folder", "what's in this folder", "identify files", "folder overview", "summarize directory".
---

## Purpose

Produce a structured, readable overview of a folder: what it contains, how it's organized, what the key files are, and what the folder is apparently for.

---

## Invocation

The user will say something like `/explore-folder` optionally followed by a path. If no path is given, use the current working directory. If a path is given, use that.

---

## Workflow

### Step 1 — Map the structure

Use `ls` (or Bash `find`) to get a recursive listing of the folder. Aim for depth 3 or less to avoid overwhelming output. Note:
- Total number of files
- All unique file extensions present
- Subfolders and their apparent purpose based on naming

### Step 2 — Classify file types

Group files by extension into categories:

| Category | Extensions |
|---|---|
| Documents | `.pdf`, `.docx`, `.doc`, `.odt`, `.txt`, `.md` |
| Spreadsheets | `.xlsx`, `.xls`, `.csv`, `.ods` |
| Stata | `.do`, `.dta`, `.ado`, `.log`, `.smcl` |
| R | `.R`, `.Rmd`, `.Rdata`, `.rds` |
| Python | `.py`, `.ipynb` |
| Images | `.png`, `.jpg`, `.svg`, `.tif` |
| Presentations | `.pptx`, `.ppt`, `.key` |
| Archives | `.zip`, `.gz`, `.tar`, `.7z` |
| Other | anything else |

### Step 3 — Sample key files

For the most important-looking files (prioritize: READMEs, main do-files, top-level documents, anything with "main", "master", "overview", "protocol", "design" in the name):

- **Markdown / plain text**: Read fully (it's short)
- **PDFs**: Read first 1–2 pages for title, abstract, or table of contents
- **Stata do-files**: Read the header block (first 30 lines) for purpose, date, author
- **Word .docx**: Note filename and size; read if small (<50 KB estimated)
- **Excel**: Note filename; do not try to read binary content
- **Data files (.dta, .csv)**: Note filename and size; do not read raw

Limit sampling to the 5–10 most informative files. Do not read everything.

### Step 4 — Identify purpose and themes

Based on structure and sampled content, infer:
- What is this folder for? (e.g., "survey instrument drafts", "ethics application", "literature review PDFs")
- Who is likely using it and how?
- Are there any obvious gaps, orphaned files, or organizational issues worth flagging?
- What is the most important or "anchor" file a newcomer should read first?

### Step 5 — Output a structured report

Return a report with these sections:

```
## Folder Overview

**Path:** [full path]
**Total files:** N
**Subfolders:** list them

---

## File Types Found

[table or bullet list by category]

---

## Subfolder Breakdown

For each subfolder:
- **Name/** — inferred purpose, N files, main file types

---

## Key Files

For each sampled key file:
- **filename** — one-sentence description of what it is/does

---

## Summary

2–4 sentence plain-English summary: what is this folder, what stage is the work at, and what should a newcomer look at first?

---

## Flags (if any)

- Anything that looks misplaced, duplicated, very large, or outdated
```

---

## Tips

- Be concise in the Key Files section — one line per file.
- If the folder is a research project, note the phase: design, data collection, analysis, writing.
- If files are in German, French, or English, note the language mix.
- Do not list every single file — only the ones that matter for understanding the folder's purpose.
- If the folder is very large (>200 files), sample strategically rather than exhaustively.
