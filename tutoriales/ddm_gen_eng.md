# Dimensional Data Modeling (DDM) - eng -

## DDM process

1. Choose the **business process (in which we have good knowledge)**.
2. Declare the **"grain"** (**granularity**).
3. Identify the **dimensions** (**attributes**).
4. Identify the **facts** (**metrics**).

## Dimension and fact tables

> "The job consists of asking questions, as many as you can, and facing the lack of precise answers with a certain humility."
> [Arthur Miller]

**_DDM_** uses a set of techniques and concepts used in the design of **_DWH_**. It is considered to be different from the **_ER_** model. **_DDM_** does not necessarily imply an **_RDB_**; the same modeling approach, at a logical level, can be used for any physical form, such as multidimensional or flat DB files. ([Wikipedia](https://es.wikipedia.org/wiki/Modelado_dimensional))

> In a **_DWH_** or an **_OLAP_** system, the construction of **_OLAP Cubes_** requires a **_fact table_** and several **_dimension tables_**. These accompany the **_fact table_** and determine the parameters (dimensions) on which the facts recorded in the **_fact table_** depend.
When building **_OLAP_** cubes, **_dimension tables_** are elements that contain attributes (or fields) used to restrict and group the data stored in a **_fact table_** when performing queries in a **_DWH_** or **_DM_** environment.
These dimensions are parameters on which other data depend, which will be the object of study and analysis and are contained in the **_fact table_**. The **_dimension tables_** help carry out this study/analysis by providing information about the data from the **_fact table_**, so it can be said that in an **_OLAP_** cube, the **_fact table_** contains the data of interest, and the **_dimension tables_** contain metadata about said facts.  

> [Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_dimensi%C3%B3n)

In essence, **dimension tables ask questions, and fact tables provide answers**.

### Facts

- They store the **metrics** that we want to consult with the perspectives that the dimensions provide.

- They will have the **foreign keys (FKs) of the relationships** with the dimension tables.

Examples:

- Sales.
- Productivity.

![001-DM](https://i.imgur.com/6KaLmDY.png)  
_DM (pic001)_

![002-Fact table](https://i.imgur.com/nP9xodv.png)  
_Fact table (pic002)_

![003-Fact table Sales](https://i.imgur.com/nAjHiaZ.png)  
_Fact table Sales (pic003)_

The central table (Sales) is the **_fact table_** of a **_star schema_**, surrounded by five tables (Product, Time, Warehouse, Promotion, and Customer), which constitute the five dimensions comprising Sales. In this table, it stores, in this case, the units sold and the price obtained for those sales—these are the stored business facts or measures. ([Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_hechos)).

#### Business measures or metrics (facts)

The most useful measures to include in a **_fact table_** are additives, meaning those measures that can be added, such as the quantity of products sold, production costs, or the revenue obtained from sales. These are numerical measurements that can be calculated by summing various quantities in the table. Consequently, the **_facts_** to be stored in a fact table will almost always be numerical, integer, or real values. ([Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_hechos))

#### Fact table cardinality

**_Fact tables_** can contain a large number of rows, sometimes hundreds of millions of records when they encompass one or more years of an organization's history. This cardinality will be primarily limited by the cardinality of the **_dimension tables_**. For instance, if you have a **_fact table "FT"_** with three dimensions, D1, D2, and D3, the maximum number of elements the FT fact table will have is:

**$Card(FT) = Card(D1) * Card(D2) * Card(D3)$**

Where $Card(x)$ is the cardinality of the table.

**_Very important!_**: This means that the **_dimension tables_** must always be built before the **_fact table_** in the model.

Naturally, these cardinalities are not fixed. For example, if one of the dimensions pertains to Customers, each time a new customer is registered, the cardinality of the **_fact table_** will increase. One of the dimensions is usually Time, which can be measured in various ways (hours, days, weeks, etc.). However, it continuously progresses, and for the system to function, records must be periodically added to the dimension's table (Time table), also increasing the cardinality of the **_fact table_**. This is the primary reason that **_fact tables_** can contain millions of records. ([Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_hechos))

#### Granularity (grain)

An important characteristic that defines a **_fact table_** is the level of granularity of the data it stores. **_Granularity_** refers to the level of detail of the data it stores, representing the most atomic level by which the data is defined in the **_fact table_**. For example, counting time by hours (fine-grain) is not the same as counting time by weeks (coarse-grain). Similarly, for Products, each variant of the same item can be considered a product (e.g., different sizes and colors of pants) or grouped as a single product (e.g., generic pants).

As you can see, granularity affects the cardinality of both the **_dimensions_** and the **_fact table_**. The greater the granularity (finer grain), the greater the final number of records in the **_fact table_**.

> The greater the **_granularity_** of the **_dimension tables_**, the greater the cardinality of the **_fact table_**.

When the **_granularity_** is larger, it is common to want to have partial subtotals. For instance, if we have a **_fact table_** with sales by day, it might be interesting to have weekly or monthly totals. These data can be calculated by making partial sums, but it is common to add records to the **_fact table_** where these calculations are stored to improve query performance. In this case, both fine-grained and coarse-grained data will be available in the same **_fact table_**, further increasing its cardinality. ([Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_hechos))

#### Aggregation

Aggregation is a calculation process in which data from detail records is summarized, typically resulting in coarse-grained measurements. When data is summarized, the detail is no longer directly available to the analyst, as it is removed from the **_fact table_**. This operation is usually performed with the oldest data to continue having that information, even if summarized, while obsolete records can be deleted from the **_fact table_** to free up space. ([Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_hechos))

#### Suitable data types

As mentioned earlier, **_fact tables_** often store millions of records. Therefore, **_it is crucial not to waste memory_**, and we must use the appropriate data types. If a measure can be stored in an integer field, we should not define that field as a long integer or real type. Similarly, if a magnitude requires decimals, and its characteristics allow it, it's better to use a simple real type than a double precision real type. Note that choosing one data type over another may initially only result in a few bytes difference per record, but when dealing with hundreds of millions of records in a **_fact table_**, this difference becomes significant (e.g., 5 bytes x 200 million records = 1GB of memory). ([Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_hechos))

[Design Approach to Handle Late Arriving Dimensions and Late Arriving Facts](https://www.disoln.org/2013/12/Design-Approach-to-Handle-Late-Arriving-Dimensions-and-Late-Arriving-Facts.html)

## Types of dimensional schemes

### The cube as a multidimensional representation of data

The cube is a multidimensional representation of the data stored in a **_DWH_**. The dimensions are represented by the edges of the cube (which is why you should not necessarily think in only 3D), and the facts are in each cell where they intersect.

For example, a cube could represent the fact table "Sales" and the dimension tables "Time," "Product," and "Location" (of the store). The cube is made up of cells that contain sales data associated with a specific combination of dimension values. In other words, a cell can contain the number of sales of a product in a specific month and region.

![004-DWH representation as a cube 01](https://i.imgur.com/8z58vEP.png)  
_DWH (pic004)_

![005-DWH representation as a cube 02](https://i.imgur.com/IOxAIGJ.png)  
_DWH (pic005)_

### Star schema

In the databases used for **_DWH_**, a **_star schema_** is a data model that has a fact table (**_fact_** table) containing the data for analysis, surrounded by dimension tables (**_dim_** tables). This aspect, a larger fact (or central) table surrounded by spokes or smaller tables, is what resembles a star, giving this type of construction its name.

Dimension tables will always have a simple **_PK_**, while in the **_fact table_**, the primary key (**_FK_**) will be composed of the **_PK_** of the **_dimension tables_**. ([Wikipedia](https://es.wikipedia.org/wiki/Esquema_en_estrella))

**_Advantages_**:
- Simple and easy-to-understand structure.
- Allows fast and efficient queries on large data sets.
- Facilitates multidimensional analysis and informed decision-making.
- Supports the addition of new dimensions without altering the existing **_fact table_**.

**_Disadvantages_**:
- Not suitable for transactional databases.
- Not flexible for cases requiring greater complexity in the data structure.
- Data redundancy can be a problem when dimensions overlap.
- Requires careful planning and prior analysis to determine the optimal structure of the model.

![006-Star schema](https://i.imgur.com/rnSXwhh.png)  
_Star schema (pic006)_

### Snowflake schema

In the DBs used in **_DWH_**, a **_snowflake schema_** is a somewhat more complex structure than the **_star schema_**. **_It occurs when any of the dimensions are implemented with more than one data table_**. **_The purpose is to normalize the tables and thus reduce storage space by eliminating data redundancy, but it has the counterpart of generating worse performance_** due to the need to create more dimension tables and more relationships between the tables (JOINs), which directly impacts performance. ([Wikipedia](https://es.wikipedia.org/wiki/Esquema_en_copo_de_nieve))

![007-Snowflake schema](https://i.imgur.com/eF9u4nd.png)  
_Snowflake schema (pic007)_

![008-Dimensional schema types](https://i.imgur.com/z79R1m2.png)  
_Dimensional schema types (pic008)_

**_Advantages_**:
- Reduces data redundancy and storage space.
- Allows better control of data integrity and data quality.
- Best suited for situations with multiple relationships between dimension tables.
- Provides greater flexibility in adding new dimensions.

**_Disadvantages_**:
- Can be more complex and challenging to understand than the **_star schema_**.
- May require more time and resources to build and maintain than the **_star schema_**.
- May have a higher query processing cost due to the need for additional joins between normalized tables.

An example would be Products with Categories. The Products dimension would be related to the Categories dimension.

**_Very important!_**: Models should never be related through **_fact tables_**; that is, **we should never relate two fact tables directly**. Always, the relationships between models are carried out through the dimensions they share.

## Data Warehouse (DWH), Data Lake (DL), and Data Lakehouse (DLH)

### **_DWH_**

**Characteristics**:
- It is the **central repository that contains structured data** (columns and rows) obtained through an **_ETL_** process from transactional systems, .CSV files, etc.

**Used by**:
- **_Business Analysts_**

**Utilized in**:
- **_Reports_**
- **_BSC_** (Balanced Scorecard)
- **_BI_** (Business Intelligence)

![009-DWH](https://i.imgur.com/1NDE7J7.png)
 _DWH (pic009)_

### **_Data Lake (DL)_**

**Characteristics**:
- It is the **repository of structured data** from **_ETL_** (DB tables and views, .CSV files, etc.), **semi-structured and unstructured data** (such as pictures, videos, web pages, etc.).
- Data can be easily stored and queried.
- It can be considered a **_raw data repository_**.

**Used by**:
- **_Data Scientists_**
- **_Data Engineers_**
- **_Data Analysts_**

**Utilized in**:
- **_ML_** (Machine Learning)
- **_Predictive Analytics_**
- **_Data Discovery_**
- **_Profiling_**

![010-DL](https://i.imgur.com/RtAStZl.png)  
 _DL (pic010)_

### **_DWH_** vs. **_DL_**

| Characteristics | DWH | DL |
| :-------------- | :-- | :- |
| Data | Optimized for analyzing related data from sources such as transactional databases, operational databases, and business applications. | Comprises unrelated data from sources such as websites, social networks, IoT devices, and mobile applications. |
| Schema | The data structure is defined before implementation to optimize queries (schema-on-write). | Stores information without defining a data structure. It allows you to implement without even knowing the business questions (schema-on-read). |
| Data quality | Data is cleaned, enriched, and transformed to serve as the “single source of truth.” | Any data, which may or may not have undergone a cleaning and transformation process (raw data). |
| Users | Business analysts. | Data scientists, data engineers, and data analysts (when using cleaned information). | Analytics Reports, dashboards, and BI. Machine Learning, predictive analytics, data discovery, and profiling. |

### Data Lakehouse (**_DLH_**)

**Characteristics**:
- Combines the best features of both **_DWH_** and **_DL_**.
- Enables the rapid consumption of stored data for **_BI_**, reports, **_DS_** (Data Science), and **_ML_** (Machine Learning).

![011-DLH 01](https://i.imgur.com/Vu3X94C.png)  
_DLH (pic011)_

![012-DLH 02](https://i.imgur.com/a0LdoXX.png)  
_DLH (pic012)_

![013-DLH 03](https://i.imgur.com/kNFF8J3.png)  
_DLH (pic013)_

## Some of the Worst Practices in Dimensional Data Modeling

1. Going to the **physical phase directly**.

2. **Confusing the** **_ER model_** **with** **_DDM_**.  
   - Setting up Excels as data sources with normalized structures always.  
   - Not clearly defining data relationship keys.  
   - Normalized DB is better than non-normalized in **_DDM_**.

3. **No IT employees are designing** **_DDM_** **(for instance, Directors or Managers of business)**.

4. **Mistakes in the definitions of fact tables and dim tables**.  
   - **Relating fact tables directly**.  
   - Defining more attributes in dimensions than necessary for the ask fact table.  
   - Dim tables do not ask anything.  
   - Fact tables do not answer anything.  
   - **Not loading atomic data into dimensional structures**.  
   - Not reducing dimensions in the **_DDM_**.  
   - **Confusing Subdimensions with data hierarchies**.  
   - Not resolving many-to-many correspondences in fact tables.  
   - Not resolving many-to-many correspondences in dimension tables.  
   - Not storing descriptions in dimension tables.  
   - **Not ensuring that all the dimensions related to the same fact have the same level of granularity**.
   - **Design fact tables before dimension tables**.  

5. **Lacking a good understanding of business rules**.  
   - Failing to constantly assess requirements and realities to provide a **_BI_** solution that is accepted by business users and supports their decision-making process.  
   - Not creating **the structure of the dimensional models based on** the **business processes** that we are going to contemplate, **specifically**.

6. **Mistakes in granularity**.  
   - Not considering the granularity of dimensions correctly.  
   - **Not considering the correct fact table grain, for instance, to allow "usability" for Managers or Directors**.  
   - **Defining different granularity between dim table and fact table**.

7. **Considering ETL unnecessary**.  
   - **Moving** **_ETL_** **to** **_BI,_** **instead of using the correct tool beforehand**.

8. **Considering DWH unnecessary**.  
   - Not using **_DWH_** **because people think it is unnecessary**.  
   - Dispensing with **_DWH_** and **going directly to the** **_DDM_** design in the **_BI_** solution. 

## Did you find the article interesting?

If you have any questions regarding what has been presented in this article, please let me know in the comments.

Please, if you have enjoyed the content of this article, leave a comment or a "like." Furthermore, if you believe it is good enough or can help other users on the network, share the post on this platform. This way, we can all together democratize knowledge and perhaps assist other people in need.

## Picture list

- **pic001**: From "Data Warehousing and OLAP modeling" Platzi course.
- **pic002**: From "Data Warehousing and OLAP modeling" Platzi course.
- **pic003**: From "Data Warehousing and OLAP modeling" Platzi course.
- **pic004**: From "Data Warehousing and OLAP modeling" Platzi course.
- **pic005**: From "Data Warehousing and OLAP modeling" Platzi course.
- **pic006**: From "Data Warehousing and OLAP modeling" Platzi course.
- **pic007**: From "Data Warehousing and OLAP modeling" Platzi course.
- **pic008**: From "Data Warehousing and OLAP modeling" Platzi course.
- **pic009**: From "Data Warehousing and OLAP modeling" Platzi course.
- **pic010**: From "Data Warehousing and OLAP modeling" Platzi course.
- **pic011**: From "Data Warehousing and OLAP modeling" Platzi course.
- **pic012**: From "Data Warehousing and OLAP modeling" Platzi course.
- **pic013**: From "Data Warehousing and OLAP modeling" Platzi course.

## Disclaimer

**_Platzi_** (formerly Mejorando.la) is a LATAM online education platform. It was founded in 2011 by the Colombian engineer **Freddy Vega** and the Guatemalan computer scientist **Christian Van Der Henst**. ([Wikipedia](https://es.wikipedia.org/wiki/Platzi))

## ChatGPT usage

**_ChatGPT 3.5 Usage_**

This project has been verified for spelling, syntax, and content using [**_ChatGPT 3.5_**](https://chat.openai.com/chat)  

Reference:  
[OpenAI. (2023). ChatGPT (09/27/2023 version) \[Large Language Model\]](https://chat.openai.com/chat)