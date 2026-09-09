# Monitoreo y Prediccion de Cobertura Vegetal: Barcelona de Indias

![Python](https://img.shields.io/badge/Python-3.12+-blue.svg)
![Google Earth Engine](https://img.shields.io/badge/Google%20Earth%20Engine-API-green)
![uv](https://img.shields.io/badge/uv-env-purple.svg)

Este proyecto implementa un flujo de trabajo de Ciencia de Datos y Teledeteccion para analizar la evolucion historica de la vegetacion acuatica (buchon) en la zona de la laguna de Barcelona de Indias, Cartagena.

## Estado actual del proyecto

Hasta ahora el proyecto ha logrado:

1. **Extraccion de series de tiempo meteorologicas (IDEAM)**: descarga, limpieza y exportacion de las series de la estacion mas cercana al area de interes: **Aeropuerto Rafael Nunez**. Variables: precipitacion, temperatura, humedad relativa, velocidad del viento, direccion del viento y presion atmosferica.
2. **Reconstruccion de series de tiempo de vegetacion (satelital)**: mediante Google Earth Engine se calculan sobre el espejo de agua de la laguna los indices de area de vegetacion:
   - **NDAVI** (Normalized Difference Aquatic Vegetation Index, Sentinel-2)
   - **FAI** (Floating Algae Index, Sentinel-2)
   - Bandas opticas **B4 (Red)**, **B8 (NIR)**, **B11 (SWIR)** de Sentinel-2
   - **VV-VH** (Sentinel-1, radar)
3. **Analisis conjunto y visualizacion**: un notebook (`analisis_buchon.ipynb`) carga toda la data, remuestrea las series meteorologicas a diario (alta frecuencia cruda) y grafica todas las series con suavizado, comparando metodos (media movil, suavizado exponencial y Savitzky-Golay) para preparar la fase de prediccion.

## Estructura del repositorio

### Notebooks

| Archivo | Descripcion |
| --- | --- |
| `analisis_buchon.ipynb` | Analisis conjunto de toda la data: carga, remuestreo a diario de las meteorologicas, suavizado de todas las series y visualizacion conjunta. Punto de partida para la prediccion de cobertura de vegetacion con Machine Learning. |
| `pulling_time_serie_indices.ipynb` | Extraccion de los indices de vegetacion desde Google Earth Engine sobre la laguna. Requiere autenticacion de Earth Engine (`ee.Authenticate`). |
| `precipitacioonIDEAM.ipynb` | Descarga/limpieza/export de la serie de precipitacion de la estacion Rafael Nunez hacia `data/ideam`. |
| `temperaturaIDEAM.ipynb` | Descarga/limpieza/export de la serie de temperatura. |
| `humedad_del_aire_IDEAM.ipynb` | Descarga/limpieza/export de la serie de humedad del aire. |
| `velocidad_vientoIDEAM.ipynb` | Descarga/limpieza/export de la serie de velocidad del viento. |
| `direccion_vientoIDEAM.ipynb` | Descarga/limpieza/export de la serie de direccion del viento. |
| `presion_atmosfericaIDEAM.ipynb` | Descarga/limpieza/export de la serie de presion atmosferica. |

Los notebooks de IDEAM leen un archivo fuente descargado desde el portal del IDEAM (por ejemplo `Precipitacion_20260818_SOLO_BOLIVAR.csv`) que **no esta versionado** en el repositorio, y exportan la serie limpia de la estacion a `data/ideam/`.

### Datos

| Ruta | Contenido |
| --- | --- |
| `data/ideam/*.csv` | Series meteorologicas de alta frecuencia (cada 10-20 minutos) de la estacion Aeropuerto Rafael Nunez. Columnas `Estacion, CodigoEstacion, FechaObservacion, ValorObservado` (la precipitacion ademas incluye `paso_s` e `intensidad_mmh`). |
| `data/raw/time-serie-indices` | Serie de areas de vegetacion (m2) por indice sobre la laguna. Columnas: `fecha, fai, id_imagen, ndavi, b4, b8, b11, vv-vh`. Periodo ~2017 a 2026. |
| `data/raw/landsat8.csv` | Serie historica de area de vegetacion (m2) derivada de Landsat 8 (referencia historica: ~2013). Columnas `fecha, area_m2_vegetacion`. |
| `data/raw/images-paper1/*.png` | Imagenes de referencia generadas para el articulo/paper (picos de NDAVI y VV-VH sobre la laguna, cobertura porcentual, series de los 6 indices). |

### Entorno y configuracion

| Archivo | Descripcion |
| --- | --- |
| `pyproject.toml` | Definicion del proyecto y dependencias (gestionado con uv). |
| `uv.lock` | Bloqueo de versiones exactas de dependencias. |
| `.python-version` | Version de Python gestionada por uv (3.12). |
| `.gitignore` | Exclusiones de archivos (entorno virtual, caches de Python/Jupyter). |

## Como inicializar el proyecto en local

El proyecto usa [uv](https://docs.astral.sh/uv/) para gestionar el entorno virtual y las dependencias. Requiere tener `uv` instalado (Python 3.12 se gestiona automaticamente).

```bash
# 1. Clonar el repositorio
git clone git@github.com:pxtroniwnl/barcelona-de-indias-time-serie.git
cd barcelona-de-indias-time-serie

# 2. Crear el entorno e instalar dependencias
uv sync

# 3. Activar el entorno (opcional)
source .venv/bin/activate

# 4. Abrir los notebooks
uv run jupyter lab
```

### Ejecutar el notebook de analisis desde terminal

Tambien se puede ejecutar el notebook de analisis completo desde la terminal (guarda las salidas en el propio archivo):

```bash
uv run jupyter nbconvert --to notebook --execute --inplace analisis_buchon.ipynb
```

### Consideraciones

- **Earth Engine**: el notebook `pulling_time_serie_indices.ipynb` requiere `earthengine-api` y `geemap`, que **no estan incluidos** en el entorno base (instalacion pesada). Para reproducir la extraccion satelital se necesitan ademas credenciales de Google Earth Engine (`ee.Authenticate`).
- **Fuentes IDEAM**: los archivos originales descargados del IDEAM (CSV a nivel departamento) no estan versionados; los datos ya procesados de la estacion si lo estan en `data/ideam/`.
- **Fase de Machine Learning**: la siguiente etapa del proyecto es predecir la cobertura de vegetacion usando las series meteorologicas diarias suavizadas como caracteristicas y los indices (`ndavi`, `vv-vh`) como variable objetivo. Esa fase se anadira en el notebook `analisis_buchon.ipynb`.