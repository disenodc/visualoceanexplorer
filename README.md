# 🌊 Visual Ocean Explorer

**Visual Ocean Explorer (VOE)** es un prototipo de investigación que combina **Knowledge Graphs**, **GraphRAG**, **Ontological Intent Parsing** y **selección determinística de visualizaciones** para transformar consultas en lenguaje natural en análisis visuales reproducibles sobre datos de biodiversidad marina.

El sistema fue desarrollado utilizando como caso de estudio la dinámica de varamiento y reproducción de **Mirounga leonina** (Elefante Marino del Sur) en Península Valdés, Argentina, demostrando cómo una consulta en lenguaje natural puede convertirse automáticamente en una consulta SPARQL validada, un conjunto de datos verificado y una visualización científicamente fundamentada. 

---

## 📖 Descripción

Los programas de monitoreo marino generan grandes volúmenes de datos heterogéneos distribuidos entre repositorios institucionales, publicaciones científicas, campañas de campo y servicios oceanográficos. Aunque los grafos de conocimiento proporcionan una base semántica para integrar esta información, su consulta suele requerir experiencia en SPARQL.

Visual Ocean Explorer reduce esta barrera mediante una arquitectura de cuatro capas que permite a investigadores interactuar con un Knowledge Graph utilizando únicamente consultas en lenguaje natural. El sistema incorpora mecanismos de validación semántica, control de calidad de datos y generación reproducible de visualizaciones. 

---

## 🏗️ Arquitectura

La arquitectura se compone de cuatro etapas principales:

```text
Consulta en lenguaje natural
            │
            ▼
┌─────────────────────────────┐
│ Ontological Intent Parser   │
└─────────────────────────────┘
            │
            ▼
┌─────────────────────────────┐
│ GraphRAG + SPARQL Validator │
└─────────────────────────────┘
            │
            ▼
┌─────────────────────────────┐
│ Data Quality Engine         │
└─────────────────────────────┘
            │
            ▼
┌─────────────────────────────┐
│ Deterministic Chart Engine  │
└─────────────────────────────┘
            │
            ▼
      Visualización Vega-Lite
```

Cada capa incorpora controles específicos para garantizar consistencia semántica, trazabilidad y reducción de alucinaciones estructurales. 

---

## 🧠 Ontological Intent

Las preguntas realizadas por el usuario son transformadas a una representación estructurada basada en la ontología del Knowledge Graph.

Cada intención incluye:

- **AnalyticalIntent**
- **TargetEntities**
- **Constraints**
- **Confidence Score**

### Categorías soportadas

- `TemporalEvolution`
- `SpatialDistribution`
- `BivariateCorrelation`
- `CategoricalComparison`
- `EntityLookup`
- `Ranking`
- `PartToWhole`

Estas categorías están inspiradas en taxonomías clásicas de análisis visual y tareas de exploración de datos. 【2-4f9315】

---

## 🔎 GraphRAG y validación SPARQL

La capa GraphRAG realiza:

- Generación de consultas SPARQL a partir de la intención ontológica.
- Validación contra el esquema de la ontología.
- Detección de propiedades inexistentes o alucinadas.
- Reparación automática de consultas inválidas.
- Recuperación de subgrafos contextuales de hasta dos saltos.

Este enfoque evita errores frecuentes observados en sistemas RAG convencionales y mejora la precisión contextual de las respuestas. 

---

## ✅ Data Quality Engine

Antes de visualizar los resultados, los datos recuperados son sometidos a cuatro capas de validación:

### 1. Completeness Check

Verifica que propiedades obligatorias como:

- `oc:hasDate`
- `oc:hasValue`
- `oc:latitude`
- `oc:longitude`

se encuentren presentes y no sean nulas.

### 2. Type Conformance

Comprueba:

- Tipos numéricos válidos.
- Fechas ISO-8601.
- Coordenadas geográficas válidas.



### 3. Quality Flag Validation

Filtra observaciones marcadas como:

- `failed`
- `rejected`
- `invalid`

para evitar visualizaciones engañosas. 

### 4. Cardinality Profiling

Evalúa el volumen de resultados para recomendar agregaciones o modificar el tipo de visualización cuando existe sobrecarga perceptual. 

---

## 📊 Motor determinístico de selección de gráficos

A diferencia de sistemas que dejan la elección del gráfico a un LLM, VOE utiliza una matriz fija de reglas basada en:

- Intención analítica.
- Esquema recuperado.
- Calidad de los datos.

### Reglas principales

