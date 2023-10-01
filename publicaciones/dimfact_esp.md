# Modelado dimensional (dimensiones y hechos)

Para construir un **_dm_**, es necesario tener en cuenta un conjunto de técnicas y conceptos para diseñar nuestro **_DWH_**. Una parte fundamental de esto es la tipología de tablas donde almacenamos los datos.

- **_Tablas de hechos_**
    - Aquello que deseamos medir o analizar.
    - Son las tablas principales del **_dm_**.
    - Contienen claves foráneas (**_FK_**) para relacionarse con las **_tablas de dimensión_**.
    - Contienen métricas (también llamadas medidas), que representan lo que deseamos medir o analizar.
    - Generalmente son valores numéricos.
    - Evitan la redundancia de atributos en las **_tablas de dimensiones_**.
    - Normalmente tienen muchos (millones) de registros.
    - Ejemplos: Ventas, Compras, Movimientos de Contabilidad, Movimientos de Almacén, etc.

- **_Tablas de dimensión_**
    - Cómo deseamos medir lo que contienen las **_tablas de hechos_**.
    - Son tablas simples y normalizadas.
    - Contienen claves primarias (**_PK_**) para relacionarse con las **_tablas de hechos_**.
    - Los atributos son características de las **_tablas de hechos_**.
    - No hay límite en su número en el **_DWH_**.
    - Pueden contener una o varias relaciones jerárquicas.
    - Tienen menos registros que las **_tablas de hechos_**.
    - Ejemplos: Clientes, Productos, Almacenes, Proveedores, Calendario, etc.

<p><br></p>

## Tipos de Atributos

- **_Jerárquicos_**: Permiten pasar de lo general a lo particular, o consolidar y desagregar. Ejemplos: país, etc.
- **_Descriptivos_**: Información relevante que es puramente descriptiva. Ejemplos: dirección, teléfono, tamaño, etc.
- **_Control_**: Datos de auditoría que no pertenecen al conocimiento del negocio. Ejemplos: fecha de registro de los datos, etc.

<p><br></p>

## Hechos (fact tables)

Los hechos son una representación de un proceso de negocios. En cuanto a diseño, permiten almacenar dos tipos de atributos diferenciados:
- Métricas del proceso/actividad/flujo de trabajo/evento que se pretende modelizar.
- **_FKs_** (claves foráneas) que apuntan a registros en una **_tabla de dimensión_** (vista de negocios).

### Hechos transaccionales (Transactional Fact Tables - TFT)

Permiten analizar los datos con el máximo detalle.
Por ejemplo, en una Venta, las métricas pueden incluir el importe de la misma.

### Hechos sin métricas (Factless Fact Tables/Coverage Table - FFT/CT)

Son tablas que no contienen métricas, pero tienen sentido, ya que representan la ocurrencia de un evento. A menudo, se añaden contadores a estas tablas para facilitar las consultas SQL.
Por ejemplo, en un evento benéfico, cada asistente puede generar un registro, aunque no haya ninguna métrica asociada.

### Hechos periódicos (Periodical Snapshot Fact Tables - PSFT)

Se utilizan para recopilar información en intervalos de tiempo regulares. Dependiendo de la situación medida o de las necesidades del negocio, este tipo de **_tablas de hechos_** pueden ser una agregación de **_FFT/CT_** o estar diseñadas específicamente.
Por ejemplo, un balance mensual en el que los datos se recopilan de forma acumulativa mensualmente.

### Hechos de acumulación (Accumulating Snapshot Fact Tables - ASFT)

Representan el ciclo de vida completo de una actividad o proceso, con un inicio y un final. Se caracterizan por incluir múltiples dimensiones relacionadas con los eventos presentes en un proceso.
Por ejemplo, un proceso de matriculación de estudiantes que recopila datos durante todo el período de vida, reemplazando a los datos anteriores (como la superación y la recopilación de asignaturas).

### Hechos X (casos de la vida real no descritos en la literatura)

Son **_tablas de hechos_** que tenemos que construir en la vida real, para favorecer su usabilidad a los usuarios que las alimentan. Además, necesitamos transformarlas para que se conviertan en algo manejable en nuestro **_dm_**.

#### Tablas de hechos comprimidos

Imaginemos el siguiente caso:

Tenemos una tabla en una hoja de cálculo Excel en nuestro servidor, el cual debe ser mantenido por un Directivo de Ventas de la empresa. Tras un proceso **_ETL_**, integraremos esa tabla con las tablas de la BD **_OLTP_** en nuestro **_DWH_**.

