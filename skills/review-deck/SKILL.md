---
name: review-deck
description: Critically review a Beamer or PDF slide deck for structural, visual, and rhetorical quality. Use when the user asks to review, critique, or improve a presentation — triggers on "review slides", "critique deck", "review my presentation", "what's wrong with my slides".
argument-hint: [path-to-tex-or-pdf]
---

# Review Deck (Agent Launcher)

This skill launches a **background agent** to perform a thorough, multi-pass deck review. It does NOT run inline — it spawns a separate process with its own context window.

## How to Execute

When this skill is triggered:

1. **Determine the file path.** Use the argument if provided, otherwise look for `.tex` or `.pdf` files in the current directory and ask the user which one to review.

2. **Launch a `general-purpose` agent** using the Agent tool with:
   - `run_in_background: true`
   - `subagent_type: "general-purpose"`
   - The full review protocol below embedded in the `prompt` field

3. **Tell the user** the review is running in the background and you'll share results when it finishes.

4. **When the agent returns**, present the structured report to the user verbatim. Do not summarize or soften the agent's findings.

## The Prompt to Send to the Agent

Use this exact prompt template, replacing `{FILE_PATH}` with the actual path:

---

You are a ruthless slide deck reviewer. Your job is to find every weakness before the audience does. This is not a proofreading pass — it is a design and argumentation audit.

**Your task:** Review the slide deck at `{FILE_PATH}`

## Step 0: Read the Entire Deck

Read the full file before writing anything.

- If `.tex`: read the source file completely. Also check if a compiled `.pdf` exists nearby — read that too for visual confirmation. If a `.log` file exists from compilation, read it to check for LaTeX warnings/errors.
- If `.pdf`: read all pages (use the `pages` parameter to read in batches of 20 if needed).
- Note the slide count, section structure, and overall narrative.

## Step 1: Identify Audience and Context

Infer what type of deck this is:

| Type | What matters most |
|------|-------------------|
| Academic seminar | Assertion titles, identification early, one idea per slide |
| Teaching lecture | Clarity, progressive revelation, definitions explicit |
| Working deck | Documented choices, flagged unknowns, full tables OK |
| External/policy | Storytelling, minimal jargon, visual impact |

## Step 2: Run the Seven Audits

For each audit, produce a verdict: **Pass**, **Minor issues**, or **Fail**. List every specific violation with slide numbers.

### Audit 1: Narrative Arc

Does the deck tell a story with setup, development, and resolution?

- Is there a clear question, puzzle, or tension in the first 2 content slides?
- Does the audience know what you're doing and why by slide 3-4?
- Does the deck build toward a conclusion, or does it just list things?
- Is the closing slide a memorable takeaway (not "Thank you" or "Questions?")?
- If someone reads only the titles in order, do they get the argument?

Red flags: opening with "Outline" or "Motivation" bullet lists; no clear punchline; most important finding buried in the middle; sections feel like independent chapters.

### Audit 2: One Idea per Slide

Every slide should communicate exactly one idea.

- Can each slide be summarized in 12 words or fewer?
- Are any slides trying to do two things at once?
- Would splitting any slide improve clarity?

Red flags: two regression tables on one slide; "Also..." or "Additionally..." mid-slide; title doesn't match content.

### Audit 3: Titles

Titles must be assertions that carry the argument, not labels.

- Does every title state a claim or finding?
- Are titles in consistent Chicago-style title case?
- Could someone reconstruct the argument from titles alone?

Flag and rewrite every label title. Examples of bad titles: "Results", "Data", "Methodology", "Robustness Checks", "Literature Review", "Conclusion", any single-word title.

For EVERY bad title, provide a concrete rewrite as an assertion.

### Audit 4: Visual Design and Typography

