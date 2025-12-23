# 🏠 US Housing Market Predictor: Enfoque Macroeconómico y de Oferta-Demanda

![Python](https://img.shields.io/badge/Python-3.8%2B-blue)
![Status](https://img.shields.io/badge/Status-Completed-green)
![Libraries](https://img.shields.io/badge/Libs-Pandas%20|%20ScikitLearn%20|%20XGBoost-orange)

Este proyecto aplica técnicas de **Machine Learning** para predecir el **Precio Mediano de Venta** de viviendas en Estados Unidos. El análisis combina datos fundamentales del mercado inmobiliario (inventario, ventas) con indicadores macroeconómicos clave (PBI, Desempleo) para modelar el comportamiento de precios en un periodo de alta volatilidad (2013-2023).

---

## 🎯 Objetivo del Proyecto

Determinar si es posible predecir los precios inmobiliarios futuros utilizando variables de oferta/demanda y salud económica, y superar las limitaciones de los modelos tradicionales de ML ante cambios estructurales del mercado (como la pandemia de COVID-19).

## 🗂️ Fuentes de Datos

El dataset maestro fue construido unificando tres fuentes con diferentes frecuencias temporales:

1.  **Redfin Data Center:** Datos semanales/mensuales del mercado inmobiliario (Precio, Inventario, Nuevos Listados).
2.  **FRED (Federal Reserve Economic Data):**
    *   **UNRATE:** Tasa de desempleo (Mensual).
    *   **GDP:** Producto Interno Bruto (Trimestral).

---

## ⚙️ Metodología y Workflow

### 1. ETL & Preprocesamiento (`01_ETL_EDA.ipynb`)
*   **Unificación Temporal:** Se estandarizaron todos los datos a una frecuencia **Mensual (Inicio de mes)**.
*   **Interpolación:** Se realizó *upsampling* e interpolación lineal para convertir datos trimestrales (GDP) a mensuales.
*   **Limpieza:** Manejo de valores nulos y alineación de índices temporales.

### 2. Ingeniería de Características (Feature Engineering)
*   **Lags (Rezagos):** Se crearon variables históricas (t-1, t-2, t-3) para que el modelo aprenda de tendencias pasadas inmediatas.
*   **Target:** El objetivo inicial fue predecir el precio absoluto del mes siguiente ($t+1$).

### 3. Modelado: El Desafío de la Extrapolación (`02_Modeling.ipynb`)

#### ❌ Experimento 1: Predicción de Precios Absolutos (Random Forest & XGBoost)
*   **Enfoque:** Entrenar modelos basados en árboles con los precios históricos (2013-2019).
*   **Resultado:** El modelo falló drásticamente en el set de prueba (2020-2022), obteniendo un **R2 negativo**.
*   **Diagnóstico:** Los modelos de árboles **no pueden extrapolar**. No pueden predecir un valor (ej: \$500k) si nunca vieron un valor tan alto en el entrenamiento (máximo histórico previo: ~$350k).

#### ✅ Experimento 2: Predicción de Diferencias (Enfoque Diferencial)
*   **Solución:** Transformar el problema para hacer la serie **estacionaria**.
*   **Nuevo Target:** En lugar de predecir el precio, se entrenó al modelo para predecir el **CAMBIO** en el precio respecto al mes anterior ($\Delta Price$).
*   **Reconstrucción:** `Precio_Predicho = Precio_Actual + Cambio_Predicho`.
*   **Resultado:** El modelo logró capturar la tendencia alcista post-pandemia, ajustándose correctamente a la dinámica del mercado.

---

## 📊 Resultados Clave

*   **Correlación Oferta-Precio:** Se confirmó una fuerte correlación inversa entre el **Inventario** disponible y el **Precio Mediano**. Cuando el inventario cae, los precios suben.
*   **Importancia de Variables:** El modelo identificó que el precio del mes anterior (inercia) y el nivel de inventario son los predictores más potentes a corto plazo.
*   **Lección Técnica:** Para series temporales económicas con tendencias fuertes, predecir diferencias/retornos es superior a predecir valores absolutos cuando se usan algoritmos no lineales como XGBoost.

---

## 🛠️ Estructura del Repositorio
'''bash   
   ├── data/ # Archivos CSV crudos y procesados
   ├── notebooks/
   │ ├── 01_ETL_EDA.ipynb # Limpieza, Fusión y Análisis Exploratorio
   │ └── 02_Modeling.ipynb # Entrenamiento, Fallos y Solución final (XGBoost)
   ├── results/ # Gráficos generados
   ├── README.md # Documentación del proyecto
   └── requirements.txt # Librerías necesarias
'''
## Requerimientos

## 🚀 Cómo ejecutar este proyecto

1.  Clonar el repositorio:
    ```bash
    git clone https://github.com/tu-usuario/us-housing-predictor.git
    ```
2.  Instalar dependencias:
    ```bash
    pip install pandas numpy matplotlib seaborn scikit-learn xgboost plotly
    ```
3.  Ejecutar los notebooks en orden numérico.
