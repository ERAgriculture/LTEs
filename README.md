# ERA long-term experiments (LTEs) with seasonal climate

Part of the **ERA (Evidence for Resilient Agriculture)** data ecosystem. This repository turns the public ERA
compiled dataset into an analysis-ready table of **long-term agronomic experiments**, each observation joined to
the seasonal climate of its growing season. Funded by the CGIAR Excellence in Agronomy Initiative (EiA).

**Current release: `2026.1`** (built 2026-09-25 from ERA compiled release 2026.1). See [CHANGELOG.md](CHANGELOG.md).

| | |
|---|---|
| LTE rows (control-vs-treatment pairs) | 33,646 |
| Publications / long-term experiments / sites / countries | 293 / 244 / 286 / 30 |
| Crop-yield rows (with yield in t/ha) | 17,979 (17,970) |
| Rows with seasonal climate | 17,059 (9,388 on reported planting dates, 7,671 on EcoCrop calendars) |
| Arm measurements in `lte_arms.csv` (of which crop yield) | 24,971 (12,853) |

Numbers are written by the build to `data/build_report.csv`; provenance is in `data/VERSION.json`.

## Files in `data/`

| File | What it is |
|---|---|
| `lte_final.csv` | **The dataset.** One row per ERA comparison (control vs treatment, one outcome, one site-year). All 138 ERA compiled columns (minus the empty `Lat`/`Lon`), plus `LTE.ID`, analysis-ready columns and about 150 climate columns. |
| `lte_arms.csv` | **One row per arm measurement** (control or treatment) per outcome and year, with the climate attached. Use this for time series, yield-vs-climate plots and any per-treatment statistic: no repeated pairs, the controls are included, and `n_means` flags arms with several values for one outcome. |
| `data_dictionary.csv` | Every column of both files with a description and its source (ERA official descriptions, this repository, or ERA geodata). |
| `unique_ltes_clean.csv` | The curated LTE registry (from `CIAT/ERA_dev`) after cleaning: `LTE.ID`, publication `Code`, `Site.ID`, years, coordinates. |
| `unique_ltes_unresolved.csv` | Registry rows that could not be used: malformed rows, and publication codes that are not in the ERA compiled release. |
| `unmatched_climate_keys.csv` | Site × year × crop × window combinations with no climate, with the reason. |
| `build_report.csv`, `VERSION.json` | Counts per build step; ERA release, climate file and registry used. |
| `era_compiled_fields.csv` | ERA's own descriptions of the compiled columns (exported from the `eragri` package). Input to the dictionary. |
| `metadata.csv` | Field dictionary of the **raw ERA data model** (the extraction tables). Kept for reference; it does not describe `lte_final.csv`. |

## How to read the data correctly

- **Pairs vs arms.** ERA stores comparisons. The same treatment arm appears in `lte_final.csv` once per control it is
  compared with, so counting rows over-counts observations. `Index` identifies the pair; `obs_id` and `ctrl_id`
  identify the arms. `lte_arms.csv` is already de-duplicated and contains the control arms too.
- **Yield.** `yield_t_ha` (treatment) and `yield_c_t_ha` (control) are in t/ha for crop-yield rows; `yield_basis`
  says whether the value is as reported, dry matter or per year. Do not re-derive from `Units`.
- **Aggregated years.** `m_year_is_aggregate = TRUE` means `M.Year` such as `1993..2003`: the value is a mean over
  several years or seasons and cannot be tied to one growing season. `m_year_first` / `m_year_last` give the span.
- **Dates and climate provenance.** `planting_date_source` is `reported` (from the paper), `EcoCrop` (generic crop
  calendar) or `none`. `climate_date_source` says which climate set matched (`PDate` or `EcoCrop`);
  `climate_window_start/end` is the window the climate statistics were computed over. EcoCrop-based rows are
  less precise; report their share in any analysis.
- **LTE identity.** `LTE.ID` links publications to experiments (one LTE can have several papers). 61 rows in 2
  multi-site publications could not be attributed to a single LTE and have `LTE.ID` empty.
- **Livestock rows.** 57 rows are animal products (`is_livestock = TRUE`); they are kept for completeness.

## Climate variables

Computed by the ERA geodata pipeline over the growing-season window, from `s3://digital-atlas/era/geodata/clim_stats_*.RData`:

| Prefix | Content |
|---|---|
| `gdd_` | growing degree days in sub-optimal, optimal, above-optimal and above-maximum ranges |
| `rain_` | rainfall total, reference ET, water balance, dry-spell indices (0.1, 1 and 5 mm/day thresholds) |
| `temp_` | min/max/mean temperature statistics and heat-stress days above 35, 37.5 and 40 °C |
| `eratio_` | actual/potential evapotranspiration ratio and drought-stress days below 0.5, 0.25 and 0.1 |
| `logging_` | waterlogging indicators |

Each group carries `_Harvest.Start` / `_Harvest.End` (the harvest bound of the window). Values are rounded to 3 decimals.

## Build it yourself

Everything is downloaded from public URLs; no credentials, no S3 client. Requires R with `dplyr`, `readr`,
`stringr`, `purrr`, `arrow`, `jsonlite`, and Quarto.

```bash
quarto render ERA_LTE_Data_Preparation_With_Climate_Merge.qmd
mv ERA_LTE_Data_Preparation_With_Climate_Merge.html docs/
```

Do not pass `--output-dir docs`: Quarto then cleans `docs/` and deletes the legacy documents kept there.
Downloads are cached in `downloaded_data/` (git-ignored). The build takes under a minute.

Inputs, resolved at run time:

- ERA compiled release named in `https://digital-atlas.s3.amazonaws.com/era/data/releases/latest.json`
- newest `clim_stats_*.RData` under `era/geodata/`
- LTE registry `data_entry/long_term_experiments/unique.ltes.csv` in `CIAT/ERA_dev`

## Using it downstream

Pin a release: fetch files from a tag (for example
`https://raw.githubusercontent.com/ERAgriculture/LTEs/2026.1/data/lte_final.csv`), not from `main`, so that
rebuilds do not change your results silently. Check `data/VERSION.json` and cite the ERA release it names.

## Legacy material

`docs/Vignette LTEs.Rmd`, `docs/Vignette-LTEs-2-.html`, `docs/LTEs_analysis.Rmd` and `lte_summary.Rmd` are the
2025 exploratory analyses (systematic map, practices, durations). They predate this build and read older inputs.

## Team

Alliance of Bioversity International and CIAT, Climate Action lever: Lolita Muller (m.lolita@cgiar.org),
Namita Joshi (n.joshi@cgiar.org), Peter Steward (p.steward@cgiar.org), Todd Rosenstock (t.rosenstock@cgiar.org).

## License and citation

Code: GPL-3.0 (see LICENSE). Data: derived from ERA compiled release 2026.1 (CC-BY-4.0). Cite this repository
(CITATION.cff) and ERA (Rosenstock et al. 2024).
