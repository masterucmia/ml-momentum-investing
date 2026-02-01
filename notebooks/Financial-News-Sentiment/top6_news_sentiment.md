# Análisis de Noticias Financieras y Sentimiento de Mercado

Top6News — Pipeline híbrido GDELT + RSS + FinBERT

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

Este notebook implementa un pipeline completo de obtención, filtrado, normalización y análisis de sentimiento de noticias financieras, combinando múltiples fuentes de información textual y un modelo de lenguaje especializado en finanzas.

El objetivo principal es transformar noticias recientes en señales cuantitativas de sentimiento a partir de texto periodístico estructurado y no estructurado.

---

## Objetivos del notebook

- Obtener noticias financieras recientes relevantes para un activo, índice o mercado
- Controlar la calidad de las fuentes informativas mediante una lista blanca de dominios
- Normalizar textos y metadatos temporales en una zona horaria común
- Combinar múltiples proveedores de noticias para mejorar la cobertura
- Aplicar un modelo de sentimiento financiero especializado
- Transformar texto en señales numéricas agregables

---

## Dependencias del entorno

El notebook se ejecuta en un entorno Python con soporte para:

- Manipulación y análisis de datos
- Consumo de APIs REST y feeds RSS
- Procesamiento de texto
- Modelos de lenguaje basados en Transformers
- Ejecución en entornos Jupyter

Las versiones concretas de las librerías se documentan para asegurar reproducibilidad del entorno.

---

## Control de calidad de fuentes (Whitelist)

Se define una lista blanca de dominios considerados fiables desde el punto de vista financiero y periodístico.

Esta whitelist se utiliza para:

- Filtrar resultados devueltos por GDELT y RSS
- Reducir ruido informativo
- Evitar sesgos derivados de fuentes no especializadas
- Asegurar consistencia en los datos de entrada al análisis de sentimiento

---

## Preprocesado y normalización

Se implementan funciones auxiliares para:

- Limpieza de HTML en títulos y resúmenes
- Normalización de fechas provenientes de distintas fuentes
- Conversión y alineación temporal a una zona horaria de referencia
- Eliminación de duplicados por título o enlace

Estas funciones garantizan homogeneidad en los datos de entrada.

---

## Obtención de noticias — Enfoque híbrido

El notebook adopta un enfoque híbrido combinando dos fuentes principales de noticias.

### GDELT

- Consulta al endpoint de documentos de GDELT
- Ventanas temporales acotadas a noticias recientes
- Priorización por recencia
- Normalización de fechas y metadatos
- Deduplicación básica de resultados

GDELT aporta cobertura global y estructurada de medios internacionales.

---

### Google News RSS (fallback)

- Construcción dinámica de feeds RSS a partir de queries semánticas
- Limitación explícita de antigüedad de las noticias
- Parseo de títulos, resúmenes, enlaces y fechas
- Descarte de entradas sin fecha válida

Este mecanismo actúa como complemento de cobertura cuando la fuente principal es insuficiente.

---

## Construcción semántica de queries

Se incluye una capa de expansión semántica de consultas que:

- Mapea alias conocidos de índices y activos financieros
- Incorpora sinónimos, tickers y ETFs representativos
- Captura distintas formas de referencia utilizadas por la prensa financiera

Esta capa mejora la recuperación de noticias relevantes.

---

## Pipeline híbrido de noticias

La metodología combina:

- Un número controlado de noticias procedentes de GDELT
- Un número controlado de noticias procedentes de RSS
- Deduplicación global por título y enlace
- Normalización de campos comunes

El resultado es un conjunto reducido y relevante de noticias recientes.

---

## Modelo de sentimiento financiero

Se utiliza un modelo de lenguaje especializado en finanzas basado en arquitectura Transformer.

Características principales:

- Clasificación en positivo, neutral o negativo
- Probabilidad asociada a cada predicción
- Adaptación específica a textos financieros
- Ejecución en CPU para garantizar portabilidad

---

## Construcción del texto de entrada

Para cada noticia:

- Se prioriza el uso combinado de título y resumen cuando ambos están disponibles
- En ausencia de resumen, se utiliza únicamente el título

Esta decisión controla el contexto textual evaluado por el modelo de sentimiento.

---

## Conversión del sentimiento a señal numérica

El sentimiento categórico se transforma en una señal escalar:

- Positivo → +1
- Neutral → 0
- Negativo → −1

El valor final se obtiene ponderando la polaridad por la probabilidad asociada a la predicción, permitiendo comparaciones cuantitativas y agregación temporal.

---

## Agregación temporal del sentimiento

Las noticias procesadas se agrupan a nivel diario para:

- Construir una serie temporal de sentimiento
- Reducir ruido individual por noticia
- Facilitar su análisis y visualización temporal

Este paso produce una señal de sentimiento agregada por día.
