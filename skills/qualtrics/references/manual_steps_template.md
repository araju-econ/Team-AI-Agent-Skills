# Manual Steps Markdown Template

Generate a file named `qualtrics_manual_steps.md` in the same folder as the source document. Follow this structure:

```markdown
# Qualtrics Manual Configuration Steps

**Survey:** [Survey Name]
**Survey ID:** [SV_xxxxx]
**Date created:** [YYYY-MM-DD]
**Source file:** [filename]

All questions, blocks, embedded data, and randomizers have been created via API.
The following steps require manual configuration in the Qualtrics survey editor.

---

## 1. Consent Logic
- [ ] Skip logic on consent question (skip to end of block if "do not consent")
- [ ] Skip logic on confirm question (skip to end of survey if confirmed no)
- [ ] Custom end of survey message

## 2. Conditional Display Logic
- [ ] Display logic on each conditional question
- [ ] Reference specific QIDs, conditions, and choice text

## 3. Branch Logic (Survey Flow)
- [ ] AI placement branches
- [ ] Vignette order branches
- [ ] Country-conditional blocks

## 4. Slider Configuration
- [ ] Min, max, grid lines, labels for each slider question

## 5. Choice Randomization
Table of which questions need randomization:
| QID | Type | Randomization | Rationale |
|-----|------|---------------|-----------|

## 6. Matrix Row Randomization
- [ ] Show N of M rows configuration

## 7. Response Requirements (Force Response)
### Questions needing Force Response
| QID | Description | Current Status |
|-----|-------------|----------------|

### Questions that should be OPTIONAL
| QID | Description | Reason |
|-----|-------------|--------|

## 8. Page Breaks
- [ ] Location of each page break within blocks

---

## Ideal Survey Flow Diagram

ASCII diagram showing the complete target flow:
- Use box-drawing characters for structure
- Show all branches, randomizers, and blocks
- Mark display-logic questions with dagger symbol
- Include legend
```

## Section Guidelines

### Consent Logic
Provide exact click paths: "Click gear icon -> Add Skip Logic -> If [choice text] Is Selected -> Skip To: End of Block"

### Choice Randomization
Include a rationale table explaining WHY each question does or does not need randomization. Reference Stantcheva (2023) for survey experiment best practices.

For multi-select questions with a catch-all option (e.g., "Other"), note to use Advanced Randomization to lock it in the last position.

### Force Response
Group into three categories:
1. Already configured (verify)
2. Needs setting (with exact click path)
3. Should be optional (with reason — e.g., "voluntary email", "open-ended creates junk data")

### Flow Diagram
Use box-drawing characters and indentation to show nesting. Include:
- Embedded data declarations
- Debug mode group
- Randomizers with even presentation
- Branches (mutually exclusive)
- Block references with question counts
- Display logic annotations
- End of survey elements
