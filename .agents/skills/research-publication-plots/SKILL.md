---
name: research-publication-plots
description: Use when creating or styling figures for this project (matplotlib): publication-quality plots, rcParams (serif font), color palettes already used in analisis_buchon.ipynb, date formatting, high-DPI saving, and consistent labels. Applies to analysis and ML results in the notebooks.
---

# Gráficas Publicables

## Convenciones del repo

Las figuras de `analisis_buchon.ipynb` usan este estilo:

- `rcParams` orientado a publicación: fuente serif para ticks/ejes y títulos en español, figsize amplio y `dpi` alto para exportación.
- Colores concretos ya usados:
  - Azul oscuro `#1B4F72` (media móvil / líneas de reales).
  - Verde `#196F3D` (Savitzky-Golay).
  - Paleta sin ruido para múltiples variables meteorológicas (la tipica: `tab10`/`Set2` con alpha), evitando líneas que se confundan.
- Títulos y etiquetas en español: unidades (mm, °C, %, m/s, hPa, m², % cobertura).
- Ejes de fecha con formato año (Matplotlib `mdates`) para evitar etiquetas apiñadas.
- Subplots separados por variable (mismas reglas en el notebook); el panel índice y meteorológico se grafica por separado y, opcionalmente, un panel conjunto normalizado (min-max).

## Prácticas recomendadas

- `figsize=(14, w*h)` con `tight_layout()`/`bbox_inches='tight'`.
- Guardar con `dpi=200-300`, formato PNG (ya embebido en el notebook) o PDF para el paper.
- Generar figuras correctas: no dejar `plt.show()`/reliquias en notebooks ejecutados; preferir `fig, ax = plt.subplots(...)` y usar `ax.` explícito.
- Para resultados ML (skill ml-regression-timeseries): comparar predicción vs real con límites iguales en ejes, mostrar métricas en el título/leyenda.

## Verificación

Tras ejecutar un notebook con nbconvert headless (`uv run jupyter nbconvert --to notebook --execute --inplace <nb>.ipynb`), las figuras deben quedar embebidas y sin warnings de texto solapado.