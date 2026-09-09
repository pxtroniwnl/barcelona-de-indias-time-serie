---
name: eda-data-science
description: Use when doing exploratory data analysis of this repo's datasets (IDEAM meteorological CSVs, vegetation series in data/raw/time-serie-indices, landsat8.csv), profiling, gap analysis, outliers, coverage over time, or data-quality checks before modeling.
---

# Análisis Exploratorio de Datos (EDA)

## Datasets del repo

- `data/ideam/*.csv`: 6 variables meteorológicas de la estación Aeropuerto Rafael Núñez (alta frecuencia). Archivos: `precipitacion_*.csv`, `temperatura_*.csv`, `humedad_*.csv`, `velocidad_viento_*.csv`, `direccion_viento_*.csv`, `presion_atmosferica_*.csv` (la presión arranca 2023-09-18).
- `data/raw/time-serie-indices`: índices de vegetación satelitales (área m²), irregular.
- `data/raw/landsat8.csv`: referencia histórica `fecha, area_m2_vegetacion`.

## Flujo recomendado

1. Perfil por dataset: `describe()`, `info()`, valores únicos, `dtypes`.
2. Alcance temporal y periodicidad: min/max de `fecha`, frecuencia de muestreo, pasos.
3. Calidad: NaNs, huecos/gaps por variable, filas duplicadas, fuera de rango físico (precipitación negativa, humedad > 100%, etc.).
4. Cobertura temporal conjunta: el solape meteorológico-índices es parcial (meteorológicas hasta ~2026-08, índices 2017→2026-05); señalarlo en el análisis.
5. Series irregulares: los índices y `vv-vh`/`ndavi` tienen fechas distintas; revisar recuentos por año y brechas (adquiriencias suelen ser estacionales).
6. Correlaciones: entre meteorológicas y con los índices (usar series remuestreadas a diario y `corr`, señalando que no hay correlación causal automática).
7. Cobertura: con `AREA_TOTAL_LAGUNA_M2` (~58,900 m²) se calcula `% cobertura = area / TOTAL`.

## Qué evitar

- No tratar las series originales de alta frecuencia como si fueran muestreo uniforme diario; siempre remuestrear primero.
- No imputar ciegamente los huecos de los índices satelitales en la fase EDA; las convenciones de relleno pertenecen a la fase ML (ver skill ml-regression-timeseries).