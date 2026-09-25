# Changelog

## 2026.1 — 2026-09-25

First versioned release of the LTE dataset. Rebuilt from scratch; the file names `data/lte_final.csv` and
`data/unmatched_climate_keys.csv` are kept, every column that existed before still exists (except the empty
`Lat`/`Lon`), so existing readers keep working, but the numbers change.

### Inputs
- ERA compiled data now comes from the versioned public release named in `era/data/releases/latest.json`
  (2026.1, built from ie 2025-08-11.1 / mh 2025-07-24.1). The previous build read a May-2025 bundle
  (ie 2025-05-09.2) and separately downloaded an August-2025 RData it never used; that download is gone.
- Climate: newest `clim_stats_*.RData` (2025-08-14.1), now including the `logging_` tables.
- LTE registry read live from `CIAT/ERA_dev` and cleaned in the build (semicolon delimiter, Latin-1, trailing
  empty columns, 4 broken rows removed, duplicates removed). Cleaned copy shipped as `unique_ltes_clean.csv`.

### New columns in `lte_final.csv`
`LTE.ID`, `obs_id`, `ctrl_id`, `yield_t_ha`, `yield_c_t_ha`, `yield_basis`, `m_year_is_aggregate`,
`m_year_first`, `m_year_last`, `is_livestock`, `planting_date_source`, `harvest_date_source`,
`climate_window_start`, `climate_window_end`, `era_release`, `era_provenance`, and the `logging_*` climate group.

### New files
`lte_arms.csv` (one row per arm measurement), `data_dictionary.csv`, `unique_ltes_clean.csv`, `unique_ltes_unresolved.csv`,
`build_report.csv`, `VERSION.json`, `era_compiled_fields.csv`.

### Fixes
- Negative `Duration` values set to missing (6 rows, one publication).
- `unmatched_climate_keys.csv` is now actually written by the build, with a reason per key.
- Rendered HTML is self-contained (no `_files/` folder).

### Removed from the repository
`.Rhistory`, the broken `data/ERAg_1.0.2.tar.gz`, `data/unique.ltes.rds`, the stale `data/unique_ltes.csv`,
`docs/Download_ERAg_Package.Rmd`, `docs/ERA_ltes.Rproj`, the stray `gitignore`, and the committed download
cache `docs/downloaded_data/`.

### Counts (previous build → 2026.1)
| | previous | 2026.1 |
|---|---|---|
| rows | 32,826 | 33,646 |
| publications | 293 | 293 |
| long-term experiments | not identifiable | 244 |
| rows with climate | 16,975 (51.7 %) | 17,059 (50.7 %) |
| crop-yield rows | 18,164 | 17,979 |

## Unversioned — 2026-08 (Namita Joshi)
Quarto rewrite with the two-tier climate merge (PDate, then EcoCrop) and `climate_date_source`.

## v1.0.0 — 2025-01-10
Initial vignette: systematic map of LTEs in ERA, practices and durations, POWER/CHIRPS climate exploration.