En esa tabla se anotarà la siguiente:
- ID vendedor (debe ser el mismo que tenemos en la BD **_OLTP_**).
- Nombre del vendedor (lo ponemos para favorecer la usabilidad de la tabla). 
- Fecha inicio (período).
- Fecha final (período).
- Incentivos dinerários diários no especificados (incentivos en dinero para el vendedor individual, no contempladas en la BD **_OLTP_**).
- Incentivos en esépcie diários no especificados (incentivos en esépcie para el vendedor individual, no contempladas en la BD **_OLTP_**).

Para poder implementar esta **_tabla de hechos_** en nuestro **_dm_**, deberemos tener la misma granularidad entre las **_FKs_** de nuestra tabla y las dimensiones asociadas. La granularidad en nuestro **_dm_** de la **_tabla de dimensión_** de Tiempo es **día**, con lo cual, debemos transformar nuestra **_tabla de hechos_** para que sus registros sean diários (no períodos).

Observemos que si transformamos los períodos en días (manteniendo los datos de las demás columnas), no perdemos información. A este proceso le vamos a llamar **_descompresión de la tabla de hechos_**.

La **_tabla de hechos_** (transformada), en nuestro **_DWH_**, quedaría como sigue:
- ID vendedor.
- Nombre del vendedor. 
- Fecha.
- Incentivos dinerários diários no especificados.
- Incentivos en esépcie diários no especificados.

#### Tablas de hechos normalizadas

Imaginemos el mismo caso, pero con otro enfoque: No tenemos **_DWH_**.

En la tabla el Directivo anotarà lo mismo, que en el caso anterior (nuestra solución será transparente para él):
- ID vendedor (debe ser el mismo que tenemos en nuestra BD OLTP).
- Nombre del vendedor (lo ponemos para favorecer la usabilidad de la tabla). 
- Fecha inicio (período).
- Fecha final (período).
- Incentivos dinerários diários no especificados (incentivos en dinero para el vendedor individual, no contempladas en la BD OLTP).
- Incentivos en esépcie diários no especificados (incentivos en esépcie para el vendedor individual, no contempladas en la BD OLTP).

Para poder implementar esta **_tabla de hechos_** en nuestro **_dm_**, pero ahora sin tener en cuenta el nivel de granularidad de la **_tabla de dimensión_** de Tiempo, debido a que no podemos implementar un **_DWH_**, deberemos transformar la filas que repitan el ID vendedor en grupos de repetición (no normalizados).

Observemos que si realizamos esta transformación (evitable si tenemos un **_DWH_**), deberemos, para un mismo ID Vendedor, comparar contínuamente el período con la fecha para saber si se aplican los hechos. Es decir, no se puede relacionar la **_tabla de hechos_** con la **_tabla de dimensión_** de Tiempo, pero sí con la dimensión de Vendedores.

La **_tabla de hechos_** (transformada), en nuestro **_DWH_**, quedaría como sigue:
- ID vendedor.
- Nombre del vendedor. 
- Período 1
- Fecha inicio (período).
- Fecha final (período).
- Incentivos dinerários diários no especificados.
- Incentivos en esépcie diários no especificados.
...
- Período n
- Fecha inicio (período).
- Fecha final (período).
- Incentivos dinerários diários no especificados.
- Incentivos en esépcie diários no especificados.

**_Muy importante!_**: **Como Analistas/Ingenieros de Datos debemos exigir la implementación de un DWH (siempre que sea necesario)**. 

<p><br></p>

## Dimensiones (tablas de dimensión)

Las dimensiones recopilan los puntos de análisis de un hecho, es decir, son las preguntas que hacemos para conocer los hechos.

Por ejemplo, una venta se puede analizar en relación al día de la venta, el producto, el cliente, el vendedor, el canal de venta, etc.

Clasificación de las **_tablas de dimensión_**:

