# ETL-Databricks – Pipeline de Datos Financieros (Bronze / Silver / Gold)

## Índice

1. [Arquitectura General](#-arquitectura-general)
2. [Bronze – Ingesta de Datos OHLCV en Tiempo Casi Real](#-bronze--ingesta-de-datos-ohlcv-en-tiempo-casi-real)
   - [Objetivo de la Capa Bronze](#-objetivo-de-la-capa-bronze)
   - [Origen de los Datos](#-origen-de-los-datos)
   - [Universos de Activos Ingeridos](#-universos-de-activos-ingeridos)
   - [Modelo de Ingesta](#-modelo-de-ingesta)
   - [Gestión Temporal y Zonas Horarias](#-gestión-temporal-y-zonas-horarias)
   - [Estructura y Almacenamiento en Bronze](#-estructura-y-almacenamiento-en-bronze)
   - [Resultado de la Capa Bronze](#-resultado-de-la-capa-bronze)
3. [Silver – Normalización y Enriquecimiento OHLCV 1m](#-silver--normalización-y-enriquecimiento-ohlcv-1m-streaming-stateful)
   - [Objetivo de la Capa Silver](#-objetivo-de-la-capa-silver)
   - [Origen de los Datos](#-origen-de-los-datos-1)
   - [Modelo de Procesamiento](#-modelo-de-procesamiento)
   - [Limpieza y Validaciones Aplicadas](#-limpieza-y-validaciones-aplicadas)
   - [Normalización Temporal](#-normalización-temporal)
   - [Normalización de Identificadores](#-normalización-de-identificadores)
   - [Enriquecimiento con Dimensión de Activos](#-enriquecimiento-con-dimensión-de-activos)
   - [Lógica de Negocio Introducida](#-lógica-de-negocio-introducida)
   - [Resultado de la Capa Silver](#-resultado-de-la-capa-silver)
4. [Gold – Dataset Analítico ML-Ready](#-gold--dataset-analítico-ml-ready-streaming)
   - [Objetivo de la Capa Gold](#-objetivo-de-la-capa-gold)
   - [Origen de los Datos](#-origen-de-los-datos-2)
   - [Modelo de Procesamiento](#-modelo-de-procesamiento-1)
   - [Validaciones Finales](#-validaciones-finales)
   - [Selección de Variables](#-selección-de-variables-feature-selection)
   - [Normalización Temporal](#-normalización-temporal-1)
   - [Resultado de la Capa Gold](#-resultado-de-la-capa-gold)

---

Este directorio contiene los notebooks de **ETL ejecutados en Databricks** que implementan un pipeline completo de **ingesta, transformación y consolidación de datos financieros**, siguiendo una arquitectura **Lakehouse basada en capas Bronze, Silver y Gold**.

El pipeline está diseñado para trabajar con **series temporales financieras de alta frecuencia (1 minuto)** y cubrir múltiples clases de activos (acciones, ETFs, divisas, criptomonedas y commodities).

---

## Arquitectura General

El flujo de datos sigue el patrón clásico de Lakehouse:

1. **Bronze** – Ingesta cruda desde la fuente externa
2. **Silver** – Limpieza, validación y normalización _(pendiente de documentar)_
3. **Gold** – Construcción de datasets analíticos finales _(pendiente de documentar)_

Cada capa tiene una responsabilidad clara y desacoplada, lo que permite reprocesos controlados y trazabilidad completa del dato.

---

## Bronze – Ingesta de Datos OHLCV en Tiempo Casi Real

### Objetivo de la Capa Bronze

La capa **Bronze** es responsable de la **ingesta directa de datos financieros OHLCV** (_Open, High, Low, Close, Volume_) a **frecuencia de 1 minuto**, preservando el dato original con transformaciones mínimas.

Su finalidad es:

- Capturar el dato tal como lo expone la fuente externa
- Garantizar trazabilidad temporal
- Servir como base inmutable para las capas Silver y Gold

---

### Origen de los Datos

Los datos se obtienen desde la librería **Yahoo Finance**, utilizando su interfaz programática para descarga de series temporales.

Características del origen:

- Datos financieros públicos
- Resolución: **1 minuto**
- Periodo consultado: **día en curso**
- Activos de múltiples mercados y clases

La ingesta se ejecuta desde Databricks, que actúa como motor de procesamiento y orquestación.

---

### Universos de Activos Ingeridos

El pipeline Bronze cubre un universo amplio y diversificado de activos, organizado en grupos lógicos:

- **Acciones (S&P 500 y grandes compañías)**
- **Fondos cotizados (ETFs)**
- **Divisas (Forex)**
- **Criptomonedas**
- **Commodities (vía ETFs)**

Cada activo se identifica de forma explícita por su símbolo de mercado, y se procesa de manera independiente para garantizar robustez ante fallos parciales.

---

### Modelo de Ingesta

La ingesta sigue un modelo de **polling continuo**, con las siguientes características:

- Ejecución cíclica cada **60 segundos**
- Descarga únicamente de **velas cerradas**
- Procesamiento incremental dentro del día
- Control de último timestamp ingerido por activo

Este enfoque evita duplicidades y garantiza que solo se persistan nuevos datos.

---

### Gestión Temporal y Zonas Horarias

- Los timestamps se normalizan a **UTC**.
- Se conserva información explícita de la zona horaria lógica del mercado.
- Se añaden campos temporales derivados para facilitar particionado y consultas posteriores.

Esto permite:

- Comparar activos de distintos mercados
- Evitar ambigüedades temporales
- Optimizar el almacenamiento en el Data Lake

---

### Estructura y Almacenamiento en Bronze

Los datos se almacenan en el **Data Lake** utilizando el formato **Delta Lake**, con las siguientes características:

- Escritura en modo _append_
- Particionado por:
  - Año
  - Mes
  - Día
- Estructura orientada a series temporales

Cada registro contiene, entre otros:

- Timestamp de la vela
- Símbolo del activo
- Valores OHLC
- Volumen
- Fuente del dato
- Metadatos temporales

---

### Resultado de la Capa Bronze

El resultado es un dataset:

- Crudo y fiel al origen
- No agregado
- No enriquecido
- Optimizado para reprocesado y auditoría

La capa Bronze **no está pensada para análisis directo**, sino como base sólida para las siguientes capas.

---

### Consideraciones Importantes

- No se aplican reglas de negocio
- No se eliminan outliers
- No se realizan agregaciones
- Cualquier corrección o interpretación se delega a Silver y Gold

---

_La capa Bronze garantiza la captura fiable y trazable del dato financiero en alta frecuencia, sirviendo como pilar del resto del pipeline._

## Silver – Normalización y Enriquecimiento OHLCV 1m (Streaming Stateful)

### Objetivo de la Capa Silver

La capa **Silver** es responsable de **procesar en streaming los datos OHLCV de 1 minuto provenientes de Bronze**, aplicando **limpieza, normalización temporal y enriquecimiento semántico**, para producir un dataset **consistente y listo para análisis**.

A diferencia de Bronze, esta capa **sí introduce lógica de negocio controlada**, manteniendo siempre la trazabilidad del dato original.

---

### Origen de los Datos

- Lectura en **streaming** desde la **capa Bronze**, almacenada en formato **Delta Lake**.
- El stream consume de forma incremental los nuevos registros que se van incorporando a Bronze.
- Se ignoran eliminaciones para garantizar un modelo append-only coherente con ingesta histórica.

Silver actúa como **consumidor continuo** del lago Bronze.

---

### Modelo de Procesamiento

El procesamiento se implementa mediante un **modelo de streaming stateful con `foreachBatch`**, lo que permite:

- Tratar cada micro-lote como un DataFrame independiente
- Aplicar lógica compleja de limpieza y enriquecimiento
- Mantener control explícito de errores y escritura

El estado del stream se gestiona mediante **checkpoints**, garantizando:

- Reanudación segura ante fallos
- Procesamiento exactamente-una-vez
- Consistencia entre ejecuciones

---

### Limpieza y Validaciones Aplicadas

En cada batch, Silver aplica una serie de validaciones y filtros estrictos:

- Eliminación de duplicados por combinación **(símbolo, timestamp)**.
- Descarte de registros incompletos:
  - Timestamps nulos
  - Precios de cierre nulos
  - Símbolos no informados
  - Fuente de datos no identificada

Estas reglas garantizan que el dataset Silver **no contenga datos corruptos o ambiguos**.

---

### Normalización Temporal

- Todos los timestamps se convierten desde **UTC** a la zona horaria **Europe/Madrid**.
- Se mantiene coherencia temporal entre activos de distintos mercados.
- Se preservan los campos derivados de partición temporal (año, mes, día).

Esto facilita:

- Análisis intradía
- Backtesting coherente
- Comparación entre activos heterogéneos

---

### Normalización de Identificadores

- Los símbolos de mercado se normalizan para eliminar sufijos técnicos del origen.
- Se garantiza una **identidad única y consistente por activo**, independientemente de su procedencia.

---

### Enriquecimiento con Dimensión de Activos

Silver introduce una **dimensión de activos** mantenida internamente, que permite enriquecer cada registro con información semántica:

- Clase de activo (Acciones, Fondos, Forex, Cripto, Commodities)
- Nombre descriptivo del activo
- Coste operativo horario asociado a la clase de activo

Este enriquecimiento se realiza mediante una unión controlada, manteniendo el carácter determinista del pipeline.

---

### Lógica de Negocio Introducida

En esta capa se añaden **atributos económicos básicos**, como:

- Coste operativo por hora, dependiente del tipo de activo
- Clasificación por familia financiera

Estas variables serán utilizadas posteriormente en:

- Cálculo de rentabilidades netas
- Simulación de estrategias
- Modelos de decisión

Silver **no calcula métricas financieras complejas**, sino que prepara el terreno para Gold.

---

### Escritura y Almacenamiento

Los datos procesados se almacenan en la **capa Silver** con las siguientes características:

- Formato: **Delta Lake**
- Modo de escritura: _append_
- Particionado por:
  - Año
  - Mes
  - Día
- Dataset optimizado para lectura analítica y reprocesos

---

### Resultado de la Capa Silver

El resultado es un dataset:

- Limpio y sin duplicados
- Temporalmente coherente
- Enriquecido con contexto financiero
- Apto para análisis y modelado

Silver actúa como **fuente única de verdad intermedia** para la capa Gold.

---

### Consideraciones Importantes

- Silver depende completamente de Bronze.
- Cualquier cambio en reglas de limpieza o enriquecimiento requiere reprocesar Silver y Gold.
- Silver no debe usarse para ingesta ni reprocesado de capas anteriores.

---

📌 _La capa Silver transforma datos crudos en información financiera consistente, manteniendo un equilibrio entre fidelidad al origen y preparación analítica._

## Gold – Dataset Analítico ML-Ready (Streaming)

### Objetivo de la Capa Gold

La capa **Gold** es responsable de construir el **dataset final orientado a análisis cuantitativo y modelos de Machine Learning**, partiendo de los datos ya limpios y enriquecidos en Silver.

Esta capa define la **vista canónica de los datos financieros** que será utilizada para:

- Backtesting de estrategias
- Análisis estadístico
- Entrenamiento de modelos de ML

Gold prioriza **simplicidad, consistencia y estabilidad del esquema**.

---

### 🔌 Origen de los Datos

- Lectura en **streaming** desde la **capa Silver**, en formato **Delta Lake**.
- Consumo incremental de nuevos registros conforme Silver los va generando.
- Modelo _append-only_, coherente con ingesta histórica y reprocesos.

Gold actúa como **consumidor final del pipeline de datos**.

---

### Modelo de Procesamiento

El procesamiento se realiza mediante un **streaming controlado con `foreachBatch`**, lo que permite:

- Tratar cada micro-lote de forma determinista
- Aplicar validaciones finales de calidad
- Mantener control sobre escritura y errores

El uso de **checkpoints** garantiza:

- Reanudación automática tras fallos
- Procesamiento exactamente-una-vez
- Integridad del dataset final

---

### Validaciones Finales

Antes de persistir los datos en Gold, se aplican validaciones estrictas:

- Eliminación de registros sin timestamp válido
- Descarte de registros sin símbolo de activo
- Eliminación de registros sin precio de cierre
- Garantía de existencia de coste operativo

Estas validaciones aseguran que **Gold solo contenga observaciones completas y utilizables**.

---

### Selección de Variables (Feature Selection)

Gold define explícitamente el **conjunto mínimo de variables necesarias para análisis y ML**, evitando ruido innecesario:

- Información temporal (timestamp)
- Identidad del activo
- Clasificación del activo
- Coste operativo
- Precio de cierre

No se incluyen campos técnicos ni metadatos intermedios.

---

### Normalización Temporal

- Los timestamps se gestionan en la zona horaria **Europe/Madrid**.
- Se derivan explícitamente los campos:
  - Año
  - Mes
  - Día

Esto facilita:

- Particionado eficiente
- Ventanas temporales
- Agregaciones y resampling posteriores

---

### Escritura y Almacenamiento

Los datos se almacenan en la **capa Gold** con las siguientes características:

- Formato: **Delta Lake**
- Modo de escritura: _append_
- Particionado por:
  - Año
  - Mes
  - Día
- Esquema estable y orientado a consumo analítico

La capa Gold está optimizada para:

- Lectura intensiva
- Procesos de modelado
- Reproducibilidad de experimentos

---

### Resultado de la Capa Gold

El resultado es un dataset:

- Consistente y sin valores faltantes críticos
- Normalizado y estructurado
- Directamente utilizable por modelos de ML
- Independiente de la lógica de ingesta

Gold representa la **fuente única de verdad analítica** del sistema.

---

### Consideraciones Importantes

- Gold depende completamente de Silver.
- Cambios en el esquema de Gold deben justificarse analíticamente.
- Gold no debe usarse como fuente para reprocesar capas anteriores.

---

_La capa Gold materializa el dato financiero en su forma final, preparada para análisis cuantitativo y aprendizaje automático._
