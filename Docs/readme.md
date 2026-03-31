📘 README – Pipeline de Datos (Bronze, Silver, Gold y Calidad)
🧪 Prueba Técnica – Ingeniero de Datos
- Empresa: PUNTORED
- Objetivo: Construcción de un pipeline de datos con almacenamiento en capas (Bronze, Silver, Gold) y validaciones de calidad.
- Fecha creación: 29/03/2025
- Autor: Jorge Andrés Mosquera
- Versión: 1.0

📂 Organización de Notebooks
📦 Prueba_Tecnica_Jorge_Mosquera
 ┣ 📓 01_Ext_Cap_Bronze
 ┣ 📓 02_Trsf_Cap_Silver
 ┣ 📓 03_Trsf_Cap_Gold
 ┗ 📓 04_Calidad_Observabilidad


Cada notebook corresponde a una capa de la arquitectura Medallion y contiene las transformaciones y validaciones específicas.

🏛️ Arquitectura Medallion
- Bronze → Datos crudos, ingestados directamente desde la fuente.
- Silver → Datos limpios, normalizados y enriquecidos.
- Gold → Datos curados y métricas analíticas listas para negocio.
- Calidad → Validaciones de integridad y observabilidad continua.

🔹 01_Ext_Cap_Bronze – Datos Crudos
- Descripción: Almacena los datos tal como llegan (JSONL).
- Implementación:
- Creación de volúmenes UC (CREATE VOLUME ...).
- Lectura de archivo JSONL con Spark.
- Escritura en tabla UC: jorge_catalog.transacciones.bronze.
- Validación:
spark.table("jorge_catalog.transacciones.bronze").count()


🔸 02_Trsf_Cap_Silver – Datos Limpios y Normalizados
- Transformaciones:
- Eliminación de duplicados (dropDuplicates).
- Filtrado de montos positivos (amount_in_cents > 0).
- Normalización de estado (status en minúsculas).
- Filtrado de registros con created_at nulo.
- Tablas creadas:
- jorge_catalog.silver.users
- jorge_catalog.silver.transaction_details
- jorge_catalog.silver.transactions (con MERGE incremental).
- Validación: Conteo de registros en cada tabla Silver.

🟡 03_Trsf_Cap_Gold – Modelo Analítico
- Métricas:
- jorge_catalog.gold.user_metrics: total de transacciones, monto total, tasa de éxito.
- jorge_catalog.gold.response_metrics: total de transacciones por response_code.
- Ejemplo de consulta:
SELECT user_id, total_transactions, total_amount, success_rate
FROM jorge_catalog.gold.user_metrics
ORDER BY total_amount DESC;


✅ 04_Calidad_Observabilidad – Validaciones
- Validaciones implementadas:
- Conteo de registros en Silver.
- Chequeo de valores nulos por columna.
- Última fecha de actualización (max(created_at)).
- Reglas de negocio:
- amount >= 0
- status válido (approved, rejected, pending)
- transaction_id único
- Registro de resultados:
- Tabla UC: jorge_catalog.transacciones.gold_quality_logs
- Consultable en SQL:
SELECT * FROM jorge_catalog.transacciones.gold_quality_logs;


📊 Modelos Entidad-Relación (ERD)
- Silver Layer:
- Users → métodos de pago asociados.
- Transaction Details → detalles de cada transacción.
- Transactions → registro principal.
- Gold Layer:
- User Metrics → agregados por usuario.
- Response Metrics → agregados por código de respuesta.


📌 Consideraciones Generales
- Tablas gestionadas en Unity Catalog para trazabilidad y seguridad.
- Uso de Delta Lake para transacciones ACID y versionamiento.
- Validaciones de calidad recurrentes para observabilidad continua.
