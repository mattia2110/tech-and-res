---
description: Guide for Victoria 3 objective subgoal categories. Use when creating, editing, or reviewing files in common/objective_subgoal_categories, or when objective_subgoals need a new category key such as sgcat_*.
name: Categories
---

# Objective Subgoal Categories

Use this skill for Victoria 3 `objective_subgoal_categories`, not for every generic `category = ...` field elsewhere in `common\`.

## File Location

Write modded category definitions in:
`common/objective_subgoal_categories/*.txt`

Use the base game for reference at:
`C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\objective_subgoal_categories\00_categories.txt`

Related files usually live in:
- `common/objective_subgoals/*.txt`
- `common/objectives/*.txt`

## Workflow

1. Check whether the requested `sgcat_*` key already exists in the mod.
2. If not, inspect the vanilla file above for naming and formatting patterns.
3. Add or update the category in `common/objective_subgoal_categories/*.txt`.
4. Verify every `objective_subgoal` that uses the category sets `category = sgcat_*` consistently.
5. If the category changes progression behavior, review the affected `objective_subgoals` for `is_repeatable`, unlocking chains, and journal entry flow.

## Structure

```pdx
sgcat_key = {
	is_exclusive = yes/no
	is_repeatable = yes/no
}
```

## Fields

- `is_exclusive`: If `yes`, only one active subgoal from that category can run at a time.
- `is_repeatable`: Default repeatability for subgoals in that category. Individual subgoals can still override this with their own `is_repeatable`.

If a field is omitted, follow vanilla expectations and set it explicitly unless there is a reason not to. The base game writes both fields out in full.

## Interaction With Objective Subgoals

Category definitions do very little on their own. Their behavior only matters once referenced by entries in `common/objective_subgoals/*.txt`, for example:

```pdx
sg_example = {
	category = sgcat_example
	is_repeatable = no

	trigger = { }

	on_start = {
		add_journal_entry = {
			type = je_example
			objective_subgoal = sg_example
		}
	}
}
```

When editing categories, always verify:
- The `sgcat_*` key exists before any subgoal references it.
- Subgoal-level `is_repeatable` values intentionally override or inherit the category default.
- `is_exclusive = yes` does not accidentally block a progression chain that expects multiple active subgoals.

## Template

```pdx
sgcat_example = {
	is_exclusive = no
	is_repeatable = no
}
```

## Vanilla Pattern

The base game currently keeps category files minimal. A representative pattern is:

```pdx
sgcat_tutorial = {
	is_exclusive = no
	is_repeatable = no
}
```

Follow that style unless the mod has an established reason to diverge.

## Editing Guidance

- Prefer `sgcat_*` names that describe the objective track, not a single journal entry.
- Keep category keys stable once subgoals depend on them; renames require updating every referencing `objective_subgoal`.
- If you add a brand new category, check whether supporting objectives, journal entries, or scripted content also need to be introduced.
- Do not confuse this folder with unrelated uses of `category` in technologies, goods, buildings, laws, or companies.
