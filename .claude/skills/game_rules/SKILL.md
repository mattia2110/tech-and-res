---
description: Guide for Victoria 3 game rule definitions. Use when creating, editing, or reviewing files in common/game_rules, adding new rules or settings, overriding vanilla rules, or checking how game rule flags interact with localization, scripted content, and production method flags.
name: Game Rules
---

# Game Rules

Use this skill for campaign setup rules stored in `common/game_rules`.

## File Location

Write modded rules in:
`common/game_rules/*.txt`

Use vanilla references from:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\game_rules\00_game_rules.txt`

Related files often include:
- `localization/*game_rules*.yml`
- `common/scripted_triggers/` and `common/scripted_effects/` when rule flags are consumed by script
- `events/`, `common/journal_entries/`, or other game content that checks settings with `has_game_rule = setting_key`

In this mod, existing rules live in:
`common/game_rules/ztr_game_rules.txt`

## Workflow

1. Check whether the task is adding a new rule, extending an existing rule, or replacing a vanilla one.
2. Inspect nearby modded and vanilla rules before choosing keys and setting names.
3. Pick a stable `default` setting.
4. Add only the flags or modifiers the setting actually needs.
5. Add matching localization for the rule and every setting.
6. Verify the rest of the mod checks the intended setting key, not just the rule key.

## Structure

```victoria3
game_rule_key = {
	default = setting_default_key

	setting_default_key = {
	}

	setting_alt_key = {
		flag = some_flag
		apply_modifier = player:some_modifier
	}
}
```

## Core Fields

- Top-level key: The game rule identifier. Localization normally uses `rule_<top_level_key>`.
- `default`: Which setting is preselected in campaign setup.
- `setting_name = { ... }`: One selectable option for that rule.
- `flag`: Adds one or more game-rule flags for the selected setting.
- `apply_modifier`: Applies a modifier by category. The built-in categories documented by vanilla are `player`, `ai`, and `all`.

## Localization Pattern

For a rule like:

```victoria3
ztr_historical_content = {
	default = allow_ztr_historical_content
	allow_ztr_historical_content = { }
	no_ztr_historical_content = { flag = no_ztr_historical_content }
}
```

Add:
- `rule_ztr_historical_content`
- `setting_allow_ztr_historical_content`
- `setting_allow_ztr_historical_content_desc`
- `setting_no_ztr_historical_content`
- `setting_no_ztr_historical_content_desc`

## Flag Semantics

There are two broad flag categories:

- Engine-recognized flags, such as `blocks_achievements`, `no_subject_flags`, `no_subject_map_color`, `disable_<production_method_key>`, and `force_<production_method_key>`
- Mod-defined flags, such as this mod's `no_ztr_decolonization_content`, which only matter if other content checks for them

In this mod, gameplay content commonly checks settings directly with:

```victoria3
has_game_rule = no_ztr_decolonization_content
```

Do not assume a `flag` does anything by itself unless the engine or modded script consumes it.

## Editing Guidance

- Keep empty setting blocks when a setting is just the neutral or enabled baseline.
- Use multiple `flag = ...` lines when a setting must toggle several hardcoded behaviors.
- Use explicit, readable setting keys; they are usually referenced elsewhere verbatim.
- If a rule controls production method availability, verify every `disable_` or `force_` target matches a real production method key.
- When overriding vanilla, replace only the intended rule and preserve unrelated settings.

## Template

```victoria3
ztr_example_content = {
	default = allow_ztr_example_content

	allow_ztr_example_content = {
	}

	limited_ztr_example_content = {
		flag = limited_ztr_example_content
	}

	no_ztr_example_content = {
		flag = no_ztr_example_content
	}
}
```

## Review Checklist

- The file is in `common/game_rules/`.
- `default` points at a setting defined in the same rule.
- Every setting key has matching localization and description text.
- Any `apply_modifier` target exists and uses a valid category.
- Any `flag` is either engine-recognized or consumed elsewhere in mod content.
- Any content checks the intended setting key with `has_game_rule = ...`.
