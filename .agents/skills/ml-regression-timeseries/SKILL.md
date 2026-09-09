---
name: ml-regression-timeseries
description: Use when building the Machine Learning stage of this project: predicting vegetation coverage (ndavi or vv-vh) from smoothed daily meteorological features, feature engineering with lags, time-based splits, regression models, metrics, or avoiding data leakage. Apply in analisis_buchon.ipynb.
---

# Machine Learning: Regresión sobre Series Temporales

Objetivo de la fase ML (a implementar en `analisis_buchon.ipynb`): predecir la cobertura de vegetación de la laguna de Barcelona de Indias a partir de variables meteorológicas.

## Diseño acordado

- **Features**: series meteorológicas diarias suavizadas (precipitación, temperatura, humedad, velocidad/dirección del viento, presión) + ingeniería con lags.
- **Target**: `ndavi` y/o `vv-vh` (área de vegetación en m²) de `data/raw/time-serie-indices`.
- **Alineación**: índice/radar SIN remuestrear (observaciones puntuales); meteorológicas alineadas por fecha exacta (o day-before/after con tolerancia pequeña). Evitar inventar observaciones en fechas sin imagen.

## Reglas obligatorias (anti-fuga)

1. **Split temporal, nunca aleatorio**: `TimeSeriesSplit` o división por fecha (train = pasadas, test = futuras).
2. **Sin lookahead**: features de un día sólo con información disponible hasta ese día; los lags se calculan con datos pasados.
3. **Escalado ajustado en train solamente** (`fit` en train, `transform` en validación/test), con `Pipeline` para evitar fuga en el preprocesado.
4. Ventana de suavizado solo sobre train si se recalcula (o fijar un horizonte y ser explícito).

## Modelos de referencia

- Lineal: `LinearRegression`, `Ridge` (con búsqueda de `alpha` por validación temporal).
- Árboles: `RandomForestRegressor`, `GradientBoostingRegressor`/`HistGradientBoostingRegressor`, `ExtraTreesRegressor`.
- Evaluar lags/features: `permutation_importance` o feature_importances; comparar con baseline naïf (persistencia: predicción = último valor conocido).

## Métricas

- `mean_squared_error`, `root_mean_squared_error`, `mean_absolute_error`, `R^2`.
- Reportar sobre la escala original (m²) y, si se trabaja con log/normalizado, desnormalizar antes de reportar.
- Guardar figures de predicción vs real en el notebook (estilo de plots del skill research-publication-plots).

## Dependencias

El entorno uv base ya incluye `pandas`, `numpy`, `matplotlib`, `scipy`. Si se requieren `scikit-learn`/`xgboost`, añadirlos a `pyproject.toml` y correr `uv sync` antes de usarlos.