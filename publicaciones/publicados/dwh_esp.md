# Data Warehousing - esp -

> Es un **proceso**, no un producto, para **recopilar y gestionar datos** de **fuentes diversas** con el fin de **obtener una vista única y detallada de una parte o de toda una empresa**.  
> [Devlin, 2011]  

## Data Warehouse (DWH)

- La **base de datos** que contiene **datos de diversas fuentes** es el **repositorio central**.
- Los informes creados a partir de un **_DWH_** se utilizan en el **_BI_** (por ejemplo). Es decir, **es el contenedor de fuentes de datos para soluciones de BI**.

<p><br></p>

![001-DWH](https://i.imgur.com/UOGxSmp.png)  
_DWH (imagen 001)_

<p><br></p>

## Data Mart (DM)

- **_DWH_** orientado hacia un **área específica del negocio**.
- Contienen **datos resumidos para su análisis en una unidad de la organización**.

<p><br></p>

![002-DM](https://i.imgur.com/bDT4i1l.png)  
_DM (imagen 002)_

<p><br></p>

## BD OLTP vs. OLAP

### OLTP (OnLine Transaction Processing)

**_OLTP_** es la sigla en inglés de Procesamiento de Transacciones En Línea. Es un tipo de procesamiento que facilita y administra aplicaciones transaccionales, usualmente para entrada de datos y recuperación y procesamiento de transacciones (gestor transaccional). Los paquetes de software para **_OLTP_** se basan en la arquitectura cliente-servidor ya que suelen ser utilizados por empresas con una red informática distribuida. ([Wikipedia](https://es.wikipedia.org/wiki/OLTP)).

<p><br></p>

![003-OLTP](https://i.imgur.com/7Jo7jf3.png)  
_OLTP (imagen 003)_  

<p><br></p>

![004-ERD](https://i.imgur.com/02kIMab.png)  
_ERD (imagen 004)_  

<p><br></p>

### OLAP (On-Line Analytical Processing)

**_OLAP_** es el acrónimo en inglés de Procesamiento Analítico En Línea (**_BI_**) cuyo objetivo es agilizar la consulta de grandes cantidades de datos. Para ello utiliza estructuras de datos diversas, normalmente multidimensionales (o **_Cubos OLAP_**), que contienen datos resumidos de grandes BD o Sistemas Transaccionales (**_OLTP_**). Se usa en informes de negocios de ventas, marketing, informes de dirección, minería de datos y áreas similares. ([Wikipedia](https://es.wikipedia.org/wiki/OLAP)).

<p><br></p>

![005-OLAP](https://i.imgur.com/HFIxxdT.png)  
_OLAP (imagen 005)_  

<p><br></p>

![006-Modelo OLAP](https://i.imgur.com/qdlHp3w.png)  
_Modelo OLAP (imagen 006)_  

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
| Responde a preguntas como: ¿La factura ### ha sido cancelada?, o ¿qué cliente ha comprado hoy el producto X? | Responde a preguntas como: ¿Qué producto fue el más vendido en el 2022, por línea de producto?, o ¿cómo han sido las ventas vs. presupuesto, mes a mes y por tienda? |

<p><br></p>

![007-Herramientas OLTP vs. OLAP](https://i.imgur.com/8aI0mBf.png)  
_Herramientas OLTP vs. OLAP (imagen 007)_  

<p><br></p>

## Metodologías de DWH

### Bill Inmon

- **_Bill Inmon_**: **_William H. Inmon_** (nacido en 1945) es un informático estadounidense, **_reconocido por muchos como el padre del DWH_**. Escribió el primer libro, realizó la primera conferencia (con Arnie Barnett), escribió la primera columna en una revista y fue el primero en ofrecer clases sobre **_DWH_**. Creó la definición aceptada de lo que es un **_DWH_**: una recopilación de datos variable en el tiempo, integrada, no volátil y orientada a temas en apoyo de las decisiones de gestión. En comparación con el enfoque del otro arquitecto pionero del **_DWH_**, **_Ralph Kimball_**, el enfoque de **_Inmon_** a menudo se caracteriza como un **enfoque de arriba hacia abajo**. ([Wikipedia](https://en.wikipedia.org/wiki/Bill_Inmon)).
<p><br></p>

![008-Bill Inmon](https://i.imgur.com/XkFHRAG.png)
_Bill Inmon (imagen 008)_  

<p><br></p>

- **_Ralph Kimball_**: **_Ralph Kimball_** (nacido el 18 de julio de 1944) es un autor **_DWH_** y el **_BI_**. Es uno de los arquitectos originales del Data Warehousing y es conocido por sus convicciones a largo plazo de que los **_DWH_** deben diseñarse para que sean comprensibles y rápidos. Su **metodología de abajo hacia arriba**, también conocida como Modelado Dimensional o metodología Kimball, es una de las dos principales metodologías de Data Warehousing junto con **_Bill Inmon_**. ([Wikipedia](https://en.wikipedia.org/wiki/Ralph_Kimball)).

<p><br></p>

![009-Ralph Kimball](https://i.imgur.com/lCHwGai.png)  
_Ralph Kimball(imagen 009)_  

<p><br></p>

### Metodología de Ralph Kimball

![010-Fases metodología de Ralph Kimball](https://i.imgur.com/layLtGp.png)  
_Fases metodología de Ralph Kimball (imagen 010)_  

<p><br></p>

### Metodología Hefesto

![011-Fases metodología Hefesto](https://i.imgur.com/jUxHObK.png)  
_Fases metodología Hefesto (imagen 011)_  

[Metodología Hefesto completa](https://troyanx.com/Hefesto/index.html)

<p><br></p>

### Metodología personalizada

![012-Metodología personalizada](https://i.imgur.com/VucCuID.png)  
_Metodología personalizada (imagen 012)_  

<p><br></p>

### Metodología Data Vault

- [Data Vault: Cómo estructurar tu DWH](https://aprenderbigdata.com/data-vault/)

<p><br></p>

![013-Metodología Data Vault](https://i.imgur.com/MhyAPoC.png)  
_Metodología Data Vault (imagen 013)_ 

<p><br></p>

- [3 razones por las que necesitas Data Vault](https://www.linkedin.com/pulse/3-razones-por-las-que-necesitas-data-vault-christian-seijas/?originalSubdomain=es)

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

## Algunas de las peores prácticas en Data Warehousing

1. **Pensar con mentalidad OLTP a la hora de diseñar el Data Warehousing**.
    - **Despreciar el área de staging** en nuestro **_DWH_**, sin ninguna razón en particular.
    - **Despreciar el área de DM** en nuestro **_DWH_**, sin ninguna razón en particular.

2. **Solo se puede contruir el modelo físico de un DWH con una BD OLAP y no una OLTP**.

3. **Se puede contruir el modelo lógico de un DWH con un modelo ER y no uno DDM**.

4. **Improvisar**, sin tener una metodología de **_DWH_**.

5. No ser conciente de los **efectos de la metodología** **_DWH_** escogida.

6. **Permitir que Directores o Gerentes de negocios decidan la configuración de nuestro DWH (priorizando sus intereses departamentales)**.

7. **Ir directamente a la solución DDM sin tener en cuenta la fase raw (datos en bruto)**.

8. **Confundir el DWH con una DB OLTP tradicional**.

9. **Considerar el DWH innecesario**.  
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
- **img013**: [Data Vault: Cómo estructurar tu Data Warehouse](https://aprenderbigdata.com/data-vault/).

## Descargo de Responsabilidad

**_Platzi_** (anteriormente Mejorando.la) es una plataforma de educación en línea de América Latina. Fue fundada en 2011 por el ingeniero colombiano **Freddy Vega** y el científico de la computación guatemalteco **Christian Van Der Henst**. ([Wikipedia](https://es.wikipedia.org/wiki/Platzi))

## Uso de ChatGPT

**_Uso de ChatGPT 3.5_**

Este proyecto ha sido verificado para ortografía, sintaxis y contenido utilizando [**_ChatGPT 3.5_**](https://chat.openai.com/chat).

Referencia:  
[OpenAI. (2023). ChatGPT (versión del 25 de septiembre de 2023) \[Large Language Model\]](https://chat.openai.com/chat)