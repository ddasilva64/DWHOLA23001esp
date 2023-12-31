## **_dbo.DimGeography (SQL Server)_**  

### Columns  

| Key	| Name                     | Data type    | Not null | Attributes | References            | Description       |
| :-: | :----------------------- | :----------: | :------: | :--------- | :-------------------- | :---------------- |
| 1   | GeographyKey             | int          | X        | Identity   |                       | PK                |
| 2   | City                     | nvarchar(30) |          |            |                       |                   |
| 3   | StateProvinceCode        | nvarchar(3)  |          |            |                       |                   |
| 4   | StateProvinceName        | nvarchar(50) |          |            |                       |                   |
| 5   | CountryRegionCode        | nvarchar(3)  |          |            |                       |                   |
| 6   | EnglishCountryRegionName | nvarchar(50) |          |            |                       | CountryRegionName |
| 7   | SpanishCountryRegionName | nvarchar(50) |          |            |                       | deprecated        |
| 8   | FrenchCountryRegionName  | nvarchar(50) |          |            |                       | deprecated        |
| 9   | PostalCode               | nvarchar(15) |          |            |                       |                   |
| 10  | SalesTerritoryKey        | int          |          |            | dbo.DimSalesTerritory	| FK                |
| 11  | IpAddressLocator         | nvarchar(15) |          |            |                       | deprecated        |

<p><br></p>  

![SQL Server source in SQL Server Management studio](https://i.imgur.com/atjnzdn.png)  
_SQL Server source in SQL Server Management studio_  

<p><br></p>  

### **_PROWPI002\_DimGeography.KTR (PDI)_**   

1. #Table input: **_dbo.DimGeography_** (SQL Server)   
2. #raw_14: arrangements/XML countries geography.XML  
3. #staging_09: **_DimStatesProvinces_** (Postgre SQL table) 
 
<p><br></p>  

![PDI transform](https://i.imgur.com/04qTBND.png)  
_PDI transform_  

<p><br></p>  

![PDI execution](https://i.imgur.com/abg4gca.png)  
_PDI execution_ 

<p><br></p>  

### **_DimStatesProvinces layout_**  

| Key | Name                  | Data type             | Not null | Attributes | References            | Description  | Metadata |
| :-: | :-------------------- | :-------------------: | :------: | :--------- | :-------------------- | :----------- | :------- |
| 1   | StateProvinceCode     | Character variying(3) | X        |            |                       | PK           |  m139    |  
| 2   | CountryCode           | Character variying(3) | X        |            | DimCountries          | FK           |  m001    |
| 3   | StateProvince         | Character variying(60)|          |            |                       |              |  m140    |

<p><br></p>  

![Postgres SQL's transactions checking in PgAdmin](https://i.imgur.com/Dar3bXc.png)  
_Postgres SQL's transactions checking in PgAdmin_  

<p><br></p>  
  
### **_PROWPI002\_DimGeography.KTR (PDI)_**   

1. #Table input: **_dbo.DimGeography_** (SQL Server)  
2. #raw_14: arrangements/XML countries geography.XML 
3. #staging_09: **_"DimGeography"_** (Postgre SQL table) 

<p><br></p>  

![PDI transform](https://i.imgur.com/04qTBND.png)  
_PDI transform_  

<p><br></p>  

![PDI execution](https://i.imgur.com/XUGP6sR.png)  
_PDI execution_ 

### **_<p><br>DimGeography layout</p>_**  

| Key | Name                  | Data type             | Not null | Attributes | References            | Description  | Metadata |
| :-: | :-------------------- | :-------------------: | :------: | :--------- | :-------------------- | :----------- | :------- |
| 1   | GeographyKey          | Integer               | X        |            |                       | PK           | m141     |  
| 2   | CountryCode           | Character variying(3) | X        |            | DimCountries          | FK           | m001     |
| 3   | StateProvinceCode     | Character variying(3) | X        |            | DimStatesProvinces    | FK           | m139     |
| 4   | SalesTerritoryKey     | Integer               |          |            | DimSalesterritory     | FK           | m142     |
| 5   | City                  | Character variying(60)|          |            |                       |              | m143     |
| 6   | PostalCode            | Character variying(10)|          |            |                       |              | m144     |

<p><br></p>  

![Postgres SQL's transactions checking in PgAdmin](https://i.imgur.com/KNp4OlJ.png)  
_Postgres SQL's transactions checking in PgAdmin_  

<p><br></p>  

**_QA_**: Go to **_[DWH (Data Warehouse)](dwh.md)_**  

<p><br></p> 

[ChatGPT usage](../CHATGPT_USAGE.md)  

<p><br></p>

[PROWPI002 ETL (AdventureWorksDW2022 DB) :arrow_up:](prowpi002_etl_adventureworksdw2022_db.md)  

[Back to Table of contents :arrow_double_up:](../README.md)