- **_Dimensión de Tiempo_** (especial)
- **_Clasificación histórica_**
	- Dimensiones que cambian lentamente (**_Slowly Changing Dimensions - SCD -_**)
		- **_Tipo 0_**: Preserva el original.
		- **_Tipo 1_**: Se sobrescribe el atributo actualizado, es decir, no se guardan datos históricos.
		- **_Tipo 2_**: Agrega un nuevo registro con el cambio (fila).
		- **_Tipo 3_**: Agrega un nuevo atributo (columna) "anterior".
		- **_Tipo 4_**: Histórico separado (*mini-dimensión*).
		- **_Tipo 5_**: SCD-1 + SCD-4 = SCD-5.
		- **_Tipo 6_**: SCD-1 + SCD-2 + SCD-3 = SCD-6.
		- **_Tipo 7_**: Agrega una fila de dimensión **_SCD-2_** con un valor de atributo nuevo, además de la visualización limitada a las filas y/o los valores de atributo actuales.
	- Dimensiones que cambian rápidamente (**_Rapidly Changing Dimensions - RCD -_**)
		- Dimensiones monstruo (**_Monster Dimensions - MONSDIM_** -)
		- Dimensiones puente (**_Bridge Dimension - BRIDDIM-_**)
- **_Clasificación funcional_**
	- Dimensiones reducidas (**_Shrunken Dimensions - SHRKDIM -_**)
	- Dimensiones conformadas (**_Conformed Dimensions - CONFDIM -_**)
	- Dimensiones basura (**_Junk Dimensions - JUNKDIM -_**)
	- Dimensiones de juego de rol (**_Role-playing dimensions - RPLYDIM -_**)
	- Dimensiones degeneradas (**_Degenerate Dimensions - DEGEDIM -_**)
	- Dimensiones estabilizadoras (**_Outrigger Dimensions - OUTGDIM -_**)
	- Dimensiones estáticas (**_Static Dimensions - STATDIM -_**)
	- Dimensiones apiladas (**_Stacked Dimensions - STACDIM -_**)
	- Dimensión diferida (**_Deferred Dimension - DEFEDIM -_**)
	- Dimensión distorsionada (**_Distorted Dimension - DISTDIM -_**)

<p><br></p>

### Dimensión Tiempo (Calendar DateTime Dimension - CDTD -)

En cualquier **_DWH_**, se pueden encontrar varios cubos con sus **_tablas de hechos_** que contienen registros con variables de interés para el negocio y que deben ser estudiadas. Además, como se mencionó anteriormente, cada **_tabla de hechos_** estará rodeada de varias **_tablas de dimensión_**, que contienen los parámetros que nos permitirán analizar los hechos que queremos estudiar. Un parámetro que con toda probabilidad estará presente en todos los cubos es el Tiempo, ya que es habitual almacenar los hechos a medida que ocurren a lo largo del tiempo, obteniendo así una serie temporal de la variable que se va a estudiar.

Dado que el Tiempo es una dimensión presente prácticamente en cualquier cubo de un sistema **_OLAP_**, merece una atención especial. Al diseñar la dimensión Tiempo (ya sea para un **_esquema en estrella_** o un **_esquema copo de nieve_**), es necesario prestar especial atención, ya que se puede hacer de varias maneras y no todas son igualmente eficientes. El diseño más común para esta tabla es utilizar la fecha o fecha/hora como **_PK_** de la tabla (tabla de tiempo 1). Este diseño no es el más recomendable, ya que en la mayoría de los sistemas de gestión de bases de datos es más costoso realizar búsquedas en campos de tipo "date" o "datetime". Estos costos se reducen si la clave principal es de tipo entero. Además, un dato entero siempre ocupa menos espacio que un dato de tipo fecha (la clave se repetirá en millones de registros en la **_tabla de hechos_**, lo que puede ocupar mucho espacio). Por lo tanto, el diseño de la tabla de tiempo se mejorará si se utiliza un campo "TiempoID" de tipo entero como clave principal (tabla de tiempo 2).

<p><br></p>

*Tabla de tiempo (1)*

| Fecha (PK)  | datetime |
| :---------  | :------: |
| Año         | char(4)  |
| Trimestre   | char(6)  |
| Mes         | char(10) |

<p><br></p>

*Tabla de tiempo (2)*

| TiempoID (PK) | integer  |
| :-----------  | :------: |
| Fecha         | datetime |
| Año           | char(4)  |
| Trimestre     | char(6)  |
| Mes           | char(10) |

<p><br></p>

Al llenar la tabla de Tiempo, si se ha optado por un campo de tipo entero como clave, hay dos opciones. La más inmediata consiste en asignar valores numéricos consecutivos (1, 2, 3, 4...) a diferentes valores de las fechas. La otra opción sería asignar valores numéricos del tipo "yyyymmdd", es decir, que los cuatro primeros dígitos del valor del campo indiquen el año de la fecha, los dos siguientes el mes y los dos últimos el día. Este segundo método tiene ciertas ventajas sobre el primero, ya que de esta manera, el propio valor numérico proporciona por sí solo la información sobre la fecha. Por ejemplo, si en la **_tabla de hechos_** se encuentra el valor 20040723, sabremos que se refiere al 23 de julio de 2004. En cambio, con el primer método, podríamos encontrar valores como 8456456, y para saber a qué fecha se refiere ese valor, sería necesario consultar la tabla de Tiempo.

