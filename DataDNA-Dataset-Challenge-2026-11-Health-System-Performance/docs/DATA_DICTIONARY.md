# Data Dictionary: UK and Global Health System Performance, 2018 to 2024

> This dataset is synthetic. It was simulated for a training challenge and does not report the real performance of any country, health system or organisation. Country names and reference attributes (WHO region, World Bank income group, OECD and EU membership, population) come from public sources so the scenario feels familiar. Every performance, workforce and spending figure is simulated, and the health system regions are fictional. Where a published country figure exists (for example hospital beds, health spending, doctors and nurses per 1,000), the simulated values are centred on it so the scale is realistic. Do not cite these figures as real-world evidence.

## Overview

- Period covered: 2018-01-01 to 2024-12-31, reported by calendar quarter.
- Currency: USD.
- Files: comma separated, UTF-8 with byte order mark, first row is the header.
- Dates are written as YYYY-MM-DD, booleans as TRUE or FALSE, decimals with a full stop and no thousands separator.
- Reference sources and licence: see README.md.

| Table | File | Grain | Rows | Primary key |
|---|---|---|---|---|
| dim_country | data/dim_country.csv | One row per reference country | 32 | country_id |
| dim_date | data/dim_date.csv | One row per calendar quarter | 28 | date_id |
| dim_health_system | data/dim_health_system.csv | One row per fictional health system region | 212 | system_id |
| fact_health_performance | data/fact_health_performance.csv | One row per health system region per quarter | 5,936 | perf_id |
| fact_workforce | data/fact_workforce.csv | One row per health system region per survey year (annual staff survey, fieldwork in Q4) | 1,484 | workforce_id |

## Relationships

| From | To | Cardinality | Active in a Power BI model |
|---|---|---|---|
| fact_health_performance.system_id | dim_health_system.system_id | many to one | Yes |
| fact_health_performance.country_id | dim_country.country_id | many to one | Yes |
| fact_health_performance.date_id | dim_date.date_id | many to one | Yes |
| fact_workforce.system_id | dim_health_system.system_id | many to one | Yes |
| fact_workforce.country_id | dim_country.country_id | many to one | Yes |
| fact_workforce.date_id | dim_date.date_id | many to one | Yes |
| dim_health_system.country_id | dim_country.country_id | many to one | No |

The fact tables link to dim_country directly as well as through dim_health_system. Both paths always give the same country, so create dim_health_system to dim_country as an inactive relationship (or leave it out) to avoid an ambiguous filter path.

## Pandemic phases

dim_date[pandemic_phase] labels each quarter as follows.

| Phase | First quarter | Last quarter |
|---|---|---|
| Pre-Pandemic | 2018 Q1 | 2020 Q1 |
| Acute Pandemic | 2020 Q2 | 2021 Q3 |
| Recovery | 2021 Q4 | 2022 Q4 |
| Post-Pandemic | 2023 Q1 | 2024 Q4 |

## dim_country

Grain: one row per reference country. Primary key: country_id. Natural key: iso3.

| Column | Type | Unit | Decimals | Description |
|---|---|---|---|---|
| country_id | integer |  |  | Surrogate key, 1 to 32 in reference order |
| iso3 | text |  |  | ISO 3166-1 alpha-3 country code |
| country_name | text |  |  | Country name in English |
| who_region | text |  |  | WHO regional grouping the country is assigned to, 2018 to 2024 |
| income_group | text |  |  | World Bank income group, FY2025 classification |
| is_oecd_member | boolean |  |  | OECD member at 31 December 2024 |
| is_eu_member | boolean |  |  | EU member at 31 December 2024 |
| healthcare_model | text |  |  | Simplified healthcare financing model (editorial label) |
| population_millions | decimal | million people | 1 | Population in millions, 2023 |

## Country groups

### WHO region (who_region)

| WHO region | Countries |
|---|---|
| African Region (7) | South Africa, Nigeria, Kenya, Ethiopia, Uganda, Malawi, Rwanda |
| Region of the Americas (4) | United States, Canada, Brazil, Mexico |
| Eastern Mediterranean Region (1) | Egypt |
| European Region (14) | United Kingdom, Ireland, France, Germany, Netherlands, Austria, Switzerland, Sweden, Norway, Denmark, Finland, Spain, Italy, Türkiye |
| South-East Asia Region (1) | India |
| Western Pacific Region (5) | Australia, New Zealand, Japan, South Korea, China |

Eastern Mediterranean Region and South-East Asia Region each contain one country in this dataset, so a regional average for them is a single country.

### World Bank income group (income_group)

| World Bank income group | Countries |
|---|---|
| High income (19) | United Kingdom, Ireland, France, Germany, Netherlands, Austria, Switzerland, Sweden, Norway, Denmark, Finland, Spain, Italy, United States, Canada, Australia, New Zealand, Japan, South Korea |
| Upper middle income (5) | Brazil, Mexico, Türkiye, China, South Africa |
| Lower middle income (4) | India, Nigeria, Kenya, Egypt |
| Low income (4) | Ethiopia, Uganda, Malawi, Rwanda |

