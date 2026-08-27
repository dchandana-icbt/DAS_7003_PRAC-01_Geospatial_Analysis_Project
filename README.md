# Yatinuwara LS-LRSI Practical Package

## Project
**Location-Specific Landslide Risk Scoring Index (LS-LRSI) for Yatinuwara Divisional Secretariat Division, Kandy District, Sri Lanka.**

Required statement:

> I propose a Location-Specific Landslide Risk Scoring Index (LS-LRSI) for Yatinuwara Divisional Secretariat Division, Kandy District, Sri Lanka.

## What this package does
The notebook downloads or queries public data for:

1. Yatinuwara DSD boundary
2. Copernicus DEM GLO-30
3. Derived 30 m slope
4. CHIRPS rainfall climatology
5. ESA WorldCover 2021 v200
6. SoilGrids clay
7. OpenStreetMap roads
8. OpenStreetMap streams
9. NASA Global Landslide Catalog / COOLR
10. WorldPop population exposure

It then reprojects/alines them to a common **30 m EPSG:32644** grid and creates:

- Static susceptibility `S` (0–1)
- Rainfall trigger potential `T` (0–1)
- Population exposure `E` (0–1)
- Final LS-LRSI (0–100)
- Low / Moderate / High / Very High risk classes
- EDA statistics, correlation diagnostics and a simple weight-sensitivity table

## Important limitation
The execution environment used to create this package does not have direct external binary-download access. Therefore the accompanying notebook is **not pre-executed with fabricated data**. Run it in an internet-enabled environment (local Jupyter or Google Colab) to download the real source files and generate the real Yatinuwara maps/statistics.

This is academically preferable to inventing EDA or risk values.

## Recommended environment
Python 3.11+.

Install:
```bash
pip install -r requirements.txt
```

Then:
```bash
jupyter notebook Yatinuwara_LS_LRSI_Practical.ipynb
```

Google Colab can also run the notebook; uncomment the `%pip install` cell.

## Folder structure created by the notebook
```text
data/
  raw/
  processed/
outputs/
  figures/
  tables/
  Yatinuwara_LS_LRSI_0_100.tif
  Yatinuwara_LS_LRSI_classes.tif
  run_metadata.json
```

## Exact sources
See `dataset_manifest.csv` and `download_links.txt`.

## Index formula
```text
LS-LRSI =
100 * [0.55*S + 0.25*T + 0.20*E + 0.15*(S*T) + 0.10*(S*E)] / 1.25
```

Risk classes:
- 0–20: Low
- >20–40: Moderate
- >40–60: High
- >60–100: Very High

## Static susceptibility baseline weights
- Slope 0.40
- Land cover 0.15
- Soil 0.12
- Road proximity 0.15
- Stream proximity 0.10
- Historical landslide prior 0.08

If the public NASA inventory query yields no usable nearby landslides, the inventory factor is dropped and the remaining weights are renormalized.

## Why rainfall is handled carefully
CHIRPS is 0.05° (~5 km), much coarser than the 30 m output. The baseline notebook uses the 1981–2024 annual climatology as a **climatic trigger potential**, not as a claim of 30 m rainfall measurement. A future operational run should replace `T` with recent 1/3/7-day rainfall or local gauges.

## SoilGrids reliability
The notebook first streams the official SoilGrids WebDAV VRT. If that fails because of GDAL `/vsicurl/` support, it automatically attempts the official SoilGrids **WCS** to download a small Yatinuwara-area GeoTIFF subset.

## What to put into the report after running
Use the generated:
- `outputs/tables/eda_summary_statistics.csv`
- `outputs/tables/risk_class_area_summary.csv`
- maps in `outputs/figures/`
- actual landslide count printed by the NASA query
- actual source/version metadata in `outputs/run_metadata.json`

Do not report provisional numerical values before executing the notebook.
