#  Estrategias de Inversión Basadas en Momentum, Machine Learning e IA

##  Descripción
Proyecto centrado en el diseño, análisis y optimización de estrategias de inversión basadas en **momentum financiero**, utilizando **Machine Learning** e **Inteligencia Artificial** para identificar combinaciones óptimas entre rentabilidad esperada (μ) y riesgo (σ²).

---

## Objetivo General
Identificar combinaciones óptimas de **retorno esperado (μ)** y **varianza (σ²)** asociadas al momentum en distintos activos financieros, aplicando estrategias basadas en patrones de precios y variables externas, y utilizar modelos de Machine Learning para aprender qué factores explican estas combinaciones y cómo optimizarlas.

---

## Arquitectura del Sistema



El sistema implementa una arquitectura híbrida **batch + streaming** para la detección de momentum financiero en tiempo real.

### Componentes principales:
- **Ingesta en tiempo real** mediante API financiera y streaming.
- **Procesamiento distribuido** con Apache Spark sobre Azure Databricks.
- **Almacenamiento Lakehouse** siguiendo el patrón Bronze / Silver / Gold.
- **Modelos de Machine Learning** para inferencia y optimización.
- **Monitorización, gobierno del dato y control de accesos**.


<img width="1201" height="1192" alt="Arquitectura de Detección de Momentum Financiero en Tiempo Real" src="https://github.com/user-attachments/assets/68ce5a00-e504-4351-b431-8eb1bb399f79" />
---

##  Metodología

El proyecto se estructura en **tres fases principales**, que cubren desde el análisis de estrategias simples hasta la optimización de un portafolio inteligente.

---

##  Fase 1 – Aplicación de estrategias sobre un único activo (S&P 500)

### Estrategia 1 – *n* subidas consecutivas, mantener *k* periodos
**Descripción:**  
Se activa una inversión cuando se detectan *n* subidas consecutivas en el precio del activo. La inversión se mantiene durante *k* periodos (segundos, minutos o días).

**Objetivo:**  
Analizar cómo la frecuencia de subidas y la duración de la inversión influyen en el retorno esperado (μ) y la varianza (σ²).

---

### Estrategia 2 – Comparación por horizonte temporal
**Descripción:**  
Aplicación de la Estrategia 1 en distintos horizontes temporales:
- Corto plazo: 30 segundos  
- Mediano plazo: 1 minuto  
- Largo plazo: 5 minutos  

**Objetivo:**  
Evaluar cómo varía el momentum según la frecuencia de observación y determinar el horizonte más eficiente.

---

### Estrategia 3 – Subidas crecientes (gradiente positivo)
**Descripción:**  
Se activa una inversión cuando existen *n* subidas consecutivas y cada subida es mayor que la anterior.

**Objetivo:**  
Detectar patrones de aceleración del momentum y evaluar su impacto en la rentabilidad y estabilidad del retorno.

---

### Estrategia 4 – Subidas por encima de un umbral porcentual
**Descripción:**  
Solo se consideran subidas que superan un *threshold* mínimo (por ejemplo, +0,1 %). La inversión se activa tras *n* subidas consecutivas válidas.

**Objetivo:**  
Filtrar ruido de mercado y analizar si el uso de umbrales mejora el rendimiento ajustado al riesgo.

---

### Estrategia 5 – Threshold condicionado por señales externas
**Descripción:**  
La estrategia se activa solo si, además del patrón de subidas, una señal externa (análisis de sentimiento o predicción IA) indica alta probabilidad de subida.

**Objetivo:**  
Evaluar si la integración de información externa mejora la precisión y rentabilidad de las estrategias de momentum.

---

##  Fase 2 – Aplicación Multiactivo y Diversificación

**Descripción:**  
Las cinco estrategias se aplican a múltiples activos financieros representativos de distintos mercados, como:
- SPY
- QQQ
- BTC
- GLD
- EUR/USD

Posteriormente, los resultados se combinan en portafolios diversificados.

**Objetivos:**
- Comparar el comportamiento del momentum entre activos.
- Identificar activos con mayor eficiencia.
- Diversificar riesgo.
- Evaluar consistencia temporal de los resultados.

---

##  Fase 3 – Definición de la estrategia y portafolio óptimo

**Descripción:**  
Se utilizan modelos de **Machine Learning (redes neuronales)** entrenados con los resultados de las fases anteriores para aprender configuraciones óptimas.

**Objetivos específicos:**
- Determinar el horizonte temporal óptimo.
- Identificar los valores óptimos de *n* y *k*.
- Evaluar si estrategias complejas aportan valor real.
- Determinar thresholds porcentuales óptimos.
- Analizar el impacto de señales externas y su nivel mínimo de confianza.
- Evaluar la influencia del precio, volatilidad, volumen y variables macroeconómicas.

El resultado es un **portafolio dinámico e inteligente**, capaz de adaptarse automáticamente a las condiciones del mercado.

---

##  Comparativas del Entregable

El análisis final incluye:
- Comparación con estrategias clásicas de portafolios.
- Comparación con inversiones tradicionales ofrecidas por bancos.
- Análisis del impacto de comisiones en el retorno neto.

---

##  Autores
- Gilberto Augusto Alzuru Díaz  
- Miguel Alberto Calixto Godínez  
- George Jonnatha Carlos Sixto  
- Juan Antonio Linares Guerra  
- César Vicente Luna Martínez  
- Mauricio Zapata Querubin
