# Rainfall and Reported Flood Incidents: A Case Study of Mumbai

**A data analytics project using the India Flood Inventory (IMD)**

| | |
|---|---|
| Programme | CDAC PGCP-AI |
| Tools | Python, pandas, numpy, matplotlib, seaborn, scipy |

---

## 1. Abstract

This project studies flood and waterlogging reports for Mumbai using the India Flood Inventory (v3) from the India Meteorological Department (IMD). The inventory holds 6,876 reports. After removing 20 reports with no start date and 74 duplicates, 6,782 remained, of which 156 are local reports for Mumbai from 1974 to 2023. 93.6% of them fall in June to September, and 94.2% name heavy rain as the main cause. Statistical tests confirm the monsoon concentration and a moderate positive link between event length and fatalities. The inventory has no rainfall measurements, so the study describes reported incidents rather than testing rainfall against them. The report also covers reporting gaps and why correlation does not prove causation.

---

## 2. Introduction and Objectives

Heavy rain can cause waterlogging and flooding in cities such as Mumbai. Rain is only one factor: drainage, tides and reporting practice also shape what gets recorded. This project studies the flood reports available in a national inventory to understand when, where and how often incidents are reported.

**Objectives**

1. Collect flood incident records for a selected city (Mumbai).
2. Align dates and location names.
3. Clean duplicate reports, missing values and inconsistent timestamps.
4. Examine incident frequencies by year, month and area.
5. Create seasonal trend charts and area-wise comparisons.
6. Discuss reporting gaps, and the difference between correlation and causation.

**Scope:** the analysis uses reported incidents only. No rainfall measurements were available, so rainfall amounts are not analysed. Section 9 explains how to add them.

---

## 3. Data Sources

| Dataset | Source | Period | Size |
|---|---|---|---|
| India Flood Inventory v3 | IMD (the only source named in the file) | July 1967 to September 2023 | 6,876 rows, 23 columns |
| Mumbai subset (after cleaning and the local-report filter) | Derived from the inventory | July 1974 to July 2023 | 156 reports |

All figures in this report were computed from the cleaned inventory.

---

## 4. Data Standardization and Cleaning

### 4.1 Standardization

| Item | What was done |
|---|---|
| Column names | Extra spaces removed from all column names (for example `"Extent of damage "`). |
| Dates | `Start Date` and `End Date` converted from text (`dd-mm-yyyy hh:mm`) to date-time. Values that could not be read became missing. |
| Location text | Leading and trailing spaces removed from `Districts` and `State`. |
| Cause labels | The free-text `Main Cause` was grouped into five categories: Heavy rain, Flash flood, Landslide, Other, Unknown. |
| City matching | Reports were selected by searching `Districts` for "Mumbai" (case-insensitive). This also matches "Mumbai Suburban". |
| Local reports only | Reports listing more than 5 districts were excluded for the city analysis. |

### 4.2 Cleaning log: before and after

| Step | Before | After | Method | Justification |
|---|---|---|---|---|
| Missing start date | 6,876 rows | 6,856 rows | Dropped 20 rows | An event without a date cannot be placed in time. |
| Duplicates | 6,856 rows | 6,782 rows | Dropped 74 rows with the same start date, districts and state | The same event recorded more than once. |
| Empty columns | 23 columns | 17 columns | Dropped 6 columns with no values: `Location`, `Latitude`, `Longitude`, `Severity`, `Area Affected`, `Event Souce ID` | They carry no information. |
| New columns | 17 columns | 19 columns | Added `Cause_Group` and `n_districts` | Needed for grouping and the local-report filter. |
| Large multi-district reports | 181 reports mention Mumbai | 156 kept | Excluded reports listing more than 5 districts | A city named inside a very long list does not show a city-specific flood. |
| Missing duration (Mumbai) | 0 missing | 0 missing | No action needed | Every Mumbai report has a duration. |

### 4.3 Missing values after cleaning

| Column | Missing | Share of 6,782 reports |
|---|---|---|
| Human fatality | 3,044 | 44.9% |
| Human injured | 5,730 | 84.5% |
| Human displaced | 6,669 | 98.3% |
| Animal fatality | 6,225 | 91.8% |
| Description of casualties | 3,518 | 51.9% |
| Extent of damage | 3,031 | 44.7% |
| Districts | 58 | 0.9% |
| Main cause | 31 | 0.5% |

