# UK and Global Health System Performance, 2018 to 2024

This dataset is synthetic. It was simulated for a training challenge and does not report the real performance of any country, health system or organisation. Country names and reference attributes (WHO region, World Bank income group, OECD and EU membership, population) come from public sources so the scenario feels familiar. Every performance, workforce and spending figure is simulated, and the health system regions are fictional. Where a published country figure exists (for example hospital beds, health spending, doctors and nurses per 1,000), the simulated values are centred on it so the scale is realistic. Do not cite these figures as real-world evidence.

Suggested chart footer: "Synthetic data, DataDNA November 2026 challenge."

This is a corrected reissue of the November 2026 DataDNA challenge dataset. It replaces the version first published this month; please build on this version only.

## What is in this pack

- `CHALLENGE_BRIEF.md`: the challenge, analysis areas, technical hints and deliverables
- `docs/DATA_DICTIONARY.md`: every table and column, with units, and the countries in each group
- `data/dim_country.csv`: 32 countries with their reference attributes
- `data/dim_date.csv`: 28 calendar quarters, 2018 Q1 to 2024 Q4, with the scenario phase
- `data/dim_health_system.csv`: 212 fictional health system regions, with population served and hospitals
- `data/fact_health_performance.csv`: 5,936 rows, one per region per quarter
- `data/fact_workforce.csv`: 1,484 rows, one per region per annual staff survey

Files are UTF-8 with a byte order mark, comma-separated, with a header row. Dates are ISO 8601 (YYYY-MM-DD) and booleans are TRUE or FALSE. Currency is US dollars.

## Data model

Both fact tables link to `dim_health_system` on `system_id`, to `dim_country` on `country_id` and to `dim_date` on `date_id`. `dim_health_system` also carries `country_id`; keep that relationship inactive in Power BI (or leave it out) to avoid an ambiguous filter path.

In the Power BI model, rate, percentage, index and per-person fact columns default to average rather than sum. Percentage values are stored as percentage points (for example, 75.0 means 75.0%). The measures average region-quarter or region-year records equally, not by population, patient count or workforce size; an average of regional median waits is not a pooled patient-level median. Countries with more fictional regions therefore contribute more observations to a combined average.

Workforce surveys are annual and linked to Q4, so a Q1-Q3 filter excludes those survey rows. Fixed-period narrative comparisons use the full stated years or quarters regardless of date slicers; other measures retain their documented date context. UK and EU benchmarks ignore country filters but still respect other filters, such as health system region.

## Licence and attribution

The simulated figures and fictional regions in this pack are released under the Creative Commons Attribution 4.0 International licence (CC BY 4.0) for learning and portfolio use. Please credit "DataDNA November 2026 challenge (synthetic data)".

Reference attributes come from these public sources, used as they stood for 2018 to 2024 or as stated:
- Country codes and names: ISO 3166-1 alpha-3 (some long names shortened, for example "South Korea").
- WHO region: the World Health Organization regional grouping each country is assigned to for 2018 to 2024. It is a regional grouping, not a statement about current WHO membership.
- Income group: World Bank country classification by income, FY2025 (World Bank, CC BY 4.0).
- Population: World Bank World Development Indicators, SP.POP.TOTL, 2023 value, rounded to 0.1 million (World Bank, CC BY 4.0). Region populations split it across fictional regions.
- OECD membership: OECD list of member countries, at 31 December 2024.
- EU membership: European Union list of member countries, at 31 December 2024.
- Healthcare model: a simplified, editorial label based on a common four-model typology of health financing, with one extra label (Private Insurance Led). It is not taken from an official source.

Country reference levels for selected indicators are centred on published figures from OECD Health Statistics, the World Bank World Development Indicators, the WHO Global Health Expenditure Database, the WHO Global Health Observatory, The Economist excess deaths model via Our World in Data, the South African Medical Research Council (SAMRC), IHME (Global Burden of Disease), the IDF Diabetes Atlas and the IARC Global Cancer Observatory (GLOBOCAN); simulated values are not those sources' data and must not be cited as such. `excess_mortality_per_100k` values for 2022 to 2024 are centred on The Economist excess deaths model via Our World in Data (central estimates); the 2024 values are estimates, because only part of 2024 had been observed when they were published.
