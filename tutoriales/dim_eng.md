# Dimensional Data Modeling (DDM)

## Data Warehouse (DWH), Data Lake (DL), and Data Lakehouse (DLH)

### **_DWH_**

**Characteristics**:
- Is the **central repository, which contains structured data** (columns and rows), obtained through an ETL process of transactional systems, .CSV, etc.

**Used for**:
- **_Business Analysts_**.

**Used in**:
- **_Reports_**,
- **_BSC_** and
- **_BI_**.  

<p><br></p>

![DWH](https://i.imgur.com/1NDE7J7.png)  
_DWH_  

<p><br></p>

### **_Data Lake (DL)_**

**Characteristics**:
- Is the **repository of structured** data from **_ETL_** (DB tables and views, .CSV files, etc.), **semi-structured and unstructured data** (photos, videos, web pages, etc.).
- Data can be easily stored and consulted.
- It can be considered as a **_raw data folder_**.

**Used for**:
- **_Data Scientists_**,
- **_Data Engineers_**, and
- **_Data Analysts_**.

**Used in**:
- **_ML_**,
- **_Predictive Analytics_**,
- **_Data Discovery_** and
- **_Profiling_**.

<p><br></p>

![DL](https://i.imgur.com/RtAStZl.png)  
_DL_

<p><br></p>

## DWH vs. DL

| Characteristics | DWH | DL |
| :-------------- | :-- | :- |
| Data | Optimized to analyze related data from sources such as transactional databases, operational databases and business applications. | Unrelated data from sources such as web sites, social networks, IoT devices, mobile applications. |
| Schema | The data structure is defined before implementation to optimize queries (schema-on-write). | Stores information without the definition of a data structure. It allows you to implement without even knowing the business questions (schema-on-read). |
| Data Quality | Data is cleaned, enriched and transformed so that it can act as the “single source of truth.” | Any data that may or may not have gone through a cleaning and transformation process (raw data). |
| Users | Business analysts. | Data scientists, data engineers, and data analysts (using clean information). | Analytics Reports, control panels and BI. Machine Learning, predictive analytics, data discovery and profiling. |

<p><br></p>

## Data Lakehouse (DLH)

**Characteristics**:
- It is the best of **_DWH_** and **_DL_**.
- Allows you to consume stored data quickly, for **_BI_**, reports, **_DS_** and **_ML_**.

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

## Dim and fact tables - [Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_dimensi%C3%B3n) -

In a **_DWH_** or a **_OLAP_** system, construction of **_OLAP Cubes_** requires a **_fact table_** and several **_dim tables_**, these accompany the **_fact table_** and determine the parameters (dimensions) on which the facts recorded in the **_fact table_** depend. 

In building**_ OLAP_** cubes, **_dim tables_** are elements that contain attributes (or fields) that are used to restrict and group the data stored in a **_fact table_** when performing queries on said data in a **_DWH or DM_** environment.  

These data on dimensions are parameters on which other data depend that will be the object of study and analysis and that are contained in the **_fact table_**. The **_dim tables_** help carry out this study/analysis by providing information on the data from the **_fact table_**, so it can be said that in an **_OLAP_** cube, the **_fact table_** contains the data of interest and the **_dim tables_** contain metadata about said facts.

That is, **dim tables ask questions and fact tables answer**.

### Dimension granularity and hierarchies

Each dimension can refer to concepts such as Time, Products, Customers, Geographic Area, etc. Now, each dimension can be measured in different ways depending on the desired granularity, for example, for the Geographic Area dimension we could consider 'localities', 'provinces', 'regions', 'countries' or 'continents'.  

**The granularity of the fact and dim table must always be the same**, otherwise, the information with the highest degree of granularity will not be able to be consulted.

![Geographic Zone Granularity](https://i.imgur.com/vDzQjC2.png)  
_Geographic Zone Granularity_  

The unit of measurement (by localities, provinces, etc.) will determine this granularity, the smaller this unit of measurement is, the finer this granularity will be (fine grain); if the units of measurement are larger, then we will talk about coarse granularity (coarse grain).

In many cases it is interesting to have the data at various levels of granularity, that is, it is important for the business to be able to consult the data (following the example of the zones) by localities, provinces, etc., in these cases a hierarchy is created with the dimension, since we have several levels of association of the data (with other dimensions such as time, hierarchical levels of the type 'days', 'weeks', 'months'... could be created). 

![Dim table detail](https://i.imgur.com/gHgpzeR.png)  
_Dim table detail_  

When the **_dim tables_** associated with a **_fact table_** do not reflect any hierarchy (for example: Zones are always only 'provinces', time is measured only in 'days', etc.), the resulting cube will be star-shaped, that is, a central **_fact table_** surrounded by as many tables as there are dimensions, and there will only be, in addition to the **_fact table_**, one table for each dimension.

![5-dimensional star schema](https://i.imgur.com/4HPriuo.png)  
_5-dimensional star schema_  

When one or more of the dimensions of the cube reflect some type of hierarchy, there are two approaches regarding the way dimension tables should be designed. The first consists of reflecting all the hierarchical levels of a dimension within a single table, in this case we would also have a **_star schema_** like the one described above, that is, in the model we would have non-normalized tables, to improve performance. 

The other approach consists of applying the **_RDB_** normalization rules to the dimensions. These rules are designed to avoid data redundancies by increasing the number of tables, in this way information is stored in less space. This design results in a snowflake outline. This way of organizing the dimensions of an **_OLAP cube_** has a drawback compared to the star model that does not compensate for the savings in storage space. In OLAP applications the critical resource is not so much the storage space as the response time of the system to user queries, and it has been proven that snowflake models have a longer response time than star models.

It is usually better to reduce dimensions in the **_DWH_** and get a **_star schema_**, rather than a **_snowflake schema_**. Therefore, **normalizing tables may be the worst solution, unlike the DBR model**.

![Snowflake 5-dimensional schema](https://i.imgur.com/1Ata1st.png)  
_Snowflake 5-dimensional schema_   

### The time dimension

In any **_DWH_** you can find several cubes with their **_fact tables_** full of records about some variable of interest to the business that must be studied. As already mentioned, each **_fact table_** will be surrounded by several **_dim tables_**, depending on which parameters serve best to carry out the analysis of the facts that you want to study. A parameter that will almost certainly be common to all cubes is Time, since the usual thing is to store the events as they occur over time, thus obtaining a time series of the variable to be studied.

Since Time is a dimension present in practically any cube of an **_OLAP_** system, it deserves special attention. When designing the Time dimension (both for a **_star schema_** and for a **_snowflake schema_**) special care must be taken, since it can be done in several ways and not all of them are equally efficient. The most common way to design this table is to set the date or date/time as **_PK_** of the table (time table 1). This design is not the most recommended, since most database management systems find it more expensive to search on fields of type "date" or "datetime", these costs are reduced if the key field is of type integer, also, an integer data always takes up less space than a data of type date (the key field will be repeated in millions of records in the fact table and that can be a lot of space), so the design of the time table if an integer "TimeID" field is used as the primary key (time table 2).

Time table (1)

| Date (PK) | datetime |
| :-------- | :------: |
| Year      | char(4)  |
| Quarter   | char(6)  |
| Month     | char(10) |

Time table (2)

| TimeID (PK) | integrate |
| :---------- | :-------: |
| Date        | datetime  |
| Year        | char(4)   |
| Quarter     | char(6)   |
| Month       | char(10)  |

When filling out the Times table, if you have chosen an integer field for the key, there are two options: the one that is perhaps most immediate is to assign consecutive numerical values (1, 2, 3, 4... .) for different date values. The other option would consist of assigning numerical values of the type "yyyymmdd", that is, the first four digits of the field value indicate the year of the date, the next two the month and the last two the day. This second way provides a certain advantage over the previous one, since in this way it is achieved that the numerical data itself provides information on which date it refers to. For example, if the value 20040723 is found in the fact table, we will know that it refers to July 23, 2004; On the other hand, with the first method, values such as 8456456 could be found, and to know what date this value refers to, a query would have to be made on the time table.  

In addition to the TimeID key field, the **_fact table_** should contain other fields that are also important to consider. These fields would be:
- A "year" field. - Which would contain values like '2002', 2003, '2004'...
- A "month" field. - Here you can enter the values 'January', 'February'... (or in abbreviated form: 'Jan', 'Feb'...). Although this is not incorrect, it can be improved if the name of the month is accompanied by the year to which it belongs. For example: 'January 2004', 'February 2004'... In this way the search for the values of a specific month is optimized, since with the first method, if the values belonging to the month of "January of 2003", all this information is contained in a single field, the "month", and it would not be necessary to also consult the year field.
- A "monthID" field. - This field would have to be of type integer and would serve to store values of type 200601 (for 'January 2006') or 200602 (for 'February 2006'). In this way it is possible to organize and group by month.  

In an analogous way to what has been done with the month field, more fields such as "Time of year", "Quarter", "Fortnight", "Week" of text type could be added to be able to display them, and their analogues of integer type " Time of year_ID", "QuarterID", "FortnightID", "WeekID" to be able to group and sort. In general, you can add a field for each level of granularity desired.  

Another special field that can be added is the "Day of the week" ('Monday', 'Tuesday'...). This field is usually added to be able to carry out studies on the behavior of the days of the week in general (not the first Monday of the month of January of a specific year, this type of study is usually not of interest), and for this reason this field It does not need to be accompanied by the month or year like the previous fields. You can also add its dual "ID" field of integer type to be able to sort and group if necessary.  

With the additions described we could have a Time table like the one in the figure "Time Table (3)". This would be valid for a star design. For a snowflake design, the time table would have to be broken down into as many tables as there are hierarchical levels it contains. Note that the "ID" type fields are all of type integer, since it will be on these fields that most of the operations will be performed and these will be performed more efficiently on integer data.

Time table (3)  

| TimeID (PK)   | integrate |
| :------------ | :-------: |
| Date          | datetime  |
| Year          | char(4)   |
| Quarter       | char(6)   |
| QuarterID     | int       |
| Month         | char(10)  |
| MonthID       | int       |
| Fortnight     | char(10)  |
| FortnightID   | int       |
| Week          | char(10)  |
| WeekID        | int       |
| Day           | char(10)  |
| DayID         | int       |
| DayWeek       | char(10)  |
| DayWeekID     | int       |

## Types of dimensional schemes

### Dimensional modeling

Dimensional modeling (DM) uses a set of techniques and concepts used in the design of **_DWH_**. It is considered to be different from the **_ER_** Model. Dimensional modeling does not necessarily imply a **_RDB_**, the same modeling approach, at a logical level, can be used for any physical form, such as multidimensional or flat DB files. ([Wikipedia](https://es.wikipedia.org/wiki/Modelado_dimensional))

### DM Process
To build the schema, in **_DM_**:
1. Choose the business process.
2. Declare the "grain" (granularity).
3. Identify the dimensions (attributes).
4. Identify the facts (metrics).

### Dimensions

They store the attributes that will allow us to have different perspectives on events.
They will have the PKs of the relationship with the fact tables.
Examples:
- Products.
- Subcategories.
- Customers.
- Employees.

### Facts

They store the metrics that we want to consult with the perspectives that the dimensions provide us.
They will have the FK of the relationship with the dimension tables.
Examples:
- Sales.
- Productivity.

<p><br></p>

![DM](https://i.imgur.com/6KaLmDY.png)  
_DM_  

<p><br></p>

### The cube as a multidimensional representation of data

The cube is a multidimensional representation of the data stored in a **_DWH_**. The dimensions would be represented by the edges of the cube (which is why you should not necessarily think in only 3D) and the facts would be each cell where they intersect.

For example, a cube could represent the fact table “Sales” and the dimension tables “Time”, “Product”, and “Location” (of the store). The cube is made up of cells that contain sales data associated with a specific combination of dimension values. That is, a cell can contain the number of sales of a product in a specific month and region. 

<p><br></p>

![DWH representation as a cube 01](https://i.imgur.com/8z58vEP.png)  
_DWH representation as a cube 01_  

<p><br></p>

![RDWH representation as a cube 02](https://i.imgur.com/IOxAIGJ.png)  
_DWH representation as a cube 02_  

<p><br></p>

### Star schema

In the databases used for **_DWH_**, a **_star schema_** is a data model that has a fact table (**_fact_** table) containing the data for analysis, surrounded by dimension tables (**_dim_** tables). This aspect, of a larger fact (or central) table surrounded by spokes or smaller tables, is what resembles a star, giving this type of construction its name.

Dimension tables will always have a simple **_PK_**, while in the fact table, the primary key (**_FK_**) will be composed of the **_PK_** of the dimension tables. ([Wikipedia](https://es.wikipedia.org/wiki/Esquema_en_estrella))  

**_Advantages_**:
- Simple and easy to understand structure.
- Allows fast and efficient queries to large data sets.
- Facilitates multidimensional analysis and informed decision making.
- Supports the addition of new dimensions without altering the existing fact table. 

**_Disadvantages_**:
- Not suitable for transactional databases.
- It is not flexible for cases in which greater complexity is required in the data structure.
- Data redundancy can be a problem in cases where dimensions overlap.
- Requires careful planning and prior analysis to determine the optimal structure of the model.

<p><br></p>

![Star schema](https://i.imgur.com/rnSXwhh.png)  
_Star schema_  

<p><br></p>

### Snowflake schema

In the DBs used in **_DWH_**, a **_snowflake schema_** is a somewhat more complex structure than the **_star schema_**. **_Occurs when any of the dimensions is implemented with more than one data table_**. **_The purpose is to normalize the tables and thus reduce storage space by eliminating data redundancy; but it has the counterpart of generating worse performance_** by having to create more dimension tables and more relationships between the tables (JOINs), which has a direct impact on performance. ([Wikipedia](https://es.wikipedia.org/wiki/Esquema_en_copo_de_nieve))  

<p><br></p>

![Snowflake schema](https://i.imgur.com/eF9u4nd.png)  
_Snowflake schema_  

<p><br></p>

![Dimensional schema types](https://i.imgur.com/z79R1m2.png)  
_Dimensional schema types_

<p><br></p>

**_Advantages_**:
- Reduces data redundancy and storage space.
- Allows better control of data integrity and data quality.
- It is best suited for situations where there are multiple relationships between dimension tables.
- Provides greater flexibility in adding new dimensions. 

**_Disadvantages_**:
- It can be more complex and difficult to understand than the **_star schema_**.
- May require more time and resources to build and maintain than the **_star schema_**.
- May have a higher query processing cost due to the need to perform additional joins between normalized tables.

An example would be Products with Categories. The Products dimension would be related to the Categories dimension.

**_Very important!_**: Models should never be related through fact tables, that is, **we will never relate two fact tables directly**. Always, the relationships between models are carried out through the dimensions they share.

## Slowly Changing Dimensions (SCD)

A **_SCD_** in **_DWH_** is a dimension that contains relatively static data that can change slowly but unpredictably, rather than following a regular schedule. Some examples of typical dimensions that change slowly are entities such as Geographic Locations, Customers, or Product names. ([Wikipedia](https://en.wikipedia.org/wiki/Slowly_changing_dimension))  

<p><br></p>

![Dimension](https://i.imgur.com/kXDzc6e.png)  
_Dimension_  

<p><br></p>

### Attributes

- **_Hierarchical_**: They allow you to go from the general to the particular, or to consolidate and disaggregate. For example: country.
- **_Descriptives_**: Relevant information, which is purely descriptive. For example: address, telephone, size, climate.
- **_Control_**: Audit data, which does not belong to the knowledge of the business. For example: log with the record recording date.

### SDC types

- **_Type 0_**: Preserves the original.
- **_Type 1_**: Overwrite the updated attribute. That is, it has no history.
- **_Type 2_**: Add a new record with the change. That is, it keeps history.
- **_Type 3_**: Add a new “previous” attribute. That is, it saves history, but adding a new column for the previous and the modified attribute.
- **_Type 4_**: Separate history. SCD-4 is often used with "history tables." This method is similar to how database audit tables and change data capture techniques work.
- **_Type 5_**: SCD-5 combines the approaches of types 1 and 4 (1+4=5).
- **_Type 6_**: The SCD-6 combines the approaches of types 1, 2 and 3 (1+2+3=6).
- **_Type 7_**: A SCD-7 consists of two separate dimensions. One dimension is maintained as a typical SCD-2. Its surrogate key is used in the fact table. A second SCD-1 is included that contains the “current” view of the dimension. Its durable key (from the OLTP system) is included in the fact table.

Choosing which type of SCD to use depends on your specific DWH requirements and analysis needs. It is important to consider factors such as the importance of historical data, the frequency of dimension changes, and the storage and performance implications of each approach.

SCDs are a crucial aspect of DWHs as they allow data to be represented over time, allowing for accurate historical analysis and reporting.

## SDC-0

SCD-0 attributes never change and are assigned to attributes that have durable values or that are described as "Original."
Examples: date of birth, original credit score.
Applies to most date size attributes.

## SCD-1

Let us remember that, in a dimensional database, SCDs are those dimensions that change over time, but not necessarily in a constant or predictable manner. For example, in a table of customer data, the customer's address may change over time, but not all customers change their address at the same rate. Some customers may change their address every month, while others may keep the same address for years.

There are different types of SDC, which are classified according to the way changes in dimensions are handled. For example, type 1 means that old information is overwritten with new information every time a change occurs.

Proper handling of Slowly Changing Dimensions is important for maintaining the accuracy and integrity of dimensional data in a database, as it allows users to perform historical analysis and compare data over time.

Example:

**_Registration_**

Transactional table

| **Student_Id** | **Full Name** | **Faculty** |
| :------------: | :------------ | :---------: |
| EST12345       | John Smith    | Marketing   |

Dimension table (now Student_Id is a surrogate key -**_SK_**-, which aims for better dimension performance by numeric key)

| **Student_Id** | **Student_Code** | Full Name  | **Faculty** |
| :------------: | :--------------: | :--------- | :---------: |
| 1              | EST12345         | John Smith | Marketing   |

**_Change of faculty to Engineering _**

Transactional table

| **Student_Id** | **Full Name** | **Faculty** |
| :------------: | :------------ | :---------: |
| EST12345       | John Smith    | Engineering |


| **Student_Id** | **Student_Code** | **Full Name** | **Faculty** |
| :------------: | :--------------: | :------------ | :---------: |
| 1              | EST12345         | John Smith    | Engineering |

It does not save history and we have created a new, more efficient key that is synonymous with the one we had in the transactional table.
It is appropriate when historical information is not important or when it can be retrieved from other sources.

## SCD-2

Example:

**_Registration_**

Transactional table

| **Student_Id** | **Full Name** | **Faculty** |
| :------------: | :------------ | :---------: |
| EST12345       | John Smith    | Marketing   |

Dimension table (now Student_Id is a surrogate key -**_SK_**-, which aims for better dimension performance by numeric key)

| **Student_Id** | **Student_Code** | **Full Name** | **Faculty** | **Start_date** | **End_date** |
| :------------: | :--------------: | :------------ | :---------: | :------------: | :----------: |
| 1              | EST12345         | John Smith    | Marketing   | 01/01/2020     |              |

**_Change of faculty to Engineering _**

Transactional table

| **Student_Id** | **Full Name** | **Faculty** |
| :------------: | :------------ | :---------: |
| EST12345       | John Smith    | Engineering |


| **Student_Id** | **Student_Code** | **Full Name** | **Faculty** | **Start_date** | **End_date** |
| :------------: | :--------------: | :------------ | :---------: | :------------: | :----------: |
| 1              | EST12345         | John Smith    | Marketing   | 01/01/2020     | 01/01/2020   |
| 2              | EST12345         | John Smith    | Engineering | 01/02/2020     |              |

**_SCD-2_** are commonly used when historical analysis is required and the dimension changes relatively infrequently.  

## SCD-3

Example:

**_Registration_**

Transactional table

| **Student_Id** | **Full Name** | **Faculty** |
| :------------: | :------------ | :---------: |
| EST12345       | John Smith    | Marketing   |

Dimension table (now Student_Id is a surrogate key -**_SK_**-, which aims for better dimension performance by numeric key)

| **Student_Id** | **Student_Code** | **Full Name** | **Faculty_old** | **Faculty_new** |
| :------------: | :--------------: | :------------ | :-------------: | :-------------: |
| 1              | EST12345         | John Smith    |                 | Marketing       |

**_Change of faculty to Engineering _**

Transactional table

| **Student_Id** | **Full Name** | **Faculty** |
| :------------: | :------------ | :---------: |
| EST12345       | John Smith    | Engineering |


| **Student_Id** | **Student_Code** | **Full Name** | **Faculty_old** | **Faculty_new** |
| :------------: | :--------------: | :------------ | :-------------: | :-------------: |
| 1              | EST12345         | John Smith    | Marketing       | Engineering     |

Type 3 SCDs are used when it is important to track specific attribute changes while maintaining simplicity in the data model.

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

| **Student_Id** | **Full Name** | **Faculty**       |
| :------------: | :------------ | :---------------: |
| EST12345       | John Smith    | Blue astrophysics |

Dimension table (after ETL)

We realize, in the ETL, that Blue Astrophysics is stupid. Everyone knows that Astrophysics it's green.

| **Student_Id** | **Student_Code** | **Full Name** | **Faculty_old** | **Faculty_new**    | **Start_date** | **End_date** | **Current_Faculty** |
| :------------: | :--------------: | :------------ | :-------------: | :----------------: | :------------: | :----------: | :-----------------: |
| 1              | EST12345         | John Smith    |                 | Green astrophysics | 08/18/2023     |              | Y                   |

**_Faculty change to Salty climate_**

Transactional table

| **Student_Id** | **Full Name** | **Faculty**   |
| :------------: | :------------ | :-----------: |
| EST12345       | John Smith    | Salty climate |

Dimension table (after ETL)

| **Student_Id** | **Student_Code** | **Full Name** | **Faculty_old** | **Faculty_new** | **Start_date** | **End_date** | **Current_Faculty** |
| :------------: | :----------------: | :-------------- | :--------------: | :-------------: | :------------: | :----------: | :----------------: |
| 1 | EST12345 | John Smith | | Green astrophysics | 08/18/2023 | 08/19/2023 | N |
| 2 | EST12345 | John Smith | Green astrophysics | Salty climate | 08/19/2023 | | Y |

**_Change of faculty, again, to green Astrophysics_**

Transactional Table

| **Student_Id** | **Full Name** | **Faculty** |
| :---------------: | :------------------ | :---------------: |
| EST12345 | John Smith | Green astrophysics |

Dimension Table (after ETL)

| **Student_Id** | **Student_Code** | **Full Name** | **Faculty_old** | **Faculty_new** | **Start_date** | **End_date** | **Current_Faculty** |
| :---------------: | :----------------: | :-------------- | :--------------: | :-------------: | :------------: | :----------: | :----------------: |
| 1 | EST12345 | John Smith | | Green astrophysics | 08/18/2023 | 08/19/2023 | N |
| 2 | EST12345 | John Smith | Green astrophysics | Salty climate | 08/19/2023 | 08/20/2023 | N |
| 3 | EST12345 | John Smith | Salty climate | Green astrophysics | 08/20/2023 | | Y |

## Tabla de hechos (fact)

<p><br></p>

![Fact table](https://i.imgur.com/nP9xodv.png)  
_Fact table_  

<p><br></p>

![Fact table Ventas](https://i.imgur.com/nAjHiaZ.png)  
_Fact table Ventas_  

La tabla central (Ventas) es la **_fact table_** de un **_star schema_**, las cinco tablas que la rodean (Producto, Tiempo, Almacén, Promoción y Cliente) son las cinco dimensiones de que consta Ventas, en dicha tabla se almacenan, en este caso, las unidades vendidas y el precio obtenido por dichas ventas, estos son los hechos o medidas de negocio almacenados. (Fuente [Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_hechos)).  

### Las medidas o métricas del negocio (hechos) - Fuente [Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_hechos) -

Las medidas más útiles para incluir en una **_fact table_** son los aditivos, es decir, aquellas medidas que pueden ser sumadas como por ejemplo la cantidad de producto vendido, los costes de producción o el dinero obtenido por las ventas; son medidas numéricas que pueden calcularse con la suma de varias cantidades de la tabla. En consecuencia, por lo general **_los hechos a almacenar en una fact table van a ser casi siempre valores numéricos, enteros o reales_**.   

### Cardinalidad de la tabla de hechos -Fuente [Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_hechos) -

Las **_fact tables_** pueden contener un gran número de filas, a veces cientos de millones de registros cuando contienen uno o más años de la historia de una organización, esta cardinalidad estará acotada superiormente por la cardinalidad de las **_dim tables_**, Por ejemplo, si se tiene una **_fact table "FT"_** de tres dimensiones D1, D2 y D3, el número máximo de elementos que tendrá la tabla de hechos FT será:

$Card(FT) = Card(D1) * Card(D2) * Card(D3)$

Donde $Card(x)$ es la cardinalidad de la tabla X  

**_Nota muy importante_**: Esto hace que siempre se deba construir primero las **_dim tables_** que la **_fact table_** en el modelo.

Naturalmente, estas cardinalidades no son fijas, ya que, por ejemplo, si una de las dimensiones se refiere a los Clientes, cada vez que se dé de alta un nuevo cliente se estará aumentando la cardinalidad de la **_fact table_**. Una de las dimensiones suele ser el Tiempo, éste puede medirse de muy distintas formas (por horas, días, semanas, ...), pero lo cierto es que transcurre continuamente, y para que el sistema funcione se deben añadir registros periódicamente a la tabla de esta dimensión (tabla de tiempos) y esto también produce un aumento de la cardinalidad de la **_fact table_**, esta es la principal causa de que las **_fact tables_** lleguen a tener una cantidad de registros del orden de millones de elementos.

### Granularidad (grain) - Fuente [Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_hechos) -  

Una característica importante que define a una **_fact table_** es el nivel de granularidad de los datos que en ella se almacenan, entendiéndose por **_grain_** el nivel de detalle de dichos datos, es decir, el **_grain_** de la **_fact table_** representa el nivel más atómico por el cual se definen los datos. Por ejemplo, no es lo mismo contar el tiempo por horas (grano fino) que por semanas (grano grueso); o en el caso de los Productos, se puede considerar cada variante de un mismo artículo como un producto (por ejemplo, en una empresa textil, cada talla y color de pantalón podría ser un producto) o agrupar todos los artículos de una misma familia considerándolos como un único producto (por ejemplo, el producto pantalón genérico).  

Como se puede observar, la granularidad afecta a la cardinalidad, tanto de las dimensiones como de la tabla de hechos, a mayor granularidad (grano más fino) mayor será el número de registros final de la **_fact table_**.

> A mayor **_grain_** de las **_dim tables_** mayor cardinalidad de la **_fact table_**.

Cuando el **_grain_** es mayor, es frecuente que se desee disponer de subtotales parciales, es decir, si tenemos una **_fact table_** con las ventas por días, podría interesar disponer de los totales semanales o mensuales, estos datos se pueden calcular haciendo sumas parciales, pero es frecuente añadir a la **_fact table_** registros donde se almacenan dichos cálculos para no tener que repetirlos cada vez que se requieran y mejorar así el rendimiento de la aplicación. En este caso se dispondrá en la misma **_fact table_** de grano fino y de grano más grueso aumentando aún más la cardinalidad de la tabla.

#### Agregación - Fuente [Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_hechos) -  

La agregación es un proceso de cálculo por el cual se resumen los datos de los registros de detalle. Esta operación consiste normalmente en el cálculo de totales dando lugar a medidas de grano grueso. Cuando se resumen los datos, el detalle ya no está directamente disponible para el analista, ya que este se elimina de la **_fact table_**.

Esta operación se realiza típicamente con los datos más antiguos con la finalidad de seguir disponiendo de dicha información (aunque sea resumida) para poder eliminar registros obsoletos de la **_fact table_** para liberar espacio.

### Tipos de datos adecuados - Fuente [Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_hechos) -  

Como ya se ha comentado, es normal que las **_fact tables_** almacenen muchos millones de registros, por esta razón **_es muy importante que no se despilfarre memoria, hay que procurar utilizar los tipos de datos adecuados, si una medida a almacenar puede guardarse en un campo de tipo entero, no debemos definir ese campo como de tipo entero largo o como tipo real_**. Del mismo modo, si una magnitud necesita decimales, si las características de ésta lo permiten, será mejor utilizar un tipo real simple que un tipo real de doble precisión. Nótese que elegir uno u otro de estos campos, en principio sólo supondría una diferencia de unos pocos bytes en un registro, pero dado que en una **_fact table_** estamos hablando de cientos de millones de registros, en realidad, esa diferencia no es despreciable (5 bytes x 200 millones de registros = 1GB de memoria).

[Design Approach to Handle Late Arriving Dimensions and Late Arriving Facts](https://www.disoln.org/2013/12/Design-Approach-to-Handle-Late-Arriving-Dimensions-and-Late-Arriving-Facts.html)

## Configuración de herramientas para DWH y ETL

En el curso se usará **_Postgre SQL_** con la BD **_Adventureworks_**. Esta será la BD transaccional y la fuente de información para llevar al **_DWH_**.  
Yo, con vistas a mi proyecto, continuaré utilizando **_AdventureWorksDW_**. En mi caso la BD transaccional está, por coherencia con proyectos anteriores en **_SQL Server_** y el **_DWH_** en **_Postgre SQL_**.

Para seguir las instrucciones del curso ejecutamos las siguientes instrucciones de configuración:

### Ruby

#### Instalación de Ruby en Ubuntu o WSL con Ubuntu

1. Abrimos la terminal de Ubuntu  
2. Ejecutamos el siguiente comando en la terminal para actualizar la lista de paquetes disponibles:

````bash
sudo apt-get update
````

3. Una vez actualizada la lista de paquetes, instalamos Ruby ejecutando el siguiente comando en la terminal:  

````bash
sudo apt-get install ruby-full
````

4. Verificamos que Ruby se haya instalado correctamente ejecutando ruby -v en la terminal.  

#### Instalación de Ruby en Windows  

1. Descargamos el instalador de Ruby desde la página oficial de Ruby para Windows: https://rubyinstaller.org/downloads/  
2. Seleccionamos la versión de Ruby que deseas instalar.
3. Ejecutamos el instalador y sigue las instrucciones del asistente de instalación.  
4. Una vez completada la instalación, abre la línea de comandos de Windows (cmd.exe) y escribe ruby -v para verificar que la instalación se haya realizado correctamente.  

#### Instalación de Ruby en macOS  

1. Abre la terminal de macOS.  
2. Instala Homebrew ejecutando el siguiente comando en la terminal:  

````bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
````

3. Una vez instalado Homebrew, ejecuta el siguiente comando en la terminal para instalar Ruby:  

````bash
brew install ruby
````

4. Verificamos que Ruby se haya instalado correctamente ejecutando ruby -v en la terminal.  

Con estos pasos ya hemos instalado Ruby.

### PostgreSQL y pgAdmin o DBeaver

Estas herramientas ya deberíamos tenerla instaladas.

**_Nota_**: si usas **_Windows_** debemos recordar asignar las variables de entorno para **_Postgre SQL_**.

![Variables de entorno](https://i.imgur.com/QbSfib5.png)  
_Variables de entorno_  

### Descarga y configuración de la BD AdventureWorks

1. Descargamos el repositorio en https://github.com/lorint/AdventureWorks-for-Postgres  

Ejecutamos el siguiente comando de Git:

````bash
git clone https://github.com/lorint/AdventureWorks-for-Postgres.git
````

Este repositorio contiene los archivos para crear las tablas y vistas de la base de datos.

2. Descargamos [Adventure Works 2014 OLTP Script](https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorks-oltp-install-script.zip).

Contiene los archivos para llenar las tablas de la base de datos.

3. Copiamos y pegamos el archivo AdventureWorks-oltp-install-script.zip en el directorio AdventureWorks-for-Postgres.

4. En nuestra terminal nos ubicamos en el directorio AdventureWorks-for-Postgres y descomprimimos AdventureWorks-oltp-install-script.zip:

````bash
cd AdventureWorks-for-Postgres/
unzip AdventureWorks-oltp-install-script.zip
````

5. En la terminal, ubicándote en el directorio AdventureWorks-for-Postgres, ejecutamos el siguiente comando para convertir los archivos csv:

````bash
ruby update_csvs.rb
````

6. Activamos la conexión con postgresql:  

````bash
sudo service postgresql start
````

7. Creamos la BD con el siguiente comando de PostgreSQL:

````bash
psql -c "CREATE DATABASE \"Adventureworks\";"
````

o

````bash
psql -c "CREATE DATABASE \"Adventureworks\";" -U postgres -h localhost
````

8. Ejecutamos el script que llena las tablas de la BD:

````bash
psql -d Adventureworks < install.sql
````

o

````bash
psql -d Adventureworks < install.sql -U postgres -h localhost
````

9. Conectamos nuestra BD en DBeaver o pgAdmin.

    1. Abrimos DBeaver o pgAdmin.

    2. Seleccionamos la opción para crear una nueva conexión.

    3. Seleccionamos PostgreSQL en la lista de BD.

    4. Ingresamos la información de conexión necesaria en la pestaña.

        - Host: localhost
        - Port: 5432
        - BD: Adventureworks
        - Nombre de usuario: postgres
        - Password: la que tengas de tu user de postgresql.

        ![DBeaver connect](https://i.imgur.com/FLrnIMo.png)  
        _DBeaver connect_  

    5. Hacemos clic en **Test Connection** para asegurarte de que los detalles de conexión sean correctos y que podamos conectarte a la BD.
    6. Si la prueba de conexión es exitosa, hacemos clic en "Finalizar" para guardar la configuración de la conexión.

### Configuración de Pentaho  

Esta herramienta la utilizaremos para crear las **_ETL_** de los datos transaccionales (DB Adventureworks) en Postgres a el **_DWH_** en **_AWS Redshift_**.

Nota: En mi caso el ETL también es Pentaho Data Integration, pero el **_DWH_** es **_Postgre SQL_**.

Esta herramienta deberías tenerla instalada del Curso de Fundamentos de ETL con Python y Pentaho. Yo me negué a instalarla, porque ya no hay versión gratuita.

### Instalación y configuración de AWS CLI

Este servicio lo usaremos para realizar la conexión a S3 y cargar archivos planos que luego serán cargados a **_AWS Redshift_** con el comando COPY.

Esta herramienta la configuramos en el Curso Práctico de AWS: Roles y Seguridad con IAM en su módulo SDK, CLI y AWS Access Keys. 

### Configuración de AWS Redshift

**_AWS Redshift_** será utilizado como **_DWH_**. Será el lugar donde construiremos las dimensiones, **_fact tables_** y llevaremos los datos modelados y limpios que se obtuvieron del sistema transaccional.

Ya he explicado como lo haré yo.

1. Creamos un nuevo clúster de **_AWS Redshift_** de manera similar al Curso de Fundamentos de ETL con Python y Pentaho. 

Nota: Recordemos nombrar diferente al clúster de **_AWS Redshift_** y al bucket de **_AWS S3_** que usaremos para el proyecto de este curso.

Con esto hemos completado la configuración de herramientas a usar en las siguientes clases del curso.

## Modelado dimensional: identificación de dimensiones y métricas

### Preguntas del negocio

> "El trabajo consiste en hacer preguntas, todas las que se puedan, y hacer frente a la falta de respuestas precisas con una cierta humildad."  
> [Arthur Miller]
