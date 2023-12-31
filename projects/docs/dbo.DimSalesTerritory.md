## **_dbo.DimSalesTerritory (SQL Server)_**  

### Columns  

| Key	| Name                       | Data type      | Not null | Attributes | References            | Description |
| :-: | :------------------------- | :------------: | :------: | :--------- | :-------------------- | :-----------|
| 1   | SalesTerritoryKey          | int            | X        | Identity   |                       | PK          |
| 2   | SalesTerritoryAlternateKey | int            |          |            |                       | deprecated  |
| 3   | SalesTerritoryRegion       | nvarchar(50)   | X        |            |                       |             |
| 4   | SalesTerritoryCountry      | nvarchar(50)   | X        |            |                       |             |
| 5   | SalesTerritoryGroup        | nvarchar(50)   |          |            |                       |             |
| 6   | SalesTerritoryImage        | varbinary(MAX) |          |            |                       | deprecated  |

<p><br></p>  

![SQL Server source in SQL Server Management studio](https://i.imgur.com/ZrlxLQB.png) 
_SQL Server source in SQL Server Management studio_  

<p><br></p>  

**_Notice_**: **_N/A_** values in **_DimSalesTerritory_** is deprecated  

<p><br></p> 

### **_PROWPI002\_DimSalesTerritory.KTR (PDI)_**   
1. #Table input: **_dbo.DimSalesTerritory_** (SQL Server)   
2. #raw_14: arrangements/XML countries geography.XML  
3. #staging_09: **_DimSalesTerritory_** (Postgre SQL table) 

<p><br></p>  

![PDI transform](https://i.imgur.com/I9vysve.png)   
_PDI transform_  

<p><br></p>  

![PDI execution](https://i.imgur.com/S4As0gi.png)  
_PDI execution_ 

<p><br></p>  

### **_DimSalesTerritory layout (Postgre SQL)_**  

| Key | Name                  | Data type             | Not null | Attributes | References            | Description  | Metadata |
| :-: | :-------------------- | :-------------------: | :------: | :--------- | :-------------------- | :----------- | :------- |
| 1   | SalesTerritoryKey     | Integer               | X        |            |                       | PK           | m063     |  
| 2   | SalesTerritoryRegion  | Character variying(60)|          |            |                       |              | m173     |
| 3   | SalesTerritoryGroup   | Character variying(60)|          |            |                       |              | m175     |
| 4   | CountryCode           | Character variying(3) |          |            | DimCountries          | FK           | m174     |

<p><br></p>  

![Postgres SQL's transactions checking in PgAdmin](https://i.imgur.com/NQUEYEj.png)  
_Postgres SQL's transactions checking in PgAdmin_  

<p><br></p> 

**_QA_**: Go to **_[DWH (Data Warehouse)](dwh.md)_**  

<p><br></p> 

[ChatGPT usage](../CHATGPT_USAGE.md)  

<p><br></p> 

[PROWPI002 ETL (AdventureWorksDW2022 DB) :arrow_up:](prowpi002_etl_adventureworksdw2022_db.md)  

[Back to Table of contents :arrow_double_up:](../README.md)