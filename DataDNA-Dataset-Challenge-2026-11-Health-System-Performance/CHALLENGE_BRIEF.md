# DataDNA Challenge: UK and Global Health System Performance, 2018 to 2024

## ⚠️ Synthetic Data Notice

> This dataset is synthetic. It was simulated for a training challenge and does not report the real performance of any country, health system or organisation. Country names and reference attributes (WHO region, World Bank income group, OECD and EU membership, population) come from public sources so the scenario feels familiar. Every performance, workforce and spending figure is simulated, and the health system regions are fictional. Where a published country figure exists (for example hospital beds, health spending, doctors and nurses per 1,000), the simulated values are centred on it so the scale is realistic. Do not cite these figures as real-world evidence.

Please add this short footer to your charts and report pages: "Synthetic data, DataDNA November 2026 challenge."

**Corrected reissue:** this pack is a corrected reissue of the November challenge dataset and replaces the version first published this month. Please build on this version only.

## 🎯 Your Role

You are a health system performance analyst in an international policy unit, reporting to its board. The board wants an evidence-led review of how health systems absorbed the pandemic shock, who recovered and who did not, and what the longer-running trends in workforce and disease burden mean for planning. Your report will be read by people who make funding and workforce decisions, so every finding needs a clear number behind it and a clear statement of how you measured it.

**Difficulty: Medium**

## 📊 The Scenario

How did health systems in 32 countries absorb the pandemic shock, and who recovered? The dataset follows fictional health system regions across 32 countries from 2018-01-01 to 2024-12-31, reported by calendar quarter, with an annual staff survey alongside. Countries carry real reference attributes (WHO region, World Bank income group, OECD and EU membership, population); every performance, workforce and spending figure is simulated for this scenario.

Every quarter is labelled with a scenario phase in `dim_date[pandemic_phase]`:

- **Pre-Pandemic:** 2018 Q1 to 2020 Q1
- **Acute Pandemic:** 2020 Q2 to 2021 Q3
- **Recovery:** 2021 Q4 to 2022 Q4
- **Post-Pandemic:** 2023 Q1 to 2024 Q4

### Dataset Overview

- **Dimensions:** 3 dimension tables
  - `dim_country`: 32 rows, one per country, with `iso3`, `country_name`, `who_region`, `income_group`, `is_oecd_member`, `is_eu_member`, `healthcare_model` and `population_millions`. `healthcare_model` is a simplified, editorial label.
  - `dim_date`: 28 rows, one per calendar quarter, with `quarter_start_date`, `quarter_end_date`, `year`, `quarter`, `year_quarter`, `uk_fiscal_year`, `pandemic_phase` and `is_acute_pandemic`
  - `dim_health_system`: 212 rows, one per fictional health system region, with `system_code`, `system_name`, `area_type`, `population_thousands` and `hospitals_count`
- **Facts:** 2 fact tables
  - `fact_health_performance`: 5,936 rows, one per region per quarter (212 regions x 28 quarters)
  - `fact_workforce`: 1,484 rows, one per region per survey year (212 regions x 7 survey years, fieldwork in Q4)
- **Currency:** USD
- **Scope:** 2018-01-01 to 2024-12-31

### How the Tables Relate

Both fact tables are at the centre of a star schema. Each fact row links to `dim_health_system` on `system_id`, to `dim_country` on `country_id` and to `dim_date` on `date_id`. In `fact_workforce`, `date_id` always points to Q4 of the `survey_year`.

`dim_health_system` also carries `country_id`. Keep that relationship **inactive** in Power BI (or leave it out), because filtering a fact table through two paths to `dim_country` makes the model ambiguous. The country on every fact row always matches the country of its region, so nothing is lost.

### Key Metrics

`fact_health_performance` (quarterly):

