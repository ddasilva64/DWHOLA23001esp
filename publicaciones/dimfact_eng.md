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

### Outrigger Dimensions (OUTGDIM)

Dimensions that reference other dimensions through **_FKs_** (subdimensions).

**_OUTGDIM_** are often considered an antipattern in **_DWH_** and it is usually considered a better practice to use some **_fact tables_** that relate the two dimensions.

**_Warning!_**: We should try to avoid **_OUTGDIM_** in our **_DDM_**.

#### Example of Outrigger Dimensions (OUTGDIM)

Let's consider Sales, Products, and Categories.

- **_Fact Table_**: Sales.
- **_Dimension Table_**: Products **_OUTGDIM_** (contains the **_FK_** of the Category).
- **_Subdimension Table_**: Categories (contains the **_PK_** of the Category).

Another way to view them is as:

- **_Fact Table_**: Sales.
- **_Dimension Table_**: Products. Each product contains its embedded Category.

The second model is **denormalized** and is **a better solution** for a **_DDM_**.

## Static or Fixed Dimensions (Static Dimensions - STATDIM -)

They come from nothing, they are dimensions that do not exist in the **_OLTP_** source database as entities.

Static dimensions are generated using an **_SQL_** script, a stored procedure, or an external file, and they are created manually.

Since they are not extracted from the **_OLTP_** data source, it can be considered that they are created in the context of the **_DWH_**.

A very healthy practice is not to create them in the **_DWH_**, but in a spreadsheet on the corporate server.

A dimension that does not exist in the **_OLTP_** does not necessarily have to be a **_STATDIM_**. But if we place static and dynamic dimensions that do not exist in the **_OLTP_** in a document (such as Excel) on the server and make them available to the user, then they will be aware of the static entities. If we create the **_STATDIM_** only in the **_DWH_**, this could likely generate distrust in the user because they do not see them.

**_Warning!_**: We should not trust users (Directors or Managers). If entities are missing in the **_OLTP_** database (which will be transformed into dimensions in the **_DDM_**), we must add them before the **_DWH_** and relate them to the **_DWH_**.

#### Example of static or fixed dimensions (Static Dimensions - STATDIM -)

An example that we will always have is the **_Time dimension table_**; another example is state codes.

Other examples (not static, even though they do not exist in the **_OLTP_** database) can be any entity that does not exist in the **_OLTP_** database.

## Slowly Changing Dimensions (SCD)

