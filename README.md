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
| `geo_estados.csv.gz`, `geo_municipios.csv.gz` | one row per boundary vertex | INEGI, **Marco Geoestadístico** (census 2020 vintage), state and municipal layers, via mxcensus; simplified |

Only the columns the course uses are kept; amounts are **monthly, current MXN** (ENIGH
publishes quarterly totals, divided by three here). Survey design columns are included —
ENIGH `factor`/`upm`/`est_dis`, ENOE `fac_tri`/`upm`/`est_d_tri` — because the course
estimates population quantities, not sample ones. Column definitions live with the course
materials (`data/README.md` in the course repository).

**Poverty tables.** `pobreza_estados_2016_2024` is long: `cve_ent` (0 = Mexico), `estado`,
`indicador`, `anio`, `porcentaje` (% of persons), `miles` (thousands of persons) and, for 2024
only, INEGI's precision indicators for the percentage: `ee` (standard error), `li95`/`ls95`
(95% interval) and `cv` (coefficient of variation, %). `pobreza_municipios_2020` has
`cvegeo` (state × 1000 + municipality), the 2020 population and the poverty and extreme-poverty
percentages (poverty also for 2010 and 2015; `NaN` where CONEVAL reports *n.d.*).

**Boundaries.** For a choropleth with matplotlib alone (no geopandas, so it runs in the
browser): columns `cve_ent` or `cvegeo`, `ring`, `x`, `y` — vertices in km in INEGI's Lambert
conformal conic projection (EPSG:6372), exterior rings counter-clockwise and holes clockwise,
so each unit's rings form one compound `matplotlib.path.Path`. Simplified (Douglas–Peucker,
2 km for states, 0.8 km for municipalities; islands under 20 km² and 2 km² dropped): for
display, not for measuring areas.

Rebuilt by `data/make_web_extracts.py` (ENIGH, ENOE) and `data/get_coneval.py` (poverty tables
and boundaries) in the course repository; tags (`v1`, `v2`, …) pin a state of the extracts so
notebooks stay reproducible when they are regenerated.

## Provenance and licence

The underlying data are published by **INEGI** (Instituto Nacional de Estadística y
Geografía) as open data — see INEGI's terms of free use — and by **CONEVAL** (Consejo Nacional
de Evaluación de la Política de Desarrollo Social), whose measurement functions passed to INEGI
in 2025. These files are derived extracts — subsets of columns, monthly conversions, reshaped
tables and simplified boundaries — not official products, and neither institution endorses
them. The code that builds them is MIT-licensed with the course materials.