- `beds_per_1000`: Hospital beds per 1,000 population
- `icu_occupancy_pct`: Average share of intensive care beds occupied in the quarter
- `ed_4hr_pct`: Share of emergency department patients admitted, transferred or discharged within 4 hours
- `elective_wait_weeks`: Median wait for planned (elective) hospital treatment, in weeks
- `excess_mortality_per_100k`: Deaths in the quarter above the 2015 to 2019 expected level, per 100,000 people; can be negative
- `mortality_per_1000`: All-cause death rate, annualised, per 1,000 people. A crude all-age rate, not age-standardised, so countries with older populations show higher values
- `readmission_30d_pct`: Share of discharged patients readmitted as an emergency within 30 days
- `avg_length_of_stay_days`: Average inpatient length of stay in days
- `cardiovascular_prevalence_pct`: Share of the population with recorded cardiovascular disease. A crude all-age rate, not age-standardised, so countries with older populations show higher values
- `diabetes_prevalence_pct`: Share of adults aged 20 to 79 with diabetes, age-adjusted (comparative prevalence), so unlike the other prevalence and incidence columns it is not driven by population age
- `cancer_incidence_per_100k`: Recorded new cancer diagnoses, annualised, per 100,000 people. A crude all-age rate, not age-standardised, so countries with older populations show higher values
- `respiratory_illness_pct`: Share of the population with a recorded consultation for a respiratory illness in the quarter, including COVID-19
- `health_spend_per_capita_usd`: Current health spending per person, annualised, in US dollars

Note on `excess_mortality_per_100k`: for 2022 to 2024, each country's yearly level is centred on published estimates (sources in `README.md`); the 2024 values are estimates, because only part of 2024 had been observed when they were published. It is a crude rate per 100,000 people, not age-standardised.

`fact_workforce` (annual survey):

- `doctors_per_1000`: Practising doctors per 1,000 population
- `nurses_per_1000`: Practising nurses and midwives per 1,000 population
- `vacancy_rate_pct`: Funded clinical posts vacant at survey date, percent
- `burnout_index`: Staff burnout index from the annual survey, 0 (none) to 100 (severe)
- `international_recruit_pct`: Share of practising doctors trained abroad

## 🔍 Analysis Areas

Each area below tells you where to look and which cut to start from. It does not tell you what you will find or how big it is: finding the pattern and measuring it is the challenge.

1. **The pandemic shock in acute care**
Compare ICU occupancy before and during the acute pandemic phase, and see how the change differs by country. Track emergency department 4-hour performance quarter by quarter and find when it fell furthest. Follow recorded respiratory illness through the pandemic phases, including whether and when it returned to its earlier level.

2. **The elective backlog and who recovered**
Compare elective waiting times in 2019 and 2021, overall and country by country, and count how many countries moved by a similar amount. In this scenario, follow elective waits in Sweden, Norway, Denmark, Finland and Germany after 2021, quarter by quarter. In this scenario, compare United Kingdom elective waits with their 2019 level and with the EU member countries, year by year.

3. **Health spending**
In this scenario, compare health spending per person in the United Kingdom with the EU member countries. Look at the average across the whole period as well as year by year.

4. **The workforce under strain**
Follow the staff burnout index year by year, and identify its peak. Plot vacancy rates against burnout for every region and survey year, and describe the relationship. In this scenario, compare staff vacancy rates in the United Kingdom with the EU member countries. In this scenario, compare how burnout changed after 2022 in the United Kingdom and in the other high-income countries.

5. **Long-running disease trends**
Look at the trend in recorded cardiovascular disease in each WHO region. Look at the trend in recorded diabetes in each WHO region. In this scenario, compare the trend in recorded cancer incidence in high-income and low-income countries, and think about what "recorded" means when you explain it.

Not every metric moves with the pandemic, and not every pair of metrics is related. If you test for a pattern and do not find one, saying so clearly, with the cut you used, is a valid finding.

## ❓ Guiding Questions

Use these questions to structure your analysis. They are starting points: feel free to explore further.

### Pandemic Shock

- How much higher was average ICU occupancy in the Acute Pandemic phase than in the Pre-Pandemic phase, and which countries saw the largest and smallest change?
- In which phase did emergency department 4-hour performance fall furthest from its Pre-Pandemic level, and by how much?
- How large was the rise in recorded respiratory illness during the Acute Pandemic phase, and how close to its Pre-Pandemic level was it by the Post-Pandemic phase?

### Elective Recovery

- What is the ratio of the average elective wait in 2021 to the average in 2019, overall, and how many of the 32 countries saw a rise of a similar size?
- In this scenario, how close to their 2019 level were elective waits in Sweden, Norway, Denmark, Finland and Germany by the second half of 2022?
- In this scenario, how did United Kingdom elective waits in 2022, 2023 and 2024 compare with the United Kingdom's own 2019 level, and how did the United Kingdom compare with the EU member countries in 2024?

### Spending

- In this scenario, how does average health spending per person in the United Kingdom compare with the average of the EU member countries across the whole period?

### Workforce

