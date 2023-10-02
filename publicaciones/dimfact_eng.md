# Dimensional Modeling (Dimensions and Facts)

To build a **_dm_**, it is necessary to consider a set of techniques and concepts to design our **_DWH_**. A fundamental part of this is the type of tables where we store the data.

- **_Fact tables_**
    - What we want to measure or analyze.
    - They are the main tables of the **_dm_**.
    - They contain foreign keys (**_FK_**) to relate to the **_dimension tables_**.
    - They contain metrics (also called measures), which represent what we want to measure or analyze.
    - They are generally numerical values.
    - They avoid redundancy of attributes in the **_dimension tables_**.
    - They usually have many (millions) of records.
    - Examples: Sales, Purchases, Accounting Movements, Warehouse Movements, etc.

- **_Dimension Tables_**
    - How we want to measure what the **_fact tables_** contain.
    - They are simple and normalized tables.
    - They contain primary keys (**_PK_**) to relate to the **_fact tables_**.
    - The attributes are characteristics of the **_fact tables_**.
    - There is no limit to their number in the **_DWH_**.
    - They can contain one or several hierarchical relationships.
    - They have fewer records than the **_fact tables_**.
    - Examples: Customers, Products, Warehouses, Suppliers, Calendar, etc.

<p><br></p>

## Types of Attributes

- **_Hierarchical_**: Allow transitioning from the general to the specific or consolidating and disaggregating. Examples: country, etc.
- **_Descriptive_**: Relevant information that is purely descriptive. Examples: address, phone, size, etc.
- **_Control_**: Audit data that does not belong to business knowledge. Examples: date of data recording, etc.

<p><br></p>

## Facts (Fact Tables)

Facts are a representation of a business process. In terms of design, they allow you to store two types of differentiated attributes:
- Metrics of the process/activity/workflow/event being modeled.
- **_FKs_** (foreign keys) that point to records in a **_dimension table_** (business view).

### Transactional Facts (TFT)

These allow analyzing data in the highest level of detail. For example, in a Sale, the metrics may include the amount of the sale.

### Factless Fact Tables/Coverage Tables (FFT/CT)

These are tables that do not contain metrics but make sense as they represent the occurrence of an event. Counters are often added to these tables to facilitate SQL queries. For example, in a charity event, each attendee can generate a record even if there is no associated metric.

### Periodical Snapshot Facts (PSFT)

These are used to collect information at regular time intervals. Depending on the measured situation or business needs, these types of **_fact tables_** can be an aggregation of **_FFT/CT_** or designed specifically. For example, a monthly balance where data is collected cumulatively on a monthly basis.

### Accumulating Snapshot Facts (ASFT)

These represent the complete life cycle of an activity or process, with a beginning and an end. They are characterized by including multiple dimensions related to events in a process. For example, a student enrollment process that collects data throughout the entire lifespan, replacing previous data (such as course completion and grade collection).

### X Facts (real-life cases not described in literature)

These are **_fact tables_** that we have to build in real life to enhance their usability for the users who feed them. Furthermore, we need to transform them to make them manageable in our **_dm_**.

#### Compressed Fact Tables

Let's imagine the following scenario:

We have a table in an Excel spreadsheet on our server, which must be maintained by a Sales Manager of the company. After an **_ETL_** process, we will integrate this table with the tables of the **_OLTP_** database in our **_DWH_**.

In this table, the following will be recorded:
- Salesperson ID (must be the same as in the **_OLTP_** database).
- Salesperson's name (added to improve the table's usability).
- Start date (period).
- End date (period).
- Unspecified daily monetary incentives (incentives in money for the individual salesperson, not included in the **_OLTP_** database).
- Unspecified daily in-kind incentives (incentives in kind for the individual salesperson, not included in the **_OLTP_** database).

To implement this **_fact table_** in our **_dm_**, we must have the same granularity between the foreign keys (**_FKs_**) in our table and the associated dimensions. The granularity in our **_dm_** for the Time **_dimension table_** is **day**, so we need to transform our **_fact table_** so that its records are daily (not in periods).

