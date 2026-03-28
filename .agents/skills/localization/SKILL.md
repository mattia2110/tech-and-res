---
name: Localization Formatting
description: Guide for formatting localization files in Victoria 3.
---

# Text Formatting and Icons

## 1. Text Properties and Colors

To change the color or style of text in localization files, wrap the text with the following code format: `#<format_name> <text>#!`
For example: `#header Important Notice#!` or `#N Penalty#!`.

Common formatting tags found in `gui/textformatting.gui` include:
* **Headers and Titles**: `#h1 text#!`, `#header text#!`, `#title text#!`
* **Values and Statuses**:
  * `#P text#!` or `#positive_value text#!` (Green, Bold, Shadow) - Positive outcomes
  * `#N text#!` or `#negative_value text#!` (Red, Bold, Shadow) - Negative outcomes
  * `#Z text#!` or `#zero_value text#!` (White, Bold, Shadow) - Neutral outcomes
  * `#v text#!` or `#variable text#!` (White, Bold, Shadow) - Variable highlights
* **Special Colors**:
  * `#gold_value text#!` (Gold/Orange, Bold, Shadow)
  * `#blue_value text#!` (Blue, Bold, Shadow)
  * `#I text#!` or `#instruction text#!` (Light Green, Bold, Italic) - Instructions or tips
* **Typography**:
  * `#b text#!` (Bold, Shadow)
  * `#lore text#!` (Grey, Shadow)
  * `#todo text#!` (Pink, Italic)

*(Note: You can verify existing definitions or add new ones in `.gui` files containing `textformatting = { ... }` blocks).*

## 2. Text Icons

You can insert inline icons directly into your text using the syntax `@<texticon_name>!`.
The game replaces this with an image defined in the GUI files (such as `gui/texticons.gui` or custom mod files like `gui/ztr_texticons.gui`).

* **Syntax**: `@icon_name!`
* **Example**: `"Produces more @automobiles! #b Automobiles#!."`

Commonly used texticons in the game and mod:
* **Goods Icons**: `@coal!`, `@lead!`, `@furniture!`, `@automobiles!`, `@telephones!`, `@telecommunications!`, `@robotics!`
* **Indices (Tech&Res Mod)**: `@liberty_index!`, `@equality_index!`, `@individualist_index!`, `@autocracy_index!`
* **Warning / Alert**: `@warning!`
* **Top Bar / Resources**: `@money!`, `@prestige!`, `@inf!`, `@bur!`, `@aut!`

## 3. Line Breaks (Newlines)

To split text into multiple lines in UI (often used for event descriptions or journal entries), use the standard `\n` sequence.
* **Example**: `"This is a paragraph.\n\nThis is a new paragraph."`

# Localization Dynamic Coding

In Victoria 3 localization, brackets `[ ]` are used to insert dynamic data into text strings. Variables and text codes are evaluated based on context scopes and their associated properties.

## 1. Context Scopes

The starting point for a dynamic reference is usually a Context Scope or a Saved Scope:

* **`ROOT`**: Refers to the default root scope of the current context (e.g., the country or character the event is triggering for). Example: `[ROOT.GetCountry.GetName]`
* **`SCOPE`**: Used to evaluate dynamically saved scopes from a script (using `save_scope_as = ...`). You must specify the data type of the scope and its saved name. Normally, scope can be found inside the script file whose localization you are editing.

## 2. Saved Scope Data Types

When using `SCOPE`, you must tell the UI system what type of data the saved scope is:

* **`sCountry('scope_name')`**: References a saved country scope. 
  * *Usage:* `[SCOPE.sCountry('attacked_country').GetName]`
* **`sState('scope_name')`**: References a saved state/province scope.
  * *Usage:* `[SCOPE.sState('research_center_site').GetName]`
* **`sCharacter('scope_name')`**: References a saved character scope.
  * *Usage:* `[SCOPE.sCharacter('durand_scope').GetFullName]`
* **`sLaw('scope_name')`**: References a saved law scope.
  * *Usage:* `[SCOPE.sLaw('government_petition_law').GetName]`
* **`sCompany('scope_name')`**: References a saved company scope.
  * *Usage:* `[SCOPE.sCompany('relevant_company').GetName]`
* **`gsInterestGroup('scope_name')`**: References a saved global interest group scope (the `g` stands for global).
  * *Usage:* `[SCOPE.gsInterestGroup('union_ig_scope').GetName]`

*(Note: `s` stands for scope, `gs` stands for global scope).*

## 3. Properties and Methods

Once a scope is targeted, you must chain methods to extract the desired localization string:

* **`.GetName`**: Displays the name of the scope entity (e.g., "Great Britain", "Industrialists").
* **`.GetAdjective`**: Displays the adjective form of a country or scope (e.g., "British").
* **`.GetFullName` / `.GetLastName`**: Displays character names.
* **`.GetPrimaryRoleTitle`**: Displays a character's role title (e.g., "King", "President").
* **`.GetRuler`**: Gets the ruler of a country scope, which you can then chain. Example: `[SCOPE.sCountry('afg_rus_scope').GetRuler.GetFullName]`.

## 4. Game Concepts Tooltips

Use `Concept` to insert clickable tooltips for game mechanics anywhere in text:

* **Standard Concept**: `[Concept('concept_country','$concept_country$')]`
* **Custom Text Concept**: `[Concept('concept_law', 'Custom Text Here')]`

## 5. Scripted Variables Display

You can display numeric variables stored on a scope directly in text:

* **Syntax**: `[ROOT.GetCountry.MakeScope.Var('variable_name').GetValue[]`
* **Formatting**: Use `|0`, `|1`, `|2` at the end to dictate the number of decimal points. Example: `[ROOT.GetCountry.MakeScope.Var('liberty_index_var').GetValue|0]` displays an integer without decimals.