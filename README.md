## ▶️ Cómo reproducir el proyecto

1. Clonar el repositorio:
```bash
git clone https://github.com/francokohn/iol-byma-data-engineer-challenge.git
```
2. Antes de ejecutar los notebooks, es necesario contar con el dataset cargado en Databricks File System (DBFS).
El archivo CSV debe estar disponible en: "dbfs:/Volumes/workspace/default/iol_data/operaciones_byma_2026.csv"

3. Ejecutar notebooks en orden:
- 01_ingesta_bronze
- 02_tipo_cambio_enrichment
- 03_cotizaciones
- 04_cotizaciones_vs_operaciones
- 05_modelado_dimensional
- 06_eda_operaciones_byma
- 07_business_questions
  
4. Validar tablas generadas:
- 🥉 bronze_byma
- 🥈 silver_byma
- 🥇 gold_byma


---

## 🔌 Cobertura de APIs

Se utilizaron múltiples fuentes para maximizar cobertura:

- **yfinance**: fuente principal para cotizaciones históricas
- **data912**: fallback para instrumentos argentinos no disponibles en yfinance
- **API de tipo de cambio**: valores diarios USD/ARS

### Estrategia

- Prioridad: yfinance
- Fallback: data912
- Instrumentos sin datos → excluidos del análisis

### Trade-off

Se priorizó performance y consistencia sobre cobertura total. 

---

## 📊 Hallazgos del EDA

- Existe un grupo reducido de clientes hiper-activos que concentran gran parte del volumen operado.
- Algunos instrumentos presentan desvíos sistemáticos respecto al precio de mercado (>2%).
- El canal digital concentra mayor volumen en operaciones en USD.
