# Qualtrics Question Type Patterns

## Multiple Choice (MC) — Single Answer Vertical
- QuestionType: `MC`, Selector: `SAVR`
- Standard radio-button list

## Multiple Choice (MC) — Dropdown
- QuestionType: `MC`, Selector: `DL`
- Use for long lists (countries, cantons, CHF amounts, percentages)

## Multiple Choice (MC) — Multi-Select
- QuestionType: `MC`, Selector: `MAVR`
- Checkboxes; respondents can select multiple options

## Matrix / Likert
- QuestionType: `Matrix`, Selector: `Likert`, SubSelector: `SingleAnswer`
- Rows = statements/items, Columns = scale points
- Row randomization and "show N of M" must be configured manually in UI

## Slider
- QuestionType: `Slider`, Selector: `HSLIDER`
- Min, max, labels, grid lines, and snap-to-grid must be configured manually in the Qualtrics UI

## Text Entry (TE)
- Selector: `SL` (single line), `ML` (multi-line), or `FORM` (form with multiple fields)

## Descriptive Text (DB/TB)
- QuestionType: `DB`, Selector: `TB`
- Display-only; no response collected
- Use for intro screens, instructions, and informational text

---

## Piped Text Syntax

Embed dynamic values using piped text:

### Embedded data
```
${e://Field/variable_name}
```

### Question responses
```
${q://QID##/SelectedChoicesRecode}
${q://QID##/ChoiceGroup/SelectedChoices}
```

### Examples
```
${e://Field/descriptive_sek_name}    -- embedded data variable
${q://QID62/SelectedChoicesRecode}    -- recode value of selected choice
```
