---
name: ideam-meteorological-data
description: Use when working with IDEAM meteorological data for this project (station AEROPUERTO RAFAEL NUNEZ, Cartagena): reading data/ideam/*.csv, understanding the columns (Estacion, CodigoEstacion, FechaObservacion, ValorObservado, paso_s, intensidad_mmh), or reproducing the exports from the *IDEAM.ipynb notebooks.
---

# Datos Meteorológicos IDEAM

## Variables y archivos

Exportados a `data/ideam/*.csv` (estación `AEROPUERTO RAFAEL NUNEZ`, código `0014015020`):

- `precipitacion_rafael_nunez.csv`
- `temperatura_rafael_nunez.csv`
- `humedad_rafael_nunez.csv`
- `velocidad_viento_rafael_nunez.csv`
- `direccion_viento_rafael_nunez.csv` (termina antes: ~2026-02-22; el resto llega ~2026-08-17)
- `presion_atmosferica_rafael_nunez.csv` (empieza tarde: ~2023-09-18)

## Esquema común

Columnas: `Estacion, CodigoEstacion, FechaObservacion, ValorObservado`. La precipitación añade `paso_s` (intervalo de paso, p. ej. 600 s) e `intensidad_mmh`.

- Alta frecuencia: muestreo cada ~10-20 minutos → al trabajar en diario, precip = SUMA, resto = MEDIA (skill time-series-analysis).
- Lectura recomendada: `parse_dates=['FechaObservacion']`, codificar estación como constante.

## Reproducir la exportación

Los notebooks de `notebook/ideam/` descargan del portal de IDEAM:

1. Se leen los CSV fuente (p. ej. `Precipitación_20260818_SOLO_BOLIVAR.csv`, `Temperatura_Ambiente_del_Aire_20260818_SOLO_BOLIVAR.csv`) que NO están versionados en el repo (descarga manual del portal).
2. Se filtran por la estación más cercana al ROI (Rafael Núñez) y se guardan en `data/ideam/`.
3. La carpeta de salida se homogeneiza en minúsculas (p. ej. `precipitacion_rafael_nunez.csv`).

Nota: estos notebooks dependen de archivos externos de IDEAM; no son reproducibles sin descargarlos primero.