Note that if we transform periods into days (while keeping the data in the other columns), we do not lose information. We will call this process **_fact table decompression_**.

The transformed **_fact table_** in our **_DWH_** would look like this:
- Salesperson ID.
- Salesperson's name.
- Date.
- Unspecified daily monetary incentives.
- Unspecified daily in-kind incentives.

#### Normalized Fact Tables

Let's imagine the same scenario but with a different approach: We do not have a **_DWH_**.

In the table, the Sales Manager will record the same information as in the previous case (our solution will be transparent to them):
- Salesperson ID (must be the same as in our **_OLTP_** database).
- Salesperson's name (added to improve the table's usability).
- Start date (period).
- End date (period).
- Unspecified daily monetary incentives (incentives in money for the individual salesperson, not included in the **_OLTP_** database).
- Unspecified daily in-kind incentives (incentives in kind for the individual salesperson, not included in the **_OLTP_** database).

To implement this **_fact table_** in our **_dm_**, but now without considering the granularity level of the Time **_dimension table_** because we cannot implement a **_DWH_**, we must transform the rows that repeat the Salesperson ID into repetition groups (non-normalized).

Note that if we perform this transformation (which can be avoided if we have a **_DWH_**), we will need to continuously compare the period with the date for the same Salesperson ID to determine if the facts apply. In other words, we cannot relate the **_fact table_** to the Time **_dimension table_**, but we can relate it to the Salespersons dimension.

The transformed **_fact table_** in our **_DWH_** would look like this:
- Salesperson ID.
- Salesperson's name.
- Period 1.
- Start date (period).
- End date (period).
- Unspecified daily monetary incentives.
- Unspecified daily in-kind incentives.
...
- Period n.
- Start date (period).
- End date (period).
- Unspecified daily monetary incentives.
- Unspecified daily in-kind incentives.

**_Very important!_**: **As Data Analysts/Engineers, we must demand the implementation of a DWH (whenever necessary)**.

<p><br></p>

## Dimensions (dim tables)

Dimensions collect the points of analysis for a fact, meaning they are the questions we ask to understand the facts.

For example, a Sale can be analyzed in relation to the sale date, product, customer, salesperson, sales channel, and so on.

Classification of **_dimension tables_**:

- **_Time Dimension_** (special)
- **_Historical Classification_**
	- Slowly Changing Dimensions (**_SCD_**)
		- **_Type 0_**: Preserves the original.
		- **_Type 1_**: Overwrites the updated attribute, meaning no historical data is saved.
		- **_Type 2_**: Adds a new record with the change (row).
		- **_Type 3_**: Adds a new "previous" attribute (column).
		- **_Type 4_**: Separate history (*mini-dimension*).
		- **_Type 5_**: SCD-1 + SCD-4 = SCD-5.
		- **_Type 6_**: SCD-1 + SCD-2 + SCD-3 = SCD-6.
		- **_Type 7_**: Adds an **_SCD-2_** dimension row with a new attribute value, in addition to limited visualization of current rows and/or attribute values.
	- Dimensions that change rapidly (**_Rapidly Changing Dimensions - RCD -_**)
		- Monster Dimensions (**_MONSDIM_** -)
		- Bridge Dimensions (**_BRIDDIM_**)
- **_Functional Classification_**
	- Shrunk Dimensions (**_SHRKDIM_**)
	- Conformed Dimensions (**_CONFDIM_**)
	- Junk Dimensions (**_JUNKDIM_**)
	- Role-playing Dimensions (**_RPLYDIM_**)
	- Degenerate Dimensions (**_DEGEDIM_**)
	- Outrigger Dimensions (**_OUTGDIM_**)
	- Static Dimensions (**_STATDIM_**)
	- Stacked Dimensions (**_STACDIM_**)
	- Deferred Dimension (**_DEFEDIM_**)
	- Distorted Dimension (**_DISTDIM_**)

### Time Dimension (Calendar DateTime Dimension - CDTD -)

