# Data Warehousing - eng -

> It is a **process**, not a product, for **collecting and managing data** from **various sources** in order to **obtain a unique and detailed view of a part or the entire company**.  
> [Devlin, 2011]  

## Data Warehouse (DWH)

- Is the **database** containing **data from various sources** is the **central repository**.
- Reports created from a **_DWH_** are used in **_BI_** (for example). In other words, **it is the data source container for BI solutions**.

<p><br></p>

![001-DWH](https://i.imgur.com/UOGxSmp.png)  
_DWH (picture 001)_

<p><br></p>

## Data Mart (DM)

- **_DWH_** oriented towards a **specific area of the business**.
- They contain **summarized data for analysis in a unit of the organization**.

<p><br></p>

![002-DM](https://i.imgur.com/bDT4i1l.png)  
_DM (picture 002)_

<p><br></p>

## DB OLTP vs. OLAP

### OLTP (OnLine Transaction Processing)

**_OLTP_** is the acronym for Online Transaction Processing. It is a type of processing that facilitates and manages transactional applications, usually for data input and retrieval, as well as transaction processing (transaction manager). **_OLTP_** software packages are based on client-server architecture since they are commonly used by companies with a distributed computer network. ([Wikipedia](https://es.wikipedia.org/wiki/OLTP)).

<p><br></p>

![003-OLTP](https://i.imgur.com/7Jo7jf3.png)  
_OLTP (picture 003)_  

<p><br></p>

![004-ERD](https://i.imgur.com/02kIMab.png)  
_ERD (picture 004)_  

<p><br></p>

### OLAP (On-Line Analytical Processing)

**_OLAP_** is the acronym for Online Analytical Processing (**_BI_**), which aims to streamline the querying of large amounts of data. It uses various data structures, typically multidimensional (or **_OLAP Cubes_**), containing summarized data from large databases or Transactional Systems (**_OLTP_**). It is used in business reports for sales, marketing, management reports, data mining, and similar areas. ([Wikipedia](https://es.wikipedia.org/wiki/OLAP)).

<p><br></p>

![005-OLAP](https://i.imgur.com/HFIxxdT.png)  
_OLAP (picture 005)_  

<p><br></p>

![006-OLAP model](https://i.imgur.com/qdlHp3w.png)  
_OLAP model (picture 006)_   

<p><br></p>

### OLTP vs. OLAP

| OLTP                                                 | OLAP                                                  |  
| :--------------------------------------------------- | :---------------------------------------------------- |
| Operational system                                  | Data Warehouse (**_DWH_**) system                         |
| Speed, efficiency, real-time operations             | Analysis, querying, multidimensional data, decision-making |
| Used for day-to-day business operations             | Used for analytics                                    |
| Designed to support business transactions           | Designed to support the decision-making process      |
| Volatile data                                       | Data is NOT volatile                                  |
| Detailed data                                       | Summarized data                                      |
| Entity-Relationship modeling                        | Dimensional modeling                                 |
| Transaction processing                              | Analytical processing                                | 
| High concurrency                                    | Low concurrency                                      |
| Answers questions like: Has invoice ### been canceled?, or which customer bought product X today? | Answers questions like: What product was the best-seller in 2022, by product line? How have sales vs. budget been, month by month and by store? |

<p><br></p>

![007-OLTP vs. OLAP tools](https://i.imgur.com/8aI0mBf.png)  
_OLTP vs. OLAP tools (picture 007)_  

<p><br></p>

## DWH methodologies

### Bill Inmon

- **_Bill Inmon_**: **_William H. Inmon_** (born in 1945) is an American computer scientist, **_widely recognized as the father of DWH_**. He wrote the first book, conducted the first conference (with Arnie Barnett), wrote the first column in a magazine, and was the first to offer classes on **_DWH_**. He created the accepted definition of what a **_DWH_** is: a time-variant, integrated, non-volatile collection of data oriented toward management decisions. Compared to the approach of the other pioneering **_DWH_** architect, **_Ralph Kimball_**, **_Inmon_**'s approach is often characterized as a **top-down approach**. ([Wikipedia](https://en.wikipedia.org/wiki/Bill_Inmon)).

<p><br></p>

![008-Bill Inmon](https://i.imgur.com/XkFHRAG.png)
_Bill Inmon (picture 008)_  

<p><br></p>

- **_Ralph Kimball_**: **_Ralph Kimball_** (born on July 18, 1944) is an author in the field of **_DWH_** and **_BI_**. He is one of the original architects of Data Warehousing and is known for his long-standing beliefs that **_DWH_** should be designed to be understandable and fast. His **bottom-up methodology**, also known as Dimensional Modeling or the Kimball methodology, is one of the two major Data Warehousing methodologies along with **_Bill Inmon_**. ([Wikipedia](https://en.wikipedia.org/wiki/Ralph_Kimball)).

<p><br></p>

![009-Ralph Kimball](https://i.imgur.com/lCHwGai.png)  
_Ralph Kimball(picture 009)_  

<p><br></p>

### Metodología de Ralph Kimball

![010-Phases of Ralph Kimball's methodology](https://i.imgur.com/layLtGp.png)  
_Phases of Ralph Kimball's methodology (picture 010)_  

<p><br></p>

### Hephaestus methodology

![011-Hephaestus methodology phases](https://i.imgur.com/jUxHObK.png)  
_Hephaestus methodology phases (picture 011)_  

[Complete Hephaestus methodology](https://troyanx.com/Hefesto/index.html)

<p><br></p>

### Custom methodology

![012-Custom methodology](https://i.imgur.com/VucCuID.png)  
_Custom methodology (picture 012)_  

<p><br></p>

### Data Vault methodology

- [Data Vault: How to structure your DWH](https://aprenderbigdata.com/data-vault/)

<p><br></p>

![013- Data Vault methodology](https://i.imgur.com/MhyAPoC.png)  
_Data Vault methodology (picture 013)_ 

<p><br></p>

- [3 reasons why you need Data Vault](https://www.linkedin.com/pulse/3-razones-por-las-que-necesitas-data-vault-christian-seijas/?originalSubdomain=es)

<p><br></p>

### Methodologies comparison

| ITEM                      | MULTIDIMENSIONAL (Ralph Kimball) | RELATIONAL (Bill Inmon)  | DATA VAULT (Dan Linsted) |
| :------------------------ | :-----------------------------:   | :---------------------:   | :-----------------------: |
| DESIGN                    | less effort and time              | more effort and time      | medium effort and time   |
| MAINTENANCE               | medium to high                    | simple                    | medium                   |
| INVESTMENT                | low cost                          | high initial cost         | low cost                 |
| TIME                      | low initial setup time            | high initial time         | low initial time         |
| ELEMENTS                   | staging-DM                        | staging-DWH-DM            | hub/link/satellite       |
| SPECIALIZATION LEVEL      | medium to low                     | high                      | medium to high           |
| DATA INTEGRATION          | individual areas                  | extensive                 | individual areas         |
| FLEXIBILITY	            | lower                             | higher                    | medium                   |

<p><br></p>

[Data Warehouse methodologies](https://gravitar.biz/datawarehouse/metodologias-data-warehouse/)

## Some of the worst practices in Data Warehousing

1. **Thinking with an OLTP mindset when designing Data Warehousing**.
    - **Neglecting the Staging area** in our **_DWH_**, without any particular reason.
    - **Neglecting the Data Mart area** in our **_DWH_**, without any particular reason.

2. **Building the physical model of a DWH only with an OLAP DB, and not an OLTP DB**.

3. **Building the logical model of a DWH with an ER model, and not a DDM**.

4. **Improvising**, without having a **_DWH_** methodology.

5. Not being aware of the **effects of the chosen** **_DWH_** **methodology**.

6. **Allowing Directors or Business Managers to decide the configuration of our DWH (prioritizing their departmental interests)**.

7. **Going directly to the DDM solution without considering the raw data phase**.

8. **Confusing the DWH with a traditional OLTP DB**.

9. **Considering the DWH unnecessary**.
    - Not using the **_DWH_** because people think it's unnecessary.
    - Skipping the **_DWH_** and going straight to designing the **_DDM_** in the **_BI_** solution.

## Did you find the article interesting?

If you have any questions regarding what has been presented in this article, please let me know in the comments.

Please, if you have enjoyed the content of this article, leave a comment or a "like." Furthermore, if you believe it is good enough or can help other users on the network, share the post on this platform. This way, we can all together democratize knowledge and perhaps assist other people in need.

## Picture list

- **pic001**: From "Data Warehousing and OLAP modeling" Platzi course.
- **pic002**: From "Data Warehousing and OLAP modeling" Platzi course.
- **pic003**: From "Data Warehousing and OLAP modeling" Platzi course.
- **pic004**: From "Data Warehousing and OLAP modeling" Platzi course.
- **pic005**: From "Data Warehousing and OLAP modeling" Platzi course.
- **pic006**: From "Data Warehousing and OLAP modeling" Platzi course.
- **pic007**: From "Data Warehousing and OLAP modeling" Platzi course.
- **pic008**: From "Data Warehousing and OLAP modeling" Platzi course.
- **pic009**: From "Data Warehousing and OLAP modeling" Platzi course.
- **pic010**: From "Data Warehousing and OLAP modeling" Platzi course.
- **pic011**: From "Data Warehousing and OLAP modeling" Platzi course.
- **pic012**: From "Data Warehousing and OLAP modeling" Platzi course.
- **pic013**: [Data Vault: How to structure your Data Warehouse](https://aprenderbigdata.com/data-vault/).

## Disclaimer

**_Platzi_** (formerly Mejorando.la) is a LATAM online education platform. It was founded in 2011 by the Colombian engineer **Freddy Vega** and the Guatemalan computer scientist **Christian Van Der Henst**. ([Wikipedia](https://es.wikipedia.org/wiki/Platzi))

## ChatGPT usage

**_ChatGPT 3.5 Usage_**

This project has been verified for spelling, syntax, and content using [**_ChatGPT 3.5_**](https://chat.openai.com/chat)  

Reference:  
[OpenAI. (2023). ChatGPT (09/25/2023 version) \[Large Language Model\]](https://chat.openai.com/chat)