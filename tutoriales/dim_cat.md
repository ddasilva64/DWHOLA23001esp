# Dimensions

## La dimensió Temps (Calendar Date dimension)

En qualsevol **_DWH_**, es poden trobar diversos cubs amb les seves **_taules de fets_** amb registres amb variables d'interès pel negoci que han de ser estudiades. També, com s'ha comentat anteriorment, cada **_taula de fets_** estarà envoltada de diverses **_taules de dimensió_**, amb els paràmetres que ens permetran fer l'anàlisi dels fets que es volen estudiar. Un paràmetre que amb tota probabilitat tindrem a tots els cubs és el Temps, ja que l'habitual és emmagatzemar els fets a mesura que van succeint al llarg del temps, obtenint així una sèrie temporal de la variable a estudiar.

Atès que el Temps és una dimensió present pràcticament en qualsevol cub d'un sistema **_OLAP_**, mereix una atenció especial. En dissenyar la dimensió Temps (tant per un **_esquema en estrella_** com per a un **_esquema en floc de neu_**) cal prestar especial cura, ja que es pot fer de diverses maneres i no totes són igualment eficients. La forma més comuna de dissenyar aquesta taula és posant com a **_PK_** de la taula la data o data/hora (*taula de temps 1*). Aquest disseny no és dels més recomanables, ja que a la majoria de sistemes de gestió de bases de dades els resulta més costós fer cerques sobre camps de tipus "date" o "datetime", aquests costos es redueixen si el camp clau és de tipus enter, a més, una dada entera sempre ocupa menys espai que una dada de tipus data (el camp clau es repetirà en milions de registres a la **_taula de fets_** i això pot ser molt espai), pel que millorarà el disseny de la taula de temps si s'utilitza un camp "TempsID" de tipus enter com a clau principal (*taula de temps 2*).

<p><br></p>

*Taula de temps (1)*

| Data (PK)   | datetime |
| :---------  | :------: |
| Any         | char(4)  |
| Trimestre   | char(6)  |
| Mes         | char(10) |

<p><br></p>

*Taula de temps (2)*

| TempsID (PK) | integer  |
| :----------- | :------: |
| Data         | datetime |
| Any          | char(4)  |
| Trimestre    | char(6)  |
| Mes          | char(10) |

<p><br></p>

A l'hora de omplir la taula de Temps, si s'ha optat per un camp de tipus enter per la clau, hi ha dues opcions: la que potser sigui més immediata consisteix a assignar valors numèrics consecutius (1, 2, 3, 4...) als diferents valors de les dates. L'altra opció consistiria a assignar valors numèrics del tipus "yyyymmdd", és a dir, que els quatre primers dígits del valor del camp indiquin l'any de la data, els dos següents el mes i els dos últims el dia. Aquest segon mètode aporta certes avantatges sobre el primer, ja que d'aquesta manera es fa que la dada numèrica en sí mateixa aporti per si sola la informació de quina data es tracta. Per exemple, si a la **_taula de fets_** es troba el valor 20040723, sabrem que es refereix al 23 de juliol de 2004; en canvi, amb el primer mètode, es podrien trobar valors com 8456456, i per saber a quina data es refereix aquest valor caldria fer una consulta a la taula de Temps.

A més del camp clau TempsID, la **_taula de fets_** també ha de contenir altres camps que també és important considerar. Aquests camps serien:
- Un camp "**any**": Que contindria valors com '2002', 2003, '2004'...
- Un camp "**mes**": Aquí es poden posar els valors 'gener', 'febrer'... (o de forma abreujada: 'Gen', 'Feb'...). Tot i que això no és incorrecte, es pot millorar si el nom del mes va acompanyat de l'any al qual pertany. Per exemple: '2004 gener', '2004 febrer'... D'aquesta manera s'optimitza la cerca dels valors d'un mes concret, ja que si es busquen els valors pertanyents al mes de "gener de 2003", tota aquesta informació està continguda en un sol camp, el "mes", i no caldria consultar també el camp any.
- Un camp "mesID": Aquest camp hauria de ser de tipus enter i serviria per emmagatzemar valors del tipus 200601 (per a '2006 gener') o 200602 (per a '2006 febrer'). D'aquesta manera és possible realitzar oRPLYDIMenacions i agrupacions per mesos.

De manera anàloga a com s'ha fet amb el camp mes, es podrien afegir més camps com **"Trimestre", "Quinzena", "Setmana"** de tipus text per a poder visualitzar-los, i els seus anàlegs de tipus enter "TrimestreID", "QuinzenaID", "SetmanaID" per a poder realitzar agrupacions i oRPLYDIMenacions. En general, es pot afegir un camp per a cada nivell de granularitat desitjat.

