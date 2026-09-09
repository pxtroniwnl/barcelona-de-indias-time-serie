# Monitoreo y Prediccion de Cobertura Vegetal: Barcelona de Indias

![Python](https://img.shields.io/badge/Python-3.12+-blue.svg)
![Google Earth Engine](https://img.shields.io/badge/Google%20Earth%20Engine-API-green)
![Sentinel](https://img.shields.io/badge/Sentinel-1%20%26-2-orange)
![uv](https://img.shields.io/badge/uv-env-purple.svg)

Proyecto de Ciencia de Datos y Teledeteccion para monitorear y predecir la cobertura de vegetacion acuatica (buchon) sobre la laguna de Barcelona de Indias, en Cartagena (Colombia).

## Que intentamos hacer

La laguna de Barcelona de Indias presenta eventos de crecimiento masivo de vegetacion acuatica (buchon), que alteran el ecosistema y el uso del espejo de agua. El objetivo de este proyecto es **cuantificar y luego predecir la cobertura de esa vegetacion** combinando dos fuentes de datos:

1. **Meteorologia (IDEAM)**: series de alta frecuencia (cada 10-20 min) de la estacion Aeropuerto Rafael Nunez, la mas cercana a la laguna: temperatura, humedad, velocidad del viento, direccion del viento, presion atmosferica y precipitacion.
2. **Teledeteccion (satelital)**: area de vegetacion detectada sobre el espejo de agua mediante indices espectrales y radar:
   - **Sentinel-2** (optico): `ndavi` (Normalized Difference Aquatic Vegetation Index) y bandas `b4`, `b8`, `b11`.
   - **Sentinel-1** (radar): diferencia `vv-vh`, sensible a estructuras verticales de la vegetacion.

La **fase pendiente** del proyecto es la de **Machine Learning**: usar las series meteorologicas diarias como caracteristicas (features) para predecir la cobertura de vegetacion (objetivo) derivada de `ndavi` y `vv-vh`, con una division temporal de entrenamiento/validacion para evitar fuga de datos.

## Flujo de trabajo

```text
[1] Satelites (GEE)                [2] Meteorologia (IDEAM)
Sentinel-2 + Sentinel-1            CSV crudos del portal IDEAM
      |                                   |
      v                                   v
Extraccion de indices             Limpieza, QC y seleccion de
(data/time-serie-indices.csv)     estaciones (data/ideam/*.csv)
      |                                   |
      +----------------+------------------+
                       v
        [3] Analisis conjunto (notebook 02)
        remuestreo diario + suavizado + visualizacion
                       |
                       v
        [4] Machine Learning (pendiente)
        predecir ndavi / vv-vh con meteo
```

## Estructura del repositorio

```text
.
├── notebook/
│   ├── ideam/                  # Descarga, limpieza y export de series meteorologicas
│   │   ├── 01_precipitacion_ideam.ipynb
│   │   ├── 02_temperatura_ideam.ipynb
│   │   ├── 03_humedad_aire_ideam.ipynb
│   │   ├── 04_velocidad_viento_ideam.ipynb
│   │   ├── 05_direccion_viento_ideam.ipynb
│   │   └── 06_presion_atmosferica_ideam.ipynb
│   └── vegetacion/
│       ├── 01_extraccion_indices.ipynb   # Indices satelitales via Google Earth Engine
│       └── 02_analisis_buchon.ipynb      # Analisis conjunto meteorologia + indices
├── data/
│   ├── ideam/                  # Series procesadas de la estacion Rafael Nunez
│   ├── time-serie-indices.csv  # Serie satelital principal de vegetacion
│   └── raw/
│       ├── landsat8.csv              # Referencia historica (2013)
│       └── images-paper1/            # Figuras de picos y cobertura del paper
├── .agents/skills/             # Skills (guias para agentes) del proyecto
├── AGENTS.md                   # Guia interna del proyecto
├── pyproject.toml              # Proyecto y dependencias (uv)
└── uv.lock                     # Lock de dependencias
```

## Notebooks

### IDEAM (`notebook/ideam/`)

Todos los notebooks de esta carpeta siguen la misma secuencia:

1. **Carga** del CSV crudo descargado manualmente del portal IDEAM (departamento Bolivar). Estos archivos fuente **no se versionan**; hay que descargarlos del portal y situarlos al lado del notebook.
2. **Limpieza**: normalizacion de texto, parseo robusto de fechas (`%Y %b %d %I:%M:%S %p`), control de calidad por rango fisico (valores fuera de rango → `NaN`) y desduplicacion.
3. **Seleccion de estaciones**: construye el catalogo de estaciones del CSV, calcula cajas anidadas alrededor del ROI de la laguna, clasifica por distancia Haversine y elige las `N` estaciones mas cercanas (se prefiere la estacion Aeropuerto Rafael Nunez).
4. **Diagnostico**: visualizacion con mapa interactivo (folium), paso de muestreo real, completitud por anio, cobertura anual y ciclo diario medio por estacion.
5. **Export** de la serie de la estacion seleccionada a `data/ideam/`.

| Notebook | Variable | Archivo exportado |
| --- | --- | --- |
| `01_precipitacion_ideam.ipynb` | Precipitacion (mm) acumulada por intervalo | `precipitacion_rafael_nunez.csv` |
| `02_temperatura_ideam.ipynb` | Temperatura del aire (°C) | `temperatura_rafael_nunez.csv` |
| `03_humedad_aire_ideam.ipynb` | Humedad relativa del aire (%) | `humedad_rafael_nunez.csv` |
| `04_velocidad_viento_ideam.ipynb` | Velocidad del viento | `velocidad_viento_rafael_nunez.csv` |
| `05_direccion_viento_ideam.ipynb` | Direccion del viento | `direccion_viento_rafael_nunez.csv` |
| `06_presion_atmosferica_ideam.ipynb` | Presion atmosferica | `presion_atmosferica_rafael_nunez.csv` |

### Vegetacion (`notebook/vegetacion/`)

| Notebook | Para que sirve | Que hace |
| --- | --- | --- |
| `01_extraccion_indices.ipynb` | Obtener la serie satelital de cobertura de vegetacion | Extrae con Google Earth Engine las areas (m²) de vegetacion sobre el agua por indice: Sentinel-2 (`ndavi`, `fai`, `b4`, `b8`, `b11`) con enmascaramiento de nubes y Sentinel-1 (`vv-vh`) con regla logica de doble umbral sobre mascara fija de agua. Detecta picos historicos de cobertura, genera mapas estilizados de esos picos y exporta `data/time-serie-indices.csv` y las figuras a `data/raw/images-paper1/`. **Requiere** `ee.Authenticate` y las credenciales de Earth Engine. |
| `02_analisis_buchon.ipynb` | Integrar meteorologia + indices y preparar los datos para ML | Carga las series IDEAM y los indices, **remuestrea a diario** (media; precipitacion = suma), **suaviza** todas las series (media movil, exponencial y Savitzky-Golay con manejo de gaps), calcula la cobertura porcentual y grafica el panel conjunto normalizado. Es la base de la fase de Machine Learning que se anadira en este notebook. |

## Datos

| Ruta | Contenido |
| --- | --- |
| `data/ideam/*.csv` | Series de alta frecuencia (10-20 min) de la estacion Aeropuerto Rafael Nunez (codigo 0014015020). Columnas: `Estacion, CodigoEstacion, FechaObservacion, ValorObservado`; la precipitacion anade `paso_s` e `intensidad_mmh`. |
| `data/time-serie-indices.csv` | Areas de vegetacion (m²) por fecha e indice: `fecha, fai, id_imagen, ndavi, b4, b8, b11, vv-vh`. Periodo ~2017 a 2026. |
| `data/raw/landsat8.csv` | Serie historica de referencia (2013) derivada de Landsat 8: `fecha, area_m2_vegetacion`. |
| `data/raw/images-paper1/` | Figuras de referencia del paper: picos de NDAVI/VV-VH, cobertura porcentual y series de los 6 indices. |

## Uso local

El entorno se administra con [uv](https://docs.astral.sh/uv/); nunca usar pip directamente.

```bash
# Instalar o restaurar el entorno
uv sync

# Trabajar con los notebooks
uv run jupyter lab

# Ejecutar el analisis conjunto completo desde la terminal (recalcula y guarda salidas)
uv run jupyter nbconvert --to notebook --execute --inplace notebook/vegetacion/02_analisis_buchon.ipynb
```

## Consideraciones

- El notebook de extraccion (`01_extraccion_indices.ipynb`) requiere `earthengine-api`, `geemap` (no estan en las dependencias base del proyecto) y autenticacion con `ee.Authenticate`.
- Los CSV crudos del IDEAM (`Precipitacion_*.csv`, `Temperatura_*.csv`, etc.) no estan en el repositorio: se descargan del portal IDEAM y se colocan junto a cada notebook. Las series procesadas si estan disponibles en `data/ideam/`.
- La siguiente fase del proyecto es la **prediccion**: modelar la cobertura vegetal con las variables meteorologicas diarias suavizadas como caracteristicas y los indices `ndavi` y `vv-vh` como objetivos, con split temporal y sin fuga de datos. Ver `.agents/skills/ml-regression-timeseries`.