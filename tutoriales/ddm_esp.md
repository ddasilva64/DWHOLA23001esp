# Modelado Dimensional de Datos (DDM) - esp -

## Proceso de DDM

1. Elegir el **proceso de negocio (en el que tenemos un buen conocimiento)**.
2. Definir el **"grano"** (**granularidad**).
3. Identificar las **dimensiones** (**atributos**).
4. Identificar los **hechos** (**métricas**).

## Identificación de dimensiones y hechos

> "El trabajo consiste en hacer preguntas, tantas como puedas, y enfrentar la falta de respuestas precisas con cierta humildad."
> [Arthur Miller]

**_DDM_** utiliza un conjunto de técnicas y conceptos utilizados en el diseño de **_DWH_**. Se considera diferente al modelo **_ER_**. **_DDM_** no implica necesariamente una **_RDB_**; el mismo enfoque de modelado, a nivel lógico, se puede utilizar para cualquier forma física, como bases de datos multidimensionales o archivos planos. ([Wikipedia](https://es.wikipedia.org/wiki/Modelado_dimensional))

## Tablas de dimensiones y hechos

> En un sistema **_DWH_** o un sistema **_OLAP_**, la construcción de **_Cubos OLAP_** requiere una **_tabla de hechos_** y varias **_tablas de dimensiones_**. Estas acompañan a la **_tabla de hechos_** y determinan los parámetros (dimensiones) en los que se basan los hechos registrados en la **_tabla de hechos_**.
Cuando se construyen cubos **_OLAP_**, las **_tablas de dimensiones_** son elementos que contienen atributos (o campos) utilizados para restringir y agrupar los datos almacenados en una **_tabla de hechos_** al realizar consultas en un entorno **_DWH_** o **_DM_**.
Estas dimensiones son parámetros en los que dependen otros datos, que serán objeto de estudio y análisis y que están contenidos en la **_tabla de hechos_**. Las **_tablas de dimensiones_** ayudan a llevar a cabo este estudio/análisis proporcionando información sobre los datos de la **_tabla de hechos_**, por lo que se puede decir que en un cubo **_OLAP_**, la **_tabla de hechos_** contiene los datos de interés, y las **_tablas de dimensiones_** contienen metadatos sobre esos hechos.

> [Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_dimensi%C3%B3n)

En esencia, **las tablas de dimensiones plantean preguntas y las tablas de hechos proporcionan respuestas**.

### Hechos

- Almacenan las **métricas** que deseamos consultar con las perspectivas proporcionadas por las dimensiones.

- Tendrán las **claves foráneas (FKs) de las relaciones** con las tablas de dimensiones.

Ejemplos:

- Ventas.
- Productividad.

![001-DDM](https://i.imgur.com/6KaLmDY.png)  
_DDM (imagen001)_

![002-Tabla de hechos](https://i.imgur.com/nP9xodv.png)  
_Tabla de hechos (imagen002)_

![003-Tabla de hechos Ventas](https://i.imgur.com/nAjHiaZ.png)  
_Tabla de hechos Ventas (imagen003)_

La tabla central (Ventas) es la **_tabla de hechos_** de un **_esquema en estrella_**, rodeada de cinco tablas (Producto, Tiempo, Almacén, Promoción y Cliente), que constituyen las cinco dimensiones que comprenden Ventas. En esta tabla, se almacenan, en este caso, las unidades vendidas y el precio obtenido por esas ventas; estos son los hechos empresariales o medidas almacenadas. ([Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_hechos)).

## Medidas o métricas de negocio (hechos)

Las medidas más útiles para incluir en una **tabla de hechos** son aquellas que se pueden sumar, como la cantidad de productos vendidos, los costos de producción o los ingresos obtenidos por las ventas. Estas son mediciones numéricas que se pueden calcular sumando varias cantidades en la tabla. Por lo tanto, los **hechos** que se almacenan en una tabla de hechos casi siempre serán valores numéricos, enteros o reales. ([Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_hechos))

## Cardinalidad de la tabla de hechos

Las **tablas de hechos** pueden contener un gran número de filas, a veces cientos de millones de registros cuando abarcan uno o más años de la historia de una organización. Esta cardinalidad estará limitada principalmente por la cardinalidad de las **tablas de dimensiones**. Por ejemplo, si tienes una **tabla de hechos "FT"** con tres dimensiones, D1, D2 y D3, el número máximo de elementos que tendrá la tabla de hechos FT es:

**$Card(FT) = Card(D1) * Card(D2) * Card(D3)$**

Donde $Card(x)$ es la cardinalidad de la tabla.

**_¡Muy importante!_**: Esto significa que las **tablas de dimensiones** siempre deben construirse antes que la **tabla de hechos** en el modelo.

Naturalmente, estas cardinalidades no son fijas. Por ejemplo, si una de las dimensiones se refiere a los Clientes, cada vez que se registra un nuevo cliente, la cardinalidad de la **tabla de hechos** aumentará. Una de las dimensiones suele ser el Tiempo, que se puede medir de varias formas (horas, días, semanas, etc.). Sin embargo, progresa continuamente, y para que el sistema funcione, periódicamente se deben agregar registros a la tabla de la dimensión de Tiempo, lo que también aumenta la cardinalidad de la **tabla de hechos**. Esta es la razón principal por la que las **tablas de hechos** pueden contener millones de registros. ([Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_hechos))

## Granularidad (grano)

Una característica importante que define una **tabla de hechos** es el nivel de granularidad de los datos que almacena. **La granularidad** se refiere al nivel de detalle de los datos que almacena, representando el nivel más atómico por el cual se define la información en la **tabla de hechos**. Por ejemplo, contar el tiempo por horas (granularidad fina) no es lo mismo que contar el tiempo por semanas (granularidad gruesa). De manera similar, en el caso de los Productos, cada variante del mismo artículo puede considerarse como un producto (por ejemplo, diferentes tallas y colores de pantalones) o agruparse como un solo producto (por ejemplo, pantalones genéricos).

Como puedes ver, la granularidad afecta la cardinalidad tanto de las **dimensiones** como de la **tabla de hechos**. Cuanto mayor sea la granularidad (grano más fino), mayor será el número final de registros en la **tabla de hechos**.

> Cuanto mayor sea la **granularidad** de las **tablas de dimensiones**, mayor será la cardinalidad de la **tabla de hechos**.

Cuando la granularidad es mayor, es común querer tener subtotales parciales. Por ejemplo, si tenemos una **tabla de hechos** con ventas por día, podría ser interesante tener totales semanales o mensuales. Estos datos se pueden calcular mediante sumas parciales, pero es común agregar registros a la **tabla de hechos** donde se almacenan estos cálculos para mejorar el rendimiento de las consultas. En este caso, tanto los datos de granularidad fina como los de granularidad gruesa estarán disponibles en la misma **tabla de hechos**, aumentando aún más su cardinalidad. ([Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_hechos))

## Agregación

La agregación es un proceso de cálculo en el que se resumen los datos de los registros detallados, lo que suele dar como resultado medidas de granularidad gruesa. Cuando los datos se resumen, los detalles ya no están disponibles directamente para el analista, ya que se eliminan de la **tabla de hechos**. Esta operación generalmente se realiza con los datos más antiguos para seguir teniendo esa información, incluso si está resumida, mientras que los registros obsoletos se pueden eliminar de la **tabla de hechos** para liberar espacio. ([Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_hechos))

## Tipos de datos adecuados

Como se mencionó anteriormente, las **tablas de hechos** a menudo almacenan millones de registros. Por lo tanto, **es crucial no desperdiciar memoria**, y debemos utilizar los tipos de datos adecuados. Si una medida se puede almacenar en un campo entero, no debemos definir ese campo como un tipo entero largo o real. De manera similar, si una magnitud requiere decimales y sus características lo permiten, es mejor utilizar un tipo real simple que un tipo real de doble precisión. Ten en cuenta que elegir un tipo de datos sobre otro puede resultar inicialmente en una diferencia de unos pocos bytes por registro, pero cuando se trata de cientos de millones de registros en una **tabla de hechos**, esta diferencia se vuelve significativa (por ejemplo, 5 bytes x 200 millones de registros = 1 GB de memoria). ([Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_hechos))

[Enfoque de Diseño para Manejar Dimensiones y Hechos de Llegada Tardía](https://www.disoln.org/2013/12/Design-Approach-to-Handle-Late-Arriving-Dimensions-and-Late-Arriving-Facts.html)

### Dimensiones

- Almacenan los atributos que nos permiten tener **diferentes perspectivas sobre los eventos**.

- Tendrán las **PKs de las relaciones** con las tablas de hechos.

Ejemplos:

- Productos.
- Subcategorías.
- Clientes.
- Empleados.

#### La dimensión del Tiempo

En cualquier **_DWH_**, puedes encontrar varios cubos con sus **_tablas de hechos_** llenas de registros sobre alguna variable de interés para el negocio que debe estudiarse. Como se mencionó anteriormente, cada **_tabla de hechos_** estará rodeada de varias **_tablas de dimensiones_**, según los parámetros que mejor sirvan para llevar a cabo el análisis de los hechos que deseas estudiar. Un parámetro que casi con seguridad será común a todos los cubos es el Tiempo, ya que lo habitual es almacenar los eventos a medida que ocurren con el tiempo, obteniendo así una serie temporal de la variable a estudiar.

Dado que el Tiempo es una dimensión presente en prácticamente cualquier cubo de un sistema **_OLAP_**, merece especial atención. Al diseñar la dimensión del Tiempo (tanto para un **_esquema en estrella_** como para un **_esquema en copo de nieve_**), se debe tener especial cuidado, ya que se puede hacer de varias formas y no todas son igualmente eficientes. La forma más común de diseñar esta tabla es establecer la fecha o fecha/hora como la **_PK_** de la tabla (tabla de tiempo 1). Este diseño no es el más recomendado, ya que la mayoría de los sistemas de gestión de bases de datos consideran que es más costoso buscar en campos de tipo "fecha" o "datetime". Estos costos se reducen si el campo clave es de tipo entero. Además, un dato entero siempre ocupa menos espacio que un dato de tipo fecha (el campo clave se repetirá en millones de registros en la tabla de hechos y eso puede ocupar mucho espacio). Por lo tanto, el diseño de la tabla de tiempo, si se utiliza un campo "TimeID" entero como clave primaria (tabla de tiempo 2).

**Tabla de tiempo (1)**

| Fecha (PK) | Fecha/hora |
| :-------- | :---------: |
| Año        | char(4)     |
| Trimestre | char(6)     |
| Mes        | char(10)    |

**Tabla de tiempo (2)**

| TimeID (PK) | Entero |
| :---------- | :-----: |
| Fecha         | Fecha/hora |
| Año           | char(4)     |
| Trimestre   | char(6)     |
| Mes           | char(10)   |

Al completar la tabla de Tiempo, si has elegido un campo entero para la clave, existen dos opciones: la más inmediata es asignar valores numéricos consecutivos (1, 2, 3, 4...) para diferentes valores de fecha. La otra opción consistiría en asignar valores numéricos del tipo "yyyymDDM", es decir, los primeros cuatro dígitos del valor del campo indican el año de la fecha, los siguientes dos el mes y los dos últimos el día. Esta segunda forma proporciona una ventaja sobre la primera, ya que de esta manera se logra que los datos numéricos en sí mismos proporcionen información sobre a qué fecha se refieren. Por ejemplo, si se encuentra el valor 20040723 en la tabla de hechos, sabremos que se refiere al 23 de julio de 2004. Por otro lado, con el primer método, se podrían encontrar valores como 8456456, y para saber a qué fecha se refiere este valor, se tendría que hacer una consulta en la tabla de tiempo.

Además del campo clave TimeID, la **_tabla de hechos_** debe contener otros campos que también son importantes de considerar. Estos campos serían:

- Un campo "año": que contendría valores como '2002', '2003', '2004'...
- Un campo "mes": aquí se pueden ingresar los valores 'enero', 'febrero'... (o en forma abreviada: 'ene', 'feb'...). Aunque esto no es incorrecto, se puede mejorar si se acompaña el nombre del mes con el año al que pertenece. Por ejemplo: 'enero 2004', 'febrero 2004'... De esta manera, se optimiza la búsqueda de los valores de un mes específico, ya que con el primer método, si se necesitan los valores pertenecientes al mes de "enero 2003", toda esta información está contenida en un solo campo, el "mes", y no sería necesario consultar también el campo del año.
- Un campo "mesID": este campo debería ser de tipo entero y serviría para almacenar valores del tipo 200601 (para 'enero 2006') o 200602 (para 'febrero 2006'). De esta manera, es posible organizar y agrupar por mes.

De manera análoga a lo que se ha hecho con el campo del mes, se podrían agregar más campos como "Época del año", "Trimestre", "Quincena", "Semana" de tipo texto para poder mostrarlos, y sus análogos de tipo entero "Época del año_ID", "TrimestreID", "QuincenaID", "SemanaID" para poder agrupar y ordenar. En general, se puede agregar un campo para cada nivel de granularidad deseado.

Otro campo especial que se puede agregar es el "Día de la semana" ('lunes', 'martes'...). Este campo suele agregarse para poder realizar estudios sobre el comportamiento de los días de la semana en general (no el primer lunes del mes de enero de un año específico, este tipo de estudio generalmente no es de interés), y por esta razón, este campo no necesita estar acompañado del mes o año como los campos anteriores. También se puede agregar su campo dual "ID" de tipo entero para poder ordenar y agrupar si es necesario.

Con las adiciones descritas, podríamos tener una tabla de Tiempo como la que se muestra en la figura "Tabla de Tiempo (3)". Esto sería válido para un diseño en estrella. Para un diseño en copo de nieve, la tabla de Tiempo se tendría que desglosar en tantas tablas como niveles jerárquicos contenga. Observa que los campos "ID" son todos de tipo entero, ya que será en estos campos donde se realizarán la mayoría de las operaciones y estas se realizarán de manera más eficiente en datos enteros.

**Tabla de tiempo (3)**

| TimeID (PK)   | Entero |
| :------------ | :-----: |
| Fecha          | Fecha/hora |
| Año             | char(4) |
| Trimestre    | char(6) |
| TrimestreID | Entero |
| Mes            | char(10)|
| MesID          | Entero |
| Quincena     | char(10)|
| QuincenaID | Entero |
| Semana      | char(10)|
| SemanaID   | Entero |
| Día             | char(10)|
| DíaID         | Entero |
| DíaSemana | char(10)|
| DíaSemanaID | Entero |

#### Granularidad de la dimensión y jerarquías

Cada dimensión puede referirse a conceptos como Tiempo, Productos, Clientes, Área Geográfica, etc. Ahora bien, cada dimensión puede medirse de diferentes maneras según la granularidad deseada. Por ejemplo, para la dimensión Área Geográfica, podríamos considerar 'localidades', 'provincias', 'regiones', 'países' o 'continentes'.

**La granularidad de las tablas de hechos y dimensiones siempre debe ser la misma**; de lo contrario, no se podrá consultar la información con el grado de granularidad más alto.

![004-Granularidad de la Zona Geográfica](https://i.imgur.com/vDzQjC2.png)  
_Granularidad de la Zona Geográfica (imagen004)_

La unidad de medida (por localidades, provincias, etc.) determinará esta granularidad. Cuanto más pequeña sea esta unidad de medida, más fina será esta granularidad (grano fino); si las unidades de medida son más grandes, entonces hablaremos de granularidad gruesa (grano grueso).

En muchos casos, es interesante tener los datos en varios niveles de granularidad, es decir, es importante para el negocio poder consultar los datos (siguiendo el ejemplo de las zonas) por localidades, provincias, etc. En estos casos, se crea una jerarquía dentro de la dimensión, ya que tenemos varios niveles de asociación de los datos (con otras dimensiones como el tiempo, se podrían crear niveles jerárquicos del tipo 'días', 'semanas', 'meses', etc.).

![005-Detalle de la Tabla de Dimensión](https://i.imgur.com/gHgpzeR.png)  
_Detalle de la Tabla de Dimensión (imagen005)_

Cuando las **_tablas de dimensiones_** asociadas a una **_tabla de hechos_** no reflejan ninguna jerarquía (por ejemplo: las Zonas son siempre solo 'provincias', el tiempo se mide solo en 'días', etc.), el cubo resultante será en forma de estrella. En este modelo, hay una **_tabla de hechos central_** rodeada por tantas tablas como dimensiones, y además de la **_tabla de hechos_**, solo habrá una tabla para cada dimensión.

![006-Esquema Estrella de 5 dimensiones](https://i.imgur.com/4HPriuo.png)  
_Esquema Estrella de 5 dimensiones (imagen006)_

Cuando una o más de las dimensiones del cubo reflejan algún tipo de jerarquía, existen dos enfoques con respecto a la forma en que deben diseñarse las tablas de dimensión. El primero consiste en reflejar todos los niveles jerárquicos de una dimensión dentro de una sola tabla. En este caso, también tendríamos un **_esquema en estrella_** como el descrito anteriormente, es decir, en el modelo tendríamos tablas no normalizadas para mejorar el rendimiento.

El otro enfoque consiste en aplicar las reglas de normalización **_RDB_** a las dimensiones. Estas reglas están diseñadas para evitar redundancias de datos al aumentar el número de tablas; de esta manera, la información se almacena en menos espacio. Este diseño da como resultado un esquema en forma de copo de nieve. Esta forma de organizar las dimensiones de un cubo **_OLAP_** tiene una desventaja en comparación con el modelo en estrella que no compensa el ahorro de espacio de almacenamiento. En las aplicaciones OLAP, el recurso crítico no es tanto el espacio de almacenamiento como el tiempo de respuesta del sistema a las consultas de los usuarios, y se ha demostrado que los modelos en forma de copo de nieve tienen un tiempo de respuesta más largo que los modelos en estrella.

Suele ser mejor reducir las dimensiones en el **_DWH_** y obtener un **_esquema en estrella_** en lugar de un **_esquema en copo de nieve_**. Por lo tanto, **normalizar las tablas puede ser la peor solución, a diferencia del modelo DBR**.

![007-Esquema en Copo de Nieve de 5 dimensiones](https://i.imgur.com/1Ata1st.png)  
_Esquema en Copo de Nieve de 5 dimensiones (imagen007)_

# Tablas de hechos desnormalizadas

Imagina el mismo escenario:

Tienes la misma tabla en una hoja de cálculo con las mismas columnas, relacionada con Vendedores (tabla Allowances):

| ID del Empleado | Nombre del Empleado | Fecha de Inicio | Fecha de Fin | Zona de Ventas | Comentarios                      | Asignaciones Mensuales |
| :---------: | :------------ | :--------: | :--------: | :--------- | :---------------------------- | -----------------: |
| 601         | John Smith    | 01/01/2021 | 31/03/2022 | O          | Estará allí durante 455 días | $456.24            |
| 601         | John Smith    | 01/04/2022 | 31/08/2022 | E          | Estará allí durante 153 días | $348.51            |
| 601         | John Smith    | 01/09/2022 | 31/12/2022 | C          | Estará allí durante 112 días | $132.97            |

Queremos relacionar esta tabla con la **_tabla de hechos_** de Ventas que proviene de la base de datos **_OLTP_**. También queremos relacionar estas dos tablas, pero ahora no tenemos un **_DWH_**. Estableceremos la relación en **_BI DM_**. Además, el Gerente de Ventas desea revisar fácilmente esta tabla (desnormalizada) sin tener que revisarla día a día.

Ahora, no podemos relacionarla por día con la tabla de Dimensión de Fechas porque no podemos descomprimir esta tabla. Calcularemos las asignaciones mediante cálculos, pero no mediante relaciones. Esto es más complicado que el ejemplo anterior.

Al desnormalizarla, obtenemos esta tabla:

| ID del Empleado | Nombre del Empleado | Fecha de Inicio 01 | Fecha de Fin 01  | Zona de Ventas 01 | Comentarios 01                   | Asignaciones Mensuales 01 | Fecha de Inicio 02 | Fecha de Fin 02  | Zona de Ventas 02 | Comentarios 02                   | Asignaciones Mensuales 02 | Fecha de Inicio 03 | Fecha de Fin 03  | Zona de Ventas 03 | Comentarios 03                   | Asignaciones Mensuales 03 |
| :---------: | :------------ | :-----------: | :----------: | :------------ | :---------------------------- | --------------------: | :---------: | :-----------: | :----------: | :------------ | :---------------------------- | :-----------: | :----------: | :------------ | :---------------------------- | --------------------: |
| 601         | John Smith    | 01/01/2021    | 31/03/2022   | O             | Estará allí durante 455 días | $456.24               | 01/04/2022    | 31/08/2022   | E             | Estará allí durante 153 días | $348.51               | 01/09/2022    | 31/12/2022   | C             | Estará allí durante 112 días | $132.97               |

¿Cuántos registros tendrá esta nueva tabla desnormalizada? Tendrá 3 registros, lo que facilitará mucho la revisión para el Gerente de Ventas.

Esta tarea también se puede realizar fácilmente utilizando **_Python_** y **_Pandas_**.

Además, esta tabla tampoco es una tabla de dimensión, sino una tabla de hechos. Ahora, el ID del Empleado y la Zona de Ventas son claves foráneas (**_FKs_**), pero debemos calcular métricas en **_BI_** porque la Fecha no es un atributo en esta solución.

Aunque podemos responder preguntas, requerirá un esfuerzo innecesario en **_BI_**.

Por cierto, ahora las Asignaciones y las Ventas no están directamente relacionadas en **_DM_**; están relacionadas a través de **_tablas de dimensión_** (Empleados, Zonas de Ventas, pero no con Fechas).

# Tablas de hechos descomprimidas

Imagina el siguiente escenario:

Tienes una tabla en una hoja de cálculo con las siguientes columnas, relacionadas con Vendedores (llamada "Allowances"):

| ID del Empleado | Nombre del Empleado | Fecha de Inicio | Fecha de Fin   | Zona de Ventas | Comentarios                      | Asignaciones Mensuales |
| :---------:     | :------------       | :--------:      | :--------:     | :---------      | :---------------------------- | -----------------:     |
| 601             | John Smith          | 01/01/2021      | 31/03/2022     | O               | Estará allí durante 455 días | $456.24                |
| 601             | John Smith          | 01/04/2022      | 31/08/2022     | E               | Estará allí durante 153 días | $348.51                |
| 601             | John Smith          | 01/09/2022      | 31/12/2022     | C               | Estará allí durante 112 días | $132.97                |

Deseas relacionar esta tabla con la **_tabla de hechos_** de Ventas que proviene de la base de datos **_OLTP_**. También deseas relacionar estas dos tablas en tu **_DWH_**.

Además, necesitas calcular el costo por día en asignaciones. Por ejemplo, el 1 de enero de 2021, John Smith recibe $456.24/31 = $14.72, y el 31 de diciembre de 2022, recibe $132.97/31 = $4.29 en asignaciones.

La tabla de Ventas (**_tabla de hechos_**) tiene una granularidad diaria. Por lo tanto, debes asegurarte de que esta tabla también tenga la misma granularidad. En otras palabras, debes "descomprimir" esta tabla para que se vea de la siguiente manera:

| ID del Empleado | Fecha       | Zona de Ventas | Asignaciones Mensuales |
| :---------:     | :--------:  | :---------      | -----------------:     |
| 601             | 01/01/2021  | O               | $14.72                |
| 601             | 01/02/2021  | O               | $14.72                |
| 601             | 01/03/2021  | O               | $14.72                |
...
| 601             | 31/12/2022  | C               | $4.29                 |

¿Cuántos registros tendrá esta nueva tabla "descomprimida"? Tendrá un total de 720 registros (con granularidad diaria), calculados como 455 + 153 + 112 = 720.

Esta tarea se puede realizar fácilmente en **_Python_** y **_Pandas_** durante el proceso de **_ETL_**, utilizando una herramienta como **_Pentaho Data Integration_**.

De hecho, esta tabla no es una tabla de dimensión sino una tabla de hechos. El ID del Empleado, la Fecha y la Zona de Ventas son claves foráneas (**_FKs_**), y las asignaciones diarias son una métrica.

Con esta estructura, puedes responder preguntas como:

- ¿Cuánto costó John Smith en asignaciones en 2021?
- ¿Cuánto pagamos en asignaciones a todos los vendedores en marzo de 2022?
- ¿Cuánto pagamos en asignaciones entre el 1 de julio de 2022 y el 15 de septiembre de 2022 en la zona de ventas "O" (Oeste)?

Por cierto, las Asignaciones y las Ventas no están relacionadas directamente en el **_DWH_**; están relacionadas a través de **_tablas de dimensión_** (como Empleados, Fechas y Zonas de Ventas).

# Data Warehouse (DWH), Data Lake (DL) y Data Lakehouse (DLH)

## **_DWH_**

**Características**:
- El **repositorio central que contiene datos estructurados** (columnas y filas) obtenidos a través de un proceso de **_ETL_** desde sistemas transaccionales, archivos .CSV, etc.

**Utilizado por**:
- **_Analistas de Negocios_**

**Utilizado en**:
- **_Informes_**
- **_BSC_** (Cuadro de Mando Integral)
- **_BI_** (Inteligencia de Negocios)

![008-DWH](https://i.imgur.com/1NDE7J7.png)
 _DWH (imagen008)_

## **_Lago de Datos (DL)_**

**Características**:
- El **repositorio de datos estructurados** desde **_ETL_** (tablas y vistas de bases de datos, archivos .CSV, etc.), **datos semiestructurados y no estructurados** (como imágenes, videos, páginas web, etc.).
- Los datos se pueden almacenar y consultar fácilmente.
- Se puede considerar un **_repositorio de datos crudos_**.

**Utilizado por**:
- **_Científicos de Datos_**
- **_Ingenieros de Datos_**
- **_Analistas de Datos_**

**Utilizado en**:
- **_ML_** (Aprendizaje Automático)
- **_Analítica Predictiva_**
- **_Descubrimiento de Datos_**
- **_Perfilado_**

![009-DL](https://i.imgur.com/RtAStZl.png)  
 _DL (imagen009)_

### DWH vs. DL

| Características | DWH | DL |
| :-------------- | :-- | :- |
| Datos | Optimizado para analizar datos relacionados de fuentes como bases de datos transaccionales, bases de datos operativas y aplicaciones de negocios. | Compuesto por datos no relacionados de fuentes como sitios web, redes sociales, dispositivos IoT y aplicaciones móviles. |
| Esquema | La estructura de datos se define antes de la implementación para optimizar las consultas (esquema al escribir). | Almacena información sin definir una estructura de datos. Permite implementar sin siquiera conocer las preguntas comerciales (esquema al leer). |
| Calidad de Datos | Los datos se limpian, enriquecen y transforman para servir como "única fuente de verdad". | Cualquier dato, que puede o no haber pasado por un proceso de limpieza y transformación (datos crudos). |
| Usuarios | Analistas de negocios. | Científicos de datos, ingenieros de datos y analistas de datos (cuando se utiliza información limpia). | Informes de Análisis, paneles y BI. Aprendizaje automático, análisis predictivo, descubrimiento de datos y perfilado. |

## Data Lakehouse (DLH)

**Características**:
- Combina las mejores características de **_DWH_** y **_DL_**.
- Permite el consumo rápido de datos almacenados para **_BI_**, informes, **_DS_** (Ciencia de Datos) y **_ML_** (Aprendizaje Automático).

![010-DLH 01](https://i.imgur.com/Vu3X94C.png)  
_DLH (imagen010)_

![011-DLH 02](https://i.imgur.com/a0LdoXX.png)  
_DLH (imagen011)_

![012-DLH 03](https://i.imgur.com/kNFF8J3.png)  
_DLH (imagen012)_

## Tipos de esquemas dimensionales

### El cubo como representación multidimensional de datos

El cubo es una representación multidimensional de los datos almacenados en un **_DWH_**. Las dimensiones están representadas por los bordes del cubo (por eso no debes pensar necesariamente en solo 3D), y los hechos se encuentran en cada celda donde se intersecan.

Por ejemplo, un cubo podría representar la tabla de hechos "Ventas" y las tablas de dimensión "Tiempo", "Producto" y "Ubicación" (de la tienda). El cubo está compuesto por celdas que contienen datos de ventas asociados con una combinación específica de valores de dimensión. En otras palabras, una celda puede contener la cantidad de ventas de un producto en un mes y región específicos.

![013-Representación de DWH como un cubo 01](https://i.imgur.com/8z58vEP.png)  
_DWH (imagen013)_

![014-Representación de DWH como un cubo 02](https://i.imgur.com/IOxAIGJ.png)  
_DWH (imagen014)_

### Esquema estrella

En las bases de datos utilizadas para **_DWH_**, un **_esquema estrella_** es un modelo de datos que tiene una tabla de hechos (tabla **_fact_**) que contiene los datos para el análisis, rodeada de tablas de dimensión (tablas **_dim_**). Este aspecto, una tabla de hechos más grande (o central) rodeada de radios o tablas más pequeñas, es lo que se asemeja a una estrella, dando a este tipo de construcción su nombre.

Las tablas de dimensión siempre tendrán una clave primaria simple (**_PK_**), mientras que en la tabla de hechos, la clave primaria (**_FK_**) estará compuesta por las **_PK_** de las tablas de dimensión. ([Wikipedia](https://es.wikipedia.org/wiki/Esquema_en_estrella))

**_Ventajas_**:
- Estructura simple y fácil de entender.
- Permite consultas rápidas y eficientes en conjuntos de datos grandes.
- Facilita el análisis multidimensional y la toma de decisiones informadas.
- Admite la adición de nuevas dimensiones sin alterar la tabla de hechos existente.

**_Desventajas_**:
- No es adecuado para bases de datos transaccionales.
- No es flexible para casos que requieren una mayor complejidad en la estructura de datos.
- La redundancia de datos puede ser un problema cuando las dimensiones se superponen.
- Requiere una planificación cuidadosa y un análisis previo para determinar la estructura óptima del modelo.

![015-Esquema estrella](https://i.imgur.com/rnSXwhh.png)  
_Esquema estrella (imagen015)_

### Esquema copo de nieve

En las bases de datos utilizadas en **_DWH_**, un **_esquema copo de nieve_** es una estructura algo más compleja que el **_esquema estrella_**. **_Ocurre cuando alguna de las dimensiones se implementa con más de una tabla de datos_**. **_El propósito es normalizar las tablas y, por lo tanto, reducir el espacio de almacenamiento al eliminar la redundancia de datos, pero tiene el contrapunto de generar un peor rendimiento_** debido a la necesidad de crear más tablas de dimensión y más relaciones entre las tablas (JOIN), lo que impacta directamente en el rendimiento. ([Wikipedia](https://es.wikipedia.org/wiki/Esquema_en_copo_de_nieve))

![016-Esquema copo de nieve](https://i.imgur.com/eF9u4nd.png)  
_Esquema copo de nieve (imagen016)_

![017-Tipos de esquema dimensional](https://i.imgur.com/z79R1m2.png)  
_Tipos de esquema dimensional (imagen017)_

**_Ventajas_**:
- Reduce la redundancia de datos y el espacio de almacenamiento.
- Permite un mejor control de la integridad de datos y la calidad de datos.
- Más adecuado para situaciones con múltiples relaciones entre tablas de dimensión.
- Proporciona mayor flexibilidad para agregar nuevas dimensiones.

**_Desventajas_**:
- Puede ser más complejo y difícil de entender que el **_esquema estrella_**.
- Puede requerir más tiempo y recursos para construir y mantener que el **_esquema estrella_**.
- Puede tener un mayor costo de procesamiento de consultas debido a la necesidad de uniones adicionales entre tablas normalizadas.

Un ejemplo sería Productos con Categorías. La dimensión Productos estaría relacionada con la dimensión Categorías.

**_¡Muy importante!_**: Los modelos nunca deben relacionarse a través de tablas de hechos; es decir, **nunca debemos relacionar directamente dos tablas de hechos**. Siempre, las relaciones entre modelos se llevan a cabo a través de las dimensiones que comparten.

## Lista de imágenes

- **img001**: Del curso "Data Warehousing y modelado OLAP" de Platzi.
- **img002**: Del curso "Data Warehousing y modelado OLAP" de Platzi.
- **img003**: Del curso "Data Warehousing y modelado OLAP" de Platzi.
- **img004**: De Wikipedia.
- **img005**: Del curso "Data Warehousing y modelado OLAP" de Platzi.
- **img006**: Del curso "Data Warehousing y modelado OLAP" de Platzi.
- **img007**: Del curso "Data Warehousing y modelado OLAP" de Platzi.
- **img008**: Del curso "Data Warehousing y modelado OLAP" de Platzi.
- **img009**: Del curso "Data Warehousing y modelado OLAP" de Platzi.
- **img010**: Del curso "Data Warehousing y modelado OLAP" de Platzi.
- **img011**: Del curso "Data Warehousing y modelado OLAP" de Platzi.
- **img012**: Del curso "Data Warehousing y modelado OLAP" de Platzi.
- **img013**: Del curso "Data Warehousing y modelado OLAP" de Platzi.
- **img014**: Del curso "Data Warehousing y modelado OLAP" de Platzi.
- **img015**: Del curso "Data Warehousing y modelado OLAP" de Platzi.
- **img016**: Del curso "Data Warehousing y modelado OLAP" de Platzi.
- **img017**: Del curso "Data Warehousing y modelado OLAP" de Platzi.

## Descargo de responsabilidad

**_Platzi_** (anteriormente Mejorando.la) es una plataforma de educación en línea de América Latina. Fue fundada en 2011 por el ingeniero colombiano **Freddy Vega** y el científico de la computación guatemalteco **Christian Van Der Henst**. [Wikipedia](https://es.wikipedia.org/wiki/Platzi)

## Uso de ChatGPT

**_Uso de ChatGPT 3.5_**

Este proyecto ha sido verificado para ortografía, sintaxis y contenido utilizando [**_ChatGPT 3.5_**](https://chat.openai.com/chat).

Referencia:  
[OpenAI. (2023). ChatGPT (versión del 19 de septiembre de 2023) \[Large Language Model\]](https://chat.openai.com/chat)