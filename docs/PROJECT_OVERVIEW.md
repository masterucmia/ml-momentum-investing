# Documentación del proyecto: ml-momentum-investing

Fecha: 2026-02-14

Este documento resume la estructura del repositorio y describe carpeta por carpeta el propósito y uso principal de los archivos. Está pensado como guía rápida para navegar y ejecutar los notebooks y pipelines.

## Resumen del repositorio (raíz)
- `README.md`: descripción general del proyecto y puntos de inicio.
- `.git/`: control de versiones.
- `notebooks/`: notebooks de desarrollo, ETL, entrenamiento e inferencia.
- `results/`: artefactos de salida (modelos, predicciones, gráficas, csv/parquet).

## notebooks/
Carpeta principal con notebooks organizados por flujo de trabajo (ETL → entrenamiento → inferencia → alertas).

- `0. Entreamiento.ipynb`:
  - Propósito: Notebook principal de entrenamiento de modelos (experimentos, backtesting mínimo, guardado de artefactos).
  - Entradas típicas: datos procesados desde la capa Gold (parquets/csv) o resultados de notebooks de carga histórica.
  - Salidas típicas: modelos serializados, métricas, gráficos de rendimiento.

- `1. Bronze.ipynb`, `2. Silver.ipynb`, `3. Gold.ipynb`:
  - Propósito: pipeline ETL por capas (Bronze: ingestión raw → Silver: limpieza/normalización → Gold: features listos para modeling).
  - Uso: ejecutar en orden Bronze → Silver → Gold; suelen producir archivos parquet u objetos listos para entrenamiento.

- `4. Inference_Streaming.ipynb`:
  - Propósito: ejemplo/plantilla para inferencia en streaming o por lotes cortos; diseñado para integraciones en tiempo real.
  - Notas: revisar dependencias y claves/API si conecta a servicios externos.

- `5. Inference_Enero2026.ipynb`:
  - Propósito: ejecución de inferencia con dataset o ventana temporal concreta (enero 2026), útil como referencia reproducible.

- `6. Alerts - Telegram.ipynb`:
  - Propósito: notebook para generar alertas y enviarlas por Telegram. Incluye configuración de bot y ejemplos de mensajes.

- `ETL - Databricks/`:
  - Contenido: notebooks y documentación adaptada para correr el pipeline ETL en Databricks Lakehouse.
  - Archivos clave: `1. Bronze.ipynb`, `2. Silver.ipynb`, `3. Gold.ipynb`, `ETL_Databricks_Lakehouse_Pipeline.md`.

- `Financial-News-Sentiment/`:
  - Propósito: análisis de sentimiento de noticias financieras.
  - Archivos: `Top6News.ipynb` (notebook de ejemplo), `aliases.txt` (posible lista de símbolos/alias), `top6_news_sentiment.md` (notas explicativas).

- `Historical-Load/`:
  - Propósito: notebooks para ingesta de datos históricos desde APIs (Alpaca, Binance, Dukascopy, etc.).
  - Ejemplos: `alpaca_market_hist1y.ipynb`, `binance_crypto_hist1y.ipynb`, `dukascopy_forex_hist1y.ipynb`, `data_ingestion_alpaca_dukascopy_binace.md`.

## results/
- Carpeta donde se depositan artefactos producidos por los notebooks (resultados de experimentos, modelos, reportes). Revisa el contenido luego de ejecutar los pipelines para ver los archivos concretos generados.

## Recomendaciones de ejecución (orden sugerido)
1. Ejecutar los notebooks de `Historical-Load/` para poblar datos históricos.
2. Correr `notebooks/1. Bronze.ipynb` → `2. Silver.ipynb` → `3. Gold.ipynb` para transformar los datos.
3. Ejecutar `notebooks/0. Entreamiento.ipynb` para entrenar y generar modelos.
4. Probar `notebooks/4. Inference_Streaming.ipynb` o `5. Inference_Enero2026.ipynb` para usar los modelos en inferencia.
5. Configurar y probar `notebooks/6. Alerts - Telegram.ipynb` para alertas en producción o pruebas.

## Notas adicionales
- Si quieres documentación más detallada (entradas/outputs por notebook extraídas de las celdas markdown), puedo leer cada `.ipynb` y agregar una sección con:
  - Propósito extendido
  - Variables/parametros configurables
  - Archivos de entrada y salida concretos (nombres/formatos)
  - Dependencias externas/API keys a configurar
- Indica si quieres que haga esa extracción automática y guardaré la versión extendida en este mismo archivo.

---

Proyecto generado: resumen automático creado el 2026-02-14.