| Intent | Visualización |
|----------|---------------|
| TemporalEvolution | Line Chart |
| SpatialDistribution | Scatter Map / Heatmap |
| BivariateCorrelation | Scatter Plot + Density |
| CategoricalComparison | Grouped Bar Chart |
| Ranking | Sorted Bar Chart |
| PartToWhole | Stacked Bar Chart |
| EntityLookup | Table |

Esta aproximación garantiza que una misma consulta produzca siempre el mismo tipo de visualización, favoreciendo la reproducibilidad científica. 

---

## 🌐 Funcionalidades de la demo

La implementación incluye:

- Editor de consultas en lenguaje natural.
- Seguimiento visual del pipeline de ejecución.
- Explorador de benchmark MVB-500.
- Visualizaciones Vega-Lite interactivas.
- Inspector de consultas SPARQL.
- Reportes de calidad de datos.
- Visualización de subgrafos mediante D3.js.
- Contexto geográfico mediante Leaflet y OpenStreetMap.


---

## 🦭 Caso de estudio

### Especie

**Mirounga leonina** (Southern Elephant Seal)

### Ubicación

**Península Valdés, Argentina**

Sitio declarado Patrimonio Mundial por UNESCO y una de las colonias reproductivas más importantes de Elefante Marino del Sur en el Atlántico Sur.

### Datos modelados

La demo utiliza un subconjunto simulado de OceanGraph con aproximadamente:

| Recurso | Cantidad |
|----------|-----------|
| Observation Nodes | 42.600 |
| Places | 1.230 |
| Species | 3 |
| Cobertura temporal | 2010–2023 |

Variables representadas:

- Haul-out Count
- Pup Count
- Body Condition Index
- Sea Surface Temperature (SST)
- Chlorophyll-a


---

## 💬 Ejemplos de consultas

```text
Show me the seasonal haul-out pattern of elephant seals
at Caleta Valdés between 2015 and 2023
```

```text
Map all haul-out sites with more than 200 individuals
```

```text
Is SST during the breeding season correlated
with haul-out density?
```

```text
Rank beaches by total haul-out count in 2023
```

```text
Which datasets underpin the 2019 census?
```

Estas consultas forman parte del benchmark **MVB-500**, desarrollado para la evaluación experimental del sistema. 

---

## 📈 Resultados experimentales

Evaluación sobre el benchmark **MVB-500**:

| Sistema | Intent Accuracy | Context Precision | Chart Accuracy | Hallucination Rate |
|----------|----------|----------|----------|----------|
| Naive LLM | 54.8% | — | 31.2% | 44.6% |
| Vector RAG | 72.3% | 0.51 | 58.4% | 19.8% |
| GraphRAG-NoIntent | 88.1% | 0.82 | 74.0% | 5.2% |
| **Visual Ocean Explorer** | **93.6%** | **0.87** | **96.2%** | **2.8%** |


---

## 👩‍🔬 Estudio con usuarios

Se realizó una evaluación con **14 investigadores marinos**:

- 7 ecólogos
- 4 oceanógrafos
- 3 bioinformáticos

Comparando:

1. SPARQL + Python manual.
2. Dashboard BI tradicional.
3. Visual Ocean Explorer.

Resultados destacados:

- Reducción del **62% en el tiempo para obtener insights**.
- Menor carga cognitiva (NASA-TLX).
- Alta valoración de la adecuación de las visualizaciones.


---

## 🛠️ Tecnologías utilizadas

- HTML5
- JavaScript (ES6)
- Vega
- Vega-Lite
- Vega-Embed
- D3.js
- Leaflet
- OpenStreetMap


---

## 🚀 Ejecución

Clonar el repositorio y abrir:

```bash
index.html
```

La demostración funciona completamente en el navegador y no requiere backend, ya que la base de conocimiento simulada se encuentra embebida en la aplicación.

---

## 📚 Publicación asociada

> Visual Ocean Explorer: Automating Chart Selection over Linked Marine Data through Ontological Intent

El trabajo presenta una arquitectura que integra GraphRAG, validación SPARQL, control de calidad y selección determinística de gráficos para análisis visual reproducible sobre Knowledge Graphs marinos.

---

## 📄 Licencia

Este repositorio contiene un prototipo de investigación desarrollado con fines académicos y de demostración. Consulte el archivo `LICENSE` para conocer los términos de uso correspondientes.

---

## ✨ Resumen

Visual Ocean Explorer demuestra cómo la combinación de **Knowledge Graphs**, **GraphRAG**, **Ontological Intent** y **visualización determinística** puede proporcionar una interfaz fiable, explicable y reproducible para la exploración de datos científicos complejos sin requerir conocimientos de SPARQL.