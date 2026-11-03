> **This README was written by Claude** (Anthropic's AI assistant, model Claude Opus 5.5, running in Claude Code) at the author's request on November 3, 2026, based on the notebook and repository at commit `2735d7f`.

# Who in North Carolina is not served by terrestrial broadband?

E102 Final Project, Topic 5: Rural Broadband Access, population analysis.

This notebook uses the FCC's Broadband Data Collection (BDC) to find the census tracts in North Carolina where a large share of locations have **no terrestrial 100/20 Mbps service available**. It then groups touching tracts into regions and ranks the regions by how many locations lack service.

> **Status (November 3, 2026):** the core analysis is complete. Robustness testing, a stricter service definition, and demographic (ACS) data are still to do.

---

## Result so far

Rule: a tract is **flagged** when at least **15%** of its locations have no terrestrial 100/20 service and it has at least **100** locations.

- Statewide: **147,066 of 4,435,342** broadband serviceable locations (3.3%) have no terrestrial 100/20 service.
- **120 tracts** meet the rule. Touching flagged tracts form **45 clusters**.

| Rank | Counties | Tracts | Locations | No terrestrial 100/20 | % |
|---|---|---|---|---|---|
| 1 | Cherokee, Clay, Graham, Jackson, Macon, Swain, Transylvania | 28 | 66,793 | 22,587 | 33.8 |
| 2 | Henderson, Polk, Transylvania | 8 | 15,808 | 3,936 | 24.9 |
| 3 | Franklin, Halifax, Nash, Warren | 9 | 14,403 | 3,341 | 23.2 |
| 4 | Anson, Cabarrus, Stanly | 8 | 13,245 | 2,949 | 22.3 |
| 5 | Buncombe, McDowell, Rutherford | 9 | 12,429 | 2,709 | 21.8 |

**The largest region is the far west**, with almost six times as many unserved locations as the next cluster.

Maps and tables for each threshold that has been run (15%, 20%, 50%) are in `results/`.

---

## Important definitions

- **Location:** a broadband serviceable location from the FCC's Fabric, meaning a home or business where fixed broadband could be installed. One row in the FCC file is one location. Locations are not people or households.
- **Available ≠ subscribed.** The FCC data shows what providers report they **could** install (within 10 business days, with no extra construction charge). It doesn't show whether anyone actually subscribes.
- **Terrestrial** means every technology **except satellite** (codes 60 and 61): copper (10), cable (40), fiber to the premises (50), unlicensed fixed wireless (70), licensed fixed wireless (71), licensed-by-rule fixed wireless (72), and other (0).
- **100/20** means at least 100 Mbps download and 20 Mbps upload, maximum advertised speed.
- **No terrestrial service** means `terrestrial_dl100_ul20 == 0` in the FCC file. These locations may still have satellite service.

Sources for these definitions:
- FCC, *Data Specifications for Broadband Map Data Downloads*, August 11, 2026, rev. 2.4.3 (technology codes and wired/terrestrial definitions, §3.1.1.2).
- FCC, *Broadband Data Collection: Data Specifications for Availability Data*, November 25, 2024, rev. 2.1.3 (definition of "available," p. 21).

---

## Data

The data files are **not in git** (see `.gitignore`). Put them in `data/`:

| File | Source | Notes |
|---|---|---|
| `bdc_37_fixed_broadband_served_unserved_J26_24oct2026.csv` | FCC National Broadband Map → Data Download → North Carolina, fixed broadband, **June 30, 2026** data, served/unserved summary | ~217 MB, 4,435,342 rows. **Don't open it in Excel** (see warning below). |
| `tl_2020_37_tract.zip` | Census Bureau 2020 TIGER/Line tracts for NC: `https://www2.census.gov/geo/tiger/TIGER2020/TRACT/tl_2020_37_tract.zip` | Keep it zipped. 2020 tracts match the FCC's 2020 Census blocks. |
| `ncstate_county_geoids.csv` | Copied from Geocodio, *North Carolina GEOIDs*: `https://www.geocod.io/geoids/north-carolina` | County name and 5-digit FIPS lookup. Its `Tracts` column uses 2010 tract counts and isn't used. |

> ⚠️ **Opening the FCC CSV in Excel and saving it damages it.** Excel converts `block_geoid` to scientific notation, which loses digits, and cuts the file off at 1,048,576 rows. This happened once during this project. Keep the file read-only (Properties → Read-only) and inspect it with pandas.

**Quick check after loading:** `len(df)` should be **4,435,342**, and every `block_geoid` should be 15 characters long.

---

## How to run

Requires [uv](https://docs.astral.sh/uv/) and Python 3.13.

1. In this folder, run `uv sync` to create `.venv` from `pyproject.toml` and `uv.lock`.
2. Open `notebook.ipynb` in VS Code and select the `.venv` kernel.
3. Set `threshold` (default analysis: `15`) in the cell that creates `no_terra_service`.
4. **Restart Kernel → Run All.**

Outputs go to `results/`, named by threshold.

> Shut down the notebook kernel before running `uv add` or `uv sync`. If the kernel is running while uv changes `.venv`, the environment can be left broken.

---

## Method

1. **Load** the FCC served/unserved file and convert the 0/1 columns to True/False.
2. **Build IDs** from `block_geoid` (15 digits = state 2 + county 3 + tract 6 + block 4):
   - county code = digits 3–5
   - tract ID = first 11 digits. The 6-digit tract code alone repeats across counties, so the county must be included.
3. **Add county names** by merging the county lookup on the 3-digit county code.
4. **County and tract tables:** group all locations and count total locations, locations with no terrestrial 100/20, and the percentage.
5. **Flag tracts** with at least 100 locations (to drop tiny tracts where one or two locations swing the percentage) and ≥ `threshold` % without service. Export both tables to Excel.
6. **Attach shapes:** load 2020 TIGER/Line tracts, reproject to **EPSG:32119** (NAD83 / North Carolina, meters), and left-merge the flagged tracts on the 11-digit `GEOID`.
7. **Find touching tracts:** spatial join of the flagged tracts with themselves (`predicate="touches"`, so tracts touching only at a corner count).
8. **Cluster:** build a NetworkX graph (tracts = nodes, touching pairs = edges). Each connected component is one cluster.
9. **Rank clusters:** sum locations and unserved locations per cluster. The percentage is calculated from those sums, not by averaging tract percentages. Sort by unserved locations.
10. **Map** all flagged tracts, and the top 3 clusters.

---

## Repository layout

```
notebook.ipynb     the analysis
data/              input files (not in git)
results/           Excel tables and PNG maps, named by threshold
pyproject.toml     dependencies (pandas, geopandas, matplotlib, networkx, openpyxl, ipykernel)
uv.lock            exact package versions
```

---

## Limitations

- **Provider-reported availability.** The BDC is based on what providers report and is checked through FCC challenges. It isn't independently measured.
- **Availability, not adoption.** A "served" location may not subscribe. Cost, devices and digital skills aren't measured here.
- **Satellite is excluded on purpose.** Satellite is reported almost everywhere (only 86 NC locations lack 100/20 from any technology), so including it would hide the gaps this project is looking for.
- **The terrestrial definition is broad.** It includes unlicensed fixed wireless, which is generally considered less reliable. A stricter "wired + licensed fixed wireless" measure hasn't been run yet.
- **Locations, not people.** Results count places, not residents. Population and demographics require ACS data.
- **The results depend on the rule.** The threshold, the minimum tract size, and counting corner contact as touching are all choices. At 50%, for example, only 5 tracts qualify and the far west splits into separate pieces.

---

## Known issues

- The merged map table is named `map`, which hides Python's built-in `map()` function. It works here, but a different name (for example `tracts_map`) avoids confusion.
- `block_geoid` is read as a number and then converted to text. That works for NC, because state code 37 has no leading zero, but reading it as text when loading (`dtype={"block_geoid": str}`) is safer.

---

## Credits

- **Analysis and code:** written by the project author in `notebook.ipynb`. As the notebook states, the cells from the tract-shapes step onward were written by hand with step-by-step guidance from Claude. Claude also explained code and helped debug earlier cells. A few cells use code Claude provided directly.
- **Data:** Federal Communications Commission, Broadband Data Collection (June 30, 2026 data). U.S. Census Bureau, 2020 TIGER/Line Shapefiles.
