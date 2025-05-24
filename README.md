# Análisis y Optimización de Campañas de Marketing

Este proyecto aborda el análisis avanzado de campañas de marketing, desde la limpieza y enriquecimiento de datos hasta la extracción de insights y recomendaciones accionables. Se utilizan técnicas de preprocesamiento, análisis exploratorio y visualización avanzada con Python.

## Dashboard Interactivo

Para una experiencia interactiva y visual del análisis completo, he creado un dashboard utilizando Streamlit que permite:

- Explorar los datos de manera dinámica
- Visualizar las métricas clave en tiempo real
- Filtrar los resultados por diferentes variables
- Generar gráficos personalizados
- Descargar reportes específicos

👉 **[Accede al dashboard aquí](https://marketingdashboard.streamlit.app/)**

El dashboard incluye:
- Métricas de ROI y conversión
- Análisis por canal y tipo de campaña
- Visualizaciones temporales
- Predicciones de rendimiento
- Recomendaciones automatizadas

*Nota: El dashboard se actualiza periódicamente con los últimos datos disponibles.*

---

## Estructura del Proyecto

- **data/**
  - `marketingcampaigns.csv`: Dataset original.
  - `limpio_marketingcampaigns.csv`: Dataset limpio y enriquecido.
- **código/**
  - `preprocesamiento.ipynb`: Limpieza, transformación y enriquecimiento de datos.
  - `EDA.ipynb`: Análisis exploratorio y visualización.
- **recomendaciones/**
  - `insights y recomendaciones.ipynb`: Síntesis de hallazgos y recomendaciones.

---

## 1. Preprocesamiento de Datos

Archivo: `código/preprocesamiento.ipynb`

### 1.1. Carga e Inspección Inicial

- Importación del dataset original con pandas.
- Revisión de dimensiones, tipos de datos, valores nulos y duplicados.
- Eliminación de filas duplicadas.

### 1.2. Limpieza de Valores Nulos

- **Numéricos**:  
  - Columnas `budget`, `roi`, `conversion_rate`, `revenue` convertidas a numérico (`pd.to_numeric`), forzando errores a `NaN`.
  - Imputación de nulos con la mediana de cada columna.
- **Categóricos**:  
  - Columnas `type`, `target_audience`, `channel`, `end_date` rellenadas con `"sin datos"`.

### 1.3. Normalización y Conversión de Formatos

- **Fechas**:  
  - Conversión de `start_date` y `end_date` a tipo `datetime` con manejo de errores (`errors='coerce'`).
  - Detección y reporte de fechas no válidas.
- **Números**:  
  - Conversión de columnas numéricas a formato español (coma decimal, punto de miles) mediante funciones personalizadas.
  - Corrección de posibles errores de formato en columnas como `inversión` y `facturación`.

### 1.4. Detección y Eliminación de Valores Anómalos

- Eliminación de filas con valores negativos en la columna `inversión`.
- Revisión de outliers, pero sin eliminarlos en esta fase (se analizan en EDA).

### 1.5. Enriquecimiento del Dataset

- **Duración de campañas**:  
  - Cálculo de la columna `duración días` como diferencia entre fechas.
  - Relleno de valores no computables con `"sin datos"`.
- **Categorizaciones**:  
  - Clasificación de duración (`corta`, `media`, `larga`) según la mediana.
  - Clasificación de inversión y beneficio (`bajo`, `medio`, `alto`) usando rangos relativos a la mediana.
- **Beneficio neto**:  
  - Cálculo de `beneficio neto` como diferencia entre `facturación` e `inversión`.
  - Conversión a formato español.
- **Éxito de campaña**:  
  - Nueva columna booleana (`campaña exitosa`) según si el beneficio neto es positivo.

### 1.6. Corrección de Errores de Escritura

- Revisión de valores únicos y recuentos en columnas categóricas (`canal`, `audiencia target`) para detectar y corregir errores manuales.

### 1.7. Exportación

- Guardado del DataFrame limpio y enriquecido como `limpio_marketingcampaigns.csv`.

### 1.8. Justificación de las Técnicas de Preprocesamiento

El preprocesamiento es una fase crítica en cualquier proyecto de análisis de datos, ya que la calidad de los datos determina la fiabilidad de los resultados y conclusiones. A continuación se explica el motivo de cada técnica aplicada:

- **Eliminación de duplicados:**  
  Para evitar sesgos y distorsiones en el análisis, es fundamental que cada campaña esté representada una sola vez. Los duplicados pueden inflar métricas y generar conclusiones erróneas.

- **Imputación de valores nulos:**  
  - *Numéricos (mediana):* La mediana es robusta frente a outliers y permite mantener la distribución original de los datos sin verse afectada por valores extremos, asegurando que los análisis estadísticos posteriores sean representativos.
  - *Categóricos ("sin datos"):* Etiquetar los nulos permite distinguir entre datos realmente ausentes y categorías válidas, evitando la eliminación de información potencialmente útil y facilitando el análisis de la calidad del dato.

- **Conversión de tipos y formatos:**  
  - *Fechas:* Convertir a `datetime` permite realizar cálculos temporales (como duración de campañas) y análisis estacionales. El manejo de errores evita que datos mal formateados interrumpan el flujo de trabajo.
  - *Números en formato español:* Unifica el formato numérico para evitar errores en cálculos y visualizaciones, especialmente en contextos internacionales donde el separador decimal puede variar.

- **Eliminación de valores anómalos (negativos):**  
  Valores negativos en inversión no tienen sentido en el contexto de campañas de marketing y suelen indicar errores de carga o registro. Eliminarlos mejora la integridad del análisis.

- **Enriquecimiento del dataset:**  
  - *Duración de campañas:* Permite analizar el impacto del tiempo en el rendimiento.
  - *Categorizaciones por mediana:* Facilita la segmentación y comparación entre campañas de diferentes escalas, haciendo el análisis más interpretable.
  - *Beneficio neto y éxito:* Añadir métricas derivadas permite evaluar la rentabilidad real y clasificar campañas de forma objetiva.

- **Corrección de errores de escritura:**  
  Revisar y corregir valores categóricos evita la fragmentación de categorías (por ejemplo, "Email" vs "email"), lo que podría diluir resultados y dificultar la interpretación.

- **Exportación del dataset limpio:**  
  Garantiza la reproducibilidad y permite que el análisis exploratorio y los modelos posteriores trabajen sobre una base de datos fiable y estandarizada.

En resumen, cada técnica aplicada en el preprocesamiento tiene como objetivo maximizar la calidad, consistencia y utilidad de los datos, sentando una base sólida para el análisis exploratorio, la visualización y la toma de decisiones basada en datos.

---

## 2. Análisis Exploratorio de Datos (EDA)

Archivo: `código/EDA.ipynb`

### 2.1. Preparación

- Carga del dataset limpio.
- Conversión de columnas a formato numérico para análisis (`roi_num`, `facturación num`, `inversión num`, etc.).
- Configuración visual avanzada con Matplotlib y Seaborn (paletas, estilos, tamaños).

### 2.2. Visualizaciones y Análisis Realizados

#### 2.2.1. Distribución y Comparación de Canales

- **Gráficos de barras**:  
  - Número de campañas por canal.
  - ROI promedio por canal (horizontal).
- **Gráfico de burbujas**:  
  - Relación entre canal, inversión y facturación (tamaño de burbuja proporcional a facturación, color a inversión).

#### 2.2.2. Análisis por Tipo de Campaña

- **Gráficos de barras**:  
  - Ingresos totales por tipo de campaña.
- **Boxplots**:  
  - Distribución de la duración por tipo de campaña.
  - ROI por canal y tipo de campaña (excluyendo categorías irrelevantes).

#### 2.2.3. Análisis de ROI

- **Histograma**:  
  - Distribución del ROI en todas las campañas.
- **Boxplot**:  
  - ROI por canal y tipo de campaña.

#### 2.2.4. Tasa de Conversión

- **Gráfico de barras**:  
  - Tasa de conversión promedio por tipo de cliente (B2B, B2C, sin datos).

#### 2.2.5. Beneficio Neto

- **Scatter plot**:  
  - Top 10 campañas por beneficio neto, con tamaño de punto según duración.

#### 2.2.6. Relación Inversión-Ingresos

- **Hexbin plot**:  
  - Densidad de campañas según inversión y facturación.
  - Etiquetado de campañas con mayor facturación.

#### 2.2.7. Campañas Exitosas

- **Gráfico de burbujas**:  
  - Campañas con ROI > 0.5 e ingresos > 500.000€, color por tasa de conversión, tamaño por beneficio neto.

#### 2.2.8. Patrones Estacionales

- **Line plots múltiples**:  
  - Evolución mensual de ROI, tasa de conversión y facturación promedio.
  - Uso de ejes secundarios y leyenda combinada.

#### 2.2.9. Relación Duración-Facturación

- **Hexbin plot**:  
  - Relación entre duración de campaña y facturación.

### 2.3. Conclusiones Parciales y Generales

- Documentación de hallazgos clave tras cada análisis, incluyendo patrones, outliers, correlaciones y recomendaciones preliminares.

---

## 3. Insights y Recomendaciones

Archivo: `recomendaciones/insights y recomendaciones.ipynb`

- Síntesis de los principales hallazgos del EDA.
- Recomendaciones técnicas y estratégicas, incluyendo:
  - Potenciar canales y tipos de campaña más eficientes.
  - Revaluar campañas de bajo presupuesto.
  - Establecer umbrales de ROI.
  - Optimizar duración de campañas.
  - Ajustar el calendario según estacionalidad.
  - Mejorar segmentación y personalización.

---

## 4. Requisitos Técnicos

- Python 3.x
- pandas
- numpy
- matplotlib
- seaborn

---

## 5. Ejecución

1. Ejecutar el preprocesamiento en `código/preprocesamiento.ipynb`.
2. Ejecutar el análisis exploratorio en `código/EDA.ipynb`.
3. Consultar los insights y recomendaciones en `recomendaciones/insights y recomendaciones.ipynb`.

---

## 6. Autoría

Proyecto realizado para Upgrade Hub como ejercicio avanzado de análisis de datos de marketing.