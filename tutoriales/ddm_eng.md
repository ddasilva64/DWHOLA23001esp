# Dimensional Data Modeling (DDM) - eng -

## DDM process

1. Choose the **business process (in which we have good knowledge)**.
2. Declare the **"grain"** (**granularity**).
3. Identify the **dimensions** (**attributes**).
4. Identify the **facts** (**metrics**).

## Identifying dimensions and facts

> "The job consists of asking questions, as many as you can, and facing the lack of precise answers with a certain humility."
> [Arthur Miller]

**_DDM_** uses a set of techniques and concepts used in the design of **_DWH_**. It is considered to be different from the **_ER_** model. **_DDM_** does not necessarily imply an **_RDB_**; the same modeling approach, at a logical level, can be used for any physical form, such as multidimensional or flat DB files. ([Wikipedia](https://es.wikipedia.org/wiki/Modelado_dimensional))

## Dimension and fact tables

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

### Suitable data types

As mentioned earlier, **_fact tables_** often store millions of records. Therefore, **_it is crucial not to waste memory_**, and we must use the appropriate data types. If a measure can be stored in an integer field, we should not define that field as a long integer or real type. Similarly, if a magnitude requires decimals, and its characteristics allow it, it's better to use a simple real type than a double precision real type. Note that choosing one data type over another may initially only result in a few bytes difference per record, but when dealing with hundreds of millions of records in a **_fact table_**, this difference becomes significant (e.g., 5 bytes x 200 million records = 1GB of memory). ([Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_hechos))

[Design Approach to Handle Late Arriving Dimensions and Late Arriving Facts](https://www.disoln.org/2013/12/Design-Approach-to-Handle-Late-Arriving-Dimensions-and-Late-Arriving-Facts.html)

### Dimensions

- They store the attributes that will allow us to have **different perspectives on events**.

- They will have the **PKs of the relationships** with the fact tables.

Examples:

- Products.
- Subcategories.
- Customers.
- Employees.

#### The Time dimension

In any **_DWH_**, you can find several cubes with their **_fact tables_** full of records about some variable of interest to the business that must be studied. As already mentioned, each **_fact table_** will be surrounded by several **_dimension tables_**, depending on which parameters serve best to carry out the analysis of the facts that you want to study. A parameter that will almost certainly be common to all cubes is Time, since the usual thing is to store the events as they occur over time, thus obtaining a time series of the variable to be studied.

Since Time is a dimension present in practically any cube of an **_OLAP_** system, it deserves special attention. When designing the Time dimension (both for a **_star schema_** and for a **_snowflake schema_**), special care must be taken, since it can be done in several ways and not all of them are equally efficient. The most common way to design this table is to set the date or date/time as the **_PK_** of the table (time table 1). This design is not the most recommended, since most database management systems find it more expensive to search on fields of type "date" or "datetime". These costs are reduced if the key field is of type integer. Also, an integer data always takes up less space than a data of type date (the key field will be repeated in millions of records in the fact table and that can take up a lot of space). So, the design of the time table, if an integer "TimeID" field is used as the primary key (time table 2).

**Time table (1)**

| Date (PK) | Datetime |
| :-------- | :------: |
| Year      | char(4)  |
| Quarter   | char(6)  |
| Month     | char(10) |

**Time table (2)**

| TimeID (PK) | Integer |
| :---------- | :-----: |
| Date        | Datetime|
| Year        | char(4) |
| Quarter     | char(6) |
| Month       | char(10)|

When filling out the Time table, if you have chosen an integer field for the key, there are two options: the one that is perhaps most immediate is to assign consecutive numerical values (1, 2, 3, 4...) for different date values. The other option would consist of assigning numerical values of the type "yyyymmdd", that is, the first four digits of the field value indicate the year of the date, the next two the month and the last two the day. This second way provides a certain advantage over the previous one, since in this way it is achieved that the numerical data itself provides information on which date it refers to. For example, if the value 20040723 is found in the fact table, we will know that it refers to July 23, 2004. On the other hand, with the first method, values such as 8456456 could be found, and to know what date this value refers to, a query would have to be made on the time table.

In addition to the TimeID key field, the **_fact table_** should contain other fields that are also important to consider. These fields would be:
- A "year" field. - Which would contain values like '2002', 2003, '2004'...
- A "month" field. - Here you can enter the values 'January', 'February'... (or in abbreviated form: 'Jan', 'Feb'...). Although this is not incorrect, it can be improved if the name of the month is accompanied by the year to which it belongs. For example: 'January 2004', 'February 2004'... In this way, the search for the values of a specific month is optimized, since with the first method, if the values belonging to the month of "January 2003" are needed, all this information is contained in a single field, the "month", and it would not be necessary to also consult the year field.
- A "monthID" field. - This field would have to be of type integer and would serve to store values of type 200601 (for 'January 2006') or 200602 (for 'February 2006'). In this way, it is possible to organize and group by month.

In an analogous way to what has been done with the month field, more fields such as "Time of year", "Quarter", "Fortnight", "Week" of text type could be added to be able to display them, and their analogues of integer type " Time of year_ID", "QuarterID", "FortnightID", "WeekID" to be able to group and sort. In general, you can add a field for each level of granularity desired.

Another special field that can be added is the "Day of the week" ('Monday', 'Tuesday'...). This field is usually added to be able to carry out studies on the behavior of the days of the week in general (not the first Monday of the month of January of a specific year, this type of study is usually not of interest), and for this reason, this field does not need to be accompanied by the month or year like the previous fields. You can also add its dual "ID" field of integer type to be able to sort and group if necessary.

With the additions described, we could have a Time table like the one in the figure "Time Table (3)". This would be valid for a star design. For a snowflake design, the time table would have to be broken down into as many tables as there are hierarchical levels it contains. Note that the "ID" type fields are all of type integer, since it will be on these fields that most of the operations will be performed and these will be performed more efficiently on integer data.

**Time table (3)**

| TimeID (PK)   | Integer |
| :------------ | :-----: |
| Date          | Datetime|
| Year          | char(4) |
| Quarter       | char(6) |
| QuarterID     | Integer |
| Month         | char(10)|
| MonthID       | Integer |
| Fortnight     | char(10)|
| FortnightID   | Integer |
| Week          | char(10)|
| WeekID        | Integer |
| Day           | char(10)|
| DayID         | Integer |
| DayWeek       | char(10)|
| DayWeekID     | Integer |

#### Dimension granularity and hierarchies

Each dimension can refer to concepts such as Time, Products, Customers, Geographic Area, etc. Now, each dimension can be measured in different ways depending on the desired granularity. For example, for the Geographic Area dimension, we could consider 'localities', 'provinces', 'regions', 'countries' or 'continents'.

**The granularity of the fact and dimension tables must always be the same**; otherwise, the information with the highest degree of granularity will not be able to be consulted.

![004-Geographic Zone Granularity](https://i.imgur.com/vDzQjC2.png)  
_Geographic Zone Granularity (pic004)_

The unit of measurement (by localities, provinces, etc.) will determine this granularity. The smaller this unit of measurement is, the finer this granularity will be (fine grain); if the units of measurement are larger, then we will talk about coarse granularity (coarse grain).

In many cases, it is interesting to have the data at various levels of granularity, that is, it is important for the business to be able to consult the data (following the example of the zones) by localities, provinces, etc. In these cases, a hierarchy is created within the dimension, as we have several levels of association of the data (with other dimensions such as time, hierarchical levels of the type 'days', 'weeks', 'months', etc. could be created).

![005-Dimension Table Detail](https://i.imgur.com/gHgpzeR.png)  
_Dimension Table Detail (pic005)_

When the **_dimension tables_** associated with a **_fact table_** do not reflect any hierarchy (for example: Zones are always only 'provinces', time is measured only in 'days', etc.), the resulting cube will be star-shaped. In this model, there is a central **_fact table_** surrounded by as many tables as there are dimensions, and there will only be, in addition to the **_fact table_**, one table for each dimension.

![006-5-dimensional Star Schema](https://i.imgur.com/4HPriuo.png)  
_5-dimensional Star Schema (pic006)_

When one or more of the dimensions of the cube reflect some type of hierarchy, there are two approaches regarding the way dimension tables should be designed. The first consists of reflecting all the hierarchical levels of a dimension within a single table. In this case, we would also have a **_star schema_** like the one described above, that is, in the model, we would have non-normalized tables to improve performance.

The other approach consists of applying the **_RDB_** normalization rules to the dimensions. These rules are designed to avoid data redundancies by increasing the number of tables; in this way, information is stored in less space. This design results in a snowflake outline. This way of organizing the dimensions of an **_OLAP cube_** has a drawback compared to the star model that does not compensate for the savings in storage space. In OLAP applications, the critical resource is not so much the storage space as the response time of the system to user queries, and it has been proven that snowflake models have a longer response time than star models.

It is usually better to reduce dimensions in the **_DWH_** and get a **_star schema_**, rather than a **_snowflake schema_**. Therefore, **normalizing tables may be the worst solution, unlike the DBR model**.

![007-Snowflake 5-dimensional Schema](https://i.imgur.com/1Ata1st.png)  
_Snowflake 5-dimensional Schema (pic007)_

# Unzipped fact tables

Imagine the following scenario:

You have a table in a spreadsheet with the following columns, related to Sellers (named "Allowances"):

| Employee ID | Employee Name | Begin Date | End Date   | Sales Zone | Comments                      | Monthly Allowances |
| :---------: | :------------ | :--------: | :--------: | :--------- | :---------------------------- | -----------------: |
| 601         | John Smith    | 01/01/2021 | 31/03/2022 | W          | He will be there for 455 days | $456.24            |
| 601         | John Smith    | 01/04/2022 | 31/08/2022 | E          | He will be there for 153 days | $348.51            |
| 601         | John Smith    | 01/09/2022 | 31/12/2022 | C          | He will be there for 112 days | $132.97            |

You want to relate this table with the Sales **_fact table_** that comes from the **_OLTP_** database. You also want to relate these two tables in your **_DWH_**.

Additionally, you need to calculate the cost per day in allowances. For example, on January 1, 2021, John Smith gets $456.24/31 = $14.72, and on December 31, 2022, he gets $132.97/31 = $4.29 in allowances.

The Sales (**_fact table_**) has a daily granularity. Therefore, you need to ensure that this table also has the same granularity. In other words, you need to "unzip" this table to look like the following:

| Employee ID | Date       | Sales Zone | Monthly Allowances |
| :---------: | :--------: | :--------- | -----------------: |
| 601         | 01/01/2021 | W          | $14.72             |
| 601         | 01/02/2021 | W          | $14.72             |
| 601         | 01/03/2021 | W          | $14.72             |
...
| 601         | 31/12/2022 | C          | $4.29              |

How many records will this new "unzipped" table have? It will have a total of 720 records (with daily granularity), calculated as 455 + 153 + 112 = 720.

This task can be easily accomplished in **_Python_** and **_Pandas_** during the **_ETL_** process, for example, using a tool like **_Pentaho Data Integration_**.

In fact, this table is not a dimension table but a fact table. Employee ID, Date, and Sales Zone are foreign keys (**_FKs_**), and daily allowances are a metric.

With this structure, you can answer questions such as:

- How much did John Smith cost in allowances in 2021?
- How much did we pay in allowances to all the sellers in March 2022?
- How much did we pay in allowances between July 1, 2022, and September 15, 2022, in the "W" (West) sales zone?

By the way, Allowances and Sales are not directly related in the **_DWH_**; they are related through **_dimension tables_** (such as Employees, Dates, and Sales Zones).

## Denormalized fact tables

Imagine the same scenario:

You have the same table in a spreadsheet with the same columns, related to Sellers (table Allowances):

| Employee ID | Employee Name | Begin Date | End Date   | Sales Zone | Comments                      | Monthly Allowances |
| :---------: | :------------ | :--------: | :--------: | :--------- | :---------------------------- | -----------------: |
| 601         | John Smith    | 01/01/2021 | 31/03/2022 | W          | He will be there for 455 days | $456.24            |
| 601         | John Smith    | 01/04/2022 | 31/08/2022 | E          | He will be there for 153 days | $348.51            |
| 601         | John Smith    | 01/09/2022 | 31/12/2022 | C          | He will be there for 112 days | $132.97            |

We want to relate this table with the Sales **_fact table_** that comes from the **_OLTP_** database. We want, also, to relate these two tables, but now we do not have a **_DWH_**. We will establish the relationship in **_BI DM_**. Additionally, the Sales Manager wants to easily review this table (denormalized) without having to check it day by day.

Now, we cannot relate it by day with the Dates dimension table because we cannot unzip this table. We will calculate allowances through calculations, but not through relationships. This is more complicated than the previous example.

By denormalizing it, we obtain this table:

| Employee ID | Employee Name | Begin Date 01 | End Date 01  | Sales Zone 01 | Comments 01                   | Monthly Allowances 01 | Begin Date 02 | End Date 02  | Sales Zone 02 | Comments 02                   | Monthly Allowances 02 | Begin Date 03 | End Date 03  | Sales Zone 03 | Comments 03                   | Monthly Allowances 03 |
| :---------: | :------------ | :-----------: | :----------: | :------------ | :---------------------------- | --------------------: | :---------: | :-----------: | :----------: | :------------ | :---------------------------- | :-----------: | :----------: | :------------ | :---------------------------- | --------------------: |
| 601         | John Smith    | 01/01/2021    | 31/03/2022   | W             | He will be there for 455 days | $456.24               | 01/04/2022    | 31/08/2022   | E             | He will be there for 153 days | $348.51               | 01/09/2022    | 31/12/2022   | C             | He will be there for 112 days | $132.97               |

How many records will this new denormalized table have? It will have 3 records, making it much easier for the Sales Manager to review.

This task can also be easily accomplished using **_Python_** and **_Pandas_**.

Furthermore, this table is not a dimension table but a fact table. Now, Employee ID and Sales Zone are foreign keys (**_FKs_**), but we must calculate metrics in **_BI_** because Date is not an attribute in this solution.

While we can answer questions, it will require unnecessary effort in **_BI_**.

By the way, now Allowances and Sales are not directly related in **_DM_**; they are related through **_dimension tables_** (Employees, Sales Zones, but not with Dates).

# Data Warehouse (DWH), Data Lake (DL), and Data Lakehouse (DLH)

## **_DWH_**

**Characteristics**:
- The **central repository that contains structured data** (columns and rows) obtained through an **_ETL_** process from transactional systems, .CSV files, etc.

**Used by**:
- **_Business Analysts_**

**Utilized in**:
- **_Reports_**
- **_BSC_** (Balanced Scorecard)
- **_BI_** (Business Intelligence)

![008-DWH](https://i.imgur.com/1NDE7J7.png)
 _DWH (pic008)_

## **_Data Lake (DL)_**

**Characteristics**:
- The **repository of structured data** from **_ETL_** (DB tables and views, .CSV files, etc.), **semi-structured and unstructured data** (such as pictures, videos, web pages, etc.).
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

![009-DL](https://i.imgur.com/RtAStZl.png)  
 _DL (pic009)_

### DWH vs. DL

| Characteristics | DWH | DL |
| :-------------- | :-- | :- |
| Data | Optimized for analyzing related data from sources such as transactional databases, operational databases, and business applications. | Comprises unrelated data from sources such as websites, social networks, IoT devices, and mobile applications. |
| Schema | The data structure is defined before implementation to optimize queries (schema-on-write). | Stores information without defining a data structure. It allows you to implement without even knowing the business questions (schema-on-read). |
| Data Quality | Data is cleaned, enriched, and transformed to serve as the “single source of truth.” | Any data, which may or may not have undergone a cleaning and transformation process (raw data). |
| Users | Business analysts. | Data scientists, data engineers, and data analysts (when using cleaned information). | Analytics Reports, dashboards, and BI. Machine Learning, predictive analytics, data discovery, and profiling. |

## Data Lakehouse (DLH)

**Characteristics**:
- Combines the best features of both **_DWH_** and **_DL_**.
- Enables the rapid consumption of stored data for **_BI_**, reports, **_DS_** (Data Science), and **_ML_** (Machine Learning).

![010-DLH 01](https://i.imgur.com/Vu3X94C.png)  
_DLH (pic010)_

![011-DLH 02](https://i.imgur.com/a0LdoXX.png)  
_DLH (pic011)_

![012-DLH 03](https://i.imgur.com/kNFF8J3.png)  
_DLH (pic012)_

## Types of dimensional schemes

### The cube as a multidimensional representation of data

The cube is a multidimensional representation of the data stored in a **_DWH_**. The dimensions are represented by the edges of the cube (which is why you should not necessarily think in only 3D), and the facts are in each cell where they intersect.

For example, a cube could represent the fact table "Sales" and the dimension tables "Time," "Product," and "Location" (of the store). The cube is made up of cells that contain sales data associated with a specific combination of dimension values. In other words, a cell can contain the number of sales of a product in a specific month and region.

![013-DWH representation as a cube 01](https://i.imgur.com/8z58vEP.png)  
_DWH (pic013)_

![014-DWH representation as a cube 02](https://i.imgur.com/IOxAIGJ.png)  
_DWH (pic014)_

### Star schema

In the databases used for **_DWH_**, a **_star schema_** is a data model that has a fact table (**_fact_** table) containing the data for analysis, surrounded by dimension tables (**_dim_** tables). This aspect, a larger fact (or central) table surrounded by spokes or smaller tables, is what resembles a star, giving this type of construction its name.

Dimension tables will always have a simple **_PK_**, while in the fact table, the primary key (**_FK_**) will be composed of the **_PK_** of the dimension tables. ([Wikipedia](https://es.wikipedia.org/wiki/Esquema_en_estrella))

**_Advantages_**:
- Simple and easy-to-understand structure.
- Allows fast and efficient queries on large data sets.
- Facilitates multidimensional analysis and informed decision-making.
- Supports the addition of new dimensions without altering the existing fact table.

**_Disadvantages_**:
- Not suitable for transactional databases.
- Not flexible for cases requiring greater complexity in the data structure.
- Data redundancy can be a problem when dimensions overlap.
- Requires careful planning and prior analysis to determine the optimal structure of the model.

![015-Star schema](https://i.imgur.com/rnSXwhh.png)  
_Star schema (pic015)_

### Snowflake schema

In the DBs used in **_DWH_**, a **_snowflake schema_** is a somewhat more complex structure than the **_star schema_**. **_It occurs when any of the dimensions are implemented with more than one data table_**. **_The purpose is to normalize the tables and thus reduce storage space by eliminating data redundancy, but it has the counterpart of generating worse performance_** due to the need to create more dimension tables and more relationships between the tables (JOINs), which directly impacts performance. ([Wikipedia](https://es.wikipedia.org/wiki/Esquema_en_copo_de_nieve))

![016-Snowflake schema](https://i.imgur.com/eF9u4nd.png)  
_Snowflake schema (pic016)_

![017-Dimensional schema types](https://i.imgur.com/z79R1m2.png)  
_Dimensional schema types (pic017)_

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

**_Very important!_**: Models should never be related through fact tables; that is, **we should never relate two fact tables directly**. Always, the relationships between models are carried out through the dimensions they share.


## Picture list

- **pct001**: From "Data Warehousing and OLAP modeling" Platzi course.
- **pct002**: From "Data Warehousing and OLAP modeling" Platzi course.
- **pct003**: From "Data Warehousing and OLAP modeling" Platzi course.
- **pct004**: From Wikipedia.
- **pct005**: From "Data Warehousing and OLAP modeling" Platzi course.
- **pct006**: From "Data Warehousing and OLAP modeling" Platzi course.
- **pct007**: From "Data Warehousing and OLAP modeling" Platzi course.
- **pct008**: From "Data Warehousing and OLAP modeling" Platzi course.
- **pct009**: From "Data Warehousing and OLAP modeling" Platzi course.
- **pct010**: From "Data Warehousing and OLAP modeling" Platzi course.
- **pct011**: From "Data Warehousing and OLAP modeling" Platzi course.
- **pct012**: From "Data Warehousing and OLAP modeling" Platzi course.
- **pct013**: From "Data Warehousing and OLAP modeling" Platzi course.
- **pct014**: From "Data Warehousing and OLAP modeling" Platzi course.
- **pct015**: From "Data Warehousing and OLAP modeling" Platzi course.
- **pct016**: From "Data Warehousing and OLAP modeling" Platzi course.
- **pct017**: From "Data Warehousing and OLAP modeling" Platzi course.

## Disclaimer

**_Platzi_** (formerly Mejorando.la) is a LATAM online education platform. It was founded in 2011 by the Colombian engineer **Freddy Vega** and the Guatemalan computer scientist **Christian Van Der Henst**. [Wikipedia](https://es.wikipedia.org/wiki/Platzi)

## ChatGPT usage

**_ChatGPT 3.5 Usage_**

This project has been verified for spelling, syntax, and content using [**_ChatGPT 3.5_**](https://chat.openai.com/chat)  

Reference:  
[OpenAI. (2023). ChatGPT (09/19/2023 version) \[Large Language Model\]](https://chat.openai.com/chat)