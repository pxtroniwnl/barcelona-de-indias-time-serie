# Modelos exportados

Artefactos finales de la fase ML del proyecto (índice de vegetación `vv-vh`, laguna de Barcelona
de Indias), listos para cargar y predecir sin reentrenar. Generados por la seccion 9.10 del
notebook `notebook/vegetacion/02_analisis_buchon.ipynb`.

## Versionado

- `v1/` — primer lote: modelos finales entrenados sobre TODO el historial (639 observaciones,
  2018-2026) con las configuraciones validadas en el backtest walk-forward del notebook.
- Para una version nueva, crear `v2/` (volver a ejecutar la celda de exportacion y actualizar
  el `manifiesto`). Nunca sobrescribir versiones ya publicadas.

## Contenido de `v1/`

8 artefactos = 4 modelos x 2 modalidades:

| Archivo | Modalidad | Formato |
| --- | --- | --- |
| `elasticnet_{crudo,estado}_v1.joblib` | proxima pasada / estado EWM-7 | joblib (scikit-learn pipeline + scaler) |
| `randomforest_{crudo,estado}_v1.joblib` | proxima pasada / estado EWM-7 | joblib (scikit-learn) |
| `lstm_{crudo,estado}_v1.pt` | proxima pasada / estado EWM-7 | torch (state_dict + meta de escalado) |
| `prophet_{crudo,estado}_v1.pkl` | proxima pasada / estado EWM-7 | pickle de Prophet (parametros completos) |

- `estado` = objetivo operativo, cobertura suavizada EWM-7 (~30 dias).
- `crudo` = observacion por pasada (~4-5 dias).
- `manifiesto_v1.json` = metricas reales del backtest, hiperparametros, features (`cols_x`, 105),
  versiones de dependencias y fecha.

## Como cargar y predecir

Las features se reconstruyen con el pipeline causal `build_dataset` de la seccion 9.2 del
notebook (lags, EWMs, precipitacion acumulada al dia previo). Ejemplo de minima para cada
framework (ver `/tmp` o consultar `smoke_modelos.py`):

```python
import joblib, torch
en = joblib.load('modelos/v1/elasticnet_estado_v1.joblib')   # EN / RF
pred = en.predict(X_fila)                                    # X_fila = df[cols_x].iloc[[-1]]

meta = torch.load('modelos/v1/lstm_estado_v1.pt', weights_only=False)
# reconstruir SeqModel(d_in=len(cols), hidden=48), load_state_dict, escalar con meta['mu']/'sd'

import pickle
with open('modelos/v1/prophet_estado_v1.pkl', 'rb') as f:
    m = pickle.load(f)                                       # m.predict(futuro con regresores)
```

Requisitos: mismo entorno del proyecto (`uv sync`): scikit-learn, torch, prophet, joblib.

## Rendimiento esperado (backtest walk-forward del notebook, sub-periodo 2022+)

- Estado EWM-7: ElasticNet R2=0.92 | RandomForest 0.92 | Prophet 0.81 | LSTM 0.76.
- Crudo: LSTM 0.36 | ElasticNet 0.35 | RandomForest 0.33 | Prophet 0.28.