Además del campo clave TiempoID, la **_tabla de hechos_** también debe contener otros campos que también son importantes. Estos campos serían:
- Un campo "año": que contendría valores como '2002', '2003', '2004'...
- Un campo "mes": aquí se pueden poner valores como 'enero', 'febrero'... (o de forma abreviada: 'Ene', 'Feb'...). Aunque esto no es incorrecto, se puede mejorar si el nombre del mes va acompañado del año al que pertenece. Por ejemplo: '2004 enero', '2004 febrero'... De esta manera, se optimiza la búsqueda de los valores de un mes específico, ya que si se buscan los valores correspondientes al mes de "enero de 2003", toda esta información está contenida en un solo campo, el "mes", y no sería necesario consultar también el campo de año.
- Un campo "mesID": este campo debería ser de tipo entero y se usaría para almacenar valores como 200601 (para '2006 enero') o 200602 (para '2006 febrero'). De esta manera, es posible realizar agregaciones y agrupaciones por meses.

De manera análoga a lo hecho con el campo mes, se podrían agregar más campos como **"Trimestre", "Quincena", "Semana"** de tipo texto para poder visualizarlos, así como sus equivalentes de tipo entero "TrimestreID", "QuincenaID", "SemanaID" para realizar agrupaciones y agregaciones si es necesario. En general, se puede agregar un campo para cada nivel de granularidad deseado.

Otro campo especial que se puede agregar es el **"Día de la semana"** ('lunes', 'martes'...). Este campo se suele agregar para poder realizar estudios sobre el comportamiento de los días de la semana en general (no del primer lunes del mes de enero de un año específico, este tipo de estudio a menudo carece de interés). Por esta razón, este campo no necesita ir acompañado del mes o del año como los campos anteriores. También se puede agregar su campo dual "ID" de tipo entero para realizar agrupaciones y oRPLYDIMenaciones si es necesario.

Con las adiciones descritas, podríamos tener una tabla de Tiempo como la que se muestra en la figura (tabla de tiempo 3). Esta sería válida para un diseño en **_estrella_**. Para un diseño en **_copo de nieve_**, tendríamos que desglosar la tabla de Tiempo en tantas tablas como niveles jerárquicos contenga. Observe que los campos de tipo "ID" son todos de tipo entero, ya que la mayoría de las operaciones se realizarán en estos campos y serán más eficientes en datos enteros.

<p><br></p>

*Tabla de tiempo (3)*

| TiempoID (PK) | integer  |
| :------------ | :------: |
| Fecha         | datetime |
| Año           | char(4)  |
| Trimestre     | char(6)  |
| TrimestreID   | int      |
| Mes           | char(10) |
| MesID         | int      |
| Quincena      | char(10) |
| QuincenaID    | int      |
| Semana        | char(10) |
| SemanaID      | int      |
| Día           | char(10) |
| DíaID         | int      |
| DíaSemana     | char(10) |
| DíaSemanaID   | int      |

**_Nota importante!_**: Algunas herramientas BI, como **_Power BI_**, incorporan sistemas automáticos de generación de esta dimensión, pero realizar un estudio previo evitará problemas y inconsistencias posteriores (que a menudo son difíciles de resolver). Además, si realizamos otros tipos de análisis o visualizaciones, por ejemplo, en **_Python_** (fuera de **_Power BI_**), entonces necesitaremos diseñar la dimensión Tiempo adecuadamente por nosotros mismos.

<p><br></p>

### Dimensiones reducidas (Shrunken Dimensions - SHRKDIM -)

Las **_SHRKDIMs_** son útiles cuando necesitamos mostrar la agregación de los datos que tenemos en un **_DWH_**. Es decir, necesitamos mostrar una granularidad más alta que la que tenemos en el **_DWH_**.

Un ejemplo clásico es cuando necesitamos mostrar datos mensuales, aunque tengamos datos diarios almacenados en el **_DWH_**.

Otro ejemplo, menos intuitivo, sería considerar las Ventas por Ciudad. Entonces crearíamos una **_SHRKDIM_** de Ciudades que se relacionaría con la **_tabla de hechos_** (Ventas).