- Did the burnout index rise in every year up to its peak, which year was the peak, and what was the average in that year?
- How strong is the relationship between burnout and vacancy rates across all region-years?
- In this scenario, how much higher or lower are United Kingdom vacancy rates than those of the EU member countries?
- In this scenario, where did the other high-income countries and the United Kingdom each stand on burnout in 2024, compared with 2022?

### Disease Trends

- By how many percentage points a year did recorded cardiovascular disease and recorded diabetes change in each WHO region?
- In this scenario, how did recorded cancer incidence change each year in high-income and in low-income countries, and why might recorded incidence move differently from true incidence?

## 🛠️ Technical Hints

- **Use averages, not sums.** Every metric is a rate, a share, a per-person figure or an index. Use Average (the mean of the rows in your filter context), never Sum.
- **Comparing one country with a group of countries.** In this scenario, as in the reference data, the United Kingdom is not an EU member, so a slicer on `is_eu_member` shows TRUE against FALSE, and FALSE mixes the United Kingdom with every other non-member country. Write two CALCULATE measures instead and compare them directly, for example:

```dax
UK Wait = CALCULATE ( AVERAGE ( fact_health_performance[elective_wait_weeks] ), dim_country[country_name] = "United Kingdom" )
EU Wait = CALCULATE ( AVERAGE ( fact_health_performance[elective_wait_weeks] ), dim_country[is_eu_member] = TRUE () )
UK to EU Ratio = DIVIDE ( [UK Wait], [EU Wait] )
```

  The same pattern works for spending, vacancy rates and burnout. For "the other high-income countries", filter `dim_country[income_group] = "High income"` and `dim_country[country_name] <> "United Kingdom"` in the same CALCULATE.

- **Trend slopes need a number, not just a line.** The analytics pane trend line in Power BI shows the direction of a trend but not its slope. To quantify a yearly trend, either take the endpoint estimate, (average in 2024 minus average in 2018) / 6, or use a LINESTX measure, for example:

```dax
CVD Slope per Year =
VAR Yearly =
    ADDCOLUMNS (
        VALUES ( dim_date[year] ),
        "@Avg", CALCULATE ( AVERAGE ( fact_health_performance[cardiovascular_prevalence_pct] ) )
    )
RETURN
    MAXX ( LINESTX ( Yearly, [@Avg], dim_date[year] ), [Slope1] )
```

  Put the measure in a table or matrix with `dim_country[who_region]` or `dim_country[income_group]` on rows.

- **Phases and years.** Use `dim_date[pandemic_phase]` for phase comparisons and `dim_date[year]` or `dim_date[year_quarter]` for yearly and quarterly ones. For `fact_workforce`, use `survey_year` (or `dim_date[year]`); there is one survey per region per year.
- **Look at countries, not only the overall average.** Many effects differ from country to country. A matrix with countries on rows and years or phases on columns, plus a ratio or difference measure, shows how widely they vary.
- **State your cut.** For every number you report, say which measure, filter and period produced it, so a reader can reproduce it.

## 💡 Impact & Purpose

Your analysis should help the board:

- Understand how deep the pandemic shock was in acute care, and how long it lasted
- See which systems cleared their elective backlogs and which did not
- Weigh workforce pressure, and its link to vacancies, when planning staffing
- Plan for slow-moving changes in recorded disease burden

## 📋 Deliverables

Create a professional analytical report that includes:

- **Executive summary:** three to five key findings, each with the number you measured, and a headline recommendation for the board
- **Detailed analysis:** visualisations covering the pandemic shock, elective recovery, health spending, the workforce and disease trends, each labelled with the synthetic data footer
- **Method notes:** the measures, filters and periods behind each headline number, including how you calculated any trend slope or comparison with the EU member countries
- **Actionable insights:** recommendations framed for this scenario, with caveats where the data cannot show a cause

## 📁 Data Dictionary

See `docs/DATA_DICTIONARY.md` for full column-level documentation of all tables, including units and the countries in each WHO region and income group.

## 🚀 Getting Started

- **Build the model first:** load the five tables, create the star relationships and keep `dim_health_system` to `dim_country` inactive
- **Explore the dimensions:** check which countries sit in each income group, WHO region and EU membership group
- **Start with the phases:** a line chart by `year_quarter` with `pandemic_phase` in the background shows the shape of the shock
- **Write your comparison measures:** add the CALCULATE and slope measures from the Technical Hints before you build visuals
- **Quantify, then narrate:** measure each pattern, record the cut you used, then build your story across the five analysis areas

---
