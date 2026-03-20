---
name: Production Method Balancing
description: Guide for balancing Victoria 3 production methods using Standard and Pessimistic Productivity relative to Tech Era.
---

# Balancing Production Methods

When creating or modifying Production Methods (PMs), it is critical to ensure they are mathematically balanced and provide appropriate economic returns based on the technology Era they belong to.

## Core Concepts & Formulas

To assess the balance of a Production Method, we calculate two key metrics: **Standard Productivity (SP)** and **Pessimistic Productivity (PP)**.

*   **Value of a Good:** `goods_price * good_quantity`
*   **Output Value:** The sum of each output good's value.
*   **Input Value:** The sum of each input good's value.
*   **Worker Number:** The total employment required by the production method.

### 1. Standard Productivity (SP)
This represents the productivity under normal market conditions, assuming all goods trade at their base prices.

**Formula:**
`SP = (Output Value - Input Value) / Worker Number`

### 2. Pessimistic Productivity (PP)
This represents the productivity in an unfavorable market where the output goods are oversupplied and their price crashes to 1/4 (25%) of their normal price, while input goods remain at their normal price.

**Formula:**
`PP = ((Output Value / 4) - Input Value) / Worker Number`

## Era-Based Balance Targets

The expected productivity of a PM scales non-linearly with its technology Era. A later-era PM should be more productive than an early-era one, but it also carries larger risks when markets crash due to higher input costs.

To evaluate if a PM is balanced, use the unlocking tech's Era and aim for the following targets:

*   **Target SP:** `~ (Era**2) / 20`
*   **Target PP:** `~ -(Era**2) / 30`

### Example Eras:
| Era | Target SP ((Era**2)/20) | Target PP (-(Era**2)/30) |
| :--- | :--- | :--- |
| Era 1 | 0.05 | ~ -0.033 |
| Era 2 | 0.20 | ~ -0.133 |
| Era 3 | 0.45 | ~ -0.300 |
| Era 4 | 0.80 | ~ -0.533 |
| Era 5 | 1.25 | ~ -0.833 |
| Era 6 | 1.80 | ~ -1.200 |
| Era 7 | 2.45 | ~ -1.633 |
| Era 8 | 3.20 | ~ -2.133 |

## How to Apply This Workflow

1. Identify the Production Method you want to evaluate.
2. Sum the base prices of all **inputs** and **outputs** (referencing normal base price data).
3. Determine the total **worker number** added by the PM.
4. Calculate **SP** and **PP** using the formulas above.
5. Identify the **Era** of the highest required unlocking technology for that PM.
6. Compare the calculated SP and PP against the Era-based targets.
7. Adjust goods input quantities, output quantities, or worker numbers until the PM aligns closely with the targets.

### Summary of Base Goods Prices

Here is a quick reference table of goods prices in Victoria 3.

| Good | Price | Good | Price | Good | Price |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `advancedores` | 80 | `fruit` | 30 | `paper` | 30 |
| `aeroplanes` | 100 | `fuel_cells` | 100 | `pharmaceuticals` | 50 |
| `ai_system` | 60 | `furniture` | 30 | `plastics` | 45 |
| `air_travel` | 125 | `gas` | 30 | `porcelain` | 70 |
| `alloys` | 50 | `glass` | 40 | `processors` | 130 |
| `aluminium` | 50 | `gold` | 100 | `radios` | 80 |
| `ammunition` | 50 | `good_uranium` | 40 | `rare_earths` | 50 |
| `artillery` | 70 | `grain` | 20 | `raw_data` | 5 |
| `automobiles` | 120 | `groceries` | 30 | `robotics` | 100 |
| `batteries` | 60 | `hardwood` | 40 | `rubber` | 40 |
| `bauxite` | 30 | `heavy_fuel` | 70 | `services` | 30 |
| `business_data` | 30 | `homeappliances` | 140 | `silk` | 40 |
| `civil_planes` | 90 | `instruments` | 40 | `small_arms` | 60 |
| `clippers` | 60 | `iron` | 40 | `softwares` | 60 |
| `clothes` | 35 | `ironclads` | 90 | `space_assets` | 200 |
| `coal` | 30 | `lead` | 40 | `steamers` | 70 |
| `coffee` | 50 | `light_fuel` | 70 | `steel` | 50 |
| `commonores` | 40 | `liquor` | 30 | `sugar` | 30 |
| `computer` | 140 | `lubricant` | 70 | `sulfur` | 50 |
| `copper` | 30 | `luxury_automobiles` | 150 | `tanks` | 80 |
| `copperwires` | 50 | `luxury_clothes` | 60 | `tea` | 50 |
| `cosmetics` | 70 | `luxury_furniture` | 60 | `telecommunications` | 30 |
| `digital_entertainment` | 80 | `manowars` | 70 | `telephones` | 70 |
| `dye` | 40 | `manzoni_prints` | 60 | `televisions` | 140 |
| `electricity` | 30 | `meat` | 30 | `tobacco` | 40 |
| `electroniccomponents` | 80 | `merchant_marine` | 50 | `tools` | 40 |
| `engines` | 60 | `missiles` | 80 | `transportation` | 30 |
| `explosives` | 50 | `music` | 80 | `water` | 50 |
| `fabric` | 20 | `oil` | 40 | `wine` | 50 |
| `fertilizer` | 30 | `on_demand_goods` | 70 | `wood` | 20 |
| `fine_art` | 200 | `opium` | 50 | | |
| `fish` | 20 | `organized_data` | 10 | | |
