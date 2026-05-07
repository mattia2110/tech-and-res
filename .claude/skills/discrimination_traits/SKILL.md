---
description: Guide for Victoria 3 discrimination trait definitions. Use when creating, editing, or reviewing files in common/discrimination_traits, especially when adding or overriding heritage, language, or tradition traits, tracing their use from common/cultures or common/religions, or checking that trait_group links match common/discrimination_trait_groups.
name: Discrimination Traits
---

# Discrimination Traits

Use this skill for Victoria 3 `discrimination_traits`.

Do not use it for:
- discrimination trait groups in `common/discrimination_trait_groups`
- localization by itself
- culture or religion edits that do not change discrimination trait keys

## File Location

Write modded trait definitions in:
`common/discrimination_traits/*.txt`

Use vanilla references from:
`C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\discrimination_traits\*.txt`

Relevant neighboring folders:
- `common/discrimination_trait_groups`
- `common/cultures`
- `common/religions`
- `localization/english`

Vanilla currently splits discrimination traits by domain:
- `00_cultural_heritages.txt`
- `01_languages.txt`
- `02_traditions.txt`
- `03_religious_heritages.txt`

Match that structure when choosing file placement in the mod. Do not drop unrelated traits into an arbitrary catch-all file.

## Workflow

1. Identify whether the task is adding a new trait, overriding a vanilla trait, or fixing broken references.
2. Inspect the nearest vanilla or modded definition with the same role before editing.
3. Place the trait in the matching domain file:
   `heritage_*` cultural traits in cultural heritages, religious `heritage_*` traits in religious heritages, `language_*` traits in languages, and `tradition_*` traits in traditions.
4. Check the consuming data before finalizing:
   cultures usually reference `heritage = ...`, `language = ...`, and `traditions = { ... }`;
   religions reference `heritage = ...`.
5. If the trait uses `trait_group`, verify the group exists in `common/discrimination_trait_groups` and has the same `type`.
6. Keep alphabetical order if the surrounding file uses it. Vanilla does.

## Structure

Discrimination traits declare a key, a `type`, and sometimes a `trait_group`.

```pdx
heritage_example = {
	type = heritage
	trait_group = heritage_group_example
}

language_example = {
	type = language
	trait_group = language_group_example
}

tradition_example = {
	type = tradition
}
```

Valid types are:
- `heritage`
- `language`
- `tradition`

## Type Rules

- `heritage_*` traits must use `type = heritage`.
- `language_*` traits must use `type = language`.
- `tradition_*` traits must use `type = tradition`.
- `heritage` and `language` traits need a `trait_group = ...`.
- `tradition` traits must not define `trait_group`.
- Religious heritage traits still use `type = heritage`; they are separated by file placement, not by schema.

Examples:
- `heritage_abyssinian` uses `trait_group = heritage_group_african`
- `language_akan` uses `trait_group = language_group_kwa`
- `tradition_rumelian` has no `trait_group`
- `heritage_christian` uses `trait_group = heritage_group_abrahamic`

## Cross-File Relationships

These traits are normally referenced from Victoria 3 `common/` content:

- `common/cultures/*.txt`
  - `heritage = heritage_*`
  - `language = language_*`
  - `traditions = { tradition_* }`
- `common/religions/*.txt`
  - `heritage = heritage_*`
- `common/discrimination_trait_groups/*.txt`
  - provides the `heritage_group_*` and `language_group_*` keys used by eligible traits

If you add or rename a discrimination trait, search these folders for downstream references before finishing.

## Mod Patterns

New trait:

```pdx
language_example = {
	type = language
	trait_group = language_group_example
}
```

Vanilla override:

```pdx
REPLACE:language_example = {
	type = language
	trait_group = language_group_example
}
```

Use `REPLACE:` only when intentionally overriding an existing vanilla or previously defined key.

## Validation Checklist

Before finishing, verify all of the following:

- The file is under `common/discrimination_traits`.
- The key prefix matches the `type`.
- `heritage` and `language` traits define `trait_group`; `tradition` traits do not.
- Every `trait_group = ...` points to a real group in `common/discrimination_trait_groups` with the same `type`.
- File placement matches the trait domain instead of mixing unrelated categories.
- File ordering matches the surrounding convention.
- Any changed key is updated in `common/cultures` or `common/religions` as needed.

Useful searches:

```powershell
rg -n "heritage_example|language_example|tradition_example" common
rg -n "trait_group = heritage_group_example|trait_group = language_group_example" common/discrimination_traits
rg -n "heritage = heritage_example|language = language_example|traditions = \\{[^\\n}]*tradition_example" common/cultures common/religions
```

## Template

```pdx
# Keep alphabetical order if the file already does.
heritage_example = {
	type = heritage
	trait_group = heritage_group_example
}
```

## Editing Guidance

- Prefer adding traits next to the closest vanilla analog instead of inventing a new layout.
- Keep names stable and descriptive; cultures and religions depend on exact keys.
- When the user asks for a new culture or religion family, inspect both the consuming file and `common/discrimination_traits`; editing only one side is often incomplete.
- If the task introduces a new `heritage_group_*` or `language_group_*`, update `common/discrimination_trait_groups` as part of the same change or call out the missing dependency explicitly.
