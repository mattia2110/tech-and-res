---
description: Guide for Victoria 3 geographic region definitions. Use when creating, editing, or reviewing files in common/geographic_regions, adding new script-facing geography sets, or checking how geographic regions interact with strategic regions, state regions, harvest conditions, and scripted triggers.
name: Geographic Regions
---

# Geographic Regions

Use this skill for aggregated geography sets in `common/geographic_regions`.

## File Location

Write modded definitions in:
`common/geographic_regions/*.txt`

Use vanilla references from:
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\geographic_regions\01_geographic_regions_europe.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\geographic_regions\02_geographic_regions_america.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\geographic_regions\03_geographic_regions_africa.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\geographic_regions\04_geographic_regions_asia.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\geographic_regions\05_geographic_regions_content.txt`
- `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\common\geographic_regions\05_geographic_regions_seismic.txt`

Related files often include:
- `common/strategic_regions/` for `sr:<key>` references
- map state region definitions when using `state_regions = { ... }`
- `localization/*geographic_regions*.yml` for optional description text

In this mod, existing definitions live in:
`common/geographic_regions/ztr_geographic_regions.txt`

## Workflow

1. Decide whether the region should group strategic regions, state regions, or both.
2. Check vanilla first to avoid duplicating an existing geography set.
3. Keep the top-level key descriptive, usually `geographic_region_<name>`.
4. Set `short_key` carefully because script-list access derives from it.
5. Verify every strategic region and state region key exists.
6. Add a `_desc` localization key only if the region is player-facing or lore-relevant.

## Structure

```victoria3
geographic_region_key = {
	short_key = "example"

	strategic_regions = {
		sr:region_example
	}

	state_regions = {
		STATE_EXAMPLE
	}
}
```

## Core Fields

- Top-level key: Region identifier used in script, such as `is_in_geographic_region = geographic_region_oceania`.
- `short_key`: Suffix used by generated script-list names. Keep it short, stable, and unambiguous.
- `strategic_regions`: Any number of `sr:<key>` entries.
- `state_regions`: Any number of `STATE_REGION` keys.

The game combines all listed strategic regions and state regions into one geographic region.

## Mod Pattern

This mod already defines:

```victoria3
geographic_region_oceania = {
	short_key = "oceania"

	strategic_regions = {
		sr:region_oceania
	}
}
```

That region is then consumed elsewhere by checks such as:

```victoria3
is_in_geographic_region = geographic_region_oceania
```

## Editing Guidance

- Prefer strategic-region grouping when the geography is broad and map-driven.
- Add direct `state_regions` only when you need to carve out exceptions or assemble a custom set that does not map cleanly to strategic regions.
- Do not change `short_key` casually; script-list references derived from it may break.
- If the region is used for disasters or climate logic, verify nearby `common/harvest_condition_types/` and scripted triggers still behave sensibly.

## Template

```victoria3
geographic_region_example = {
	short_key = "example"

	strategic_regions = {
		sr:region_example_north
		sr:region_example_south
	}

	state_regions = {
		STATE_EXAMPLE_ISLAND
	}
}
```

## Review Checklist

- The file is in `common/geographic_regions/`.
- The top-level key follows the geographic-region naming pattern.
- `short_key` is present and stable.
- Every `sr:<key>` exists in strategic region data.
- Every `STATE_*` entry is valid.
- The region does not duplicate an equivalent vanilla region without reason.
- Optional `<key>_desc` localization exists if the region is meant to surface to players.
