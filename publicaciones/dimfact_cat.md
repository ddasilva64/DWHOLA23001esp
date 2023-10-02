# Dimensions i Fets - cat -

Per construir un **_dm_**, cal tenir en compte un conjunt de tècniques i conceptes per dissenyar el nostre **_DWH_**. Una part fonamental d'aquest és la tipologia de taules on desem les dades.

- **_Taules de fets_**
	- Allò que volem mesurar o analitzar.
	- Són les taules principals del **_dm_**.
	- Contenen claus foranes (**_FK_**) per relacionar-se amb les **_taules de dimensió_**.
	- Contenen mètriques (també anomenades mesures), les quals representen allò que volem mesurar o analitzar.
	- Generalment són valors numèrics.
	- Eviten la redundància d'atributs a les **_taules de dimensions_**.
	- Normalment tenen molts (milions) de registres.
	- Exemples: Vendes, Compres, Moviments de Comptabilitat, Moviments de Magatzem, etc.

- **_Taules de dimensió_**
	- Com volem mesurar allò que contenen les **_taules de fets_**.
	- Són taules simples i normalitzades.
	- Contenen claus primàries (**_PK_**) per relacionar-se amb les **_taules de fets_**.
	- Els atributs són característiques de les **_taules de fets_**.
	- No hi ha límit en el seu nombre al **_DWH_**.
	- Poden contenir una o diverses relacions jeràrquiques.
	- Tenen menys registres que les **_taules de fets_**.
	- Exemples: Clients, Productes, Magatzems, Proveïdors, Calendari, etc.

## Tipus d'Atributs

- **_Jeràrquics_**: Permeten passar del general al particular, o consolidar i desagregar. Exemples: país, etc.
- **_Descriptius_**: Informació rellevant que és purament descriptiva. Exemples: adreça, telèfon, mida, etc.
- **_Control_**: Dades d'auditoria que no pertanyen al coneixement del negoci. Exemples: data d'enregistrament de la dada, etc.

## Fets (fact tables)
 
Són una representació dun procés de negoci. Pel que fa a disseny, permeten desar dos tipus d'atributs diferenciats:

- Mesures del procés/activitat/flux de treball/esdeveniment que es pretén modelitzar.
- **_FKs_** cap a registres en una **_taula de dimensió_** (vista de negoci).

### Fets transaccionals (Transaction Fact Table - TFT -)

Permeten analitzar les dades amb el màxim detall.

Per exemple, en una venda que té com a resultat mètriques com a import d'aquesta.

### Fets sense mesures (Factless Fact Tables/Coverage Table - FFT/CT -)

Taules que no tenen mesures però tenen sentit, atès que representen el fet que succeeixi l'esdeveniment. Sovint s'afegeixen comptadors a aquestes taules per facilitar les consultes SQL.

Per exemple, l'assistència a un acte benèfic en què per cada persona que hi assisteix tenim un registre però podríem no tenir cap mètrica associada més.

### Fets periòdics (Periodic Snapshot Fact Table - PSFT -)

Són usades per recollir informació a intervals de temps regulars. Depenent de la situació mesurada o de la necessitat de negoci, aquest tipus de **_taules de fet_** són una agregació de **_FFT/CT_** o estan dissenyades específicament.

Per exemple, el Balanç mensual, en què les dades recullen acumulades de forma mensual.

### Fets agregats (Accumulating Snapshot Fact Tables - ASFT -)

Tepresenten el cicle de vida complet d'una activitat o procés, amb un principi i un final. Es caracteritzen per presentar múltiples dimensions relacionades
amb els esdeveniments presents en un procés.

Per exemple, un procés de matriculació d'un estudiant i que recopila dades durant el període de vida que solen substituir els anteriors (superació i recopilació
d'assignatures, per exemple).

### Fets X (casos de la vida real no descrits a la literatura)

Són **_taules de fets_** que hem de construir en la vida real per afavorir-ne la usabilitat pels usuaris que les alimenten. A més, necessitem transformar-les perquè esdevinguin manejables al nostre **_dm_**.

#### Taules de fets comprimides

Imaginem el següent cas:

Tenim una taula en un full de càlcul Excel al nostre servidor, el qual ha de ser mantingut per un Directiu de Ventes de l'empresa. Després d'1 procés **_ETL_**, integrarem aquesta taula amb les taules de la BD **_OLTP_** dins del nostre **_DWH_**.

En aquesta taula s'anotarà el següent:
- ID venedor (ha de ser el mateix que tenim a la BD **_OLTP_**).
- Nom del venedor (el posem per afavorir la usabilitat de la taula).
- Data d'inici (període).
- Data final (període).
- Incentius dineraris diaris no especificats (incentius en diners pel venedor individual, no contemplats a la BD **_OLTP_**).
- Incentius en espècie diaris no especificats (incentius en espècie pel venedor individual, no contemplats a la BD **_OLTP_**).

Per poder implementar aquesta **_taula de fets_** al nostre **_dm_**, haurem de tenir la mateixa granularitat entre les **_FKs_** de la nostra taula i les dimensions associades. La granularitat al nostre **_dm_** de la **_taula de dimensió_** de Temps és el **dia**, amb la qual cosa, haurem de transformar la nostra **_taula de fets_** perquè els seus registres siguin diaris (no periòdics).

Observem que si transformem els períodes en dies (mantenint les dades de les altres columnes), no perdem informació. A aquest procés l'anomenarem **_descompressió de la taula de fets_**.

La **_taula de fets_** (transformada), al nostre **_DWH_**, quedaria com segueix:
- ID venedor.
- Nom del venedor.
- Data.
- Incentius dineraris diaris no especificats.
- Incentius en espècie diaris no especificats.

#### Taules de fets normalitzades

Imaginem el mateix cas, però amb una altra aproximació: No tenim **_DWH_**.

A la taula, el Directiu anotarà el mateix que al cas anterior (la nostra solució serà transparent per a ell):
- ID venedor (ha de ser el mateix que tenim a la nostra BD **_OLTP_**).
- Nom del venedor (el posem per afavorir la usabilitat de la taula).
- Data d'inici (període).
- Data final (període).
- Incentius dineraris diaris no especificats (incentius en diners pel venedor individual, no contemplats a la BD **_OLTP_**).
- Incentius en espècie diaris no especificats (incentius en espècie pel venedor individual, no contemplats a la BD **_OLTP_**).

Per poder implementar aquesta **_taula de fets_** al nostre **_dm_**, però ara sense tenir en compte el nivell de granularitat de la **_taula de dimensió_** de Temps, degut a que no podem implementar un **_DWH_**, haurem de transformar les files que repeteixin l'ID venedor en grups de repetició (no normalitzats).