A **_SCD_** in a **_DWH_** is a dimension that contains relatively static data that can change slowly and unpredictably, rather than following a regular schedule. Some examples of typical dimensions that change slowly are entities such as Geographic Locations, Customers, or Product Names. ([Wikipedia](https://en.wikipedia.org/wiki/Slowly_changing_dimension)).

<p><br></p>

![001-Dimension](https://i.imgur.com/kXDzc6e.png)  
_Dimension (picture 001)_

<p><br></p>

#### Types of SCD

In a dimensional database, **_SCDs_** are those dimensions that change over time, but not necessarily in a constant or predictable manner. **For example, in a Customer data table, the customer's address can change over time, but not all customers change their address at the same rate. Some customers may change their address every month, while others may maintain the same address for years**.

Proper management of **_SCDs_** is important to maintain the accuracy and integrity of dimensional data in a database, as it allows users to perform historical analysis and compare data over time.

#### Choosing the Type of SCD

The choice of which type of **_SCD_** to use depends on the specific needs of the **_DWH_** and analysis requirements. It is important to consider factors such as the importance of historical data, the frequency of changes in dimensions, and the storage and performance implications of each approach.

**_SCDs_** are a crucial aspect of **_DWHs_** as they enable representing data over time, thereby facilitating accurate historical analysis and reporting.

#### SCD-0

**_SCD-0_** does not consider the management of historical changes. It is applied when the information is never changed, meaning attributes in **_SCD-0_** never change and are assigned to attributes that have lasting values or are described as "**originals**".

It applies to most attributes of dimensions.

What this all means is that, **since there are no changes in the original table, there are also no changes in the dimension**.

##### Example of SDC-0

Examples: Date of birth, original credit score. 

#### SCD-1

**_SCD-1_** does not keep historical records. New information always overwrites the old. The overwriting is mainly done due to data quality errors. This type of dimension is easy to maintain and is used when historical information is not important. In other words, **it is appropriate when historical data is not relevant or when it can be retrieved from other sources**.

##### Example of SDC-1

Let's imagine the record of a student at a university, and then, because they reconsider, they change their major.

**_Transactional table at the time of registration (day 1)_**

| **Student_ID** | **Full Name** | **Major** |
| :------------: | :------------ | :-------: |
| EST12345       | John Smith    | Marketing |

**_Dimensional table at the time of registration (day 1)_**

We create a Student_ID, which is a surrogate key - **_SK_** - aimed at improving performance in dimension searches with a numerical key.

| **Student_ID** | **Student_Code** | **Full Name** | **Major** |
| :------------: | :--------------: | :-----------  | :-------: |
| 1              | EST12345         | John Smith    | Marketing |

On day 2, John Smith changes his major to Engineering (he reconsidered).

**_Transactional table at the time of the major change (day 2)_**

| **Student_ID** | **Full Name** | **Major**   |
| :------------: | :------------ | :---------: |
| EST12345       | John Smith    | Engineering |

**_Dimensional table at the time of the major change (day 2)_**

| **Student_ID** | **Student_Code** | **Full Name** | **Major**   |
| :------------: | :--------------: | :-----------  | :---------: |
| 1              | EST12345         | John Smith    | Engineering |

What all this means is that, despite there being changes, **each time all records from the transactional table are imported (overwriting those that existed previously in the dimensional table)**. In other words, the records in the dimensional table are the "**originals**" in the transactional table, without changes.

#### SCD-2

**_SCD-2_** stores historical information in the **_DWH_**.

When there is a change, a new entry is created with its appropriate date and **_SK_**.

**_SCD-2_** **is usually used when historical analysis is required and the dimension changes relatively infrequently**. When there is any change in the values of the records, **a new row will be added**, and the data related to the change history will need to be completed.

##### Example of SDC-2

Let's imagine the same previous example, but this time, we want to store historical data.

**_Enrollment_**

**_Transactional table at the time of registration (day 1)_**

| **Student_ID** | **Full Name** | **Major** |
| :------------: | :------------ | :-------: |
| EST12345       | John Smith    | Marketing |

**_Dimensional table at the time of registration (day 1)_**

| **Student_ID** | **Student_Code** | **Full Name** | **Major** | **Start_Date** | **End_Date** | **Version** | **Current** |
| :------------: | :--------------: | :-----------  | :-------: | :------------: | :---------:  | :---------: | :---------: |
| 1              | EST12345         | John Smith    | Marketing | 01/01/2020     |              | 1           | True        |

On day 2, John Smith changes his major to Engineering (he reconsidered).

**_Transactional table at the time of the major change (day 2)_**

| **Student_ID** | **Full Name** | **Major**   |
| :------------: | :------------ | :---------: |
| EST12345       | John Smith    | Engineering |

**_Dimensional table at the time of the major change (day 2)_**

| **Student_ID** | **Student_Code** | **Full Name** | **Major**   | **Start_Date** | **End_Date** | **Version** | **Current** |
| :------------: | :--------------: | :------------ | :---------: | :------------: | :---------:  | :---------: | :---------: |
| 1              | EST12345         | John Smith    | Marketing   | 01/01/2020     | 01/01/2020   | 1           | False       |
| 2              | EST12345         | John Smith    | Engineering | 02/01/2020     |              | 2           | True        |

This means that **each time there is a change, all records from the transactional table are imported, and a new row is added to the dimensional table**, maintaining the historical data. The "Current" column indicates the current version of the record.

#### SCD-3

**_SCD-3_** stores historical information in the **_DWH_**.

**_SCD-3_** is used **when it's important to track specific attribute changes while maintaining simplicity in the data model**. It **requires adding** an additional column **to the dimension table** for each column whose values need to be maintained as a history of changes.

##### Example of SCD-3

Let's imagine the same previous example but with the desire to store historical data.

**_Enrollment_**

**_Transactional table at the time of registration (day 1)_**

| **Student_Id** | **Full Name** | **Faculty** |
| :------------: | :------------ | :---------: |
| EST12345       | John Smith    | Marketing   |

**_Dimensional table at the time of registration (day 1)_**

| **Student_Id** | **Student_Code** | **Full Name** | **Old_Faculty** | **New_Faculty** |
| :------------: | :--------------: | :------------ | :-------------: | :-------------: |
| 1              | EST12345         | John Smith    |                 | Marketing       |

On day 2, John Smith changes from the Marketing faculty to the Engineering faculty (he had a change of mind).

**_Transactional table at the time of the faculty change (day 2)_**

| **Student_Id** | **Full Name** | **Faculty** |
| :------------: | :------------ | :---------: |
| EST12345       | John Smith    | Engineering |

**_Dimensional table at the time of the faculty change (day 2)_**  

| **Student_Id** | **Student_Code** | **Full Name** | **Old_Faculty** | **New_Faculty** |
| :------------: | :--------------: | :------------ | :-------------: | :-------------: |
| 1              | EST12345         | John Smith    | Marketing       | Engineering     |

#### SCD-4 (Separate History)

**_SCD-4_** is commonly known as *historical tables*.

**_SCD-4_** uses "*historical tables*" where **one table retains current data, and additionally, a historical table is used** to maintain a record of some or all changes. Both **_SKs_** (**_dimension table_** and **_historical table_**) reference the **_fact table_** to improve query performance.

##### Example of SDC-4

For the following example, the name of the original (transactional) table is Supplier, and the **_historical table_** is Supplier_Historical:

| **SK** | **Key** | **Name**                 | **State** |
| :----: | :----- | :----------------------- | :-------: |
| 124    | ABC    | Acme & Johnson Supply Co | IL        |


| **SK** | **Key** | **Name**                 | **State** | **Registration date** |
| :----: | :----- | :----------------------- | :-------: | :-------------------: |
| 123    | ABC    | Acme Supply Co           | AC        | 2003-06-14T00:00:00   |
| 124    | ABC    | Acme & Johnson Supply Co | IL        | 2004-12-22T00:00:00   |

This method is similar to how database audit tables work. It's a fantastic way to keep track of records that undergo many changes over time.

The historical table is often referred to as a *mini-dimension*.

**_SCD-4_** is used when **_SCD-2_** is growing rapidly because dimension attributes change frequently. In **_SCD-4_**, attributes that change frequently are removed from the main dimension and added to the *mini-dimension*.

Let's consider another example to explain the above with a **_Customer dimension table_** with the following structure:
- Key (**_PK_**)
- Start date
- End date
- Name
- Date of birth
- State
- Age range
- Income range
- Purchase range

Customer attributes like name, date of birth, and customer state change very rarely or not at all. However, age range, income range, and purchase range are expected to change frequently.

If an organization with 100 million customers uses this Customer dimension, it's expected that this dimension will grow to 200 or 300 million records in a year, assuming at least two or three changes per customer per year.

In this case, we can split the dimension into two dimensions—one with attributes that change less frequently and another with attributes that change more frequently. Attributes that change frequently will be grouped into the *mini-dimension*.

Customer Dimension
- Key (**_PK_**)
- Start date
- End date
- Name
- Date of birth
- State

Mini-Dimension
- Key (**_PK_**)
- Age range
- Income range
- Purchase range

The *mini-dimension* will contain a row for each possible combination of attributes. In our case, all possible combinations of age ranges, income ranges, and purchase ranges will be available in the *mini-dimension* with the same **_PK_** as in the **_dimension table_**.

If there are 20 different age ranges, 4 different income ranges, and 3 different purchase ranges, there will be 20 X 4 X 3 = 240 different possible combinations.

These values can be populated in the **_mini-dimension table_** once and for all with an **_SK_** ranging from 1 to 240.

**_Important Note_**: The *mini-dimension* doesn't store historical attributes, even though the **_fact table_** retains the history of attribute assignments in the dimension.

Since both **_dimension tables_** are related to a **_fact table_** (Sales), this table will have the primary key (natural) of the Customer Dimension and the primary key of the *mini-dimension* (**_SK_**).

Sales Facts
- PK_Customers
- SK_MiniDimension
- Date
- Product Key
etc.

A challenge that arises is when the *mini-dimension* starts changing rapidly. In that case, multiple *mini-dimensions* can be introduced to manage these scenarios. If no fact record needs to associate the main dimension and the *mini-dimension*, a **_fact table_** with *no facts* can be used to associate the main dimension and the *mini-dimension*.

#### SCD-5

**_SCD-5_** is based on the *mini-dimension* **_SCD-4_** by embedding a "mini-dimension" key of the "current profile" into the base dimension, which is overwritten as an **_SCD-1_** attribute. This approach, named **_SCD-5_** because **4 + 1 = 5**, allows accessing the attribute values of the currently assigned mini-dimension together with the base dimension's attributes without linking them through a **_fact table_**. Typically, we represent the base dimension and the current mini-dimension stabilizer as a single table in the presentation layer. The stabilizer attributes should have different column names, such as "Current Income Level," to distinguish them from the mini-dimension attributes linked to the **_fact table_**. The **_ETL_** team must update/overwrite the **_SCD-1_** mini-dimension reference whenever the current mini-dimension changes over time.

##### Example of SCD-5

Sales (**_fact table_**)
- Date (**_FK_**)
- Customer (**_FK_**)
- Profile (**_FK_**)
...

Customers (**_dimension table_**)
- Customer (**_PK_**)
- Customer ID (**_NK_**)
- Name
...
- Current Profile (**_FK_**)

Profile (mini-dimension)
- Profile (**_PK_**)
- Age Range
- Purchase Frequency Score
- Income Level

View of the mini-dimension as a stabilizer
- Current Profile (**_PK_**)
- Current Age Range
- Current Purchase Frequency Score
- Current Income Level

#### SCD-6 (Hybrid)

**_SCD-6_** combines the approaches of types 1, 2, and 3 (**1+2+3=6**). It involves considering an **_SCD-1_** and adding a couple of additional columns indicating the time range of validity for one of the table columns. Although the design is complex, among its benefits, we can highlight that it **reduces the size of temporal queries**. There is another variant for this type of dimension, which involves having versions of the dimension record (numbered from 0 to n+1, where 0 is always the current version).

##### Example of SCD-6

**_Enrollment in the Faculty of Blue Astrophysics_**

Transactional table

| **Student_ID** | **Full Name** | **Faculty**       |
| :-------------:| :------------ | :---------------: |
| EST12345       | John Smith    | Blue Astrophysics |

Dimension table (after the **_ETL_**)

We realize, in the **_ETL_**, that Blue Astrophysics is not very intelligent. Everyone knows that Astrophysics is Green.

| **Student_ID** | **Student_Code** | **Full Name** | **Old_Faculty** | **New_Faculty**    | **Start_Date** | **End_Date** | **Current_Faculty** |
| :------------: | :--------------: | :------------ | :-------------: | :----------------: | :------------: | :----------: | :-----------------: |
| 1              | EST12345         | John Smith    |                 | Green Astrophysics | 18/08/2023     |              | N                   |

**_Change of Faculty to Salty Climate_**

Transactional table

| **Student_ID** | **Full Name** | **Faculty**   |
| :------------: | :------------ | :-----------: |
| EST12345       | John Smith    | Salty Climate |

Dimension table (after the **_ETL_**)

| **Student_ID** | **Student_Code** | **Full Name** | **Old_Faculty**    | **New_Faculty**    | **Start_Date** | **End_Date** | **Current_Faculty** |
| :------------: | :--------------: | :------------ | :----------------: | :----------------: | :------------: | :----------: | :-----------------: |
| 1              | EST12345          | John Smith   |                    | Green Astrophysics | 18/08/2023     | 19/08/2023   | N                   |
| 2              | EST12345          | John Smith   | Green Astrophysics | Salty Climate      | 19/08/2023     |              | Y                   |

**_Change of Faculty, once again, to Green Astrophysics_**

Transactional table

| **Student_ID** | **Full Name** | **Faculty**        |
| :------------: | :------------ | :----------------: |
| EST12345       | John Smith    | Green Astrophysics |

Dimension table (after the **_ETL_**)

| **Student_ID** | **Student_Code**  | **Full Name** | **Old_Faculty**    | **New_Faculty**    | **Start_Date** | **End_Date** | **Current_Faculty** |
| :------------: | :---------------: | :------------ | :----------------: | :----------------: | :------------: | :----------: | :-----------------: |
| 1              | EST12345          | John Smith    |                    | Green Astrophysics | 18/08/2023     | 19/08/2023   | N                   |
| 2              | EST12345          | John Smith    | Green Astrophysics | Salty Climate      | 19/08/2023     | 20/08/2023   | N                   |
| 3              | EST12345          | John Smith    | Salty Climate      | Green Astrophysics | 20/08/2023     |              | Y                   |

#### SCD-7 (Hybrid: Surrogate and Natural Key)

An alternative implementation is to place both the surrogate key and the natural key in the **_fact table_**.

This method allows for more flexible links to the dimension, even if **_SCD-2_** has been used instead of **_SCD-6_**.

With **_SCD-7_**, the **_fact table_** contains dual **_FKs_** for a given dimension: one **_SK_** linked to the dimension table where type 2 attributes are tracked, plus the enduring natural key of the dimension linked to the current row of the type 2 dimension to present the current value of the attribute.

**_SCD-7_** offers the same functionality as **_SCD-6_**, but it is achieved through dual keys instead of physically overwriting the current attributes as in **_SCD-6_**. Like other hybrid approaches, the attributes of the current dimension should be labeled differently to minimize confusion.

##### Example of SCD-7

Imagine the following scenario:

Sales (**_fact table_**)
- Date (**_PK_**)
- Product Key (**_FK_**)
- Enduring Product Key (**_DK_**)
- more **_FKs_**
- facts

Products (**_dimension table_**)
- Product Key (**_PK_**)
- **_SK_**
- **_DK_**
- Description
- Start Date
- End Date
- ...

Current Products (**_current dimension table_**)
- **_DK_**
- Description
- ...

#### SCD Summary

| **_SCD_**   | **_Dimension Table_**                         | **_Fact Table_**                                                 |
| :---------: | :------------------------------------- | :-------------------------------------------------------- |
| **_SCD-0_** | No changes to attribute value           | Facts associated with the original attribute value         |
| **_SCD-1_** | Overwrite the attribute value          | Facts associated with the current attribute value          |
| **_SCD-2_** | Add a new row with the new attribute value | Facts associated with the attribute value when the event occurred |
| **_SCD-3_** | Add a new column to preserve previous and current values of the attribute | Facts associated with the alternative value of the attribute (previous and current) |
| **_SCD-4_** | Add a *mini-dimension* containing attributes that change rapidly | Facts associated with attributes that change rapidly take effect when an event occurs |
|**_SCD-5_** | **_SCD-4_** + **_SCD-1_** = Add the *mini-dimension* (**_SCD-4_**), along with the **_SCD-1_** key overwritten on the base dimension | Facts associated with attributes that change rapidly take effect when the event occurred, in addition to the current attributes that change rapidly |
| **_SCD-6_** | **_SCD-1_** + **_SCD-2_** + **_SCD-3_** = Add overwritten attributes **_SCD-1_** to the **_SCD-2_** dimension row and overwrite all previous dimension rows | Facts associated with the attribute value when the event occurred, plus the current values |
| **_SCD-7_** | Add a **_SCD-2_** dimension row with a new attribute value, plus limited display on rows and/or current attribute values | Facts associated with the attribute value when the event occurred, plus the current values |

### Rapidly Changing Dimensions (Rapidly Changing Dimensions - RCD -)

These are dimensions that change (or can change) rapidly over time. **_RCDs_** are generally implemented as **_JUNKDIMs_**.

Handling **_RCDs_** in the **_DWH_** is very challenging due to performance issues. As we have seen, **_SCDs_** are used to maintain the history of changes. However, the problem with **_SCD-2_** is that with every change in the dimension attribute, it adds a new row to the table. If there are dimensions that change frequently, the table becomes larger and can cause serious performance problems. Therefore, using **_SCD-2_** may not be a good decision for implementing rapidly changing dimensions.

##### Example of RCD

Let's consider that in the Customer dimension, we have 1000 rows. On average, each customer changes 10 attributes per year. If we use **_SCD-2_** to manage this scenario, there will be 1000 * 10 = 10000 rows at the end of the year. If the table has millions of rows, it will be very challenging to manage the situation with **_SCD-2_**. Therefore, we will use an **_RCD_** approach.

To implement this, we will use a separate rapidly changing attribute, by implementing a **_JUNKDIM_**.

In the fact table, not all attributes change rapidly. Some attributes may change rapidly, while others do not. The idea here is to separate the attribute that changes rapidly from those that change slowly and move these rapidly changing attributes to another **_JUNKDIM_** table, keeping the slowly changing attribute in the same table. This way, we can manage situations of table size growth.

**Customer (_Dimension Table_)**
- ID
- Name
- City
- State
- Gender
- Income
- Rating
- Credit Score

Attributes like ID, Name, City, State, or Gender will not change or change very rarely. In contrast, attributes like Income, Rating, and Credit Score change every month based on the customer's circumstances. Therefore, we need to separate these columns from the customer table; otherwise, we will fill the table if we use **_SCD-2_** in the Customer dimension. We can place these rapidly changing columns in the **_JUNKDIM_** dimension table.

**Customer Junk (_JUNKDIM_)**
- SK
- Income
- Rating
- Credit Score

The Customer dimension remains as:

**Customer (_Dimension Table_)**
- ID
- Name
- City
- State
- Gender

However, we need to link the **_JUNKDIM_** (Customer Junk) and the **_Dimension Table_** (Customer). Additionally, we cannot simply reference the **_JUNKDIM_** by adding its primary key (**_SK_**) to Customer as a foreign key (**_FK_**). Since any changes made to the **_JUNKDIM_** need to be reflected in the **_Dimension Table_**, this obviously increases the Customer data. Instead, we will create another _mini-dimension_ table that acts as a bridge between the **_Dimension Table_** and **_JUNKDIM_**. We can also add columns such as the start and end date to track the change history.

**Customer Mini Dim (_Bridge Dimension - BRIDDIM-_)**
- ID
- SK
- Start Date
- End Date

**_BRIDDIMs_** allow defining many-to-many relationships between **_fact tables_**. They are necessary to define, for example, the relationship between a pilot and their multiple sponsors (m:n).

This table is only a bridge between two tables and does not require any **_SK_**.

An example of **_RCD_**, when growth is explosive, is Monster Dimensions (**_MONSDIM_**).

### Stacked Dimensions (STACDIM)

**_STACDIM_** is used when two or more dimensions are combined into one dimension. It has one or two attributes and is always **_SCD-0_**.

Examples (type and state): Product type, Customer state, Store type, Security type, etc. All these columns should be stored in their respective dimensions because they are properties of the dimension.

However, there are type and state columns that belong to the **_fact table_**, such as Transaction type or Transaction state. **To combine transaction type and transaction state into one dimension, we create a dummy dimension. We should never use a STACDIM**.

**Using a STACDIM is not recommended. It's wrong to use them, but they exist**. Typically because it was that way in the original system, so we simply copy it to the **_DWH_** (without thinking).

### Deferred Dimension (DEFEDIM)

When loading a record from a **_fact table_**, it's possible that a record from a **_dimension table_** is not yet ready. Technically, it's called a **_lower-level member_** or **_sensitive dimension_**.

### Distorted Dimension (DISTDIM)

A dimension used in many places is called a **_distorted dimension - DISTDIM -_**. It can be used in a single database or in multiple, or in multiple **_fact tables_**, or in multiple **_DM_** or **_DWH_**.

## Some of the Worst Practices When Working with Dimensions and Facts

1. **Not designing the Time dimension in the DWH, expecting the BI tool to do the job**.

2. **Designing the DDM with JUNKDIMs instead of normal fact tables**.

3. **Not creating SHRKDIMs and waiting for the BI tool to do it**.

4. **Not knowing the business well enough, which can lead to neglecting essential CONFDIMs in our DDM**.

5. **Not defining RPLYDIMs properly due to lack of knowledge about some point in our project's pipeline**.

6. **Defining DEGEDIMs**.

7. **We should avoid OUTGDIMs in our DDM**.

8. **If you trust users (Directors or Managers), STATDIMs will be missing in your DDM**.

9. **Considering dimension changes as SCD-0**.

10. **Considering SCD-2 instead of considering possible RCDs**.

11. **If you come across a STACDIM in the DDM, then you have made a mistake in dimensional analysis**.

## Conclusions

Designing the **_BI_** solution or any other data analysis visualization is a small part of the work, equivalent to the visible part of an iceberg (we only see 20% of the work). This is what the user perceives.

Designing **_fact tables_** and **_dimension tables_** is a small but important part of the work, and it is not perceived by users. As Data Engineers, we must perform many other tasks, such as maximizing performance, designing a **data change capture mechanism** (**_CDC_**), which ensures that data is loaded incrementally, if necessary.

In complex **_ETL_** processes, we may need frequent updates according to business needs. **We may have to add or remove fields, change data types, modify the SCD applied to a table, etc.** Making these changes to queries not only takes a lot of time but is also prone to errors.

**_VERY IMPORTANT!_**: **More often than in OLTP databases, with any minor change requested by users, and before we realize it, we may have disrupted the existing pipeline. It is advisable to move changes to the DWH as much as possible and verify the impact before the data visualization phase**.

If at this point, we think that most of the hard work is done, we must consider that **companies are constantly looking to modernize and improve their data processes**. The day may come when our company decides to switch to a cloud-based data storage platform from an on-premise database.

**_VERY IMPORTANT!_**: To prevent this problem, **first, we need to create a new architecture on the new platform**, then **we need to rewrite all ETL processes to reconfigure the new pipelines**.

Imagine that, for whatever reason, we had to work without a **_DWH_**, directly with **_Power BI_**, and now we want to implement **_ETL processes_** with Pentaho Data Integration (**_PDI_**). In that case, we first need to design the dimension and fact tables in the **_DWH_**, then implement the **_ETL processes_**, and finally re-implement the visualization in **_Power BI_**. This involves redesigning the entire pipeline and possibly adopting a collaborative work strategy if it wasn't adopted before.

The **roles required** to address these tasks range from **Data Analysts** to **Data Engineers** and can be of great complexity.

**In other words, the impact of changes can be so significant that we may have to go through the entire process again from scratch!** Therefore, **the level of complexity involved can become very high, even for technical users. It is absolutely essential that these technical professionals have certification (not necessarily a university degree, but recognized) as Data Analysts and/or Data Engineers**.

**Conclusion: The analysis and engineering involved must be carried out by qualified technicians, equipped with the appropriate resources and guided, in terms of the business, by adequately qualified and motivated users**.

## Did you find the article interesting?

If you have any questions regarding what has been presented in this article, please let me know in the comments.

Please, if you have enjoyed the content of this article, leave a comment or a "like." Furthermore, if you believe it is good enough or can help other users on the network, share the post on this platform. This way, we can all together democratize knowledge and perhaps assist other people in need.

## Picture list

- **pic001**: From "Data Warehousing and OLAP modeling" Platzi course.

## Disclaimer

**_Platzi_** (formerly Mejorando.la) is a LATAM online education platform. It was founded in 2011 by the Colombian engineer **Freddy Vega** and the Guatemalan computer scientist **Christian Van Der Henst**. ([Wikipedia](https://es.wikipedia.org/wiki/Platzi))

## ChatGPT usage

**_ChatGPT 3.5 Usage_**

This project has been verified for spelling, syntax, and content using [**_ChatGPT 3.5_**](https://chat.openai.com/chat)  

Reference:  
[OpenAI. (2023). ChatGPT (10/03/2023 version) \[Large Language Model\]](https://chat.openai.com/chat)