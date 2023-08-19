# Introducción a BI y Data Warehouse

## ¿Qué es BI y Data Warehousing?  

### ¿Qué es BI?

> "Habilidad para **entender** la interconexión de los hechos presentes como **medio para descubrir guías** que nos permitan llegar a metas futuras, lo que significa mejorar nuestra capacidad de hacer juicios **predictivos** para el acierto en la **toma de decisiones**."  
> [Hans Peter Luhn, 1958]

> "Conjunto de **estrategias y herramientas** enfocadas a la administración y creación de **conocimiento** mediante el **análisis de datos** existentes en una organización o empresa."  
> [Howard Dresner (Grupo Gartner), 1989] 

> "Habilidad para **transformar los datos en información, y la información en conocimiento**, de forma que se pueda optimizar el proceso de **toma de decisiones** en los negocios"  
> [Mora, 2007]  

> "BI usa **datos de ayer y hoy para tomar las mejores decisiones** acerca del **mañana**"
> [Scheps, 2008]

#### Aportación 

* **_Qué no es BI_**
* Conjunto de herramientas de moda, con las que Directivos no orientados a resultados (de nivel intermedio) se evitan hacer reportes y/o presentaciones en PowerPoint, ante una imposición superior o para justificarse.
* **_Qué debería ser BI_**
* Conjunto de técnicas y herramientas, manejados por personal formado adecuadamente, cuyo fin es obtener información a partir de datos existentes.
* Con la información obtenida, personal directivo (adecuadamente formado y motivado), debe extraer conocimiento para acometer las mejores decisiones tácticas y/o estratégicas.
* Los datos, normalmente se refieren a negocios, aunque no es imprescindible.

### ¿Qué es Data Warehousing?

> "Es un proceso, no un producto, para ensamblar y administrar datos de diversas fuentes con el fin de obtener una visión única y detallada de una parte o de toda una empresa."  
> [Devlin, 2011]  

## Niveles de analítica y jerarquía del conocimiento