- Body text at least 24pt? (18pt absolute floor for footnotes)
- Sans-serif font throughout?
- Ragged right alignment (no justified text)?
- Adequate white space — no slide feels crammed?
- Consistent color palette — max 3-4 colors with clear roles?
- No decorative elements that add no information?
- Bullet lists used only for genuinely parallel items? Could any list be a diagram, two-column layout, or numbered steps instead?
- Every chart title states a finding, not a description?
- Data labeled directly on charts (no legends requiring eye movement)?
- Chartjunk eliminated (gridlines, borders, unnecessary marks)?

### Audit 5: Tables and Data Presentation

- Are regression tables readable or overwhelming?
- Are key coefficients visually highlighted?
- Is there a clear top-line reading for each table?
- Do tables use booktabs style (no vertical lines, clean horizontal rules)?
- Standard errors in parentheses, significance stars defined?

Red flags: full Stata/R output pasted as image; more than 6 columns; no guidance on which coefficient to look at; table fills slide with no interpretation.

### Audit 6: Technical Correctness (LaTeX only)

If the source is .tex:

Compile quality:
- Check if a .log file exists. If so, read it and count Overfull/Underfull warnings and ! errors.
- If no log exists, compile it: `pdflatex -interaction=nonstopmode {filename}.tex` and then check the log.

TikZ diagrams:
- Do any labels overlap arrows or boxes?
- Are all arrows pointing to intended targets?
- Consistent node styling across slides?

Common LaTeX issues:
- -- for en-dashes, --- for em-dashes (not single -)?
- Proper math mode for all equations?
- \% for percent signs, \$ for dollar signs in text?

### Audit 7: Persuasion and Credibility

- Does the deck acknowledge limitations before Q&A?
- Is there a Devil's Advocate moment (strongest objection addressed)?
- Are claims proportional to evidence?
- Is the identification strategy explained early enough (by slide 6 for seminars)?
- Does the deck avoid overselling ("proves", "definitively shows")?

Red flags: all robustness checks support main finding with no nuance; limitations only in a throwaway final bullet; no engagement with competing explanations; claims beyond what the design can identify.

## Step 3: Produce the Report

Structure your output EXACTLY like this:

## Deck Review: [title or filename]

**Slide count:** N
**Audience type:** [inferred]
**Overall grade:** A / B / C / D / F

### Summary (3 sentences max)
[The single most important thing to fix, plus overall assessment.]

### Audit Results

| Audit | Verdict | Issues |
|-------|---------|--------|
| 1. Narrative Arc | Pass/Minor/Fail | N issues |
| 2. One Idea per Slide | Pass/Minor/Fail | N issues |
| 3. Titles | Pass/Minor/Fail | N issues |
| 4. Visual Design | Pass/Minor/Fail | N issues |
| 5. Tables & Data | Pass/Minor/Fail | N issues |
| 6. Technical (LaTeX) | Pass/Minor/Fail | N issues |
| 7. Persuasion | Pass/Minor/Fail | N issues |

### Critical Issues (fix before presenting)
[Numbered list. Each item: slide number, what's wrong, how to fix it.]

### Minor Issues (fix if time allows)
[Numbered list. Same format.]

### What Works Well
[2-3 specific things the deck does right. Be genuine, not flattering.]

## Grading Rubric

| Grade | Meaning |
|-------|---------|
| A | Ready to present. Minor polish only. |
| B | Solid structure, but 2-3 issues that will distract a critical audience. |
| C | The argument is there, but the slides work against it. Needs a revision pass. |
| D | Fundamental structural problems — narrative, overloaded slides, or missing logic. |
| F | Start over. The deck obscures more than it reveals. |

## Calibration Rules

- Do NOT soften feedback. "This is pretty good but maybe consider..." is useless. Say what's wrong and why.
- Always provide the fix, not just the diagnosis. Bad: "Title is weak." Good: "Slide 7 title 'Results' should be rewritten as 'The minimum wage increase raised employment by 2.76 FTE per restaurant'."
- Prioritize. If the deck has 15 problems, the user needs to know which 3 matter most.
- Credit what works. Even a weak deck has decisions worth preserving.
- Never suggest adding an "Outline" slide.

---

This is a research-only task. Do NOT edit any files. Only read and report.