**Mumbai subset (156 reports):** human fatality is missing in 24.4%, human injured in 55.1%, human displaced in 100%, extent of damage in 35.3% and description of casualties in 23.1%.

### 4.4 Assumptions and handling of extreme values

- **Blank fields are not filled in this report.** A blank casualty or damage field usually means "not reported", not zero. Filling it with a typical value would create numbers that were never observed, so all charts and tests use only the values that exist.
- **Duplicates:** reports with the same start date, districts and state are treated as one. Reports with a blank `Districts` on the same date and state also match, so a few real events may have been removed.
- **Extreme values were kept in the data.** Charts use clipped or filtered views only: the duration histogram groups all events of 15 days or more together, and the state box plot keeps events longer than 0 and shorter than 10 days. The longest Mumbai report lasts 79 days. Long events are real reports of long flooding, so they were not removed.

---

## 5. Exploratory Analysis

### 5.1 Incident frequency by year

![Reported flood incidents per year, Mumbai](charts/c1_mumbai_per_year.png)

*Interpretation:* Mumbai has reports in 45 different years, with the highest counts in 1994 (9 reports) and 1990, 2010 and 2023 (8 reports each).

### 5.2 Seasonal pattern by month

![Flood reports by month, Mumbai](charts/c2_mumbai_per_month.png)

*Interpretation:* Reports are concentrated in the monsoon, with 54 in June, 53 in July, 24 in August and 15 in September, which is 93.6% of all Mumbai reports.

### 5.3 Event duration

![Histogram of event duration, Mumbai](charts/c3_mumbai_duration.png)

*Interpretation:* Most reports describe short events: the median duration is 1 day and 66.0% of reports last 1 day or less.

### 5.4 Cause of incidents

| Cause group | All reports (6,782) | Mumbai (156) |
|---|---|---|
| Heavy rain | 5,096 | 147 |
| Other | 963 | 2 |
| Flash flood | 361 | 3 |
| Landslide | 331 | 4 |
| Unknown | 31 | 0 |

![Reported main cause by month, Mumbai](charts/c4_mumbai_cause_month.png)

*Interpretation:* Heavy rain is named as the main cause in 94.2% of Mumbai reports, compared with 75.1% of all reports in the inventory.

> The cause is a label entered by the reporting source. It is not a measured rainfall amount.

### 5.5 Area-wise and state-wise comparison

![Areas named in Mumbai reports](charts/c5_mumbai_areas.png)

*Interpretation:* Within the Mumbai reports, the areas named most often are Mumbai (146), Thane (22), Mumbai Suburban (13), Ratnagiri (7), Pune (6).

![Top 10 states by number of reports](charts/c6_top_states.png)

*Interpretation:* Across India, Maharashtra (1004), Assam (876), Kerala (603), Karnataka (489), Uttar Pradesh (446) have the most reports, together 50.4% of all reports.

![Share of reports by state](charts/c7_state_pie.png)

*Interpretation:* The five largest `State` values account for about half of all reports, and the rest are spread across many others.

![Event duration by state](charts/c8_duration_by_state.png)

*Interpretation:* Events of under 10 days have similar short durations across the six states with the most reports (median duration by state: Maharashtra (1), Assam (1), Kerala (1), Karnataka (1), Uttar Pradesh (1), Himachal Pradesh (1)).

> The `State` column has 79 distinct values, because some reports list several states in one cell. State counts therefore mix single-state and multi-state reports. No maps were made, because latitude and longitude are completely empty in this file.

### 5.6 Duration and fatalities

![Duration and human fatality](charts/c9_duration_fatality.png)

*Interpretation:* Longer events show a moderate positive link with fatalities (Spearman correlation 0.478, from 3,738 reports that record both values).

### 5.7 Reporting trend across India

![All reports in the inventory per year](charts/c10_national_per_year.png)

*Interpretation:* Reports per year stayed between 120 and 177 in 2016 to 2020, then rose to 330 in 2021, 1,102 in 2022 and 607 in 2023, which points to a change in recording and not only in flooding.

---

## 6. Hypothesis Tests

