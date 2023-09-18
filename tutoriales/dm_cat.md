# Modelatge de Dades (MD) - cat -

## Què és el MD?

**_MD_** és el **procés de crear un esquema simplificat de les dades en un sistema de programari**.

Un model de dades (**_MD_**) es pot considerar com un diagrama de flux que il·lustra les entitats de dades, els seus atributs i les relacions entre les entitats. Permet documentar els requisits de dades de l'aplicació i identificar errors de desenvolupament abans d'escriure qualsevol codi.

## Fases

- **Conceptual**: **És una visualització de nivell alt (lògica)**. Descriu els tipus de dades que es necessiten, com es relacionen les diferents entitats i les regles de negoci que utilitzaran. **El públic objectiu són els Gestors**.

- **Lògica**: **És un nivell lògic, menys abstracte que el conceptual**. Mostra com estan relacionades les entitats de dades i descriu les dades des d'una perspectiva tècnica. **El públic objectiu són els Tècnics per ajudar-los a comprendre els dissenys de bases de dades**.

- **Física**: **És la base per crear un** **_MD_**. És específica del sistema de gestió de bases de dades (**_DBMS_**) o del programari d'aplicació que es implementarà. **El públic objectiu són els dissenyadors de bases de dades per ajudar-los a crear dissenys de bases de dades**.

<p><br></p>

## Modelatge Dimensional de Dades (MDD)

**_MDD_** s'utilitza principalment en **_DWH_** i **_MD_** per donar suport a aplicacions de **_BI_**. Consisteix en taules de fets (**_fact_**) i taules de dimensions (**_dim_**).

Tipus de **_MDD_**:
- Esquemes d'**_Estrella_**.

    ![001-Esquema d'estrella amb 5 dimensions](https://i.imgur.com/4HPriuo.png)
    _Esquema d'estrella amb 5 dimensions_

- Esquemes de **_Cristall de Neu_**.

    ![002-Esquema de cristall de neu amb 5 dimensions](https://i.imgur.com/1Ata1st.png)
    _Esquema de cristall de neu amb 5 dimensions_

- Esquemes de **_Constel·lació o Galàxia_** (derivats dels tipus anteriors).

    ![003-Esquema de constel·lació o galàxia](https://i.imgur.com/AEqg4iO.png)
    _Esquema de constel·lació o galàxia_

# Algunes de les pitjors pràctiques en Modelatge Dimensional de Dades

1. Anar **directament a la fase física**.

2. Pensar que el **_model ER_** **és igual que** **_el MDD_**.

3. **No permetre que els empleats de TI dissenyin el MDD (per exemple, Directors o Gestors de negocis)**.

4. **Relacionar directament les taules de fets**.

5. No avaluar constantment els requisits i les realitats per proporcionar una solució de BI que sigui acceptada pels usuaris de negocis i doni suport al seu procés de presa de decisions.

## Llista d'Imatges

- **img001**: Del curs "Data Warehousing i modelatge OLAP" de Platzi.
- **img002**: Del curs "Data Warehousing i modelatge OLAP" de Platzi.
- **img003**: De l'autor de l'article.

## Descàrrec de Responsabilitat

**_Platzi_** (abans conegut com Mejorando.la) és una plataforma d'educació en línia a Amèrica Llatina. Va ser fundada el 2011 per l'enginyer colombià **Freddy Vega** i el científic de la computació guatemalenc **Christian Van Der Henst**. [Wikipedia](https://ca.wikipedia.org/wiki/Platzi)

## Ús de ChatGPT

**_Ús de ChatGPT 3.5_**

Aquest projecte ha estat verificat per ortografia, sintaxi i contingut utilitzant [**_ChatGPT 3.5_**](https://chat.openai.com/chat).  

Referència:  
[OpenAI. (2023). ChatGPT (versió del 18 de setembre de 2023) \[Large Language Model\]](https://chat.openai.com/chat)