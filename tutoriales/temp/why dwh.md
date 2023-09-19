# Per què DWH?

| Amb DWH                                                                            | Sense DWH                                                           |
| :--------------------------------------------------------------------------------- | :------------------------------------------------------------------ |
| Recull i **pot relacionar qualsevol tipus de dades d'origen heterogenis (Oracle, Excel, CSV, etc.)**, organitzades en files i columnes | Temps de disseny, implementació BD i de processos **_ETL_** (**Power Query**) |
| Permet **des normalitzar taules, reduir i expandir dimensions** <p></p> 1. **Eficàcia** al **_DWH_** final. <p></p> 2. Permet **eliminar relacions m:n**, imposibles d'aliminar del model final <p></p> 3. Permet **eliminar camps calculats** al **_DDM_** final (es nota molt amb **_DQ_**) <p></p> 4. **Fàcil implementació** <p></p> 5. **Necessita eines addicionals** (**_PDI_**, p.e.) | Es poden **des normalitzar taules amb pèrdues de granularitat** (p.e.) <p></p> 1. És **molt menys eficient** al **_DDM_** <p></p> 2. **No soporta tota la funcionalitat** (derivada de la granularitat) <p></p> 3. **No sempre es poden eliminar relacions m:n, ni camps calculats** <p></p> 4. **Difícil d'implementar** <p></p> 5. **No necessita eines addicionals**, però exigeix molta feina a **_Power Query_** |
|Permet **_des comprimir dimensions_** | **No es poden des comprimir dimensions**. **Mai no es pot arrivar a una granularitat prou baixa** com per que sigui la relació amb la taula de fets. **Com a màxim podem des normalitzar amb un procés extern a Power BI (en Python, p.e.), per facilitar la usabilitat a l'usuari** |
| Permet **reduir dimensions de manera molt senzilla** | La reducció de dimensions **és complicada o impossible** <p></p> Esforç d'**_ETL_** (a **_Power Query_**, en llenguatge **_M_**), lo qual implica **temps més gran de càrrega al BI** |
| La majoria dels passos d'**_ETL_** (a **_Power Query_**) són absorbits per l'**_ETL_** (amb **_PDI_**) | **Càrrega lenta o molt lenta** |
| **_Direct Query (DQ)_** **no té sentit**, a no ser que muntem el **_DWH_** en **_SQL Server_**. Si es fa això, aleshores, **com que ja no hi ha de camps calculats, és molt més ràpid** | **DQ és lent o molt lent** |
| **Amb àrea de staging es poden obviar les vistes i taules temporals** que transformen dades a la BD **_OLTP_** | El millor (de vegades l'únic), que es pot fer és **treure les transformacions de Power BI a la BD, carregant el sistema OLTP** |
| Com que les dades **_raw_** van al staging, sense perdre la integritat de les dades originals, **es poden modificar al** **_DWH_** | Per no espatllar una vista, es munta un altre, amb lo qual **es pot produir una inflació de taules temporals i/o vistes a la BD OLTP** |
| **Es segmenta per target del negoci el DWH**, això dona **_DM_** <p></p> Físicament són **esquemes de la mateixa BD** | **Les vistes i taules es barregen a la BD OLTP**. **Sols es poden distinguir pel prefix del nom** <p></p> **Es pot reduir el rendiment de la BD (OLTP)**, sobre tot per espai ocupat i temps de càrrega |

## Avantatges

1. **Ajuda a predir situacions futures** (prospectiva).  
    Mitjançant l'ús de **_DWH_** els usuaris poden accedir a una immensa quantitat de dades històriques, cosa que permetrà que analitzin i creïn comparatives sobre diferents períodes de temps i tendències amb l'objectiu de realitzar prediccions futures a partir de dades precises, coherents i de qualitat.
2. **Integra fonts de dades heterogènies**.  
    Integració de multitud de fonts de dades, la qual cosa redueix, a més, la pressió al sistema de producció (**_OLTP_**).
3. **Optimitza les dades de cara al BI**.  
    Proporciona informació exacta sobre diferents activitats i/o departaments que serveixi en l'elaboració d'informes i consultes específiques, reduint el temps que s'hi dediqui i permeti prendre decisions fonamentades.
4. **Permet l'accés i circulació fluits de dades entre àrees**.  
    L'ús d'un **_DWH_** permet als usuaris accedir amb facilitat a les dades rellevants de diferents orígens en un sol lloc. L'accés a la informació és més ràpid que a la BD **_OLTP_**.
5. **Guarda les dades de manera segura**.
    El procés d'**_ETL_** és automàtic, i es pot implementar seguretat a nivell de **_DWH_**, **_DM_**, **_BI_**, etc.
6. **Permet fer una bona estratègia de BI**.  
    La reestructuració i la integració de les dades facilitarà moltíssim a l'usuari la generació i l'ús d'informes i anàlisis.

## Diferències entre DWH i DB

- Un **_DWH_** permet recuparar dades d'1 **_DDM_** (adaptat a **_BI_**, reporting, analítica, etc.), mentre que a una BD, sols permet obtenir dades de taules del **_BDR_**.  

- Un **_DWH_** pot ser **_OLAP_**, mentre que una BD és **_OLTP_**.  

- Al **_DWH_** es poden integrar dades de fonts heterogènies (BD, Excels, CSVs, XMLs, JSONs, etc.), mentre que a la BD han de provenir d'un únic origen (transaccional).  

- El registre de dades a **_DWH_** es realitza mitjançant càrregues d'escriptura massives per lots (**_ETL_**), mentre que a la DB aquest procés es realitza segons la disponibilitat de noves dades.

- L'emmagatzematge de dades a **_DWH_** s'optimitza perquè l'accés a consultes es pugui fer a la màxima velocitat a través de columnes, mentre que a la DB l'optimització se centra en les operacions d'escriptura registrades mitjançant files.

- L'accés a les dades a **_DWH_** s'optimitza per minimitzar les operacions entrants i sortints alhora que es maximitza el rendiment de les dades; en BDs s'accedeix a volums importants d'operacions de lectura reduïdes.

## Estructures

L'arquitectura d'un **_DWH_** es pot dividir en tres estructures simplificades: bàsica, bàsica amb una àrea d'assaig (staging) i bàsica amb àrea d'assaig (staging) i **_DMs_**.

### Bàsica

Sistemes operatius i fitxers plans proporcionen dades en brut (**_raw_**) que s'emmagatzemen conjuntament amb les metadades. Es pot accedir per anàlisi, generació d'informes i mineria.  

### Bàsica amb staging

Quan s'afegeix un àrea de staging, aquesta es pot col·locar entre les fonts de dades i el **_DWH_**. Proporciona un lloc on les dades es poden netejar abans d'entrar al magatzem.   

### Bàsica amb staging i DMs

Es pot personalitzar l'arquitectura del **_DWH_** per diferents grups dins de l'organització. Això es pot fer afegint DMs, que són sistemes dissenyats per cadascuna de les línies de negoci en particular. Es pot establir seguretat per **_DM_**, si s'escau.

## Conclusió

Un **_BI_** és una mena de "coll d'ampolla" de les dades reconegudes, del ***_DWH_***, que si arriben de forma exacta i útil, aleshores estan llestes per fer-les servir. Cas contrari, hem de fer transformacions, que de vegades són imposibles al **_BI (Power Query)_**.