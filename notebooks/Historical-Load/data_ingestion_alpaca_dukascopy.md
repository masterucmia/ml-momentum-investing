# Ingesta de Datos Financieros de Alta Frecuencia

Alpaca (Acciones) y Dukascopy (Forex M1)

## Índice

1. [Visión General del Sistema](#visión-general-del-sistema)
2. [Notebook 1 — Ingesta de Acciones (Alpaca)](#notebook-1--ingesta-de-acciones-alpaca)
   1. [Objetivo](#objetivo)
   2. [Funcionalidad](#funcionalidad)
   3. [Datos Generados](#datos-generados)
   4. [Limitaciones](#limitaciones)
3. [Notebook 2 — Ingesta Forex M1 (Dukascopy)](#notebook-2--ingesta-forex-m1-dukascopy)
   1. [Objetivo](#objetivo-1)
   2. [Funcionalidad](#funcionalidad-1)
   3. [Datos Generados](#datos-generados-1)
   4. [Consideraciones Específicas](#consideraciones-específicas)
4. [Relación entre Notebooks](#relación-entre-notebooks)

---

## Visión General del Sistema

La ingesta de datos se divide por tipo de mercado, utilizando en cada caso la fuente que permite la máxima resolución temporal disponible.

Mercados cubiertos:

- Acciones y ETFs
- Divisas (Forex)

Fuentes utilizadas:

- Alpaca Markets para acciones y ETFs
- Dukascopy para Forex

Limitación clave del sistema:

Alpaca no ofrece datos Forex a resolución de 1 minuto, únicamente a partir de 5 minutos.  
Para trabajar con Forex M1, el uso de Dukascopy es obligatorio.

---

## Notebook 1 — Ingesta de Acciones (Alpaca)

### Objetivo

Obtener datos históricos OHLCV a frecuencia de 1 minuto para activos bursátiles (acciones o ETFs), cubriendo aproximadamente un año de histórico mediante la API oficial de Alpaca Markets.

### Funcionalidad

El notebook realiza las siguientes operaciones:

- Autenticación contra la API de Alpaca
- Descarga de barras OHLCV a resolución de 1 minuto
- Uso de un feed de mercado soportado
- Consolidación de los datos en un único dataset temporal
- Garantía de orden cronológico
- Exportación del resultado a formato CSV

### Datos Generados

- Resolución temporal: 1 minuto
- Tipo de datos: OHLCV
- Uso principal:
  - Backtesting intradía
  - Análisis de momentum y volatilidad
  - Construcción de capas base en arquitecturas Lakehouse
  - Entrenamiento y validación de modelos cuantitativos

### Limitaciones

- Aplicable únicamente a acciones y ETFs
- No utilizable para Forex en M1
- Volumen elevado de datos
- Requiere gestión segura de credenciales

---

## Notebook 2 — Ingesta Forex M1 (Dukascopy)

### Objetivo

Descargar datos históricos del mercado Forex a frecuencia de 1 minuto para múltiples pares de divisas, cubriendo un año completo sin restricciones de granularidad.

### Funcionalidad

El notebook realiza las siguientes operaciones:

- Descarga de datos BID OHLCV desde Dukascopy
- Procesamiento de ficheros binarios comprimidos
- Reconstrucción de series temporales minuto a minuto
- Ingesta diaria para evitar pérdidas de información
- Consolidación del histórico completo por par
- Exportación del resultado a formato CSV

### Datos Generados

- Resolución temporal: 1 minuto
- Tipo de datos: OHLCV (precio BID)
- Uso principal:
  - Backtesting intradía en Forex
  - Estrategias de momentum de corto plazo
  - Análisis de microestructura
  - Construcción de datasets base para modelos cuantitativos

### Consideraciones Específicas

- Datos correspondientes al lado BID
- Proceso intensivo en tiempo y red
- Recomendado para entornos de investigación cuantitativa

---

## Relación entre Notebooks

Ambos notebooks son complementarios y permiten construir un dataset financiero homogéneo a resolución de 1 minuto, cubriendo:

- Acciones y ETFs mediante Alpaca
- Forex mediante Dukascopy

Este enfoque garantiza máxima resolución temporal y coherencia en análisis multi-mercado.
