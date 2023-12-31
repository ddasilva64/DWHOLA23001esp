## **_dbo.DimProductSubcategory (SQL Server)_**  

### Columns  

| Key	| Name                           | Data type    | Not null | Attributes | References            | Description            |
| :-: | :----------------------------- | :----------: | :------: | :--------- | :-------------------- | :--------------------- |
| 1   | ProductSubcategoryKey          | int          | X        | Identity   |                       | PK                     |
| 2   | ProductSubcategoryAlternateKey | int          |          |            |                       | deprecated             |
| 3   | EnglishProductSubcategoryName  | nvarchar(50) | X        |            |                       | ProductSubcategoryName |
| 4   | SpanishProductSubcategoryName  | nvarchar(50) | X        |            |                       | deprecated             |
| 5   | FrenchProductSubcategoryName   | nvarchar(50) | X        |            |                       | deprecated             |
| 6   | ProductCategoryKey             | int          |          |            | dbo.DimProductCategory| FK                     |	

<p><br></p>  

![SQL Server source in SQL Server Management studio](https://i.imgur.com/zIPgD0x.png)  
_SQL Server source in SQL Server Management studio_  

<p><br></p>  

### **_PROWPI002\_DimProductSubcategory.KTR (PDI)_**   
1. #Table input: **_dbo.DimProductSubcategory_** (SQL Server)  
2. staging_12: **_DimProductSubcategory_** (Postgre SQL table)
 
<p><br></p>  

![PDI transform](https://i.imgur.com/eqxcJFk.png)  
_PDI transform_  

<p><br></p>  

![PDI execution](https://i.imgur.com/Tti91Ot.png)  
_PDI execution_ 

<p><br></p>  

### **_DimProductSubcategory layout (Postgre SQL)_**  

| Key	| Name                           | Data type             | Not null | Attributes | References            | Description            | Metadata |
| :-: | :----------------------------- | :-------------------: | :------: | :--------- | :-------------------- | :--------------------- | :------: |
| 1   | ProductSubcategoryKey          | integer               | X        |            |                       | PK                     | m145     |
| 2   | ProductSubcategory             | character varying(50) | X        |            |                       |                        | m146     |
| 3   | ProductCategoryKey             | integer               |          |            | DimProductCategory    | FK                     |	m121     |

<p><br></p>  

![Postgres SQL's transactions checking in PgAdmin](https://i.imgur.com/sPsJ5uZ.png)  
_Postgres SQL's transactions checking in PgAdmin_  

<p><br></p>  

**_QA_**: Go to **_[DWH (Data Warehouse)](dwh.md)_**  

<p><br></p> 

[ChatGPT usage](../CHATGPT_USAGE.md)  

<p><br></p>

[PROWPI002 ETL (AdventureWorksDW2022 DB) :arrow_up:](prowpi002_etl_adventureworksdw2022_db.md)  

[Back to Table of contents :arrow_double_up:](../README.md)