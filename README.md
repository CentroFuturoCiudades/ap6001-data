# ap6001-data

Slim, browser-ready extracts of Mexican public microdata for **AP6001 · Probabilidad y
estadística** (DPP26, Tecnológico de Monterrey).

The course notebooks run in the browser (marimo → WebAssembly) and read these files directly
over HTTPS, so the same three lines work on a laptop and in a student's browser:

```python
import sys, pandas as pd
if sys.platform == "emscripten":          # in the browser: let pandas fetch over HTTP
    import pyodide_http; pyodide_http.patch_all()

BASE = "https://raw.githubusercontent.com/CentroFuturoCiudades/ap6001-data/v1/"
enigh = pd.read_csv(BASE + "enigh_2024_hogares.csv.gz")
```

## Files

| File | Rows | Source |
|---|---:|---|
| `enigh_2024_hogares.csv.gz` | 91,414 households | INEGI **ENIGH 2024** (Nueva serie), `concentradohogar`, via [mxcensus](https://github.com/CentroFuturoCiudades/mxcensus) |
| `enoe_2025t1_ocupados.csv.gz` | 118,931 employed workers | INEGI **ENOE 2025 T1**, `SDEMT` |
| `pobreza_estados_2016_2024.csv.gz` | 33 units × 16 indicators × 5 years | INEGI, **Pobreza multidimensional 2024**, *Indicadores de pobreza según entidad federativa* (`pm_ef_2024.xlsx`; 2016–2022 are CONEVAL's estimates) |
| `pobreza_municipios_2020.csv.gz` | 2,469 municipalities | CONEVAL, **Medición de la pobreza municipal 2010–2020**, *Concentrado, indicadores de pobreza por municipio* |
| `pobreza_conteo_2024.csv.gz`, `pobreza_contribuciones_2024.csv.gz` | 33 units × rules; 33 units × 6 deprivations | INEGI, **Pobreza multidimensional 2024**, person-level file `pobreza_24.csv` of the *Algoritmo de cómputo (Python)* |
| `geo_estados.csv.gz`, `geo_municipios.csv.gz` | one row per boundary vertex | INEGI, **Marco Geoestadístico** (census 2020 vintage), state and municipal layers, via mxcensus; simplified |
| `midterm_enigh_2024.csv.gz` | 4,742 households in 580 PSUs | The midterm's frozen subsample of `enigh_2024_hogares.csv.gz` (same columns): within each state a seeded simple random sample of about 5.5% of the PSUs, with every household in them; `factor` is ENIGH's weight times the inverse of the state's PSU sampling fraction |
| `qss_minwage.csv.gz` | 358 fast-food restaurants | Card, D. & Krueger, A. B. (1994), *Minimum wages and employment*, *AER* 84(4), as distributed with Imai, **Quantitative Social Science** (2017), `minwage.csv` |
| `qss_star.csv.gz` | 6,325 pupils | The Tennessee **STAR** class-size experiment, as distributed with Imai, **Quantitative Social Science** (2017), `STAR.csv` |

Only the columns the course uses are kept; amounts are **monthly, current MXN** (ENIGH
publishes quarterly totals, divided by three here). Survey design columns are included —
ENIGH `factor`/`upm`/`est_dis`, ENOE `fac_tri`/`upm`/`est_d_tri` — because the course
estimates population quantities, not sample ones. Column definitions live with the course
materials (`data/README.md` in the course repository).

**Poverty tables.** `pobreza_estados_2016_2024` is long: `cve_ent` (0 = Mexico), `estado`,
`indicador`, `anio`, `porcentaje` (% of persons), `miles` (thousands of persons),
`carencias_promedio` (average number of the six social deprivations in the group; INEGI's
"carencias promedio") and, for 2024 only, INEGI's precision indicators for the percentage: `ee` (standard error), `li95`/`ls95`
(95% interval) and `cv` (coefficient of variation, %). `pobreza_municipios_2020` has
`cvegeo` (state × 1000 + municipality), the 2020 population and the poverty and extreme-poverty
percentages (poverty also for 2010 and 2015; `NaN` where CONEVAL reports *n.d.*).
`pobreza_conteo_2024` gives the headcount `H` (%), the intensity `A` and `M0` = H × A for every
second cutoff `k` = 1–6 under three income conditions (`ingreso`: `ninguna`, `lpi`, `lpei`); the
official rules are (`lpi`, 1) and (`lpei`, 3). `pobreza_contribuciones_2024` gives, at the
official poverty rule, each deprivation's rate, the share poor and deprived (`H_j`), the share of
the poor deprived and its contribution to `M0` (%).

**QSS classroom datasets** (all columns, unchanged). `qss_minwage`: `chain`, `location`
(`PA`, or one of four New Jersey regions: `centralNJ`, `northNJ`, `shoreNJ`, `southNJ`),
`wageBefore`/`wageAfter` (starting wage, USD per hour, before and after New Jersey's April
1992 increase), `fullBefore`/`fullAfter` and `partBefore`/`partAfter` (full- and part-time
employees; managers are not included). `qss_star`: `race` (1 white, 2 black, 3 Asian,
4 Hispanic, 5 Native American, 6 other), `classtype` (kindergarten assignment: 1 small,
2 regular, 3 regular with aide), `yearssmall` (years in a small class), `hsgrad` (graduated
from high school, 0/1), `g4math` and `g4reading` (4th-grade test scores; missing where not
observed).

**Boundaries.** For a choropleth with matplotlib alone (no geopandas, so it runs in the
browser): columns `cve_ent` or `cvegeo`, `ring`, `x`, `y` — vertices in km in INEGI's Lambert
conformal conic projection (EPSG:6372), exterior rings counter-clockwise and holes clockwise,
so each unit's rings form one compound `matplotlib.path.Path`. Simplified (Douglas–Peucker,
2 km for states, 0.8 km for municipalities; islands under 20 km² and 2 km² dropped): for
display, not for measuring areas.

Rebuilt by `data/make_web_extracts.py` (ENIGH, ENOE, QSS) and `data/get_coneval.py` (poverty tables
and boundaries) in the course repository; tags (`v1`, `v2`, …) pin a state of the extracts so
notebooks stay reproducible when they are regenerated.

## Provenance and licence

The underlying data are published by **INEGI** (Instituto Nacional de Estadística y
Geografía) as open data — see INEGI's terms of free use — and by **CONEVAL** (Consejo Nacional
de Evaluación de la Política de Desarrollo Social), whose measurement functions passed to INEGI
in 2025. These files are derived extracts — subsets of columns, monthly conversions, reshaped
tables and simplified boundaries — not official products, and neither institution endorses
them. The code that builds them is MIT-licensed with the course materials.
