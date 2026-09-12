# AGENTS.md

Guia para agentes que trabajen en este repositorio. El objetivo es evitar re-descubrimiento: leela primero y acude a los skills y comandos antes de explorar de cero.

## Panorama del proyecto

Ciencia de Datos y Teledeteccion para monitorear y predecir la cobertura de vegetacion acuatica (buchon) sobre la laguna de Barcelona de Indias (Cartagena). Combina series meteorologicas del IDEAM (estacion Aeropuerto Rafael Nunez) con indices de vegetacion satelitales (Sentinel-2 y Sentinel-1 via Google Earth Engine). La fase de Machine Learning ya esta implementada en `notebook/vegetacion/02_analisis_buchon.ipynb` (seccion 9): prediccion del indice `vv-vh` (observacion cruda y estado de cobertura suavizado EWM) con features causales, backtesting walk-forward, modelos clasicos/ensamble y Deep Learning (PyTorch). El target `ndavi` se descarto por su alto porcentaje de ceros.

## Estructura del repositorio

### Notebooks

| Archivo | Contenido |
| --- | --- |
| `notebook/vegetacion/02_analisis_buchon.ipynb` | Analisis conjunto: carga, remuestreo diario de meteorologicas, suavizado de todas las series (media movil, exponencial, Savitzky-Golay) y visualizacion. Incluye la fase ML completa (seccion 9): features causales, backtesting walk-forward, clasicos/ensamble y Deep Learning (PyTorch). |
| `notebook/vegetacion/01_extraccion_indices.ipynb` | Extraccion de indices desde Google Earth Engine. Requiere Earth Engine (`ee.Authenticate`) y depende de `earthengine-api`/`geemap` que NO estan en el entorno base. |
| `notebook/ideam/*.ipynb` | Descarga/limpieza/export de series del IDEAM hacia `data/ideam/`. Requieren CSV fuente descargados del portal IDEAM (no versionados). |

### Datos

| Ruta | Contenido |
| --- | --- |
| `data/ideam/*.csv` | Series meteorologicas de alta frecuencia (cada 10-20 min) de la estacion AEROPUERTO RAFAEL NUNEZ (codigo 0014015020). Columnas `Estacion, CodigoEstacion, FechaObservacion, ValorObservado`; la precipitacion anade `paso_s` e `intensidad_mmh`. |
| `data/time-serie-indices.csv` | Areas de vegetacion (m2) por indice. Columnas `fecha, fai, id_imagen, ndavi, b4, b8, b11, vv-vh`. Periodo ~2017 a 2026. |
| `data/raw/landsat8.csv` | Referencia historica (~2013): `fecha, area_m2_vegetacion`. |
| `data/raw/images-paper1/*.png` | Imagenes de referencia del paper (picos NDAVI/VV-VH, cobertura, series). |

### Configuracion

| Archivo | Descripcion |
| --- | --- |
| `pyproject.toml` | Proyecto uv y dependencias: pandas, numpy, matplotlib, scipy, folium, jupyter, ipykernel, nbconvert, scikit-learn, statsmodels, torch, xgboost. NO incluye earthengine-api/geemap (anadir via `uv add` si se re-extraen indices en GEE). |
| `uv.lock` | Lock de dependencias. |
| `.python-version` | Python 3.12 gestionado por uv. |
| `.gitignore` | Excluye `.venv/`, caches. |
| `opencode.json` | Config de opencode: registra `skills.paths: [".agents/skills"]`. |
| `AGENTS.md` | Este archivo. |
| `.agents/skills/*/SKILL.md` | Skills del proyecto (ver seccion Skills). |

## Entorno y comandos

Todo trabaja con `uv`; nunca usar pip directamente ni el Python del sistema.

```bash
# Instalar/restaurar entorno
uv sync

# Abrir notebooks
uv run jupyter lab

# Ejecutar un notebook headless (guardando salidas en el propio archivo)
uv run jupyter nbconvert --to notebook --execute --inplace notebook/vegetacion/02_analisis_buchon.ipynb
```

- No hay lint ni typecheck configurado. Verificar los notebooks ejecutandolos con nbconvert (arriba) o comprobando que no haya errores en las celdas.
- Si se anade una dependencia nueva, usar `uv add <paquete>` y confirmar `pyproject.toml` + `uv.lock` quedan actualizados.

## Convenciones

- **Idioma**: notebooks, graficas, README y commits en espanol (sin acentos en commits/titulos cuando el historial lo refleje).
- **Celdas de notebook**: usar `fig, ax = plt.subplots(...)` explicito, titulos y ejes en espanol con unidades, figsize amplio. Estilo publicable ver skill `research-publication-plots`.
- **Remuestreo**: series meteorologicas a diario = media; precipitacion = SUMA. Suavizado con ventana constante `VENTANA_SUAVIZADO_DIAS`. Ver skill `time-series-analysis`.
- **Archivos grandes / secretos**: no versionar `.venv/`, `__pycache__/`, `.ipynb_checkpoints/`, `.DS_Store`, ni CSV fuente del portal IDEAM.
- Commits: solo lo que se pida; mensajes cortos y descriptivos.

## Skills del proyecto

Los skills viven en `.agents/skills/<nombre>/SKILL.md` (formato estandar `name` + `description` en frontmatter) y se registran en `opencode.json` para que el agente los cargue.

Consultar el skill relevante ANTES de trabajar en esas areas para ahorrar tokens:

| Skill | Cuando usarlo |
| --- | --- |
| `remote-sensing-vegetation` | Indices NDAVI/FAI/VV-VH, mascaras/umbrales del ROI, lectura de `data/time-serie-indices.csv`, extraccion GEE. |
| `time-series-analysis` | Remuestreo, suavizado, alineacion temporal; modificaciones a `notebook/vegetacion/02_analisis_buchon.ipynb`. |
| `eda-data-science` | Perfilado, calidad, gaps, cobertura temporal, correlaciones de la data. |
| `ml-regression-timeseries` | Fase ML: features, lags, split temporal, modelos, metricas, anti-fuga. |
| `ideam-meteorological-data` | Columnas/formato IDEAM, estacion Rafael Nunez, reproduccion de exportaciones. |
| `research-publication-plots` | Estilo de graficas publicables (rcParams, colores, ejes de fecha, dpi). |

### Como anadir un skill nuevo

1. Crear la carpeta `.agents/skills/<nombre>/` y dentro `SKILL.md`.
2. Frontmatter obligatorio: `name` (minusculas, con guiones, igual al nombre de carpeta) y `description` en tercera persona con keywords de disparo concretas (rutas, funciones, tareas).
3. Cuerpo corto y util: rutas de datos, patrones del proyecto, pitfalls conocidos. Sin humo ni relleno.
4. No hace falta tocar config: opencode escanea `.agents/skills` automaticamente via `opencode.json`.

### Agentes y comandos extra de opencode

Si se necesita un agente/command especifico de opencode, se crean en `.opencode/agent/<nombre>.md` y `.opencode/command/<nombre>.md`. Tras tocar cualquier config de opencode, hay que reiniciar opencode para que surta efecto.

## Tareas tipicas y flujo recomendado

1. Leer `AGENTS.md`.
2. Cargar el skill del area (`remote-sensing-vegetation`, `time-series-analysis`, etc.).
3. Explorar solo lo necesario; confiar en skills y en la estructura de esta guia.
4. Implementar con `uv` y verificar ejecutando el notebook con nbconvert.