En otras palabras, las **_SHRKDIMs_** crean lo que podríamos asimilar a tablas de búsqueda (en un modelo de BD **_OLTP_**). En el **_DDM_**, siempre debemos pensar en las preguntas que debemos hacer a la **_tabla de hechos_** para poder satisfacer las necesidades del negocio (esto debe ser nuestra lógica). Si no podemos responder estas preguntas con las **_tablas de dimensiones_** creadas, es posible que creemos dimensiones con muy pocas columnas (**reducidas**) para responderlas, y estas serán nuestras **_SHRKDIMs_**.

**_Nota importante_**: Algunas herramientas de **_BI_**, como **_Power BI_**, tienen la capacidad de crear segmentaciones de **_tablas de hechos_** sin extraer **_SHRKDIMs_** de la misma. Esto puede resultar en un **_DDM_** incompleto en el **_DWH_**, especialmente si decidimos analizar los datos en otro entorno, como **_Python_** (fuera de **_Power BI_**).

#### Ejemplo de Uso de Dimensiones Reducidas (SHRKDIM)

Supongamos que tenemos un **_DWH_** que almacena datos de Ventas para una cadena de tiendas. En este **_DWH_**, tenemos una **_tabla de hechos_** principal llamada "Ventas" que almacena información detallada sobre cada venta individual, como la fecha de la venta, el producto vendido, la cantidad, el precio, etc.

Sin embargo, también tenemos una necesidad de realizar análisis a nivel mensual y a nivel de ciudad. Esto significa que necesitamos mostrar datos agregados de Ventas por Mes y por Ciudad, a pesar de que los datos detallados se almacenan a nivel diario en la **_tabla de hechos_** "Ventas".

En este escenario, podríamos crear dos dimensiones reducidas (**_SHRKDIM_**):

1. **Dimensión de Mes (SHRKDIM_Mes)**: Esta dimensión contendría información sobre los meses, como el nombre del mes, el número del mes y cualquier otra información relevante. Sería una tabla reducida en comparación con la **_tabla de hechos_** de "Ventas" y se relacionaría con ella a través de la fecha de la venta. Esto nos permitiría realizar análisis y consultas a nivel mensual.

2. **Dimensión de Ciudad (SHRKDIM_Ciudad)**: Esta dimensión contendría información sobre las ciudades en las que operan las tiendas, como el nombre de la ciudad, el código postal y otros detalles. Al igual que la dimensión de Mes, sería una tabla reducida en comparación con la **_tabla de hechos_** de "Ventas" y se relacionaría con ella a través de la ubicación de la tienda. Esto nos permitiría realizar análisis y consultas a nivel de ciudad.

Con estas dimensiones reducidas en su lugar, podríamos responder preguntas como:

- **"¿Cuál fue la cantidad total de ventas en el mes de julio de 2023 en todas las ciudades?"**
- **"¿Cuál fue el promedio de ventas diarias en la ciudad de Bogotá en el último trimestre?"**

Estas preguntas implican agregaciones a nivel de Mes y Ciudad, y las dimensiones reducidas nos permiten realizar estas consultas de manera eficiente y coherente, incluso si los datos detallados se almacenan a nivel diario en la **_tabla de hechos_** "Ventas".

<p><br></p>

### Dimensiones conformadas (Conformed Dimensions - CONFDIM -)

Las Dimensiones Conformadas permiten compartir información entre dimensiones, lo que facilita la realización de consultas conjuntas.

En un **_DWH_**, una **_CONFDIM_** es una dimensión que tiene el mismo significado en cada hecho con el que se relaciona. Las **_CONFDIMs_** permiten categorizar y describir hechos y medidas de la misma manera a través de múltiples **_tablas de hechos_** o Data Marts (**_DM_**), garantizando la coherencia de los informes en toda la empresa.

Se puede hacer referencia a una **_CONFDIM_** mediante múltiples **_tablas de hechos_** en un DWH. La dimensión se implementa como una tabla de dimensión única a la que hacen referencia **_varias tablas de hechos_**, o como múltiples **_tablas de dimensiones_** a las que hacen referencia **_tablas de hechos_** en **_DMs_** diferentes dentro del mismo **_DWH_**. También es posible que varias **_tablas de hechos_** dentro de un solo **_DM_** hagan referencia a la misma tabla de **_CONFDIM_**, así como una **_tabla de hechos_** en otros **_DM_**.

