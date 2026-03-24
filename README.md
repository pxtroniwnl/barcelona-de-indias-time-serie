# Análisis de Cobertura Vegetal en Zonas de Laguna de Barcelo de Indias

Este repositorio contiene un Jupyter Notebook diseñado para el monitoreo de la dinámica de vegetación en cuerpos de agua utilizando imágenes de radar de apertura sintética (SAR) de la misión **Sentinel-1** a través de **Google Earth Engine (GEE)**.

## 💡 Descripción del Proyecto

El objetivo principal de este notebook es procesar series temporales de datos de retrodispersión (backscatter) para identificar y cuantificar la presencia de vegetación (macrófitas o vegetación flotante) en una laguna específica. A diferencia de los sensores ópticos, el radar Sentinel-1 permite el monitoreo constante incluso en condiciones de nubosidad persistente.

### Características principales:
* **Acceso a Datos**: Uso de la API de Google Earth Engine para acceder a la colección `COPERNICUS/S1_GRD`.
* **Procesamiento Espacial**: Definición de una Región de Interés (ROI) y aplicación de máscaras de agua fijas.
* **Análisis de Series Temporales**: Generación de métricas de área (m²) y porcentaje de cobertura vegetal a lo largo del tiempo.
* **Visualización Interactiva**: Uso de `geemap` y `ipyleaflet` para visualizar mapas con umbrales de polarización VV.
* **Análisis Estadístico**: Identificación de picos estacionales de crecimiento.

## 🛠️ Tecnologías Utilizadas

* **Lenguaje**: Python 3
* **Plataforma**: Google Colab / Jupyter Notebook
* **Bibliotecas Clave**:
    * `ee`: Google Earth Engine API.
    * `geemap`: Visualización interactiva de datos geoespaciales.
    * `pandas`: Manejo de series temporales y estructuras de datos.
    * `matplotlib`: Generación de gráficos de tendencias.
    * `ipyleaflet`: Controles de mapas interactivos y widgets.

## 📊 Flujo de Trabajo

1.  **Autenticación**: Conexión con Google Earth Engine.
2.  **Filtrado de Datos**: Selección de imágenes Sentinel-1 con polarización VV y VH, filtradas por fecha y localización.
3.  **Segmentación**: Aplicación de umbrales (ej. `VV > -15`) para diferenciar el agua libre de la vegetación emergente/flotante.
4.  **Cálculo de Áreas**: Transformación de píxeles clasificados en medidas de superficie (m²).
5.  **Visualización**: Generación de gráficos de cobertura (%) y comparación visual de fechas específicas.

## 🚀 Cómo usar este Notebook

1.  Clona este repositorio.
2.  Asegúrate de tener una cuenta activa en [Google Earth Engine](https://earthengine.google.com/).
3.  Instala las dependencias necesarias:
    ```bash
    pip install earthengine-api geemap pandas matplotlib
    ```
4.  Abre `Zona_lago.ipynb` en tu entorno preferido (se recomienda Google Colab para facilitar la autenticación).
5.  Sigue las celdas de ejecución para procesar tus propios activos o los ejemplos incluidos.

## 📈 Ejemplo de Resultados

El análisis permite observar variaciones estacionales claras, identificando periodos de máxima expansión vegetal. El notebook incluye herramientas para exportar estos datos y visualizar las fechas con mayor densidad vegetal de forma automática.

---