In any **_DWH_**, you can find various cubes with their **_fact tables_** containing records with variables of interest to the business that need to be studied. Also, as mentioned earlier, each **_fact table_** will be surrounded by various **_dimension tables_** with parameters that will allow us to analyze the facts we want to study. One parameter that is likely to be present in all cubes is Time since it is common to store facts as they occur over time, thereby obtaining a time series of the variable under study.

Since Time is a dimension present in practically any cube of an **_OLAP_** system, it deserves special attention. When designing the Time dimension (whether for a **_star schema_** or a **_snowflake schema_**), special care should be taken because it can be done in various ways, and not all of them are equally efficient. The most common way to design this table is by using the date or datetime as the **_PK_** of the table (time table 1). This design is not the most recommended because in most database management systems, searching on fields of type "date" or "datetime" is more costly. These costs are reduced if the primary key field is of integer type. Furthermore, an integer data always occupies less space than a date data (the primary key will be repeated in millions of records in the **_fact table_**, which can take up a lot of space). Therefore, the design of the time table will be improved if an "TimeID" field of integer type is used as the primary key (time table 2).

<p><br></p>

*Time table (1)*

| Date (PK)   | datetime |
| :---------  | :------: |
| Year        | char(4)  |
| Quarter     | char(6)  |
| Month       | char(10) |

<p><br></p>

*Time table (2)*

| TimeID (PK) | integer  |
| :----------- | :------: |
| Date         | datetime |
| Year         | char(4)  |
| Quarter      | char(6)  |
| Month        | char(10) |

<p><br></p>

When filling the Time table, if you have opted for an integer field as the key, there are two options: the most immediate one is to assign consecutive numeric values (1, 2, 3, 4...) to different date values. The other option would be to assign numeric values of the type "yyyymmdd," meaning that the first four digits of the field value indicate the year of the date, the next two indicate the month, and the last two indicate the day. This second method has certain advantages over the first, as it allows the numeric data itself to provide information about the date. For example, if the **_fact table_** contains the value 20040723, we would know that it refers to July 23, 2004. In contrast, with the first method, you could find values like 8456456, and to know the date to which this value refers, you would need to query the Time table.

In addition to the TimeID primary key field, the **_fact table_** should also contain other fields that are important to consider. These fields would be:
- A "year" field: This would contain values like '2002', '2003', '2004'...
- A "month" field: Here, values like 'January', 'February'... can be placed (or abbreviated as 'Jan', 'Feb'...). Although this is not incorrect, it can be improved if the month name is accompanied by the year it belongs to. For example: 'January 2004', 'February 2004'... This way, the search for values of a specific month is optimized because if you are looking for values for "January 2003," all this information is contained in a single field, the "month," and there is no need to also query the year field.
- A "monthID" field: This field should be of integer type and would be used to store values like 200601 (for 'January 2006') or 200602 (for 'February 2006'). This way, it is possible to perform aggregations and groupings by months.

Similarly to what was done with the month field, more fields like **"Quarter", "Fortnight", "Week"** of text type could be added for visualization, as well as their integer type counterparts "QuarterID", "FortnightID", "WeekID" for performing aggregations and groupings if necessary. In general, a field can be added for each desired level of granularity.

Another special field that can be added is the **"Day of the week"** ('Monday', 'Tuesday'...). This field is usually added to study the behavior of days of the week in general (not the first Monday of January in a specific year, as this type of study often lacks interest). For this reason, this field does not need to be accompanied by the month or year like the previous fields. Its integer type dual "ID" field can also be added for ordering and grouping if necessary.

With the described additions, we could have a Time table like the one shown in the figure (time table 3). This would be valid for a **_star schema_** design. For a **_snowflake schema_** design, we would need to break down the Time table into as many tables as it contains hierarchical levels. Note that the "ID" fields are all of integer type, as most operations will be performed on these fields, and they will be more efficient with integer data.

<p><br></p>

*Time table (3)*