Observem que si realitzem aquesta transformació (evitable si tenim un **_DWH_**), haurem de comparar continuament el període amb la data per a saber si s'apliquen els fets per al mateix ID Venedor. És a dir, no es pot relacionar la **_taula de fets_** amb la **_taula de dimensió_** de Temps, però sí amb la dimensió de Venedors.

La **_taula de fets_** (transformada), al nostre **_DWH_**, quedaria com segueix:
- ID venedor.
- Nom del venedor.
- Període 1
- Data d'inici (període).
- Data final (període).
- Incentius dineraris diaris no especificats.
- Incentius en espècie diaris no especificats.
...
- Període n
- Data d'inici (període).
- Data final (període).
- Incentius dineraris diaris no especificats.
- Incentius en espècie diaris no especificats.

**_Molt important!_**: **Com a Analistes/Enginyers de Dades, hem de requerir la implementació d'un DWH (sempre que sigui necessari)**.

## Dimensions (dim tables)

Les dimensions recullen els punts d'anàlisi d'1 fet, és a dir, són les preguntes que fem per conèixer els fets.

Per exemple, una venda es pot analitzar respecte del dia de venda, producte, client, venedor, canal de venda, etc.

Classificació de les **_taules de dimensió_**:

- **_Dimensió de Temps_** (especial)
- **_Classificació històrica_**
	- Dimensions que canvien lentament (**_Slowly Changing Dimensions - SCD -_**)
		- **_Tipus 0_**: Preserva l'original.
		- **_Tipus 1_**: Es sobreescriu l'atribut actualitzat, es a dir, no es desen dades històriques.
		- **_Tipus 2_**: Afegeix un nou registre amb el canvi (fila).
		- **_Tipus 3_**: Afegeix un nou atribut (columna) "anterior". 
		- **_Tipus 4_**: Històric separat (*mini-dimensió*).
		- **_Tipus 5_**: SCD-1 + SCD-4 = SCD-5.
		- **_Tipus 6_**: SCD-1 + SCD-2 + SCD-3 = SCD-6.
		- **_Tipus 7_**: Afegeix una fila de dimensió **_SCD-2_** amb un valor d'atribut nou, a més de la visualització limitada a les files i/o els valors d'atribut actuals.
	- Dimensions que canvien ràpidament (**_Rapidly Changing Dimensions - RCD -_**)
		- Dimensions monstre (**_Monster Dimensions - MONSDIM_** -)
		- Dimensions pont (**_Bridge Dimension - BRIDDIM-_**)
- **_Clasificació funcional_**
	- Dimensions reduïdes (**_Shrunken Dimensions - SHRKDIM -_**)
	- Dimensions conformades (**_Conformed Dimensions - CONFDIM -_**)
	- Dimensions brossa (**_Junk Dimensions - JUNKDIM -_**)
	- Dimensions de joc de rol (**_Role-playing dimensions - RPLYDIM -_**)
	- Dimensions degenerades (**_Degenerate Dimensions - DEGEDIM -_**)
	- Dimensions estabilizadores (**_Outrigger Dimensions - OUTGDIM -_**)
	- Dimensions estàtiques (**_Static Dimensions - STATDIM -_**)
	- Dimensions apilades (**_Stacked Dimensions - STACDIM -_**)
	- Dimensió diferida (**_Deferred Dimension - DEFEDIM -_**)
	- Dimensió distorsionada (**_Distorted Dimension - DISTDIM -_**)

### La dimensió Temps (Calendar DateTime Dimension - CDTD -)

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

### Dimensions reduïdes (Shrunken Dimensions - SHRKDIM -)

Les **_SHRKDIMs_** ens donen quan tenim la necessitat de mostrar l'agregació de les dades que tenim a **_DWH_**. És a dir, necessitem mostrar una granularitat més alta que la que tenim a **_DWH_**.

Un exemple clàssic és quan necessitem mostrar dades mensuals, encara que tenim dades diàries emmagatzemades a **_DWH_**.

Un altre exemple, menys intuïtiu, seria considerar les Vendes per Ciutat. Aleshores crearíem una **_SHRKDIM_** de Ciutats que es relacionaria amb la **_taula de fets_** (Vendes).

És a dir, les **_SHRKDIMs_** creen el que podríem assimilar a taules de cerca (a un model de BD **_OLTP_**). Al **_DDM_**, sempre, hem de pensar en quines preguntes hem de fer a la **_taula de fets_**, poder respondre a les necessitats del negoci (aquesta ha de ser la nostra lògica). Si no les podem respondre amb les **_taules de dimensions_** creades, potser crearem dimensions amb molt poques columnes (**reduïdes**), per respondre-les i aquestes seran les nostres **_SHRKDIMs_**.

**_Nota important_**: Algunes eines de **_BI_**, com **_Power BI_**, tenen la possibilitat de crear segmentacions de **_taules de fets_**, sense extreure **_SHRKDIMs_** de la mateixa. Això ens pot donar com a resultat un **_DDM_** incomplet al **_DWH_**, sobretot si decidim explotar les dades en un altre entorn, com **_Python_** (fora de **_Power BI_**).

#### Exemple d'Ús de Dimensions Reduïdes (SHRKDIM)

Suposem que tenim un **_DWH_** que emmagatzema dades de Vendes per a una cadena de botigues. En aquest **_DWH_**, tenim una **_taula de fets_** principal anomenada "Vendes" que emmagatzema informació detallada sobre cada venda individual, com la data de la venda, el producte venut, la quantitat, el preu, etc.

No obstant això, també tenim la necessitat de realitzar anàlisis a nivell mensual i a nivell de ciutat. Això significa que necessitem mostrar dades agregades de Vendes per Mes i per Ciutat, tot i que les dades detallades s'emmagatzemen a nivell diari a la **_taula de fets_** "Vendes".

En aquest escenari, podríem crear dues dimensions reduïdes (**_SHRKDIM_**):

1. **Dimensió de Mes (SHRKDIM_Mes)**: Aquesta dimensió contindria informació sobre els mesos, com el nom del mes, el número del mes i qualsevol altra informació rellevant. Seria una taula reduïda en comparació amb la **_taula de fets_** de "Vendes" i es relacionaria amb ella a través de la data de la venda. Això ens permetria realitzar anàlisis i consultes a nivell mensual.

2. **Dimensió de Ciutat (SHRKDIM_Ciutat)**: Aquesta dimensió contindria informació sobre les ciutats on operen les botigues, com el nom de la ciutat, el codi postal i altres detalls. Igual que la dimensió de Mes, seria una taula reduïda en comparació amb la **_taula de fets_** de "Vendes" i es relacionaria amb ella a través de la ubicació de la botiga. Això ens permetria realitzar anàlisis i consultes a nivell de ciutat.