### Healthcare model (editorial) (healthcare_model)

| Healthcare model | Countries |
|---|---|
| Beveridge (12) | United Kingdom, Ireland, Sweden, Norway, Denmark, Finland, Spain, Italy, New Zealand, Brazil, South Africa, Malawi |
| Bismarck (8) | France, Germany, Netherlands, Austria, Switzerland, Japan, Mexico, Türkiye |
| National Health Insurance (6) | Canada, Australia, South Korea, China, Kenya, Rwanda |
| Private Insurance Led (1) | United States |
| Out-of-Pocket Led (5) | India, Nigeria, Egypt, Ethiopia, Uganda |

These labels simplify mixed systems. For example, South Africa is labelled Beveridge because most people rely on the tax-funded public system, although private schemes carry a large share of spending.

### EU and OECD membership (is_eu_member, is_oecd_member)

- EU members (10): Ireland, France, Germany, Netherlands, Austria, Sweden, Denmark, Finland, Spain, Italy. High-income countries in the dataset that are not EU members: United Kingdom, Switzerland, Norway, United States, Canada, Australia, New Zealand, Japan and South Korea.
- OECD members (21). Not OECD members (11): Brazil, China, South Africa, India, Nigeria, Kenya, Egypt, Ethiopia, Uganda, Malawi, Rwanda.

### Health system regions

Each country has fictional regions named "{country} Region NN", with codes such as GBR-R03. `area_type` cycles Metropolitan, Urban, Mixed, Rural within each country, so every country has the same mix of area types. `population_thousands` splits the country's population across its regions by area type, and `hospitals_count` follows from that population and the country's beds per 1,000.

| Regions per country | Countries |
|---|---|
| 12 | United Kingdom, United States |
| 8 | Ireland, France, Germany, Netherlands, Austria, Switzerland, Sweden, Norway, Denmark, Finland, Spain, Italy, Canada, Australia, New Zealand, Japan, South Korea |
| 4 | Brazil, Mexico, Türkiye, China, South Africa, India, Nigeria, Kenya, Egypt, Ethiopia, Uganda, Malawi, Rwanda |

### Country list

| WHO region | ISO3 | Country | Income group |
|---|---|---|---|
| European Region | GBR | United Kingdom | High income |
| European Region | IRL | Ireland | High income |
| European Region | FRA | France | High income |
| European Region | DEU | Germany | High income |
| European Region | NLD | Netherlands | High income |
| European Region | AUT | Austria | High income |
| European Region | CHE | Switzerland | High income |
| European Region | SWE | Sweden | High income |
| European Region | NOR | Norway | High income |
| European Region | DNK | Denmark | High income |
| European Region | FIN | Finland | High income |
| European Region | ESP | Spain | High income |
| European Region | ITA | Italy | High income |
| European Region | TUR | Türkiye | Upper middle income |
| Region of the Americas | USA | United States | High income |
| Region of the Americas | CAN | Canada | High income |
| Region of the Americas | BRA | Brazil | Upper middle income |
| Region of the Americas | MEX | Mexico | Upper middle income |
| Western Pacific Region | AUS | Australia | High income |
| Western Pacific Region | NZL | New Zealand | High income |
| Western Pacific Region | JPN | Japan | High income |
| Western Pacific Region | KOR | South Korea | High income |
| Western Pacific Region | CHN | China | Upper middle income |
| African Region | ZAF | South Africa | Upper middle income |
| African Region | NGA | Nigeria | Lower middle income |
| African Region | KEN | Kenya | Lower middle income |
| African Region | ETH | Ethiopia | Low income |
| African Region | UGA | Uganda | Low income |
| African Region | MWI | Malawi | Low income |
| African Region | RWA | Rwanda | Low income |
| South-East Asia Region | IND | India | Lower middle income |
| Eastern Mediterranean Region | EGY | Egypt | Lower middle income |

## dim_date

Grain: one row per calendar quarter. Primary key: date_id. Natural key: year_quarter.

| Column | Type | Unit | Decimals | Description |
|---|---|---|---|---|
| date_id | integer |  |  | Surrogate key, 1 = 2018 Q1, 28 = 2024 Q4 |
| quarter_start_date | date |  |  | First day of the quarter |
| quarter_end_date | date |  |  | Last day of the quarter |
| year | integer |  |  | Calendar year |
| quarter | integer |  |  | Calendar quarter 1 to 4 |
| year_quarter | text |  |  | Label, for example 2020 Q2; sorts correctly as text |
| uk_fiscal_year | text |  |  | UK financial year, April to March, for example 2020/21 |
| pandemic_phase | text |  |  | Scenario phase derived from the quarter |
| is_acute_pandemic | boolean |  |  | TRUE for 2020 Q2 to 2021 Q3 |