| TimeID (PK) | integer  |
| :------------ | :------: |
| Date         | datetime |
| Year         | char(4)  |
| Quarter      | char(6)  |
| QuarterID    | int      |
| Month        | char(10) |
| MonthID      | int      |
| Fortnight    | char(10) |
| FortnightID  | int      |
| Week         | char(10) |
| WeekID       | int      |
| Day          | char(10) |
| DayID        | int      |
| DayOfWeek    | char(10) |
| DayOfWeekID  | int      |

**_Important note!_**: Some BI tools, like **_Power BI_**, incorporate automatic systems for generating this dimension, but conducting a preliminary study will help avoid subsequent problems and inconsistencies (which are often difficult to resolve). Additionally, if we perform other types of analysis or visualizations, for example, in **_Python_** (outside of **_Power BI_**), then we will need to design the Time dimension properly ourselves.

<p><br></p>

### Shrunken Dimensions (SHRKDIM)

**_SHRKDIMs_** come into play when we need to display data aggregation from our **_DWH_**. In other words, we need to show a higher granularity than what we have in the **_DWH_**.

A classic example is when we need to display monthly data, even though we have daily data stored in the **_DWH_**.

Another less intuitive example would be considering Sales by City. In this case, we would create a City **_SHRKDIM_** that relates to the fact table (Sales).

In essence, **_SHRKDIMs_** create what we could liken to lookup tables (in a DB **_OLTP_** model). In the **_DDM_**, we always need to think about what questions we need to ask the fact table to meet business needs (that should be our logic). If we can't answer them with the existing **_dimension tables_**, we might create dimensions with very few columns (**shrunken** ones) to address them, and these would be our **_SHRKDIMs_**.

#### Example of Using Shrunken Dimensions (SHRKDIM)

Let's assume that we have a **_DWH_** (Data Warehouse) that stores Sales data for a chain of stores. In this **_DWH_**, we have a primary **_fact table_** called "Sales" that stores detailed information about each individual sale, such as the sale date, the sold product, quantity, price, and more.

However, we also have a need to perform analysis at both the monthly and city levels. This means that we need to display aggregated Sales data by Month and by City, even though detailed data is stored daily in the "Sales" **_fact table_**.

In this scenario, we could create two Shrunken Dimensions (**_SHRKDIM_**):

1. **Month Dimension (SHRKDIM_Month)**: This dimension would contain information about the months, such as the month name, month number, and any other relevant information. It would be a reduced table compared to the "Sales" **_fact table_** and would be related to it through the sale date. This would allow us to perform analysis and queries at the monthly level.

2. **City Dimension (SHRKDIM_City)**: This dimension would contain information about the cities where the stores operate, such as city name, postal code, and other details. Similar to the Month Dimension, it would be a reduced table compared to the "Sales" **_fact table_** and would be related to it through the store's location. This would enable us to perform analysis and queries at the city level.

With these shrunken dimensions in place, we could answer questions such as:

- **"What was the total amount of sales in July 2023 in all cities?"**
- **"What was the average daily sales in New York city during the last quarter?"**

These questions involve aggregations at the Month and City levels, and the shrunken dimensions allow us to efficiently and consistently perform these queries, even if detailed data is stored on a daily basis in the "Sales" **_fact table_**.

**_Important Note_**: Some **_BI_** tools, like **_Power BI_**, offer the option to create segmentations from **_fact tables_** without extracting **_SHRKDIMs_** from them. This can result in an incomplete **_DDM_** in the **_DWH_**, especially if you decide to explore the data in another environment like **_Python_** (outside of **_Power BI_**).

<p><br></p>

### Conformed Dimensions (CONFDIM)

Conformed Dimensions allow for the sharing of information across dimensions, enabling joint queries.

In a **_DWH_**, a **_CONFDIM_** is a dimension that holds the same meaning for every fact it relates to. **_CONFDIMs_** enable the categorization and description of facts and measures in the same way across multiple **_fact tables_** or Data Marts (**_DM_**), ensuring report consistency throughout the entire organization.

