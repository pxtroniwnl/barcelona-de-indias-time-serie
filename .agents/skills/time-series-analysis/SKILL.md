---
name: time-series-analysis
description: Use when loading, resampling, smoothing, aligning, or merging time series on this project (IDEAM meteorological data, vegetation indices), or when modifying notebook/vegetacion/02_analisis_buchon.ipynb and its smoothing functions (media_movil, suavizado_exp, savgol_con_gaps).
---

# Análisis de Series Temporales

## Convenciones del proyecto

Todo el análisis conjunto vive en `notebook/vegetacion/02_analisis_buchon.ipynb` (Python + pandas + matplotlib). Reglas ya acordadas:

- Las series meteorológicas IDEAM (alta frecuencia, cada 10-20 min) se remuestrean a **diario** con media (`resample('1D').mean()`); la **precipitación usa suma** (`resample('1D').sum()`), no media.
- Todas las series (meteorológicas e índices) se **suavizan** con una ventana de `VENTANA_SUAVIZADO_DIAS` (definida como constante al inicio del notebook, típicamente ~5-15 días).
- Tamaños de punto de referencia tras el remuestreo diario: precipitación 955,217 → 4,368 filas; temperatura 639,720 → 3,746; humedad 644,646 → 3,753; velocidad_viento 459,932 → 3,451; direccion_viento 484,539 → 3,612; presion_atmosferica 578,649 → 844. Los índices (`data/time-serie-indices.csv`) quedan como ~1,253 observaciones sin remuestrear.

## Funciones de suavizado ya implementadas

Definidas en `notebook/vegetacion/02_analisis_buchon.ipynb`:

- `media_movil(serie, ventana=VENTANA_SUAVIZADO_DIAS)`: media móvil centrada (rolling mean).
- `suavizado_exp(serie, span=VENTANA_SUAVIZADO_DIAS)`: suavizado exponencial (`ewm`).
- `savgol_con_gaps(serie, ventana, polyorder=3)`: Savitzky-Golay que interpola huecos (si hay NaNs), convierte la ventana a impar y, si `scipy` no está disponible, cae a `media_movil`.

Comparación de suavizados ya graficada para `ndavi` y `vv-vh`. La serie usada como "limpia" para análisis posteriores es la media móvil (`media_movil`).

## Patrones útiles

- Cargar índices con índice de fecha: `pd.to_datetime`, `set_index('fecha')`.
- Para ML la regla es: remuestrear meteorológicas a diario → suavizar → alinear por fecha con los índices (los índices sin remuestrear; ver skill ml-regression-timeseries).
- Normalización/estandarización para comparar series de distinta escala: función `normalizar(serie)` (min-max) ya presente para el panel conjunto.
