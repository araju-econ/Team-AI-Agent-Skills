---
name: newproject
description: Scaffold a new research project with standard directory structure, CLAUDE.md template, and documented README. Use this at the start of every new project to ensure consistent organization.
allowed-tools: Bash(mkdir*), Bash(ls*), Bash(git*), Bash(cd*), Write, Read
argument-hint: [project-name]
---

# New Research Project

Create a new economics research project with the standard numbered folder structure, a CLAUDE.md instruction file, and a README.

## Folder Structure

```
[Project Name]/
├── CLAUDE.md
├── README.md
├── .gitignore
├── 1_Literature/
├── 2_Presentations/
├── 3_Admin/
├── 4_Design/
├── 5_Questionnaires/
├── 6_Ethics/
├── 7_Logistics/
├── 8_Analysis/
│   ├── code/
│   │   ├── stata/
│   │   ├── R/
│   │   └── python/
│   ├── data/
│   │   ├── raw/            # Original source data (never modify)
│   │   └── clean/          # Cleaned/merged datasets
│   └── output/
│       ├── tables/
│       └── figures/
├── log/                    # Claude Code and Codex session logs
└── archive/
```

### What each folder is for

| Folder | Purpose |
|--------|---------|
| `1_Literature/` | Papers, literature review files, reference materials |
| `2_Presentations/` | Slides, presentation PDFs, talk outlines |
| `3_Admin/` | Data applications, contracts, administrative documents |
| `4_Design/` | Research design documents, experimental design, timelines |
| `5_Questionnaires/` | Survey instruments, questionnaire drafts, Qualtrics exports |
| `6_Ethics/` | Ethics applications, IRB/ethics board correspondence |
| `7_Logistics/` | Field logistics, coordination documents, budgets |
| `8_Analysis/` | Analysis work — contains `code/` (stata, R, python), `data/` (raw, clean), and `output/` (tables, figures) |
| `log/` | Session logs from Claude Code and Codex (date-stamped summaries) |
| `archive/` | Deleted files go here (never delete, always archive) |

## Execution

1. **Get the project name.** Use the argument if provided. If not, ask the user. Use the name as-is for the folder (preserve spaces, capitalization — this is how economists name project folders).

2. **Determine location.** Default is the current working directory. Confirm with the user if unclear.

3. **Create all directories:**
   ```bash
   mkdir -p "[project-name]"/{1_Literature,2_Presentations,3_Admin,4_Design,5_Questionnaires,6_Ethics,7_Logistics,8_Analysis/{code/{stata,R,python},data/{raw,clean},output/{tables,figures}},log,archive}
   ```

4. **Create CLAUDE.md** from the template below. Replace `[Project Name]` with the actual project name. Replace `[PI Name]` with the PI's name if known, otherwise leave the placeholder.

5. **Create README.md** with the project name, a brief description placeholder, and the folder structure tree.

6. **Create .gitignore** with sensible defaults for an economics research project.

7. **Set up shared team skills** (see below).

8. **Print a summary** of what was created. Remind the user to fill in the CLAUDE.md.

## Shared Team Skills Setup

The team's shared Claude Code skills live in a Git repo. Each team member's `~/.claude/skills/` directory IS the repo — so `git pull` updates everyone's skills.

**During project scaffolding, check if this is already set up:**

1. Check if `~/.claude/skills/.git` exists.
2. **If yes** — it's already set up. Run `git pull` inside `~/.claude/skills/` to get the latest skills. Tell the user skills are up to date.
3. **If no** — the user hasn't set up shared skills yet. Ask them:
   - "Your team's shared skills aren't set up yet. Do you have a team skills repo URL? (e.g., https://github.com/your-team/team-claude-skills)"
   - **If they provide a URL**: Back up existing skills to a temp folder, clone the repo as `~/.claude/skills/`, then copy any backed-up skills that don't conflict back in and commit them.
     ```bash
     # Back up existing skills
     cp -r ~/.claude/skills /tmp/claude-skills-backup
     rm -rf ~/.claude/skills
     # Clone team repo
     git clone <URL> ~/.claude/skills
     # Copy back any skills that don't already exist in the repo
     # (skip ones that conflict)
     ```
   - **If they want to create a new repo**: Initialize `~/.claude/skills/` as a Git repo.
     ```bash
     cd ~/.claude/skills
     git init
     git add .
     git commit -m "Add initial shared skills"
     ```
     Then tell them to create a repo on GitHub and push:
     ```bash
     git remote add origin <URL>
     git push -u origin main
     ```
   - **If they say skip**: Move on. Don't block project creation on this.

**For new team members**, the onboarding is just:
```bash
git clone https://github.com/your-team/team-claude-skills.git ~/.claude/skills
```

## CLAUDE.md Template

```markdown
# Project: [Project Name]

## Overview
[What does this project study? Who is the PI? What stage is it at?]

## Project Structure
- `1_Literature/` — Papers and reference materials
- `2_Presentations/` — Slides and presentations
- `3_Admin/` — Data applications, administrative documents
- `4_Design/` — Research design and timelines
- `5_Questionnaires/` — Survey instruments and drafts
- `6_Ethics/` — Ethics applications and correspondence
- `7_Logistics/` — Field logistics, coordination, budgets
- `8_Analysis/` — Analysis work
  - `code/` — Scripts (stata/, R/, python/)
  - `data/` — Datasets (raw/ = untouched originals, clean/ = processed)
  - `output/` — Results (tables/, figures/)
- `log/` — Claude Code and Codex session logs

## Conventions
- Primary analysis language: Stata
- File naming: snake_case with numbered prefixes (e.g., `01_clean_data.do`)
- Files may be in English, German, or French
- Commit messages: imperative mood ("Add controls" not "Added controls")

## Rules
- Never delete files. Move to `archive/` preserving relative paths.
- Never commit data files (*.dta, *.csv) or credentials (*.env, *.secret).
- Always work on a branch. Never push directly to main.
- Never force-push or rewrite Git history.

## Key Files
[Add important files here as the project develops]

## Notes
[Active tasks, recent decisions, anything the AI should know]
```

## .gitignore Template

```gitignore
# Data
*.dta
*.rds
*.sav
*.csv
data/raw/
data/restricted/

# Outputs
*.pdf
*.png
*.jpg
*.eps

# LaTeX build files
*.aux
*.log
*.bbl
*.blg
*.synctex.gz
*.fls
*.fdb_latexmk

# System
.DS_Store
Thumbs.db
desktop.ini
~$*

# Secrets
.env
credentials.*
```

## README.md Template

```markdown
# [Project Name]

[Brief description of the project — what it studies, who is involved.]

## Folder Structure

- `1_Literature/` — Papers and reference materials
- `2_Presentations/` — Slides and presentations
- `3_Admin/` — Data applications, administrative documents
- `4_Design/` — Research design and timelines
- `5_Questionnaires/` — Survey instruments and drafts
- `6_Ethics/` — Ethics applications and correspondence
- `7_Logistics/` — Field logistics, coordination, budgets
- `8_Analysis/` — Analysis work
  - `code/` — Scripts (stata/, R/, python/)
  - `data/` — Datasets (raw/ = untouched originals, clean/ = processed)
  - `output/` — Results (tables/, figures/)
- `log/` — Claude Code and Codex session logs

## Data

Data files are not tracked in Git. [Describe where data is stored and how to access it.]

## Team

- [PI Name] (PI)
- [Add team members]
```