A **_CONFDIM_** can be referenced by multiple **_fact tables_** within a DWH. The dimension is implemented as a single dimension table that is referenced by **_several fact tables_** or as multiple **_dimension tables_** referenced by **_fact tables_** in different **_DMs_** within the same **_DWH_**. It's also possible for multiple **_fact tables_** within a single **_DM_** to reference the same **_CONFDIM_** table, as well as a **_fact table_** in other **_DMs_**.

The most classic example of a **_CONFDIM_** is the Time table since we can inquire about monthly Sales, employee Productivity Plans for the upcoming month, or Warehouse Entries on the 15th of last month. In all cases, even if it's from another departmental **_DM_**, these are questions we ask from our Time table (**_dimension table_**) to different **_fact tables_** (in one or more **_DMs_**, if we have them in our **_DWH_**).

**_Attention!_**: If we haven't asked the right business questions, we might overlook dimensions that, if they are generalized enough in our **_DWH_**, should be considered **_CONFDIMs_**.

#### Example of using Conformed Dimensions (CONFDIM) with Data Marts

Imagine we have a large **_DWH_** that stores data for a retail company. Within this **_DWH_**, there are multiple Data Marts (**_DMs_**) for different departments, such as Sales, Marketing, and Inventory Management. Each Data Mart focuses on specific business aspects and has its own **_fact tables_**.

Let's consider the Sales **_DM_**, which contains detailed Sales information. One of the primary **_fact tables_** in this **_DM_** is "SalesTransactions," which records each individual sale's data, including product sold, customer information, date, and amount.

Now, the Marketing **_DM_** is interested in analyzing the sales data to understand Customer behavior and trends. They want to know, for example, which products are frequently bought together by Customers. To do this, they need to share dimension Data, such as Product and Customer, with the Sales **_DM_**.

Here's how **_CONFDIM_** are used in this scenario:

1. **Product Dimension (CONFDIM_Product)**: This dimension contains information about the Products, such as product name, category, and manufacturer. It is maintained consistently across both the Sales **_DM_** and the Marketing **_DMs_**. The Sales **_DM_** relates the "SalesTransactions" **_fact table_** to this dimension, while the Marketing **_DM_** uses the same dimension to analyze Product-related data.

2. **Customer Dimension (CONFDIM_Customer)**: This dimension includes Customer information like name, address, and contact details. Similar to the Product Dimension, it's shared between the Sales and Marketing **_DMs_**. The Sales **_DM_** links the "SalesTransactions" **_fact table_** to this dimension, while the Marketing **_DM_** uses it to understand Customer preferences and behavior.

With **_CONFDIM_** in place, the Marketing **_DM_** can perform analyses like identifying Product affinity and segmenting Customers effectively, as they can rely on consistent dimension data shared with the Sales **_DM_**.

Example query in the Marketing **_DM_**:

- **"Which Products are often purchased together, and which Customer segments show this behavior?"**

By leveraging **_CONFDIM_**, these **_DMs_** ensure consistency and data integrity when sharing dimension information, allowing different departments within the organization to perform cross-functional analyses and gain valuable insights.

<p><br></p>

### Junk Dimensions (JUNKDIM)

They contain volatile information that is used occasionally and is not usually saved permanently in the **_DWH_**.

- They reduce the number of dimensions (columns with low cardinality) in the dimensional model and decrease the number of columns in the **_fact table_**. It's a collection of random transactional codes, flags, or text attributes.

- They can optimize space, as **_fact tables_** *should not include text fields or low cardinality fields* (**a highly questionable practice**). They mainly include measures, foreign keys, and degenerate dimension keys (**_DEGEDIMs_**).

<p><br></p>

Example with a **_fact table_** of Cars and two **_dimension tables_** of Colors and Model.

