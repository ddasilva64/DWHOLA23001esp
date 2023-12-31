## **_Health.XML (XML staging)_**

### **_PROWPI001\_Health.KTR_**
1. #raw_06: HDR21-22_Composite_indices_complete_time_series.CSV
2. #raw_02: countries_eng.CSV
3. #raw_10: NHA indicators.XLSX
4. #raw_11: arrangements/XML countries NHS not match input.XML
5. #staging_06: **_staging\Health.XML_**

<p><br></p> 

![PDI transform](https://i.imgur.com/CYrxkwL.png)
_PDI transform_

<p><br></p> 

![PDI execution](https://i.imgur.com/tRRucKt.png)
_PDI execution_

<p><br></p> 

### **_Health.XML layout_**

| Key | Name        | Data type            | Not null | Attributes | References   | Description | Metadata |
| :-: | :---------- | :------------------: | :------: | :--------- | :----------- | :---------- | :------- |
| 1   | CountryCode | Character varying(3) | X        |            | DimCountries | PK, FK      | m001     |
| 2   | CHEGDP      | real                 |          |            |              |             | m022     |
| 3   | CHEPCUSD    | real                 |          |            |              |             | m023     |
| 4   | LE          | real                 |          |            |              |             | m024     |
| 5   | FLE         | real                 |          |            |              |             | m025     |
| 6   | MLE         | real                 |          |            |              |             | m026     |
| 7   | MMR         | integer              |          |            |              |             | m027     |
| 8   | TBR         | real                 |          |            |              |             | m028     |

<p><br></p> 

![XML file checking](https://i.imgur.com/7gcWnrz.png)
_XML file checking_

<p><br></p> 

**_QA_**: Go to **_[DWH (Data Warehouse)](dwh.md)_**  

<p><br></p> 

[ChatGPT usage](../CHATGPT_USAGE.md)  

<p><br></p>

[PROWPI001 ETL :arrow_up:](prowpi001_etl.md)  

[Back to Table of contents :arrow_double_up:](../README.md)