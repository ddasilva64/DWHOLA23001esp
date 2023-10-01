# Tablas de hechos

Es una representación de un proceso de negocio. En cuanto a diseño, permite guardar dos tipos de atributos diferenciados:

- Medidas del proceso/actividad/flujo de trabajo/evento que se pretende modelizar.
- **_FKs_** hacia registros en una **_tabla de dimensión_** (vista de negocio).

## Tabla de hechos de transacciones (Transaction Fact Table - TFT -)

Permiten analizar los datos con el máximo detalle. 

Por ejemplo, en una venta que tiene como resultado métricas como el importe de esta.

## Tablas de hechos sin medidas (Factless Fact Tables/Coverage Table - FFT/CT -)

Tablas que no tienen medidas pero tienen sentido, dado que representan el hecho de que el evento suceda. Frecuentemente se añaden contadores a estas tablas para  facilitar las consultas SQL. 

Por ejemplo, la asistencia a un acto benéfico en el que por cada persona que asiste tenemos un registro pero podríamos no tener ninguna métrica asociada más.

## Tablas de hechos periódicas (Periodic Snapshot Fact Table - PSFT -)

Son usadas para recoger información a intervalos de tiempo regulares. Dependiendo de la situación medida o de la necesidad de negocio, este tipo de **_tablas de hecho_** son una agregación de **_FFT/CT_** o están diseñadas específicamente. 

Por ejemplo, el Balance mensual, en el cual los datos recogen acumulados de forma mensual.

## Tablas de hecho agregadas (Accumulating Snapshot Fact Tables - ASFT -)

Tepresentan el ciclo de vida completo de una actividad o proceso, con un principio y un final. Se caracterizan por presentar múltiples dimensiones relacionadas
con los eventos presentes en un proceso. 

Por ejemplo, un proceso de matriculación de un estudiante y que recopila datos durante su periodo de vida que suelen sustituir los anteriores (superación y recopilación
de asignaturas, por ejemplo).
