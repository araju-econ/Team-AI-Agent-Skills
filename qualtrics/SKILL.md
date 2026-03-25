---
name: qualtrics
description: This skill should be used when creating, formatting, or updating Qualtrics surveys from Word documents or design specs. Triggers on "qualtrics", "survey", "questionnaire", references to a Qualtrics survey ID (SV_*), or requests to build a survey from a document. Handles question creation, HTML formatting, survey flow structure, display/skip logic, randomization, validation, and Matrix/Slider configuration — all via API.
---

# Qualtrics Survey Builder

## Purpose

Build and format Qualtrics surveys programmatically via the Qualtrics MCP tools, applying consistent HTML formatting from source documents. Produce a manual-steps markdown file for everything that cannot be automated.

## Prerequisites

This skill requires the **Qualtrics MCP server**. Before proceeding, check whether Qualtrics MCP tools are available (e.g., `list_surveys`). If they are not, stop and prompt the user to install it first:

> The Qualtrics MCP server is not installed. You need it for this skill to work.
> Install it from: https://github.com/araju-econ/qualtrics-mcp-server
> Follow the setup instructions there, then restart Claude Code.

Do not attempt any survey operations without the MCP server connected.

---

## Golden Rule

**Never change question text content.** Only format HTML structure (bold, line breaks, helper text styling). The source document is the single source of truth for all wording.

---

## API Safety — CRITICAL

The fork's `update_question` tool now auto-fetches and preserves all current fields before applying changes, so it is safe to pass only the fields you want to change. However, be aware that the underlying Qualtrics API performs a **full PUT replacement** — the tool handles this for you, but if making direct API calls, always pass all fields.

---

## When Stuck — Consult Official Documentation

If you encounter issues implementing a feature, getting unexpected API behavior, or are unsure how a parameter works, **look up the official Qualtrics API documentation** before guessing or retrying blindly:

- Use `WebSearch` to look up specific endpoints or parameters on https://api.qualtrics.com/ (the API reference). Note: the site is JS-rendered, so `WebFetch` cannot read it — always use `WebSearch` instead.
- Also search https://www.qualtrics.com/support/ and https://community.qualtrics.com/ for feature-specific guidance and community solutions
- This is especially important for complex features like survey flow manipulation, advanced logic, or less-common question types

Do not repeatedly retry failing API calls without first understanding the expected request format from the docs.

---

## MCP Tool Limitations — Direct API Fallback

The Qualtrics MCP tools do not expose every field the Qualtrics API supports. When a field is missing from the MCP tool schema, **fall back to a direct `curl` call** against the Qualtrics REST API.

### Known unsupported fields (not in MCP tool schema)

| Field | Context | Workaround |
|-------|---------|------------|
| `TextEntry` on choices | Adding "allow text entry" to an MC choice (e.g., "Sonstiges") | Direct PUT to `/survey-definitions/{surveyId}/questions/{questionId}` |
| `Labels` on Slider questions | Setting min/max endpoint labels (e.g., "Völlig unzufrieden" / "Völlig zufrieden") | Direct PUT to same endpoint |

### How to make direct API calls

1. **Ask the user for their API token** if not already available
2. **Datacenter**: try `fra1.qualtrics.com` first (UZH default), then `eu`, then others
3. **Endpoint**: `PUT https://{datacenter}.qualtrics.com/API/v3/survey-definitions/{surveyId}/questions/{questionId}`
4. **Headers**: `X-API-TOKEN: {token}`, `Content-Type: application/json; charset=utf-8`
5. **Body**: Must be a **full PUT** — include ALL existing fields (fetch via `get_question` first), then add/modify the missing field
6. **Required fields**: The API validates required fields like `DataExportTag` — always include it
7. **Unicode**: Write the JSON body to a temp file and use `curl -d @/tmp/file.json` to avoid shell encoding issues with umlauts and special characters
8. **Remind the user to rotate their API token** after use, since it was shared in the chat

### Example: Adding Labels to a Slider

```bash
# 1. Get current question state via MCP get_question
# 2. Write full question JSON to temp file, adding the Labels field:
cat > /tmp/update.json << 'EOF'
{
  "QuestionText": "...",
  "QuestionType": "Slider",
  "Selector": "HSLIDER",
  "DataExportTag": "my_tag",
  "Configuration": { ... },
  "Choices": { ... },
  "Answers": { ... },
  "Labels": {
    "1": {"Display": "Min label"},
    "2": {"Display": "Max label"}
  },
  "Validation": { ... }
}
EOF
# 3. Send the update
curl -s -X PUT "https://fra1.qualtrics.com/API/v3/survey-definitions/{surveyId}/questions/{questionId}" \
  -H "X-API-TOKEN: {token}" \
  -H "Content-Type: application/json; charset=utf-8" \
  -d @/tmp/update.json
```

---

## Workflow

### Step 1: Read the source document
- Extract all questions, response options, instructions, and block structure
- Note bolding, italics, and visual formatting exactly as they appear
- Identify randomization requirements, page breaks, and conditional logic
- Logic instructions in the source document use `[brackets]` — e.g., `[randomize order of A and B]`, `[show only if Q3 = "Yes"]`, `[50/50 split across respondents]`. These are not question text; they are instructions for how to configure the survey.

