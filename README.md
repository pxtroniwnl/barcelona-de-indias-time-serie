# 🛰️ Monitoreo y Predicción de Cobertura Vegetal: Barcelona de Indias

![Python](https://img.shields.io/badge/Python-3.9+-blue.svg)
![Earth Engine](https://img.shields.io/badge/Google%20Earth%20Engine-API-green)
![Status](https://img.shields.io/badge/Status-Completado-brightgreen)

Este proyecto implementa un flujo de trabajo avanzado de **Ciencia de Datos** y **Teledetección** para analizar la evolución histórica de la vegetación acuática en la zona de Barcelona de Indias, Cartagena. Mediante el uso de satélites de radar y ópticos, se reconstruye una serie de tiempo de 25 años y se aplican modelos estocásticos para predecir el comportamiento del ecosistema en 2026.

## 📌 Descripción del Problema
La gestión de cuerpos hídricos en zonas residenciales de alto valor ambiental, como Barcelona de Indias, requiere un monitoreo constante. El exceso de vegetación acuática (macrófitas) puede indicar procesos de eutrofización. Este proyecto busca responder:
1.  **¿Cómo ha cambiado la laguna desde antes?**
2.  **¿Podemos predecir con precisión el área de vegetación para el próximo año?**

---

## 🚀 Flujo de Trabajo (Pipeline)

### 1. Adquisición de Datos Multiespectrales
Se diseñó un enfoque híbrido de fusión de sensores para cubrir dos décadas de datos:
* **Era Óptica (2000-2017):** Uso de **Landsat 5 y 8** con el índice **MNDWI** (Modified Normalized Difference Water Index) para detectar agua abierta y vegetación.
* **Era Radar (2017-2025):** Uso de **Sentinel-1 (SAR)**. La ventaja del radar es su capacidad de "ver" a través de las nubes de Cartagena, proporcionando datos consistentes en temporada de lluvias.

### 2. Procesamiento y Armonización
* **Intercalibración:** Dado que Landsat (30m) y Sentinel (10m) tienen resoluciones distintas, se aplicó un **Factor de Ajuste de Sesgo** basado en el año de solapamiento (2017) para garantizar una serie continua sin saltos artificiales.
* **Limpieza:** Máscaras de nubes dinámicas y re-muestreo mensual para estabilizar la señal.

### 3. Modelado Estadístico (Time Series Forecasting)
Se implementaron y compararon dos enfoques de la familia ARIMA:
* **Modelo Manual (SARIMA):** Ajustado mediante el análisis de funciones de autocorrelación (ACF/PACF), capturando la estacionalidad de 12 meses.
* **Modelo Optimizado (Auto-ARIMA):** Algoritmo de búsqueda que minimiza el Criterio de Información de Akaike (AIC).

---

## 📊 Resultados y Validación

### Métricas de Precisión
El modelo final (Auto-ARIMA) demostró una alta confiabilidad:
* **MAE (Error Absoluto Medio):** 993.41 m²
* **RMSE (Raíz del Error Cuadrático Medio):** 1530.96 m²

### Validación en Tiempo Real (2026)
Se realizó una validación externa con datos de **enero a marzo de 2026**, confirmando que el modelo captura la tendencia de reversión a la media, aunque se detectó un incremento atípico en marzo que sugiere la influencia de variables climáticas externas.



---

## 🛠️ Tecnologías Utilizadas
* **Google Earth Engine (GEE):** Procesamiento geoespacial en la nube.
* **Python:** Análisis de datos y modelado.
* **Pandas & NumPy:** Manipulación de series temporales.
* **Statsmodels & Pmdarima:** Motores de modelos ARIMA/SARIMA.
* **Matplotlib & Seaborn:** Visualización avanzada de datos.

---

## 📂 Estructura del Notebook
1.  **Configuración:** Inicialización de GEE y definición del ROI (Barcelona de Indias).
2.  **Extracción SAR:** Procesamiento de Sentinel-1 (2017-2025).
3.  **Extracción Legacy:** Procesamiento de Landsat 5/8 (2000-2017).
4.  **Armonización:** Unión de datasets y ajuste de escala.
5.  **Análisis:** Descomposición estacional (Trend, Seasonal, Resid).
6.  **Predicción:** Modelado y Forecast 2026.

---

## 📈 Conclusión
Este proyecto demuestra que la combinación de datos históricos de Landsat con la precisión actual de Sentinel-1 permite crear herramientas de monitoreo ambiental robustas. El modelo predice una estabilización de la vegetación en Barcelona de Indias para el ciclo 2026, sirviendo como soporte para la toma de decisiones en el mantenimiento del cuerpo de agua.

---
**Autor:** Alejandro Patrón Montero  
**Institución:** Universidad Tecnológica de Bolívar (UTB)  
**Fecha:** Marzo 2026
