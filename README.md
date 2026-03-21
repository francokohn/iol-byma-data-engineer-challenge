# iol-byma-data-engineer-challenge

El proyecto fue desarrollado utilizando Databricks Community Edition integrado con GitHub para versionado y reproducibilidad del pipeline.


## 🏗️ Arquitectura

CSV (Unity Catalog Volume)
   ↓
Bronze (Delta Lake)
   ↓
Silver (clean + enrichment)
   ↓
Gold (modelo dimensional)
   ↓
Analytics / BI / APIs

## 🥉 Bronze Layer - Ingesta de datos

La capa Bronze implementa la ingesta batch del dataset de operaciones BYMA desde un archivo CSV.

### 📥 Origen de datos
El dataset se carga desde un volumen gestionado en Unity Catalog debido a restricciones del entorno Databricks Community Edition, que impiden el acceso directo al filesystem del workspace.

### ⚙️ Procesamiento
Se aplicaron las siguientes transformaciones:
- Definición explícita de schema para evitar inferencias incorrectas
- Conversión del timestamp desde UTC a zona horaria local (America/Argentina/Buenos_Aires)
- Generación de columna `fecha_particion` a partir de la fecha local
- Agregado de columna `_ingested_at` con timestamp de carga

### 🔁 Ingesta incremental e idempotencia

La ingesta se implementa simulando un proceso batch diario, procesando los datos por `fecha_particion` como si cada día llegara un archivo incremental.

Se utiliza Delta Lake con operaciones `MERGE` sobre `id_transaccion`, lo que garantiza idempotencia: re-ejecutar el pipeline no genera duplicados en la tabla bronze.

### 🧪 Calidad de datos
Se implementó un control de duplicados basado en `id_transaccion`.

- Se genera la tabla `bronze_byma.calidad_duplicados` con registros duplicados detectados
- En el dataset provisto no se identificaron duplicados, pero la lógica se mantiene para robustez futura

Para garantizar consistencia en la tabla principal, se conserva un único registro por `id_transaccion`, mientras que los duplicados se registran en la tabla de calidad.

### 💾 Persistencia
- Los datos se almacenan en formato Delta Lake en la tabla: bronze_byma.operaciones_raw
- Particionada por: fecha_particion

Esto permite mejorar la performance en consultas y simular ingestas batch diarias.



## ⚙️ Tecnologías utilizadas

- Databricks Community Edition
- PySpark
- Delta Lake
- Unity Catalog Volumes
