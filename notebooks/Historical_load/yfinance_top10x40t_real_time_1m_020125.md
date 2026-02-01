# yfinance_top10x40t_real_time_1m_020125

## Tabla de contenidos

- [Descripción general](#descripción-general)
- [Objetivo](#objetivo)
- [Contexto dentro del proyecto](#contexto-dentro-del-proyecto)
- [Activos monitorizados](#activos-monitorizados)
- [Frecuencia de ejecución y rate limiting](#frecuencia-de-ejecución-y-rate-limiting)
- [Arquitectura lógica del notebook](#arquitectura-lógica-del-notebook)
- [Lógica de polling](#lógica-de-polling)
- [Flag de cambio](#flag-de-cambio)
- [Formato de salida](#formato-de-salida)
- [Zona horaria](#zona-horaria)
- [Gestión de mercados cerrados](#gestión-de-mercados-cerrados)
- [Estrategia de obtención de datos (fallback)](#estrategia-de-obtención-de-datos-fallback)
- [Persistencia y uso de datos](#persistencia-y-uso-de-datos)
- [Dependencias](#dependencias)
- [Ejecución](#ejecución)
- [Limitaciones conocidas](#limitaciones-conocidas)
- [Casos de uso](#casos-de-uso)
- [Estado](#estado)

---

## Descripción general

Este notebook implementa un sistema de **captura de datos financieros en tiempo casi real**, basado en un mecanismo de **polling minuto a minuto**, utilizando la librería yfinance.

Se monitorizan **40 activos financieros** pertenecientes a distintas clases (acciones, ETFs, criptomonedas, forex y commodities), registrando de forma continua:

- Precio del activo
- Hora real de cotización del mercado
- Hora de solicitud del dato
- Indicador binario de cambio de cotización

El sistema está diseñado para **mantener series temporales continuas**, incluso cuando el mercado no genera nuevas cotizaciones, evitando huecos en los datos y facilitando su uso posterior en análisis cuantitativo.

---

## Objetivo

Los objetivos principales de este notebook son:

- Obtener precios minuto a minuto de múltiples activos financieros.
- Detectar si existe un **cambio real de cotización** entre rondas consecutivas.
- Mantener continuidad temporal aunque el mercado esté cerrado.
- Evitar huecos en las series temporales.
- Generar datos reutilizables para análisis de momentum y Machine Learning.

---

## Contexto dentro del proyecto

Este notebook forma parte del proyecto **ML-MOMENTUM-INVESTING** y constituye la **fase de adquisición de datos de alta frecuencia**.

Los datos generados aquí se utilizan posteriormente en notebooks dedicados a:

- Discretización de señales de momentum (parámetro J).
- Construcción de variables explicativas.
- Backtesting de estrategias cuantitativas.
- Entrenamiento y validación de modelos de Machine Learning.

---

## Activos monitorizados

El notebook monitoriza un total de **40 tickers**, agrupados en las siguientes categorías:

- Acciones (S&P 500 – Top 10)
- ETFs principales
- Criptomonedas (vs USD)
- Forex (Tipos de cambio)
- Commodities (Futuros)

La definición completa de los activos se encuentra en el diccionario `ASSET_GROUPS`.

---

## Frecuencia de ejecución y rate limiting

- Frecuencia de polling: **1 llamada por ticker cada 60 segundos**
- Total de llamadas: **hasta 40 llamadas por minuto**

Consideraciones relevantes:

- Yahoo Finance permite aproximadamente **33 llamadas por minuto**.
- Con **20 activos**, el sistema ha funcionado de forma estable durante varios días continuos.
- Con **40 activos**, existe riesgo de error _Too Many Requests_.

La frecuencia se controla explícitamente mediante pausas temporales definidas en la variable `POLLING_INTERVAL_SEC`, configurada a 60 segundos.

---

## Arquitectura lógica del notebook

El notebook sigue una arquitectura secuencial diseñada para ejecución prolongada:

1. Definición de activos y parámetros globales.
2. Inicialización de estructuras de estado por ticker.
3. Ejecución de un bucle infinito de polling.
4. Comparación del estado actual con el estado previo.
5. Registro estructurado de resultados.
6. Control de tiempo para la siguiente ronda.

Esta arquitectura permite mantener el sistema en ejecución continua sin pérdida de coherencia temporal.

---

## Lógica de polling

En cada ronda de ejecución:

1. Se solicita el último precio disponible del activo.
2. Se obtiene la **hora real de cotización** del mercado.
3. Se compara con el último precio y timestamp registrados.
4. Se determina si existe una actualización efectiva.

Esta lógica es necesaria porque:

- Muchos activos no cotizan cada minuto.
- Los mercados no operan en fines de semana y festivos.
- La hora de cotización puede ser anterior a la hora de solicitud.

---

## Flag de cambio

Para cada activo y minuto se calcula un indicador binario:

- `1` → el precio o la hora de cotización han cambiado.
- `0` → no hubo nueva cotización.

Cuando el flag es `0`, se vuelve a registrar el último valor válido, garantizando la continuidad temporal de la serie.

---

## Formato de salida

Cada activo genera una línea de salida con el siguiente formato lógico:

[REALTIME] número_activo | flag_cambio | grupo | símbolo | precio | hora_cotización | hora_solicitud

Donde:

- número_activo: posición del activo dentro de la ronda (1 a 40)
- flag_cambio: indicador de cambio (1 = cambio, 0 = sin cambio)
- grupo: categoría del activo
- símbolo: ticker
- precio: último precio disponible
- hora_cotización: hora real del mercado (local)
- hora_solicitud: hora en que se realiza la petición

---

## Zona horaria

La zona horaria base utilizada es **Europe/Madrid**.

Todos los timestamps se convierten desde UTC a hora local para mantener coherencia temporal.

---

## Gestión de mercados cerrados

Cuando el mercado está cerrado o no hay nueva cotización:

- Se mantiene el último precio válido.
- El flag de cambio se marca como `0`.
- La serie temporal permanece continua.

Este comportamiento es crítico para análisis históricos y backtesting.

---

## Estrategia de obtención de datos (fallback)

Para aumentar la robustez frente a inconsistencias internas de Yahoo Finance, los datos se obtienen siguiendo este orden:

1. Información directa del ticker.
2. Última vela disponible de 1 minuto.

---

## Persistencia y uso de datos

Actualmente, los datos se muestran por consola con formato estructurado.

El diseño permite extender fácilmente el sistema para:

- Persistencia en CSV o Parquet.
- Almacenamiento en bases de datos.
- Integración con pipelines de datos o sistemas de streaming.

---

## Dependencias

- yfinance
- pandas

---

## Ejecución

Desde la raíz del proyecto, ejecutar el notebook o script correspondiente.

La ejecución es continua hasta interrupción manual mediante **Ctrl + C**.

---

## Limitaciones conocidas

- No es un feed tick-by-tick real.
- Los datos pueden estar retrasados.
- Yahoo Finance puede modificar su comportamiento sin previo aviso.
- Existe riesgo de rate limiting.
- No recomendado para trading en vivo.

---

## Casos de uso

- Construcción de datasets minuto a minuto.
- Análisis de microestructura de mercado.
- Generación de señales de momentum.
- Backtesting de estrategias cuantitativas.
- Entrenamiento de modelos de Machine Learning.

---

## Estado

- ✔ Funcional
- ✔ Probado en ejecución prolongada
- ✔ Integrado en el flujo general del proyecto
