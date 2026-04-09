## TASKS LOG

## 2026-04-08
1. Era 10 biorefining technology package
- Completed: added the new `biorefining` production technology in era 10 as a circular-chemistry bridge between `plastic_recovery` and `urban_sustainability`, with built-in oil/plastics input reductions plus throughput bonuses for Oil Refineries and Petrochemical Industries.
- Updated: `common\technology\technologies\ztr_new_production.txt`.
- Verified: confirmed the new tech sits in the era 10 production section with valid prerequisite keys, existing modifier types, and a standard AI weighting block.
2. Biofuel, bioplastic, and recycling unlock chain
- Completed: added `pm_hydrotreated_biofuels` for Oil Refineries, `pm_biopolymer_synthesis` for Petrochemical Industries, and `pm_enzymatic_upcycling` for Urban Center recycling so the research unlock creates a full feedstock loop across fuels, plastics, and municipal waste.
- Updated: `common\production_methods\ztr_new_production_methods.txt` and `common\production_method_groups\ztr_new_production_method_groups.txt`.
- Verified: checked that each new PM is listed in the correct PMG, gated by `biorefining`, and uses only existing goods, laws, and icon paths.
3. English localization for the new green chemistry branch
- Completed: added player-facing English localization for the new tech and all three production methods.
- Updated: `localization\english\ztr_inventions_l_english.yml` and `localization\english\ztr_production_methods_l_english.yml`.
- Verified: confirmed the appended keys match the new script keys exactly.
4. Localization improver agent rewrite
- Completed: rewrote the custom agent definition so it behaves like a focused localization specialist instead of a generic checklist, with explicit tool use, constraints, workflow, formatting rules, scope checks, and output expectations.
- Updated: `.claude\agents\localization-improver.agent.md`.
- Verified: confirmed the frontmatter now includes a keyword-rich description plus agent metadata, and the body is structured as a taskable custom-agent prompt.
5. Source-backed biofuel output ratio for `pm_hydrotreated_biofuels`
- Completed: researched HEFA / hydrotreated biofuel product slates from NREL and DOE-aligned sources, then remapped the PM outputs to a realistic `light_fuel-heavy_fuel` split with no lubricant stream.
- Updated: `common\production_methods\ztr_new_production_methods.txt`.
- Verified: used NREL's 2017 HEFA yield table/figure showing product slates dominated by naphtha + jet + diesel with only a small heavy-residue fraction and no lubricant cut, so the PM now outputs `113` @light_fuel! and `2` @heavy_fuel! instead of producing lubricants.
6. Plastics PM cost calculation snapshot
- Completed: calculated the live base-price input and output values for `pm_ziegler-natta_catalysts` and `pm_biopolymer_synthesis` using the PM `workforce_scaled` quantities plus the current mod goods costs for `plastics`, `light_fuel`, `electroniccomponents`, `water`, and `gas`, and base-game costs for `sugar`, `paper`, `fertilizer`, and `electricity`.
- Updated: `LOG.md`.
- Verified: excluded `required_input_goods = electricity` from the totals because it is a gating flag, not an extra quantity line item.