El ejemplo más clásico de una **_CONFDIM_** es la tabla de Tiempo, ya que podemos consultar las Ventas mensuales, los Planes de productividad de los empleados para el próximo mes o las Entradas al almacén el día 15 del mes pasado. En todos los casos, incluso si se trata de otro **_DM_** (departamento), son preguntas que realizamos desde nuestra tabla de Tiempo (**_tabla de dimensión_**) a diferentes **_tablas de hechos_** (en uno o más **_DMs_**, en caso de que estén contemplados en nuestro **_DWH_**).

**_¡Atención!_**: Si no hemos planteado las preguntas de negocio adecuadas, podríamos pasar por alto dimensiones que, si son lo suficientemente generalizadas en nuestro **_DWH_**, podrían considerarse **_CONFDIMs_** no contempladas.

#### Ejemplo de uso de Dimensiones Conformadas (CONFDIM) con Data Marts

Imaginemos que tenemos un Data Warehouse (**_DWH_**) grande que almacena datos para una empresa minorista. Dentro de este **_DWH_**, existen múltiples Data Marts (**_DMs_**) para diferentes departamentos, como Ventas, Marketing y Gestión de Inventarios. Cada **_DM_** se enfoca en aspectos comerciales específicos y tiene sus propias **_tablas de hechos_**.

Consideremos el **_DM_** de Ventas, que contiene información detallada de ventas. Una de las **_tablas de hechos_** principales en este **_DM_** es "Transacciones de Ventas", que registra los datos de cada venta individual, incluyendo el producto vendido, la información del cliente, la fecha y el importe.

Ahora, el **_DM_** de Marketing está interesado en analizar los datos de ventas para comprender el comportamiento y las tendencias de los clientes. Quieren saber, por ejemplo, qué productos suelen comprarse juntos por los clientes. Para hacerlo, necesitan compartir datos de dimensiones, como Producto y Cliente, con el **_DM_** de Ventas.

Así es como se utilizan las **_Dimensiones Conformadas (CONFDIM)_** en este escenario:

1. **Dimensión de Producto (CONFDIM_Producto)**: Esta dimensión contiene información sobre los Productos, como el nombre del producto, la categoría y el fabricante. Se mantiene de manera consistente tanto en el **_DM_** de Ventas como en el **_DM_** de Marketing. El **_DM_** de Ventas relaciona la **_tabla de hechos_** "Transacciones de Ventas" con esta dimensión, mientras que el **_DM_** de Marketing utiliza la misma dimensión para analizar datos relacionados con Productos.

2. **Dimensión de Cliente (CONFDIM_Cliente)**: Esta dimensión incluye información sobre el Cliente, como el nombre, la dirección y los detalles de contacto. Al igual que la Dimensión de Producto, se comparte entre los **_DMs_** de Ventas y Marketing. El **_DM_** de Ventas vincula la **_tabla de hechos_** "Transacciones de Ventas" con esta dimensión, mientras que el **_DM_** de Marketing la utiliza para comprender las preferencias y el comportamiento de los Clientes.

Con las **_Dimensiones Conformadas (CONFDIM)_** en su lugar, el **_DM_** de Marketing puede realizar análisis como identificar la afinidad entre Productos y segmentar a los Clientes de manera efectiva, ya que pueden confiar en datos de dimensiones consistentes compartidos con el **_DM_** de Ventas.

Consulta de ejemplo en el **_DM_** de Marketing:

- **"¿Qué Productos se compran con frecuencia juntos, y qué segmentos de Clientes muestran este comportamiento?"**

Al aprovechar las **_Dimensiones Conformadas (CONFDIM)_**, estos **_DMs_** aseguran la consistencia y la integridad de los datos al compartir información de dimensiones, lo que permite a diferentes departamentos dentro de la organización realizar análisis interfuncionales y obtener conocimientos valiosos.

<p><br></p>

### Dimensiones basura (Junk Dimensions - JUNKDIM -)

Contienen información volátil que se utiliza puntualmente y que no suele guardarse de manera permanente en el **_DWH_**.

- Reducen el número de dimensiones (columnas de baja cardinalidad) en el modelo dimensional y disminuyen la cantidad de columnas en la **_tabla de hechos_**. Es una colección de códigos transaccionales aleatorios, flags o atributos de texto.

- Pueden optimizar el espacio, ya que las **_tablas de hechos_** *no deberían incluir campos de texto ni de baja cardinalidad* (**práctica muy cuestionable**). Incluyen principalmente medidas, claves externas y claves de dimensión degeneradas (**_DEGEDIMs_**).

