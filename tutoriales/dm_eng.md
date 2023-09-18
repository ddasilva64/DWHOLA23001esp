# Data Modeling (DM) - eng -

## What is DM?

**_DM_** is the **process of creating a simplified schema of the data in a software system**.

A data model (**_DM_**) can be thought of as a flowchart that illustrates data entities, their attributes, and the relationships between entities. It allows you to document application data requirements and identify development errors before writing any code.

## Phases

- **Conceptual**: **It is a high-level (logical) visualization**. It describes the types of data that are needed, how the different entities interrelate, and the business rules they will use. **Target audience is Managers**.

- **Logical**: **It is a logical level, less abstract than the conceptual**. It shows how data entities are related and describes the data from a technical perspective. **Target audience are Technicians to help them to understand DB designs**.

- **Physical**: **Is the basis for creating a** **_DM_**. Is specific to the DB management system (**_DBMS_**) or application software that will be implemented. **Target audience are Database designers to help them create designs of DBs**.

<p><br></p>

## Dimensional Data Modeling (DDM)

**_DDM_** is primarily used in **_DWH_** and **_DM_** to support **_BI_** applications. It consists of fact tables (**_fact_**) and dimension tables (**_dim_**).

Types of **_DDM_**:
- **_Star_** schemas.

    ![001-5-dimension star schema](https://i.imgur.com/4HPriuo.png)
    _5-dimension star schema (pic001)_

- **_Snowflake_** schemas.

    ![002-5-dimension snowflake schema](https://i.imgur.com/1Ata1st.png)
    _5-dimension snowflake schema (pic002)_

- **_003-Constellation or Galaxy_** schemas (derived from prior types).

    ![Constellation or Galaxy schema](https://i.imgur.com/mXHRJch.png)
    _Constellation or Galaxy schema (pic003)_

# Some of the worst practices in Dimensional Data Modeling

1. Go to the **Physical phase directly**.

2. Think the **_ER model_** **is the same as** **_DDM_**.

3. **No IT employees designing DDM (for instance, Directors or Managers of business)**.

4. **Relate fact tables directly**.

5. Failure to constantly assess requirements and realities to provide a BI solution that is accepted by business users and supports their decision-making process.

## Picture list

- **pct001**: From "Data Warehousing and OLAP modeling" Platzi course.
- **pct002**: From "Data Warehousing and OLAP modeling" Platzi course.
- **pct003**: From the article author.

## Disclaimer

**_Platzi_** (formerly Mejorando.la) is a LATAM online education platform. It was founded in 2011 by the Colombian engineer **Freddy Vega** and the Guatemalan computer scientist **Christian Van Der Henst**. [Wikipedia](https://es.wikipedia.org/wiki/Platzi)

## ChatGPT usage

**_ChatGPT 3.5 Usage_**

This project has been verified for spelling, syntax, and content using [**_ChatGPT 3.5_**](https://chat.openai.com/chat)  

Reference:  
[OpenAI. (2023). ChatGPT (09/18/2023 version) \[Large Language Model\]](https://chat.openai.com/chat)