### Step 2: Create and configure via API (~95% of configuration)
- Create survey shell, blocks, and questions with formatted HTML
- Set up embedded data declarations at top of flow
- Configure block randomizers and branch logic (including nested branches)
- Set **display logic** on questions via `displayLogic` parameter
- Set **skip logic** on questions via `skipLogic` parameter
- Set **choice/row randomization** via `randomization` parameter (including show N of M for Matrix)
- Set **slider configuration** (min/max/grid/snap) via `configuration` parameter
- Set **force response** via `validation` parameter: `{"Settings": {"ForceResponse": "ON", "ForceResponseType": "ON", "Type": "None"}}`
- Set **Matrix answers** (column headers) and **Slider answers** (scale labels) via `answers` and `answerOrder` parameters
- Set **export tags** via `dataExportTag` parameter
- Set **choice order** via `choiceOrder` parameter
- Set **consent flow** using nested branches in survey flow (decline → confirm → EndSurvey)
- Reference `references/formatting.md` for HTML templates and `references/question_types.md` for type-specific patterns

### Step 3: Generate manual-steps markdown (~2% of configuration)
Output `qualtrics_manual_steps.md` in the same folder as the source document, if any manual steps remain. Reference `references/manual_steps_template.md` for the required structure.

Features still requiring manual configuration:

| Feature | Reason |
|---------|--------|
| Page breaks within blocks | Not supported via API |
| End of Survey custom message text | Not fully configurable via API |
| Survey look & feel / theme | Not supported via API |

Note: TextEntry on choices and Slider Labels are now handled via direct API calls (see "MCP Tool Limitations" section) and verified by the review sub-agent in Step 4.

### Step 4: Review & auto-fix (sub-agent)

After all questions are created, launch a **review sub-agent** (`subagent_type: "general-purpose"`) that audits the survey against the source document and auto-fixes issues. Pass it the following prompt:

```
You are a Qualtrics survey QA reviewer. You have access to all Qualtrics MCP tools and direct API calls via curl.

## Inputs
- Source document: {path_to_source_doc}
- Survey ID: {survey_id}
- API token: {token} (ask user if not available — needed for direct API fixes)
- Datacenter: fra1.qualtrics.com

## Process

### 1. Read the source document
Read the source Word doc (use pandoc to extract markdown). Build a checklist of every question, its choices, formatting, logic instructions, and block structure.

### 2. Fetch all questions from Qualtrics
Use `list_questions` then `get_question` on each to get full details.

### 3. Compare and check for issues

Check EVERY question for ALL of the following:

| Check | What to look for |
|-------|-----------------|
| **Completeness** | Every question in the source doc exists in Qualtrics |
| **Question text** | Text matches source doc exactly (ignoring HTML tags) |
| **Choices** | All choices present, correct text, correct order |
| **Text entry** | Choices with "Sonstiges" / "Other" / "bitte angeben" have `TextEntry: true` |
| **Force response** | All non-descriptive questions have ForceResponse ON |
| **Export tags** | All questions have a dataExportTag set |
| **Export tag naming** | Tags follow snake_case, are descriptive, and consistent |
| **Slider labels** | Slider questions have `Labels` with min/max endpoint text |
| **Slider config** | Slider min/max/grid/snap matches source doc |
| **Matrix answers** | Matrix questions have answer columns set |
| **Display logic** | Conditional questions (marked with [brackets] in source) have displayLogic set |
| **Randomization** | Questions/choices marked for randomization in source have randomization set |
| **Formatting** | Bold text in source doc is wrapped in <strong> tags |
| **Question type** | Correct QuestionType and Selector for each question |
| **Timing questions** | Every substantive block has a Timing question with an intuitive export tag |

### 4. Auto-fix issues

For each issue found:
- **Use MCP tools first** (update_question) for fields the tool supports: choices (Display only), questionText, dataExportTag, validation, configuration, answers, displayLogic, randomization
- **Use direct curl calls** for fields MCP doesn't support: TextEntry on choices, Labels on Sliders
  - Always fetch current question state first via get_question
  - Write JSON to temp file with unicode escapes for special chars
  - Include ALL fields in the PUT body (it's a full replacement)
  - Always include DataExportTag (required by API)
- **Create missing questions** if any are absent from Qualtrics
- **Add missing Timing questions** to blocks that lack them

### 5. Report

After fixing, output a summary:
- Total questions checked
- Issues found and auto-fixed (with details)
- Issues that could NOT be auto-fixed (require manual action)
- Any discrepancies between source doc and survey that need PI review
```

### Step 5: Delivery checklist
- [ ] All questions created with correct type, selector, and choices
- [ ] Matrix questions have answers (columns) and Slider questions have answer labels
- [ ] HTML formatting matches source document bolding exactly
- [ ] Helper text separated with styled span (see formatting reference)
- [ ] Display logic set on conditional questions
- [ ] Choice/row randomization set where needed
- [ ] Force response set on all required questions
- [ ] Export tags set on all questions
- [ ] Survey flow has all randomizers, branches, and embedded data
- [ ] Consent flow terminates non-consenters correctly
- [ ] Timing questions in every substantive block
- [ ] "Sonstiges" / "Other" choices have text entry enabled
- [ ] Slider questions have min/max labels
- [ ] Review sub-agent ran and all auto-fixable issues resolved
- [ ] Survey is INACTIVE — do not activate until PI review

---

## References

Detailed templates and patterns are in `references/`:

- **`formatting.md`** — HTML templates for landing pages, regular questions, helper text, scenario labels, and bolding rules
- **`question_types.md`** — Qualtrics question type patterns (MC, Matrix, Slider, TE, DB) and piped text syntax
- **`manual_steps_template.md`** — Required structure for the manual-steps markdown output, including flow diagram format
- **`flow_patterns.md`** — Survey flow patterns: embedded data, randomizers, branching, debug mode