<p><br></p>

Ejemplo con una **_tabla de hechos_** de Coches y dos **_tablas de dimensiones_** de Colores y de Modelo.

![Sin JUNKDIM](https://i.imgur.com/hYcP0rU.png)  
_Sin JUNKDIM_

<p><br></p>

Ejemplo con una **_tabla de hechos_** de Coches y una **_tabla de dimensión_** de Características del coche.

![Con JUNKDIM](https://i.imgur.com/dw1nykb.png)  
_Con JUNKDIM_

<p><br></p>

**_Nota_**: Es al menos una práctica cuestionable, dado que nos interesa desnormalizar las dimensiones y dotar de significado las métricas de las **_tablas de hechos_**. En el caso expuesto, la práctica más recomendada sería considerar los Coches como una **_tabla de dimensión_** de una **_tabla de hechos_** de Ventas y listo.

#### Ejemplo de uso de dimensiones basura (Junk Dimensions - JUNKDIM)

Supongamos que tenemos un Data Warehouse (**_DWH_**) que almacena datos de Ventas de productos. En este **_DWH_**, tenemos una **_tabla de hechos_** principal llamada "Ventas" que almacena información detallada sobre cada transacción, como fecha, producto vendido, cliente, cantidad, precio, entre otros.

Ahora, necesitamos realizar análisis específicos que involucran la combinación de ciertos atributos de baja cardinalidad, como los colores de los productos y las características del producto. Estos atributos no justifican una dimensión independiente debido a su baja cardinalidad y naturaleza volátil.

En lugar de crear dimensiones separadas para los colores y las características del producto, podríamos optar por una **_dimensión basura (JUNKDIM)_** llamada "Atributos de Producto". Esta dimensión contendría una colección de códigos aleatorios o flags que representan combinaciones de colores y características.

Así es como se vería la estructura de datos:

- **_Tabla de hechos_** "Ventas": Se relaciona con la **_JUNKDIM_** "Atributos de Producto" a través de una **_FK_**.

- **_JUNKDIM_** "Atributos de Producto": Contiene combinaciones de colores y características representadas por códigos o flags.

Con esta estructura, podríamos responder preguntas como:

- **"¿Cuál es la cantidad total de Productos vendidos que tienen las características 'Rojo' y 'Resistente al agua'?"**
- **"¿Cuál es el precio promedio de los Productos de color 'Azul'?"**

Las **_JUNKDIMs_** nos permiten simplificar el **_dm_** y optimizar el espacio, ya que no tendríamos que crear dimensiones separadas para cada combinación posible de colores y características del producto.

<p><br></p>

### Dimensiones de juego de rol (Role-playing dimensions - RPLYDIM -)

Las **_RPLYDIMs_** tienen asignado un significado. Es decir, una **_RPLYDIM_** es una dimensión que hace referencia a múltiples propósitos dentro de una **_tabla de hechos_**.

El mejor ejemplo de una **_RPLYDIM_** es nuevamente una **_tabla de dimensión_** de Tiempo, ya que el mismo atributo de fecha en la dimensión se puede utilizar para diferentes propósitos en la misma **_tabla de hechos_**. Por ejemplo, en la **_tabla de hechos_** de Ventas pueden registrarse la fecha de pedido, de entrega, de transacción, de cancelación, de entrega, etc. Todos estos datos se relacionan con la fecha de la **_tabla de dimensión_** de Tiempo.

Dificultades en el tratamiento:

1. Pueden aumentar la complejidad y el tamaño de la **_tabla de hechos_**, ya que cada **_RPLYDIM_** añade más columnas a la **_tabla de hechos_**.

2. Pueden crear confusión e inconsistencia entre usuarios y analistas, ya que diferentes roles pueden tener diferentes significados o interpretaciones para un mismo atributo de dimensión. Es decir, si solo decimos fecha (por ejemplo), puede tener muchas interpretaciones, dependiendo del rol.

3. Pueden ser difíciles de mantener y actualizar, ya que cualquier cambio en la fuente de la **_tabla de dimensiones_** puede afectar a múltiples **_RPLYDIMs_** y **_tablas de hechos_**. Es imprescindible, por lo tanto, tenerlas muy claras en todo el **_pipeline_**.

4. Algunas herramientas de **_BI_**, como **_Power BI_**, solo permiten tener activa una relación con la misma clave principal (**_PK_**) de la **_RPLYDIM_**, aunque sean claves foráneas (**_FKs_**) diferentes en la **_tabla de hechos_**. Esto es simplemente un tema de implementación en la herramienta (en **Power BI**, con **_DAX_**), y se resuelve correctamente en la herramienta.

Esta sería una buena solución de **_RPLYDIMs_** en **Power BI** con **_DAX_**:

````DAX
[Ventas totales por fecha de pedido] :=   
    CALCULATE( 
        SUM(Pedidos[Total de línea]),
        USERELATIONSHIP(Pedidos[Fecha de pedido], Fechas[Fecha])
    )

[Ventas totales entregadas] := 
    CALCULATE( 
        SUM(Pedidos[Total de línea]),
        USERELATIONSHIP(Pedidos[Fecha de entrega], Fechas[Fecha])
    )
````

etc.

Otras soluciones pueden complicarnos la vida al crear diferentes vistas (en nuestro **_DWH_**) de la **_tabla de dimensión_** de Tiempo para cada una de las **_FKs_** de la **_tabla de hechos_**, es decir, para cada una de las **_RPLYDIMs_** definidas. Esto complica extraordinariamente las relaciones y las segmentaciones en la explotación del **_DDM_** en el **_BI_** (por ejemplo).

**_¡Atención!_**: Es imprescindible definir bien las **_RPLYDIMs_** para que funcione correctamente nuestro pipeline. Esto implica que, como **Ingenieros de Datos, debemos conocer**, en el **_pipeline_** de nuestro proyecto, **desde el negocio del cliente hasta la solución final** en la que mostraremos el resultado.

#### Ejemplo de uso de dimensiones de juego de rol (Role-playing dimensions - RPLYDIM -)

Supongamos que tenemos un Data Warehouse (**_DWH_**) que almacena datos de Ventas de productos. En este **_DWH_**, tenemos una **_tabla de hechos_** principal llamada "Ventas" que almacena información detallada sobre cada transacción, como fecha de pedido, fecha de envío, fecha de entrega, producto vendido, cliente, cantidad, precio, entre otros.

Además, queremos implementar la solución en **_Power BI_**.

Ahora, necesitamos realizar análisis específicos que involucran los atributos de tiempo. Cada uno de estos atributos tienen una relación con la dimensión de Tiempo.

En lugar de crear dimensiones separadas para las fechas, podríamos optar por una **_dimensión de juego de rol (RPLYDIM)_** y además no necesitaríamos una tabla distinta de la **_tabla de dimensión_** de Tiempo. 

Dificultades en el tratamiento:

1. En **_Power BI_** no podemos mantener más de una relación con el mismo campo, con lo cual necesitamos implementar la relación selectívamente en DAX, según los interese.

2. En otros sistemas, la implementación puede implicar crear múltiples **_tablas de dimensión_** de Tiempo, para cada atributo de la **_tabla de hechos_** con la que queramos establecer la relación.

En nuestro caso, dependiendo de la métrica que queramos obtener usaríamos códigos como los siguientes:

````DAX
[Ventas totales por fecha de pedido] :=   
    CALCULATE( 
        SUM(Pedidos[Total de línea]),
        USERELATIONSHIP(Pedidos[Fecha de pedido], Fechas[Fecha])
    )

[Ventas totales por fecha de envío] := 
    CALCULATE( 
        SUM(Pedidos[Total de línea]),
        USERELATIONSHIP(Pedidos[Fecha de envío], Fechas[Fecha])
    )

[Ventas totales por fecha de entrega] := 
    CALCULATE( 
        SUM(Pedidos[Total de línea]),
        USERELATIONSHIP(Pedidos[Fecha de entrega], Fechas[Fecha])
    )
````

Así es como se vería la estructura de datos:

- **_Tabla de hechos_** "Ventas": Se relaciona con la **_RPLYDIM_** "Fechas" a través de las 3 **_FKs_**.

- **_RPLYDIM_** "Atributos de Tiempo": En nuestro caso es la **_tabla de dimensión_** de Tiempo.

Con esta estructura, podríamos responder preguntas como:

- **"¿Cuál es la cantidad total de Productos vendidos que se enviaron el mes de marzo?"**
- **"¿En qué ciudades se hiciero más entregas en agosto?"**

Las **_JUNKDIMs_** nos permiten simplificar el **_dm_** y optimizar el espacio, ya que no tendríamos que crear dimensiones separadas para cada atributo relacionado (**_FK_**).