## dim_health_system

Grain: one row per fictional health system region. Primary key: system_id. Natural key: system_code.

| Column | Type | Unit | Decimals | Description |
|---|---|---|---|---|
| system_id | integer |  |  | Surrogate key |
| country_id | integer |  |  | Country the region belongs to (key to dim_country.country_id) |
| system_code | text |  |  | Region code, for example GBR-R03 |
| system_name | text |  |  | Fictional region name |
| area_type | text |  |  | Settlement type of the region. It sets the region's share of the country's population and hospitals only: rates such as beds, staff, waits and outcomes carry no urban or rural gradient |
| population_thousands | integer | thousand people | 0 | Population served by the region, in thousands (fictional split of the country's real population) |
| hospitals_count | integer | hospitals | 0 | Number of hospitals in the region, consistent with its population and the country's beds per 1,000 |

## fact_health_performance

Grain: one row per health system region per quarter. Primary key: perf_id.

| Column | Type | Unit | Decimals | Description |
|---|---|---|---|---|
| perf_id | integer |  |  | Surrogate key, ordered by system_id then date_id |
| system_id | integer |  |  | Health system region (key to dim_health_system.system_id) |
| country_id | integer |  |  | Country (always the region's own country) (key to dim_country.country_id) |
| date_id | integer |  |  | Quarter. The quarterly series have no seasonal (winter) pattern by design; quarter-to-quarter movement comes from trends and the pandemic phases (key to dim_date.date_id) |
| beds_per_1000 | decimal | beds per 1000 population | 2 | Hospital beds per 1,000 population |
| icu_occupancy_pct | decimal | percent | 1 | Average share of intensive care beds occupied in the quarter |
| ed_4hr_pct | decimal | percent | 1 | Share of emergency department patients admitted, transferred or discharged within 4 hours |
| elective_wait_weeks | decimal | weeks | 1 | Median wait for planned (elective) hospital treatment, in weeks |
| excess_mortality_per_100k | decimal | deaths per 100000 population per quarter | 1 | Deaths in the quarter above the 2015 to 2019 expected level, per 100,000 people; can be negative |
| mortality_per_1000 | decimal | deaths per 1000 population per year | 2 | All-cause death rate, annualised, per 1,000 people. A crude all-age rate, not age-standardised, so countries with older populations show higher values |
| readmission_30d_pct | decimal | percent | 1 | Share of discharged patients readmitted as an emergency within 30 days |
| avg_length_of_stay_days | decimal | days | 1 | Average inpatient length of stay in days |
| cardiovascular_prevalence_pct | decimal | percent | 2 | Share of the population with recorded cardiovascular disease. A crude all-age rate, not age-standardised, so countries with older populations show higher values |
| diabetes_prevalence_pct | decimal | percent | 2 | Share of adults aged 20 to 79 with diabetes, age-adjusted (comparative prevalence), so unlike the other prevalence and incidence columns it is not driven by population age |
| cancer_incidence_per_100k | decimal | new cases per 100000 population per year | 1 | Recorded new cancer diagnoses, annualised, per 100,000 people. A crude all-age rate, not age-standardised, so countries with older populations show higher values |
| respiratory_illness_pct | decimal | percent | 2 | Share of the population with a recorded consultation for a respiratory illness in the quarter, including COVID-19 |
| health_spend_per_capita_usd | decimal | USD per person per year | 0 | Current health spending per person, annualised, in US dollars |

Note on `excess_mortality_per_100k`: for 2022 to 2024, each country's yearly level is centred on published estimates (sources in the README); the 2024 values are estimates, because only part of 2024 had been observed when they were published. It is a crude rate per 100,000 people, not age-standardised.

## fact_workforce

Grain: one row per health system region per survey year (annual staff survey, fieldwork in Q4). Primary key: workforce_id.

| Column | Type | Unit | Decimals | Description |
|---|---|---|---|---|
| workforce_id | integer |  |  | Surrogate key, ordered by system_id then survey_year |
| system_id | integer |  |  | Health system region (key to dim_health_system.system_id) |
| country_id | integer |  |  | Country (always the region's own country) (key to dim_country.country_id) |
| date_id | integer |  |  | Quarter of the survey (always Q4 of survey_year) (key to dim_date.date_id) |
| survey_year | integer |  |  | Survey year |
| doctors_per_1000 | decimal | doctors per 1000 population | 2 | Practising doctors per 1,000 population |
| nurses_per_1000 | decimal | nurses per 1000 population | 2 | Practising nurses and midwives per 1,000 population |
| vacancy_rate_pct | decimal | percent | 1 | Funded clinical posts vacant at survey date, percent |
| burnout_index | decimal | index points 0 to 100 | 1 | Staff burnout index from the annual survey, 0 (none) to 100 (severe) |
| international_recruit_pct | decimal | percent | 1 | Share of practising doctors trained abroad |