| Question tested | Test | Statistic | p-value | Effect size |
|---|---|---|---|---|
| Are Mumbai reports concentrated in June to September more than chance (122 of 365 days)? | Exact binomial test | 146 of 156 reports (93.6%) vs 33.4% expected | < 0.001 | Cohen's h = 1.40 |
| Does longer duration go with more fatalities? | Spearman correlation | rho = 0.478 (n = 3,738) | < 0.001 | rho = 0.478 (moderate) |
| Does event duration differ between the six States with the most reports (durations under 10 days)? | Kruskal-Wallis | H = 58.5 (n = 3,585) | < 0.001 | epsilon-squared = 0.016 |

**Conclusions in plain language**

- **Monsoon concentration:** the share of Mumbai reports in June to September is far higher than the 33.4% that an even spread across the year would give, and the difference is very large.
- **Duration and fatalities:** there is a statistically detectable moderate positive link. Duration explains only part of why fatalities differ, because many other factors are involved.
- **Duration by State:** the difference between States is statistically detectable, but its size is small (epsilon-squared 0.016), so State explains very little of the variation in duration.

> The tests were run for this report on the cleaned data, using only recorded values. A small p-value says a pattern is unlikely to be chance. It does not say why the pattern exists.

---

## 7. Major Insights

1. **Seasonality:** 93.6% of Mumbai reports fall in June to September.
2. **Rain as cause:** 94.2% of Mumbai reports name heavy rain as the main cause.
3. **Short events:** the median Mumbai event lasts 1 day, and 66.0% last 1 day or less.
4. **Peak year:** 1994 has the most Mumbai reports in one year (9).
5. **Geographic spread:** Maharashtra has the most reports in India (1,004).
6. **Reporting jump:** national reports rose from 120 to 177 per year in 2016 to 2020, to 330 in 2021, 1,102 in 2022 and 607 in 2023.
7. **Missing detail:** 100% of Mumbai reports have no displaced-people figure, and 55.1% have no injury figure.
8. **Local filter effect:** excluding large multi-district reports reduces Mumbai from 181 to 156 reports.

---

## 8. Limitations

**Data limitations (not findings)**

- **No rainfall measurements.** The inventory has no rainfall values, so rainfall and incidents could not be compared directly.
- **Single source.** All records come from IMD. Local waterlogging that was not reported to IMD does not appear.
- **Reporting changes over time.** The jump in 2021 to 2023 probably reflects a change in recording. A rise in reports does not prove more floods.
- **Only notable events.** Small waterlogging is usually not recorded, so the data leans towards larger events.
- **District-level only.** Reports do not show which neighbourhood flooded.
- **Blank means not reported.** Casualty and damage fields are often blank and are left blank here.
- **Name matching.** "Mumbai" is matched as text and also matches "Mumbai Suburban". Reports are included even when other districts (for example Thane) appear in the same report.
- **Date accuracy.** The start date may be the date of reporting rather than the date flooding began.

**Interpretation limits**

- **Correlation is not causation.** If rain and incident reports move together, rain may be one cause among others: tides, drainage blockages, land use, dam releases and rain intensity can all play a part. Reporting behaviour also matters, because heavy rain draws more attention and more reports.
- **Tests show patterns, not reasons.** The tests in Section 6 say whether a pattern is likely to be real, not what causes it.

---

## 9. Recommendations and Future Work

- Add daily rainfall for Mumbai covering the same years, then compare the share of days with an incident for each rainfall class and compute the correlation.
- Add hourly rainfall and tide data to separate short intense rain from long steady rain.
- Compare monsoon and non-monsoon periods with further tests once rainfall is available.
- Keep blank casualty and damage fields blank, or flag any filled values, so estimates are not presented as facts.
- Add a district shapefile to draw maps.
- Check how the results change when the local-report filter allows a different number of districts.

---

## 10. Conclusion

Using the IMD flood inventory, this project found 156 local flood reports for Mumbai between 1974 and 2023. They are strongly seasonal (93.6% in June to September), mostly short, and almost always attributed to heavy rain (94.2%). The sharp rise in national reports in 2021 to 2023 and the large share of blank detail fields show that the data reflects reporting practice as well as events. The tests confirm the monsoon pattern and a moderate link between duration and fatalities. Even with rainfall data added, a link between rain and reports would not prove that rain alone caused the incidents.