![Niveles de analítica](https://i.imgur.com/JgQLCId.png)  
_Niveles de analítica_

<p><br></p>

- **_Analítica Descriptiva_**: **_Describe lo ocurrido en un período determinado_**. El análisis descriptivo se utiliza cuando la organización tiene un gran conjunto de datos sobre eventos pasados o sucesos históricos. Para que estos datos sean útiles, deben simplificar y resumir con el fin de que sean comprensibles para la audiencia a la que quieren comunicarse. (Fuente: [Viquipèdia](https://ca.wikipedia.org/wiki/An%C3%A0lisi_de_dades#An%C3%A0lisi_descriptiva)).  
- **_Analítica Diagnóstica_**: Se centra más en las causas de algo específico, a la hora de evaluar datos descriptivos, las herramientas de análisis de diagnóstico **_ayudarán a los analistas a obtener conocimientos (identificando patrones y relaciones) para resolver el problema de raíz_**. (Fuente: [Viquipèdia](https://ca.wikipedia.org/wiki/An%C3%A0lisi_de_dades#Anal%C3%ADtica_diagn%C3%B2stica)).
- **_Analítica Predictiva_**: Es la aplicación de técnicas y modelos matemáticos y estadísticos a los datos históricos que posee la organización. Aunque los análisis predictivos no intentan predecir el futuro al 100%, porque este tipo de análisis es probabilístico, si **_pronostican lo que podría suceder_**. (Fuente: [Viquipèdia](https://ca.wikipedia.org/wiki/An%C3%A0lisi_de_dades#An%C3%A0lisi_predictiva)).
- **_Analítica Prescriptiva_**: **_Utiliza la información de lo que ha pasado, por qué ha ocurrido, y una variedad de situaciones "posibles" para ayudar a los usuarios a determinar la mejor acción_**. El análisis prescriptivo es, en realidad, una combinación de otros modelos de análisis. (Fuente: [Viquipèdia](https://ca.wikipedia.org/wiki/An%C3%A0lisi_de_dades#An%C3%A0lisi_prescriptiva)).

<p><br></p>

![Pirámide del conocimiento](https://i.imgur.com/lcPsBqi.png)  
_Pirámide del conocimiento_

<p><br></p>

[Ejemplo de transformación de datos en conocimiento, para poder tomar deciones, basadas en datos](https://i.imgur.com/CgO8XOK.png)  
_Ejemplo de transformación de datos en conocimiento, para poder tomar deciones, basadas en datos_

## Conceptos de BI: Data Warehouse, Data Mart, Dimensiones y Hechos

### Data Warehouse (DWH)

- **_BD_** que contiene información de muchas fuentes diferentes, es el repositorio central. 
- Los informes creados a partir de un **_DWH_** son usados.

<p><br></p>

![DWH](https://i.imgur.com/UOGxSmp.png)  
_DWH_

<p><br></p>

### Data Mart (DM)

- Segmento del **_DWH_** orientado a un área específica del negocio. 
- Contienen información sumarizada para el análisis en una unidad de la organización. 

<p><br></p>

![DM](https://i.imgur.com/bDT4i1l.png)  
_DM_

<p><br></p>

### Dimensiones (dim)  

- Describen los procesos del negocio.
- Responde preguntas de los procesos de negocio cómo: ¿Cuál es el producto más vendido?, ¿quién lo ha comprado?, ¿por qué lo ha hecho?, o ¿qué método de pago ha utilizado?.
- Ayudan a analizar una **_métrica_** (indicador) desde diferentes perspectivas.
- Diferentes actores en los procesos del negocio.
- Entidades del negocio.

<p><br></p>

![dim](https://i.imgur.com/7CnWg7b.png)
_dim_

<p><br></p>

### Hechos (fact)

- Es la información cuantitativa de un proceso de negocio.
- Se denominan **_medidas o métricas_**.
- Si tenemos la métrica de ventas y la queremos analizar desde diferentes perspectivas o puntos de vista (dim), poríamos analizarla por: producto, cliente, subcategoría, línea, talla, color y todos los atributos de estas dimentiones.

<p><br></p>

![fact](https://i.imgur.com/dfmnXbn.png)  
_fact_

<p><br></p>

## BD OLTP vs. OLAP

### OLTP (OnLine Transaction Processing)

**_OLTP_** es la sigla en inglés de Procesamiento de Transacciones En Línea. Es un tipo de procesamiento que facilita y administra aplicaciones transaccionales, usualmente para entrada de datos y recuperación y procesamiento de transacciones (gestor transaccional). Los paquetes de software para OLTP se basan en la arquitectura cliente-servidor ya que suelen ser utilizados por empresas con una red informática distribuida. (Fuente: [Wikipedia](https://es.wikipedia.org/wiki/OLTP)).

<p><br></p>

![OLTP](https://i.imgur.com/7Jo7jf3.png)  
_OLTP_  

<p><br></p>

![ERD](https://i.imgur.com/02kIMab.png)  
_ERD_  

<p><br></p>

### OLAP (On-Line Analytical Processing)

**_OLAP_** es el acrónimo en inglés de Procesamiento Analítico En Línea (**_BI_**) cuyo objetivo es agilizar la consulta de grandes cantidades de datos. Para ello utiliza estructuras de datos diversas, normalmente multidimensionales (o **_Cubos OLAP_**), que contienen datos resumidos de grandes BD o Sistemas Transaccionales (**_OLTP_**). Se usa en informes de negocios de ventas, marketing, informes de dirección, minería de datos y áreas similares. (Fuente: [Wikipedia](https://es.wikipedia.org/wiki/OLAP)).

<p><br></p>

![OLAP](https://i.imgur.com/HFIxxdT.png)  
_OLAP_  

<p><br></p>

![Modelo OLAP](https://i.imgur.com/qdlHp3w.png)  
_Modelo OLAP_  

<p><br></p>

### OLTP vs. OLAP

| OLTP                                                 | OLAP                                                  |  
| :--------------------------------------------------- | :---------------------------------------------------- |
| Sistema operacional                                  | Sistema de almacén de datos (**_DWH_**)                      |
| Rapidez, eficiencia, operaciones en tiempo real      | Análisis, consulta, datos multidimensionales, toma de decisiones |
| Sirven para la operación del negocio                 | Sirven para la analítica                              |
| Diseñado para soportar las transacciones comerciales | Diseñado para apoyar el proceso de toma de decisiones |
| Datos vlátiles                                       | Datos NO son volátiles                                |
| Datos detallados                                     | Datos resumidos                                       |
| Modelado E-R                                         | Modelado dimensional                                  |
| Procesamiento de transacciones                       | Procesamiento analítico                               | 
| Alta concurrencia	                                   | Baja concurrencia                                     |
| Responde a preguntas como: ¿La factura ### ha sido cancelada?, o ¿qué cliente ha comprado hoy el producto X? | Responde a preguntas como: ¿Qué producto fue el más vendido en el 2022, por línea de producto?, o ¿cómo han sido las ventas vs. presupuesto, mes a mes y 
por tienda? |

<p><br></p>

![Herramientas OLTP vs. OLAP](https://i.imgur.com/8aI0mBf.png)  
_Herramientas OLTP vs. OLAP_  

<p><br></p>

## Metodologías de DWH

### Bill Inmon

- **_Bill Inmon_**: **_William H. Inmon_** (nacido en 1945) es un informático estadounidense, **_reconocido por muchos como el padre del DWH_**. Escribió el primer libro, realizó la primera conferencia (con Arnie Barnett), escribió la primera columna en una revista y fue el primero en ofrecer clases sobre **_DWH_**. Creó la definición aceptada de lo que es un **_DWH_**: una recopilación de datos variable en el tiempo, integrada, no volátil y orientada a temas en apoyo de las decisiones de gestión. En comparación con el enfoque del otro arquitecto pionero del **_DWH_**, **_Ralph Kimball_**, el enfoque de **_Inmon_** a menudo se caracteriza como un enfoque de arriba hacia abajo. (Fuente: [Wikipedia](https://en.wikipedia.org/wiki/Bill_Inmon))

<p><br></p>

![Bill Inmon](https://i.imgur.com/XkFHRAG.png)
_Bill Inmon_  

<p><br></p>

- **_Ralph Kimball_**: **_Ralph Kimball_** (nacido el 18 de julio de 1944) es un autor **_DWH_** y el **_BI_**. Es uno de los arquitectos originales del almacenamiento de datos y es conocido por sus convicciones a largo plazo de que los **_DWH_** deben diseñarse para que sean comprensibles y rápidos. Su metodología ascendente, también conocida como modelado dimensional o metodología Kimball, es una de las dos principales metodologías de almacenamiento de datos junto con **_Bill Inmon_**. (Fuente: [Wikipedia](https://en.wikipedia.org/wiki/Ralph_Kimball))

<p><br></p>

![Ralph Kimball](https://i.imgur.com/lCHwGai.png)  
_Ralph Kimball_  

<p><br></p>

### Metodología de Ralph Kimball

![Fases metodología de Ralph Kimball](https://i.imgur.com/layLtGp.png)  
_Fases metodología de Ralph Kimball_  

<p><br></p>

### Metodología Hefesto

![Fases metodología Hefesto](https://i.imgur.com/jUxHObK.png)  
_Fases metodología Hefesto_  

[Metodología Hefesto completa](https://troyanx.com/Hefesto/index.html)

<p><br></p>

### Metodología personalizada

![Metodología personalizada](https://i.imgur.com/VucCuID.png)  
_Metodología personalizada_  

<p><br></p>

### Metodología Data Vault

- [Data Vault: Cómo estructurar tu DWH](https://aprenderbigdata.com/data-vault/)

<p><br></p>

![Imgur](https://i.imgur.com/MhyAPoC.png)  

<p><br></p>

- [3 Razones por las que necesitas Data Vault](https://www.linkedin.com/pulse/3-razones-por-las-que-necesitas-data-vault-christian-seijas/?originalSubdomain=es)

<p><br></p>

### Comparativa metodologías

| ÍTEM                      | MULTIDIMENSIONAL(Ralph Kimball) | RELACIONAL(Bill Inmon)  | DATA VAULT(Dan Linsted) |
| :------------------------ | :-----------------------------: | :---------------------: | :---------------------: |
| DISEÑO                    | menor esfuerzo y tiempo         | mayor esfuerzo y tiempo | medio esfuerzo y tiempo |
| MANTENIMIENTO             | medio-alto                      | simple                  | medio                   |
| INVERSIÓN                 | bajo coste                      | alto coste inicial      | bajo coste              |
| TIEMPO                    | tiempo de conf. inicial bajo    | tiempo incial alto      | tiempo incial bajo      |
| ELEMENTOS                 | staging-DM                      | staging-DWH-DM          | hub/link/satélite       |
| NIVEL DE ESPECIALIZACIÓN  | media-baja                      | alta                    | media-alta              |
| INTEGRACIÓN DE DATOS      | áreas individuales              | ámplia                  | áreas individuales      |
| FLEXIBILIDAD	            | menor                           | mayor                   | media                   |

<p><br></p>

[Metodologías de Data Warehouse](https://gravitar.biz/datawarehouse/metodologias-data-warehouse/)