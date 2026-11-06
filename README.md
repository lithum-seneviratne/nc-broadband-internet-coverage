> **This README was written by Claude** (Anthropic's AI assistant, model Claude Opus 5.5, running in Claude Code) at the author's request. First version November 3, 2026; updated November 5 and November 6, 2026, based on the repository at commit `c08a515`.

# Who in North Carolina is not served by terrestrial broadband?

E102 Final Project, Topic 5: Rural Broadband Access, population analysis.

This project uses the FCC's Broadband Data Collection (BDC) to find the census tracts in North Carolina where a large share of locations have **no terrestrial 100/20 Mbps service available**. It groups touching tracts into regions and ranks the regions by how many locations lack service. It then uses American Community Survey (ACS) data to describe **who lives** in those tracts: income, age, internet subscriptions, and employment.

> **Status (November 6, 2026):** done so far:
> - the coverage analysis and the demographic comparison
> - margins of error for every cluster
> - runs at several thresholds (0, 5, 15, 25, 50, 75, 90, 100%)
> - a cluster-level Excel analysis (reliability filter, correlations, regressions) for 5%, 15% and 25%
>
> Still to do:
> - a stricter service definition (wired + licensed fixed wireless)
> - adding the Excel analysis workbook to the repo
> - the fixes under Known issues

---

## Results

### Coverage

Main rule: a tract is **flagged** when at least **15%** of its locations have no terrestrial 100/20 service and it has at least **100** locations.

- Statewide: **147,066 of 4,435,342** broadband serviceable locations (3.3%) have no terrestrial 100/20 service.
- **120 tracts** meet the rule. Touching flagged tracts form **45 clusters**. **27 of the 45 clusters are a single tract**, so only the top few are real multi-tract regions.
- The flagged tracts hold **59,187 unserved locations, 40% of the state's total**, on 5% of its locations.
- The 100-location minimum excludes 7 tracts that would otherwise qualify. Together they have 75 unserved locations out of 99, and most are special-use tracts (parks, airports, water).

| Rank | Counties | Tracts | Locations | No terrestrial 100/20 | % | Households | % households with no broadband subscription (± MOE) |
|---|---|---|---|---|---|---|---|
| 1 | Cherokee, Clay, Graham, Jackson, Macon, Swain, Transylvania | 28 | 66,793 | 22,587 | 33.8 | 42,052 | 18.5 ± 1.7 |
| 2 | Henderson, Polk, Transylvania | 8 | 15,808 | 3,936 | 24.9 | 11,787 | 9.3 ± 2.2 |
| 3 | Franklin, Halifax, Nash, Warren | 9 | 14,403 | 3,341 | 23.2 | 10,755 | 25.4 ± 3.3 |
| 4 | Anson, Cabarrus, Stanly | 8 | 13,245 | 2,949 | 22.3 | 10,312 | 11.9 ± 2.8 |
| 5 | Buncombe, McDowell, Rutherford | 9 | 12,429 | 2,709 | 21.8 | 8,867 | 20.5 ± 3.9 |

**The largest region is the far west**, with almost six times as many unserved locations as the next cluster.

**Availability and adoption are different problems.** Clusters 2 and 3 have similar coverage gaps (24.9% and 23.2% of locations unserved). In cluster 2, 9.3% of households have no broadband subscription, which is no different from the state's 9.9% within the margin of error. In cluster 3 it's 25.4%, about 2.5 times the state rate.

### How the threshold changes the picture

| Threshold | Flagged tracts | Clusters | Single-tract clusters | Unserved locations in flagged tracts | Share of NC's unserved | % of their locations unserved | % households with no broadband subscription (flagged / not flagged) |
|---|---|---|---|---|---|---|---|
| 5% | 466 | 29 | 15 | 117,248 | 79.7% | 13.5 | 15.3 / 9.0 |
| **15%** | **120** | **45** | **27** | **59,187** | **40.2%** | **25.6** | **17.2 / 9.6** |
| 25% | 34 | 19 | 13 | 28,306 | 19.2% | 40.6 | 20.9 / 9.8 |
| 50% | 5 | 3 | 1 | 8,278 | 5.6% | 70.5 | 19.9 / 9.9 |

- At **0%**, all 2,616 tracts with 100+ locations touch each other and form one cluster.
- At **75% and above**, no tract qualifies. The highest is 74.8%, in Jackson County (`37099950800`).
- At **5%**, the top three clusters contain 412 of the 466 tracts, so "clusters" become large regions spanning dozens of counties each.
- **The higher the threshold, the higher the share of households with no subscription**, up to 25%. At 50% only 5 tracts and 6,266 households are left, so that row is noisy.

### Who lives in flagged tracts

Flagged tracts at 15%, compared with all other tracts that have FCC data and with North Carolina as a whole. Every rate is calculated from summed counts, not by averaging tract percentages.

| Measure | Flagged tracts | Not flagged | North Carolina |
|---|---|---|---|
| Tracts | 120 | 2,534 | 2,672 |
| Households | 163,982 | 4,115,690 | 4,282,345 |
| People in households | 389,763 | 10,053,207 | 10,451,237 |
| % households with **no broadband subscription** | **17.2** | 9.6 | 9.9 |
| % households with **cellular data only** | 13.5 | 10.3 | 10.4 |
| % households with **satellite only** | 1.6 | 0.3 | 0.4 |
| % households earning **under $35,000** | 27.7 | 23.1 | 23.2 |
| % of people **aged 65+** | 23.1 | 17.0 | 17.2 |
| % of **children** (under 18) without a broadband subscription | 6.6 | 4.5 | 4.6 |
| % of the labor force **unemployed** | 4.6 | 4.6 | 4.6 |

In flagged tracts, households are **about 1.7 times as likely to have no broadband subscription** and **about 4 times as likely to rely only on satellite** as the state overall. They are also lower-income and older than the rest of the state. Unemployment is the same.

These are differences between **groups of tracts**. They don't show that the specific homes without service are the lower-income or older ones (see Limitations).

### Margins of error

Every cluster's demographic rates now have a 90% margin of error and a coefficient of variation (CV) in `results/Clusters MOE.xlsx`, one sheet per threshold.

- **Larger clusters have much tighter estimates.** Cluster 1 at 15% (42,052 households) has a CV of 5.6% for "no broadband subscription". Many single-tract clusters are above 30%.
- **The children measure is the noisiest.** It's a small group (children in households with no subscription), and cluster 2's CV is 42.7%.
- Clusters whose "no broadband subscription" estimate has **CV < 30%**: 18 of 28 at 5%, 27 of 44 at 15%, and 12 of 19 at 25%. These counts leave out Durham's zero-household cluster at 5% and 15%.

### Cluster-level analysis (Excel)

This part was done in Excel, as in class, in a working copy of `Clusters of Flagged Tracts.xlsx` (see Known issues). Each threshold's sheet has the cluster table and two summary blocks, one using **all clusters** and one using only clusters with **CV < 0.3**. Each block gives:
- **Total:** pooled over all clusters (see Important definitions)
- **Statewide:** the NC rate
- **Diff:** Total − Statewide, in percentage points

| Threshold | No broadband subscription: all / CV < 0.3 | Unserved locations: all / CV < 0.3 | Statewide (no sub / unserved) |
|---|---|---|---|
| 5% | 15.3 / 15.3 | 13.5 / 13.6 | 9.9 / 3.3 |
| 15% | 17.2 / 18.0 | 25.6 / 26.4 | 9.9 / 3.3 |
| 25% | 20.9 / 22.5 | 40.6 / 41.5 | 9.9 / 3.3 |

Dropping the unreliable clusters moves the pooled rates by less than 2 points. **The results don't depend on the noisy clusters.**

**Regression:** the dependent variable is % households with no broadband subscription. The predictors are % locations unserved, % earning < $35k, % aged 65+, % children without broadband, and % unemployed. One row per cluster, CV < 0.3 clusters only:

| Threshold | Clusters | R² | Adjusted R² | < $35k coefficient (p) | Unserved coefficient (p) |
|---|---|---|---|---|---|
| 5% | 18 | 0.89 | 0.84 | 0.36 (0.001) | 0.17 (0.23) |
| 15% | 27 | 0.45 | 0.32 | 0.35 (0.03) | 0.05 (0.81) |
| 25% | 12 | 0.84 | 0.71 | 0.90 (0.003) | −0.08 (0.59) |

- **Income is the most consistent predictor** of not subscribing. It's significant at every threshold in the CV < 0.3 models.
- **The share of unserved locations is never a significant predictor** (p from 0.13 to 0.87 across all six models). Among the reliable 15% clusters, its correlation with no-subscription is −0.21. This supports the availability-vs-adoption point above: where people don't subscribe is more about income than about where service is missing.
- The all-cluster models agree, but they are weaker. At 25% the all-cluster model isn't significant overall (p = 0.29). See Limitations for why these regressions are exploratory.

The SQL output tables are in `results/`:
- `Clusters of Flagged Tracts.xlsx`: per cluster, subscriptions and coverage, plus a North Carolina sheet
- `Demographics.xlsx`: per-cluster income, age, children and unemployment, plus a North Carolina sheet
- `Flagged vs. Not Flagged Tracts Demographics Analysis.xlsx`: one sheet per threshold
- `Clusters MOE.xlsx`: rates, MOEs and CVs per cluster

Maps for every threshold (`flagged_tracts_{t}pct.png`, `top3_clusters_{t}pct.png`) are in `results/`. The 5%, 15% and 25% maps show county borders.

---

## Important definitions

### FCC (availability)

- **Location:** a broadband serviceable location from the FCC's Fabric, meaning a home or business where fixed broadband could be installed. One row in the FCC file is one location. Locations are not people or households.
- **Available ≠ subscribed.** The FCC data shows what providers report they **could** install (within 10 business days, with no extra construction charge). It doesn't show whether anyone actually subscribes.
- **Terrestrial** means every technology **except satellite** (codes 60 and 61): copper (10), cable (40), fiber to the premises (50), unlicensed fixed wireless (70), licensed fixed wireless (71), licensed-by-rule fixed wireless (72), and other (0).
- **100/20** means at least 100 Mbps download and 20 Mbps upload, maximum advertised speed.
- **No terrestrial service** means `terrestrial_dl100_ul20 == 0` in the FCC file. These locations may still have satellite service.

Sources:
- FCC, *Data Specifications for Broadband Map Data Downloads*, August 11, 2026, rev. 2.4.3 (technology codes and wired/terrestrial definitions, §3.1.1.2).
- FCC, *Broadband Data Collection: Data Specifications for Availability Data*, November 25, 2024, rev. 2.1.3 (definition of "available," p. 21).

### ACS (who lives there, and whether they subscribe)

- **Household:** the people living in one housing unit. B28002 and B28004 count households. B28005 counts people living in households, and B28007 counts civilians aged 16 and over.
- **Internet subscription:** a service someone **pays for** to access the internet. This measures **adoption**, not availability.
- **ACS "broadband" includes cellular data plans.** A household whose only internet is a phone plan counts as having broadband. That's broader than the FCC's "terrestrial."
- **No broadband subscription** = "Internet access without a subscription" + "No Internet access" + "Dial-up with no other type of Internet subscription" (B28002). Dial-up isn't broadband, so dial-up-only households count as having no broadband.
- **Cellular data only / satellite only:** the household's only subscription is that type (B28002).
- **Under $35,000:** the three lowest income brackets in B28004 (under $10k, $10–20k, $20–35k), in 2024 dollars.
- **Children without a broadband subscription:** under 18 and either living with a computer but no internet subscription, or living with no computer (B28005).
- **Unemployed:** unemployed ÷ civilian labor force (B28007).
- **Margin of error (MOE):** every ACS estimate comes with one, at a 90% confidence level. The `_moe` columns in the database hold them. A rate of 18.5 ± 1.7 means the true value is between 16.8% and 20.2% with 90% confidence.
- **Coefficient of variation (CV):** the standard error divided by the estimate, where the standard error is MOE ÷ 1.645. It shows how noisy an estimate is relative to its size. This project treats **CV < 30%** (0.3 in the Excel analysis) as reliable enough to use.

Source: U.S. Census Bureau, *American Community Survey, ACS 5-Year Estimates Detailed Tables*, Tables B28002, B28004, B28005, B28007, 2024 (2020–2024).

### Analysis terms

- **Pooled rate (the "Total" rows):** the rate for a group of clusters treated as one area. Add up the counts, then divide. For example, households without a subscription in every cluster ÷ all households in those clusters. Each measure is pooled over its own base:
  - households for subscriptions and income
  - locations for unserved
  - people for 65+
  - children for children without broadband
  - labor force for unemployment

  It isn't a simple average of the cluster percentages, which would count a 545-household cluster the same as a 42,052-household one.
- **Percentage points:** the difference between two percentages (17.2% − 9.9% = 7.3 points). It isn't the same as "7.3% higher".

---

## Data

The input files are **not in git** (`.gitignore` excludes all of `data/`). Put them in `data/`:

| File | Source | Notes |
|---|---|---|
| `bdc_37_fixed_broadband_served_unserved_J26_24oct2026.csv` | FCC National Broadband Map → Data Download → North Carolina, fixed broadband, **June 30, 2026** data, served/unserved summary | ~217 MB, 4,435,342 rows. **Don't open it in Excel** (see warning below). |
| `tl_2020_37_tract.zip` | Census Bureau 2020 TIGER/Line tracts for NC: `https://www2.census.gov/geo/tiger/TIGER2020/TRACT/tl_2020_37_tract.zip` | Keep it zipped. 2020 tracts match the FCC's 2020 Census blocks. The county borders on the maps are made from this file too (tracts dissolved by county). |
| `ncstate_county_geoids.csv` | Copied from Geocodio, *North Carolina GEOIDs*: `https://www.geocod.io/geoids/north-carolina` | County name and 5-digit FIPS lookup. Its `Tracts` column uses 2010 tract counts and isn't used. |
| `B28002 …`, `B28004 …`, `B28005 …`, `B28007 …` (four folders) | data.census.gov → search the table ID → release **"2024: ACS 5-Year Estimates Detailed Tables"** → geography **All Census Tracts within North Carolina** → Download Table | Keep each downloaded folder as it is. The notebook reads `ACSDT5Y2024.<table>-Data.csv` from each. Each has 2,672 tracts plus one statewide row. |
| `broadband.db` | **Generated** by the notebooks (see Database) | Don't edit it by hand. It can be rebuilt from the files above. |

> ⚠️ **Opening the FCC CSV in Excel and saving it damages it.** Excel converts `block_geoid` to scientific notation, which loses digits, and cuts the file off at 1,048,576 rows. This happened once during this project. Keep the file read-only (Properties → Read-only) and inspect it with pandas.

> ⚠️ **Check the ACS release before downloading.** data.census.gov can default to an older release (one download in this project came back as 2016–2020). The file names should start with `ACSDT5Y2024`.

**Quick checks:**
- The FCC file should have **4,435,342** rows, with every `block_geoid` 15 characters long.
- Each ACS `-Data.csv` should have 2,673 data rows (2,672 tracts + the state).
- The household total should be **4,282,345**.
- The statewide "no broadband subscription" rate should be **9.9%**.

---

## Database

The notebooks pass data to each other through one SQLite file, `data/broadband.db`, instead of Excel. SQLite keeps each column's type (GEOIDs stay text), and the table rules catch bad data when it's loaded.

| Table | One row is | Primary key | Rows | Written by |
|---|---|---|---|---|
| `fcc_tracts` | a tract with FCC locations | `geoid` | 2,654 | `broadband-unavailability.ipynb` |
| `tract_clusters` | a flagged tract at one threshold | `(geoid, threshold)` | 0%: 2,616 · 5%: 466 · 15%: 120 · 25%: 34 · 50%: 5 | `broadband-unavailability.ipynb` |
| `acs_subs` | a tract (B28002) | `geoid` | 2,672 | `demographics.ipynb` |
| `acs_income` | a tract (B28004) | `geoid` | 2,672 | `demographics.ipynb` |
| `acs_age` | a tract (B28005) | `geoid` | 2,672 | `demographics.ipynb` |
| `acs_labor` | a tract (B28007) | `geoid` | 2,672 | `demographics.ipynb` |
| `acs_state` | North Carolina (all four tables, columns prefixed `subs_`, `income_`, `age_`, `labor_`) | none | 1 | `demographics.ipynb` |

- **`geoid`** is the 11-digit tract ID (state 2 + county 3 + tract 6), stored as text, in every table. Join tables on it.
- **All tables except `acs_state` are `STRICT`**, with `NOT NULL` and `CHECK` rules. For example, a GEOID must be 11 characters, and unserved locations can't exceed total locations.
- **Where the schemas are defined:**
  - `fcc_tracts` and `tract_clusters`: written by hand in `database.ipynb` (cell 1)
  - ACS tables: generated from the DataFrame's column names in `demographics.ipynb` (cell 5), and rebuilt each time it runs
- **Loading pattern:** `DELETE` the old rows, then `to_sql(..., if_exists="append")`. Don't use `if_exists="replace"` on these tables, because it replaces the schema with pandas' guess and drops the keys and rules. There's no `commit()` between the `DELETE` and the append, so if the append fails, the old rows stay.
- **`tract_clusters` keeps every threshold that has been run.** Running a threshold again replaces only that threshold's rows. A threshold where no tract qualifies (75% and up) adds no rows.
- **Threshold-dependent results are queries, not tables.** The flagged-tract list and the county table can be reproduced from `fcc_tracts` with `WHERE` / `GROUP BY` / `HAVING`.
- **Column names don't start with digits** (`inc_10_20k`, `age_18_64`). SQLite reads an unquoted `18_64` as the number 1864.
- **Zero-household tracts store 0, not blank.** 32 ACS tracts (parks, water, military land) have no households. A rate like `x / total` comes out `NULL` for them, which is correct. At 5% and 15%, one cluster (a Durham tract) has no households, so its rates and MOEs are blank.

**Reading safely:** to only read, connect with `sqlite3.connect("file:data/broadband.db?mode=ro", uri=True)`. A read-only connection can't drop or change anything.

---

## How to run

Requires [uv](https://docs.astral.sh/uv/) and Python 3.13.

1. In this folder, run `uv sync` to create `.venv` from `pyproject.toml` and `uv.lock`. Select the `.venv` kernel in VS Code.
2. **New database only:** in `database.ipynb`, set `RESET = True`, run cells 0–1 to create the two FCC tables, then set `RESET = False` again. **Running cell 1 with `RESET = True` deletes all FCC and cluster data.**
3. `broadband-unavailability.ipynb`: set `threshold` in cell 7, then **Restart Kernel → Run All**. Writes `fcc_tracts` and `tract_clusters`, saves the maps to `results/`, and stores `threshold` with `%store` for `database.ipynb`.
4. `demographics.ipynb`: **Restart Kernel → Run All**. Writes the five `acs_*` tables. It only needs to run again if the ACS files change.
5. `database.ipynb` (with `RESET = False`): **Restart Kernel → Run All**. Cell 0 reads `threshold` back with `%store -r`, so it always uses **the last threshold `broadband-unavailability.ipynb` ran**. The query cells write or replace that threshold's sheet in the four Excel files in `results/`.

To add a threshold: repeat steps 3 and 5 with the new value.

> **Shut down other kernels before step 2.** SQLite allows only one writer at a time. A notebook or DB Browser holding unsaved changes causes `database is locked`.

> Shut down the notebook kernel before running `uv add` or `uv sync`. If the kernel is running while uv changes `.venv`, the environment can be left broken.

> **Close the Excel files before step 5.** Excel locks open workbooks, and `pd.ExcelWriter` will fail with a permission error.

---

## Method

### Coverage (`broadband-unavailability.ipynb`)

1. **Load** the FCC served/unserved file and convert the 0/1 columns to True/False.
2. **Build IDs** from `block_geoid` (15 digits = state 2 + county 3 + tract 6 + block 4):
   - county code = digits 3–5
   - tract ID = first 11 digits. The 6-digit tract code alone repeats across counties, so the county must be included.
3. **Add county names** by merging the county lookup on the 3-digit county code.
4. **Tract table:** group all locations by tract, and count total locations, locations with no terrestrial 100/20, and the percentage. Save **every** tract to `fcc_tracts` (no threshold filter).
5. **Flag tracts** with at least 100 locations (to drop tiny tracts where one or two locations swing the percentage) and ≥ `threshold` % without service.
6. **Attach shapes:** load 2020 TIGER/Line tracts, reproject to **EPSG:32119** (NAD83 / North Carolina, meters), and left-merge the flagged tracts on the 11-digit `GEOID`. Dissolve the tracts by `COUNTYFP` to get county shapes for the border overlay.
7. **Find touching tracts:** spatial join of the flagged tracts with themselves (`predicate="touches"`, so tracts touching only at a corner count).
8. **Cluster:** build a NetworkX graph (tracts = nodes, touching pairs = edges). Each connected component is one cluster.
9. **Rank clusters:** sum locations and unserved locations per cluster. The percentage is calculated from those sums, not by averaging tract percentages. Sort by unserved locations.
10. **Map** all flagged tracts, and the top 3 clusters, with county borders drawn on top. Save each flagged tract's cluster and rank to `tract_clusters`.

### Demographics (`demographics.ipynb`)

1. **Load** the four ACS `-Data.csv` files, using the second header row (the long labels).
2. **Build `geoid`** from the last 11 characters of `GEO_ID`.
3. **Rename columns** by splitting each label on `!!` and mapping each piece to a short name (e.g. `Margin of Error!!Total:!!$20,000 to $34,999:!!Without an Internet subscription` → `inc_20_35k_no_sub_moe`).
4. **Convert to whole numbers**, generate a `STRICT` schema with `geoid` as the primary key, and load the tracts. The statewide row goes to `acs_state`.

### Analysis (`database.ipynb`)

- **Clusters (cell 2):** join `tract_clusters` to `fcc_tracts` and `acs_subs` on `geoid` and group by cluster rank. The statewide row uses `acs_state` and the sum of `fcc_tracts`.
- **Flagged vs. not flagged (cell 3):** start from `fcc_tracts`, `LEFT JOIN` `tract_clusters` (with the threshold **in the `ON` clause**) and the ACS tables on `geoid`, and group by whether a cluster matched.
- **Cluster and statewide demographics (cells 4–5):** income, age, children and unemployment per cluster rank, and the same rates for `acs_state`.
- **Margins of error (cells 6–8):** for each cluster and measure, following the Census Bureau's ACS formulas:
  - MOE of a sum = √(Σ MOE²). When several parts are zero estimates, only the largest of their MOEs is counted, not every one.
  - MOE of a proportion p = X / Y is (1/Y) · √(MOE_X² − p² · MOE_Y²). If the value under the root is negative, the formula uses + instead.
  - CV = (MOE / 1.645) / p.
- **Rules followed:**
  - sum counts, then divide (no averaging of percentages)
  - multiply by `100.0` first, to avoid integer division
  - join on a table's full key
  - check `COUNT(*)` against `COUNT(DISTINCT geoid)` after joins

### Excel analysis (working copy)

1. Copy the cluster table for each threshold into one sheet, and add each cluster's no-subscription CV from `Clusters MOE.xlsx`.
2. Use `FILTER` to list the clusters with CV < 0.3 and at least one household.
3. Compute pooled Totals for both lists with `SUMPRODUCT(rate, base) / SUM(base)`. Compare them with the statewide row.
4. Build correlation matrices and regressions (Data Analysis ToolPak) for all clusters and for the CV < 0.3 clusters.

---

## Repository layout

```
broadband-unavailability.ipynb   coverage gaps: tract table, flags, clusters, maps → database
demographics.ipynb               cleans the four ACS tables → database
database.ipynb                   table definitions (guarded by RESET), analysis and MOE queries → Excel
data/                            input files and broadband.db (not in git)
results/                         maps (PNG) for each threshold and the four SQL-output Excel files
pyproject.toml                   dependencies (pandas, geopandas, matplotlib, networkx, openpyxl, ipykernel)
uv.lock                          exact package versions
```

---

## Limitations

- **Provider-reported availability.** The BDC is based on what providers report and is checked through FCC challenges. It isn't independently measured.
- **Availability, not adoption.** A "served" location may not subscribe. The ACS subscription data helps here, but cost, devices and digital skills still aren't measured directly.
- **Satellite is excluded on purpose.** Satellite is reported almost everywhere (only 86 NC locations lack 100/20 from any technology), so including it would hide the gaps this project is looking for.
- **The terrestrial definition is broad.** It includes unlicensed fixed wireless, which is generally considered less reliable. A stricter "wired + licensed fixed wireless" measure hasn't been run yet.
- **The results depend on the rule.** The threshold, the minimum tract size, and counting corner contact as touching are all choices. The threshold table above shows how much the regions change. At 5% the clusters are huge regions, and at 50% the far west splits into separate pieces.
- **Group differences, not individual ones (ecological fallacy).** "Flagged tracts have more low-income households" is supported. "The unserved homes are low-income" is not, because the data can't link individual homes. The same applies to the regressions: they compare clusters, not households.
- **Small-area ACS estimates are noisy.** Single-tract clusters often have CVs above 30%. The group totals and the large clusters are reliable. Small clusters should be read with their MOE.
- **The regressions are exploratory:**
  - **Few observations per predictor.** There are 5 predictors and only 12–44 clusters (12 at 25%), so the models can overfit. Use the adjusted R², and don't read much into any single coefficient.
  - **One predictor overlaps with the outcome.** "Children without broadband" counts children living in households without a subscription, which is part of what the dependent variable measures. That inflates R². Leaving it out is a cleaner test.
  - **The p-values ignore the MOEs.** They treat every cluster's rate as exact, and they count a 1-tract cluster the same as a 28-tract one.
- **Different units.** FCC = locations (including businesses); B28002/B28004 = households; B28005 = people in households; B28007 = civilians 16+.
- **Time gap.** The ACS covers 2020–2024. The FCC data is a June 2026 snapshot.
- **Tracts with no FCC data are excluded from the comparison.** 18 ACS tracts have no FCC locations. 16 have no households. The other 2 are in Onslow County (`37133000700`, `37133000901`) and have **2,673 households** between them. Their coverage is unknown, so they're in neither group.

---

## Known issues

- **The Excel analysis isn't in the repo yet.** It lives in a working copy outside this folder. When adding it, **save it under a different name** (for example `results/Cluster Analysis.xlsx`). `database.ipynb` writes to `Clusters of Flagged Tracts.xlsx` with `if_sheet_exists="replace"`, so it would overwrite the analysis sheets (`Clusters 5%`, `Clusters 15%`, …) if they were in that file.
- **The committed `broadband-unavailability.ipynb` doesn't match its own outputs.** Cell 7 says `threshold = 25`, but the saved outputs (120 flagged tracts) are from the 15% run. Do Restart Kernel → Run All before committing, so that the code and outputs agree. Also check which threshold `%store` last saved before running `database.ipynb`.
- **Only `Clusters MOE.xlsx` is created if it's missing.** The other three Excel files are opened with `mode="a"`, which fails if the file doesn't exist (for example after deleting `results/`). The `if Path(...).is_file()` check from cell 8 can be reused.
- **Column name typo** in `database.ipynb` (cells 3–7): `pct_labor_froce_unemployed`. It was fixed in the Excel working copy, but not in the SQL.
- **The two "no broadband" measures treat dial-up differently.** The household measure counts dial-up-only as no broadband. The children measure doesn't, because it only uses `age_u18_computer_no_sub + age_u18_no_computer`. Adding dial-up changes the children's rate very little (6.67% vs. 6.58% for flagged tracts at 15%), but the two should match.
- **The cluster query in `database.ipynb` (cell 2) uses a comma join** (`FROM tract_clusters AS c, acs_subs AS s JOIN …`). It gives the correct result. But if one join condition were left out, it would silently produce every combination of rows instead of an error. Writing a separate `JOIN … ON` for each table is safer.
- **The statewide demographics query (cell 5) wraps values in `SUM()`** on a one-row table. It works, but the `SUM` doesn't do anything and can be removed.
- `Demographics.xlsx` only has the 25% sheet so far. Re-run `database.ipynb` at 5% and 15% to fill it in.
- The merged map table is named `map`, which hides Python's built-in `map()` function. It works here, but a different name (for example `tracts_map`) avoids confusion.
- `block_geoid` is read as a number and then converted to text. That works for NC, because state code 37 has no leading zero, but reading it as text when loading (`dtype={"block_geoid": str}`) is safer.

---

## Credits

- **Analysis and code:** written by the project author. As the notebooks state, the cells from the tract-shapes step onward were written by hand with step-by-step guidance from Claude. The SQLite work (table schemas, loading, and the analysis queries) was also written by the author, following a step-by-step walkthrough from Claude. The **margin-of-error query and pivot (`database.ipynb` cells 6–7) were written entirely by Claude**, as marked in the notebook. The Excel analysis (cluster tables, CV filter, correlations, regressions) was built by the author. Claude corrected the weighting in its Total formulas, removed the zero-household cluster from the CV < 0.3 lists, and fixed its layout and labels. Claude also explained code, helped debug, and checked results.
- **Data:** Federal Communications Commission, Broadband Data Collection (June 30, 2026 data). U.S. Census Bureau, 2020 TIGER/Line Shapefiles. U.S. Census Bureau, American Community Survey 2020–2024 5-Year Estimates, Tables B28002, B28004, B28005, B28007.
