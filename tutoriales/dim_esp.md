# Dimensions

### La dimensión Tiempo  

En cualquier **_DWH_** se pueden encontrar varios cubos con sus **_fact tables_** repletas de registros sobre alguna variable de interés para el negocio que debe ser estudiada. Como ya se ha comentado, cada **_fact table_** estará rodeada de varias **_dim tables_**, según que parámetros sirvan mejor para realizar el análisis de los hechos que se quieren estudiar. Un parámetro que casi con toda probabilidad será común a todos los cubos es el Tiempo, ya que lo habitual es almacenar los hechos conforme van ocurriendo a lo largo del tiempo, obteniéndose así una serie temporal de la variable a estudiar.

Dado que el Tiempo es una dimensión presente en prácticamente cualquier cubo de un sistema **_OLAP_** merece una atención especial. Al diseñar la dimensión Tiempo (tanto para un **_star schema_** como para un **_snowflake schema_**) hay que prestar especial cuidado, ya que puede hacerse de varias maneras y no todas son igualmente eficientes. La forma más común de diseñar esta tabla es poniendo como **_PK_** de la tabla la fecha o fecha/hora (*tabla de tiempos 1*). Este diseño no es de los más recomendables, ya que a la mayoría de los sistemas de gestión de bases de datos les resulta más costoso hacer búsquedas sobre campos de tipo "date" o "datetime", estos costes se reducen si el campo clave es de tipo entero, además, un dato entero siempre ocupa menos espacio que un dato de tipo fecha (el campo clave se repetirá en millones de registros en la tabla de hechos y eso puede ser mucho espacio), por lo que se mejorará el diseño de la tabla de tiempos si se utiliza un campo "TiempoID" de tipo entero como clave principal (*tabla de tiempos 2*).

<p><br></p>

*Tabla de tiempos (1)*

| Fecha (PK) | datetime |
| :--------- | :------: |
| Año        | char(4)  |
| Trimestre  | char(6)  |
| Mes        | char(10) |

<p><br></p>

*Tabla de tiempos (2)*  

| TiempoID (PK) | integer  |
| :------------ | :------: |
| Fecha         | datetime |
| Año           | char(4)  | 
| Trimestre     | char(6)  |
| Mes           | char(10) |

<p><br></p>

A la hora de rellenar la tabla de Tiempos, si se ha optado por un campo de tipo entero para la clave, hay dos opciones: la que quizá sea más inmediata consiste en asignar valores numéricos consecutivos (1, 2, 3, 4...) para los diferentes valores de fechas. La otra opción consistiría en asignar valores numéricos del tipo "yyyymmdd", es decir que los cuatro primeros dígitos del valor del campo indiquen el año de la fecha, los dos siguientes el mes y los dos últimos el día. Este segundo modo aporta una cierta ventaja sobre el anterior, ya que de esta forma se consigue que el dato numérico en sí, aporte por sí solo la información de a qué fecha se refiere. Por ejemplo, si en la tabla de hechos se encuentra el valor 20040723, sabremos que se refiere al 23 de julio de 2004; en cambio, con el primer método, se podrían encontrar valores como 8456456, y para saber a qué fecha se refiere este valor se tendría que hacer una consulta sobre la tabla de tiempos.

Además del campo clave TiempoID, la **_fact table_** debe contener otros campos que también es importante considerar. Estos campos serían:
- Un campo "año".- Que contedría valores como '2002', 2003, '2004'...
- Un campo "mes".- Aquí se pueden poner los valores 'enero', 'febrero'... (o de forma abreviada: 'Ene', 'Feb'...). Aunque esto no es incorrecto, se puede mejorar si el nombre del mes va acompañado con el año al que pertenece. Por ejemplo: '2004 enero', '2004 febrero'... De esta forma se optimiza la búsqueda de los valores de un mes en concreto, ya que con el primer método, si se buscan los valores pertenecientes al mes de "enero de 2003", toda esa información está contenida en un solo campo, el "mes", y no haría falta consultar también el campo año.
- Un campo "mesID".- Este campo tendría que ser de tipo entero y serviría para almacenar valores del tipo 200601 (para '2006 enero') o 200602 (para '2006 febrero'). De esta forma es posible realizar ordenaciones y agrupaciones por meses.

De forma análoga a como se ha hecho con el campo mes, se podrían añadir más campos como "Época del año", "Trimestre", "Quincena", "Semana" de tipo texto para poder visualizarlos, y sus análogos de tipo entero "Época del año_ID", "TrimestreID", "QuincenaID", "SemanaID" para poder realizar agrupaciones y ordenaciones. En general se puede añadir un campo por cada nivel de granularidad deseado.

