# Survey Flow Patterns

## Embedded Data Declaration

Place at the very top of the flow. Declare ALL randomization and piped-text variables with empty values:

```
Set Embedded Data:
  vignette_order    = (empty)
  ai_placement      = (empty)
  condition_name    = (empty)
  debug_mode        = 0
```

## Block Randomizer

For between-subject conditions, use a Randomizer element that picks 1 of N branches evenly:

```
Randomizer (pick 1, evenly):
  Set Embedded Data: condition = A
  Set Embedded Data: condition = B
```

Each branch sets the embedded data variable to its assigned value. The randomizer ensures even distribution across respondents.

## Branching

For conditional display of blocks based on embedded data or question responses:

```
Branch: If embedded_data = value
  Show Block: [block name]
```

Branches are mutually exclusive — only the matching branch executes per respondent.

## Debug Mode Pattern

A debug block at the top of the flow allows researchers to manually override all randomization during testing:

```
Group: "Debug Mode"
  Branch: If debug_mode = 1
    Show Block: Debug Questions (manual selectors for each variable)
    Set Embedded Data from debug question responses

  Branch: If debug_mode != 1
    Randomizer: condition_1 (pick 1, evenly)
    Randomizer: condition_2 (pick 1, evenly)
    ...
```

To enable: change `debug_mode = 0` to `debug_mode = 1` in the top embedded data element.
Before going live: set `debug_mode = 0`.

## Consent Flow

Standard two-step consent with early termination:

```
Show Block: Consent (intro + consent question)

Branch: If "do not consent" selected
  Show Block: Consent Confirm (are you sure?)
  Branch: If "confirmed no" selected
    End of Survey (custom message)
```

## Vignette Order Randomization

For within-subject designs where block order varies:

```
Branch: If vignette_order = 1
  Show Block: Section A
  Show Block: Section B

Branch: If vignette_order = 2
  Show Block: Section B
  Show Block: Section A
```

Each block appears in both branches but in different order. Only one branch executes per respondent.
