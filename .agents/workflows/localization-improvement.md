---
description: This workflow is used to improve Localization for Victoria 3 Videogame
---

# Localization Improvement Workflow

This workflow provides a comprehensive, step-by-step guide to reviewing, formatting, and improving localization files (`.yml` and trigger localization) in Victoria 3 mods. 

## Phase 1: Preparation and Context Gathering
1. **Identify the Target File**: Locate the `.yml` file in `localization/english/` that needs improvement.
2. **Review Context**: Check where these localization keys are used (e.g., events, journal entries, scripted buttons) to understand the required dynamic scopes (`[ROOT]`, `[SCOPE]`) and the intended tone.
3. **Check File Encoding**: Verify that the `.yml` file is saved with **UTF-8-BOM** encoding. This is an absolute requirement for Victoria 3 localization files.

## Phase 2: Applying Text Formatting and Colors
4. **Highlight Important Terms**: Wrap key gameplay terms, outcomes, and statuses in appropriate formatting tags:
   - Positive/Green: `#P text#!` or `#positive_value text#!`
   - Negative/Red: `#N text#!` or `#negative_value text#!`
   - Neutral/White: `#Z text#!` or `#zero_value text#!`
   - Variables: `#v text#!` or `#variable text#!`
   - Special Colors: `#gold_value text#!`, `#blue_value text#!`
   - Headers/Titles: `#header text#!`, `#title text#!`
   - Instructions: `#I text#!`
   - *Example*: `"If successful, it will cost us #n 1.5M#! of Treasury."`
5. **Insert Text Icons**: Replace generic words with text icons where applicable to improve UI readability:
   - Goods: `@coal!`, `@furniture!`, `@automobiles!`, etc.
   - Indices/Mod mechanics: `@liberty_index!`, `@equality_index!`, etc.
   - UI elements: `@money!`, `@prestige!`, `@warning!`
   - *Example*: `"The amount of @robotics! Robotics consumed by buildings"`
6. **Improve Paragraph Structure**: Ensure proper spacing for long texts (like event descriptions) by using the `\n\n` sequence for paragraph breaks.

## Phase 3: Implementing Dynamic Data (Scopes and Variables)
7. **Verify Dynamic Context Scopes**: Ensure that context scopes are used correctly. For example:
   - Default Context: `[ROOT.GetCountry.GetName]` or `[ROOT.GetCountry.GetAdjective]`
   - Saved Country: `[SCOPE.sCountry('country_scope').GetName]`
   - Saved State: `[SCOPE.sState('state_scope').GetCityHubName]`
   - Saved Character: `[SCOPE.sCharacter('char_scope').GetFullName]`
   - Saved Company: `[SCOPE.sCompany('company_scope').GetName]`
   - Saved Law: `[SCOPE.sLaw('law_scope').GetName]`
   - Global IG: `[SCOPE.gsInterestGroup('ig_scope').GetName]`
   - *Example*: `"[SCOPE.sCompany('relevant_company').GetName] will be able to produce @automobiles!"`
8. **Format Numeric Variables**: When displaying scripted variables, check formatting for decimals:
   - *Example*: `[ROOT.GetCountry.MakeScope.Var('variable_name').GetValue|0]` (the `|0` enforces an integer with no decimals).
9. **Add Game Concepts**: Add tooltips for important game mechanics using `[Concept('concept_key', '$concept_key$')]` or with custom text: `[Concept('concept_law', 'Custom Text')]`.

## Phase 4: Reviewing Trigger Localization (If Applicable)
10. **Check Trigger Contexts**: If editing trigger localization keys (`common/trigger_localization/`), ensure all necessary forms (`global`, `first`, `third`, `none`, and `_not` variants) are defined and read naturally.
11. **Verify Comparator Wording**: Make sure comparator phrasing (`_greater_or_equal`, `_less_than`, etc.) logically matches the underlying boolean check.
12. **Cross-Reference Keys**: Verify that all localization string keys referenced in the trigger localization `.txt` have corresponding entries in the actual `.yml` dictionary.

## Phase 5: Final Validation
13. **Proofread Strings**: Read the modified text from the perspective of a player seeing the rendered tooltips. Ensure noun/adjective agreements and "person" wording make sense across different contexts.
14. **Save and Validate**: Save the file without change .yml UTF used and check for any syntax errors like missing brackets or unclosed formatting tags `#!`.