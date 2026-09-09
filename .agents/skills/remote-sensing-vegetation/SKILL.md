---
name: remote-sensing-vegetation
description: Use when working with vegetation indices (NDAVI, FAI, B4, B8, B11, VV-VH) for the lagoon of Barcelona de Indias (Cartagena), or when reading data/time-serie-indices.csv, defining sensors/masks/umbrales, or re-running the Earth Engine extraction (notebook/vegetacion/01_extraccion_indices.ipynb).
---

# Sensado Remoto y Vegetación

Contexto del proyecto: monitoreo de la cobertura de vegetación acuática (buchón/macrófitas) sobre el espejo de agua de la laguna de Barcelona de Indias (Cartagena).

## Datos de índices de vegetación

Archivo: `data/time-serie-indices.csv`.

Columnas (todas representan ÁREA de vegetación en m² calculada sobre la máscara fija de la laguna):

- `fecha`: timestamp de la observación (datetime).
- `id_imagen`: identificador de la imagen satelital (poblado solo para Sentinel-2).
- `fai`: Floating Algae Index (Sentinel-2).
- `ndavi`: Normalized Difference Aquatic Vegetation Index (Sentinel-2).
- `b4`: banda Red (Sentinel-2).
- `b8`: banda NIR (Sentinel-2).
- `b11`: banda SWIR (Sentinel-2).
- `vv-vh`: diferencia/ratio radar Sentinel-1 (S1).

Periodo aproximado: 2017-01 hasta 2026-05. Registros con `ndavi` y `vv-vh` no coindicen entre sí (el radar y el óptico no pasan el mismo día con la misma frecuencia).

Otros datos:
- `data/raw/landsat8.csv`: serie histórica de referencia `fecha, area_m2_vegetacion` (~2013), columnas minúsculas.
- `data/raw/images-paper1/*.png`: imágenes de picos (NDAVI, VV-VH) y series generadas para el paper.

## Umbrales usados en la extracción (Earth Engine)

Relevantes si se reprocesa con `notebook/vegetacion/01_extraccion_indices.ipynb` (GEE):
- Máscara de agua: MNDWI > 0.05 (Sentinel-2, compuesto de mediana de 2025).
- Vegetación óptica simple: `FAI > 0.16`, `NDAVI > 0.4`, `B4 > 0.58`, `B8 > 0.66`, `B11 > 0.65`.
- Vegetación radar (regla combinada VV y VH): `VV > -16` AND `VH > -22`.
- Escala de análisis: 10 m. Área total del espejo de agua: ~58,900 m².

## Notas clave

- `ndavi` y `vv-vh` son el par de índices elegido como variable objetivo para la fase de Machine Learning.
- El notebook `notebook/vegetacion/01_extraccion_indices.ipynb` requiere Earth Engine (`ee.Authenticate`, `geemap`) y NO está en el entorno base uv (dependencia pesada). Solo se puede reproducir con credenciales de GEE.
- Al cargar en pandas: `ndavi`, `b4`, `b8`, `b11`, `fai` conviene `pd.to_numeric(..., errors='coerce')`.
