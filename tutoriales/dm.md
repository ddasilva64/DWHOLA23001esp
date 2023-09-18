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

1. Go to **Physical phase** directly.

2. Think that is the same thing ER model and DDM.

3. Think that in DDM normalized DB is better than non normalized.

4. Directors or Managers, instead of mind in their own businesses, mind in desing of DDM.

5. No reducir dimensiones en el DDM.

6. Confundir subdimensiones con jerarquías de datos.

7. No definir claramente claves de relación de los datos.

8. Relacionar tablas de hechos directamente.

9. Montar Excels, como fuentes de datos, con estructuras normalizadas, para satisfacer a directivos sin conociemientos adecuados.

10. Prescindir de DWH e ir directamente al diseño del DDM en la solución de BI.

11. No asegurarse de que cada tabla de hechos tiene una tabla de dimensión tiempo asociada.

12. No cargar los datos atómicos en estructuras dimensionales.

13. No crear la estructura de los modelos dimensionales en función de los procesos de negocio que vayamos a contemplar, específicamente.

14. No resolver correspondencias muchos a muchos en tablas de hechos.

15. No resolver correspondencias muchos a muchos en tablas de dimensiones.

16. No almacenar las descripciones en las tablas de dimensión.

17. No asegurarse de que las tablas dimensionales usan claves subrogadas.

18. No crear dimensiones conformadas para integrar los datos de toda la empresa.

19. No valorar constantemente los requerimientos y las realidades para proporcionar una solución DWH/BI que sea aceptada por los usuarios de negocios y que apoye su proceso de decisiones.

20. No asegurarsde que todos los hechos que están en una misma fact tenganel mismo nivel de granularidad.