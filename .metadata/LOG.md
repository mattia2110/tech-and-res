## TASKS LOG
## 2026-04-17
1. Decolonization key cleanup and description polish
- Completed: replaced all per-country `ztr_subject_created_*` flags in the regenerated decolonization events with the shared `ztr_decolonization_subject_cd` variable, swapped all option C and option E event names to the shared localization keys `ztr_decolonization.c` and `ztr_decolonization.e`, and refreshed the new phase descriptions with stronger visual structure.
- Updated: `events/decolonization/ztr_decolonization.txt`, `localization/english/ztr_decolonization_events_l_english.yml`, `scripts/generate_decolonization.ps1`
- Verified: the generated event file now saves `ztr_decolonization_subject_cd`, no longer references `ztr_decolonization.N.c` or `ztr_decolonization.N.e`, standard/special events that use culture-aware localization now save `ztr_decolonization_culture_scope` in `immediate`, and India keeps a bespoke country-focused description prefix instead of a misleading single-culture scope.

## 2025-06-17
1. Decolonization Events Overhaul (Events 2-67)
- Completed: Regenerated all 66 flavor decolonization events with phase-based triggered descriptions, four option branches (Grant Independence, Reject, Create Colony, War of Independence), cooldown variable checks in triggers, and preserved special handling for Algeria, Trucial States, India, China, Botswana/Zimbabwe; added 331 localization keys and a PowerShell generation script.
- Updated: `events/decolonization/ztr_decolonization.txt`, `common/scripted_triggers/ztr_decolonization_triggers.txt`, `localization/english/ztr_decolonization_events_l_english.yml`, `scripts/generate_decolonization.ps1`
- Verified: Phase-based description keys (d_phase2/3/4) match localization entries, cooldown variable names consistent between triggers and events, special-case countries retain unique logic, events 100-101 preserved verbatim, 198 phase descriptions + 66 option C + 66 option E + 1 tooltip = 331 new localization keys confirmed.

## 2026-04-17
2. Decolonization event image selection pass
- Completed: replaced the generic `southamerica_war_civilians` image on `ztr_decolonization.2` through `ztr_decolonization.66` with phase-sensitive `event_image` blocks chosen from vanilla `gfx/media_aliases`, using regional sets for Africa, the Middle East, and Asia plus special cases for Algeria, Persia, India, Japan, China, Siam, and New Zealand.
- Updated: `events/decolonization/ztr_decolonization.txt`
- Verified: events `2-66` now use triggered image aliases, event `67` still keeps the original generic image by design, and the remaining `southamerica_war_civilians` references are the untouched event `67` and event `100` fallback media logic.