Amb aquestes dimensions reduïdes en lloc, podríem respondre preguntes com:

- **"Quina va ser la quantitat total de vendes al mes de juliol de 2023 a totes les ciutats?"**
- **"Quin va ser el promig de vendes diàries a la ciutat de Barcelona a l'últim trimestre?"**

Aquestes preguntes impliquen agregacions a nivell de Mes i Ciutat, i les dimensions reduïdes ens permeten realitzar aquestes consultes de manera eficient i coherent, fins i tot si les dades detallades s'emmagatzemen a nivell diari a la **_taula de fets_** "Vendes".

<p><br></p>

### Dimensions conformades (Conformed Dimensions - CONFDIM -)

Permeten compartir informació entre dimensions, amb la qual cosa, poder realitzar consultes conjuntes. 

A un **_DWH_**, una **_CONFDIM_** és una dimensió que té el mateix significat a cada fet amb què es relaciona. Les **_CONFDIMs_** permeten categoritzar i descriure fets i mesures de la mateixa manera a través de múltiples **_taules de fets_** o Data Marts (**_DM_**), garantint la coherència d'informes a tota l'empresa.

Es pot fer referència a una **_CONFDIM_** mitjançant múltiples **_taules de fets_** a un DWH. La dimensió s'implementa com una taula de dimensió única a la qual  fan referència **_diverses taules de fets_** o com a múltiples **_taules de dimensions_** a les quals fan referència **_taules de fets_** en **_DMs_** diferents dins del mateix **_DWH_**. També és possible que diverses **_taules de fets_** dins d'un sol **_DM_** facin referència a la mateixa taula de **_CONFDIM_** així com una **_taula de fets_** en altres **_DM_**.

L'exemple més clàssic de **_CONFDIM_** és la taula de Temps, ja que podem preguntar per les Vendes mensuals, les Planificacions de productivitat dels empleats al més que vé o les Entrades al magatzem el dia 15 del mes passat. En tots els casos, inclús si es tracta d'1 altre **_DM_** (departamental), es tracta de preguntes que fem des de la nostra taula de Temps (**_taula de dimensió_**) a diferents **_taules de fets_** (a un o més **_DM_**, cas que els tinguem contemplats al nostre **_DWH_**).

**_Atenció!_**: Si no hem fet les preguntes de negoci adeqüades, no contemplarem dimensions, que si són prou generalitzades al nostre **_DWH_**, seran **_CONFDIMs_** no contemplades.

#### Exemple d'ús de Dimensions Conformades (CONFDIM) amb Data Marts

Imaginem que tenim un gran Data Warehouse (**_DWH_**) que emmagatzema dades per a una empresa minorista. Dins d'aquest **_DWH_**, hi ha diversos Data Marts (**_DMs_**) per a diferents departaments, com ara Vendes, Màrqueting i Gestió d'Estocs. Cada **_DM_** es centra en aspectes comercials específics i té les seves pròpies **_taules de fets_**.

Considerem el **_DM_** de Vendes, que conté informació detallada de les vendes. Una de les **_taules de fets_** principals en aquest **_DM_** és "Transaccions de Vendes", que registra les dades de cada venda individual, incloent el producte venut, la informació del client, la data i l'import.

Ara bé, el **_DM_** de Màrqueting està interessat a analitzar les dades de les vendes per comprendre el comportament i les tendències dels clients. Volen saber, per exemple, quins productes solen ser comprats junts pels clients. Per fer-ho, necessiten compartir dades de dimensions, com ara Producte i Client, amb el **_DM_** de Vendes.

Així és com s'utilitzen les **_Dimensions Conformades (CONFDIM)_** en aquest escenari:

1. **Dimensió de Producte (CONFDIM_Producte)**: Aquesta dimensió conté informació sobre els productes, com ara el nom del producte, la categoria i el fabricant. Es manté de manera consistent tant en el **_DM_** de Vendes com en el **_DM_** de Màrqueting. El **_DM_** de Vendes relaciona la **_taula de fets_** "Transaccions de Vendes" amb aquesta dimensió, mentre que el **_DM_** de Màrqueting utilitza la mateixa dimensió per analitzar dades relacionades amb els productes.

2. **Dimensió de Client (CONFDIM_Client)**: Aquesta dimensió inclou informació sobre el client, com ara el nom, l'adreça i els detalls de contacte. Igual que la Dimensió de Producte, es comparteix entre els **_DMs_** de Vendes i Màrqueting. El **_DM_** de Vendes vincula la **_taula de fets_** "Transaccions de Vendes" amb aquesta dimensió, mentre que el **_DM_** de Màrqueting l'utilitza per comprendre les preferències i el comportament dels clients.

Amb les **_Dimensions Conformades (CONFDIM)_** en el seu lloc, el **_DM_** de Màrqueting pot realitzar anàlisis com identificar l'afinitat entre productes i segmentar clients de manera efectiva, ja que poden confiar en dades de dimensions consistents compartides amb el **_DM_** de Vendes.

Consulta d'exemple en el **_DM_** de Màrqueting:

- **"Quins productes es compren sovint junts i quins segments de clients mostren aquest comportament?"**

Aprofitant les **_Dimensions Conformades (CONFDIM)_**, aquests **_DMs_** asseguren la consistència i la integritat de les dades en compartir informació de dimensions, permetent a diferents departaments dins de l'organització realitzar anàlisis interfuncionals i obtenir coneixements valuosos.

<p><br></p>

### Dimensions brossa (Junk Dimensions - JUNKDIM -)

Contenen informació volàtil que s'utilitza puntualment i que no s'acostuma a guarda de manera permanent al **_DWH_**.

- Redueix el nombre de dimensions (columnes de cardinalitat baixa) al model dimensional i redueix el nombre de columnes a la **_taula de fets_**. És una col·lecció de codis transnacionals aleatoris, flags o atributs de text.

- Pot optimitza l'espai, ja que les **_taules de fets_** *no haurien d'incloure camps de text ni de cardinalitat baixa* (**pràctica molt qüestionable**). Inclou principalment mesures, claus externes i claus de dimensió degenerades (**_DEGEDIMs_**).

<p><br></p>

Exemple amb una **_taula de fets_** de Cotxes i dos **_taules de dimensions_** de Colors i de Model.

