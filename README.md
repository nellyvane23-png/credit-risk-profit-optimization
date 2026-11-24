# Credit Risk Scoring & Portfolio Profit Optimization (P&L)

[![Open In Colab](https://google.com)](https://github.com)

## Resumen Ejecutivo
Este proyecto desarrolla un sistema de originación crediticia *end-to-end* utilizando **894,251 préstamos históricos reales** (Lending Club, 2015–2018). A diferencia de los enfoques puramente estadísticos, este trabajo traduce la Probabilidad de Incumplimiento (*PD*) a un **Estado de Resultados (P&L) unitario** para definir la política de aprobación que maximiza la rentabilidad neta del portafolio.

### Impacto Financiero Estimado (Validación Out-of-Time)
* **Estrategia Pasiva (Aprobar 100% de solicitudes):** Bad Rate de **19.87%** | Resultado Neto: **-$15,431,094 USD**
* **Política Óptima con LightGBM (Corte `PD ≤ 28.5%`):** Tasa de aprobación del **70.38%** | Bad Rate controlado en **13.35%** | Resultado Neto: **-$870,223 USD**
* **Valor Generado:** El modelo rescató **+$14,560,871 USD (+94.36% de mejora financiera)** rechazando al 29.6% de solicitantes de mayor riesgo sin sacrificar volumen sano.

---

## Hallazgos Clave y Metodología

### 1. Análisis de Cosechas (*Vintage Analysis*) y Sesgo de Maduración
Se evaluó la evolución trimestral de morosidad frente a tasas de interés cobradas, detectando el deterioro de 2016 (`Bad Rate` de 25.96%) y advirtiendo sobre el sesgo de maduración (*right-censoring*) en los últimos trimestres de 2018.

![Análisis de Cosechas](01_vintage_analysis.png)

### 2. Modelado Predictivo *Out-of-Time* (Benchmark vs. LightGBM)
Para evitar fuga de información temporal (*Data Leakage*), el entrenamiento se realizó con cosechas previas a julio de 2017 (759,998 créditos) y se validó a futuro sobre 134,253 créditos posteriores:

| Métrica | Regresión Logística (Benchmark) | LightGBM (Modelo Principal) |
| :--- | :---: | :---: |
| **AUC-ROC** | 0.7073 | **0.7147** |
| **PR-AUC** | 0.3542 | **0.3722** |
| **Estadístico KS** | 0.3024 | **0.3110** |

![Curva ROC](02_roc_curve.png)

### 3. Explicabilidad del Modelo (*SHAP Values*)
Mediante valores SHAP se validó que las decisiones del modelo responden a fundamentos financieros sólidos: tasa de interés (`int_rate`), plazo (`term`), carga de deuda (`dti`) y utilización de líneas revolventes (`revol_util`).

![SHAP Beeswarm](03_shap_beeswarm.png)

### 4. Optimización de Política de Corte (P&L)
Simulación de escenarios financieros considerando ingresos por intereses, comisión de apertura (2%), costo de fondeo (6%), severidad de pérdida LGD (70%) y costo operativo ($50 USD).

![Optimización Financiera](04_profit_optimization.png)

---
**Stack Tecnológico:** Python, Pandas, NumPy, Scikit-Learn, LightGBM, SHAP, Matplotlib/Seaborn.