Un altre camp especial que es pot afegir és el **"Dia de la setmana"** ('dilluns', 'dimarts'...). Aquest camp sol afegir-se per a poder fer estudis sobre el comportament dels dies de la setmana en general (no del primer dilluns del mes de gener d'un any concret, aquest tipus d'estudi sol no tenir interès), i per aquesta raó aquest camp no necessita anar acompanyat del mes o de l'any com els camps anteriors. També es pot afegir el seu camp dual "ID" de tipus enter per a poder oRPLYDIMenar i agrupar si cal.

Amb les addicions descrites podríem tenir una taula de Temps com la de la figura (*taula de temps 3*). Aquesta seria vàlida per a un disseny en **_estrella_**. Per a un disseny en **_floc de neu_** hauríem de desglossar la taula de Temps en tantes taules com nivells jeràrquics contingui. Observi's que els camps de tipus "ID" són tots de tipus enter, ja que serà sobre aquests camps sobre els quals es realitzaran la majoria d'operacions i aquestes es realitzaran més eficientment sobre dades enters.

<p><br></p>

*Tabla de tiempos (3)*

| TiempoID (PK) | integer  |
| :------------ | :------: |
| Fecha         | datetime |
| Año           | char(4)  |
| Trimestre     | char(6)  |
| TrimestreID   | int      |
| Mes           | char(10) |
| MesID         | int      |
| Quincena      | char(10) |
| QuincenaID    | int      |
| Semana        | char(10) |
| SemanaID      | int      |
| Día           | char(10) |
| DíaID         | int      |
| DíaSemana     | char(10) |
| DíaSemanaID   | int      |

**_Nota important!_**: Algunes eines BI, com **_Power BI_**, incorporen sistemes automàtics de generació d'aquesta dimensió, però, si es fa un estudi previ evitarem problemes i inconsistències posteriors (que, sovint, són complicades de resoldre). A més, si realitzem altres tipus d'anàlisis o visualitzacions, per exemple, a **_Python_** (fora de **_Power BI_**), aleshores necessitarem dissenyar la dimensió de Temps, adequadament, per nosaltres mateixos.  

<p><br></p>

https://spa.myservername.com/dimensional-data-myodel-data-warehouse-tutorial-with-examples

## Tipus de dimensions

### Dimensions reduïdes (Shrunken Dimensions - SHRKDIM -)

Les **_SHRKDIMs_** ens donen quan tenim la necessitat de mostrar l'agregació de les dades que tenim a **_DWH_**. És a dir, necessitem mostrar una granularitat més alta que la que tenim a **_DWH_**.

Un exemple clàssic és quan necessitem mostrar dades mensuals, encara que tenim dades diàries emmagatzemades a **_DWH_**.

Un altre exemple, menys intuïtiu, seria considerar les Vendes per Ciutat. Aleshores crearíem una **_SHRKDIM_** de Ciutats que es relacionaria amb la **_taula de fets_** (Vendes).

És a dir, les **_SHRKDIMs_** creen el que podríem assimilar a taules de cerca (a un model de BD transaccional). Al **_DDM_**, sempre, hem de pensar en quines preguntes hem de fer a la **_taula de fets_**, poder respondre a les necessitats del negoci (aquesta ha de ser la nostra lògica). Si no les podem respondre amb les **_taules de dimensions_** creades, potser crearem dimensions amb molt poques columnes (**reduïdes**), per respondre-les i aquestes seran les nostres **_SHRKDIMs_**.

**_Nota important_**: Algunes eines de **_BI_**, com **_Power BI_**, tenen la possibilitat de crear segmentacions de **_taules de fets_**, sense extreure **_SHRKDIMs_** de la mateixa. Això ens pot donar com a resultat un **_DDM_** incomplet al **_DWH_**, sobretot si decidim explotar les dades en un altre entorn, com **_Python_** (fora de **_Power BI_**).

### Dimensions conformades (Conformed Dimensions - CONFDIM -)

A un **_DWH_**, una **_CONFDIM_** és una dimensió que té el mateix significat a cada fet amb què es relaciona. Les **_CONFDIMs_** permeten categoritzar i descriure fets i mesures de la mateixa manera a través de múltiples **_taules de fets_** o Data Marts (**_DM_**), garantint la coherència d'informes a tota l'empresa.

Es pot fer referència a una **_CONFDIM_** mitjançant múltiples **_taules de fets_** a un DWH. La dimensió s'implementa com una taula de dimensió única a la qual  fan referència **_diverses taules de fets_** o com a múltiples **_taules de dimensions_** a les quals fan referència **_taules de fets_** en **_DMs_** diferents dins del mateix **_DWH_**. També és possible que diverses **_taules de fets_** dins d'un sol **_DM_** facin referència a la mateixa taula de **_CONFDIM_** així com una **_taula de fets_** en altres **_DM_**.

L'exemple més clàssic de **_CONFDIM_** és la taula de Temps, ja que podem preguntar per les Vendes mensuals, les Planificacions de productivitat dels empleats al més que vé o les Entrades al magatzem el dia 15 del mes passat. En tots els casos, inclús si es tracta d'1 altre **_DM_** (departamental), es tracta de preguntes que fem des de la nostra taula de Temps (**_taula de dimensió_**) a diferents **_taules de fets_** (a un o més **_DM_**, cas que els tinguem contemplats al nostre **_DWH_**).

