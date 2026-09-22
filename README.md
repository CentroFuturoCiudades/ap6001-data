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

Only the columns the course uses are kept; amounts are **monthly, current MXN** (ENIGH
publishes quarterly totals, divided by three here). Survey design columns are included —
ENIGH `factor`/`upm`/`est_dis`, ENOE `fac_tri`/`upm`/`est_d_tri` — because the course
estimates population quantities, not sample ones. Column definitions live with the course
materials (`data/README.md` in the course repository).

Rebuilt by `data/make_web_extracts.py` in the course repository; tags (`v1`, `v2`, …) pin a
state of the extracts so notebooks stay reproducible when they are regenerated.

## Provenance and licence

The underlying microdata are published by **INEGI** (Instituto Nacional de Estadística y
Geografía) as open data; see INEGI's terms of free use. These files are derived extracts —
subsets of columns with monthly conversions — not official products, and INEGI does not
endorse them. The code that builds them is MIT-licensed with the course materials.
