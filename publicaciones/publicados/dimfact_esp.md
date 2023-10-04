# Modelado dimensional (dimensiones y hechos) - esp -

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

### Dimensiones degeneradas (Degenerate Dimensions - DEGEDIM -)

Se encuentran como atributos en la **_tabla de hechos_**, tienen significado desde el punto de vista del análisis. Contienen información de baja cardinalidad formada por relaciones dicotómicas. Frecuentemente, solo contienen un atributo y, por lo tanto, no se suele crear una tabla aparte.

Es decir, son atributos que no pueden ser dimensiones, ni hechos (métricas), pero requieren análisis. Todos estos atributos, si se eliminan de la tabla de hechos, se trasladan a **_DEGEDIMs_**.

Por ejemplo, se pueden considerar el número de pedido, el número de factura, el género de un paciente, etc., como atributos de **_DEGEDIMs_**.

Una factura puede tener un atributo de nombre de cliente, pero ya forma parte de la **tabla de dimensión** Cliente.

Con lo cual, **_DEGEDIM_** es una parte de la **tabla de hechos** que no es una métricas, pero que sigue siendo una dimensión, lo que es contradictorio.

Se puede definir como **_DEGEDIM_** a un atributo de una **_tabla de hechos_** que actúa como dimensión, pero que realmente no se une con ninguna dimensión (no es **_FK_** de otra tabla), ya que sus atributos ya han sido incluidos en otras dimensiones de análisis. 

**¡Aviso!**: Definir **_DEGEDIMs_** fuera de una **tabla de hechos** es un error.

#### Ejemplo de dimensiones degeneradas (Degenerate Dimensions - DEGEDIM -)

Supongamos que tenemos una BD de Ventas que incluye una **tabla de hechos**, que registra las Ventas de productos. En esta **_tabla de hechos_**, tenemos la siguiente información:

- Número de pedido
- Fecha
- Producto
- Cantidad
- Precio
- Venta online

Ahora, imaginemos que las Ventas pueden ser online o no serlo, por lo tanto, este atributo es de baja cardinalidad, no es agregable y tampoco es **_FK_**. Por otra parte, el número de pedido de la Venta tiene baja cardinalidad en relación a las Ventas (una venta tiene más de un pedido), además no es agregable, ni es **_FK_**.

Por lo tanto, en la **_tabla de hechos_** (Ventas), número de pedido y venta online son **_DEGEDIMs_** y solo nos servirán para tener claro que no nos hemos equivocado, pero no construiremos una dimensión a parte con ellos.

### Dimensiones estabilizadoras (Outrigger Dimensions - OUTGDIM -)

Dimensiones que hacen referencia a otras dimensiones mediante **_FKs_** (subdimensiones).

Las **_OUTGDIM_** a menudo se consideran como un antipatrón de **_DWH_** y suele considerarse una práctica mejor utilizar algunas **_tablas de hechos_** que relacionen las dos dimensiones.

**_¡Aviso!_**: Debemos intentar evitar las **_OUTGDIM_** en nuestro **_DDM_**.

#### Ejemplo de dimensiones estabilizadoras (Outrigger Dimensions - OUTGDIM -)

Consideremos Ventas, Productos y Categorías.

- **_Tabla de hechos_**: Ventas.
- **_Tabla de dimensión_**: Productos **_OUTGDIM_** (contiene la **_FK_** de la Categoría).
- **_Tabla de subdimensión_**: Categorías (contiene la **_PK_** de la Categoría).

Otra manera de verlas es como:

- **_Tabla de hechos_**: Ventas
- **_Tabla de dimensión_**: Productos. Cada producto contiene su Categoría incorporada.

El segundo modelo está **desnormalizado** y es **una mejor solución** para un **_DDM_**.

## Dimensiones estáticas o fijas (Static Dimensions - STATDIM -)

Proceden de la nada, son dimensiones que no existen en la base de datos **_OLTP_** de origen, como entidades.

Las dimensiones estáticas se generan mediante un script **_SQL_**, un procedimiento almacenado o un archivo externo, y se crean manualmente.

Dado que no se extraen de la fuente de datos **_OLTP_**, se puede considerar que se crean en el contexto del **_DWH_**.

Una práctica muy saludable es no crearlas en el **_DWH_**, sino en una hoja de cálculo en el servidor corporativo.

Una dimensión que no existe en la **_OLTP_** no necesariamente debe ser una **_STATDIM_**. Pero si colocamos las dimensiones estáticas y dinámicas que no existen en la **_OLTP_** en un libro (por ejemplo, Excel) en el servidor y las ponemos a disposición del usuario, entonces este será consciente de las entidades estáticas. Si creamos las **_STATDIM_** únicamente en el **_DWH_**, esto podría generar desconfianza en el usuario, ya que no las verá.

**_¡Advertencia!_**: No debemos confiar en los usuarios (Directores o Gerentes), si faltan entidades en la BD **_OLTP_** (que se convertirán en dimensiones en el **_DDM_**), debemos agregarlas antes del **_DWH_** y deben estar relacionadas con el **_DWH_**.

