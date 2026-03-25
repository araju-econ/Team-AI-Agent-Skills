---
name: stata
description: Use when the user wants help writing, editing, running, or debugging Stata do-files. Triggers on requests involving Stata commands, .dta files, regressions, data cleaning, merges, loops, macros, labels, or any Stata-specific workflow.
---

## What is Stata

Stata is a statistical software package widely used in economics, epidemiology, and social science. It combines a point-and-click interface with a scripting language. The core workflow is:

1. Write commands in a **do-file** (`.do`) — a plain-text script
2. Run the do-file against a dataset (`.dta`)
3. Inspect results in the **Results** window and **log files**

Stata commands operate on one dataset loaded in memory at a time (`use`, `clear`, `save`). Results from estimation commands are stored in `r()` and `e()` scalars/matrices and can be retrieved immediately after.

---

## This Installation

- **Version**: Stata 18 MP (64-bit), licensed to University of Zurich
- **Executable**: `C:\Program Files\Stata18\StataMP-64.exe`
- **ADO path** (built-in commands): `C:\Program Files\Stata18\ado\base\`
- **Example dataset**: `C:\Program Files\Stata18\auto.dta`
- **PDF manuals**: `C:\Program Files\Stata18\docs\` — do not read these directly (very large). Use the pypdf workflow below to extract only relevant pages.
  - `u.pdf` (399 pp) — User's Guide
  - `d.pdf` (970 pp) — Data management
  - `r.pdf` (3288 pp) — Reference manual (all base commands A–Z)
  - `g.pdf` (767 pp) — Graphics
  - `p.pdf` (664 pp) — Programming (macros, loops, ado programs)
  - `fn.pdf` (188 pp) — Functions
  - `bayes.pdf` (830 pp), `lasso.pdf`, `me.pdf` (574 pp), `mi.pdf` (394 pp), `sem.pdf`, `st.pdf` (578 pp), `svy.pdf` (221 pp), `ts.pdf` (987 pp), `xt.pdf` (635 pp) — Specialized modules

---

## Running Do-Files

**From within Stata (GUI or command line):**
```stata
do "C:\Users\araju\path\to\file.do"
```

**From Windows command line / terminal (batch mode, no GUI):**
```bat
"C:\Program Files\Stata18\StataMP-64.exe" /e do "C:\Users\araju\path\to\file.do"
```
The `/e` flag runs in batch mode and writes output to a `.log` file beside the do-file.

**From within a do-file, run another do-file:**
```stata
do "$main/code/clean.do"
run "$main/code/helper.do"   // run = do but suppresses output
```

---

## Do-File Conventions (from this project)

Based on existing do-files in this environment, the standard header pattern is:

```stata
*****************************************************************************
* PROGRAM: Short description
* BY: Author name
* DATE: DD/MM/YYYY
*****************************************************************************

cls
clear
set more off
version 18.0

* Set global paths
global main "C:\Users\araju\path\to\project"
```

Common conventions observed:
- `**#` section headers (Stata 17+ foldable sections in the editor)
- `assert` statements after every merge and count to verify data integrity
- Extended missing values (`.d` = don't know, `.r` = refused, `.n` = not applicable)
- `unique varname` for deduplication checks (requires `ssc install unique`)
- `bys strata: egen N = total(treatment)` pattern for strata-balance tables
- `forvalues i = 1/8 { ... }` for repeated wave variables
- `$main` global for the root project path

---

## Key Command Reference

### Data I/O
```stata
use "file.dta", clear
use "$main/1_data/1_raw/file.dta", clear
save "$main/1_data/2_cleaned/file.dta", replace
merge 1:1 id using "other.dta", keep(match master) nogen
append using "other.dta"
```

### Inspection
```stata
desc varname          // variable metadata
tab varname, m        // frequency table with missing
tab varname, m nol    // show numeric codes
sum varname           // summary stats
count if condition
assert condition      // error if false — use for QA
list in 1/10
```

### Data manipulation
```stata
gen newvar = expression
replace var = value if condition
rename old new
drop varname
keep varlist
label define lname 0 "No" 1 "Yes"
label values var lname
label var var "Description"
order var1 var2, first
encode strvar, gen(numvar)
recode var (1 2 = 0) (3 = 1)
```

### Loops and macros
```stata
* Local macro (exists only within current do-file run)
local mylist "var1 var2 var3"
foreach v of local mylist {
    tab `v'
}

