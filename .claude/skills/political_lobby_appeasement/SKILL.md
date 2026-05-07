---
description: Guide for Victoria 3 political lobby appeasement factor definitions. Use when creating, editing, or reviewing files in common/political_lobby_appeasement, especially when diplomacy, treaty, event, or scripted effects need a new appeasement factor key.
name: Political Lobby Appeasement
---

# Political Lobby Appeasement

Use this skill for Victoria 3 files in `common/political_lobby_appeasement/*.txt`.

Do not use it for:
- political lobby type definitions
- the effect that applies appeasement amounts
- general opinion modifiers or diplomatic catalysts

## File Location

Write modded appeasement factor definitions in:
- `common/political_lobby_appeasement/*.txt`

Inspect the local mod first:
- `common/political_lobby_appeasement/*.txt`
- callers in `common/diplomatic_actions/*.txt`, `common/treaty_articles/*.txt`, and `common/scripted_effects/*.txt`

Then compare against vanilla references:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\political_lobby_appeasement\political_lobby_appeasement.md`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\political_lobby_appeasement\00_political_lobby_appeasement.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\scripted_effects\00_lobby_effects.txt`

## What These Definitions Do

An appeasement factor key is a lightweight metadata definition used by political lobbies.

It does not define the appeasement amount by itself. Instead, other content passes the amount when calling effects such as:
- `change_appeasement`
- `add_lobby_appeasement_from_diplomacy_bidirectional`
- `add_lobby_appeasement_from_diplomacy_unidirectional`
- `add_overlord_lobby_appeasement_from_diplomacy`

The appeasement factor definition mainly controls:
- how long the reason remains visible in the tooltip
- whether the factor is globally usable or must be explicitly allowed by a lobby type

## Workflow

1. Inspect the effect or diplomatic content that will apply the factor before adding a new key.
2. Reuse an existing factor if the gameplay meaning is the same.
3. Add a new factor only when the tooltip reason or usability category truly differs.
4. Decide whether the factor should be `is_always_usable = yes` or be gated through specific lobby definitions.
5. Search all callers after editing so the factor name matches exactly.

## Common Structure

```pdx
appeasement_example = {
	duration_to_show = { months = 12 }
	is_always_usable = yes
}
```

## Field Guidance

- `duration_to_show`: How long the appeasement reason remains visible in the tooltip.
- `is_always_usable`: Whether all lobby types can use the factor without listing it in `appeasement_factors_pro` or `appeasement_factors_anti`.

## Vanilla Patterns

Vanilla definitions are intentionally minimal. Most shipped keys only set:
- `duration_to_show = { months = 60 }`
- optional `is_always_usable = yes`

Common always-usable vanilla examples include:
- `appeasement_baseline_decay`
- `appeasement_lobby_request_ignored`
- `appeasement_lobby_request_accepted`
- `appeasement_lobby_request_failed`

Most diplomacy-specific factors, such as alliance or embargo changes, are not always usable and must be listed by compatible lobby types.

## Mod Integration

This mod already applies existing appeasement factors from diplomacy content, for example:
- `appeasement_embargo_declared`
- `appeasement_defensive_pact_formed`

Those calls appear in:
- `common/diplomatic_actions/*.txt`
- `common/treaty_articles/*.txt`

If you introduce a new factor key, check whether a matching lobby definition must also be updated to allow it.

## Editing Guidance

- Keep factor names event-focused and reusable.
- Prefer a new factor for a new tooltip reason, not for every distinct numeric amount.
- Use `is_always_usable = yes` only when the factor should make sense across many lobby types.
- Do not expect this file to solve balance by itself; the signed amount still comes from the caller.

## Review Checklist

- The factor key is defined in `common/political_lobby_appeasement/`.
- `duration_to_show` matches the intended persistence of the tooltip reason.
- `is_always_usable` is present only when broad lobby access is intended.
- Every caller uses the exact same factor key spelling.
- If the factor is not always usable, compatible lobby definitions list it explicitly.