### Ejemplo de dimensiones estáticas o fijas (Static Dimensions - STATDIM -)

Un ejemplo que siempre tendremos es la **_tabla de dimensión_** de Tiempo; otro ejemplo son los códigos de estado.

Otros ejemplos (no estáticos, aunque no existan en la BD **_OLTP_**) pueden ser cualquier entidad que no exista en la BD **_OLTP_**.

## Dimensiones que cambian lentamente (Slowly Changing Dimensions - SCD -)

Una **_SCD_** en un **_DWH_** es una dimensión que contiene datos relativamente estáticos que pueden cambiar lentamente y de manera impredecible, en lugar de seguir un horario regular. Algunos ejemplos de dimensiones típicas que cambian lentamente son entidades como Localizaciones Geográficas, Clientes o Nombres de Productos. ([Wikipedia](https://en.wikipedia.org/wiki/Slowly_changing_dimension)).  

<p><br></p>

![001-Dimensión](https://i.imgur.com/kXDzc6e.png)  
_Dimensión (imagen 001)_  

<p><br></p>

#### Tipos de SCD

En una BD dimensional, las **_SCD_** son aquellas dimensiones que cambian con el tiempo, pero no necesariamente de manera constante o predecible. **Por ejemplo, en una tabla de datos de Clientes, la dirección del cliente puede cambiar con el tiempo, pero no todos los clientes cambian de dirección a la misma velocidad. Algunos clientes pueden cambiar de dirección cada mes, mientras que otros pueden mantener la misma dirección durante años**.

La gestión adecuada de las **_SCD_** es importante para mantener la precisión y la integridad de los datos dimensionales en una base de datos, ya que permite a los usuarios realizar análisis históricos y comparar datos a lo largo del tiempo.

#### Elección del tipo de SCD

La elección del tipo de **_SCD_** a utilizar depende de las necesidades específicas del **_DWH_** y las necesidades de análisis. Es importante tener en cuenta factores como la importancia de los datos históricos, la frecuencia de los cambios en las dimensiones y las implicaciones de almacenamiento y rendimiento de cada enfoque.

Las **_SCD_** son un aspecto crucial de los **_DWHs_**, ya que permiten representar los datos a lo largo del tiempo, facilitando así un análisis e informes históricos precisos.

#### SCD-0

**_SCD-0_** no tiene en cuenta la gestión de cambios históricos. Se aplica cuando la información nunca se modifica, es decir, los atributos en **_SCD-0_** nunca cambian y se asignan a atributos que tienen valores duraderos o se describen como "**originales**".

Se aplica a la mayoría de los atributos de las dimensiones.

Lo que esto significa en conjunto es que, **ya que no hay cambios en la tabla original, tampoco los hay en la dimensión**.

##### Ejemplo de SDC-0

Ejemplos: Fecha de nacimiento, puntuación de crédito original.

#### SCD-1

**_SCD-1_** no guarda históricos. La nueva información sobrescribe siempre la antigua. Principalmente, la sobrescritura se realiza debido a errores de calidad de datos. Este tipo de dimensiones es fácil de mantener y se utiliza cuando la información histórica no es importante. Es decir, **es apropiado cuando los datos históricos no son relevantes o cuando pueden recuperarse de otras fuentes**.

##### Ejemplo de SDC-1

Imaginemos el registro de un estudiante en una Facultad y luego, debido a reconsideraciones, cambia de Facultad.

**_Tabla transaccional en el momento del registro (día 1)_**

| **Id_Estudiante** | **Nombre Completo** | **Facultad** |
| :---------------: | :------------------ | :----------: |
| EST12345          | José López          | Marketing    |

**_Tabla dimensional en el momento del registro (día 1)_**

Creamos un Id_Estudiante que es una clave sustituta o subrogada -**_SK_**-, que tiene como objetivo mejorar el rendimiento en las búsquedas de la dimensión con una clave numérica.

| **Id_Estudiante** | **Código_Estudiante** | **Nombre Completo** | **Facultad** | 
| :---------------: | :-------------------: | :------------------ | :----------: |
| 1                 | EST12345              | José López          | Marketing    |

El día 2 José López cambia de Facultad a Ingeniería (lo ha reconsiderado).

**_Tabla transaccional en el momento del cambio de Facultad (día 2)_**

| **Id_Estudiante** | **Nombre Completo** | **Facultad** |
| :---------------: | :------------------ | :----------: |
| EST12345          | José López          | Ingeniería   |

**_Tabla dimensional en el momento del cambio de Facultad (día 2)_**

| **Id_Estudiante** | **Código_Estudiante** | **Nombre Completo** | **Facultad** |
| :---------------: | :-------------------: | :------------------ | :----------: |
| 1                 | EST12345              | José López          | Ingeniería   |

Lo que esto significa en conjunto es que, a pesar de que haya cambios, **cada vez se importan todos los registros de la tabla transaccional (borrando los que existían previamente en la tabla dimensional)**. Es decir, los registros en la tabla dimensional son los "**originales**" en la tabla transaccional, sin cambios.              

#### SCD-2

**_SCD-2_** almacena información histórica en el **_DWH_**.

Cuando hay un cambio, se crea una nueva entrada con su fecha y **_SK_** correspondiente.

**_SCD-2_** **se usa generalmente cuando se necesita un análisis histórico y la dimensión cambia relativamente poco**. Cuando se produce algún cambio en los valores de los registros, **se agrega una nueva fila** y se deben completar los datos relacionados con el historial de cambios.

##### Ejemplo de SDC-2

Imagina el mismo ejemplo anterior, pero esta vez queremos almacenar datos históricos.

**_Inscripción_**

**_Tabla transaccional en el momento del registro (día 1)_**

| **Id_Estudiante** | **Nombre Completo** | **Facultad** |
| :---------------: | :------------------ | :----------: |
| EST12345          | José López          | Marketing    |

**_Tabla dimensional en el momento del registro (día 1)_**

| **Id_Estudiante** | **Código_Estudiante** | **Nombre Completo** | **Facultad** | **Fecha_Inicio** | **Fecha_Fin** | **Versión** | **Actual** |
| :---------------: | :-------------------: | :------------------ | :----------: | :--------------: | :-----------: | :---------: | :--------: |
| 1                 | EST12345              | José López          | Marketing    | 01/01/2020       |               | 1           | True       |

El día 2, José López cambia de Facultad a Ingeniería (lo ha reconsiderado).

**_Tabla transaccional en el momento del cambio de Facultad (día 2)_**

| **Id_Estudiante** | **Nombre Completo** | **Facultad** |
| :---------------: | :------------------ | :----------: |
| EST12345          | José López          | Ingeniería   |

**_Tabla dimensional en el momento del cambio de Facultad (día 2)_**

| **Id_Estudiante** | **Código_Estudiante** | **Nombre Completo** | **Facultad** | **Fecha_Inicio** | **Fecha_Fin** | **Versión** | **Actual** |
| :---------------: | :-------------------: | :------------------ | :----------: | :--------------: | :-----------: | :---------: | :--------: |
| 1                 | EST12345              | José López          | Marketing    | 01/01/2020       | 01/01/2020    | 1           | False      |
| 2                 | EST12345              | José López          | Ingeniería   | 02/01/2020       |               | 2           | True       |

Esto significa que **cada vez que hay un cambio, se importan todos los registros de la tabla transaccional y se agrega una nueva fila a la tabla dimensional**, manteniendo así los datos históricos. La columna "Actual" indica la versión actual del registro.

#### SCD-3

**_SCD-3_** almacena información histórica en el **_DWH_**.

**_SCD-3_** se utiliza **cuando es importante seguir los cambios de atributos específicos mientras se mantiene la simplicidad en el modelo de datos**. **Requiere agregar** a la tabla de dimensión **una columna adicional** por cada columna cuyos valores se desea mantener un historial de cambios.

##### Ejemplo de SDC-3

Imagina el mismo ejemplo anterior pero esta vez queremos almacenar datos históricos.

**_Inscripción_**

**_Tabla transaccional en el momento del registro (día 1)_**

| **Id_Estudiante** | **Nombre Completo** | **Facultad** |
| :---------------: | :------------------ | :----------: |
| EST12345          | José López          | Marketing    |

**_Tabla dimensional en el momento del registro (día 1)_**

| **Id_Estudiante** | **Código_Estudiante** | **Nombre Completo** | **Facultad_Antigua** | **Facultad_Nueva** |
| :---------------: | :-------------------: | :------------------ | :------------------: | :----------------: |
| 1                 | EST12345              | José López          |                      | Marketing          |

El día 2, José López cambia de Facultad a Ingeniería (lo ha reconsiderado).

**_Tabla transaccional en el momento del cambio de Facultad (día 2)_**

| **Id_Estudiante** | **Nombre Completo** | **Facultad** |
| :---------------: | :------------------ | :----------: |
| EST12345          | José López          | Ingeniería   |

**_Tabla dimensional en el momento del cambio de Facultad (día 2)_**  

| **Id_Estudiante** | **Código_Estudiante** | **Nombre Completo** | **Facultad_Antigua** | **Facultad_Nueva** |
| :---------------: | :-------------------: | :------------------ | :------------------: | :----------------: |
| 1                 | EST12345              | José López          | Marketing            | Ingeniería         |

#### SCD-4 (historia separada)

**_SCD-4_** se conoce comúnmente como *tablas históricas*.

**_SCD-4_** utiliza "*tablas históricas*" donde **una tabla conserva los datos actuales y, además, se utiliza una tabla histórica** para mantener un registro de algunos o todos los cambios. Las dos **_SK_** (**_tabla de dimensión_** y **_tabla histórica_**) hacen referencia a la **_tabla de hechos_** para mejorar el rendimiento de la consulta.

##### Ejemplo de SDC-4

En el siguiente ejemplo, el nombre de la tabla original (transaccional) es Proveedor y la **_tabla histórica_** es Proveedor_Histórico:

| **SK** |**Clave** | **Nombre**                 | **Estado** |
| :----: | :----- | :----------------------- | :-------: |
| 124    | ABC    | Acme & Johnson Supply Co | IL        |


| **SK** |**Clave** | **Nombre**                 | **Estado** | **Fecha de registro** |
| :----: | :----- | :----------------------- | :-------: | :-------------------: |
| 123    | ABC    | Acme Supply Co           | AC        | 2003-06-14T00:00:00   |
| 124    | ABC    | Acme & Johnson Supply Co | IL        | 2004-12-22T00:00:00   |

Este método se asemeja a cómo funcionan las *tablas de auditoría* en bases de datos. Esta es una excelente manera de realizar un seguimiento de los registros que tienen muchos cambios a lo largo del tiempo.

A la tabla histórica a menudo se le llama *mini-dimensión*.

**_SCD-4_** se utiliza cuando **_SCD-2_** crece rápidamente debido a que los atributos de la dimensión cambian con frecuencia. En **_SCD-4_**, los atributos que cambian con frecuencia se eliminarán de la dimensión principal y se agregarán a la *mini-dimensión*.

Consideremos otro ejemplo para explicar lo anterior, con una **_tabla de dimensión_** de Clientes con la siguiente estructura:
- Clave (**_PK_**)
- Fecha de inicio
- Fecha de finalización
- Nombre
- Fecha de nacimiento
- Estado
- Rango de edad
- Rango de ingresos
- Rango de compras

Los atributos del cliente, como el nombre, la fecha de nacimiento y el estado del cliente, cambian muy raramente o ni siquiera cambian, pero se espera que los rangos de edad, los rangos de ingresos y los rangos de compras cambien con mucha frecuencia.

Si una organización con 100 millones de clientes utiliza esta dimensión de Cliente, es posible que esta dimensión crezca a 200 o 300 millones de registros en un año, asumiendo que habrá al menos dos o tres cambios por cliente al año.

En ese caso, podemos dividir la dimensión en dos dimensiones, una con los atributos que cambian con menos frecuencia y otra con los atributos que cambian con más frecuencia. Los atributos que cambian con frecuencia se agruparán en la *mini-dimensión*.

Dimensión de Clientes
- Clave (**_PK_**)
- Fecha de inicio
- Fecha de finalización
- Nombre
- Fecha de nacimiento
- Estado

Mini-dimensión
- Clave (**_PK_**)
- Rango de edad
- Rango de ingresos
- Rango de compras

La *mini-dimensión* contendrá una fila para cada posible combinación de atributos. En nuestro caso, todas las combinaciones posibles de rangos de edad, rangos de ingresos y rangos de compras estarán disponibles en la *mini-dimensión* con la misma **_PK_** que en la **_tabla de dimensión_**.

Si tenemos 20 rangos de edad diferentes, 4 rangos de ingresos diferentes y 3 rangos de compras, tendremos 20 X 4 X 3 = 240 combinaciones posibles diferentes.

Estos valores se pueden llenar en la tabla de **_mini-dimensión_** una vez para siempre con una **_SK_** que oscila entre 1 y 240.

**_Nota importante_**: En la *mini-dimensión* no se almacenan los atributos históricos, aunque la **_tabla de hechos_** conserva el historial de asignación de atributos de la dimensión.

Dado que ambas **_tablas de dimensión_** están relacionadas con una **_tabla de hechos_** (Ventas), esta tendrá la PK (natural) de la Dimensión de Clientes y la PK de la **_mini-dimensión_** (**_SK_**).

Hechos de Ventas
- PK_Clientes
- SK_MiniDimensión
- Fecha
- Clave Producto
etc.

Un desafío que se presenta es cuando la *mini-dimensión* comienza a cambiar rápidamente. En ese caso, se pueden introducir múltiples *mini-dimensiones* para gestionar estos escenarios. Si ningún registro de hechos debe asociar la dimensión principal y la *mini-dimensión*, se puede utilizar una **_tabla de hechos_** *sin hechos* para asociar la dimensión principal y la *mini-dimensión*.

#### SCD-5

**_SCD-5_** se basa en la *mini-dimensión* **_SCD-4_** al incrustar una clave de "mini-dimensión" del "*perfil actual*" en la dimensión base que se sobrescribe como un atributo **_SCD-1_**. Este enfoque, llamado **_SCD-5_** porque **4 + 1 = 5**, permite acceder a los valores de los atributos de la mini-dimensión asignados actualmente junto con los demás de la dimensión base sin vincularlos a través de una **_tabla de hechos_**. Normalmente representamos la dimensión base y el estabilizador del perfil de la mini-dimensión actual como una tabla única en la capa de presentación. Los atributos de los estabilizadores deben tener nombres de columnas diferentes, como "Nivel de Ingresos Actual", para distinguirlos de los atributos de la mini-dimensión vinculados a la **_tabla de hechos_**. El equipo de **_ETL_** debe actualizar/sobrescribir la referencia de la mini-dimensión **_SCD-1_** cada vez que la mini-dimensión actual cambie con el tiempo.

##### Ejemplo de SCD-5

Ventas (**_tabla de hechos_**)
- Fecha (**_FK_**)
- Cliente (**_FK_**)
- Perfil (**_FK_**)
...

Clientes (**_tabla de dimensión_**)
- Cliente (**_PK_**)
- ID de Cliente (**_NK_**)
- Nombre
...
- Perfil Actual (**_FK_**)

Perfil (mini-dimensión)
- Perfil (**_PK_**)
- Rango de Edad
- Puntuación de Frecuencia de Compra
- Nivel de Ingresos

Vista de la mini-dimensión como estabilizador
- Perfil Actual (**_PK_**)
- Rango de Edad Actual
- Puntuación de Frecuencia de Compra Actual
- Nivel de Ingresos Actual

#### SCD-6 (híbrida)

**_SCD-6_** combina los enfoques de los tipos 1, 2 y 3 (**1+2+3=6**). Consiste en considerar una **_SCD-1_** y agregar un par de columnas adicionales que indican el rango temporal de validez de una de las columnas de la tabla. Aunque el diseño es complejo, entre sus beneficios podemos destacar que **reduce el tamaño de las consultas temporales**. Existe otra variante para este tipo de dimensión, que consiste en tener versiones del registro de la dimensión (numeradas de 0 a n+1, donde 0 siempre es la versión actual).

##### Ejemplo de SCD-6

**_Inscripción en la Facultad de Astrofísica Azul_**

Tabla transaccional

| **Id_Estudiante** | **Nombre Completo** | **Facultad**     |
| :--------------:  | :------------------ | :--------------: |
| EST12345          | José López          | Astrofísica Azul |

Tabla de dimensión (después del **_ETL_**)

Nos damos cuenta, en el **_ETL_**, de que la Astrofísica Azul no es muy inteligente. Todos saben que la Astrofísica es Verde.

| **Id_Estudiante** | **Código_Estudiante** | **Nombre Completo** | **Facultad_Antigua** | **Facultad_Nueva** | **Fecha_Inicio** | **Fecha_Final** | **Facultad_Actual** |
| :--------------:   | :-----------------:   | :--------------      | :-----------------:   | :---------------:   | :------------:   | :------------: | :-----------------: |
| 1                  | EST12345              | José López         |                      | Astrofísica Verde  | 18/08/2023       |                  | N                   |

**_Cambio de Facultad a Clima Salado_**

Tabla transaccional

| **Id_Estudiante** | **Nombre Completo** | **Facultad** |
| :---------------: | :------------------ | :----------: |
| EST12345          | José López          | Clima Salado |

Tabla de dimensión (después del **_ETL_**)

| **Id_Estudiante** | **Código_Estudiante** | **Nombre Completo** | **Facultad_Antigua** | **Facultad_Nueva** | **Fecha_Inicio** | **Fecha_Final** | **Facultad_Actual** |
| :--------------:   | :-----------------:   | :--------------      | :-----------------:   | :---------------:   | :------------:   | :------------: | :-----------------: |
| 1                  | EST12345              | José López         |                      | Astrofísica Verde  | 18/08/2023       | 19/08/2023       | N                   |
| 2                  | EST12345              | José López         | Astrofísica Verde    | Clima Salado       | 19/08/2023       |                  | Y                   |

**_Cambio de Facultad, nuevamente, a Astrofísica Verde_**

Tabla transaccional

| **Id_Estudiante** | **Nombre Completo** | **Facultad**      |
| :---------------: | :------------------ | :---------------: |
| EST12345          | José López          | Astrofísica Verde |

Tabla de dimensión (después del **_ETL_**)

| **Id_Estudiante** | **Código_Estudiante** | **Nombre Completo** | **Facultad_Antigua** | **Facultad_Nueva** | **Fecha_Inicio** | **Fecha_Final** | **Facultad_Actual** |
| :--------------:   | :-----------------:   | :--------------      | :-----------------:   | :---------------:   | :------------:   | :------------: | :-----------------: |
| 1                  | EST12345              | José López         |                      | Astrofísica Verde  | 18/08/2023       | 19/08/2023       | N                   |
| 2                  | EST12345              | José López         | Astrofísica Verde    | Clima Salado       | 19/08/2023       | 20/08/2023       | N                   |
| 3                  | EST12345              | José López         | Clima Salado         | Astrofísica Verde  | 20/08/2023       |                  | Y                   |

#### SCD-7 (Híbrido: Clave Sustituta y Clave Natural)

Una implementación alternativa es incluir tanto la clave sustituta como la clave natural en la **_tabla de hechos_**.

Este método permite enlaces más flexibles a la dimensión, incluso si se ha utilizado **_SCD-2_** en lugar de **_SCD-6_**.

Con **_SCD-7_**, la **_tabla de hechos_** contiene claves dobles **_FKs_** para una dimensión dada: una **_SK_** vinculada a la tabla de dimensión donde se realiza el seguimiento de los atributos de tipo 2, además de la clave natural duradera de la dimensión vinculada a la fila actual de la dimensión tipo 2 para presentar el valor actual del atributo.

**_SCD-7_** ofrece la misma funcionalidad que **_SCD-6_**, pero se logra mediante claves duales en lugar de sobrescribir físicamente los atributos actuales como en **_SCD-6_**. Al igual que otros enfoques híbridos, los atributos de la dimensión actual deben etiquetarse de manera diferente para minimizar la confusión.

##### Ejemplo de SCD-7

Imagina el siguiente escenario:

Ventas (**_tabla de hechos_**)
- Fecha (**_PK_**)
- Clave de Producto (**_FK_**)
- Clave de Producto Duradera (**_DK_**)
- más **_FKs_**
- datos

Productos (**_tabla de dimensión_**)
- Clave de Producto (**_PK_**)
- **_SK_**
- **_DK_**
- Descripción
- Fecha de Inicio
- Fecha de Finalización
- ...

Productos Actuales (**_tabla de dimensión actual_**)
- **_DK_**
- Descripción
- ...

#### Resumen de SCD's

| **_SCD_**   | **_Tabla de Dimensión_**                        | **_Tabla de Hechos_**                                                                                                |
| :---------: | :--------------------------------------- | :------------------------------------------------------------------------------------------------------------- |
| **_SCD-0_** | Sin cambios en el valor del atributo     | Los hechos asociados al valor original del atributo                                                              |
| **_SCD-1_** | Sobrescribe el valor del atributo        | Los hechos asociados al valor actual del atributo                                                                |
| **_SCD-2_** | Agrega una nueva fila con el nuevo valor del atributo | Los hechos asociados al valor del atributo cuando ocurre el hecho                                      |
| **_SCD-3_** | Agrega una nueva columna para preservar los valores del atributo anterior y actual | Hechos asociados al valor alternativo del atributo (anterior y actual)        |
| **_SCD-4_** | Agrega una *mini-dimensión* que contiene atributos que cambian rápidamente | Los hechos asociados a atributos que cambian rápidamente tienen efecto cuando se produce un hecho |
| **_SCD-5_** | **_SCD-4_** + **_SCD-1_** = Agrega la mini-dimensión (**_SCD-4_**), junto con la clave de **_SCD-1_** sobrescrita en la dimensión base | Los hechos asociados con atributos que cambian rápidamente tienen efecto cuando se produjo el hecho, además de los valores de los atributos actuales que cambian rápidamente |
| **_SCD-6_** | **_SCD-1_** + **_SCD-2_** + **_SCD-3_** = Agrega atributos sobrescritos **_SCD-1_** a la fila de dimensiones **_SCD-2_** y sobrescribe todas las filas de dimensiones anteriores | Los hechos asociados al valor del atributo cuando se produjo el hecho, más los valores actuales |
| **_SCD-7_** | Agrega una fila de dimensión **_SCD-2_** con un valor de atributo nuevo, además de la visualización limitada a las filas y/o los valores de atributo actuales | Los hechos asociados al valor del atributo cuando se produjo el hecho, más los valores actuales |

### Dimensiones que Cambian Rápidamente (Rapidly Changing Dimensions - RCD -)

Estas son dimensiones que cambian (o pueden cambiar) rápidamente con el tiempo. Las **_RCDs_** generalmente se implementan como **_JUNKDIMs_**.

La manipulación de las **_RCDs_** en el **_DWH_** es muy difícil debido al rendimiento. Como hemos visto, las **_SCD_** se utilizan para mantener el historial de cambios. Sin embargo, el problema con **_SCD-2_** es que con cada cambio en el atributo de la dimensión, agrega una nueva fila a la tabla. Si hay dimensiones que cambian con frecuencia, la tabla se vuelve más grande y puede causar graves problemas de rendimiento. Por lo tanto, el uso de **_SCD-2_** puede no ser una buena decisión para implementar dimensiones que cambian rápidamente.

##### Ejemplo de RCD

Supongamos que en la dimensión Cliente tenemos 1000 filas. En promedio, cada cliente cambia 10 atributos al año. Si usamos **_SCD-2_** para gestionar este escenario, habrá 1000*10 = 10000 filas al final del año. Si la tabla tiene millones de filas, será muy difícil gestionar la situación con **_SCD-2_**. Por lo tanto, usaremos un enfoque **_RCD_**.

Para implementarlo, usaremos un atributo de cambio rápido por separado, mediante la implementación de una **_JUNKDIM_**.

En la tabla de hechos, no todos los atributos cambian rápidamente. Algunos atributos pueden cambiar rápidamente y otros no. La idea aquí es separar el atributo que cambia rápidamente de los que cambian lentamente y mover estos atributos que cambian rápidamente a otra tabla **_JUNKDIM_**, manteniendo el atributo que cambia lentamente en la misma tabla. De esta manera, podemos gestionar situaciones de aumento en el tamaño de la tabla.

Cliente (**_tabla de dimensión_**)
- ID
- Nombre
- Ciudad
- Estado
- Género
- Ingresos
- Valoración
- Puntuación de Crédito

Atributos como ID, Nombre, Ciudad, Estado o Género no cambiarán o cambiarán muy raramente. En cambio, atributos como Ingresos, Valoración y Puntuación de Crédito cambian cada mes según las circunstancias del cliente. Por lo tanto, debemos separar estas columnas de la tabla de clientes; de lo contrario, llenaríamos la tabla si usamos **_SCD-2_** en la dimensión Cliente. Podemos colocar estas columnas que cambian rápidamente en la **_tabla de dimensiones JUNKDIM_**.

Cliente Junk (**_JUNKDIM_**)
- SK
- Ingresos
- Valoración
- Puntuación de Crédito

La dimensión Cliente permanece como:

Cliente (**_tabla de dimensión_**)
- ID
- Nombre
- Ciudad
- Estado
- Género

Sin embargo, debemos enlazar la **_JUNKDIM_** (Cliente Junk) y la **_tabla de dimensión_** (Cliente). Además, no podemos simplemente hacer referencia a la **_JUNKDIM_** agregando su clave primaria (**_SK_**) a Cliente como una clave foránea (**_FK_**). Dado que cualquier cambio realizado en la **_JUNKDIM_** debe reflejarse en la **_tabla de dimensión_**, esto aumenta obviamente los datos del Cliente. En su lugar, crearemos otra tabla de *mini-dimensión* que actúe como puente entre la **_tabla de dimensión_** y la **_JUNKDIM_**. También podemos agregar columnas como la fecha de inicio y finalización para realizar un seguimiento del historial de cambios.

Cliente Mini Dim (**_Bridge Dimension - BRIDDIM-_**)
- ID
- SK
- Fecha de Inicio
- Fecha de Finalización

Las **_BRIDDIMs_** permiten definir relaciones muchos a muchos entre **_tablas de hechos_**. Son necesarias para definir, por ejemplo, la relación entre un piloto y sus múltiples patrocinadores (m:n).

Esta tabla es solo un puente entre dos tablas y no requiere ninguna clave (**_SK_**).

Un ejemplo de RCD, cuando el crecimiento es explosivo, son las Dimensiones Monstruo (**_MONSDIM_**).

### Dimensiones Apiladas (Stacked Dimensions - STACDIM -)

**_STACDIM_** se utiliza cuando dos o más dimensiones se combinan en una sola dimensión. Tiene uno o dos atributos y siempre es **_SCD-0_**.

Ejemplos (tipo y estado): Tipo de producto, Estado del cliente, Tipo de tienda, Tipo de seguridad, etc. Todas estas columnas deberían almacenarse en sus respectivas dimensiones porque son propiedades de la dimensión.

Sin embargo, existen columnas de tipo y estado que son propias de la **_tabla de hechos_**, como Tipo de transacción o Estado de transacción. **Para combinar el tipo de transacción y el estado de la transacción en una dimensión, creamos una dimensión ficticia. Sin embargo, nunca deberíamos utilizar una STACDIM**.

**No se recomienda el uso de una STACDIM. Está mal utilizarlas, pero existen**. Normalmente, esto se debe a que era así en el sistema original, por lo que simplemente se copia al **_DWH_** (sin reflexionar).

### Dimensión Diferida (Deferred Dimension - DEFEDIM -)

Cuando se carga un registro de una **_tabla de hechos_**, es posible que un registro de una **_tabla de dimensión_** aún no esté listo. Técnicamente se le llama **_miembro inferior_** o **_dimensión pendiente_**.

### Dimensión Distorsionada (Distorted Dimension - DISTDIM -)

Una dimensión que se utiliza en muchos lugares se llama **_dimensión distorsionada - DISTDIM -_**. Puede utilizarse en una sola base de datos o en varias, o en múltiples **_tablas de hechos_**, o en múltiples **_DM_** o **_DWH_**.

## Algunas de las Peores Prácticas al Trabajar con Dimensiones y Hechos

1. **No diseñar la dimensión de Tiempo en el DWH, esperando que la herramienta de BI lo haga por ti**.

2. **Diseñar el DDM con JUNKDIMs en lugar de tablas de hechos normales**.

3. **No crear SHRKDIMs y esperar a que la herramienta de BI lo haga por ti**.

4. **No conocer suficientemente el negocio, lo que puede llevar a no contemplar CONFDIMs esenciales en nuestro DDM**.

5. **No definir adecuadamente las RPLYDIMs por desconocimiento de algún punto en el pipeline de nuestro proyecto**.

6. **Definir DEGEDIMs**.

7. **Debemos evitar las OUTGDIM en nuestro DDM**.

8. **Si confías en los usuarios (Directores o Gerentes), faltarán STATDIMs en tu DDM**.

9. **Considerar los cambios en las dimensiones como SCD-0**.

10. **Considerar SCD-2 en lugar de contemplar posibles RCD**.

11. **Si encuentras alguna STACDIM en el DDM, entonces has cometido un error en el análisis dimensional**.

## Conclusiones

Diseñar la solución **_BI_** o cualquier otra visualización de análisis de datos es una pequeña parte del trabajo, equivalente a la parte visible de un iceberg (solo vemos el 20% del trabajo). Esto es lo que percibe el usuario.

Diseñar las **_tablas de hechos_** y las **_tablas de dimensiones_** es una parte pequeña pero importante del trabajo, y no es percibida por los usuarios. Como Ingenieros de Datos, debemos realizar muchas otras tareas, como maximizar el rendimiento, diseñar un **mecanismo de captura de datos modificados** (**_CDC_**), que nos asegure que los datos se carguen de forma incremental, si es necesario.

En los procesos **_ETL_** complejos, es posible que necesitemos actualizaciones frecuentes según las necesidades del negocio. **Es posible que tengamos que agregar o eliminar campos, cambiar tipos de datos, modificar el SCD aplicado a una tabla, etc.** Realizar estos cambios en las consultas no solo lleva mucho tiempo, sino que también es propenso a generar errores.

**_¡MUY IMPORTANTE!_**: **Con más frecuencia que en las bases de datos OLTP, con cualquier cambio menor solicitado por los usuarios y antes de que nos demos cuenta, es posible que hayamos dañado el pipeline existente. Es recomendable trasladar los cambios al DWH tanto como sea posible y verificar el impacto antes de la fase de visualización de datos**.

Si en este punto pensamos que la mayor parte del trabajo duro está hecho, debemos considerar que **las empresas buscan constantemente modernizar y mejorar sus procesos de datos**. Puede llegar el día en que nuestra empresa decida cambiar de plataforma de almacenamiento de datos. Supongamos que han decidido pasar de una base de datos local a una plataforma en la nube.

**_¡MUY IMPORTANTE!_**: Para prevenir este problema, **primero debemos crear una nueva arquitectura en la nueva plataforma**, luego **debemos reescribir todos los procesos ETL para reconfigurar los nuevos pipelines**.

Imaginemos que, por alguna razón, hemos tenido que trabajar sin un **_DWH_**, directamente con **_Power BI_**, y ahora queremos implementar procesos **_ETL_** con Pentaho Data Integration (**_PDI_**). En ese caso, primero debemos diseñar las tablas de dimensiones y hechos en el **_DWH_**, luego implementar los procesos **_ETL_**, y finalmente volver a implementar la visualización en **_Power BI_**. Esto implica redescribir todo el pipeline y posiblemente adoptar una estrategia de trabajo colaborativo si no se había adoptado antes.

Los **roles necesarios** para abordar estas tareas van desde **Analistas de Datos** hasta **Ingenieros de Datos** y pueden ser de gran complejidad.

**En otras palabras, el impacto de los cambios puede ser tan grande que posiblemente debamos repetir todo el proceso desde cero**. Por lo tanto, **el nivel de complejidad involucrado puede ser muy alto, incluso para usuarios técnicos. Es absolutamente esencial que estos profesionales técnicos tengan certificación (no necesariamente universitaria, pero reconocida) como Analistas de Datos y/o Ingenieros de Datos**.

**Conclusión: El análisis y la ingeniería involucrados deben ser realizados por técnicos calificados, equipados con los recursos adecuados y guiados, en términos de negocios, por usuarios lo suficientemente calificados y motivados**.

## ¿Te ha interesado el artículo?

Si tienes alguna duda con respecto a lo expuesto en este artículo, házmelo saber en los comentarios.

Por favor, si te ha gustado el contenido de este artículo, deja un comentario o un "me gusta". Además, si consideras que es lo suficientemente bueno o que puede ayudar a otros usuarios de la red, comparte la publicación en esta plataforma. De esta manera, todos juntos podemos democratizar el conocimiento y quizás ayudar a otras personas que lo necesiten.

## Lista de Imágenes

- **img001**: Del curso "Data Warehousing y modelado OLAP" de Platzi.

## Descargo de Responsabilidad

**_Platzi_** (anteriormente Mejorando.la) es una plataforma de educación en línea de América Latina. Fue fundada en 2011 por el ingeniero colombiano **Freddy Vega** y el científico de la computación guatemalteco **Christian Van Der Henst**. ([Wikipedia](https://es.wikipedia.org/wiki/Platzi))

## Uso de ChatGPT

**_Uso de ChatGPT 3.5_**

Este proyecto ha sido verificado para ortografía, sintaxis y contenido utilizando [**_ChatGPT 3.5_**](https://chat.openai.com/chat).

Referencia:  
[OpenAI. (2023). ChatGPT (versión del 3 de octubre de 2023) \[Large Language Model\]](https://chat.openai.com/chat)