Otro campo especial que se puede añadir es el "Día de la semana" ('lunes', 'martes'...). Este campo se suele añadir para poder hacer estudios sobre el comportamiento de los días de la semana en general (no del primer lunes del mes de enero de un año concreto, este tipo de estudio no suele tener interés), y por esta razón este campo no necesita ir acompañado del mes o del año como los campos anteriores. También se puede añadir su campo dual "ID" de tipo entero para poder ordenar y agrupar si fuera necesario.

Con los añadidos descritos podríamos tener una tabla de Tiempos como la de la figura (*tabla de Tiempos 3*). Esta sería válida para un diseño en estrella. Para un diseño en copo de nieve habría que desglosar la tabla de tiempos en tantas tablas como niveles jerárquicos contenga. Obsérvese que los campos de tipo "ID" son todos de tipo entero, ya que será sobre estos campos sobre los que se realizarán la mayoría de las operaciones y estas se realizarán más eficientemente sobre datos enteros.

<p><br></p>

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

<p><br></p>

## Slowly Changing Dimensions (SCD)

A **_SCD_** in **_DWH_** is a dimension that contains relatively static data that can change slowly but unpredictably, rather than following a regular schedule. Some examples of typical dimensions that change slowly are entities such as Geographic Locations, Customers, or Product names. ([Wikipedia](https://en.wikipedia.org/wiki/Slowly_changing_dimension)).  

<p><br></p>

![001-Dimensión](https://i.imgur.com/kXDzc6e.png)  
_Dimensión (imagen 001)_  

<p><br></p>

### Attributes

- **_Hierarchical_**: They allow you to go from the general to the particular, or to consolidate and disaggregate. For example: country.
- **_Descriptives_**: Relevant information, which is purely descriptive. For example: address, telephone, size, climate.
- **_Control_**: Audit data, which does not belong to the knowledge of the business. For example: log with the record recording date.

### Tipus de SDC

- **_Tipus 0_**: Preserva l'original.
- **_Tipus 1_**: Sobreescriu l'atribut actualitzat. Això no té històric.
- **_Tipus 2_**: Afegeix un nou registre amb el canvi. Això manté històric.
- **_Tipus 3_**: Afegeix un nou atribut "anterior". Això guarda històric, però afegint una nova columna per a l'atribut anterior i el modificat.
- **_Tipus 4_**: Històric separat. **_SCD-4_** sovint s'utilitza amb "taules d'historial". Aquest mètode és similar a com funcionen les taules d'auditoria de bases de dades i les tècniques de captura de dades de canvi.
- **_Tipus 5_**: Combina els enfocaments dels tipus 1 i 4 (1+4=5).
- **_Tipus 6_**: Combina els enfocaments dels tipus 1, 2 i 3 (1+2+3=6).
- **_Tipus 7_**: Consta de dues dimensions separades. Una dimensió es manté com un **_SCD-2_** típic. La seva clau substituta s'utilitza a la taula de fets. S'inclou un segon **_SCD-1_** que conté la vista "actual" de la dimensió. La seva clau duradora (del sistema **_OLTP_**) s'inclou a la taula de fets.

#### Elecció del tipus de SCD

Choosing which type of **_SCD_** to use depends on your specific **_DWH_** requirements and analysis needs. It is important to consider factors such as the importance of historical data, the frequency of dimension changes, and the storage and performance implications of each approach.

**_SCDs_** are a crucial aspect of **_DWHs_** as they allow data to be represented over time, allowing for accurate historical analysis and reporting.

## SDC-0

**_SCD-0_** attributes never change and are assigned to attributes that have durable values or that are described as "Original."  
Examples: date of birth, original credit score.  
Applies to most date size attributes.  

## SCD-1

Let us remember that, in a dimensional database, **_SCDs_** are those dimensions that change over time, but not necessarily in a constant or predictable manner. For example, in a table of Customer data, the customer's address may change over time, but not all customers change their address at the same rate. Some customers may change their address every month, while others may keep the same address for years.

There are different types of **_SDC_**, which are classified according to the way changes in dimensions are handled. For example, type 1 means that old information is overwritten with new information every time a change occurs.

Proper handling of **_SCD_** is important for maintaining the accuracy and integrity of dimensional data in a database, as it allows users to perform historical analysis and compare data over time.

Example:

**_Registration_**

Transactional table

| **Id_Estudiante** | **Full Name** | **Faculty** |
| :------------: | :------------ | :---------: |
| EST12345       | José López    | Marketing   |

Dimension table (now Id_Estudiante is a surrogate key -**_SK_**-, which aims for better dimension performance by numeric key).

| **Id_Estudiante** | **Student_Code** | **Full Name**  | **Faculty** |
| :------------: | :--------------: | :--------- | :---------: |
| 1              | EST12345         | José López | Marketing   |

**_Change of faculty to Engineering _**

Transactional table

| **Id_Estudiante** | **Full Name** | **Faculty** |
| :------------: | :------------ | :---------: |
| EST12345       | José López    | Engineering |


| **Id_Estudiante** | **Student_Code** | **Full Name** | **Faculty** |
| :------------: | :--------------: | :------------ | :---------: |
| 1              | EST12345         | José López    | Engineering |

It does not save history and we have created a new, more efficient key that is synonymous with the one we had in the transactional table.  
It is appropriate when historical information is not important or when it can be retrieved from other sources.

## SCD-2

Example:

**_Registration_**

Transactional table

| **Id_Estudiante** | **Full Name** | **Faculty** |
| :------------: | :------------ | :---------: |
| EST12345       | José López    | Marketing   |

Dimension table (now Id_Estudiante is a surrogate key -**_SK_**-, which aims for better dimension performance by numeric key).

| **Id_Estudiante** | **Student_Code** | **Full Name** | **Faculty** | **Start_date** | **End_date** |
| :------------: | :--------------: | :------------ | :---------: | :------------: | :----------: |
| 1              | EST12345         | José López    | Marketing   | 01/01/2020     |              |

**_Change of faculty to Engineering _**

Transactional table

| **Id_Estudiante** | **Full Name** | **Faculty** |
| :------------: | :------------ | :---------: |
| EST12345       | José López    | Engineering |


| **Id_Estudiante** | **Student_Code** | **Full Name** | **Faculty** | **Start_date** | **End_date** |
| :------------: | :--------------: | :------------ | :---------: | :------------: | :----------: |
| 1              | EST12345         | José López    | Marketing   | 01/01/2020     | 01/01/2020   |
| 2              | EST12345         | José López    | Engineering | 01/02/2020     |              |

**_SCD-2_** are commonly used when historical analysis is required and the dimension changes relatively infrequently.  

## SCD-3

Example:

**_Registration_**

Transactional table

| **Id_Estudiante** | **Full Name** | **Faculty** |
| :------------: | :------------ | :---------: |
| EST12345       | José López    | Marketing   |

Dimension table (now Id_Estudiante is a surrogate key -**_SK_**-, which aims for better dimension performance by numeric key)

| **Id_Estudiante** | **Student_Code** | **Full Name** | **Faculty_old** | **Faculty_new** |
| :------------: | :--------------: | :------------ | :-------------: | :-------------: |
| 1              | EST12345         | José López    |                 | Marketing       |

**_Change of faculty to Engineering _**

Transactional table

| **Id_Estudiante** | **Full Name** | **Faculty** |
| :------------: | :------------ | :---------: |
| EST12345       | José López    | Engineering |


| **Id_Estudiante** | **Student_Code** | **Full Name** | **Faculty_old** | **Faculty_new** |
| :------------: | :--------------: | :------------ | :-------------: | :-------------: |
| 1              | EST12345         | José López    | Marketing       | Engineering     |

Type 3 **_SCDs_** are used when it is important to track specific attribute changes while maintaining simplicity in the data model.

## SCD-4 (Separate History)

SCD-4 is often used with "history tables," where one table retains the current data and an additional table (**_dim table_**) is used to keep track of some or all of the changes.  
Both **_SKs_** are referenced to the **_fact table_** to improve query performance.

For the following example, the name of the original table is Suppliers and the history table is History_Suppliers:

   124 ABC Acme & Johnson Supply Co IL

Suppliers (**_dim table_**)

| **SK** |**Key** | **Name**                 | **State** |
| :----: | :----- | :----------------------- | :-------: |
| 124    | ABC    | Acme & Johnson Supply Co | IL        |

Historical_Suppliers

| **SK** |**Key** | **Name**                 | **State** | **Registration date** |
| :----: | :----- | :----------------------- | :-------: | :-------------------: |
| 123    | ABC    | Acme Supply Co           | AC        | 2003-06-14T00:00:00   |
| 124    | ABC    | Acme & Johnson Supply Co | IL        | 2004-12-22T00:00:00   |

This method is similar to how database audit tables and change data capture techniques work.

## SCD-5

SCD-5 builds on the SCD-4 mini-dimension by embedding a "current profile" mini-dimension key into the base dimension that has been overridden as a type 1 attribute.
This approach is called type 5 because 4 + 1 = 5.
SCD-5 allows you to access the currently assigned minidimension attribute values along with the rest of the base dimension without linking them through a **_fact table_**. We typically represent the base dimension and stabilizer of the current minidimension profile as a single table in the presentation layer. The stabilizer attributes should have different column names, such as "Current Income Level", to differentiate them from the attributes in the mini-dimension linked to the **_fact table_**. The **_ETL_** team must update/overwrite the type 1 minidimension reference whenever the current minidimension changes over time.
If the stabilizers approach does not provide satisfactory query performance, the mini-dimension attributes could be physically embedded (and updated) to the base dimension.

# SCD-6

The SCD-6 combines the approaches of types 1, 2 and 3 (1+2+3=6).

**_Enrollment in the Faculty of Blue Astrophysics_**

Transactional Table

| **Id_Estudiante** | **Full Name** | **Faculty**       |
| :------------: | :------------ | :---------------: |
| EST12345       | José López    | Blue astrophysics |

Dimension table (after ETL)

We realize, in the ETL, that Blue Astrophysics is stupid. Everyone knows that Astrophysics it's green.

| **Id_Estudiante** | **Student_Code** | **Full Name** | **Faculty_old** | **Faculty_new**    | **Start_date** | **End_date** | **Current_Faculty** |
| :------------: | :--------------: | :------------ | :-------------: | :----------------: | :------------: | :----------: | :-----------------: |
| 1              | EST12345         | José López    |                 | Green astrophysics | 08/18/2023     |              | Y                   |

**_Faculty change to Salty climate_**

Transactional table

| **Id_Estudiante** | **Full Name** | **Faculty**   |
| :------------: | :------------ | :-----------: |
| EST12345       | José López    | Salty climate |

Dimension table (after ETL)

| **Id_Estudiante** | **Student_Code** | **Full Name** | **Faculty_old** | **Faculty_new** | **Start_date** | **End_date** | **Current_Faculty** |
| :------------: | :----------------: | :-------------- | :--------------: | :-------------: | :------------: | :----------: | :----------------: |
| 1 | EST12345 | José López | | Green astrophysics | 08/18/2023 | 08/19/2023 | N |
| 2 | EST12345 | José López | Green astrophysics | Salty climate | 08/19/2023 | | Y |

**_Change of faculty, again, to green Astrophysics_**

Transactional Table

| **Id_Estudiante** | **Full Name** | **Faculty** |
| :---------------: | :------------------ | :---------------: |
| EST12345 | José López | Green astrophysics |

Dimension Table (after ETL)

| **Id_Estudiante** | **Student_Code** | **Full Name** | **Faculty_old** | **Faculty_new** | **Start_date** | **End_date** | **Current_Faculty** |
| :---------------: | :----------------: | :-------------- | :--------------: | :-------------: | :------------: | :----------: | :----------------: |
| 1 | EST12345 | José López | | Green astrophysics | 08/18/2023 | 08/19/2023 | N |
| 2 | EST12345 | José López | Green astrophysics | Salty climate | 08/19/2023 | 08/20/2023 | N |
| 3 | EST12345 | José López | Salty climate | Green astrophysics | 08/20/2023 | | Y |

## Some of the Worst Practices in Dimensional Data Modeling

1.  

## Did you find the article interesting?

If you have any questions regarding what has been presented in this article, please let me know in the comments.

Please, if you have enjoyed the content of this article, leave a comment or a "like." Furthermore, if you believe it is good enough or can help other users on the network, share the post on this platform. This way, we can all together democratize knowledge and perhaps assist other people in need.

## Picture list

- **pic001**: From "Data Warehousing and OLAP modeling" Platzi course.

## Disclaimer

**_Platzi_** (formerly Mejorando.la) is a LATAM online education platform. It was founded in 2011 by the Colombian engineer **Freddy Vega** and the Guatemalan computer scientist **Christian Van Der Henst**. ([Wikipedia](https://es.wikipedia.org/wiki/Platzi))

## ChatGPT usage

**_ChatGPT 3.5 Usage_**

This project has been verified for spelling, syntax, and content using [**_ChatGPT 3.5_**](https://chat.openai.com/chat)  

Reference:  
[OpenAI. (2023). ChatGPT (09/29/2023 version) \[Large Language Model\]](https://chat.openai.com/chat)