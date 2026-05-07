---
name: Game Concepts
description: Guide for Victoria 3 game concept definitions. Use when creating, editing, or reviewing files in common/game_concepts, especially when adding clickable concept keys, assigning icon textures, syncing concept definitions with localization, or making sure concepts referenced in events, journal entries, and custom localization are actually registered in script.
---

# Game Concepts

## Core Rule

When adding a new concept, do not stop at localization.

Every Victoria 3 concept needs both:

1. A script definition in `common/game_concepts/*.txt`
2. Localization entries for `concept_key` and `concept_key_desc`

If the localization exists but the script definition is missing, the concept will not be properly registered in game.

## Definition Pattern

Use this structure in the appropriate `common/game_concepts` file:

```txt
concept_example = {
	texture = "gfx/interface/icons/path/example.dds"
	is_loading_tip = yes
}
```

Guidelines:

- Reuse an existing texture when several concepts belong to the same subject or mechanic family.
- Keep related concepts grouped in the same domain file, such as `common/game_concepts/ztr_un_concepts.txt`.
- Prefer existing icon folders and naming patterns instead of inventing a new texture path without need.

## Recommended Workflow

1. Search for the concept keys already referenced in localization or scripted text.
2. Add missing concept definitions in the matching `common/game_concepts` domain file.
3. Add or verify `concept_key` and `concept_key_desc` localization.
4. Check whether the concept is shown together with a texticon and keep the icon/concept pairing visually consistent.
5. Verify that every referenced concept key has both a script definition and localization.

## UN Concepts

For United Nations content, check these files together:

- `common/game_concepts/ztr_un_concepts.txt`
- `localization/english/ztr_concepts_l_english.yml`
- `gui/ztr_texticons.gui`
- `common/customizable_localization/ztr_un_governance.txt`
- `localization/english/ztr_journal_entries_l_english.yml`
- `localization/english/ztr_events_l_english.yml`

For UN law families:

- Use the subject icon for the subject concept itself.
- Reuse that same image for stance concepts and country-law concepts within the same subject.
- Keep institution concepts on their institution icon.

## Localization Usage

Use concepts in localization with:

```txt
[Concept('concept_example','$concept_example$')]
```

For custom visible text:

```txt
[Concept('concept_example','Custom Text')]
```

If the UI uses a texticon too, pair them deliberately:

```txt
@example_icon! [Concept('concept_example','$concept_example$')]
```

## Validation

After editing concepts:

- Search for `concept_*` references in the edited domain.
- Confirm every non-`_desc` localization key has a matching script definition.
- Confirm the texture paths exist.
- If working on a concept family like UN concepts, compare counts between concept loc keys and concept definitions as a quick sanity check.

Useful checks:

```powershell
rg "concept_un_" common/game_concepts localization/english/ztr_concepts_l_english.yml
rg "gfx/interface/icons/un_law_icons" common/game_concepts/ztr_un_concepts.txt
```

## Editing Notes

- Append new concept localization at the end of the localization file unless the file already uses another clear structure.
- Keep concept names stable once referenced in localization or scripted content.
- When adding concepts for a new feature, update the concept file in the same task so the registration is never forgotten.
