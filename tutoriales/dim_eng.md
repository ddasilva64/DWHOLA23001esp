# Dimensions

## The time dimension

In any **_DWH_** you can find several cubes with their **_fact tables_** full of records about some variable of interest to the business that must be studied. As already mentioned, each **_fact table_** will be surrounded by several **_dim tables_**, depending on which parameters serve best to carry out the analysis of the facts that you want to study. A parameter that will almost certainly be common to all cubes is Time, since the usual thing is to store the events as they occur over time, thus obtaining a time series of the variable to be studied.

Since Time is a dimension present in practically any cube of an **_OLAP_** system, it deserves special attention. When designing the Time dimension (both for a **_star schema_** and for a **_snowflake schema_**) special care must be taken, since it can be done in several ways and not all of them are equally efficient. The most common way to design this table is to set the date or date/time as **_PK_** of the table (*time table 1*). This design is not the most recommended, since most database management systems find it more expensive to search on fields of type "date" or "datetime", these costs are reduced if the key field is of type integer, also, an integer data always takes up less space than a data of type date (the key field will be repeated in millions of records in the fact table and that can be a lot of space), so the design of the time table if an integer "TimeID" field is used as the primary key (*time table 2*).

<p><br></p>

*Time table (1)*

| Date (PK) | datetime |
| :-------- | :------: |
| Year      | char(4)  |
| Quarter   | char(6)  |
| Month     | char(10) |

<p><br></p>

*Time table (2)*

| TimeID (PK) | integrate |
| :---------- | :-------: |
| Date        | datetime  |
| Year        | char(4)   |
| Quarter     | char(6)   |
| Month       | char(10)  |

<p><br></p>

When filling out the Times table, if you have chosen an integer field for the key, there are two options: the one that is perhaps most immediate is to assign consecutive numerical values (1, 2, 3, 4... .) for different date values. The other option would consist of assigning numerical values of the type "yyyymmdd", that is, the first four digits of the field value indicate the year of the date, the next two the month and the last two the day. This second way provides a certain advantage over the previous one, since in this way it is achieved that the numerical data itself provides information on which date it refers to. For example, if the value 20040723 is found in the fact table, we will know that it refers to July 23, 2004; On the other hand, with the first method, values such as 8456456 could be found, and to know what date this value refers to, a query would have to be made on the time table.  

In addition to the TimeID key field, the **_fact table_** should contain other fields that are also important to consider. These fields would be:
- A "year" field. - Which would contain values like '2002', 2003, '2004'...
- A "month" field. - Here you can enter the values 'January', 'February'... (or in abbreviated form: 'Jan', 'Feb'...). Although this is not incorrect, it can be improved if the name of the month is accompanied by the year to which it belongs. For example: 'January 2004', 'February 2004'... In this way the search for the values of a specific month is optimized, since with the first method, if the values belonging to the month of "January of 2003", all this information is contained in a single field, the "month", and it would not be necessary to also consult the year field.
- A "monthID" field. - This field would have to be of type integer and would serve to store values of type 200601 (for 'January 2006') or 200602 (for 'February 2006'). In this way it is possible to organize and group by month.  

In an analogous way to what has been done with the month field, more fields such as "Time of year", "Quarter", "Fortnight", "Week" of text type could be added to be able to display them, and their analogues of integer type " Time of year_ID", "QuarterID", "FortnightID", "WeekID" to be able to group and sort. In general, you can add a field for each level of granularity desired.  

Another special field that can be added is the "Day of the week" ('Monday', 'Tuesday'...). This field is usually added to be able to carry out studies on the behavior of the days of the week in general (not the first Monday of the month of January of a specific year, this type of study is usually not of interest), and for this reason this field It does not need to be accompanied by the month or year like the previous fields. You can also add its dual "ID" field of integer type to be able to sort and group if necessary.  

With the additions described we could have a Time table like the one in the figure (*Time table 3*). This would be valid for a star design. For a snowflake design, the time table would have to be broken down into as many tables as there are hierarchical levels it contains. Note that the "ID" type fields are all of type integer, since it will be on these fields that most of the operations will be performed and these will be performed more efficiently on integer data.

<p><br></p>

*Time table (3)*  

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

<p><br></p>

## Slowly Changing Dimensions (SCD)

A **_SCD_** in **_DWH_** is a dimension that contains relatively static data that can change slowly but unpredictably, rather than following a regular schedule. Some examples of typical dimensions that change slowly are entities such as Geographic Locations, Customers, or Product names. ([Wikipedia](https://en.wikipedia.org/wiki/Slowly_changing_dimension)).  

<p><br></p>

![001-Dimension](https://i.imgur.com/kXDzc6e.png)  
_Dimension (picture 001)_  

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
- **_Type 4_**: Separate history. **_SCD-4_** is often used with "history tables." This method is similar to how database audit tables and change data capture techniques work.
- **_Type 5_**: Combines the approaches of types 1 and 4 (1+4=5).
- **_Type 6_**: Combines the approaches of types 1, 2 and 3 (1+2+3=6).
- **_Type 7_**: Consists of two separate dimensions. One dimension is maintained as a typical **_SCD-2_**. Its surrogate key is used in the fact table. A second **_SCD-1_** is included that contains the “current” view of the dimension. Its durable key (from the OLTP system) is included in the fact table.

#### Elecció del tipus de SCD

Choosing which type of **_SCD_** to use depends on your specific **_DWH_** requirements and analysis needs. It is important to consider factors such as the importance of historical data, the frequency of dimension changes, and the storage and performance implications of each approach.

**_SCDs_** are a crucial aspect of **_DWHs_** as they allow data to be represented over time, allowing for accurate historical analysis and reporting.

## SDC-0

**_SCD-0_** attributes never change and are assigned to attributes that have durable values or that are described as "Original."  
Examples: date of birth, original credit score.  
Applies to most date size attributes.  

## SCD-1

Let us remember that, in a dimensional database, **_SCDs_** are those dimensions that change over time, but not necessarily in a constant or predictable manner. For instance, in a table of Customer data, the customer's address may change over time, but not all customers change their address at the same rate. Some customers may change their address every month, while others may keep the same address for years.

There are different types of **_SCD_**, which are classified according to the way changes in dimensions are handled. For example, type 1 means that old information is overwritten with new information every time a change occurs.

Proper handling of **_SCD_** is important for maintaining the accuracy and integrity of dimensional data in a database, as it allows users to perform historical analysis and compare data over time.

Example:

**_Registration_**

Transactional table

| **Student_Id** | **Full Name** | **Faculty** |
| :------------: | :------------ | :---------: |
| EST12345       | John Smith    | Marketing   |

Dimension table (now Student_Id is a surrogate key -**_SK_**-, which aims for better dimension performance by numeric key).

| **Student_Id** | **Student_Code** | **Full Name** | **Faculty** |
| :------------: | :--------------: | :------------ | :---------: |
| 1              | EST12345         | John Smith    | Marketing   |

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

Dimension table (now Student_Id is a surrogate key -**_SK_**-, which aims for better dimension performance by numeric key).

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

Type 3 **_SCDs_** are used when it is important to track specific attribute changes while maintaining simplicity in the data model.

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

## Some of the Worst Practices in Dimensional Data Modeling

1. 

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
[OpenAI. (2023). ChatGPT (09/29/2023 version) \[Large Language Model\]](https://chat.openai.com/chat)