forvalues i = 1/8 {
    gen x`i' = .
}

* Global macro (persists across do-files in session)
global main "C:\Users\araju\project"
use "$main/data.dta"
```

### Estimation
```stata
reg y x1 x2, robust
reghdfe y x1 x2, absorb(id year) vce(cluster id)   // requires reghdfe
ivreg2 y (x = z) controls, robust                   // requires ivreg2
logit y x1 x2
probit y x1 x2
areg y x1, absorb(id) robust
```

### Post-estimation
```stata
estimates store name
esttab name1 name2, se r2 b(3)    // requires estout
outreg2 using "table.docx", replace
```

### Randomization / power
```stata
power twomeans mu1 mu2, sd(s) power(0.8) n(n)
power twomeans mu1, k1(clusters) k2(clusters) m1(size) m2(size) power(0.8) sd(1) rho(icc)
```

---

## Installing User-Written Packages

```stata
ssc install reghdfe
ssc install ivreg2
ssc install estout
ssc install unique
ssc install sdecode
net install package, from(url)
```

Installed packages go to the personal ado folder. Check with:
```stata
adopath
```

---

## Logging

```stata
log using "$main/logs/run.log", replace text
* ... your code ...
log close
```

Or run with `/e` flag from command line — Stata writes a `.log` automatically.

---

## Looking Up Stata Documentation

The PDFs are too large to read in full. Use `pypdf` (already installed) to find and extract only the pages you need.

### Which PDF contains which command?

| Command category | PDF |
|---|---|
| Base commands (reg, merge, gen, tab, …) | `r.pdf` |
| Data management (use, save, reshape, merge, …) | `d.pdf` |
| Programming (macros, loops, ado, mata, …) | `p.pdf` |
| Functions (string, math, date, …) | `fn.pdf` |
| Graphics | `g.pdf` |
| User's Guide (concepts, workflows) | `u.pdf` |
| Multilevel/mixed models | `me.pdf` |
| Time series | `ts.pdf` |
| Panel data | `xt.pdf` |
| Survey data | `svy.pdf` |
| Survival analysis | `st.pdf` |
| Multiple imputation | `mi.pdf` |
| Bayesian analysis | `bayes.pdf` |

All PDFs live at: `C:\Program Files\Stata18\docs\`

### Workflow: look up a specific command

Use a Bash tool call with this Python one-liner. It finds the command's title page (the pattern Stata uses: "Title\n<command> — <description>") and extracts the next N pages.

```python
import pypdf, sys, io
sys.stdout = io.TextIOWrapper(sys.stdout.buffer, encoding='utf-8', errors='replace')

PDF   = r'C:\Program Files\Stata18\docs\d.pdf'   # change to relevant PDF
CMD   = 'merge'                                    # command name to look up
PAGES = 6                                          # pages to extract after title

r = pypdf.PdfReader(PDF)
hits = []
for i, page in enumerate(r.pages):
    lines = [l.strip() for l in (page.extract_text() or '').split('\n') if l.strip()]
    for j, line in enumerate(lines[:-1]):
        if line == 'Title' and lines[j+1].lower().startswith(CMD.lower()):
            hits.append(i)

if not hits:
    print(f'No title page found for {CMD!r}')
else:
    for pg in range(hits[0], min(hits[0] + PAGES, len(r.pages))):
        print(f'\n=== page {pg} ===')
        print(r.pages[pg].extract_text() or '')
```

Run this as a one-liner via Bash:
```bash
python -c "
import pypdf, sys, io
sys.stdout = io.TextIOWrapper(sys.stdout.buffer, encoding='utf-8', errors='replace')
PDF = r'C:\Program Files\Stata18\docs\d.pdf'; CMD = 'merge'; PAGES = 6
r = pypdf.PdfReader(PDF)
hits = [i for i, pg in enumerate(r.pages) if any(l.strip() == 'Title' and lines[j+1].lower().startswith(CMD.lower()) for j, l in enumerate(lines := [x.strip() for x in (pg.extract_text() or '').split('\n') if x.strip()][:-1]))]
[print(f'\n=== page {p} ===\n' + (r.pages[p].extract_text() or '')) for p in range(hits[0], min(hits[0]+PAGES, len(r.pages)))] if hits else print('not found')
"
```

### Workflow: keyword search across a PDF

When you don't know the page but need to find where a topic is discussed:

```bash
python -c "
import pypdf, sys, io
sys.stdout = io.TextIOWrapper(sys.stdout.buffer, encoding='utf-8', errors='replace')
PDF = r'C:\Program Files\Stata18\docs\u.pdf'; KW = 'egen'
r = pypdf.PdfReader(PDF)
for i, pg in enumerate(r.pages):
    t = pg.extract_text() or ''
    if KW.lower() in t.lower() and len(t) > 200:
        print(f'page {i}: {t[:120].strip()}')
        print('---')
" 2>&1 | head -60
```

### Workflow: extract a page range directly

When you already know roughly which pages to read (e.g., from a TOC search):

```bash
python -c "
import pypdf, sys, io
sys.stdout = io.TextIOWrapper(sys.stdout.buffer, encoding='utf-8', errors='replace')
PDF = r'C:\Program Files\Stata18\docs\p.pdf'; START = 40; END = 48
r = pypdf.PdfReader(PDF)
for i in range(START, min(END, len(r.pages))):
    print(f'\n=== page {i} ===')
    print(r.pages[i].extract_text() or '')
"
```

### Notes on text quality

- The Stata PDFs render well with pypdf — syntax boxes and examples come through clearly.
- Ligatures like `fi`/`fl` may appear as `ﬁ`/`ﬂ` — this is cosmetic and doesn't affect readability.
- If layout is garbled for a particular PDF, install `pdfplumber` (`pip install pdfplumber`) for better table and column handling. The API is similar: `pdfplumber.open(PDF).pages[i].extract_text()`.

---

## Tips for This Environment

- Use `version 18.0` at the top of do-files to ensure reproducibility
- The `auto.dta` example dataset at `C:\Program Files\Stata18\auto.dta` is always available for testing: `sysuse auto, clear`
- When writing do-files to share (e.g., replication files), define all paths via a single global at the top so collaborators only change one line
- Use `tempfile` and `tempvar` for intermediate objects that don't need to be saved to disk
