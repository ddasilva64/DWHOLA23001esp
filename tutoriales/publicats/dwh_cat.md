# Data Warehousing - cat -

> És un **procés**, no un producte, per **recopilar i gestionar dades** de **diverses fonts** amb l'objectiu d'**obtenir una vista única i detallada d'una part o de tota una empresa**.  
> [Devlin, 2011]  

## Data Warehouse (DWH)

- La **base de dades** que conté **dades de diverses fonts** és el **repositori central**.
- Els informes creats a partir d'un **_DWH_** s'utilitzen al **_BI_** (per exemple). És a dir, **és el contenidor de fonts de dades per a solucions de BI**.

<p><br></p>

![001-DWH](https://i.imgur.com/UOGxSmp.png)  
_DWH (imatge 001)_

<p><br></p>

## Data Mart (DM)

- **_DWH_** orientat cap a una **àrea específica del negoci**.
- Contenen **dades resumides per a la seva anàlisi en una unitat de l'organització**.

<p><br></p>

![002-DM](https://i.imgur.com/bDT4i1l.png)  
_DM (imatge 002)_

<p><br></p>

## BD OLTP vs. OLAP

### OLTP (OnLine Transaction Processing)

**_OLTP_** és l'acrònim en anglès de Processament de Transaccions En Línia. És un tipus de processament que facilita i administra aplicacions transaccionals, generalment per a l'entrada de dades i la recuperació i el processament de transaccions (gestor de transaccions). Els paquets de programari per a **_OLTP_** es basen en l'arquitectura client-servidor ja que sovint són utilitzats per empreses amb una xarxa informàtica distribuïda. ([Wikipedia](https://es.wikipedia.org/wiki/OLTP)).

<p><br></p>

![003-OLTP](https://i.imgur.com/7Jo7jf3.png)  
_OLTP (imatge 003)_  

<p><br></p>

![004-ERD](https://i.imgur.com/02kIMab.png)  
_ERD (imatge 004)_  

<p><br></p>

### OLAP (On-Line Analytical Processing)

**_OLAP_** es el acrónimo en inglés de Procesamiento Analítico En Línea (**_BI_**) cuyo objetivo es agilizar la consulta de grandes cantidades de datos. Para ello utiliza estructuras de datos diversas, normalmente multidimensionales (o **_Cubos OLAP_**), que contienen datos resumidos de grandes BD o Sistemas Transaccionales (**_OLTP_**). Se usa en informes de negocios de ventas, marketing, informes de dirección, minería de datos y áreas similares. ([Wikipedia](https://es.wikipedia.org/wiki/OLAP)).

<p><br></p>

![005-OLAP](https://i.imgur.com/HFIxxdT.png)  
_OLAP (imatge 005)_  

<p><br></p>

![006-Modelo OLAP](https://i.imgur.com/qdlHp3w.png)  
_Modelo OLAP (imatge 006)_  

<p><br></p>

### OLTP vs. OLAP

| OLTP                                                 | OLAP                                                  |  
| :--------------------------------------------------- | :---------------------------------------------------- |
| Sistema de processament de transaccions en línia      | Processament analític en línia                        |
| Rapidesa, eficiència, operacions en temps real        | Anàlisi, consulta, dades multidimensionals, presa de decisions |
| Utilitzat per a les operacions del negoci             | Utilitzat per a l'anàlisi                            |
| Dissenyat per a suportar transaccions comercials       | Dissenyat per donar suport al procés de presa de decisions |
| Dades volàtils                                       | Dades NO són volàtils                                |
| Dades detallades                                     | Dades resumides                                       |
| Modelatge E-R                                        | Modelatge dimensional                                |
| Processament de transaccions                         | Processament analític                               | 
| Alta concorrència	                                  | Baixa concorrència                                   |
| Respon a preguntes com: La factura ### s'ha cancel·lat?, o què client ha comprat avui el producte X? | Respon a preguntes com: Quin producte va ser el més venut el 2022, per línia de producte?, o com han anat les vendes respecte al pressupost, mes a mes i per botiga? |

<p><br></p>

![007-Eines OLTP vs. OLAP](https://i.imgur.com/8aI0mBf.png)  
_Eines OLTP vs. OLAP (imatge 007)_  

<p><br></p>

## Metodologies de DWH

### Bill Inmon

- **_Bill Inmon_**: **_William H. Inmon_** (nascut el 1945) és un informàtic estatunidenc, **_reconegut per molts com el pare del DWH_**. Va escriure el primer llibre, va realitzar la primera conferència (amb Arnie Barnett), va escriure la primera columna en una revista i va ser el primer a oferir classes sobre **_DWH_**. Va crear la definició acceptada de què és un **_DWH_**: una recopilació de dades variable en el temps, integrada, no volàtil i orientada a temes en suport de les decisions de gestió. En comparació amb l'enfocament de l'altre arquitecte pioner del **_DWH_**, **_Ralph Kimball_**, l'enfocament de **_Inmon_** sovint es caracteritza com un **enfocament de dalt a baix**. ([Wikipedia](https://en.wikipedia.org/wiki/Bill_Inmon)).

<p><br></p>

![008-Bill Inmon](https://i.imgur.com/XkFHRAG.png)
_Bill Inmon (imatge 008)_  

<p><br></p>

- **_Ralph Kimball_**: **_Ralph Kimball_** (nascut el 18 de juliol de 1944) és un autor **_DWH_** i el **_BI_**. És un dels arquitectes originals de la gestió de dades (Data Warehousing) i és conegut per les seves conviccions a llarg termini que els **_DWH_** han de ser dissenyats per ser comprensibles i ràpids. La seva **metodologia de baix cap amunt**, també coneguda com a Modelatge Dimensional o metodologia Kimball, és una de les dues principals metodologies de gestió de dades juntament amb **_Bill Inmon_**. ([Wikipedia](https://en.wikipedia.org/wiki/Ralph_Kimball)).

<p><br></p>

![009-Ralph Kimball](https://i.imgur.com/lCHwGai.png)  
_Ralph Kimball(imatge 009)_  

<p><br></p>

### Metodologia de Ralph Kimball

![010-Fases metodologia de Ralph Kimball](https://i.imgur.com/layLtGp.png)  
_Fases metodologia de Ralph Kimball (imatge 010)_  

<p><br></p>

### Metodologia Hefest

![011-Fases metodologia Hefest](https://i.imgur.com/jUxHObK.png)  
_Fases metodologia Hefest (imatge 011)_  

[Metodología Hefest sencera](https://troyanx.com/Hefesto/index.html)

<p><br></p>

### Metodologia personalitzada

![012-Metodologia personalitzada](https://i.imgur.com/VucCuID.png)  
_Metodologia personalitzada (imatge 012)_  

<p><br></p>

### Metodologia Data Vault

- [Data Vault: Com estructurar el teu DWH](https://aprenderbigdata.com/data-vault/)

<p><br></p>

![013-Metodologia Data Vault](https://i.imgur.com/MhyAPoC.png)  
_Metodologia Data Vault (imatge 013)_ 

<p><br></p>

- [3 raons per les quals necessites Data Vault](https://www.linkedin.com/pulse/3-razones-por-las-que-necesitas-data-vault-christian-seijas/?originalSubdomain=es)

<p><br></p>

### Comparativa de les metodologies

| ÍTEM                      | MULTIDIMENSIONAL (Ralph Kimball) | RELACIONAL (Bill Inmon)  | DATA VAULT (Dan Linsted) |
| :------------------------ | :------------------------------: | :----------------------: | :----------------------: |
| DISSENY                   | menor esforç i temps             | major esforç i temps    | mitjà esforç i temps     |
| MANTENIMENT               | mitjà-alt                        | simple                 | mitjà                   |
| INVERSIÓ                  | baix cost                       | alt cost inicial       | baix cost               |
| TEMPS                     | temps de configuració inicial baix | temps inicial alt     | temps inicial baix     |
| ELEMENTS                  | staging-DM                       | staging-DWH-DM         | hub/link/satèl·lit      |
| NIVELL D'ESPECIALITZACIÓ  | mitjana-baixa                    | alta                   | mitjana-alta            |
| INTEGRACIÓ DE DADES       | àrees individuals                | àmplia                 | àrees individuals       |
| FLEXIBILITAT	            | menor                            | major                  | mitjana                 |

<p><br></p>

[Metodologies de Data Warehouse](https://gravitar.biz/datawarehouse/metodologias-data-warehouse/)

## Algunes de les pitjors pràctiques en Data Warehousing

1. **Pensar amb una mentalitat OLTP en el moment de dissenyar el Data Warehousing**.
    - **Menysprear l'àrea de staging** al nostre **_DWH_**, sense cap raó concreta.
    - **Menysprear l'àrea de DM** al nostre **_DWH_**, sense cap raó concreta.

2. **Només es pot construir el model físic d'un DWH amb una BD OLAP i no una OLTP**.

3. **Es pot construir el model lògic d'un DWH amb un model ER i no un DDM**.

4. **Improvisar**, sense tenir una metodologia de **_DWH_**.

5. No ser conscient dels **efectes de la metodologia** **_DWH_** escollida.

6. **Permetre que Directors o Gerents de negocis decideixin la configuració del nostre DWH (prioritzant els seus interessos departamentals)**.

7. **Anar directament a la solució DDM sense tenir en compte la fase raw (dades en brut)**.

8. **Confondre el DWH amb una BD OLTP tradicional**.

9. **Considerar el DWH innecessari**.  
   - No utilitzar el **_DWH_** perquè la gent pensa que és innecessari.  
   - Prescindir del **_DWH_** i anar directament al disseny del **_DDM_** en la solució de **_BI_**.

## T'ha interessat l'article?

Si tens alguna pregunta sobre el que s'ha exposat en aquest article, si us plau, fes-ho saber als comentaris.

Si us plau, si t'ha agradat el contingut d'aquest article, deixa un comentari o un "m'agrada". A més, si consideres que és prou bo o pot ajudar altres usuaris de la xarxa, comparteix la publicació en aquesta plataforma. D'aquesta manera, tots junts podem democratitzar el coneixement i potser ajudar altres persones que ho necessitin.

## Llista d'Imatges

- **img001**: Del curs "Data Warehousing i modelatge OLAP" de Platzi.
- **img002**: Del curs "Data Warehousing i modelatge OLAP" de Platzi.
- **img003**: Del curs "Data Warehousing i modelatge OLAP" de Platzi.
- **img004**: Del curs "Data Warehousing i modelatge OLAP" de Platzi.
- **img005**: Del curs "Data Warehousing i modelatge OLAP" de Platzi.
- **img006**: Del curs "Data Warehousing i modelatge OLAP" de Platzi.
- **img007**: Del curs "Data Warehousing i modelatge OLAP" de Platzi.
- **img008**: Del curs "Data Warehousing i modelatge OLAP" de Platzi.
- **img009**: Del curs "Data Warehousing i modelatge OLAP" de Platzi.
- **img010**: Del curs "Data Warehousing i modelatge OLAP" de Platzi.
- **img011**: Del curs "Data Warehousing i modelatge OLAP" de Platzi.
- **img012**: Del curs "Data Warehousing i modelatge OLAP" de Platzi.
- **img013**: [Data Vault: Com estructurar el teu Data Warehouse](https://aprenderbigdata.com/data-vault/).

## Descàrrec de Responsabilitat

**_Platzi_** (abans conegut com Mejorando.la) és una plataforma d'educació en línia a Amèrica Llatina. Va ser fundada el 2011 per l'enginyer colombià **Freddy Vega** i el científic de la computació guatemalenc **Christian Van Der Henst**. [Wikipedia](https://ca.wikipedia.org/wiki/Platzi)

## Ús de ChatGPT

**_Ús de ChatGPT 3.5_**

Aquest projecte ha estat verificat per ortografia, sintaxi i contingut utilitzant [**_ChatGPT 3.5_**](https://chat.openai.com/chat).  

Referència:  
[OpenAI. (2023). ChatGPT (versió del 25 de setembre de 2023) \[Large Language Model\]](https://chat.openai.com/chat)