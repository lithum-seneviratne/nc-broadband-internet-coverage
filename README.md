> **This README was written by Claude** (Anthropic's AI assistant, model Claude Opus 5.5, running in Claude Code) at the author's request. First version November 3, 2026; updated November 5, 2026, based on the repository at commit `83c1cb9`.

# Who in North Carolina is not served by terrestrial broadband?

E102 Final Project, Topic 5: Rural Broadband Access, population analysis.

This project uses the FCC's Broadband Data Collection (BDC) to find the census tracts in North Carolina where a large share of locations have **no terrestrial 100/20 Mbps service available**. It groups touching tracts into regions and ranks the regions by how many locations lack service. It then uses American Community Survey (ACS) data to describe **who lives** in those tracts: income, age, internet subscriptions, and employment.

> **Status (November 5, 2026):** the coverage analysis and the demographic comparison are complete. Still to do: margins of error for the demographic results, and other thresholds and a stricter service definition (robustness).

---

## Results

### Coverage

Rule: a tract is **flagged** when at least **15%** of its locations have no terrestrial 100/20 service and it has at least **100** locations.

- Statewide: **147,066 of 4,435,342** broadband serviceable locations (3.3%) have no terrestrial 100/20 service.
- **120 tracts** meet the rule. Touching flagged tracts form **45 clusters**. **27 of the 45 clusters are a single tract**, so only the top few are real multi-tract regions.
- The 100-location minimum excludes 7 tracts that would otherwise qualify. Together they have 75 unserved locations out of 99, and most are special-use tracts (parks, airports, water).

| Rank | Counties | Tracts | Locations | No terrestrial 100/20 | % | Households | % households with no internet subscription |
|---|---|---|---|---|---|---|---|
| 1 | Cherokee, Clay, Graham, Jackson, Macon, Swain, Transylvania | 28 | 66,793 | 22,587 | 33.8 | 42,052 | 18.3 |
| 2 | Henderson, Polk, Transylvania | 8 | 15,808 | 3,936 | 24.9 | 11,787 | 8.7 |
| 3 | Franklin, Halifax, Nash, Warren | 9 | 14,403 | 3,341 | 23.2 | 10,755 | 24.9 |
| 4 | Anson, Cabarrus, Stanly | 8 | 13,245 | 2,949 | 22.3 | 10,312 | 11.9 |
| 5 | Buncombe, McDowell, Rutherford | 9 | 12,429 | 2,709 | 21.8 | 8,867 | 20.2 |

**The largest region is the far west**, with almost six times as many unserved locations as the next cluster.

**Availability and adoption are different problems.** Clusters 2 and 3 have similar coverage gaps (24.9% and 23.2% of locations unserved), but in cluster 2 only 8.7% of households have no internet subscription, below the state's 9.8%. In cluster 3 it's 24.9%, more than twice the state rate.

### Who lives in flagged tracts

Flagged tracts at 15%, compared with all other tracts that have FCC data and with North Carolina as a whole. Every rate is calculated from summed counts, not by averaging tract percentages.

| Measure | Flagged tracts | Not flagged | North Carolina |
|---|---|---|---|
| Tracts | 120 | 2,534 | 2,672 |
| Households | 163,982 | 4,115,690 | 4,282,345 |
| People in households | 389,763 | 10,053,207 | 10,451,237 |
| % households with **no internet subscription** | **16.9** | 9.5 | 9.8 |
| % households with **cellular data only** | 13.5 | 10.3 | 10.4 |
| % households with **satellite only** | 1.6 | 0.3 | 0.4 |
| % households earning **under $35,000** | 27.7 | 23.1 | 23.2 |
| % of people **aged 65+** | 23.1 | 17.0 | 17.2 |
| % of **children** (under 18) without a broadband subscription | 6.6 | 4.5 | 4.6 |
| % of the labor force **unemployed** | 4.7 | 4.6 | 4.6 |

In flagged tracts, households are **about 1.8 times as likely to have no internet subscription** and **5 times as likely to rely only on satellite**. They are also lower-income and older than the rest of the state. Unemployment is about the same.

These are differences between **groups of tracts**. They don't show that the specific homes without service are the lower-income or older ones (see Limitations).

The tables are in `results/Flagged vs. Not Flagged Tracts Demographics Analysis.xlsx` (income, age, children, unemployment) and `results/Clusters of Flagged Tracts.xlsx` (all 45 clusters). The subscription rows above (no subscription, cellular only, satellite only) come from the same kind of query on `acs_subs` but aren't in either Excel file yet. Maps for each threshold that has been run (15%, 20%, 50%) are in `results/`.

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
- **No internet subscription** = "Internet access without a subscription" + "No Internet access" (B28002).
- **Cellular data only / satellite only:** the household's only subscription is that type (B28002).
- **Under $35,000:** the three lowest income brackets in B28004 (under $10k, $10–20k, $20–35k), in 2024 dollars.
- **Children without a broadband subscription:** under 18 and either living with a computer but no internet subscription, or living with no computer (B28005).
- **Unemployed:** unemployed ÷ civilian labor force (B28007).
- **Margin of error (MOE):** every ACS estimate comes with one, at a 90% confidence level. The `_moe` columns in the database hold them.

Source: U.S. Census Bureau, *American Community Survey, ACS 5-Year Estimates Detailed Tables*, Tables B28002, B28004, B28005, B28007, 2024 (2020–2024).

---

## Data

The input files are **not in git** (`.gitignore` excludes all of `data/`). Put them in `data/`:

| File | Source | Notes |
|---|---|---|
| `bdc_37_fixed_broadband_served_unserved_J26_24oct2026.csv` | FCC National Broadband Map → Data Download → North Carolina, fixed broadband, **June 30, 2026** data, served/unserved summary | ~217 MB, 4,435,342 rows. **Don't open it in Excel** (see warning below). |
| `tl_2020_37_tract.zip` | Census Bureau 2020 TIGER/Line tracts for NC: `https://www2.census.gov/geo/tiger/TIGER2020/TRACT/tl_2020_37_tract.zip` | Keep it zipped. 2020 tracts match the FCC's 2020 Census blocks. |
| `ncstate_county_geoids.csv` | Copied from Geocodio, *North Carolina GEOIDs*: `https://www.geocod.io/geoids/north-carolina` | County name and 5-digit FIPS lookup. Its `Tracts` column uses 2010 tract counts and isn't used. |
| `B28002 …`, `B28004 …`, `B28005 …`, `B28007 …` (four folders) | data.census.gov → search the table ID → release **"2024: ACS 5-Year Estimates Detailed Tables"** → geography **All Census Tracts within North Carolina** → Download Table | Keep each downloaded folder as it is. The notebook reads `ACSDT5Y2024.<table>-Data.csv` from each. Each has 2,672 tracts plus one statewide row. |
| `broadband.db` | **Generated** by the notebooks (see Database) | Don't edit it by hand. It can be rebuilt from the files above. |

> ⚠️ **Opening the FCC CSV in Excel and saving it damages it.** Excel converts `block_geoid` to scientific notation, which loses digits, and cuts the file off at 1,048,576 rows. This happened once during this project. Keep the file read-only (Properties → Read-only) and inspect it with pandas.

> ⚠️ **Check the ACS release before downloading.** data.census.gov can default to an older release (one download in this project came back as 2016–2020). The file names should start with `ACSDT5Y2024`.

**Quick checks:** the FCC file should have **4,435,342** rows, with every `block_geoid` 15 characters long. Each ACS `-Data.csv` should have 2,673 data rows (2,672 tracts + the state), and the household total should be **4,282,345**.

---

## Database

The notebooks pass data to each other through one SQLite file, `data/broadband.db`, instead of Excel. SQLite keeps each column's type (GEOIDs stay text), and the table rules catch bad data when it's loaded.

| Table | One row is | Primary key | Rows | Written by |
|---|---|---|---|---|
| `fcc_tracts` | a tract with FCC locations | `geoid` | 2,654 | `broadband-unavailability.ipynb` |
| `tract_clusters` | a flagged tract at one threshold | `(geoid, threshold)` | 120 (threshold 15) | `broadband-unavailability.ipynb` |
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
- **Threshold-dependent results are queries, not tables.** The flagged-tract list and the county table can be reproduced from `fcc_tracts` with `WHERE` / `GROUP BY` / `HAVING`.
- **Column names don't start with digits** (`inc_10_20k`, `age_18_64`). SQLite reads an unquoted `18_64` as the number 1864.
- **Zero-household tracts store 0, not blank.** 32 ACS tracts (parks, water, military land) have no households. A rate like `x / total` comes out `NULL` for them, which is correct.

**Reading safely:** to only read, connect with `sqlite3.connect("file:data/broadband.db?mode=ro", uri=True)`. A read-only connection can't drop or change anything.

---

## How to run

Requires [uv](https://docs.astral.sh/uv/) and Python 3.13.

1. In this folder, run `uv sync` to create `.venv` from `pyproject.toml` and `uv.lock`. Select the `.venv` kernel in VS Code.
2. **New database only:** in `database.ipynb`, set `RESET = True`, run cells 0–1 to create the two FCC tables, then set `RESET = False` again. **Running cell 1 with `RESET = True` deletes all FCC and cluster data.**
3. `broadband-unavailability.ipynb`: set `threshold` (default `15`) in cell 7, then **Restart Kernel → Run All**. Writes `fcc_tracts` and `tract_clusters`, and saves the maps to `results/`.
4. `demographics.ipynb`: **Restart Kernel → Run All**. Writes the five `acs_*` tables.
5. `database.ipynb` (with `RESET = False`): run the query cells. They write the two Excel files in `results/`.

> **Shut down other kernels before step 2.** SQLite allows only one writer at a time. A notebook or DB Browser holding unsaved changes causes `database is locked`.

> Shut down the notebook kernel before running `uv add` or `uv sync`. If the kernel is running while uv changes `.venv`, the environment can be left broken.

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
6. **Attach shapes:** load 2020 TIGER/Line tracts, reproject to **EPSG:32119** (NAD83 / North Carolina, meters), and left-merge the flagged tracts on the 11-digit `GEOID`.
7. **Find touching tracts:** spatial join of the flagged tracts with themselves (`predicate="touches"`, so tracts touching only at a corner count).
8. **Cluster:** build a NetworkX graph (tracts = nodes, touching pairs = edges). Each connected component is one cluster.
9. **Rank clusters:** sum locations and unserved locations per cluster. The percentage is calculated from those sums, not by averaging tract percentages. Sort by unserved locations.
10. **Map** all flagged tracts, and the top 3 clusters. Save each flagged tract's cluster and rank to `tract_clusters`.

### Demographics (`demographics.ipynb`)

1. **Load** the four ACS `-Data.csv` files, using the second header row (the long labels).
2. **Build `geoid`** from the last 11 characters of `GEO_ID`.
3. **Rename columns** by splitting each label on `!!` and mapping each piece to a short name (e.g. `Margin of Error!!Total:!!$20,000 to $34,999:!!Without an Internet subscription` → `inc_20_35k_no_sub_moe`).
4. **Convert to whole numbers**, generate a `STRICT` schema with `geoid` as the primary key, and load the tracts. The statewide row goes to `acs_state`.

### Analysis (`database.ipynb`)

- **Flagged vs. not flagged:** start from `fcc_tracts`, `LEFT JOIN` `tract_clusters` (with the threshold **in the `ON` clause**) and the ACS tables on `geoid`, and group by whether a cluster matched.
- **Clusters:** join `tract_clusters` to `fcc_tracts` and `acs_subs` on `geoid`, and group by cluster rank.
- **Rules followed:**
  - sum counts, then divide (no averaging of percentages)
  - multiply by `100.0` first, to avoid integer division
  - join on a table's full key
  - check `COUNT(*)` against `COUNT(DISTINCT geoid)` after joins

---

## Repository layout

```
broadband-unavailability.ipynb   coverage gaps: tract table, flags, clusters, maps → database
demographics.ipynb               cleans the four ACS tables → database
database.ipynb                   table definitions (guarded by RESET) and the analysis queries → Excel
data/                            input files and broadband.db (not in git)
results/                         maps (PNG) and the final Excel tables
results/available/               old output of broadband-availability.ipynb (removed; see Known issues)
pyproject.toml                   dependencies (pandas, geopandas, matplotlib, networkx, openpyxl, ipykernel)
uv.lock                          exact package versions
```

---

## Limitations

- **Provider-reported availability.** The BDC is based on what providers report and is checked through FCC challenges. It isn't independently measured.
- **Availability, not adoption.** A "served" location may not subscribe. The ACS subscription data helps here, but cost, devices and digital skills still aren't measured directly.
- **Satellite is excluded on purpose.** Satellite is reported almost everywhere (only 86 NC locations lack 100/20 from any technology), so including it would hide the gaps this project is looking for.
- **The terrestrial definition is broad.** It includes unlicensed fixed wireless, which is generally considered less reliable. A stricter "wired + licensed fixed wireless" measure hasn't been run yet.
- **The results depend on the rule.** The threshold, the minimum tract size, and counting corner contact as touching are all choices. At 50%, for example, only 5 tracts qualify and the far west splits into separate pieces.
- **Group differences, not individual ones (ecological fallacy).** "Flagged tracts have more low-income households" is supported. "The unserved homes are low-income" is not, because the data can't link individual homes.
- **Margins of error aren't reported yet.** Tract-level ACS estimates are noisy. The group and cluster totals are more reliable than single tracts. For a sum, MOE = √(Σ MOE²).
- **Different units.** FCC = locations (including businesses); B28002/B28004 = households; B28005 = people in households; B28007 = civilians 16+.
- **Time gap.** The ACS covers 2020–2024. The FCC data is a June 2026 snapshot.
- **Tracts with no FCC data are excluded from the comparison.** 18 ACS tracts have no FCC locations. 16 have no households. The other 2 are in Onslow County (`37133000700`, `37133000901`) and have **2,673 households** between them. Their coverage is unknown, so they're in neither group.

---

## Known issues

- **The cluster query in `database.ipynb` uses a comma join** (`FROM tract_clusters AS c, acs_subs AS s JOIN …`). It gives the correct result. But if one join condition were left out, it would silently produce every combination of rows instead of an error. Writing a separate `JOIN … ON` for each table is safer.
- **"Children without broadband" leaves out dial-up-only households.** Including them changes the result very little: 6.67% vs. 6.58% for flagged tracts, 4.55% vs. 4.52% for unflagged.
- **Column name typo** in the flagged-tract query: `pct_labor_froce_unemployed`.
- **`results/available/` is left over from `broadband-availability.ipynb`**, which was removed in commit `217dc45` because it was out of date after the switch to SQLite. Nothing regenerates these files anymore. Their threshold means "% *with* coverage," the opposite of `results/`, so the two `…15…` files there mean something different from the 15% results here. The notebook is still in git history (commit `d61e0c5`). The folder can be deleted, or kept as an archive.
- The merged map table is named `map`, which hides Python's built-in `map()` function. It works here, but a different name (for example `tracts_map`) avoids confusion.
- `block_geoid` is read as a number and then converted to text. That works for NC, because state code 37 has no leading zero, but reading it as text when loading (`dtype={"block_geoid": str}`) is safer.

---

## Credits

- **Analysis and code:** written by the project author. As the notebooks state, the cells from the tract-shapes step onward were written by hand with step-by-step guidance from Claude. The SQLite work (table schemas, loading, and the analysis queries) was also written by the author, following a step-by-step walkthrough from Claude. Claude also explained code, helped debug, and checked results. A few cells use code Claude provided directly.
- **Data:** Federal Communications Commission, Broadband Data Collection (June 30, 2026 data). U.S. Census Bureau, 2020 TIGER/Line Shapefiles. U.S. Census Bureau, American Community Survey 2020–2024 5-Year Estimates, Tables B28002, B28004, B28005, B28007.
