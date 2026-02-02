# Análisis de Noticias Financieras y Sentimiento de Mercado

**Top6News — Pipeline híbrido GDELT + RSS + FinBERT**

---

## Índice

1. [Descripción general](#descripción-general)
2. [Objetivos del notebook](#objetivos-del-notebook)
3. [Dependencias del entorno](#dependencias-del-entorno)
4. [Control de calidad de fuentes (Whitelist)](#control-de-calidad-de-fuentes-whitelist)
5. [Preprocesado y normalización](#preprocesado-y-normalización)
6. [Obtención de noticias — Enfoque híbrido](#obtención-de-noticias--enfoque-híbrido)
   1. [GDELT](#gdelt)
   2. [Google News RSS (fallback)](#google-news-rss-fallback)
7. [Construcción semántica de queries](#construcción-semántica-de-queries)
8. [Pipeline híbrido de noticias](#pipeline-híbrido-de-noticias)
9. [Modelo de sentimiento financiero](#modelo-de-sentimiento-financiero)
10. [Construcción del texto de entrada](#construcción-del-texto-de-entrada)
11. [Conversión del sentimiento a señal numérica](#conversión-del-sentimiento-a-señal-numérica)
12. [Agregación temporal del sentimiento](#agregación-temporal-del-sentimiento)

---

## Descripción general

Este notebook implementa un pipeline completo de **obtención, filtrado, normalización y análisis de sentimiento de noticias financieras**, combinando múltiples fuentes de información textual con un modelo de lenguaje especializado en el dominio financiero.

El objetivo principal es **transformar noticias financieras recientes en señales cuantitativas de sentimiento**, a partir de texto periodístico estructurado y no estructurado, permitiendo su posterior integración en análisis financieros y modelos cuantitativos.

---

## Objetivos del notebook

- Obtener noticias financieras recientes relevantes para un activo, índice o mercado.
- Controlar la calidad de las fuentes informativas mediante una lista blanca de dominios.
- Normalizar textos y metadatos temporales bajo una zona horaria común.
- Combinar múltiples proveedores de noticias para mejorar la cobertura informativa.
- Aplicar un modelo de sentimiento financiero especializado.
- Transformar texto periodístico en señales numéricas agregables.

---

## Dependencias del entorno

El notebook se ejecuta en un entorno Python con soporte para:

- Manipulación y análisis de datos.
- Consumo de APIs REST y feeds RSS.
- Procesamiento y normalización de texto.
- Modelos de lenguaje basados en arquitecturas Transformer.
- Ejecución interactiva en entornos Jupyter.

Las versiones concretas de las librerías utilizadas se documentan explícitamente con el fin de **asegurar la reproducibilidad del entorno de ejecución**.

---

## Control de calidad de fuentes (Whitelist)

Se define una **lista blanca de dominios** considerados fiables desde el punto de vista financiero y periodístico.

Esta whitelist se utiliza para:

- Filtrar los resultados devueltos por GDELT y RSS.
- Reducir ruido informativo procedente de fuentes no especializadas.
- Evitar sesgos derivados de medios de baja calidad.
- Garantizar consistencia y fiabilidad en los datos de entrada al análisis de sentimiento.

---

## Preprocesado y normalización

Se implementan funciones auxiliares para:

- Limpieza de contenido HTML en títulos y resúmenes.
- Normalización de fechas procedentes de distintas fuentes.
- Conversión y alineación temporal a una zona horaria de referencia común.
- Eliminación de duplicados por título o enlace.

Estas operaciones garantizan la homogeneidad del conjunto de noticias analizadas.

---

## Obtención de noticias — Enfoque híbrido

El pipeline adopta un enfoque híbrido que combina dos fuentes principales de noticias financieras.

### GDELT

- Consulta al endpoint de documentos de GDELT.
- Uso de ventanas temporales acotadas a noticias recientes.
- Priorización de resultados por recencia.
- Normalización de fechas y metadatos.
- Deduplicación básica de artículos.

GDELT proporciona cobertura global y estructurada de medios internacionales.

---

### Google News RSS (fallback)

- Construcción dinámica de feeds RSS a partir de queries semánticas.
- Limitación explícita de la antigüedad de las noticias.
- Parseo de títulos, resúmenes, enlaces y fechas de publicación.
- Descarte de entradas sin fecha válida.

Este mecanismo actúa como complemento cuando la cobertura de la fuente principal es insuficiente.

---

## Construcción semántica de queries

Se incluye una capa de **construcción semántica de consultas** que:

- Mapea alias conocidos de activos financieros, índices y mercados.
- Incorpora sinónimos, tickers bursátiles y ETFs representativos.
- Captura distintas formas de referencia utilizadas por la prensa financiera.

Esta capa mejora la recuperación de noticias relevantes y reduce la dependencia de una única forma textual de búsqueda.

---

## Pipeline híbrido de noticias

La metodología combina:

- Un número controlado de noticias procedentes de GDELT.
- Un número controlado de noticias procedentes de RSS.
- Deduplicación global por título y enlace.
- Normalización de campos comunes entre proveedores.

El resultado es un conjunto reducido, reciente y relevante de noticias financieras.

---

## Modelo de sentimiento financiero

Se emplea un modelo de lenguaje especializado en finanzas basado en arquitectura Transformer.

Características principales:

- Clasificación del sentimiento en positivo, neutral o negativo.
- Probabilidad asociada a cada predicción.
- Entrenamiento específico sobre textos financieros.
- Ejecución en CPU para garantizar portabilidad y reproducibilidad.

El modelo se utiliza exclusivamente en modo inferencia, sin ajuste fino adicional.

---

## Construcción del texto de entrada

Para cada noticia:

- Se prioriza el uso combinado de **título y resumen** cuando ambos están disponibles.
- En ausencia de resumen, se utiliza únicamente el título.

Esta decisión define el contexto textual evaluado por el modelo de sentimiento.

---

## Conversión del sentimiento a señal numérica

El sentimiento categórico se transforma en una señal escalar:

- Positivo → +1  
- Neutral → 0  
- Negativo → −1  

El valor final se obtiene ponderando la polaridad por la probabilidad asociada a la predicción, permitiendo comparaciones cuantitativas y agregación temporal.

---

## Agregación temporal del sentimiento

Las noticias procesadas se agrupan a nivel diario con el objetivo de:

- Construir una serie temporal de sentimiento de mercado.
- Reducir el ruido individual por noticia.
- Facilitar el análisis y la visualización temporal.

Este paso produce una señal diaria agregada de sentimiento, lista para su integración en análisis financieros posteriores.
