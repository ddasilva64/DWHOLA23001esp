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

Tipo 1: Lentamente cambiante, un registro en una fuente de datos que en algun momento cambia y se reemplaza no guarda historicos. (reemplaza el valor inicial)
Tipo 2: Lentamente cambiante, un registro que guarda la historia, diciendo que en algun momento fue algo y cambio despues.
Tipo 3: Almacena infomracion historica pero almacenado en una columna nueva indicando el valor que tenia y en la columna nueva el nuevo valor asociado.



