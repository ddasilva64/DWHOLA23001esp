## **_Metadata (Postgre SQL)_**  

**_Atention!_**: While the importance of metadata cannot be overstated, the reason to use it is to help drive reporting accuracy, validate data transformation, and ensure calculation accuracy. Metadata also imposes the definition of terms such as indicators, standards, etc. In the development of the DWH, metadata appears, and with it arises the need to create a table with this content  

<p><br></p>

### **_DimCustomer\_Metadata.XML (values)_**  
  1. #staging_07: **_staging\DimCustomer\_Metadata.XML_** (values, but not layout)  

<p><br></p>

### **_DimCustomer\_Metadata.XML layout_**  

| Key  | Meaning                                 | Standard              | Formula                                                                  | Units |
| :--: | :-------------------------------------- | :-------------------: | :----------------------------------------------------------------------- | :---: |
| m060 | Primary key for Customer records        |                       |                                                                          |       |
| m107 | ID of the territory in which the customer is located. Foreign key to SalesTerritory.SalesTerritoryID  |  |                                 |       |
| m108 | Customer alternate key                  |                       |                                                                          |       |
| m109 | A courtesy title                        |                       | For example, Mr. or Ms.                                                  |       |
| m034 | First name                              |                       |                                                                          |       |
| m036 | Middle name                             |                       |                                                                          |       |
| m035 | Last name                               |                       |                                                                          |       |
| m044 | Marital status                          |                       | M = Married, S = Single                                                  |       |
| m040 | Date of birth                           |                       |                                                                          |       |
| m045 | Surname suffix                          |                       | For example, Sr. or Jr.                                                  |       |
| m048 | Gender                                  |                       | M = Male, F = Female                                                     |       |
| m042 | Email address                           |                       |                                                                          |       |
| m110 | Sales total year to date                |                       |                                                                          |       |
| m111 | Total children                          |                       |                                                                          |       |
| m112 | Number of children at home              |                       |                                                                          |       |
| m113 | Education                               |                       |                                                                          |       |
| m114 | Occupation                              |                       |                                                                          |       |
| m115 | House owner flag                        |                       | 0 = No, 1 = Yes                                                          |       |
| m116 | Number of cars owned                    |                       |                                                                          |       |
| m117 | First street address line               |                       |                                                                          |       |
| m118 | Second street address line              |                       |                                                                          |       |
| m043 | Phone                                   |                       |                                                                          |       |
| m119 | Date of the first purchase              |                       |                                                                          |       |
| m120 | Commute distance                        |                       |                                                                          |       |
| m037 | Name style||0 = The data in FirstName and LastName are stored in western style (first name, last name) order. 1 = Eastern style (last name, first name) order||

<p><br></p>  

![XML file checking](https://i.imgur.com/PYuP11s.png)  
_XML file checking_  

<p><br></p>  

**_QA_**: Go to **_[DWH (Data Warehouse)](dwh.md)_**  

<p><br></p> 

[ChatGPT usage](../CHATGPT_USAGE.md)  

<p><br></p>  

[PROWPI002 ETL :arrow_up:](prowpi002_etl_adventureworksdw2022_db.md)  

[Back to Table of contents :arrow_double_up:](../README.md)  

<p><br></p>  

### **_DimCustomer\_Metadata.KTR_**  
  1. #staging_07: **_staging\DimCustomer\_Metadata.XML_**  

<p><br></p>  

![PDI transform](https://i.imgur.com/SQ52OxV.png)  
_PDI transform_  

<p><br></p>  

![PDI execution](https://i.imgur.com/199u4vD.png)  
_PDI execution_ 

<p><br></p>  

### **_Metadata layout_**  

| Key	| Name                  | Data type              | Not null | Attributes | References            | Description |
| :-: | :-------------------- | :--------------------: | :------: | :--------- | :-------------------- | :-----------| 
| 1   | Key                   | Character variying(4)  | X        |            |                       | PK,FK       |
| 2   | Meaning               | Character variying(60) | X        |            |                       |             |
| 3   | Standard              | Character variying(20) |          |            |                       |             |
| 4   | Formula               | Character variying(500)|          |            |                       |             |
| 5   | Units                 | Character variying(10) |          |            |                       |             |

<p><br></p>  

![Postgres SQL's transactions checking in PgAdmin](https://i.imgur.com/PJg9swG.png)  
_Postgres SQL's transactions checking in PgAdmin_  

<p><br></p>  

**_QA_**: Go to **_[DWH (Data Warehouse)](dwh.md)_**  

<p><br></p> 

[ChatGPT usage](../CHATGPT_USAGE.md)  

<p><br></p> 

[PROWPI002 ETL :arrow_up:](prowpi002_etl_adventureworksdw2022_db.md)  

[Back to Table of contents :arrow_double_up:](../README.md)  