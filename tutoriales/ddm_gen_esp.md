# Modelado Dimensional de Datos (DDM) - esp -

## Proceso DDM (Modelado Dimensional de Datos)

1. Elegir el **proceso de negocio (del cual tenemos buen conocimiento)**.
2. Declarar la **"granularidad"** (**nivel de detalle**).
3. Identificar las **dimensiones** (**atributos**).
4. Identificar los **hechos** (**métricas**).

## Tablas de dimensión y de hechos

> "El trabajo consiste en hacer preguntas, tantas como puedas, y enfrentar la falta de respuestas precisas con cierta humildad."
> [Arthur Miller]

> El **_DDM_** utiliza un conjunto de técnicas y conceptos utilizados en el diseño de un **_DWH_** (Almacén de Datos). Se considera diferente del modelo **_ER_**. El **_DDM_** no implica necesariamente una **_RDB_** (Base de Datos Relacional); el mismo enfoque de modelado, a nivel lógico, se puede utilizar en cualquier forma física, como bases de datos multidimensionales o archivos planos. ([Wikipedia](https://es.wikipedia.org/wiki/Modelado_dimensional))

> En un sistema **_DWH_** u **_OLAP_**, la construcción de **_Cubos OLAP_** requiere una **_tabla de hechos_** y varias **_tablas de dimensión_**. Estas acompañan a la **_tabla de hechos_** y determinan los parámetros (dimensiones) sobre los cuales dependen los hechos registrados en la **_tabla de hechos_**.
Cuando se construyen cubos **_OLAP_**, las **_tablas de dimensión_** son elementos que contienen atributos (o campos) utilizados para restringir y agrupar los datos almacenados en una **_tabla de hechos_** al realizar consultas en un entorno **_DWH_** o **_DM_**.
Estas dimensiones son parámetros de los cuales otros datos dependen, que serán el objeto de estudio y análisis y están contenidos en la **_tabla de hechos_**. Las **_tablas de dimensión_** ayudan a llevar a cabo este estudio/análisis al proporcionar información sobre los datos de la **_tabla de hechos_**, por lo que se puede decir que en un cubo **_OLAP_**, la **_tabla de hechos_** contiene los datos de interés y las **_tablas de dimensión_** contienen metadatos sobre esos hechos. ([Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_dimensi%C3%B3n))

En esencia, **las tablas de dimensión hacen preguntas, y las tablas de hechos proporcionan respuestas**.

### Hechos

- Almacenan las **métricas** que deseamos consultar con las perspectivas que proporcionan las dimensiones.

- Contienen las **claves foráneas (FKs) de las relaciones** con las tablas de dimensiones.

Ejemplos:

- Ventas.
- Productividad.

![001-DM](https://i.imgur.com/6KaLmDY.png)  
_DM (imagen 001)_

![002-Tabla de hechos](https://i.imgur.com/nP9xodv.png)  
_Tabla de hechos (imagen 002)_

![003-Tabla de hechos Ventas](https://i.imgur.com/nAjHiaZ.png)  
_Tabla de hechos Ventas (imagen 003)_

La tabla central (Ventas) es la **_tabla de hechos_** de un **_esquema en estrella_**, rodeada por cinco tablas (Producto, Tiempo, Almacén, Promoción y Cliente), que constituyen las cinco dimensiones que componen las Ventas. En esta tabla, se almacenan, en este caso, las unidades vendidas y el precio obtenido por esas ventas; estos son los hechos o medidas de negocio almacenados. ([Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_hechos)).

#### Medidas o métricas de negocio (hechos)

Las medidas más útiles para incluir en una **_tabla de hechos_** son aditivas, es decir, aquellas medidas que se pueden sumar, como la cantidad de productos vendidos, los costos de producción o los ingresos obtenidos por las ventas. Estas son mediciones numéricas que se pueden calcular sumando varias cantidades en la tabla. En consecuencia, los **_hechos_** que se almacenarán en una tabla de hechos casi siempre serán valores numéricos enteros o reales. ([Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_hechos))

#### Cardinalidad de la tabla de hechos

Las **_tablas de hechos_** pueden contener un gran número de filas, a veces cientos de millones de registros cuando abarcan uno o más años de la historia de una organización. Esta cardinalidad estará principalmente limitada por la cardinalidad de las **_tablas de dimensiones_**. Por ejemplo, si tienes una **_tabla de hechos "FT"_** con tres dimensiones, D1, D2 y D3, el número máximo de elementos que tendrá la tabla de hechos FT será:

**$Card(FT) = Card(D1) * Card(D2) * Card(D3)$**

Donde $Card(x)$ es la cardinalidad de la tabla.

**_¡Muy importante!_**: Esto significa que las **_tablas de dimensiones_** siempre deben construirse antes que la **_tabla de hechos_** en el modelo.

Naturalmente, estas cardinalidades no son fijas. Por ejemplo, si una de las dimensiones se refieren a los Clientes, cada vez que se registra un nuevo cliente, la cardinalidad de la **_tabla de hechos_** aumentará. Una de las dimensiones suele ser el Tiempo, que se puede medir de varias formas (horas, días, semanas, etc.). Sin embargo, avanza continuamente, y para que el sistema funcione, se deben agregar periódicamente registros a la tabla de la dimensión del Tiempo, lo que también aumenta la cardinalidad de la **_tabla de hechos_**. Esta es la razón principal por la que las **_tablas de hechos_** pueden contener millones de registros. ([Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_hechos))

#### Granularidad (grano)

Una característica importante que define una **_tabla de hechos_** es el nivel de granularidad de los datos que almacena. La **_granularidad_** se refiere al nivel de detalle de los datos que almacena, representando el nivel más atómico mediante el cual se define la información en la **_tabla de hechos_**. Por ejemplo, contar el tiempo en horas (granularidad fina) no es lo mismo que contar el tiempo por semanas (granularidad gruesa). De manera similar, en el caso de los Productos, cada variante del mismo artículo puede considerarse como un producto (por ejemplo, diferentes tallas y colores de pantalones) o agruparse como un solo producto (por ejemplo, pantalones genéricos).

Como se puede ver, la granularidad afecta a la cardinalidad tanto de las **_dimensiones_** como de la **_tabla de hechos_**. Cuanto mayor sea la granularidad (grano más fino), mayor será el número final de registros en la **_tabla de hechos_**.

> Cuanto mayor sea la **_granularidad_** de las **_tablas de dimensiones_**, mayor será la cardinalidad de la **_tabla de hechos_**.

Cuando la **_granularidad_** es mayor, es común querer tener subtotales parciales. Por ejemplo, si tenemos una **_tabla de hechos_** con ventas por día, podría ser interesante tener totales semanales o mensuales. Estos datos se pueden calcular mediante sumas parciales, pero es común agregar registros a la **_tabla de hechos_** donde se almaculan estos cálculos para mejorar el rendimiento de las consultas. En este caso, tanto los datos de granularidad fina como de granularidad gruesa estarán disponibles en la misma **_tabla de hechos_**, aumentando aún más su cardinalidad. ([Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_hechos))

#### Agregación

La agregación es un proceso de cálculo en el cual los datos de los registros de detalle se resumen, generalmente resultando en medidas de granularidad gruesa. Cuando los datos se resumen, los detalles ya no están directamente disponibles para el analista, ya que se eliminan de la **_tabla de hechos_**. Esta operación generalmente se realiza con los datos más antiguos para seguir teniendo esa información, incluso si está resumida, mientras que los registros obsoletos pueden eliminarse de la **_tabla de hechos_** para liberar espacio. ([Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_hechos))

#### Tipos de datos adecuados

Como se mencionó anteriormente, las **_tablas de hechos_** a menudo almacenan millones de registros. Por lo tanto, **_es crucial no desperdiciar memoria_**, y debemos utilizar los tipos de datos apropiados. Si una medida puede almacenarse en un campo de tipo entero, no deberíamos definir ese campo como un entero largo o un tipo real. Del mismo modo, si una magnitud requiere decimales y sus características lo permiten, es mejor utilizar un tipo real simple que un tipo real de doble precisión. Ten en cuenta que elegir un tipo de datos en lugar de otro puede inicialmente resultar en una diferencia de solo unos pocos bytes por registro, pero cuando se trata de cientos de millones de registros en una **_tabla de hechos_**, esta diferencia se vuelve significativa (por ejemplo, 5 bytes x 200 millones de registros = 1 GB de memoria). ([Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_hechos))

[Enfoque de diseño para manejar dimensiones y hechos que llegan tarde](https://www.disoln.org/2013/12/Design-Approach-to-Handle-Late-Arriving-Dimensions-and-Late-Arriving-Facts.html)


## Tipos de esquemas dimensionales

### El cubo como una representación multidimensional de datos

El cubo es una representación multidimensional de los datos almacenados en un **_DWH_** (Data Warehouse). Las dimensiones están representadas por los bordes del cubo (por eso no necesariamente debes pensar solo en 3D), y los hechos se encuentran en cada celda donde se intersecan.

Por ejemplo, un cubo podría representar la tabla de hechos "Ventas" y las tablas de dimensiones "Tiempo", "Producto" y "Ubicación" (de la tienda). El cubo está compuesto por celdas que contienen datos de ventas asociados a una combinación específica de valores de dimensiones. En otras palabras, una celda puede contener la cantidad de ventas de un producto en un mes y una región específicos.

![004-Representación de DWH como un cubo 01](https://i.imgur.com/8z58vEP.png)  
_DWH (imagen 004)_

![005-Representación de DWH como un cubo 02](https://i.imgur.com/IOxAIGJ.png)  
_DWH (imagen 005)_

### Esquema en estrella

En las bases de datos utilizadas en los **_DWH_** (almacenes de datos), un **_esquema en estrella_** es un modelo de datos que consta de una **_tabla de hechos_** que contiene los datos para el análisis, rodeada de **_tablas de dimensiones_**. Este aspecto, una **_tabla de hechos_** más grande (o central) rodeada de tablas más pequeñas, que se asemeja a una estrella es de donde proviene el nombre de esta construcción.

Las **_tablas de dimensiones_** siempre tendrán una clave primaria simple (**_PK_**), mientras que en la **_tabla de hechos_**, la clave primaria (**_FK_**) estará compuesta por las claves primarias (**_PK_**) de las tablas de dimensiones. ([Wikipedia](https://es.wikipedia.org/wiki/Esquema_en_estrella))

**_Ventajas_**:
- Estructura simple y fácil de entender.
- Permite consultas rápidas y eficientes en conjuntos de datos grandes.
- Facilita el análisis multidimensional y la toma de decisiones informadas.
- Admite la adición de nuevas dimensiones sin alterar la tabla de hechos existente.

**_Desventajas_**:
- No es adecuado para bases de datos transaccionales.
- No es flexible para casos que requieren mayor complejidad en la estructura de datos.
- La redundancia de datos puede ser un problema cuando las dimensiones se superponen.
- Requiere una planificación cuidadosa y un análisis previo para determinar la estructura óptima del modelo.

![006-Esquema en estrella](https://i.imgur.com/rnSXwhh.png)  
_Esquema en estrella (imagen 006)_

### Esquema en copo de nieve

En las bases de datos utilizadas en **_DWH_** (almacenes de datos), un **_esquema copo de nieve_** es una estructura algo más compleja que el **_esquema estrella_**. **_Se produce cuando cualquiera de las dimensiones se implementa con más de una tabla de datos_**. **_El propósito es normalizar las tablas y, de esta manera, reducir el espacio de almacenamiento al eliminar la redundancia de datos, pero tiene la contrapartida de generar un peor rendimiento_** debido a la necesidad de crear más tablas de dimensiones y más relaciones entre las tablas (JOINs), lo que impacta directamente en el rendimiento. ([Wikipedia](https://es.wikipedia.org/wiki/Esquema_en_copo_de_nieve))

![007-Esquema copo de nieve](https://i.imgur.com/eF9u4nd.png)  
_Esquema copo de nieve (imagen 007)_

![008-Tipos de esquemas dimensionales](https://i.imgur.com/z79R1m2.png)  
_Tipos de esquemas dimensionales (imagen 008)_

**_Ventajas_**:
- Reduce la redundancia de datos y el espacio de almacenamiento.
- Permite un mejor control de la integridad de datos y la calidad de datos.
- Se adapta mejor a situaciones con múltiples relaciones entre tablas de dimensiones.
- Proporciona mayor flexibilidad para agregar nuevas dimensiones.

**_Desventajas_**:
- Puede ser más complejo y difícil de entender que el **_esquema estrella_**.
- Puede requerir más tiempo y recursos para construir y mantener que el **_esquema estrella_**.
- Puede tener un mayor costo de procesamiento de consultas debido a la necesidad de joins adicionales entre tablas normalizadas.

Un ejemplo sería Productos con Categorías. La dimensión de Productos estaría relacionada con la dimensión de Categorías.

**_¡Muy importante!_**: Los modelos nunca deben relacionarse a través de tablas de hechos; es decir, **nunca debemos relacionar directamente dos tablas de hechos**. Siempre, las relaciones entre modelos se realizan a través de las dimensiones que comparten.

## Data Warehouse (DWH), Data Lake (DL), y Data Lakehouse (DLH)

### **_DWH_** (Almacén de Datos)

**Características**:
- Es el **repositorio central que contiene datos estructurados** (filas y columnas) obtenidos a través de un proceso **_ETL_** desde sistemas transaccionales, archivos .CSV, etc.

**Utilizado por**:
- **_Analistas de Negocios_**

**Utilizado en**:
- **_Informes_**
- **_BSC_** (Cuadro de Mando Integral)
- **_BI_** (Inteligencia de Negocios)

![009-DWH](https://i.imgur.com/1NDE7J7.png)
 _DWH (imagen 009)_

### **_Data Lake (DL)_** (Lago de Datos)

**Características**:
- Es el **repositorio de datos estructurados** obtenidos del **_ETL_** (tablas y vistas de bases de datos, archivos .CSV, etc.), **datos semi-estructurados y no estructurados** (como imágenes, vídeos, páginas web, etc.).
- Los datos se pueden almacenar y consultar fácilmente.
- Se puede considerar un **_repositorio de datos en bruto_**.

**Utilizado por**:
- **_Científicos de Datos_**
- **_Ingenieros de Datos_**
- **_Analistas de Datos_**

**Utilizado en**:
- **_ML_** (Aprendizaje Automático)
- **_Analítica Predictiva_**
- **_Descubrimiento de Datos_**
- **_Perfilado_**

![010-DL](https://i.imgur.com/RtAStZl.png)  
 _DL (imagen 010)_

### **_DWH_** vs. **_DL_**

| Características | DWH | DL |
| :-------------- | :-- | :- |
| Datos | Optimizado para analizar datos relacionados de fuentes como bases de datos transaccionales, bases de datos operativas y aplicaciones empresariales. | Compuesto por datos no relacionados de fuentes como sitios web, redes sociales, dispositivos IoT y aplicaciones móviles. |
| Esquema | La estructura de datos se define antes de la implementación para optimizar las consultas (esquema en escritura). | Almacena información sin definir una estructura de datos. Permite implementar sin siquiera conocer las preguntas comerciales (esquema en lectura). |
| Calidad de datos | Los datos se limpian, enriquecen y transforman para servir como la "fuente única de verdad". | Cualquier dato, que puede o no haber pasado por un proceso de limpieza y transformación (datos en bruto). |
| Usuarios | Analistas de negocios. | Científicos de datos, ingenieros de datos y analistas de datos (cuando se utiliza información limpia). | Analítica, informes, paneles y BI. Aprendizaje automático, analítica predictiva, descubrimiento de datos y perfilado. |

### Data Lakehouse (**_DLH_**)

**Características**:
- Combina las mejores características tanto de **_DWH_** como de **_DL_**.
- Permite el consumo rápido de datos almacenados para **_BI_** (Inteligencia de Negocios), informes, **_DS_** (Ciencia de Datos) y **_ML_** (Aprendizaje Automático).

![011-DLH 01](https://i.imgur.com/Vu3X94C.png)  
_DLH (imagen 011)_

![012-DLH 02](https://i.imgur.com/a0LdoXX.png)  
_DLH (imagen 012)_

![013-DLH 03](https://i.imgur.com/kNFF8J3.png)  
_DLH (imagen 013)_

## Algunas de las peores prácticas en Modelado Dimensional de Datos

1. Ir directamente a la **fase física**.

2. **Confundir el** **_modelo ER_** **con** **_DDM_**.  
   - Configurar Excels como fuentes de datos siempre con estructuras normalizadas.  
   - No definir claramente las claves de relación de datos.  
   - Una base de datos normalizada es mejor que una no normalizada en **_DDM_**.

3. **No tener empleados de TI en el diseño** **_DDM_** **(por ejemplo, tener Directores o Gerentes de negocios)**.

4. **Errores en las definiciones de tablas de hechos y tablas de dimensiones**.  
   - **Relacionar tablas de hechos directamente**.  
   - Definir más atributos en las dimensiones de lo necesario para la tabla de hechos solicitada.  
   - Las tablas de dimensiones no preguntan nada.  
   - Las tablas de hechos no responden nada.  
   - **No cargar datos atómicos en estructuras dimensionales**.  
   - No reducir las dimensiones en el **_DDM_**.  
   - **Confundir subdimensiones con jerarquías de datos**.  
   - No resolver correspondencias de muchos a muchos en tablas de hechos.  
   - No resolver correspondencias de muchos a muchos en tablas de dimensiones.  
   - No almacenar descripciones en tablas de dimensiones.  
   - **No asegurarse de que todas las dimensiones relacionadas con el mismo hecho tengan el mismo nivel de granularidad**.
   - **Diseñar antes las tablas de hechos que las de dimensión**.

5. **Falta de un buen entendimiento de las reglas de negocio**.  
   - Fallar en evaluar constantemente los requisitos y realidades para proporcionar una solución de **_BI_** que sea aceptada por los usuarios de negocios y que respalde su proceso de toma de decisiones.  
   - No crear **la estructura de los modelos dimensionales basada en** los **procesos de negocio** que vamos a contemplar, **específicamente**.

6. **Errores en la granularidad**.  
   - No considerar correctamente la granularidad de las dimensiones.  
   - **No considerar la granularidad correcta de las tablas de hechos, por ejemplo, para permitir "usabilidad" para Gerentes o Directores**.  
   - **Definir diferentes niveles de granularidad entre la tabla de dimensiones y la tabla de hechos**.

7. **Considerar el ETL innecesario**.  
   - **Mover el** **_ETL_** **al** **_BI,_** **en lugar de utilizar la herramienta correcta de antemano**.

8. **Considerar el DWH innecesario**.  
   - No utilizar el **_DWH_** **porque la gente piensa que es innecesario**.  
   - Prescindir del **_DWH_** e **ir directamente al diseño del** **_DDM_** en la solución de **_BI_**.

## ¿Te ha interesado el artículo?

Si tienes alguna duda con respecto a lo expuesto en este artículo, házmelo saber en los comentarios.

Por favor, si te ha gustado el contenido de este artículo, deja un comentario o un "me gusta". Además, si consideras que es lo suficientemente bueno o que puede ayudar a otros usuarios de la red, comparte la publicación en esta plataforma. De esta manera, todos juntos podemos democratizar el conocimiento y quizás ayudar a otras personas que lo necesiten.

## Lista de Imágenes

- **img001**: Del curso "Data Warehousing y modelado OLAP" de Platzi.
- **img002**: Del curso "Data Warehousing y modelado OLAP" de Platzi.
- **img003**: Del curso "Data Warehousing y modelado OLAP" de Platzi.
- **img004**: Del curso "Data Warehousing y modelado OLAP" de Platzi.
- **img005**: Del curso "Data Warehousing y modelado OLAP" de Platzi.
- **img006**: Del curso "Data Warehousing y modelado OLAP" de Platzi.
- **img007**: Del curso "Data Warehousing y modelado OLAP" de Platzi.
- **img008**: Del curso "Data Warehousing y modelado OLAP" de Platzi.
- **img009**: Del curso "Data Warehousing y modelado OLAP" de Platzi.
- **img010**: Del curso "Data Warehousing y modelado OLAP" de Platzi.
- **img011**: Del curso "Data Warehousing y modelado OLAP" de Platzi.
- **img012**: Del curso "Data Warehousing y modelado OLAP" de Platzi.
- **img013**: Del curso "Data Warehousing y modelado OLAP" de Platzi.

## Descargo de Responsabilidad

**_Platzi_** (anteriormente Mejorando.la) es una plataforma de educación en línea de América Latina. Fue fundada en 2011 por el ingeniero colombiano **Freddy Vega** y el científico de la computación guatemalteco **Christian Van Der Henst**. ([Wikipedia](https://es.wikipedia.org/wiki/Platzi))

## Uso de ChatGPT

**_Uso de ChatGPT 3.5_**

Este proyecto ha sido verificado para ortografía, sintaxis y contenido utilizando [**_ChatGPT 3.5_**](https://chat.openai.com/chat).

Referencia:  
[OpenAI. (2023). ChatGPT (versión del 27 de septiembre de 2023) \[Large Language Model\]](https://chat.openai.com/chat)