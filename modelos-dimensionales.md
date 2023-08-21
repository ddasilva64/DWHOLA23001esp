# Modelos dimensionales

## Data Warehouse, Data Lake y Data Lakehouse: ¿Cuál utilizar?

- **_DWH_**

Repositorio que alberga datos estructurados (columnas y filas), obtenidos a través de un proceso de ETL de sistemas transaccionales, .CSV, etc. 

Se puede considerar como la **_fuente única de verdad_**.

Usado por:
- **_Business Analysts_**.  

Usado en: 
- **_Reports_**, 
- **_BSC_** y 
- **_BI_**.  

<p><br></p>

![DWH](https://i.imgur.com/1NDE7J7.png)  
_DWH_  

<p><br></p>

- **_Data Lake (DL)_**

Repositorio de datos estructurados provinientes de ETL (tablas y vistas de BD, ficheros .CSV, etc.), semi-estructurados y no-estructurados (fotos, vídeos, páginas web, etc.). 

Los datos pueden ser facilmente almacenados y consultados.

Se puede considerar como una **_carpeta de datos raw_**.

Usado por:
- **_Data Scientists_**, 
- **_Data Engineers_**, y 
- **_Data Analysts_**.

Usado en:
- **_ML_**, 
- **_Predictive Analytics_**, 
- **_Data Discovery_** y 
- **_Profiling_**.

<p><br></p>

![DL](https://i.imgur.com/RtAStZl.png)  
_DL_

<p><br></p>

## Data Warehouse vs. Data Lake

| Características | DWH | DL |
| :-------------- | :-- | :- |
| Data            | Optimizado para analizar datos relacionados de fuentes como BD transaccionales, operativas y aplicaciones de negocio. | Datos no relacionados de fuentes como web sites, redes sociales, dispositivos IoT, aplicaciones móviles. |
| Schema          | La estructura de datos se define antes de la implementación para optimizar las consultas (schema-on-write). | Almacena información sin la definición de una estructura de datos. Permite implementar sin conocer aún las preguntas de negocio (schema-on-read). |
| Data Quality    | Los datos se limpian, enriquecen y transforman para que puedan actuar como la "única fuente de verdad". | Cualquier dato que pudo o no pasar por un proceso de limpieza y transformación ( raw data). |
| Users           | Analistas de negocio. | Científicos de datos, ingenieros de datos, y analistas de datos (usando información limpia). | Analytics Reportes, tableros de control y BI. Machine Learning, análisis predictivos, data discovery y profiling. | 

<p><br></p>

- **_Data Lakehouse (DLH)_**

Es lo mejor del **_DWH_** y del **_DL_**. 

Permite consumir los datos almacenados rápidamente, para **_BI_**, informes, **_DS_** y **_ML_**.

<p><br></p>

![DLH 01](https://i.imgur.com/Vu3X94C.png)  
_DLH 01_  

<p><br></p>

![DLH 02](https://i.imgur.com/a0LdoXX.png)  
_DLH 02_  

<p><br></p>

![DLH 03](https://i.imgur.com/kNFF8J3.png)  
_DLH 03_  

<p><br></p>

## Dim tables - [Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_dimensi%C3%B3n) -

En un **_DWH_** o un sistema **_OLAP_**, la construcción de **_Cubos OLAP_** requiere de una **_fact table_** y varias **_dim tables_**, estas acompañan a la **_fact table_** y determinan los parámetros (dimensiones) de los que dependen los hechos registrados en la **_fact table_**.

### Introducción

En la construcción de**_ cubos OLAP_**, las **_dim tables_** son elementos que contienen atributos (o campos) que se utilizan para restringir y agrupar los datos almacenados en una **_fact table_** cuando se realizan consultas sobre dicho datos en un entorno **_DWH o DM_**.  

Estos datos sobre dimensiones son parámetros de los que dependen otros datos que serán objeto de estudio y análisis y que están contenidos en la **_fact table_**. Las **_dim tables_** ayudan a realizar ese estudio/análisis aportando información sobre los datos de la **_fact table_**, por lo que puede decirse que en un **_cubo OLAP_**, la **_fact table_** contiene los datos de interés y las**_dim tables_** contienen metadatos sobre dichos hechos.

### Granularidad de dimensión y jerarquías

Cada dimensión puede referirse a conceptos como Tiempo, Productos, Clientes, Zona geográfica, etc. Ahora bien, cada dimensión puede estar medida de diferentes maneras según la granularidad deseada, por ejemplo, para la dimensión Zona geográfica podríamos considerar 'localidades', 'provincias', 'regiones', 'países' o 'continentes'.  

![Granularidad de Zona geográfica](https://i.imgur.com/vDzQjC2.png)  
_Granularidad de Zona geográfica_  

La unidad de medida (por localidades, provincias, etc.) determinará esa granularidad, cuanto más pequeña sea esta unidad de medida más fina será esta granularidad (grano fino); si las unidades de medida son mayores, entonces hablaremos de granularidad gruesa (grano grueso).  

En muchas ocasiones interesa disponer de los datos a varios niveles de granularidad, es decir, es importante para el negocio poder consultar los datos (siguiendo el ejemplo de las zonas) por localidades, provincias, etc., en estos casos se crea una jerarquía con la dimensión, ya que tenemos varios niveles de asociación de los datos (con otras dimensiones como el tiempo, se podrían crear niveles jerárquicos del tipo 'días', 'semanas', 'meses'...).  

![Detalle dim table](https://i.imgur.com/gHgpzeR.png)  
_Detalle dim table_  

Cuando las **_dim tables_** asociadas a una **_fact table_** no reflejan ninguna jerarquía (por ejemplo: Las zonas siempre son 'provincias' y solo provincias, el tiempo se mide en 'días' y solo en días, etc.) el cubo resultante tendrá forma de estrella, es decir, una **_fact table_** central rodeada de tantas tablas como dimensiones, y solo habrá, además de la **_fact table_**, una tabla por cada dimensión.

![Star schema de 5 dimensiones](https://i.imgur.com/4HPriuo.png)  
_Star schema de 5 dimensiones_  

Cuando una o varias de las dimensiones del cubo refleja algún tipo de jerarquía existen dos planteamientos con respecto a la forma que deben ser diseñadas las tablas de dimensión. El primero consiste en reflejar todos los niveles jerárquicos de una dimensión dentro de una única tabla, en este caso también tendríamos un esquema en estrella como el que se ha descrito anteriormente, es decir, en el modelo tendríamos tablas no normalizadas, para mejorar el rendimiento.  

El otro planteamiento consiste en aplicar a las dimensiones las reglas de normalización de las **_RDB_**. Estas normas están ideadas para evitar redundancias en los datos aumentando el número de tablas, de esta forma se consigue almacenar la información en menos espacio. Este diseño da como resultado en esquema en copo de nieve. Este modo de organizar las dimensiones de un **_cubo OLAP_** tiene un inconveniente respecto al modelo en estrella que no compensa el ahorro de espacio de almacenamiento. En las aplicaciones OLAP el recurso crítico, no es tanto el espacio para almacenamiento como el tiempo de respuesta del sistema ante consultas del usuario, y está constatado que los modelos en copo de nieve tienen un tiempo de respuesta mayor que los modelos en estrella.

![Snowflake schema de 5 dimensiones](https://i.imgur.com/1Ata1st.png)  
_Snowflake schema de 5 dimensiones_   

### La dimensión Tiempo  

En cualquier **_DWH_** se pueden encontrar varios cubos con sus **_fact tables_** repletas de registros sobre alguna variable de interés para el negocio que debe ser estudiada. Como ya se ha comentado, cada **_fact table_** estará rodeada de varias **_dim tables_**, según que parámetros sirvan mejor para realizar el análisis de los hechos que se quieren estudiar. Un parámetro que casi con toda probabilidad será común a todos los cubos es el Tiempo, ya que lo habitual es almacenar los hechos conforme van ocurriendo a lo largo del tiempo, obteniéndose así una serie temporal de la variable a estudiar.

Dado que el Tiempo es una dimensión presente en prácticamente cualquier cubo de un sistema **_OLAP_** merece una atención especial. Al diseñar la dimensión Tiempo (tanto para un star schema como para un snowflake schema) hay que prestar especial cuidado, ya que puede hacerse de varias maneras y no todas son igualmente eficientes. La forma más común de diseñar esta tabla es poniendo como **_PK_** de la tabla la fecha o fecha/hora (tabla de tiempos 1). Este diseño no es de los más recomendables, ya que a la mayoría de los sistemas de gestión de bases de datos les resulta más costoso hacer búsquedas sobre campos de tipo "date" o "datetime", estos costes se reducen si el campo clave es de tipo entero, además, un dato entero siempre ocupa menos espacio que un dato de tipo fecha (el campo clave se repetirá en millones de registros en la tabla de hechos y eso puede ser mucho espacio), por lo que se mejorará el diseño de la tabla de tiempos si se utiliza un campo "TiempoID" de tipo entero como clave principal (tabla de tiempos 2).

Tabla de tiempos (1)

| Fecha (PK) | datetime |
| :--------- | :------: |
| Año        | char(4)  |
| Trimestre  | char(6)  |
| Mes        | char(10) |

Tabla de tiempos (2)  

| TiempoID (PK) | integer  |
| :------------ | :------: |
| Fecha         | datetime |
| Año           | char(4)  | 
| Trimestre     | char(6)  |
| Mes           | char(10) |

A la hora de rellenar la tabla de Tiempos, si se ha optado por un campo de tipo entero para la clave, hay dos opciones: la que quizá sea más inmediata consiste en asignar valores numéricos consecutivos (1, 2, 3, 4...) para los diferentes valores de fechas. La otra opción consistiría en asignar valores numéricos del tipo "yyyymmdd", es decir que los cuatro primeros dígitos del valor del campo indiquen el año de la fecha, los dos siguientes el mes y los dos últimos el día. Este segundo modo aporta una cierta ventaja sobre el anterior, ya que de esta forma se consigue que el dato numérico en sí, aporte por sí solo la información de a qué fecha se refiere. Por ejemplo, si en la tabla de hechos se encuentra el valor 20040723, sabremos que se refiere al 23 de julio de 2004; en cambio, con el primer método, se podrían encontrar valores como 8456456, y para saber a qué fecha se refiere este valor se tendría que hacer una consulta sobre la tabla de tiempos.

Además del campo clave TiempoID, la **_fact table_** debe contener otros campos que también es importante considerar. Estos campos serían:
- Un campo "año".- Que contedría valores como '2002', 2003, '2004'...
- Un campo "mes".- Aquí se pueden poner los valores 'enero', 'febrero'... (o de forma abreviada: 'Ene', 'Feb'...). Aunque esto no es incorrecto, se puede mejorar si el nombre del mes va acompañado con el año al que pertenece. Por ejemplo: '2004 enero', '2004 febrero'... De esta forma se optimiza la búsqueda de los valores de un mes en concreto, ya que con el primer método, si se buscan los valores pertenecientes al mes de "enero de 2003", toda esa información está contenida en un solo campo, el "mes", y no haría falta consultar también el campo año.
- Un campo "mesID".- Este campo tendría que ser de tipo entero y serviría para almacenar valores del tipo 200601 (para '2006 enero') o 200602 (para '2006 febrero'). De esta forma es posible realizar ordenaciones y agrupaciones por meses.

De forma análoga a como se ha hecho con el campo mes, se podrían añadir más campos como "Época del año", "Trimestre", "Quincena", "Semana" de tipo texto para poder visualizarlos, y sus análogos de tipo entero "Época del año_ID", "TrimestreID", "QuincenaID", "SemanaID" para poder realizar agrupaciones y ordenaciones. En general se puede añadir un campo por cada nivel de granularidad deseado.

Otro campo especial que se puede añadir es el "Día de la semana" ('lunes', 'martes'...). Este campo se suele añadir para poder hacer estudios sobre el comportamiento de los días de la semana en general (no del primer lunes del mes de enero de un año concreto, este tipo de estudio no suele tener interés), y por esta razón este campo no necesita ir acompañado del mes o del año como los campos anteriores. También se puede añadir su campo dual "ID" de tipo entero para poder ordenar y agrupar si fuera necesario.

Con los añadidos descritos podríamos tener una tabla de Tiempos como la de la figura "Tabla de tiempos (3)". Esta sería válida para un diseño en estrella. Para un diseño en copo de nieve habría que desglosar la tabla de tiempos en tantas tablas como niveles jerárquicos contenga. Obsérvese que los campos de tipo "ID" son todos de tipo entero, ya que será sobre estos campos sobre los que se realizarán la mayoría de las operaciones y estas se realizarán más eficientemente sobre datos enteros.

Tabla de tiempos (3)

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

## Tipos de esquemas dimensionales

### Modelado dimensional

Modelado dimensional (DM en inglés) nombra a un conjunto de técnicas y conceptos utilizados en el diseño de **_DWH_**. Se considera que es diferente del Modelo **_ER_**. El modelado de dimensiones no implica necesariamente una **_RDB_**, el mismo enfoque de modelado, a nivel lógico, se puede utilizar para cualquier forma física, tal como archivos de BD multidimensional o planas. (Fuente: [Wikipedia](https://es.wikipedia.org/wiki/Modelado_dimensional))

### Proceso de DM
Para construir el esquema, en el **_DM_** se:
1. Escoge el proceso de negocio.
2. Declara el "grain" (granularidad).
3. Identifican las dimensiones (atributos).
4. Identifican los hechos (métricas).

### Dimensiones

Almacenan los atributos que nos permitirán tener perspectivas diferentes de los hechos.  
Tendrán las PK de la relación con las tablas de hechos.  
Ejemplos:
- Productos.
- Subcategorías.
- Clientes.
- Empleados.

### Hechos

Almacenan las métricas que queremos consultar con las perspectivas que nos proporcionan las dimensiones.  
Tendrán las FK de la relación con las tablas de dimensiones.  
Ejemplos:
- Ventas.
- Productividad.

<p><br></p>

![DM](https://i.imgur.com/6KaLmDY.png)  
_DM_  

<p><br></p>

### El cubo como representación multidimensional de los datos

El cubo es una representación multidimensional de los datos almacenados en un **_DWH_**. Las dimensiones estarían representadas por las aristas del cubo (con lo cual no se debe, necesariamente, pensar en solo 3D) y los hechos sería cada celda donde se intreseccionan.

Por ejemplo, un cubo podría representar a la tabla de hechos “Ventas” y a las tablas de dimensiones “Tiempo”, “Producto” y “Ubicación” (de la tienda). El cubo se compone de celdas que contienen los datos de ventas asociados a una combinación específica de valores de las dimensiones. Es decir, una celda puede contener la cantidad de ventas de un producto en un mes y una región específica. 

<p><br></p>

![Representación DWH como un cubo 01](https://i.imgur.com/8z58vEP.png)  
_Representación DWH como un cubo 01_  

<p><br></p>

![Representación DWH como un cubo 02](https://i.imgur.com/IOxAIGJ.png)  
_Representación DWH como un cubo 02_  

<p><br></p>

### Star schema

En las BD usadas para **_DWH_**, un esquema en estrella es un modelo de datos que tiene una tabla de hechos (**_fact_** table) que contiene los datos para el análisis, rodeada de las tablas de dimensiones (**_dim_** tables). Este aspecto, de tabla de hechos (o central) más grande rodeada de radios o tablas más pequeñas es lo que asemeja a una estrella, dándole nombre a este tipo de construcciones.  

Las tablas de dimensiones tendrán siempre una **_PK_** simple, mientras que en la tabla de hechos, la clave principal (**_FK_**) estará compuesta por las **_PK_** de las tablas dimensionales. (Fuente: [Wikipedia](https://es.wikipedia.org/wiki/Esquema_en_estrella))  

**_Ventajas_**:
- Estructura sencilla y fácil de entender.  
- Permite consultas rápidas y eficientes a grandes conjuntos de datos.  
- Facilita el análisis multidimensional y la toma de decisiones informadas.  
- dmite la adición de nuevas dimensiones sin alterar la tabla de hechos existente.  

**_Desventajas_**:
- No es adecuado para bases de datos transaccionales.  
- No es flexible para casos en los que se requiere una complejidad mayor en la estructura de datos.
- La redundancia de datos puede ser un problema en casos donde las dimensiones se solapan.
- Requiere una planificación cuidadosa y análisis previo para determinar la estructura óptima del modelo.

<p><br></p>

![Star schema](https://i.imgur.com/rnSXwhh.png)  
_Star schema_  

<p><br></p>

### Snowflake schema

En las BD utilizadas en **_DWH_**, un esquema en copo de nieve es una estructura algo más compleja que el esquema en estrella. **_Se da cuando alguna de las dimensiones se implementa con más de una tabla de datos_**. **_La finalidad es normalizar las tablas y así reducir el espacio de almacenamiento al eliminar la redundancia de datos; pero tiene la contrapartida de generar peores rendimientos_** al tener que crear más tablas de dimensiones y más relaciones entre las tablas (JOINs) lo que tiene un impacto directo sobre el rendimiento. (Fuente: [Wikipedia](https://es.wikipedia.org/wiki/Esquema_en_copo_de_nieve))  

<p><br></p>

![Snowflake schema](https://i.imgur.com/eF9u4nd.png)  
_Snowflake schema_  

<p><br></p>

![Tipos de esquemas dimensionales](https://i.imgur.com/z79R1m2.png)  
_Tipos de esquemas dimensionales_

<p><br></p>

**_Ventajas_**:
- Reduce la redundancia de datos y el espacio de almacenamiento.  
- Permite un mejor control de la integridad de los datos y la calidad de los datos.  
- Es más adecuado para situaciones en las que existen varias relaciones entre las tablas de dimensiones.  
- Proporciona una mayor flexibilidad en la adición de nuevas dimensiones.  

**_Desventajas_**:
- Puede ser más complejo y difícil de entender que el modelo estrella.  
- Puede requerir más tiempo y recursos para construir y mantener que el modelo estrella.  
- Puede tener un mayor costo de procesamiento de consultas debido a la necesidad de realizar uniones adicionales entre las tablas normalizadas. 

Un ejemplo sería Productos con Categorías. La dimensión de Productos se relacionaría con la de Categorías.

**_¡Muy importante!_**: Nunca se deben relacionar modelos a través de tablas de hechos, es decir, nunca relacionaremos dos tablas de hechos. Siempre, las relaciones entre modelos se efectúan a través de las dimensiones que comparten.

## Dimensiones lentamente cambiantes o Slowly Changing Dimensions (SCD)

Una **_SCD_** en **_DWH_** es una dimensión que contiene datos relativamente estáticos que pueden cambiar de forma lenta pero impredecible, en lugar de seguir un programa regular. Algunos ejemplos de dimensiones típicas que cambian lentamente son entidades como nombres de Ubicaciones geográficas, Clientes o Productos. (Fuente [Wikipedia](https://en.wikipedia.org/wiki/Slowly_changing_dimension))  

<p><br></p>

![Dimensión](https://i.imgur.com/kXDzc6e.png)  
_Dimensión_  

<p><br></p>

### Atributos

- **_Jerárquicos_**: Permiten ir de lo general a lo particular, o consolidar y desagregar. Por ejemplo: país.
- **_Descriptivos_**: Información relevante, que es netamente descriptiva. Por ejemplo: dirección, teléfono, talla, clima.
- **_De control_**: Datos de auditoría, lo cuales no pertenecen al conocimiento del negocio. Por ejemplo: log con la fecha de grabación del registro.

### Tipos de SDC

- **_Tipo 1_**: Sobreescribir el atributo actualizado. Es decir, no guarda historia.  
- **_Tipo 2_**: Agrega un nuevo registro con el cambio. Es decir, guarda historia.  
- **_Tipo 3_**: Agrega un nuevo atributo “anterior”. Es decir, guarda historia, pero agregando una nueva columna por el atributo anterior y el modificado.

## SCD-1

Recordemos que, en una base de datos dimensional, las SCD (Slowly Changing Dimensions) son aquellas dimensiones que cambian con el tiempo, pero no necesariamente de manera constante o predecible. Por ejemplo, en una tabla de datos de clientes, la dirección del cliente puede cambiar con el tiempo, pero no todos los clientes cambian su dirección al mismo ritmo. Algunos clientes pueden cambiar su dirección cada mes, mientras que otros pueden mantener la misma dirección durante años.

Existen diferentes tipos de SDC, que se clasifican según la forma en que se manejan los cambios en las dimensiones. Por ejemplo, el tipo 1 significa que se sobrescribe la información antigua con la nueva información cada vez que se produce un cambio.

El manejo adecuado de las Slowly Changing Dimensions es importante para mantener la precisión y la integridad de los datos dimensionales en una base de datos, ya que permite a los usuarios realizar análisis históricos y comparar datos a lo largo del tiempo.


Ejemplo:

**_Matriculación_**

Tabla Transaccional

| **Id_Estudiante** | **Nombre Completo** | **Facultad** |
| :---------------: | :------------------ | :----------: |
| EST12345          | Pepito Perez        | Marketing    |

Tabla de Dimensión (ahora Id_Estudiante es una **_surrogate key_**, que tiene como objetivo un mejor rendimiento de la dimensión por clave numérica) 

| **Id_Estudiante** | **Cod_Estudiante** | Nombre Completo | **Facultad** |
| :---------------: | :----------------: | :-------------- | :----------: |
| 1                 | EST12345           | Pepito Perez    | Marketing    |

**_Cambio de facultad a Ingeniería _** 

Tabla Transaccional

| **Id_Estudiante** | **Nombre Completo** | **Facultad** |
| :---------------: | :------------------ | :----------: |
| EST12345          | Pepito Perez        | Ingeniería   |


| **Id_Estudiante** | **Cod_Estudiante** | Nombre Completo | **Facultad** |
| :---------------: | :----------------: | :-------------- | :----------: |
| 1                 | EST12345           | Pepito Perez    | Ingeniería   |

No guarda historia y hemos creado una nueva clave más eficiente que es sinónima de la que teníamos en la tabla transaccional.  
Es adecuada cuando la información histórica no es importante o cuando se puede recuperar de otras fuentes.

## SCD-2

Ejemplo:

**_Matriculación_**

Tabla Transaccional

| **Id_Estudiante** | **Nombre Completo** | **Facultad** |
| :---------------: | :------------------ | :----------: |
| EST12345          | Pepito Perez        | Marketing    |

Tabla de Dimensión (ahora Id_Estudiante es una **_surrogate key_**, que tiene como objetivo un mejor rendimiento de la dimensión por clave numérica) 

| **Id_Estudiante** | **Cod_Estudiante** | Nombre Completo | **Facultad** | **Start_date** | **End_date** |
| :---------------: | :----------------: | :-------------- | :----------: | :------------: | :----------: |
| 1                 | EST12345           | Pepito Perez    | Marketing    | 01/01/2020     |              |

**_Cambio de facultad a Ingeniería _** 

Tabla Transaccional

| **Id_Estudiante** | **Nombre Completo** | **Facultad** |
| :---------------: | :------------------ | :----------: |
| EST12345          | Pepito Perez        | Ingeniería   |


| **Id_Estudiante** | **Cod_Estudiante** | Nombre Completo | **Facultad** | **Start_date** | **End_date** |
| :---------------: | :----------------: | :-------------- | :----------: | :------------: | :----------: |
| 1                 | EST12345           | Pepito Perez    | Marketing    | 01/01/2020     | 01/01/2020   |
| 2                 | EST12345           | Pepito Perez    | Ingeniería   | 02/01/2020     |              |

Las **_SCD-2_** se utilizan comúnmente cuando se requiere análisis histórico y la dimensión cambia con relativa poca frecuencia.

## SCD-3

Ejemplo:

**_Matriculación_**

Tabla Transaccional

| **Id_Estudiante** | **Nombre Completo** | **Facultad** |
| :---------------: | :------------------ | :----------: |
| EST12345          | Pepito Perez        | Marketing    |

Tabla de Dimensión (ahora Id_Estudiante es una **_surrogate key_**, que tiene como objetivo un mejor rendimiento de la dimensión por clave numérica) 

| **Id_Estudiante** | **Cod_Estudiante** | Nombre Completo | **Facultad_old** | **Facultad_new** |
| :---------------: | :----------------: | :-------------- | :--------------: | :-------------:  |
| 1                 | EST12345           | Pepito Perez    |                  |  Marketing       |

**_Cambio de facultad a Ingeniería _** 

Tabla Transaccional

| **Id_Estudiante** | **Nombre Completo** | **Facultad** |
| :---------------: | :------------------ | :----------: |
| EST12345          | Pepito Perez        | Ingeniería   |


| **Id_Estudiante** | **Cod_Estudiante** | Nombre Completo | **Facultad_old** | **Facultad_new** |
| :---------------: | :----------------: | :-------------- | :--------------: | :-------------:  |
| 1                 | EST12345           | Pepito Perez    | Marketing        |  Ingeniería      |

Los Tipo 3 SCD se utilizan cuando es importante realizar un seguimiento de cambios específicos de atributos mientras se mantiene la simplicidad en el modelo de datos.


Reto: Combinar SCD-2 y 3

**_Matriculación en la facultad de Astrofísica azul_**

Tabla Transaccional

| **Id_Estudiante** | **Nombre Completo** | **Facultad**     |
| :---------------: | :------------------ | :--------------: |
| EST12345          | Daniel da Silva     | Astrofísica azul |

Tabla de Dimensión (después de ETL) 

Nos damos cuenta, en el ETL, que Astrofísica azul es una estupidez. Todo el mundo sabe que es verde.

| **Id_Estudiante** | **Cod_Estudiante** | Nombre Completo | **Facultad_old** | **Facultad_new** | **Start_date** | **End_date** | **Facultad_actual** |
| :---------------: | :----------------: | :-------------- | :--------------: | :-------------:  | :------------: | :----------: | :----------------:  |
| 1                 | EST12345           | Daniel da Silva |                  | Astrofísica verde| 18/08/2023     |              | Y                   |

**_Cambio de facultad a Clima salado_** 

Tabla Transaccional

| **Id_Estudiante** | **Nombre Completo** | **Facultad** |
| :---------------: | :------------------ | :----------: |
| EST12345          | Daniel da Silva     | Clima salado |

Tabla de Dimensión (después de ETL) 

| **Id_Estudiante** | **Cod_Estudiante** | Nombre Completo | **Facultad_old** | **Facultad_new** | **Start_date** | **End_date** | **Facultad_actual** |
| :---------------: | :----------------: | :-------------- | :--------------: | :-------------:  | :------------: | :----------: | :----------------:  |
| 1                 | EST12345           | Daniel da Silva |                  | Astrofísica verde| 18/08/2023     | 19/08/2023   | N                   |
| 2                 | EST12345           | Daniel da Silva | Astrofísica verde| Clima salado     | 19/08/2023     |              | Y                   |

**_Cambio de facultad, de nuevo, a Astrofísica verde_** 

Tabla Transaccional

| **Id_Estudiante** | **Nombre Completo** | **Facultad**      |
| :---------------: | :------------------ | :---------------: |
| EST12345          | Daniel da Silva     | Astrofísica verde |

Tabla de Dimensión (después de ETL) 

| **Id_Estudiante** | **Cod_Estudiante** | Nombre Completo | **Facultad_old** | **Facultad_new** | **Start_date** | **End_date** | **Facultad_actual** |
| :---------------: | :----------------: | :-------------- | :--------------: | :-------------:  | :------------: | :----------: | :----------------:  |
| 1                 | EST12345           | Daniel da Silva |                  | Astrofísica verde| 18/08/2023     | 19/08/2023   | N                   |
| 2                 | EST12345           | Daniel da Silva | Astrofísica verde| Clima salado     | 19/08/2023     | 20/08/2023   | N                   |
| 3                 | EST12345           | Daniel da Silva | Clima salado     | Astrofísica verde| 20/08/2023     |              | Y                   |

La elección del tipo de dimensión cambiante lentamente a utilizar depende de los requisitos específicos del almacén de datos y las necesidades de análisis. Es importante considerar factores como la importancia de los datos históricos, la frecuencia de los cambios en la dimensión y las implicaciones de almacenamiento y rendimiento de cada enfoque.

Las SCD son un aspecto crucial de los almacenes de datos, ya que permiten representar datos a lo largo del tiempo, lo que permite un análisis e informes históricos precisos.

**_Tipo 1_**: Lentamente cambiante, un registro en una fuente de datos que en algun momento cambia y se reemplaza no guarda historicos. (reemplaza el valor inicial).  
**_Tipo 2_**: Lentamente cambiante, un registro que guarda la historia, diciendo que en algun momento fue algo y cambio despues.  
**_Tipo 3_**: Almacena infomracion historica pero almacenado en una columna nueva indicando el valor que tenia y en la columna nueva el nuevo valor asociado.  

## Tabla de hechos (fact)

<p><br></p>

![Fact table](https://i.imgur.com/nP9xodv.png)  
_Fact table_  

<p><br></p>

![Fact table Ventas](https://i.imgur.com/nAjHiaZ.png)  
_Fact table Ventas_  

La tabla central (Ventas) es la **_fact table_** de un **_star schema_**, las cinco tablas que la rodean (Producto, Tiempo, Almacén, Promoción y Cliente) son las cinco dimensiones de que consta Ventas, en dicha tabla se almacenan, en este caso, las unidades vendidas y el precio obtenido por dichas ventas, estos son los hechos o medidas de negocio almacenados. (Fuente [Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_hechos)).  

### Las medidas o métricas del negocio (hechos) - Fuente [Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_hechos) -

Las medidas más útiles para incluir en una **_fact table_** son los aditivos, es decir, aquellas medidas que pueden ser sumadas como por ejemplo la cantidad de producto vendido, los costes de producción o el dinero obtenido por las ventas; son medidas numéricas que pueden calcularse con la suma de varias cantidades de la tabla. En consecuencia, por lo general **_los hechos a almacenar en una fact table van a ser casi siempre valores numéricos, enteros o reales_**.   

### Cardinalidad de la tabla de hechos -Fuente [Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_hechos) -

Las **_fact tables_** pueden contener un gran número de filas, a veces cientos de millones de registros cuando contienen uno o más años de la historia de una organización, esta cardinalidad estará acotada superiormente por la cardinalidad de las **_dim tables_**, Por ejemplo, si se tiene una **_fact table "FT"_** de tres dimensiones D1, D2 y D3, el número máximo de elementos que tendrá la tabla de hechos FT será:

$Card(FT) = Card(D1) * Card(D2) * Card(D3)$

Donde $Card(x)$ es la cardinalidad de la tabla X  

**_Nota muy importante_**: Esto hace que siempre se deba construir primero las **_dim tables_** que la **_fact table_** en el modelo.

Naturalmente, estas cardinalidades no son fijas, ya que, por ejemplo, si una de las dimensiones se refiere a los Clientes, cada vez que se dé de alta un nuevo cliente se estará aumentando la cardinalidad de la **_fact table_**. Una de las dimensiones suele ser el Tiempo, éste puede medirse de muy distintas formas (por horas, días, semanas, ...), pero lo cierto es que transcurre continuamente, y para que el sistema funcione se deben añadir registros periódicamente a la tabla de esta dimensión (tabla de tiempos) y esto también produce un aumento de la cardinalidad de la **_fact table_**, esta es la principal causa de que las **_fact tables_** lleguen a tener una cantidad de registros del orden de millones de elementos.

### Granularidad (grain) - Fuente [Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_hechos) -  

Una característica importante que define a una **_fact table_** es el nivel de granularidad de los datos que en ella se almacenan, entendiéndose por **_grain_** el nivel de detalle de dichos datos, es decir, el **_grain_** de la **_fact table_** representa el nivel más atómico por el cual se definen los datos. Por ejemplo, no es lo mismo contar el tiempo por horas (grano fino) que por semanas (grano grueso); o en el caso de los Productos, se puede considerar cada variante de un mismo artículo como un producto (por ejemplo, en una empresa textil, cada talla y color de pantalón podría ser un producto) o agrupar todos los artículos de una misma familia considerándolos como un único producto (por ejemplo, el producto pantalón genérico).  

Como se puede observar, la granularidad afecta a la cardinalidad, tanto de las dimensiones como de la tabla de hechos, a mayor granularidad (grano más fino) mayor será el número de registros final de la **_fact table_**.

> A mayor **_grain_** de las **_dim tables_** mayor cardinalidad de la **_fact table_**.

Cuando el **_grain_** es mayor, es frecuente que se desee disponer de subtotales parciales, es decir, si tenemos una **_fact table_** con las ventas por días, podría interesar disponer de los totales semanales o mensuales, estos datos se pueden calcular haciendo sumas parciales, pero es frecuente añadir a la **_fact table_** registros donde se almacenan dichos cálculos para no tener que repetirlos cada vez que se requieran y mejorar así el rendimiento de la aplicación. En este caso se dispondrá en la misma **_fact table_** de grano fino y de grano más grueso aumentando aún más la cardinalidad de la tabla.

#### Agregación - Fuente [Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_hechos) -  

La agregación es un proceso de cálculo por el cual se resumen los datos de los registros de detalle. Esta operación consiste normalmente en el cálculo de totales dando lugar a medidas de grano grueso. Cuando se resumen los datos, el detalle ya no está directamente disponible para el analista, ya que este se elimina de la **_fact table_**.

Esta operación se realiza típicamente con los datos más antiguos con la finalidad de seguir disponiendo de dicha información (aunque sea resumida) para poder eliminar registros obsoletos de la **_fact table_** para liberar espacio.

### Tipos de datos adecuados - Fuente [Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_hechos) -  

Como ya se ha comentado, es normal que las **_fact tables_** almacenen muchos millones de registros, por esta razón **_es muy importante que no se despilfarre memoria, hay que procurar utilizar los tipos de datos adecuados, si una medida a almacenar puede guardarse en un campo de tipo entero, no debemos definir ese campo como de tipo entero largo o como tipo real_**. Del mismo modo, si una magnitud necesita decimales, si las características de ésta lo permiten, será mejor utilizar un tipo real simple que un tipo real de doble precisión. Nótese que elegir uno u otro de estos campos, en principio sólo supondría una diferencia de unos pocos bytes en un registro, pero dado que en una **_fact table_** estamos hablando de cientos de millones de registros, en realidad, esa diferencia no es despreciable (5 bytes x 200 millones de registros = 1GB de memoria).

[Design Approach to Handle Late Arriving Dimensions and Late Arriving Facts](https://www.disoln.org/2013/12/Design-Approach-to-Handle-Late-Arriving-Dimensions-and-Late-Arriving-Facts.html)

## Configuración de herramientas para DWH y ETL

En el curso se usará **_Postgre SQL_** con la BD **_Adventureworks_**. Esta será la BD transaccional y la fuente de información para llevar al **_DWH_**.  
Yo, con vistas a mi proyecto, continuaré utilizando **_AdventureWorksDW_**. En mi caso la BD transaccional está, por coherencia con proyectos anteriores en **_SQL Server_** y el **_DWH_** en **_Postgre SQL_**.

Para seguir las instrucciones del curso ejecutamos las siguientes instrucciones de configuración:

### Ruby

#### Instalación de Ruby en Ubuntu o WSL con Ubuntu

1. Abrimos la terminal de Ubuntu  
2. Ejecutamos el siguiente comando en la terminal para actualizar la lista de paquetes disponibles:

````bash
sudo apt-get update
````

3. Una vez actualizada la lista de paquetes, instalamos Ruby ejecutando el siguiente comando en la terminal:  

````bash
sudo apt-get install ruby-full
````

4. Verificamos que Ruby se haya instalado correctamente ejecutando ruby -v en la terminal.  

#### Instalación de Ruby en Windows  

1. Descargamos el instalador de Ruby desde la página oficial de Ruby para Windows: https://rubyinstaller.org/downloads/  
2. Seleccionamos la versión de Ruby que deseas instalar.
3. Ejecutamos el instalador y sigue las instrucciones del asistente de instalación.  
4. Una vez completada la instalación, abre la línea de comandos de Windows (cmd.exe) y escribe ruby -v para verificar que la instalación se haya realizado correctamente.  

#### Instalación de Ruby en macOS  

1. Abre la terminal de macOS.  
2. Instala Homebrew ejecutando el siguiente comando en la terminal:  

````bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
````

3. Una vez instalado Homebrew, ejecuta el siguiente comando en la terminal para instalar Ruby:  

````bash
brew install ruby
````

4. Verificamos que Ruby se haya instalado correctamente ejecutando ruby -v en la terminal.  

Con estos pasos ya hemos instalado Ruby.

### PostgreSQL y pgAdmin o DBeaver

Estas herramientas ya deberíamos tenerla instaladas.

**_Nota_**: si usas **_Windows_** debemos recordar asignar las variables de entorno para **_Postgre SQL_**.

![Variables de entorno](https://i.imgur.com/QbSfib5.png)  
_Variables de entorno_  

### Descarga y configuración de la BD AdventureWorks

1. Descargamos el repositorio en https://github.com/lorint/AdventureWorks-for-Postgres  

Ejecutamos el siguiente comando de Git:

````bash
git clone https://github.com/lorint/AdventureWorks-for-Postgres.git
````

Este repositorio contiene los archivos para crear las tablas y vistas de la base de datos.

2. Descargamos [Adventure Works 2014 OLTP Script](https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorks-oltp-install-script.zip).

Contiene los archivos para llenar las tablas de la base de datos.

3. Copiamos y pegamos el archivo AdventureWorks-oltp-install-script.zip en el directorio AdventureWorks-for-Postgres.

4. En nuestra terminal nos ubicamos en el directorio AdventureWorks-for-Postgres y descomprimimos AdventureWorks-oltp-install-script.zip:

````bash
cd AdventureWorks-for-Postgres/
unzip AdventureWorks-oltp-install-script.zip
````

5. En la terminal, ubicándote en el directorio AdventureWorks-for-Postgres, ejecutamos el siguiente comando para convertir los archivos csv:

````bash
ruby update_csvs.rb
````

6. Activamos la conexión con postgresql:  

````bash
sudo service postgresql start
````

7. Creamos la BD con el siguiente comando de PostgreSQL:

````bash
psql -c "CREATE DATABASE \"Adventureworks\";"
````

o

````bash
psql -c "CREATE DATABASE \"Adventureworks\";" -U postgres -h localhost
````

8. Ejecutamos el script que llena las tablas de la BD:

````bash
psql -d Adventureworks < install.sql
````

o

````bash
psql -d Adventureworks < install.sql -U postgres -h localhost
````

9. Conectamos nuestra BD en DBeaver o pgAdmin.

    1. Abrimos DBeaver o pgAdmin.

    2. Seleccionamos la opción para crear una nueva conexión.

    3. Seleccionamos PostgreSQL en la lista de BD.

    4. Ingresamos la información de conexión necesaria en la pestaña.

        - Host: localhost
        - Port: 5432
        - BD: Adventureworks
        - Nombre de usuario: postgres
        - Password: la que tengas de tu user de postgresql.

        ![DBeaver connect](https://i.imgur.com/FLrnIMo.png)  
        _DBeaver connect_  

    5. Hacemos clic en **Test Connection** para asegurarte de que los detalles de conexión sean correctos y que podamos conectarte a la BD.
    6. Si la prueba de conexión es exitosa, hacemos clic en "Finalizar" para guardar la configuración de la conexión.

### Configuración de Pentaho  

Esta herramienta la utilizaremos para crear las **_ETL_** de los datos transaccionales (DB Adventureworks) en Postgres a el **_DWH_** en **_AWS Redshift_**.

Nota: En mi caso el ETL también es Pentaho Data Integration, pero el **_DWH_** es **_Postgre SQL_**.

Esta herramienta deberías tenerla instalada del Curso de Fundamentos de ETL con Python y Pentaho. Yo me negué a instalarla, porque ya no hay versión gratuita.

### Instalación y configuración de AWS CLI

Este servicio lo usaremos para realizar la conexión a S3 y cargar archivos planos que luego serán cargados a **_AWS Redshift_** con el comando COPY.

Esta herramienta la configuramos en el Curso Práctico de AWS: Roles y Seguridad con IAM en su módulo SDK, CLI y AWS Access Keys. 

### Configuración de AWS Redshift

**_AWS Redshift_** será utilizado como **_DWH_**. Será el lugar donde construiremos las dimensiones, **_fact tables_** y llevaremos los datos modelados y limpios que se obtuvieron del sistema transaccional.

Ya he explicado como lo haré yo.

1. Creamos un nuevo clúster de **_AWS Redshift_** de manera similar al Curso de Fundamentos de ETL con Python y Pentaho. 

Nota: Recordemos nombrar diferente al clúster de **_AWS Redshift_** y al bucket de **_AWS S3_** que usaremos para el proyecto de este curso.

Con esto hemos completado la configuración de herramientas a usar en las siguientes clases del curso.

## Modelado dimensional: identificación de dimensiones y métricas

### Preguntas del negocio

> "El trabajo consiste en hacer preguntas, todas las que se puedan, y hacer frente a la falta de respuestas precisas con una cierta humildad."  
> [Arthur Miller]

**_Preguntas de Negocio_**  
- Unidades vendidas de cada producto por cliente en un tiempo determinado.
- Cantidad de contrataciones por área en un país específico.  

**_Ejemplo_**  

- **_Organización_**: Cadena de supermercados.  
- **_Actividad objeto de análisis_**: Ventas de productos.  
- **_Información registrada sobre una venta_**: Del producto “crema dental” se han vendido en el almacén “Almacén nro.1” el día 2/2/2030, 5 unidades por un valor de $20.  

**_Ejercicio_**

- ¿Cuánto ha sido en cantidades y valores, los descuentos y las ventas netas (venta-descuento), para cada mes y día?
- ¿Cuánto ha crecido o disminuido las ventas netas al corte del mes de Marzo del año 2013 para cada vendedor?  
- ¿Cuál es el producto más vendido a corte del día? Por categoría.  
- ¿Quién es el cliente que más unidades ha comprado en el último año?  
- ¿Cuánto ha sido en cantidades y valores, los descuentos y las ventas netas (venta-descuento), para cada mes y día?  
- ¿Cuánto ha crecido o disminuido las ventas netas al corte del mes de Marzo del año 2013 para cada vendedor?  
- ¿Cuál es el producto más vendido a corte del día? Por categoría.  
- ¿Quién es el cliente que más unidades ha comprado en el último año?  

**_Mi reto_**  

- ¿Dónde geográficamente se ubican las mayores ventas netas del mes de Diciembre del año 2014?  
- ¿Quién es el vendedor que más ventas realizó por semestre, teniendo en cuenta el nivel del cargo en el momento de la venta?  
- ¿Dónde geográficamente, se ubican las mayores ventas netas del mes de Diciembre del año 2014?  
- ¿Quién es el vendedor que más ventas realizó por semestre, teniendo en cuenta el nivel del cargo en el momento de la venta?  

Esto lo resuelvo en el proyecto.

## Diseño de modelo

**_Reglas de negocio_**

- Crear un campo con el nombre completo del cliente.  
- El campo de observación del producto es demasiado largo. Recortar a los 100 primeros caracteres.  
- Si un vendedor tiene personas a cargo, marcarlo como beneficiario del bono.  

**_Ejercicio_**  


* [platzi/curso-data-warehouse-olap · GitHub](https://github.com/platzi/curso-data-warehouse-olap/blob/main/Proyecto%20Data%20Warehouse/Dise%C3%B1o%20modelo%20dimensional%20-%20dbdiagram.sql)

* [Diseño modelo dimensional - dbdiagram](https://drive.google.com/file/d/1g0kckTsNym7cQ1jPTP9VJ8FXjCt4RL49/view)  

Ya lo tengo hecho de proyectos anteriores.
