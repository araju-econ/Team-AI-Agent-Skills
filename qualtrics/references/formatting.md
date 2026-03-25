# HTML Formatting Reference

## Landing/Consent Page

```html
<div style="max-width: 700px; margin: 0 auto; padding: 20px;">

<div style="display: flex; flex-direction: column; align-items: center; text-align: center; gap: 20px; margin-bottom: 30px;">
<div>
  <div style="font-size: 26px; font-weight: bold;">STUDY TITLE</div>
  <div style="font-size: 22px; font-weight: 500;">INSTITUTION</div>
  <div style="font-size: 18px;">Department</div>
  <div style="font-size: 18px;">Lead: Prof. Name</div>
</div>
</div>

<div style="text-align: left; line-height: 1.5;">
<!-- Body text with <br> for line breaks within paragraphs -->
<!-- Separate paragraphs with <br><br> -->

<!-- Legal/consent text: -->
<div style="font-size: 18px;"><em>Legal disclaimer text here.</em></div>

<!-- Contact info: -->
<div style="margin-top: 20px;">
Contact: <a href="mailto:email@example.com" style="color: #2b6cb0; text-decoration: underline;">email@example.com</a>
</div>
</div>

</div>
```

## Regular Question Text

- Use `<strong>` for key terms (name, track, age, outcome variable)
- Follow the source document's bolding EXACTLY — never add or remove bold
- Use `<br>` for line breaks within a question

## Helper/Definition Text

Separate helper text from the main question with a line break, then wrap in a styled span:

```html
...main question text ending with ?<br>
<span style="font-size: 0.9em;"><em>Helper text, definition, or instruction here.</em></span>
```

Examples of helper text:
- Definitions: "Unter Hochschulausbildung versteht man..."
- Inflation notes: "Bitte ignoriere die Inflation und gebe die Betraege in heutigen CHF an."
- Sub-instructions: "Waehle die zutreffende Option."

## Scenario Labels

When questions reference multiple scenarios:

```html
...main question text<br>
<span style="font-size: 0.9em;"><em>Helper text</em></span><br>
<br>
Szenario A: Name besucht <strong>das Gymnasium</strong>
```

Only bold the **track name** in scenario labels, not the entire line.

## Bolding Rules

1. **Always follow the source document exactly** — match bold in the doc to `<strong>` in Qualtrics
2. Common patterns to bold:
   - Vignette names (e.g., **David**, **Nicolas**)
   - Track names (e.g., **die Sekundarschule**, **das Gymnasium**)
   - Age references (e.g., **im Alter von 35 Jahren**)
   - Outcome variables (e.g., **monatliches Bruttoeinkommen**, **Hochschulausbildung**)
   - Work status qualifiers (e.g., **Vollzeit (100%)**, **arbeitet**)
3. Do NOT bold:
   - Entire scenario labels (only the track name within them)
   - Helper/definition text
   - Standard instruction text
