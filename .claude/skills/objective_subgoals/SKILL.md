---
description: Guide for Victoria 3 objective subgoal definitions. Use when creating, editing, or reviewing files in common/objective_subgoals, especially when wiring objective progression into categories, journal entries, and neighboring objective content.
name: Objective Subgoals
---

# Objective Subgoals

Use this skill for Victoria 3 files in `common/objective_subgoals/*.txt`.

Do not use it for:
- `common/objective_subgoal_categories/*.txt` edits with no subgoal definition changes
- `common/journal_entries/*.txt` edits that do not touch objective progression
- direct `add_journal_entry` event or decision work when no `objective_subgoal` key is involved

Pair it with:
- `Categories` when a new `sgcat_*` key is needed
- `Journal Entries` when the subgoal starts or depends on a JE

## File Location

Write modded subgoal definitions in:
- `common/objective_subgoals/*.txt`

Use vanilla as the schema and pattern source:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\objective_subgoals\subgoals.md`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\objective_subgoals\*.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\objective_subgoal_categories\*.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\objectives\*.txt`

In this repo there is currently no local `common/objective_subgoals/` folder, so new subgoal work will usually also require checking the surrounding local objective flow in:
- `common/journal_entries/*.txt`
- `common/journal_entry_groups/*.txt`
- `common/decisions/*.txt`
- `events/**/*.txt`
- `localization/english/*.yml`

## What These Definitions Do

Objective subgoals are progression nodes inside the player-objective system. They do not usually carry the whole feature by themselves. In practice, a subgoal definition mostly decides:
- which category queue it belongs to
- which earlier subgoals unlock or block it
- whether the country is currently eligible to roll it
- how often it can repeat
- what happens when it starts, usually `add_journal_entry = { ... objective_subgoal = <key> }`

That means subgoals sit in a wider `common/` chain:
- `common/objective_subgoal_categories/` defines queue behavior such as exclusivity and default repeatability
- `common/objectives/` decides which objective track can use the subgoal pool
- `common/journal_entries/` usually provides the actual player-facing task
- `events/` and `common/decisions/` often start or continue the same content without using subgoals at all

## Practical Rule

Do not write a subgoal as if it were isolated content. First decide whether the feature actually belongs in the objective system.

In this repo, most progression currently goes through journal entries, events, and decisions directly rather than through local `objective_subgoals`. If you add a new subgoal here, check whether you are:
- extending the vanilla player-objective system on purpose
- duplicating an existing JE flow that should stay event-driven instead
- missing a needed category, objective entry, or localization key

## Workflow

1. Check whether the `sg_*` key already exists in the mod or vanilla.
2. Identify the owning objective track and the `sgcat_*` category it should use.
3. Inspect the linked category and objective definitions before writing the subgoal.
4. Map the progression chain first: which prior subgoals unlock it, and which completed branches should block it.
5. Write the `trigger` so it answers "can this subgoal start now?" rather than trying to perform the task itself.
6. Put player-facing work in `on_start`, usually by adding a JE and passing any needed saved scope as a target.
7. Re-check the resulting chain in `common/objectives`, `common/journal_entries`, events, decisions, and localization.

## Common Structure

```pdx
sg_example = {
	category = sgcat_example

	unlocking_subgoals_all = { }
	unlocking_subgoals_any = { }
	blocking_subgoals_none_of = { }
	blocking_subgoals_not_all = { }

	trigger = {
		always = yes
	}

	chance = {
		value = 1
	}

	on_start = {
		add_journal_entry = {
			type = je_example
			objective_subgoal = sg_example
		}
	}

	is_repeatable = no
}
```

## Field Guidance

- `category`: Required in practice. Must point at a valid `sgcat_*` key.
- `unlocking_subgoals_all`: All listed subgoals must already be resolved before this one can trigger.
- `unlocking_subgoals_any`: At least one listed subgoal must already be resolved.
- `blocking_subgoals_none_of`: None of the listed subgoals may already be resolved.
- `blocking_subgoals_not_all`: At least one listed subgoal must still be unresolved.
- `trigger`: Live eligibility check. Keep it focused on whether the country can receive the subgoal now.
- `chance`: Daily roll chance. If omitted, the effective default is `1`, meaning fully eligible once triggered.
- `on_start`: Startup payload. Vanilla usually uses this to create the real JE and save any needed targets first.
- `is_repeatable`: Optional override of the category default. If `no`, failure also counts as resolving the subgoal for future unlock and block checks.

## Vanilla Pattern

Current vanilla patterns are straightforward:
- most subgoals are thin wrappers around `add_journal_entry`
- branching objective paths are handled with the four unlock and block lists
- `on_start` often saves a target scope before creating the JE
- many later subgoals set `is_repeatable = no` explicitly even when the category already suggests that behavior

Representative vanilla pattern:

```pdx
sg_example = {
	category = sgcat_example
	unlocking_subgoals_all = { sg_previous }

	trigger = { }

	on_start = {
		add_journal_entry = {
			type = je_example
			objective_subgoal = sg_example
		}
	}
}
```

## Editing Guidance

- Keep subgoal keys stable once objectives, JEs, or localization refer to them.
- Prefer `sg_*` names that describe the progression step, not the implementation detail.
- Avoid burying large gameplay effects directly in `on_start`; if the subgoal is really launching a feature, that feature probably belongs in a JE, event, or scripted effect.
- If the subgoal needs a target, save the scope before `add_journal_entry` and pass it explicitly.
- When adding the first local subgoal content to this repo, decide whether to mirror vanilla folder structure cleanly instead of scattering objective logic across unrelated files.

## Review Checklist

- The definition lives in `common/objective_subgoals/`.
- `category = sgcat_*` points at a real category key.
- Unlocking and blocking chains do not deadlock or bypass intended branches.
- `trigger` represents eligibility, not completion.
- `on_start` adds or starts the intended JE or scripted content.
- Every referenced JE, category, objective key, and localization key exists.
- The change does not accidentally duplicate an existing event-driven JE flow already present in `common/decisions` or `events`.