![Sense JUNKDIM](https://i.imgur.com/hYcP0rU.png)  
_Sense JUNKDIM_

<p><br></p>

Exemple amb una **_taula de fets_** de Cotxes i una **_taula de dimensió_** de Característiques del cotxe.

![Amb JUNKDIM](https://i.imgur.com/dw1nykb.png)  
_Amb JUNKDIM_

<p><br></p>

**_Nota_**: És com a mínim una pràctica qüestionable, atés que ens interesa des normalitzar les dimensions i dotar de significat les mètriques de les **_taules de fets_**. Al cas esposat, la pràctica més recomenada seria considerar els Cotxes com a **_taula de dimensió_** d'1 **_taula de fets_** de Vendes i llestos.

#### Exemple d'ús de dimensions brossa (Junk Dimensions - JUNKDIM)

Suposem que tenim un Data Warehouse (**_DWH_**) que emmagatzema dades de Vendes de productes. En aquest **_DWH_**, tenim una **_taula de fets_** principal anomenada "Vendes" que emmagatzema informació detallada sobre cada transacció, com ara data, producte venut, client, quantitat, preu, entre d'altres.

Ara, necessitem realitzar anàlisis específics que involucren la combinació de certs atributs de baixa cardinalitat, com ara els colors dels productes i les característiques del producte. Aquests atributs no justifiquen una dimensió independent a causa de la seva baixa cardinalitat i naturalesa volàtil.

En lloc de crear dimensions separades pels colors i les característiques del producte, podríem optar per una **_dimensió brossa (JUNKDIM)_** anomenada "Atributs del Producte". Aquesta dimensió contindria una col·lecció de codis aleatoris o indicadors que representen combinacions de colors i característiques.

Així és com es veuria l'estructura de dades:

- **_Taula de fets_** "Vendes": Es relaciona amb la **_JUNKDIM_** "Atributs del Producte" mitjançant una **_FK_**.

- **_JUNKDIM_** "Atributs del Producte": Conté combinacions de colors i característiques representades per codis o indicadors.

Amb aquesta estructura, podríem respondre preguntes com ara:

- **"Quina és la quantitat total de Productes venuts que tenen les característiques 'Vermell' i 'Resistent a l'aigua'?"**
- **"Quin és el preu mitjà dels Productes de color 'Blau'?"**

Les **_JUNKDIMs_** ens permeten simplificar el **_dm_** i optimitzar l'espai, ja que no hauríem de crear dimensions separades per a cada combinació possible de colors i característiques del producte.

<p><br></p>

### Dimensions de joc de rol (Role-playing dimensions - RPLYDIM -)

Les **_RPLYDIMs_** tenen assignat un significat. És a dir, una **_RPLYDIM_** és una dimensió que fa referència a múltiples propòsits dins d'1 **_taula de fets_**.

El millor exemple per una **_RPLYDIM_** és novament una **_taula de dimensió_** de Temps, ja que el mateix atribut de data en la dimensió es pot fer servir per a diferents propòsits a la mateixa **_taula de fets_**. Per exemple a la **_taula de fets_** de Vendes poden estar enregistrades la data de comanda, de lliurament, de transacció, de cancel·lació, de lliurament, etc. Totes aquestes dades es relacionen amb la data de la **_taula de dimensió_** de Temps.

Dificultat de tractament:

1. Poden augmentar la complexitat i la mida de la **_taula de fets_**, ja que cada **_RPLYDIM_** afegeix més columnes a la **_taula de fets_**.  

2. Poden crear confusió i inconsistència entre usuaris i analistes, ja que diferents rols poden tenir diferents significats o interpretacions per un mateix atribut de dimensió. Es a dir, si sols diguem data (p.e.), pot tenir moltes interpretacions, depenet del rol.  

3. Poden ser difícils de mantenir i actualitzar, ja que qualsevol canvi a la **_taula de dimensions_** font pot afectar a múltiples **_RPLYDIMs_** i **_taules de fets_**. És imprescindible, doncs tenir-les molt clares a tot el **_pipeline_**.  

4. Algunes eines de **_BI_**, com ara **_Power BI_**, **no més permet tenir activa** una relació amb la mateixa **_PK_** de la RPLYDIM, malgrat que siguin **_FKs_** diferents a la **_taula de fets_**. Això no més es un tema d'implementacio al **_BI_** i es resol correctament a l'eina (a **_Power BI_**, amb **_DAX_**).  

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

Altres solucions, ens poden complicar la vida com muntar diferents vistes (al nostre **_DWH_**) de la **_taula de dimensió_** Temps per cadascuna de les **_FKs_** de la **_taula de fets_**, es a dir, per cadascuna de les **_RPLYDIMs_** definides. Això ens complica extraordinàriament les relacions i les segmentacions en l'explotació del **_DDM_** al **_BI_** (p.e.).

**_Atenció!_**: És imprescindible definir bé les **_RPLYDIMs_** per que funcioni bé el nostre **_pipeline_**. Això implica que, com a **Enginyers de Dades hem de conèixer**, al **_pipeline_** del nostre projecte, **des del negoci del client fins la solució final** en que mostrarem el resultat.

#### Exemple d'ús de dimensions de joc de rol (Role-playing dimensions - RPLYDIM -)

Suposem que tenim un Data Warehouse (**_DWH_**) que emmagatzema dades de Vendes de productes. En aquest **_DWH_**, tenim una **_taula de fets_** principal anomenada "Vendes" que emmagatzema informació detallada sobre cada transacció, com la data de comanda, la data d'enviament, la data de lliurament, el producte venut, el client, la quantitat, el preu, entre altres.

A més, volem implementar la solució a **_Power BI_**.

Ara, necessitem realitzar anàlisis específics que involucren els atributs de temps. Cadascun d'aquests atributs té una relació amb la dimensió de Temps.

En lloc de crear dimensions separades per a les dates, podríem optar per una **_dimensió de joc de rol (RPLYDIM)_** i a més no necessitaríem una taula diferent de la **_taula de dimensió_** de Temps. 

Dificultats en el tractament:

1. A **_Power BI_** no podem mantenir més d'una relació amb el mateix camp, amb la qual cosa necessitem implementar la relació selectivament en DAX, segons els interessos.

2. En altres sistemes, la implementació pot implicar crear múltiples **_taules de dimensió_** de Temps, per a cada atribut de la **_taula de fets_** amb la qual vulguem establir la relació.

En el nostre cas, depenent de la mètrica que vulguem obtenir, utilitzaríem codis com els següents:

````DAX
[Vendes totals per data de comanda] :=   
    CALCULATE( 
        SUM(Comandes[Total de línia]),
        USERELATIONSHIP(Comandes[Data de comanda], Dates[Data])
    )

[Vendes totals per data d'enviament] := 
    CALCULATE( 
        SUM(Comandes[Total de línia]),
        USERELATIONSHIP(Comandes[Data d'enviament], Dates[Data])
    )

[Vendes totals per data de lliurament] := 
    CALCULATE( 
        SUM(Comandes[Total de línia]),
        USERELATIONSHIP(Comandes[Data de lliurament], Dates[Data])
    )
````

Així és com es veuria l'estructura de dades:

- **_Taula de fets_** "Vendes": Es relaciona amb la **_RPLYDIM_** "Dates" a través de les 3 **_FKs_**.

- **_RPLYDIM_** "Atributs de Temps": En el nostre cas és la **_taula de dimensió_** de Temps.

Amb aquesta estructura, podríem respondre preguntes com:

- **"Quina és la quantitat total de Productes venuts que es van enviar al mes de març?"**
- **"En quines ciutats es van fer més lliuraments a l'agost?"**

Les **_JUNKDIMs_** ens permeten simplificar el **_dm_** i optimitzar l'espai, ja que no hauríem de crear dimensions separades per a cada atribut relacionat (**_FK_**).

### Dimensions degenerades (Degenerate Dimensions - DEGEDIM -)

Es troben com a atributs a la **_taula de fets_**, tenen significat des del punt de vista de l'anàlisi. Contenen informació de baixa cardinalitat formada per relacions dicotòmiques. Sovint, només contenen un atribut i, per tant, no sol crear-se una taula separada.

Dit d'una altra manera, són atributs que no poden ser dimensions, ni fets (mètriques), però requereixen anàlisi. Tots aquests atributs, si s'eliminen de la taula de fets, es traslladen a **_DEGEDIMs_**.

Per exemple, es poden considerar el número de comanda, el número de factura, el gènere d'un pacient, etc., com a atributs de **_DEGEDIMs_**.

Una factura pot tenir un atribut de nom de client, però ja forma part de la **taula de dimensió** Client.

Per tant, **_DEGEDIM_** és una part de la **taula de fets** que no és una mètrica, però que continua sent una dimensió, la qual cosa és contradictòria.

Es pot definir com a **_DEGEDIM_** a un atribut d'una **_taula de fets_** que actua com a dimensió, però que realment no s'uneix amb cap altra dimensió (no és una **_FK_** d'una altra taula), ja que els seus atributs ja han estat inclosos en altres dimensions d'anàlisi.

**Avís!**: Definir **_DEGEDIMs_** fora d'una **taula de fets** és un error.

#### Exemple de dimensions degenerades (Degenerate Dimensions - DEGEDIM -)

Suposem que tenim una BD de Vendes que inclou una **taula de fets**, que registra les Vendes de productes. En aquesta **_taula de fets_**, tenim la següent informació:

- Número de comanda
- Data
- Producte
- Quantitat
- Preu
- Venda en línia

Ara, imaginem que les Vendes poden ser en línia o no, per tant, aquest atribut té baixa cardinalitat, no és agregable i tampoc és una **_FK_**. D'altra banda, el número de comanda de la Venda té baixa cardinalitat en relació a les Vendes (una venda té més d'una comanda), a més no és agregable, ni és una **_FK_**.

Per tant, a la **_taula de fets_** (Vendes), el número de comanda i la venda en línia són **_DEGEDIMs_** i només ens serviran per tenir clar que no ens hem equivocat, però no construirem una dimensió a part amb ells.

#### Dimensions estabilizadores (Outrigger Dimensions - OUTGDIM -)

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

#### Dimensions estàtiques (Static Dimensions - STATDIM -)

Venen del no-res, són dimensions que no existeixen la BD **_OLTP_** d'origen, com a entitats.  

Les dimensions estàtiques es generen mitjançant un script **_SQL_**, un procediment emmagatzemat o un fitxer extern i es generen manualment.

Com que no s'extreuen de la font de dades **_OLTP_**, aleshores es pot considerar que es creen en el context del **_DWH_**. 

Una pràctica molt saludable és no crear-les al **_DWH_**, sinó a un full de càlcul al servidor corporatiu.

Una dimensió que no existeix a la **_OLTP_**, no necessàriament han de ser una **_STATDIM_**. Però si col·loquem les dimensions estàtiques i dinàmiques, que no existeixen a la **_OLTP_**, a un llibre (Excel, per exemple) al servidor i les posem a disposició de l'usuari, aleshores aquest serà conscient de les entitats estàtiques. Si creem les **_STATDIM_** únicament al **_DWH_**, aleshores això pot, molt possiblement, generar desconfiança en l'usuari, perquè no les veu.

**_Avís!_**: No hem de confiar en els usuaris (Directors o Gerents), si manquen entitats a la BD **_OLTP_** (que es transformaran en dimensions al **_DDM_**), hem d'afegir-les, abans del **_DWH_** i s'han de relacionar al **_DWH_**.

#### Dimensions que canvien lentament (Slowly Changing Dimensions - SCD -)

Una **_SCD_** a un **_DWH_** és una dimensió que conté dades relativament estàtiques que poden canviar lentament i de manera imprevisible, en lloc de seguir un horari regular. Alguns exemples de dimensions típiques que canvien lentament són entitats com Localitzacions Geogràfiques, Clients o Noms de Productes. ([Wikipedia](https://en.wikipedia.org/wiki/Slowly_changing_dimension)).  

<p><br></p>

![001-Dimensió](https://i.imgur.com/kXDzc6e.png)  
_Dimensió (imatge 001)_  

<p><br></p>

##### Tipus de SDC

A una BD dimensional, els **_SCDs_** són aquelles dimensions que canvien amb el temps, però no necessàriament de manera constant o previsible. **Per exemple, a una taula de dades de Clients, l'adreça del client pot canviar amb el temps, però no tots els clients canvien l'adreça a la mateixa velocitat. Alguns clients poden canviar l'adreça cada mes, mentre que altres poden mantenir la mateixa adreça durant anys**.

La gestió adequada de les **_SCD_** és important per mantenir l'exactitud i la integritat de les dades dimensionals en una base de dades, ja que permet als usuaris realitzar anàlisis històriques i comparar dades al llarg del temps.

##### Elecció del tipus de SCD

La elecció del tipus de **_SCD_** a utilitzar depèn de les necessitats específiques del **_DWH_** i les necessitats d'anàlisi. És important tenir en compte factors com la importància de les dades històriques, la freqüència dels canvis a les dimensions i les implicacions d'emmagatzematge i rendiment de cada enfocament.

Les **_SCDs_** són un aspecte crucial dels **_DWHs_** ja que permeten representar les dades al llarg del temps, facilitant així un anàlisi i informes històrics precisos.

##### SDC-0

**_SCD-0_** no té en compte la gestió dels canvis històrics. S'aplica quan no es canvia mai la informació, es dir, els atributs al **_SCD-0_** mai canvien i s'assignen a atributs que tenen **valors** duradors o que es descriuen com a "**originals**".  

Exemples: Data de naixement, puntuació de crèdit original.  

S'aplica a la majoria dels atributs de les dimensions. 

El que vol dir, tot plegat, és que, **com que a la taula original no hi ha canvis, aleshores tampoc n'hi ha en la dimensió**.

##### SCD-1

**_SCD-1_** no guarda històrics. La nova informació sobreescriu l'antiga sempre. Principalment la sobreescriptura es fa per errors de qualitat de dades. Aquest tipus de dimensions és fàcil de mantenir i es fa servir quan la informació històrica no és important. És a dir, **és apropiat quan les dades històriques no són importants o quan es poden recuperar d'altres fonts**.  

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

El que vol dir, tot plegat, és que, malgrat que hi hagi canvis, **cada vegada s'importen tots els registres de la taula transaccional (esborrant els que existien abans a la taula dimensional)**. Es a dir, els registres a la taula dimensional són els "**originals**" a la taula transaccional, sense canvis.

##### SCD-2

**_SCD-2_** guarda la informació històrica al **_DWH_**.

Quan hi ha un canvi es crea una nova entrada amb la seva data i **_SK_** apropiades.

La **_SCD-2_** **es sol utilitzar quan es requereix un anàlisi històric i la dimensió canvia relativament poc sovint**. Quan es produeixi algun canvi en els valors dels registres, **s'afegirà una nova fila** i s'hauran de completar les dades referides a l'historial de canvis.

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

##### SCD-3

**_SCD-3_** guarda la informació històrica al **_DWH_**.

La **_SCD-3_** s'utilitza **quan és important seguir els canvis d'atributs específics mentre es manté la simplicitat en el model de dades**.**Requereix que s'afegeixi** a la taula de dimensió **una columna addicional** per cada columna els valors dels quals es vol mantenir un historial de canvis.

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

##### SCD-4 (història separada)

**_SCD-4_** és coneix habitualment com a taules històriques. 

La **_SCD-4_** fa servir "*taules históriques*", a on **una taula conserva les dades actuals i addicionalment s'utilitza una taula històrica** per mantenir un registre d'alguns o tots els canvis. Les dues **_SK_** (**_taula de dimensió_** i **_taula històrica_**) fan referència a la **_taula de fets_** per millorar el rendiment de la consulta.

Per a l'exemple següent, el nom de la taula original (transaccional) és Proveïdor i la **_taula històrica_** és Proveïdor_Històric:

| **SK** |**Key** | **Name**                 | **State** |
| :----: | :----- | :----------------------- | :-------: |
| 124    | ABC    | Acme & Johnson Supply Co | IL        |


| **SK** |**Key** | **Name**                 | **State** | **Registration date** |
| :----: | :----- | :----------------------- | :-------: | :-------------------: |
| 123    | ABC    | Acme Supply Co           | AC        | 2003-06-14T00:00:00   |
| 124    | ABC    | Acme & Johnson Supply Co | IL        | 2004-12-22T00:00:00   |

Aquest mètode s'assembla a com funcionen les *taules d'auditoria* de BD. Aquesta és una manera fantàstica de fer un seguiment dels registres que tenen molts canvis al llarg del temps.

A la taula històrica se li acostuma a dir *mini-dimensió*.

La **_SCD-4_**s'utilitza quan la **_SCD-2_** creix ràpidament, a causa de que atributs de la dimensió canvien sovint. A la **_SCD-4_**, els atributs que canvien sovint s'eliminaran de la dimensió principal i s'afegiran a la *mini-dimensió*.

Considerem un altre exemple, per explicar l'anterior, amb una **_taula de dimensió_** de Clients amb l'estructura següent:
- Key (PK)
- Data inici
- Data final
- Name
- Data neixement
- Estat
- Franja edat
- Franja ingressos
- Franja compres

Els atributs del client, com ara el nom, la data de naixement, l'estat del client, canvien molt poques vegades o ni tan sols canvien, ara bé s'espera que la franja d'edat, la franja d'ingressos i la franja de compra canviïn amb molta freqüència.

Si una organització amb 100 milions de clients utilitza aquesta dimensió de Client, es pot esperar que aquesta dimensió creixi fins a 200 o 300 milions de registres en un any, suposant que hi haurà almenys dos o tres canvis per a un client per any.

Aleshores, podem dividir la dimensió en dues dimensions, una amb els atributs que canvien amb menys freqüència i un altre amb els atributs que canvien amb més freqüència. Els atributs que canvien amb freqüència s'agruparan a la *mini-dimensió*. 

Dimensió de Clients
- Key (PK)
- Data inici
- Data final
- Name
- Data neixement
- Estat

Mini-dimensió
- Key (PK)
- Franja edat
- Franja ingressos
- Franja compres

La *mini-dimensió* contindrà una fila per a cada possible combinació d'atributs. Al nostre cas, totes les combinacions possibles de franja d'edat, franja d'ingressos i franja de compres estaran disponibles a la *mini-dimensió* amb la mateixa **_PK_** que a la **_taula de dimensió_**.

Si tenim 20 franges d'edat diferents, 4 franges d'ingressos diferents i 3 franges de compres, tindrem 20 X 4 X 3 = 240 combinacions possibles diferents. 

Aquests valors es poden emplenar a la taula de **_mini-dimensió_** una vegada per sempre amb una **_SK_** que oscil·la entre 1 i 240.

**_Nota important_**: A la *mini-dimensió* no s'emmagatzemen els atributs històrics, to i que la **_taula de fets_** es conserva l'historial d'assignació d'atributs de la dimensió.

Com que les dues **_taules de dimensió_** es relacionen amb una **_taula de fets_** (Vendes), aleshores, aquesta tindrà la PK (natural) de la Dimensió de Clients y la PK de la **_mini-dimensió_** (SK).

Fets de Vendes
- PK_Clients
- SK_Minidimensio
- Data
- Key Producte
etc.

Un repte que s'ens presente és quan la *mini-dimensió* comença a canviar ràpidament. Aleshores es poden introduir múltiples *mini-dimensions* per gestionar aquests escenaris. Si cap registre de fets ha d'associar la dimensió principal i la *mini-dimensió*, es pot utilitzar una **_taula de fets_** *sense fets* per associar la dimensió principal i la *mini-dimensió*.

##### SCD-5

**_SCD-5_** es basa en la *mini-dimensió* **_SCD-4_** incrustant una clau de *mini-dimensió* del "*perfil actual*" a la dimensió base que es sobreescriu com a atribut **_SCD-1_**. Aquest enfocament, anomenat **_SCD-5_** perquè **4 + 1 = 5**, permet accedir als valors dels atributs de *mini-dimensió* assignats actualment conjuntament amb els altres de la dimensió base sense enllaçar-los mitjançant una **_taula de fets_**. Normalment representem la dimensió base i l'estabilizador del perfil de *mini-dimensió* actual com una taula única a la capa de presentació. Els atributs dels estabilizadors haurien de tenir noms de columnes diferents, com ara "Nivell d'ingressos actual", per diferenciar-los dels atributs de la *mini-dimensió* vinculada a la **_taula de fets_**. L'equip d'ETL ha d'actualitzar/sobreescriure la referència de *mini-dimensió* **_SCD-1_**, sempre que la *mini-dimensió* actual canviï amb el temps.

Exemple:

Vendes (taula de fets)
- Data (FK)
- Client (FK)
- Perfil (FK)
...

Clients (dimensió)
- Client (PK)
- Client ID (NK)
- Nom
...
- Perfil actual (FK)

Perfil (*mini-dimensió*)
- Perfil (PK)
- Franja edat
- Puntuació de la freqüència de compres 
- Nivell ingressos

Vista de la *mini-dimensió* com estabilitzador
- Perfil actual (PK)
- Franja edat actual
- Puntuació de la freqüència de compres actual
- Nivell ingressos actual

##### SCD-6 (híbrida)

**_SCD-6_** combina els enfocaments dels tipus 1, 2 i 3 (**1+2+3=6**). Consisteix a considerar una **_SCD-1_** i afegir un parell de columnes addicionals que indiquen el rang temporal de validesa duna de les columnes de la taula. Si bé el disseny és complex, entre els seus beneficis podem destacar que **redueix la mida de les consultes temporals**. Hi ha una altra variant per a aquest tipus de dimensió, que consisteix a tenir versions del registre de la dimensió (numerats de 0 a n+1, on 0 sempre és la versió actual).

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

##### SCD-7 (Híbrid: clau subrogada i natural)

Una implementació alternativa és col·locar tant la clau substituta com la clau natural a la **_taula de fets_**.   

Aquest mètode **permet enllaços més flexibles a la dimensió**, fins i tot si s'ha utilitzat **_SCD-2_** en lloc de **_SCD-6_**.

Amb la **_SCD-7_**, la **_taula de fets_** conté **_FKs_** dobles per a una dimensió determinada: una **_SK_** enllaçada a la taula de dimensions on es fan el seguiment dels atributs del tipus 2, més la clau natural duradora de la dimensió enllaçada a la fila actual de la dimensió tipus 2 per presentar el valor l'actual de l'atribut.

La **_SCD-7_** ofereix la mateixa funcionalitat que la **_SCD-6_**, però s'aconsegueix mitjançant claus duals en lloc de sobreescriure físicament els atributs actuals com al**_ SCD-6_**. Igual que els altres enfocaments híbrids, els atributs de la dimensió actual s'han d'etiquetar de manera distinta per minimitzar la confusió.

Imaginem el següent cas:

Vendes (**_taula de fets_**)
- Data (**_PK_**)
- Clau producte (**_FK_**)
- Clau duradora de producte (**_DK_**)
- més **_FKs_**
- fets

Productes (**_taula de dimensió_**)
- Clau producte (**_PK_**)
- **_SK_**
- **_DK_**
- Descripció
- Data inicial
- Data final
- ...

Productes actuals (**_taula de dimensió actual_**)
- **_DK_**
- Descripció
- ...

##### Resum SCD's

| **_SCD_**   | Taula de dimensió                           | Taula de fets                                                                                                         |
| :---------: | :------------------------------------------ | :-------------------------------------------------------------------------------------------------------------------- |
| **_SCD-0_** | Sense canvis al valor de l'atribut          | Els fets associats al valor original de l'atribut                                                                     |
| **_SCD-1_** | Sobreescriu el valor de l'atribut           | Els fets associats amb el valor actual de l'atribut                                                                   |
| **_SCD-2_** | Afegeix una nova fila amb el nou valor de l'atribut | Els fets associats amb el valor de l'atribut quan el fet occurreix                                            |
| **_SCD-3_** | Afegeix una nova columna per preservar els valors de l'atribut anterior i actual | Fets associats amb el valor alternatiu de l'atribut (anterior i actual)          |
| **_SCD-4_** | Afegeix una *mini-dimensió* que conté atributs que canvien ràpidament | Els fets associats a atributs que canvien ràpidament tenen efecte quan es produeix un fet |
|**_ SCD-5_** | **_SCD-4_** + **_SCD-1_** = Afegeix la mini-dimensió (**_SCD-4_**), conjuntament amb la clau de **_SCD-1_** sobreescrita a la dimensió base | Els fets associats amb atributs que canvien ràpidament tenen efecte quan es va produir el fet, a més dels valors dels atributs actuals, que canvien ràpidament |
| **_SCD-6_** | **_SCD-1_** + **_SCD-2_** +**_SCD-3_** = Afegeix atributs sobreescrits **_SCD-1_** a la fila de dimensions **_SCD-2_** i sobreescriu totes les files de dimensions anteriors | Els fets associats al valor de l'atribut quan es va produir el fet, més els valors actuals |
| **_SCD-7_** | Afegeix una fila de dimensió **_SCD-2_** amb un valor d'atribut nou, a més de la visualització limitada a les files i/o els valors d'atribut actuals | Els fets associats amb el valor de l'atribut quan es va produir el fet, més els valors actuals |

#### Dimensions que canvien ràpidament (Rapidly Changing Dimensions - RCD -)

Són dimensions que canvien (o poden canviar) ràpidament amb el temps. Les **_RCD_** s'implementen generalment com a **_JUNKDIM_**.

La manipuplació de les **_RCD_** al **_DWH_** és molt difícil a causa del rendiment. Com hem vist, les **_SCD_** s'utilitzen per mantenir l'historial dels canvis. Però el problema amb **_SCD-2_** és que amb tots i cadascun dels canvis en l'atribut de dimensió, afegeix una nova fila a la taula. Si hi ha dimensions que canvien molt, la taula es fa més gran i pot causar greus problemes de rendiment. Per tant, l'ús de **_SCD-2_** pot no ser una bona decisió per implementar les dimensions que canvien ràpidament.

Exemple:

Considerem que a la dimensió Client tenim 1000 files. De mitjana, cada client canvia 10 d'atributs al any. Si utilitzem **_SCD-2_** per gestionar aquest escenari, hi haurà 1000*10 = 10000 files al final del any. Si la taula té milions de files, serà molt difícil gestionar la situació amb **_SCD-2_**. Per això farem servir un enfocament **_RCD_**.

Per implementar-ho farem servir un atribut de canvi ràpid separat, mitjançant la implementació d'1 **_JUNKDIM_**.

A la taula de fets no tots els atributs canvien ràpidament. Pot haver-hi algun atribut que pot canviar ràpidament i un altre no. La idea aquí és separar l'atribut que canvia ràpidament dels que canvien lentament i moure aquests atributs a una altra taula **_JUNKDIM_** i mantenir l'atribut que canvia lentament a la mateixa taula. D'aquesta manera, podem gestionar situacions d'augment de la mida de la taula.

Client (**_taula de dimensió_**)
- ID
- Nom
- Ciutat
- Estat
- Gènere
- Ingressos
- Valoració
- Puntuació crèdit

Atributs com ID, Nom, Ciutat, Estat o Gènere no canviaran o canvien molt poques vegades. En canvi, atributs com Ingressos, Valoració i Puntuació_crèdit canvien cada mes en funció de l'entorn del client. Per tant, hem de separar aquestes columnes de la taula de clients, en cas contrari, acabem omplint la taula si utilitzem **_SCD-2_** a la dimensió Client. Podem posar aquestes columnes que estan canviant ràpidament a la **_taula de dimensions JUNKDIM_**. 

Client Junk (**_JUNKDIM_**)
- SK
- Ingressos
- Valoració
- Puntuació crèdit

La dimensió Client resta com:

Client (**_taula de dimensió_**)
- ID
- Nom
- Ciutat
- Estat
- Gènere

Ara bé, hem d'enllaçar la **_JUNKDIM_** (Client_Junk) i la **_taula de dimensió_** (Client). A més, no podem fer referència simplement a la **_JUNKDIM_** afegint la seva clau primària (**_SK_**) a Client com a **_FK_**. Com que qualsevol canvi fet a la **_JUNKDIM_** s'haurà de reflectir a la **_taula de dimensió_**, això òbviament augmenta les dades de Client. En lloc d'això, crearem una taula més de *mini-dimensió* que actua com a pont entre la **_taula de dimensió_** i **_JUNKDIM_**. També podem afegir columnes com ara la data d'inici i de finalització per fer un seguiment de l'historial de canvis.

Client Mini Dim (**_Bridge Dimension - BRIDDIM-_**)
- ID
- SK
- Data inici
- Data final

Les **_BRIDDIMs_** permeten definir relacions m:n entre **_taules de fets_**. Són necessàries per definir, per exemple, la relació entre un pilot i els seus múltiples patrocinadors (m:n).

Aquesta taula és només un pont entre dues taules i no requereix cap **_SK_**.

Un exemple de RCD, quan els creixements són explosius, són les Dimensions monstre (Monster Dimensions - **_MONSDIM_** -)

#### Dimensions apilades (Stacked Dimensions - STACDIM -)

**_STACDIM_** s'utilitza quan dues o més dimensions es combinen en una dimensió. Té un o dos atributs i sempre és **_SCD-0_**.

Exemples (tipus i estat): Tipus de producte, Estat del client, Tipus de botiga, Tipus de seguretat, etc. Totes aquestes columnes haurien d'emmagatzemar-se a les seves dimensions respectives perquè són les propietats de la dimensió.

Tot i això, hi ha columnes de tipus i estat que són propietat de la **_taula de fets_**, com ara: Tipus de transacció o Estat de transacció. **Per combinar el tipus de transacció i l'estat de la transacció en una dimensió, creem una dimensió brossa. No utilitzarem mai una STACDIM**.

**No es recomana fer servir una STACDIM. Està malament fer-les servir, però existeixen**. Normalment perquè era així al sistema d'origen, de manera que simplement ho copiem al **_DWH_** (sense reflexionar).

#### Dimensió diferida (Deferred Dimension - DEFEDIM -)

Quan es carrega un registre d'1 **_taula de fets_**, és possible que un registre d'1 **_taula de dimensió_** encara no estigui preparat. Tècnicament s'anomena **_membre inferior_** o **_dimensió sensible_**.

#### Dimensió distorsionada (Distorted Dimension - DISTDIM -)

Una dimensió que s'utilitza en molts llocs s'anomena **_dimensió distorsionada - DISTDIM -_**. Es pot utilitzar en una sola BD o en varies, o en múltiples **_taules de fets_**, o en múltiples **_DM_** o **_DWH_**.

## Algunes de les pitjors pràctiques en treballar amb Dimensions i Fets

1. **No dissenyar al DWH la dimensió de Temps, esperant que la feina la faci l'eina de BI**.

2. **Dissenyar el DDM amb JUNKDIMs, en comptes de taules de fets normals**.

3. **No crear SHRKDIMs i esperar a que ho faci l'eina BI**.

4. **No conèixer prou el negoci, lo qual pot portar a no contemplar CONFDIMs imprescindibles al nostre DDM**.  

5. **No definir bé les RPLYDIMs per desconeixement d'algun punt al pipeline del nostre projecte**.

6. **Definir DEGEDIMs**.

7. **Hem d'evitar les OUTGDIM al nostre DDM**

8. **Si confies en els usuaris (Directors o Gerents), mancaran STATDIMs al teu DDM**.

9. **Considerar els canvis a les dimensions com SCD-0**

10. **Considerar SCD-2 enlloc de contemplar possibles RCD**

11. **Si ens surt cap STACDIM al DDM, aleshores ens hem equivocat en l'anàlisi dimensional**

## Conclusions 

Dissenyar les dimensions i els processos ETL és no més una part petita de la feina, però important.

Per exemple, per maximitzar el rendiment, necessitarem un **mecanisme de captura de dades modificades** (**_CDC_**), que ens ens assegurari que **les dades es carreguin de forma incremental**.

Als ETL complexos necessitarem actualitzacions freqüents segons les necessitats del negoci. **És possible que haguem d'afegir o eliminar camps, canviar certs tipus de dades, modificar el SCD aplicat a un camp, etc**. Fer aquests canvis a les consultes no només porta molt de temps sinó que també és extremadament propens a errors. **Abans que ens adonem, és possible que haguem fet malbé un pipeline existent en implementar un canvi menor en el mecanisme de càrrega**.

Tot i això, encara sentirem que la major part del treball dur està fet. Però, **les empreses busquen constantment modernitzar i millorar els seus processos de dades**. Pot arribar el dia en què la nostra empresa decideixi canviar de plataforma d'emmagatzematge de dades. Suposem que han decidit passar d'1 BD on-premise a una plataforma al núvol.

És a dir, **primer, hem de crear una nova arquitectura a la nova plataforma**, després, **reescriurem totes les consultes per configurar pipelines natives a les noves taules de destí**. 

**L'impacte dels canvis pot ser tan gran que** possiblement haurem de fer tot el procés novament, des de zero!. Aleshores, **el nivell de complexitat involucrat pot esdevenir molt alt, fins i tot per als usuaris tècnics**. Imprescindible que aquests tècnics tinguin certificació (no necessariament universitaria, però si reconeguda) com a Analistes de Dades i/o Enginyers de Dades.

**Conclusió: L'anàlisi i enginyeria implicats l'han de desenvolupar tècnics qualificats, dotats dels mitjans adequats i assesorats, en lo que al negoci es refereix, per usuaris prou qualificats i motivats**.

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



