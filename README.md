# 🏞️Serie temporal de la laguna de Barcelona de Indias

Este repositorio contiene un flujo de trabajo en Python y Jupyter Notebook para construir una **serie temporal de indicadores de agua** sobre la laguna de **Barcelona de Indias (Cartagena, Colombia)** utilizando imágenes satelitales de **Sentinel‑2** a través de **Google Earth Engine (GEE)**.

El objetivo principal es:

- **Delimitar de forma estable la laguna** mediante índices espectrales de agua.
- **Filtrar ruido de tierra firme, vegetación y construcciones** alrededor del cuerpo de agua.
- **Generar una serie temporal limpia** de métricas (por ejemplo, área de espejo de agua) para análisis ambientales.

---

## Contenido del repositorio

- `v1_zona_lag.ipynb`: cuaderno principal donde se:
  - Inicializa la sesión con Earth Engine (`ee.Authenticate`, `ee.Initialize`).
  - Define el **polígono de interés (ROI)** de la laguna con coordenadas en formato `[longitud, latitud]`.
  - Descarga y procesa colecciones de imágenes de **Sentinel‑2**.
  - Aplica el índice **MNDWI (Modified Normalized Difference Water Index)** para resaltar agua.
  - Binariza el índice según un **umbral definido** (por ejemplo, `mndwi.gt(0.026)`) para generar una máscara de agua.
  - Construye una **máscara espacial fija** (contorno estable de la laguna) usando el promedio de un año, que se usa como filtro estático.
  - Extrae y organiza los resultados en tablas (p. ej. con `pandas`) y visualizaciones.

En versiones futuras se pueden añadir más cuadernos, scripts o directorios para:

- Exportar resultados (`.csv`, `.geojson`, figuras).
- Probar distintos índices o umbrales.
- Integrar modelos estadísticos o de machine learning sobre la serie temporal generada.

---

## Requisitos

Para ejecutar el proyecto localmente necesitas:

- **Python 3.8+**
- Cuenta activa de **Google Earth Engine** vinculada a tu correo.
- Entorno con las siguientes librerías de Python:

```bash
pip install earthengine-api geemap pandas matplotlib
```

Dependiendo de la evolución del cuaderno, también podrían utilizarse otras bibliotecas de análisis y visualización.

---

## Cómo ejecutar el cuaderno

1. **Clonar el repositorio**

   ```bash
   git clone <URL_DE_ESTE_REPO>
   cd barcelona-de-indias-time-serie
   ```

2. **(Opcional pero recomendado) Crear un entorno virtual**

   ```bash
   python -m venv .venv
   source .venv/bin/activate   # En Linux/WSL
   # .venv\Scripts\activate    # En Windows PowerShell
   ```

3. **Instalar dependencias**

   ```bash
   pip install earthengine-api geemap pandas matplotlib
   ```

4. **Autenticarte en Google Earth Engine**

   En el primer bloque del cuaderno se utilizan:

   ```python
   import ee
   ee.Authenticate(auth_mode="notebook")
   ee.Initialize()
   ```

   Sigue las instrucciones en pantalla para iniciar sesión con la cuenta que tiene acceso a GEE.

5. **Abrir el cuaderno**

   Ejecuta Jupyter Lab o Jupyter Notebook:

   ```bash
   jupyter lab
   # o
   jupyter notebook
   ```

   Luego abre `v1_zona_lag.ipynb` y ejecuta las celdas en orden.

---

## Flujo de trabajo resumido

- **Inicialización**: conexión con Google Earth Engine y carga de librerías.
- **Definición de la ROI**: polígono de la laguna de Barcelona de Indias.
- **Selección de datos**: imágenes Sentinel‑2 sobre la ROI y rango temporal de interés.
- **Índice de agua (MNDWI)**: cálculo del índice, análisis de histogramas y selección de umbral.
- **Máscara fija de la laguna**: construcción de un contorno estable usando promedios anuales.
- **Serie temporal**: extracción de valores agregados (por ejemplo, área de agua) para cada fecha.
- **Visualización y análisis**: gráficos y tablas para interpretar la dinámica de la laguna.

---

## Notas y posibles extensiones

- Ajustar el **umbral del MNDWI** según la calidad de las imágenes y la época del año.
- Probar otros índices (NDWI clásico, AWEI, etc.) o combinar varios índices.
- Incorporar datos climáticos (precipitación, temperatura) para relacionar la serie de agua con condiciones meteorológicas.
- Publicar tableros interactivos o mapas web a partir de los resultados.

---

## Licencia

La licencia de este proyecto aún no está definida explícitamente. Añade aquí tu licencia preferida (por ejemplo, MIT, GPL, Creative Commons) según el uso que quieras permitir.

