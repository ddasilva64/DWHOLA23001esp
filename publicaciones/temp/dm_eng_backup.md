# Data Modeling (DM)

## What is DM?

**_DM_** is the process of creating a simplified schema of the data in a software system.  

A data model (**_dm_**) can be thought of as a flowchart that illustrates data entities, their attributes, and the relationships between entities. It allows you to document application data requirements and identify development errors before writing any code.

## Phases

- **Conceptual**: **It is a high-level (logical) visualization**. It describes the types of data that are needed, how the different entities interrelate, and the business rules they will use. **Target audience are Managers**.  
- **Logical**: **It is a logical level, less abstract than the conceptual**. It shows how data entities are related and describes the data from a technical perspective. **Target audience are Technicians** to help them to understand DB designs.  
- **Physical**: **Is the basis for creating a** **_dm_**. Is specific to the DB management system (DBMS) or application software that will be implemented. **Taget audience are Database designers** to help them to create designs of DBs.

<p><br></p> 

## Dimensional DM (DDM)

**_DDM_** are primarily used in **_DWH_** and **_dm_**, that support **_BI_** applications. They consist of fact tables (**_fact_**) and dimension tables (**_dim_**).   

Types of **_DDM_**:
- **_Star_** schemas.

    ![5 dimensions star schema](https://i.imgur.com/4HPriuo.png)  
    _5 dimensions star schema_ 

- **_Snowflake_** schemas. 
    
    ![5 dimensions snowflake schema](https://i.imgur.com/1Ata1st.png)  
    _5 dimensions snowflake schema_ 

- **_Constellation or Gallaxy_** schemas (derivated of prior types).

    ![Constellation or Gallaxy schema](https://i.imgur.com/AEqg4iO.png)
    _Constellation or Gallaxy schema_

# Some of the worst practices in Business Intelligence

1. Go to **Physical phase directly**.

2. ER model is the same that DDM.

3. **Directors or Managers designing DDM**.

4. **Relate fact tables directly**.

5. Failure to constantly assess requirements and realities to provide a BI solution that is accepted by business users and supports their decision process.



-------------------------------




17. Not ensuring that dimension tables use surrogate keys.

18. Do not create custom dimensions to integrate data from the entire company.

Not ensuring that each fact table has an associated time dimension table.