![Without JUNKDIM](https://i.imgur.com/hYcP0rU.png)  
_Without JUNKDIM_

<p><br></p>

Example with a **_fact table_** of Cars and a **_dimension table_** of Car Features.

![With JUNKDIM](https://i.imgur.com/dw1nykb.png)  
_With JUNKDIM_

<p><br></p>

**_Note_**: It is at least a questionable practice, given that we are interested in denormalizing dimensions and giving meaning to the metrics of **_fact tables_**. In the exposed case, the most recommended practice would be to consider Cars as a **_dimension table_** of a Sales **_fact table_**, and that's it.

#### Example of using Junk Dimensions (JUNKDIM)

Let's suppose we have a Data Warehouse (**_DWH_**) that stores product sales data. In this **_DWH_**, we have a primary **_fact table_** called "Sales" that stores detailed information about each transaction, such as date, product sold, customer, quantity, price, among others.

Now, we need to perform specific analyses that involve the combination of certain low cardinality attributes, such as product colors and product characteristics. These attributes do not justify having an independent dimension due to their low cardinality and volatile nature.

Instead of creating separate dimensions for product colors and characteristics, we could opt for a **_junk dimension (JUNKDIM)_** called "Product Attributes." This dimension would contain a collection of random codes or flags representing combinations of colors and characteristics.

Here's how the data structure would look:

- **_Fact table_** "Sales": It relates to the **_JUNKDIM_** "Product Attributes" through a **_FK_**.

- **_JUNKDIM_** "Product Attributes": It contains combinations of colors and characteristics represented by codes or flags.

With this structure, we could answer questions such as:

- **"What is the total quantity of Products sold that have the characteristics 'Red' and 'Water-resistant'?"**
- **"What is the average price of Products of the color 'Blue'?"**

**_JUNKDIMs_** allow us to simplify the **_DM_** and optimize space, as we would not have to create separate dimensions for every possible combination of product colors and characteristics.

### Role-Playing Dimensions (- RPLYDIM -)

**_RPLYDIMs_** have assigned meaning. That is, a **_RPLYDIM_** is a dimension that refers to multiple purposes within a **_fact table_**.

The best example for a **_RPLYDIM_** is once again a **_dimension table_** of Time since the same date attribute in the dimension can be used for different purposes within the same **_fact table_**. For example, in the Sales **_fact table_**, the order date, delivery date, transaction date, cancellation date, and delivery date can all be recorded. All of this data is related to the date in the Time **_dimension table_**.

Challenges in handling:

1. They can increase the complexity and size of the **_fact table_** since each **_RPLYDIM_** adds more columns to the **_fact table_**.

2. They can create confusion and inconsistency among users and analysts because different roles may have different meanings or interpretations for the same dimension attribute. That is, if we only mention "date" (for example), it can have many interpretations depending on the role.

3. They can be difficult to maintain and update because any changes to the source of the dimension table can affect multiple **_RPLYDIMs_** and **_fact tables_**. Therefore, it is essential to have them very clear throughout the **_pipeline_**.

4. In some Business Intelligence (BI) tools, such as Power BI, it only allows having an active relationship with the same primary key (**_PK_**) of the RPLYDIM, even if they are different foreign keys (**_FKs_**) in the **_fact table_**. This is simply a tool implementation issue (in Power BI, with DAX) and is resolved correctly in the tool.

Here is a good solution for **_RPLYDIMs_** in **Power BI** with **DAX**:

````DAX
[Total Sales by Order Date] :=   
    CALCULATE( 
        SUM(Orders[Line Total]),
        USERELATIONSHIP(Orders[Order Date], Dates[Date])
    )

[Total Delivered Sales] := 
    CALCULATE( 
        SUM(Orders[Line Total]),
        USERELATIONSHIP(Orders[Delivery Date], Dates[Date])
    )
````

etc.

Other solutions can complicate our lives, such as setting up different views (in our **_DWH_**) of the Time **_dimension table_** for each of the Foreign Keys (**_FKs_**) in the **_fact table_**, meaning for each of the defined **_RPLYDIMs_**. This significantly complicates relationships and segmentations in the exploitation of the **_DDM_** in the **_BI_** (e.g.).

**_Attention!_**: It is essential to define **_RPLYDIMs_** properly for our **_pipeline_** to work well. This implies that, as **Data Engineers**, we need to understand, in the **_pipeline_** of our project, from the client's business to the final solution where we will display the results.

#### Example of using Role-Playing Dimensions (RPLYDIM)

Let's assume we have a Data Warehouse (**_DWH_**) that stores Sales data of products. In this **_DWH_**, we have a primary **_fact table_** called "Sales" that stores detailed information about each transaction, such as order date, shipping date, delivery date, the product sold, customer, quantity, price, and more.

Additionally, we want to implement the solution in **_Power BI_**.

Now, we need to perform specific analyses involving time attributes. Each of these attributes has a relationship with the Time dimension.

Instead of creating separate dimensions for dates, we could opt for a **_Role-Playing Dimension (RPLYDIM)_**, and we wouldn't need a distinct table for the Time **_dimension table_**.

Challenges in handling this:

1. In **_Power BI_**, we cannot maintain more than one relationship with the same field, so we need to selectively implement the relationship in DAX, depending on our needs.

2. In other systems, the implementation may involve creating multiple Time **_dimension tables_** for each attribute of the **_fact table_** with which we want to establish a relationship.

In our case, depending on the metric we want to obtain, we would use codes like the following:

````DAX
[Total Sales by Order Date] :=
    CALCULATE(
        SUM(Orders[Line Total]),
        USERELATIONSHIP(Orders[Order Date], Dates[Date])
    )

[Total Sales by Ship Date] :=
    CALCULATE(
        SUM(Orders[Line Total]),
        USERELATIONSHIP(Orders[Ship Date], Dates[Date])
    )

[Total Sales by Delivery Date] :=
    CALCULATE(
        SUM(Orders[Line Total]),
        USERELATIONSHIP(Orders[Delivery Date], Dates[Date])
    )
````

Here's how the data structure would look:

- **_Fact Table_** "Sales": It relates to the **_RPLYDIM_** "Dates" through the 3 **_FKs_**.

- **_RPLYDIM_** "Time Attributes": In our case, it's the Time **_dimension table_**.

With this structure, we could answer questions like:

- **"What is the total quantity of Products sold that were shipped in the month of March?"**
- **"In which cities were more deliveries made in August?"**

**_JUNKDIMs_** allow us to simplify the **_dm_** and optimize space, as we wouldn't have to create separate dimensions for each related attribute (**_FK_**).

### Degenerate Dimensions (DEGEDIM)

These are found as attributes in the **_fact table_** and have meaning from an analytical perspective. They contain low cardinality information formed by dichotomous relationships. Often, they contain only one attribute and therefore a separate table is not usually created for them.

In other words, they are attributes that cannot be dimensions or facts (metrics) but require analysis. All these attributes, when removed from the fact table, are moved to **_DEGEDIMs_**.

For example, attributes like order number, invoice number, patient gender, etc., can be considered as attributes of **_DEGEDIMs_**.

An invoice may have a customer name attribute, but it already belongs to the Customer **dimension table**.

Therefore, **_DEGEDIM_** is a part of the **fact table** that is not a metric but still remains a dimension, which is contradictory.

It can be defined as **_DEGEDIM_** for an attribute in a **_fact table_** that acts as a dimension but does not actually join with any dimension (it is not an **_FK_** of another table) since its attributes have already been included in other analysis dimensions.

**Note**: Defining **_DEGEDIMs_** outside of a **fact table** is an error.

#### Example of Degenerate Dimensions (DEGEDIM)

Let's assume we have a Sales database that includes a **fact table**, which records product sales. In this **_fact table_**, we have the following information:

- Order number
- Date
- Product
- Quantity
- Price
- Online sale

Now, let's imagine that Sales can be online or not, so this attribute has low cardinality, is not aggregable, and is not an **_FK_** either. On the other hand, the order number of the Sale has low cardinality compared to Sales (one sale has more than one order), and it is also not aggregable nor an **_FK_**.

Therefore, in the **_fact table_** (Sales), the order number and online sale are **_DEGEDIMs_**, and they will only serve us to be clear that we have not made a mistake, but we will not build a separate dimension for them.