**_Atenció!_**: Si no hem fet les preguntes de negoci adeqüades, no contemplarem dimensions, que si són prou generalitzades al nostre **_DWH_**, seran **_CONFDIMs_** no contemplades.

### Dimensions brossa (Junk Dimensions - JUNKDIM -)

- Redueix el nombre de dimensions (columnes de caRPLYDIMinalitat baixa) al model dimensional i redueix el nombre de columnes a la **_taula de fets_**. És una col·lecció de codis transnacionals aleatoris, flags o atributs de text.

- Pot optimitza l'espai, ja que les **_taules de fets_** *no haurien d'incloure camps de text ni de caRPLYDIMinalitat baixa* (**pràctica molt qüestionable**). Inclou principalment mesures, claus externes i claus de dimensió degenerades (**_DEGEDIMs_**).

<p><br></p>

Exemple amb una **_taula de fets_** de Cotxes i dos **_taules de dimensions_** de Colors i de Model.

![Sense JUNKDIM](https://i.imgur.com/hYcP0rU.png)  
_Sense JUNKDIM_

<p><br></p>

Exemple amb una **_taula de fets_** de Cotxes i una **_taules de dimensió_** de Característiques del cotxe.

![Amb JUNKDIM](https://i.imgur.com/dw1nykb.png)  
_Amb JUNKDIM_

<p><br></p>

**_Nota_**: És com a mínim una pràctica qüestionable, atés que ens interesa des normalitzar les dimensions i dotar de significat les mètriques de les **_taules de fets_**. Al cas esposat, la pràctica més recomenada seria considerar els Cotxes com a **_taula de dimensió_** d'1 **_taula de fets_** de Vendes i llestos.

### Dimensions de joc de rol (Role-playing dimensions - RPLYDIM -)

Una **_RPLYDIM_** és una dimensió que fa referència a múltiples propòsits dins d'1 **_taula de fets_**.

El millor exemple per una **_RPLYDIM_** és novament una **_taula de dimensió_** de Temps, ja que el mateix atribut de data en la dimensió es pot fer servir per a diferents propòsits a la mateixa **_taula de fets_**. Per exemple a la **_taula de fets_** de Vendes poden estar enregistrades la data de comanda, de lliurament, de transacció, de cancel·lació, de lliurament, etc. Totes aquestes dades es relacionen amb la data de la **_taula de dimensió_** de Temps.

Dificultat de tractament:

1. Poden augmentar la complexitat i la mida de la **_taula de fets_**, ja que cada **_RPLYDIM_** afegeix més columnes a la **_taula de fets_**.  

2. Poden crear confusió i inconsistència entre usuaris i analistes, ja que diferents rols poden tenir diferents significats o interpretacions per un mateix atribut de dimensió. Es a dir, si sols diguem data (p.e.), pot tenir moltes interpretacions, depenet del rol.  

3. Poden ser difícils de mantenir i actualitzar, ja que qualsevol canvi a la **_taula de dimensions_** font pot afectar a múltiples **_RPLYDIMs_** i **_taules de fets_**. És imprescindible, doncs tenir-les molt clares a tot el **_pipeline_**.  

4. A algunes eines de **_BI_**, com ara **_Power BI_**, **no més permet tenir activa** una relació amb la mateixa **_PK_** de la RPLYDIM, malgrat que siguin **_FKs_** diferents a la **_taula de fets_**. Això no més es un tema d'implementacio al **_BI_** i es resol correctament a l'eina (a **_Power BI_**, amb **_DAX_**).  

Aquesta seria una bona solució de **_RPLYDIMs_** a **_Power BI_** amb **_DAX_**:

````DAX
[Vendes totals per data de comanda] :=   
		CALCULATE( 
			SUM(Comandes[Total línia]),
			USERELATIONSHIP(Comandes[Data comanda], Dates[Data])
		)

[Vendes totals lliurades] := 
		CALCULATE( 
			SUM(Comandes[Total línia]),
			USERELATIONSHIP(Comandes[Data lliurament], Dates[Data])
		)
````

etc.

Altres solucions, ens poden complicar la vida com muntar diferents vistes (al nostre **_DWH_**) de la taula de dimensió Temps per cadascuna de les Fks de la taula de fets, es a dir, per cadascuna de les **_RPLYDIMs_** definides. Això ens complica extraoRPLYDIMinàriament les relacions i les segmentacions en l'explotació del **_DDM_** al **_BI_** (p.e.).

**_Atenció!_**: És imprescindible definir bé les **_RPLYDIMs_** per que funcioni bé el nostre **_pipeline_**. Això implica que, com a **Enginyers de Dades** **hem de conèixer**, al **_pipeline_** del nostre projecte, **des del negoci del client fins la solució final** en que mostrarem el resultat.

### Dimensions degenerades (Degenerate Dimensions - DEGEDIM -)

Hi pot haver pocs atributs que no puguin ser dimensions (mètriques) ni fets (mesures) però necessiten anàlisi. Tots aquests atributs es poden traslladar a dimensions degenerades.

Per exemple, podem considerar el número de comanda, el número de factura, etc., com a atributs de **_DEGEDIMs_**.

Una factura pot tenir un atribut de nom de client, però ja forma part de la **_taula de dimensió_** Client. Una **_DEGEDIM_** és una part de la **_taula de fets_**, que no és una mesura, però que no deixa de ser una dimensió, la qual cosa es contradictòria.

**_Avís!_**: Definir **_DEGEDIMs_** fora d'1 **_taula de fets_** és un error.

## Dimensions estabilizadores (Outrigger Dimensions - OUTGDIM -)

Dimensions que fan referència a altres dimensions mitjançant claus externes (subdimensions). 

Les **_OD_** sovint es consideren com un antipatró de **_DWH_** i s'acostuma a considerar una pràctica millor, utilitzar algunes **_taules de fets_** que relacionin les dues dimensions.  

Un exemple seria considerar Vendes, Productes i Categories. 

Podem considerar-les com:

- **_Taula de fets_**: Vendes.
- **_Taula de dimensió_**: Productes **_OUTGDIM_** (conté el **_FK_** de la Categoria).  
- *Taula de subdimensió*: Categories (conté el **_PK_** de la Categoria).

Un altre manera de vure-les és com:

- **_Taula de fets_**: Vendes
- **_Taula de dimensió_**: Productes. Cada producte conté la seva Categoria incorporada.  

El segon model està des normalitzat i és una millor solució a un **_DDM_**.

**_Avís!_**: Hem d'intentar evitar les **_OUTGDIM_** al nostre **_DDM_**.

## Dimensions estàtiques (Static Dimensions - STATDIM -)

Venen del no-res, són dimensions que no existeixen la BD **_OLTP_** d'origen, com a entitats.  

Les dimensions estàtiques es generen mitjançant un script **_SQL_**, un procediment emmagatzemat o un fitxer extern i es generen manualment.

**_Avís!_**: No hem de confiar en els usuaris (Directors o Gerents), si manquen entitats a la BD **_OLTP_** (que es transformaran en dimensions al **_DDM_**), les hem d'afegir-les, abans del **_DWH_** i s'han de relacionar si no és al **_DWH_**.

## Dimensions que canvien lentament (Slowly Changing Dimensions - SCD -)

Una **_SCD_** a un **_DWH_** és una dimensió que conté dades relativament estàtiques que poden canviar lentament i de manera imprevisible, en lloc de seguir un horari regular. Alguns exemples de dimensions típiques que canvien lentament són entitats com Localitzacions Geogràfiques, Clients o Noms de Productes. ([Wikipedia](https://en.wikipedia.org/wiki/Slowly_changing_dimension)).  

<p><br></p>

![001-Dimensió](https://i.imgur.com/kXDzc6e.png)  
_Dimensió (imatge 001)_  

<p><br></p>

### Tipus d'atributs

- **_Jeràrquics_**: Permeten passar del general al particular, o consolidar i desagregar. Per exemple: país.
- **_Descriptius_**: Informació rellevant, que és purament descriptiva. Per exemple: adreça, telèfon, mida, clima.
- **_Control_**: Dades d'auditoria, que no pertanyen al coneixement del negoci. Per exemple: la data d'enregistrament de la dada.

### Tipus de SDC

A una BD dimensional, els **_SCDs_** són aquelles dimensions que canvien amb el temps, però no necessàriament de manera constant o previsible. **Per exemple, a una taula de dades de Clients, l'adreça del client pot canviar amb el temps, però no tots els clients canvien l'adreça a la mateixa velocitat. Alguns clients poden canviar l'adreça cada mes, mentre que altres poden mantenir la mateixa adreça durant anys**.

La gestió adequada de les **_SCD_** és important per mantenir l'exactitud i la integritat de les dades dimensionals en una base de dades, ja que permet als usuaris realitzar anàlisis històriques i comparar dades al llarg del temps.

Hi ha diferents tipus de **_SDC_**, que es classifiquen segons la manera com es gestionen els canvis a les dimensions.

- **_Tipus 0_**: Preserva l'original.
- **_Tipus 1_**: Es sobreescriu l'atribut actualitzat, es a dir, no es desen dades històriques.
- **_Tipus 2_**: Afegeix un nou registre amb el canvi, amb la qual cosa, es manté un registre històric.
- **_Tipus 3_**: Afegeix un nou atribut "anterior". Això guaRPLYDIMa històric, però afegint una nova columna per a l'atribut anterior i el modificat.
- **_Tipus 4_**: Històric separat. **_SCD-4_** sovint s'utilitza amb "taules d'historial". Aquest mètode és similar a com funcionen les taules d'auditoria de bases de dades i les tècniques de captura de dades de canvi.
- **_Tipus 5_**: Combina els enfocaments dels tipus 1 i 4 (1+4=5).
- **_Tipus 6_**: Combina els enfocaments dels tipus 1, 2 i 3 (1+2+3=6).
- **_Tipus 7_**: Consta de dues dimensions separades. Una dimensió es manté com un **_SCD-2_** típic. La seva clau substituta s'utilitza a la taula de fets. S'inclou un segon **_SCD-1_** que conté la vista "actual" de la dimensió. La seva clau duradora (del sistema **_OLTP_**) s'inclou a la taula de fets.

#### Elecció del tipus de SCD

La elecció del tipus de **_SCD_** a utilitzar depèn de les necessitats específiques del **_DWH_** i les necessitats d'anàlisi. És important tenir en compte factors com la importància de les dades històriques, la freqüència dels canvis a les dimensions i les implicacions d'emmagatzematge i rendiment de cada enfocament.

Les **_SCDs_** són un aspecte crucial dels **_DWHs_** ja que permeten representar les dades al llarg del temps, facilitant així un anàlisi i informes històrics precisos.

## SDC-0

### Atributs SCD-0

Els atributs al **_SCD-0_** mai canvien i s'assignen a atributs que tenen **valors** duradors o que es descriuen com a "**originals**".  
Exemples: data de naixement, puntuació de crèdit original.  
S'aplica a la majoria dels atributs de les dimensions. 

El que vol dir, tot plegat, és que **cada vegada s'importen tots els registres de la taula transaccional (esborrant els que existien abans a la taula dimensional)**. No hi ha canvis, es a dir, els registres a la taula dimensional són els "**originals**" a la taula transaccional, sense canvis.

Exemple: Imaginem que importem directament una taula de productes dins del nostre **_DM_** al **_DWH_**. Sería una **_SCD-0_**.

**_Taula transaccional dia 1_**

| ID producte | Descripció | Talla      | Preu   |
| :---------- | :--------- | :--------- | -----: |
| a003        | pantaló H  | S          | $20.19 |
| a001        | pantaló H  | M          | $21.20 |
| a004        | pantaló H  | L          | $22.21 |
| a016        | pantaló H  | XL         | $23.22 |

**_Taula dimensional dia 1_**

| ID producte | Descripció | Talla      | Preu   |
| :---------- | :--------- | :--------- | -----: |
| a003        | pantaló H  | S          | $20.19 |
| a001        | pantaló H  | M          | $21.20 |
| a004        | pantaló H  | L          | $22.21 |
| a016        | pantaló H  | XL         | $23.22 |

**_Taula transaccional dia 2_**

| ID producte | Descripció | Talla      | Preu         |
| :---------- | :--------- | :--------- | -----------: |
| a003        | pantaló H  | S          | $20.19       |
| a001        | pantaló H  | M          | $21.20       |
| a004        | pantaló H  | L          | $22.21       |
| a016        | pantaló H  | XL         | **_$24.23_** |

**_Taula dimensional dia 2_**

| ID producte | Descripció | Talla      | Preu         |
| :---------- | :--------- | :--------- | -----------: |
| a003        | pantaló H  | S          | $20.19       |
| a001        | pantaló H  | M          | $21.20       |
| a004        | pantaló H  | L          | $22.21       |
| a016        | pantaló H  | XL         | **_$24.23_** |

## SCD-1

La **_SCD-1_** significa que la dada antiga s'obre amb nova dada cada vegada que es produeix un canvi.  
**És apropiat quan les dades històriques no són importants o quan es poden recuperar d'altres fonts**.  

Exemple: Imaginem el registre d'1 alumne en una Facultat i que després, per que s'ho repensa, canvia de Facultat.  

**_Taula transaccional en el moment del registre (dia 1)_**

| **Id_Estudiant** | **Nom Complet** | **Facultat** |
| :--------------: | :-------------- | :----------: |
| EST12345         | Jordi Ferrer    | Màrqueting   |

**_Taula dimensional en el moment del registre (dia 1)_**

Creem un Id_Estudiant que és una clau substituta o subrogada -**_SK_**-, que té com a objectiu millorar el rendiment en les cerques de la dimensió amb una clau numèrica.

| **Id_Estudiant** | **Codi_Estudiant** | **Nom Complet** | **Facultat** | 
| :--------------: | :----------------: | :-------------  | :----------: |
| 1                | EST12345           | Jordi Ferrer    | Màrqueting   |

El dia 2 Jordi Ferrer canvia de Facultat a Enginyeria (s'ho ha repensat).

**_Taula transaccional en el moment del canvi de Facultat (dia 2)_**

| **Id_Estudiant** | **Nom Complet** | **Facultat** |
| :--------------: | :-------------- | :----------: |
| EST12345         | Jordi Ferrer    | Engineering  |

**_Taula dimensional en el moment del canvi de Facultat (dia 2)_**

| **Id_Estudiant** | **Codi_Estudiant** | **Nom Complet** | **Facultat** |
| :--------------: | :----------------: | :-------------- | :----------: |
| 1                | EST12345           | Jordi Ferrer    | Engineering  |

## SCD-2

La **_SCD-2_** **es sol utilitzar quan es requereix un anàlisi històric i la dimensió canvia relativament poc sovint**. Quan es produeixi algun canvi en els valors dels registres, s'afegirà una nova fila i s'hauran de completar les dades referides a l'historial de canvis.

Exemple: Imaginem el mateix exemple anterior però volem desar dades històriques.  

**_Inscripció_**

**_Taula transaccional en el moment del registre (dia 1)_**

| **Id_Estudiant** | **Nom Complet** | **Facultat** |
| :--------------: | :-------------- | :----------: |
| EST12345         | Jordi Ferrer    | Marketing    |

**_Taula dimensional en el moment del registre (dia 1)_**

| **Id_Estudiant** | **Codi_Estudiant** | **Nom Complet** | **Facultat** | **Data_Inici** | **Data_Fi** | **Versió** | **Actual** |
| :--------------: | :----------------: | :-------------- | :----------: | :------------: | :---------: | :--------: | :--------: |
| 1                | EST12345           | Jordi Ferrer    | Marketing    | 01/01/2020     |             | 1          | True       |

El dia 2 Jordi Ferrer canvia de Facultat a Enginyeria (s'ho ha repensat).

**_Taula transaccional en el moment del canvi de Facultat (dia 2)_**

| **Id_Estudiant** | **Nom Complet** | **Facultat** |
| :--------------: | :-------------- | :----------: |
| EST12345         | Jordi Ferrer    | Enginyeria   |

**_Taula dimensional en el moment del canvi de Facultat (dia 2)_**

| **Id_Estudiant** | **Codi_Estudiant** | **Nom Complet** | **Facultat** | **Data_Inici** | **Data_Fi** | **Versió** | **Actual** |
| :--------------: | :----------------: | :-------------- | :----------: | :------------: | :---------: | :--------: | :--------: |
| 1                | EST12345           | Jordi Ferrer    | Marketing    | 01/01/2020     | 01/01/2020  | 1          | false      |
| 2                | EST12345           | Jordi Ferrer    | Enginyeria   | 02/01/2020     |             | 2          | True       |

## SCD-3

La **_SCD-3_** s'utilitza quan és important seguir els canvis d'atributs específics mentre es manté la simplicitat en el model de dades. Requereix que s'afegeixi a la taula de dimensió una columna addicional per cada columna els valors dels quals es vol mantenir un historial de canvis.

Exemple: Imaginem el mateix exemple anterior però volem desar dades històriques. 

**_Inscripció_**

**_Taula transaccional en el moment del registre (dia 1)_**

| **Id_Estudiant** | **Nom Complet** | **Facultat** |
| :--------------: | :-------------- | :----------: |
| EST12345         | Jordi Ferrer    | Marketing    |

**_Taula dimensional en el moment del registre (dia 1)_**

| **Id_Estudiant** | **Codi_Estudiant** | **Nom Complet** | **Facultat_antiga** | **Facultat_nova** |
| :--------------: | :----------------: | :-------------- | :-----------------: | :---------------: |
| 1                | EST12345           | Jordi Ferrer    |                     | Marketing         |

El dia 2 Jordi Ferrer canvia de Facultat a Enginyeria (s'ho ha repensat).

**_Taula transaccional en el moment del canvi de Facultat (dia 2)_**

| **Id_Estudiant** | **Nom Complet** | **Facultat** |
| :--------------: | :-------------- | :----------: |
| EST12345         | Jordi Ferrer    | Enginyeria   |

**_Taula dimensional en el moment del canvi de Facultat (dia 2)_**  

| **Id_Estudiant** | **Codi_Estudiant** | **Nom Complet** | **Facultat_antiga** | **Facultat_nova** |
| :--------------: | :----------------: | :-------------- | :-----------------: | :---------------: |
| 1                | EST12345           | Jordi Ferrer    | Marketing           | Enginyeria        |

## SCD-4 (història separada)

La idea darrere de **_SCD-4_** és gairebé la mateixa que **_SCD-2_**. Aquí, també mantindrem les dades històriques, però no a la mateixa taula. Crearem una nova taula històrica per mantenir-hi els registres antics. La separació de les dades històriques redueix les nostres dimensions i, per tant, redueix la complexitat i millora el rendiment si la majoria d'usos només necessiten el valor actual. **_SCD-4_** proporciona una solució per gestionar els canvis ràpids a les **_taules de dimensions_**.

A l'exemple següent, imaginem el següents canvis a una taula de Productes, les quals enregistrarem a una **_taula de dimensió_** i a una d'hidtòrica:

**_Taula transaccional de Productes (dia 1/1/2020)_**

| **ID** | **Descripció** | **Preu unitari** |
| :----: | :------------- | :--------------- |
| 100    | Producte 1     | 99,99            | 
| 101    | Producte 2     | 101,01           |

**_Taula dimensional de Productes (dia 1/1/2020)_**

| **SK** | **ID** | **Descripció** | **Preu unitari** | **Data Càrrega** | 
| :----: | :----: | :------------- | :--------------- | :--------------: | 
| 1      | 100    | Producte 1     | 99,99            | 01/01/2020       | 
| 2      | 100    | Producte 2     | 101,01           | 01/01/2020       |

**_Taula històrica Productes\_Històrics (dia 1/1/2020)_**

| **SK** | **ID** | **Descripció** | **Preu unitari** | **Data Inici** | **Data Final** | **Data Actualització** |
| :----: | :----: | :------------- | :--------------- | :------------: | :------------: | :--------------------: | 
| 1      | 100    | Producte 1     | 99,99            | 01/01/2020     |                | 01/01/2020             |
| 2      | 101    | Producte 2     | 101,01           | 01/01/2020     |                | 01/01/2020             |

**_Taula transaccional de Productes (dia 2/1/2020)_**

| **ID** | **Descripció** | **Preu unitari** |
| :----: | :------------- | :--------------- |
| 102    | Producte 3     | 102,02           | 
| 101    | Producte 2     | 101,01           |

**_Taula dimensional de Productes (dia 2/1/2020)_**

| **SK** | **ID** | **Descripció** | **Preu unitari** | **Data Càrrega** | 
| :----: | :----: | :------------- | :--------------- | :--------------: |
| 3      | 102    | Producte 3     | 102,02           | 02/01/2020       | 
| 2      | 101    | Producte 2     | 101,01           | 02/01/2020       |

**_Taula històrica Productes\_Històrics (dia 2/1/2020)_**

| **SK** | **ID** | **Descripció** | **Preu unitari** | **Data Inici** | **Data Final** | **Data Actualització** |
| :----: | :----: | :------------- | :--------------- | :------------: | :------------: | :--------------------: |
| 1      | 100    | Producte 1     | 99,99            | 01/01/2020     | 02/01/2020     | 01/01/2020             |
| 2      | 101    | Producte 2     | 101,01           | 01/01/2020     |                | 01/01/2020             |
| 3      | 102    | Producte 3     | 102,02           | 02/01/2020     |                | 02/01/2020             |

Ara imaginem que el dia 3 modifiquem el preu unitari del Producte 2:

**_Taula transaccional de Productes (dia 3/1/2020)_**

| **ID** | **Descripció** | **Preu unitari** |
| :----: | :------------- | :--------------- |
| 102    | Producte 3     | 102,02           | 
| 101    | Producte 2     | 104,04           |

**_Taula dimensional de Productes (dia 3/1/2020)_**

| **SK** | **ID** | **Descripció** | **Preu unitari** | **Data Càrrega** | 
| :----: | :----: | :------------- | :--------------- | :--------------: |
| 3      | 102    | Producte 3     | 102,02           | 03/01/2020       | 
| 2      | 101    | Producte 2     | 103,03           | 03/01/2020       |

**_Taula històrica Productes\_Històrics (dia 2/1/2020)_**

| **SK** | **ID** | **Descripció** | **Preu unitari** | **Data Inici** | **Data Final** | **Data Actualització** |
| :----: | :----: | :------------- | :--------------- | :------------: | :------------: | :--------------------: | 
| 1      | 100    | Producte 1     | 99,99            | 01/01/2020     | 02/01/2020     | 01/01/2020             | 
| 2      | 101    | Producte 2     | 101,01           | 01/01/2020     |                | 01/01/2020             |
| 2      | 101    | Producte 2     | 104,04           | 01/01/2020     |                | 03/01/2020             |
| 3      | 102    | Producte 3     | 102,02           | 01/01/2020     |                | 02/01/2020             |

Aquest mètode és semblant a com funcionen les taules d'**auditoria de BD** i les tècniques de captura de dades de canvi. Serveix per **verificar com canvien les dades**. En aquest model la dimensió s'ha transformat en una *mini-dimensió* i estarà relaracionada (igual que la dimensió històrica, pel **_SK_**).

## SCD-5

El SCD-5 es basa en la *dimensió històrica* **_SCD-4_** incrustant una clau de "*perfil actual*" de *dimensió històrica* a la *dimensió base* que ha estat substituïda com a atribut de **_SCD-1_**. Aquest enfocament es coneix com a tipus 5 perquè 4 + 1 = 5.

## SCD-6

El SCD-6 combina els enfocaments dels tipus 1, 2 i 3 (1+2+3=6).

**_Inscripció a la Facultat d'Astrofísica Blava_**

Taula transaccional

| **Id_Estudiant** | **Nom Complet** | **Facultat**      |
| :--------------: | :-------------- | :---------------: |
| EST12345         | Jordi Ferrer    | Astrofísica blava |

Taula de dimensió (després de l'ETL)

Ens adonem, en l'ETL, que l'Astrofísica Blava és poc intel·ligent. Tothom sap que l'astrofísica és verda.

| **Id_Estudiant** | **Codi_Estudiant** | **Nom Complet** | **Facultat_antiga** | **Facultat_nova** | **Data_inici** | **Data_final** | **Facultat_actual** |
| :--------------: | :----------------: | :-------------- | :-----------------: | :---------------: | :------------: | :------------: | :-----------------: |
| 1                | EST12345           | Jordi Ferrer    |                     | Astrofísica verda | 18/08/2023     |                | N                   |

**_Canvi de Facultat a Clima Salat_**

Taula transaccional

| **Id_Estudiant** | **Nom Complet** | **Facultat** |
| :--------------: | :-------------- | :----------: |
| EST12345         | Jordi Ferrer    | Clima salat  |

Taula de dimensió (després de l'ETL)

| **Id_Estudiant** | **Codi_Estudiant** | **Nom Complet** | **Facultat_antiga** | **Facultat_nova** | **Data_inici** | **Data_final** | **Facultat_actual** |
| :--------------: | :----------------: | :-------------- | :-----------------: | :---------------: | :------------: | :------------: | :-----------------: |
| 1                | EST12345           | Jordi Ferrer    |                     | Astrofísica verda | 18/08/2023     | 19/08/2023     | N                   |
| 2                | EST12345           | Jordi Ferrer    | Astrofísica verda   | Clima salat       | 19/08/2023     |                | Y                   |

**_Canvi de Facultat, de nou, a Astrofísica verda_**

Taula transaccional

| **Id_Estudiant** | **Nom Complet** | **Facultat**      |
| :--------------: | :-------------- | :---------------: |
| EST12345         | Jordi Ferrer    | Astrofísica verda |

Taula de dimensió (després de l'ETL)

| **Id_Estudiant** | **Codi_Estudiant** | **Nom Complet** | **Facultat_antiga** | **Facultat_nova** | **Data_inici** | **Data_final** | **Facultat_actual** |
| :---------------: | :---------------: | :-------------- | :-----------------: | :---------------: | :------------: | :------------: | :----------------: |
| 1                | EST12345           | Jordi Ferrer    |                     | Astrofísica verda | 18/08/2023     | 19/08/2023     | N                  |
| 2                | EST12345           | Jordi Ferrer    | Astrofísica verda   | Clima salat       | 19/08/2023     | 20/08/2023     | N                  |
| 3                | EST12345           | Jordi Ferrer    | Clima salat         | Astrofísica verda | 20/08/2023     |                | Y                  |

## SCD-7 (Híbrid: clau subrogada i natural)

Una implementació alternativa és col·locar tant la clau substituta com la clau natural a la taula de fets.   

Aquest mètode permet enllaços més flexibles a la dimensió, fins i tot si s'ha utilitzat **_SCD-2_** en lloc de **_SCD-6_**.

## Algunes de les pitjors pràctiques en treballar amb Dimensions

1. **No dissenyar al DWH la dimensió de Temps, esperant que la feina la faci l'eina de BI**.

2. **Dissenyar el DDM amb JUNKDIMs, en comptes de taules de fets normals**.

3. **No crear SHRKDIMs i esperar a que ho faci l'eina BI**.

4. **No conèixer prou el negoci, lo qual pot portar a no contemplar CONFDIMs imprescindibles al nostre DDM**.  

5. **No definir bé les RPLYDIMs per desconeixement d'algun punt al pipeline del nostre projecte**.

6. **Definir DEGEDIMs**.

7. **Hem d'evitar les OUTGDIM al nostre DDM**

8. **Si confies en els usuaris (Directors o Gerents), mancaran STATDIMs al teu DDM**.

9. 

## T'ha interessat l'article?

Si tens cap dubte amb relació al que s'ha exposat en aquest article, si us plau, fes-m'ho saber als comentaris.

Si t'ha agradat el contingut d'aquest article, deixa un comentari o un "m'agrada". A més, si consideres que és prou bo o pot ajudar altres usuaris de la xarxa, comparteix la publicació en aquesta plataforma. D'aquesta manera, tots junts podem democratitzar el coneixement i potser ajudar altres persones que ho necessitin.

## Llista d'Imatges

- **img001**: Del curs "Data Warehousing i modelatge OLAP" de Platzi.

## Descàrrec de Responsabilitat

**_Platzi_** (abans conegut com Mejorando.la) és una plataforma d'educació en línia a Amèrica Llatina. Va ser fundada el 2011 per l'enginyer colombià **Freddy Vega** i el científic de la computació guatemalenc **Christian Van Der Henst**. ([Wikipedia](https://ca.wikipedia.org/wiki/Platzi))

## Ús de ChatGPT

**_Ús de ChatGPT 3.5_**

Aquest projecte ha estat verificat per ortografia, sintaxi i contingut utilitzant [**_ChatGPT 3.5_**](https://chat.openai.com/chat).  

Referència:  
[OpenAI. (2023). ChatGPT (versió del 29 de setembre de 2023) \[Large Language Model\]](https://chat.openai.com/chat)






