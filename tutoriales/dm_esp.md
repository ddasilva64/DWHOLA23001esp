# Modelado de Datos (MD) - esp -

## ¿Qué es el MD?

**_MD_** es el **proceso de crear un esquema simplificado de los datos en un sistema de software**.

Un modelo de datos (**_MD_**) se puede considerar como un diagrama de flujo que ilustra entidades de datos, sus atributos y las relaciones entre las entidades. Permite documentar los requisitos de datos de la aplicación e identificar errores de desarrollo antes de escribir cualquier código.

## Fases

- **Conceptual**: **Es una visualización de alto nivel (lógica)**. Describe los tipos de datos que se necesitan, cómo se relacionan las diferentes entidades y las reglas comerciales que utilizarán. **El público objetivo son los Gerentes**.

- **Lógica**: **Es un nivel lógico, menos abstracto que el conceptual**. Muestra cómo están relacionadas las entidades de datos y describe los datos desde una perspectiva técnica. **El público objetivo son los Técnicos para ayudarles a comprender los diseños de bases de datos**.

- **Física**: **Es la base para crear un** **_MD_**. Es específica del sistema de gestión de bases de datos (**_DBMS_**) o del software de aplicación que se implementará. **El público objetivo son los diseñadores de bases de datos para ayudarles a crear diseños de bases de datos**.

<p><br></p>

## Modelado Dimensional de Datos (MDD)

**_MDD_** se utiliza principalmente en **_DWH_** y **_MD_** para respaldar aplicaciones de **_BI_**. Consiste en tablas de hechos (**_fact_**) y tablas de dimensiones (**_dim_**).

Tipos de **_MDD_**:
- Esquemas de **_Estrella_**.

    ![001-Esquema de estrella con 5 dimensiones](https://i.imgur.com/4HPriuo.png)
    _Esquema de estrella con 5 dimensiones (imagen001)_

- Esquemas de **_Copo de Nieve_**.

    ![002-Esquema de copo de nieve con 5 dimensiones](https://i.imgur.com/1Ata1st.png)
    _Esquema de copo de nieve con 5 dimensiones (imagen002)_

- Esquemas de **_Constelación o Galaxia_** (derivados de los tipos anteriores).

    ![003-Esquema de constelación o galaxia](https://i.imgur.com/mXHRJch.png)
    _Esquema de constelación o galaxia (imagen003)_

# Algunas de las peores prácticas en Modelado Dimensional de Datos

1. Ir **directamente a la fase física**.

2. Pensar que el **_modelo ER_** **es igual que el** **_MDD_**.

3. **No permitir que los empleados de TI diseñen el MDD (por ejemplo, Directores o Gerentes de negocios)**.

4. **Relacionar directamente las tablas de hechos**.

5. No evaluar constantemente los requisitos y las realidades para proporcionar una solución de BI que sea aceptada por los usuarios de negocios y respalde su proceso de toma de decisiones.

## Lista de Imágenes

- **img001**: Del curso "Data Warehousing y modelado OLAP" de Platzi.
- **img002**: Del curso "Data Warehousing y modelado OLAP" de Platzi.
- **img003**: Del autor del artículo.

## Descargo de Responsabilidad

**_Platzi_** (anteriormente Mejorando.la) es una plataforma de educación en línea de América Latina. Fue fundada en 2011 por el ingeniero colombiano **Freddy Vega** y el científico de la computación guatemalteco **Christian Van Der Henst**. [Wikipedia](https://es.wikipedia.org/wiki/Platzi)

## Uso de ChatGPT

**_Uso de ChatGPT 3.5_**

Este proyecto ha sido verificado para ortografía, sintaxis y contenido utilizando [**_ChatGPT 3.5_**](https://chat.openai.com/chat).

Referencia:  
[OpenAI. (2023). ChatGPT (versión del 18 de septiembre de 2023) \[Large Language Model\]](https://chat.openai.com/chat)