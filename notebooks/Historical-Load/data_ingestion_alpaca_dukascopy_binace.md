# Ingesta de Datos Financieros de Alta Frecuencia

Alpaca (Acciones), Dukascopy (Forex M1) y Binance (Criptomonedas)

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
4. [Notebook 3 — Ingesta de Criptomonedas (Binance)](#notebook-3--ingesta-de-criptomonedas-binance)
   1. [Objetivo](#objetivo-2)
   2. [Funcionalidad](#funcionalidad-2)
   3. [Datos Generados](#datos-generados-2)
   4. [Consideraciones Específicas](#consideraciones-específicas-1)
5. [Relación entre Notebooks](#relación-entre-notebooks)

---

## Visión General del Sistema

La ingesta de datos se divide por tipo de mercado, utilizando en cada caso la fuente que permite la máxima resolución temporal disponible.

Mercados cubiertos:

- Acciones y ETFs
- Divisas (Forex)
- Criptomonedas

Fuentes utilizadas:

- Alpaca Markets para acciones y ETFs
- Dukascopy para Forex
- Binance para criptomonedas

Limitaciones clave del sistema:

- Alpaca no ofrece datos Forex a resolución de 1 minuto, únicamente a partir de 5 minutos.
- Alpaca y Dukascopy no cubren mercados de criptomonedas.

Para trabajar con Forex M1 es obligatorio el uso de Dukascopy.  
Para trabajar con criptomonedas se utiliza Binance, que ofrece mercado continuo 24/7.

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

## Notebook 3 — Ingesta de Criptomonedas (Binance)

### Objetivo

Descargar datos históricos del mercado de criptomonedas, incluyendo Bitcoin y otros criptoactivos de alta liquidez, utilizando datos públicos de Binance y permitiendo granularidades flexibles desde 1 minuto hasta 1 día.

### Funcionalidad

El notebook realiza las siguientes operaciones:

- Consulta dinámica de los pares USDT con mayor volumen en Binance
- Selección interactiva del criptoactivo a descargar
- Selección interactiva de la granularidad temporal
- Definición precisa de una ventana temporal cerrada correctamente
- Descarga paginada y robusta de klines históricos
- Manejo de reintentos, rate limits y endpoints alternativos
- Transformación de los datos en un dataset enriquecido
- Consolidación en un único fichero CSV

### Datos Generados

- Resolución temporal configurable (1m a 1d)
- Tipo de datos: OHLCV + métricas derivadas
- Variables adicionales:
  - Volúmenes base y quote
  - Trades
  - Flujo comprador
  - VWAP aproximado
  - Spread absoluto y relativo
  - Retornos porcentuales
  - Variables temporales (día de la semana, hora local)
- Uso principal:
  - Backtesting intradía y swing en criptoactivos
  - Análisis de volatilidad y microestructura
  - Construcción de datasets enriquecidos para modelado cuantitativo

### Consideraciones Específicas

- Mercado continuo 24/7 sin cierres
- No requiere API keys
- Volumen de datos elevado
- Especialmente adecuado para Bitcoin como activo base de análisis

---

## Relación entre Notebooks

Los tres notebooks son complementarios y permiten construir un dataset financiero homogéneo de alta frecuencia, cubriendo distintos tipos de mercado:

- Acciones y ETFs mediante Alpaca
- Forex mediante Dukascopy
- Criptomonedas mediante Binance

Este enfoque permite análisis comparativos entre mercados, backtesting consistente y el desarrollo de modelos cuantitativos multi-activo.
