# Monitoreo y Prediccion de Cobertura Vegetal: Barcelona de Indias

![Python](https://img.shields.io/badge/Python-3.12+-blue.svg)
![Google Earth Engine](https://img.shields.io/badge/Google%20Earth%20Engine-API-green)
![uv](https://img.shields.io/badge/uv-env-purple.svg)

Proyecto de Ciencia de Datos y Teledeteccion para monitorear la vegetacion acuatica (buchon) en la laguna de Barcelona de Indias, Cartagena. Integra datos meteorologicos del IDEAM con indices satelitales de Sentinel-1 y Sentinel-2.

## Estructura

```text
.
├── notebook/
│   ├── ideam/                 # Procesamiento de variables meteorologicas
│   └── vegetacion/            # Extraccion y analisis de cobertura vegetal
├── data/
│   ├── ideam/                 # Series procesadas de la estacion Rafael Nunez
│   ├── time-serie-indices.csv # Serie satelital principal de vegetacion
│   └── raw/                   # Datos historicos y figuras del paper
├── pyproject.toml
└── uv.lock
```

## Notebooks

### IDEAM

| Notebook | Descripcion breve |
| --- | --- |
| `notebook/ideam/01_precipitacion_ideam.ipynb` | Limpia la precipitacion y exporta la serie de Rafael Nunez. |
| `notebook/ideam/02_temperatura_ideam.ipynb` | Limpia la temperatura del aire y exporta la serie de la estacion. |
| `notebook/ideam/03_humedad_aire_ideam.ipynb` | Limpia la humedad relativa del aire y exporta la serie de la estacion. |
| `notebook/ideam/04_velocidad_viento_ideam.ipynb` | Procesa y exporta la velocidad del viento. |
| `notebook/ideam/05_direccion_viento_ideam.ipynb` | Procesa y exporta la direccion del viento. |
| `notebook/ideam/06_presion_atmosferica_ideam.ipynb` | Procesa y exporta la presion atmosferica. |

Los notebooks IDEAM usan CSV fuente descargados manualmente desde el portal IDEAM, que no se versionan. Todos filtran la estacion **AEROPUERTO RAFAEL NUNEZ** y guardan los resultados en `data/ideam/`.

### Vegetacion

| Notebook | Descripcion breve |
| --- | --- |
| `notebook/vegetacion/01_extraccion_indices.ipynb` | Extrae con Google Earth Engine las areas de vegetacion por indice Sentinel-1 y Sentinel-2; guarda las figuras en `data/raw/images-paper1/`. |
| `notebook/vegetacion/02_analisis_buchon.ipynb` | Integra meteorologia e indices, remuestrea a diario, suaviza las series y prepara el analisis para Machine Learning. |

## Datos

| Ruta | Contenido |
| --- | --- |
| `data/ideam/*.csv` | Series de alta frecuencia de temperatura, humedad, precipitacion, viento y presion de la estacion Rafael Nunez. |
| `data/time-serie-indices.csv` | Areas de vegetacion en m2 por fecha e indice: `fai`, `ndavi`, `b4`, `b8`, `b11` y `vv-vh`. |
| `data/raw/landsat8.csv` | Serie historica de referencia derivada de Landsat 8. |
| `data/raw/images-paper1/` | Figuras de referencia y resultados graficos para el paper. |

## Uso local

El entorno se administra con [uv](https://docs.astral.sh/uv/).

```bash
uv sync
uv run jupyter lab
```

Para ejecutar el analisis completo desde la terminal:

```bash
uv run jupyter nbconvert --to notebook --execute --inplace notebook/vegetacion/02_analisis_buchon.ipynb
```

## Consideraciones

- El notebook de extraccion requiere `earthengine-api`, `geemap` y credenciales de Google Earth Engine mediante `ee.Authenticate`.
- Los CSV crudos del IDEAM no estan en el repositorio; las series procesadas si estan disponibles en `data/ideam/`.
- La siguiente fase del proyecto es modelar la cobertura vegetal con las variables meteorologicas diarias como caracteristicas y los indices `ndavi` y `vv-vh` como objetivos.
