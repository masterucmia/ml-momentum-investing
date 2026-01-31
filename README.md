# Estrategias de Inversión Basadas en Momentum, Machine Learning e IA

## Índice

- [Descripción](#descripción)
- [Objetivo General](#objetivo-general)
- [Conceptos Básicos](#conceptos-básicos)
  - [Momentum financiero](#momentum-financiero)
  - [Rentabilidad esperada (μ)](#rentabilidad-esperada-μ)
  - [Riesgo y varianza (σ²)](#riesgo-y-varianza-σ²)
  - [Backtesting](#backtesting)
  - [Inercia y rebote](#inercia-y-rebote)
  - [Machine Learning en el proyecto](#machine-learning-en-el-proyecto)
- [Arquitectura del Sistema](#arquitectura-del-sistema)
  - [Componentes principales](#componentes-principales)
- [Metodología del Proyecto](#metodología-del-proyecto)
- [Estrategia de Momentum Utilizada](#estrategia-de-momentum-utilizada)
  - [Parámetros de la Estrategia de Momentum](#parámetros-de-la-estrategia-de-momentum)
  - [Parámetro X – Escala Temporal (Resampling)](#parámetro-x--escala-temporal-resampling)
  - [Parámetro K – Longitud del Patrón de Momentum](#parámetro-k--longitud-del-patrón-de-momentum)
  - [Parámetro N – Horizonte de Mantenimiento (Holding Period)](#parámetro-n--horizonte-de-mantenimiento-holding-period)
  - [Parámetro J – Umbral de Discretización del Movimiento](#parámetro-j--umbral-de-discretización-del-movimiento)
  - [Interacción entre los Parámetros](#interacción-entre-los-parámetros)
  - [Optimización de Parámetros](#optimización-de-parámetros)
- [Enfoque Multiactivo](#enfoque-multiactivo)
- [Tipos de Activos Analizados](#tipos-de-activos-analizados)
  - [Acciones de Alta Volatilidad](#acciones-de-alta-volatilidad)
  - [Acciones de Baja Volatilidad](#acciones-de-baja-volatilidad)
  - [ETFs (Exchange-Traded Funds)](#etfs-exchange-traded-funds)
  - [Forex (Mercado de Divisas)](#forex-mercado-de-divisas)
  - [Criptomonedas](#criptomonedas)
  - [Commodities (vía ETF)](#commodities-vía-etf)
  - [Fondos y ETFs de Bonos y Mercados Globales](#fondos-y-etfs-de-bonos-y-mercados-globales)
- [Justificación del Enfoque](#justificación-del-enfoque)
- [Autores](#autores)

## Descripción

Proyecto centrado en el diseño, análisis y optimización de estrategias de inversión basadas en **momentum financiero**, utilizando **Machine Learning** e **Inteligencia Artificial** para identificar combinaciones óptimas entre **rentabilidad esperada (μ)** y **riesgo (σ²)**.

El proyecto no se basa en la predicción directa de precios futuros, sino en la **evaluación sistemática de reglas de inversión** mediante simulación histórica (_backtesting_), con el objetivo de identificar configuraciones robustas y estables en el tiempo.

---

## Objetivo General

Identificar combinaciones óptimas de **retorno esperado (μ)** y **varianza (σ²)** asociadas al momentum en distintos activos financieros, aplicando estrategias basadas en patrones de precios y utilizando modelos de **Machine Learning** para aprender qué parámetros explican dichas combinaciones y cómo optimizarlas de forma eficiente.

---

## Conceptos Básicos

Esta sección introduce los conceptos fundamentales necesarios para comprender el proyecto.

### Momentum financiero

El **momentum** es un fenómeno observado en los mercados financieros según el cual los activos que han tenido un buen comportamiento reciente tienden a seguir comportándose bien en el corto o medio plazo, y viceversa.

En este proyecto, el momentum se identifica a partir de **patrones consecutivos de movimientos del precio**, evaluando su persistencia y estabilidad en el tiempo.

---

### Rentabilidad esperada (μ)

La **rentabilidad esperada (μ)** representa el beneficio medio que se espera obtener de una estrategia de inversión durante un periodo determinado.

En el contexto del proyecto, μ se estima a partir del **retorno acumulado generado por una estrategia durante el backtesting**, normalizado para poder comparar estrategias con distintas duraciones o frecuencias temporales.

---

### Riesgo y varianza (σ²)

El **riesgo** se asocia a la incertidumbre de los resultados obtenidos por una estrategia.  
Una medida habitual de este riesgo es la **varianza (σ²)**, que cuantifica la dispersión de los retornos alrededor de su valor medio.

Estrategias con alta rentabilidad pero elevada varianza se consideran menos deseables que aquellas con retornos más estables.

---

### Backtesting

El **backtesting** consiste en aplicar una estrategia de inversión sobre datos históricos para evaluar cómo habría funcionado en el pasado.

En este proyecto:

- Se simulan reglas de trading sobre series temporales reales.
- Se utiliza una separación **temporal train / test** para evitar sesgos y sobreajuste.
- No se utilizan datos futuros para tomar decisiones pasadas (_no look-ahead bias_).

---

### Inercia y rebote

Las estrategias evaluadas pueden operar bajo dos regímenes:

- **Inercia (Trend-Following)**: operar a favor de la tendencia dominante del mercado.
- **Rebote (Mean Reversion)**: operar en contra de la tendencia cuando el comportamiento histórico sugiere reversión.

El sistema identifica automáticamente cuál de estos regímenes es más adecuado para cada activo y configuración.

---

### Machine Learning en el proyecto

El **Machine Learning** no se utiliza para predecir precios futuros, sino como una herramienta para:

- Aprender la relación entre los parámetros de una estrategia y su rendimiento.
- Guiar la búsqueda de configuraciones prometedoras.
- Reducir el coste computacional frente a una búsqueda exhaustiva (_fuerza bruta_).

---

## Arquitectura del Sistema

El sistema implementa una arquitectura híbrida **batch + streaming** para la detección de momentum financiero en tiempo real.

### Componentes principales:

- **Ingesta en tiempo real** mediante API financiera y streaming.
- **Procesamiento distribuido** con Apache Spark sobre Azure Databricks.
- **Almacenamiento Lakehouse** siguiendo el patrón Bronze / Silver / Gold.
- **Modelos de Machine Learning** para inferencia y optimización.
- **Monitorización, gobierno del dato y control de accesos**.

## <img width="1201" height="1192" alt="Arquitectura de Detección de Momentum Financiero en Tiempo Real" src="https://github.com/user-attachments/assets/68ce5a00-e504-4351-b431-8eb1bb399f79" />

## Metodología del Proyecto

La metodología implementada en el proyecto se basa en la **evaluación sistemática de una única estrategia paramétrica de momentum**, aplicada de forma homogénea sobre un **conjunto amplio y diverso de activos financieros**.

El objetivo es analizar cómo se comporta el momentum bajo distintas condiciones de mercado (alta volatilidad, baja volatilidad, renta variable, divisas, criptomonedas, commodities y fondos), y utilizar **Machine Learning** para optimizar los parámetros de la estrategia evitando el sobreajuste.

---

## Estrategia de Momentum Utilizada

La estrategia implementada en el notebook se define por los siguientes elementos:

- **Discretización del movimiento del precio** mediante un umbral porcentual, que permite filtrar ruido de mercado.
- **Detección de patrones de `k` movimientos consecutivos** en la misma dirección.
- **Entrada en mercado**:
  - A favor de la tendencia (_inercia_), cuando el patrón histórico es positivo.
  - En contra de la tendencia (_rebote_), cuando el patrón histórico es consistentemente negativo.
- **Mantenimiento de la posición durante `n` periodos**.
- **Evaluación en distintas escalas temporales (`X`)** mediante resampling.
- **Validación temporal train / test**, evitando sesgos de anticipación (_look-ahead bias_).
- **Optimización de parámetros (`X, K, N, J`)** mediante Model-Based Optimization con Random Forest.

La estrategia es exactamente la misma para todos los activos, lo que permite comparar su comportamiento de forma objetiva.

### Parámetros de la Estrategia de Momentum

La estrategia de inversión se define mediante **cuatro parámetros fundamentales**: **X, K, N y J**.  
Estos parámetros controlan cómo se detecta el momentum, cuándo se entra en mercado, cuánto tiempo se mantiene la posición y qué movimientos se consideran relevantes.

La correcta selección de estos parámetros es clave para el rendimiento y la robustez de la estrategia.

---

#### Parámetro X – Escala Temporal (Resampling)

**Definición**  
El parámetro **X** representa la **escala temporal** a la que se analiza el precio del activo.  
Se obtiene mediante **resampling** de la serie de precios original (por ejemplo, de datos de alta frecuencia a intervalos de X minutos).

**Ejemplos**

- X = 1 → análisis a 1 minuto
- X = 5 → análisis a 5 minutos
- X = 15 → análisis a 15 minutos

**Función en la estrategia**

- Controla el **nivel de agregación temporal** de los datos.
- Permite estudiar el momentum en distintos horizontes (corto, medio o largo plazo).
- Reduce ruido de microestructura cuando X aumenta.

**Importancia**
El momentum puede existir en una escala temporal y desaparecer en otra.  
Optimizar X permite identificar el horizonte temporal donde el momentum es más persistente.

---

#### Parámetro K – Longitud del Patrón de Momentum

**Definición**  
El parámetro **K** indica el número de **movimientos consecutivos en la misma dirección** que deben observarse para considerar que existe momentum.

**Ejemplos**

- K = 2 → dos subidas consecutivas
- K = 5 → cinco movimientos consecutivos

**Función en la estrategia**

- Determina la **fuerza mínima del patrón** requerido para entrar en mercado.
- Valores pequeños de K generan más señales (mayor frecuencia).
- Valores grandes de K generan menos señales, pero potencialmente más fiables.

**Importancia**
K controla el equilibrio entre:

- Sensibilidad de la estrategia
- Robustez frente a señales espurias

---

#### Parámetro N – Horizonte de Mantenimiento (Holding Period)

**Definición**  
El parámetro **N** representa el número de periodos durante los cuales se mantiene la posición una vez abierta.

**Ejemplos**

- N = 1 → cierre inmediato
- N = 10 → mantener la posición durante 10 periodos

**Función en la estrategia**

- Controla la **duración de la exposición al mercado**.
- Influye directamente en el retorno acumulado y en el riesgo asumido.
- Afecta al número total de operaciones realizadas.

**Importancia**
Un valor de N demasiado corto puede no capturar completamente el momentum.  
Un valor demasiado largo puede exponer la estrategia a reversiones del mercado.

---

#### Parámetro J – Umbral de Discretización del Movimiento

**Definición**  
El parámetro **J** es un **umbral porcentual mínimo** que debe superar un movimiento de precio para ser considerado relevante.

Los retornos se discretizan de la siguiente forma:

- +1 si el retorno ≥ J
- −1 si el retorno ≤ −J
- 0 si el retorno está dentro del intervalo [−J, +J]

**Función en la estrategia**

- Filtra ruido de mercado y microfluctuaciones irrelevantes.
- Permite distinguir movimientos significativos de variaciones aleatorias.
- Reduce falsas señales en mercados laterales.

**Importancia**
J actúa como un filtro de calidad del momentum.  
Valores bajos de J generan más señales, pero más ruido.  
Valores altos de J reducen ruido, pero pueden eliminar oportunidades reales.

---

### Interacción entre los Parámetros

Los parámetros **X, K, N y J no actúan de forma independiente**, sino que interactúan entre sí:

- X define **a qué escala temporal** se observa el mercado.
- K define **cuán fuerte debe ser el patrón** para actuar.
- J define **qué movimientos son suficientemente relevantes**.
- N define **cuánto tiempo se mantiene la exposición**.

La combinación de estos cuatro parámetros define completamente una estrategia concreta.

---

### Optimización de Parámetros

Dado que el espacio de combinaciones posibles es muy amplio, los parámetros **(X, K, N, J)** se optimizan utilizando un enfoque de **Model-Based Optimization**, donde un modelo **Random Forest** aprende qué combinaciones producen mejores resultados y guía la exploración hacia regiones prometedoras del espacio de búsqueda.

Este enfoque permite:

- Reducir el coste computacional.
- Evitar la fuerza bruta.
- Disminuir el riesgo de sobreajuste.

## Enfoque Multiactivo

La estrategia se aplica sobre un **universo amplio de instrumentos financieros**, con el objetivo de:

- Analizar la persistencia del momentum en distintos mercados.
- Comparar activos con perfiles de riesgo muy diferentes.
- Evaluar la robustez y generalización de la estrategia.
- Reducir dependencia de un único mercado o activo.

---

## Tipos de Activos Analizados

A continuación se describe **brevemente qué representa cada tipo de activo** incluido en el estudio.

---

### Acciones de Alta Volatilidad

Empresas con fuertes oscilaciones de precio, alta sensibilidad al ciclo económico y elevada actividad especulativa.  
Son especialmente útiles para estudiar estrategias de momentum debido a la frecuencia e intensidad de sus movimientos.

**Ejemplos:**  
Tesla (TSLA), Nvidia (NVDA), AMD (AMD), Coinbase (COIN), Palantir (PLTR), Rivian (RIVN), Shopify (SHOP), Lucid Motors (LCID), Zoom (ZM), Virgin Galactic (SPCE).

---

### Acciones de Baja Volatilidad

Empresas consolidadas, con modelos de negocio estables y menor variabilidad en el precio.  
Permiten evaluar si el momentum existe también en activos defensivos.

**Ejemplos:**  
Coca-Cola (KO), Procter & Gamble (PG), Johnson & Johnson (JNJ), PepsiCo (PEP), Walmart (WMT), McDonald’s (MCD), Verizon (VZ), Duke Energy (DUK), Unilever (UL), Visa (V).

---

### ETFs (Exchange-Traded Funds)

Fondos cotizados que replican índices, sectores o regiones completas.  
Reducen el riesgo específico y permiten analizar momentum a nivel agregado.

**Ejemplos:**  
SPDR S&P 500 ETF (SPY), Invesco QQQ (QQQ), MSCI Emerging Markets (EEM), Vanguard FTSE Europe (VGK), ARK Innovation (ARKK), iShares Semiconductor ETF (SOXX).

---

### Forex (Mercado de Divisas)

Pares de divisas negociados en el mercado de divisas.  
Se caracterizan por alta liquidez, comportamiento continuo y fuerte influencia macroeconómica.

**Ejemplos:**  
EUR/USD, USD/JPY, GBP/USD, USD/CHF, AUD/USD, USD/CAD, NZD/USD, EUR/GBP, EUR/JPY, GBP/JPY.

---

### Criptomonedas

Activos digitales altamente volátiles, con mercados abiertos 24/7 y fuerte componente especulativo.  
Ideales para analizar momentum extremo y cambios rápidos de régimen.

**Ejemplos:**  
Bitcoin (BTC), Ethereum (ETH), Binance Coin (BNB), Ripple (XRP), Solana (SOL), Dogecoin (DOGE), Cardano (ADA), Avalanche (AVAX), Litecoin (LTC).

---

### Commodities (vía ETF)

Materias primas negociadas indirectamente a través de ETFs.  
Suelen estar influenciadas por factores macroeconómicos, geopolíticos y de oferta y demanda.

**Ejemplos:**  
Oro (GLD), Plata (SLV), Petróleo (USO), Gas Natural (UNG), Maíz (CORN), Soya (SOYB), Trigo (WEAT), Azúcar (CANE).

---

### Fondos y ETFs de Bonos y Mercados Globales

Instrumentos diversificados que representan mercados de renta fija o regiones completas.  
Permiten evaluar el comportamiento del momentum en entornos más conservadores.

**Ejemplos:**  
IEF, TLT, AGG, LQD, BNDX, DIA, IWM, FEZ, EWU, EWG, URTH.

---

## Justificación del Enfoque

El uso de un universo tan amplio de activos permite:

- Comparar el momentum en distintos regímenes de mercado.
- Identificar activos más propensos a estrategias de inercia o rebote.
- Evaluar la estabilidad del método fuera de un único contexto financiero.
- Reforzar la validez empírica de los resultados obtenidos.

## Autores

- Gilberto Augusto Alzuru Díaz
- Miguel Alberto Calixto Godínez
- George Jonnatha Carlos Sixto
- Juan Antonio Linares Guerra
- César Vicente Luna Martínez
- Mauricio Zapata Querubin
