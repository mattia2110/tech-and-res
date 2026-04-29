# Tech & Res , Work Log

## 2026-04-29

- Creato `gfx/map/fleet_entities/ztr_navy_fleet_entities.txt` per le navi del mod definite in `common/ship_types/ztr_navy_ship_types.txt`. Cinque entity coperti:
  - `ship_type_modern_destroyer`: riusa `destroyer_base_entity`, decals/VFX vanilla destroyer; attach armor (`hull_destroyer_*`), guns (cannoni gemelli + tubi lanciasiluri tripli su locator destroyer), propulsion (`funnel_small_03_entity` su tutti i quattro funnel).
  - `ship_type_modern_cruiser`: riusa `lightcruiser_base_entity`; attach armor (`hull_lightcruiser_*`), guns (small turret gemelli per posizioni primarie + deck mounted gemelli laterali e silurifici), propulsion via modification `funnel` legata al range slot (`funnel_medium_01/02/03`).
  - `ship_type_modern_battleship`: riusa `superdreadnought_base_entity`, length 7.0; attach armor (`hull_superdreadnought_*`), guns (turret triple sui quattro slot principali + casemate destri attivi e sinistri statici), propulsion via modification `funnel` (`funnel_large_01/02/03`).
  - `ship_type_nuclear_carrier`: riusa `aircraftcarrier_base_entity`; attach armor (`hull_aircraftcarrier_*`), planes su quattro locator, AA su torrette/towers, propulsion `funnel_trunked_02_entity` su entrambi i funnel.
  - `ship_type_nuclear_submarine`: riusa `submarine_base_entity`; solo attach armor (`hull_submarine_*`), come il submarine vanilla.
- Gli slot custom `ship_mod_slot_electronics`, `ship_mod_slot_missiles`, `ship_mod_slot_air_wing` e tutti gli `ship_mod_slot_utility_*` restano stat-only (nessun entity dedicato), quindi non hanno blocchi `attach`.
- Creata la directory `gfx/map/fleet_entities/` precedentemente assente nel mod.

## 2026-04-28

- Aggiornato `common/law_groups/ztr_laws.txt`: rimossi `progressive_movement_chance` e `regressive_movement_chance` dai law group custom e aggiunto `ideological_opinion_impact` calibrato sulla scala vanilla (`0.25`, `0.5`, `1.0`) in base alla rilevanza reale e al tipo di conflitto ideologico. Verificato che non restino campi movement chance nel file.
- Spostata la PM `pm_uhv_grid_download` da `smart_energy_grid` (era 10) a `embedded_control_systems` (era 8), che riflette meglio l'elettronica di potenza/HVDC alla base delle linee UHV reali e colma il salto era 7 (`high_voltage_transmission`) -> era 10.
- Aggiunto `common/company_types/ztr_consulting_firm_companies.txt` con:
  - `generic_company_basic_consulting`: company generica con dynamic naming, sbloccata da `market_studies`, basata su `building_office`. Bonus: throughput `building_office` +5%, `country_bureaucracy_investment_cost_factor_mult` -5%.
  - `company_deloitte` (UK, STATE_HOME_COUNTIES): bonus `building_office` +10%, `building_pharmaceuticals_industry` +5%, `country_bureaucracy_investment_cost_factor_mult` -5%; extension `building_pharmaceuticals_industry`, `building_paper_mill`.
  - `company_mckinsey` (USA, STATE_NEW_YORK + STATE_ILLINOIS): bonus throughput `building_office`, `building_motor_industry`, `building_steel_mills`, `building_pharmaceuticals_industry` +5% ciascuno.
  - `company_roland_berger` (Germania, STATE_BAVARIA): bonus throughput `building_motor_industry`, `building_chemical_plant`, `building_steel_mills`, `building_electrics_industry` +5% ciascuno.
  - `company_capgemini` (Francia, STATE_ILE_DE_FRANCE): bonus throughput `building_software_industry`, `building_datacenter_industry`, `building_aircraft_industry`, `building_telecommunications_industry` +5% ciascuno.
  - `company_nri_nomura` (Giappone, STATE_KANTO): bonus throughput `building_consumer_electronics_industry`, `building_electronics_industry`, `building_motor_industry`, `building_office` +5% ciascuno.
- Aggiunte le chiavi di localizzazione per le sei nuove company in `localization/english/ztr_companies_l_english.yml`.
