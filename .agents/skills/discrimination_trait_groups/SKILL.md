---
description: Guide for Victoria 3 discrimination trait group definitions. Use when creating, editing, or reviewing files in common/discrimination_trait_groups, especially when adding new heritage or language groups, overriding vanilla groups, or checking that discrimination traits in common/discrimination_traits point to valid groups.
name: Discrimination Trait Groups
---

# Discrimination Trait Groups

Use this skill for Victoria 3 `discrimination_trait_groups`.

Do not use it for:
- individual discrimination traits in `common/discrimination_traits`
- pop cultures, religions, or localization by themselves

## File Location

Write modded trait group definitions in:
`common/discrimination_trait_groups/*.txt`

Use vanilla references from:
`C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\discrimination_trait_groups\*.txt`

Related files usually include:
- `common/discrimination_traits/*.txt`
- `common/cultures/*.txt`
- `common/religions/*.txt`
- `localization/english/*.yml`

Vanilla currently splits groups by domain:
- `00_cultural_heritage_groups.txt`
- `01_language_groups.txt`
- `02_religious_heritage_groups.txt`

Keep that structure in mind when choosing file placement in the mod.

## Workflow

1. Check whether the task is adding a new group, overriding a vanilla group, or correcting broken references.
2. Inspect the nearest vanilla or modded group with the same conceptual role before editing.
3. Place the new block in the most relevant `common/discrimination_trait_groups` file instead of creating an arbitrary catch-all file.
4. Match the key prefix to the type:
   `heritage_group_*` for `type = heritage`
   `language_group_*` for `type = language`
5. Keep each file alphabetically sorted if the surrounding file follows that pattern.
6. Recheck all linked traits in `common/discrimination_traits` before finishing.

## Structure

Trait groups are simple definitions. They only declare a group key and its type.

```pdx
heritage_group_example = {
	type = heritage
}

language_group_example = {
	type = language
}
```

Valid types here are:
- `heritage`
- `language`

Do not define `tradition` groups. Tradition traits do not use `trait_group`.

## Type Semantics

- `heritage_group_*` groups are used by `heritage_*` traits in `common/discrimination_traits`.
- `language_group_*` groups are used by `language_*` traits in `common/discrimination_traits`.
- Religious heritage groups still use `type = heritage`; they are not a separate schema.

If a discrimination trait uses `trait_group`, its `type` must match the group's `type`.

Examples:
- `heritage_abyssinian` can point to `heritage_group_african`
- `language_gbe` can point to `language_group_volta_niger`
- `tradition_rumelian` should not point to any trait group

## Mod Patterns

New group:

```pdx
language_group_example = {
	type = language
}
```

Vanilla override:

```pdx
REPLACE:language_group_example = {
	type = language
}
```

Use `REPLACE:` only when intentionally overriding an existing vanilla or previously defined key.

## Validation Checklist

Before finishing, verify all of the following:

- The file is under `common/discrimination_trait_groups`.
- The key prefix matches the type.
- The type is only `heritage` or `language`.
- The file ordering matches the surrounding convention.
- Every new or changed `trait_group = ...` reference in `common/discrimination_traits` points to a real group.
- No `tradition_*` trait is assigned a trait group.

Useful searches:

```powershell
rg -n "trait_group = heritage_group_example|trait_group = language_group_example" common/discrimination_traits
rg -n "heritage_group_example|language_group_example" common
```

## Template

```pdx
# Keep alphabetical order if the file already does.
language_group_example = {
	type = language
}
```

## Editing Guidance

- Prefer adding the group next to similar vanilla groups instead of inventing a new placement scheme.
- Keep naming stable and descriptive; downstream traits depend on exact keys.
- If you introduce a new group, check whether the corresponding cultures or religions also need new discrimination traits.
- If the user asks for a new heritage or language family, inspect both `common/discrimination_trait_groups` and `common/discrimination_traits`; editing one without the other is often incomplete.
