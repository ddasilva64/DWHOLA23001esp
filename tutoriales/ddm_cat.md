# Modelat Dimensional de Dades (DDM) - cat -

## Procés de DDM

1. Triar el **procés de negoci (en el qual tenim un bon coneixement)**.
2. Definir el **"grà"** (**granularitat**).
3. Identificar les **dimensions** (**atributs**).
4. Identificar els **fets** (**mètriques**).

## Identificació de dimensions i fets

> "La feina consisteix a fer preguntes, tantes com puguis, i enfrontar la manca de respostes precises amb certa humilitat."
> [Arthur Miller]

**_DDM_** utilitza un conjunt de tècniques i conceptes utilitzats en el disseny de **_DWH_**. Es considera diferent al model **_ER_**. **_DDM_** no implica necessàriament una **_RDB_**; el mateix enfocament de modelatge, a nivell lògic, es pot utilitzar per a qualsevol forma física, com bases de dades multidimensionals o arxius plans. ([Wikipedia](https://es.wikipedia.org/wiki/Modelado_dimensional))

## Taules de dimensions i fets

> En un sistema **_DWH_** o un sistema **_OLAP_**, la construcció de **_Cubs OLAP_** requereix una **_taula de fets_** i diverses **_taules de dimensions_**. Aquestes acompanyen la **_taula de fets_** i determinen els paràmetres (dimensions) en els quals es basen els fets enregistrats a la **_taula de fets_**.
Quan es construeixen cubs **_OLAP_**, les **_taules de dimensions_** són elements que contenen atributs (o camps) utilitzats per restringir i agrupar les dades emmagatzemades a una **_taula de fets_** al realitzar consultes en un entorn **_DWH_** o **_DM_**.
Aquestes dimensions són paràmetres en els quals depenen altres dades, que seran objecte d'estudi i anàlisi i que estan contingudes a la **_taula de fets_**. Les **_taules de dimensions_** ajuden a dur a terme aquest estudi/anàlisi proporcionant informació sobre les dades de la **_taula de fets_**, per la qual cosa es pot dir que en un cub **_OLAP_**, la **_taula de fets_** conté les dades d'interès, i les **_taules de dimensions_** contenen metadades sobre aquests fets.

> [Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_dimensi%C3%B3n)

En essència, **les taules de dimensions plantejen preguntes i les taules de fets proporcionen respostes**.

### Fets

- Emmagatzemen les **mètriques** que volem consultar amb les perspectives proporcionades per les dimensions.

- Tindran les **claus estrangeres (FKs) de les relacions** amb les taules de dimensions.

Exemples:

- Vendes.
- Productivitat.

![001-DDM](https://i.imgur.com/6KaLmDY.png)  
_DDM (imatge001)_

![002-Taula de fets](https://i.imgur.com/nP9xodv.png)  
_Taula de fets (imatge002)_

![003-Taula de fets Vendes](https://i.imgur.com/nAjHiaZ.png)  
_Taula de fets Vendes (imatge003)_

La taula central (Vendes) és la **_taula de fets_** d'un **_esquema en estrella_**, envoltada de cinc taules (Producte, Temps, Magatzem, Promoció i Client), que constitueixen les cinc dimensions que comprenen Vendes. En aquesta taula, s'emmagatzemen, en aquest cas, les unitats venudes i el preu obtingut per aquestes vendes; aquests són els fets empresarials o mesures emmagatzemades. ([Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_hechos)).

## Mesures o mètriques de negoci (fets)

Les mesures més útils per incloure en una **_taula de fets_** són aquelles que es poden sumar, com la quantitat de productes venuts, els costos de producció o els ingressos obtinguts per les vendes. Aquestes són mesuraments numèrics que es poden calcular sumant diverses quantitats a la taula. Per tant, els **fets** que s'emmagatzemen en una taula de fets gairebé sempre seran valors numèrics, sencers o reals. ([Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_hechos))

## Cardinalitat de la taula de fets

Les **taules de fets** poden contenir un gran nombre de files, a vegades centenars de milions de registres quan abasten un o més anys de la història d'una organització. Aquesta cardinalitat estarà limitada principalment per la cardinalitat de les **taules de dimensions**. Per exemple, si tens una **taula de fets "FT"** amb tres dimensions, D1, D2 i D3, el nombre màxim d'elements que tindrà la taula de fets FT és:

**$Card(FT) = Card(D1) * Card(D2) * Card(D3)$**

On $Card(x)$ és la cardinalitat de la taula.

**_¡Molt important!_**: Això significa que les **taules de dimensions** sempre s'han de construir abans que la **taula de fets** en el model.

Naturalment, aquestes cardinalitats no són fixes. Per exemple, si una de les dimensions es refereix als Clients, cada vegada que es registra un nou client, la cardinalitat de la **taula de fets** augmentarà. Una de les dimensions sol ser el Temps, que es pot mesurar de diverses formes (hores, dies, setmanes, etc.). Tanmateix, progressa contínuament, i perquè el sistema funcioni, periòdicament s'han d'afegir registres a la taula de la dimensió de Temps, la qual cosa també augmenta la cardinalitat de la **taula de fets**. Aquesta és la raó principal per la qual les **taules de fets** poden contenir milions de registres. ([Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_hechos)).

# Granularitat (grà)

Una característica important que defineix una **taula de fets** és el nivell de granularitat de les dades que emmagatzema. **La granularitat** es refereix al nivell de detall de les dades que emmagatzema, representant el nivell més atòmic pel qual es defineix la informació en la **taula de fets**. Per exemple, comptar el temps per hores (granularitat fina) no és el mateix que comptar el temps per setmanes (granularitat gruixuda). De manera similar, en el cas dels Productes, cada variante del mateix article es pot considerar com un producte (per exemple, diferents talles i colors de pantalons) o agrupar-se com un sol producte (per exemple, pantalons genèrics).

Com pots veure, la granularitat afecta la cardinalitat tant de les **dimensions** com de la **taula de fets**. Com més gran sigui la granularitat (grà més fi), més gran serà el nombre final de registres a la **taula de fets**.

> Com més gran sigui la **granularitat** de les **taules de dimensions**, més gran serà la cardinalitat de la **taula de fets**.

Quan la granularitat és major, és comú voler tenir subtotals parcials. Per exemple, si tenim una **taula de fets** amb vendes per dia, podria ser interessant tenir totals setmanals o mensuals. Aquestes dades es poden calcular mitjançant sumes parcials, però és comú afegir registres a la **taula de fets** on s'emmagatzemen aquests càlculs per millorar el rendiment de les consultes. En aquest cas, tant les dades de granularitat fina com les de granularitat gruixuda estaràn disponibles a la mateixa **taula de fets**, augmentant encara més la seva cardinalitat. ([Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_hechos))

# Agregació

L'agregació és un procés de càlcul en què es resumixen les dades dels registres detallats, la qual cosa sol donar com a resultat mesures de granularitat gruixuda. Quan les dades es resumixen, els detalls ja no estan disponibles directament per a l'analista, ja que s'eliminen de la **taula de fets**. Aquesta operació generalment es realitza amb les dades més antigues per continuar tenint aquesta informació, fins i tot si està resumida, mentre que els registres obsolets es poden eliminar de la **taula de fets** per alliberar espai. ([Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_hechos))

# Tipus de dades adequats

Com s'ha esmentat anteriorment, les **taules de fets** sovint emmagatzemen milions de registres. Per tant, **és crucial no malbaratar memòria**, i hem de fer servir els tipus de dades adequats. Si una mesura es pot emmagatzemar en un camp enter, no hem de definir aquest camp com un tipus enter llarg o real. De manera similar, si una magnitud requereix decimals i les seves característiques ho permeten, és millor fer servir un tipus real simple que un tipus real de doble precisió. Tingues en compte que triar un tipus de dades sobre un altre pot resultar inicialment en una diferència de uns quants bytes per registre, però quan es tracta de centenars de milions de registres en una **taula de fets**, aquesta diferència esdevé significativa (per exemple, 5 bytes x 200 milions de registres = 1 GB de memòria). ([Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_hechos))

[Enfocament de Disseny per Gestionar Dimensions i Fets d'Arribada Tardana](https://www.disoln.org/2013/12/Design-Approach-to-Handle-Late-Arriving-Dimensions-and-Late-Arriving-Facts.html)

## Dimensions

- Emmagatzemen els atributs que ens permeten tenir **diferents perspectives sobre els esdeveniments**.

- Tindran les **PKs de les relacions** amb les taules de fets.

Exemples:

- Productes.
- Subcategories.
- Clients.
- Empleats.

## Granularitat (grà)

Una característica important que defineix una **taula de fets** és el nivell de granularitat de les dades que emmagatzema. **La granularitat** es refereix al nivell de detall de les dades que emmagatzema, representant el nivell més atòmic pel qual es defineix la informació a la **taula de fets**. Per exemple, comptar el temps per hores (granularitat fina) no és el mateix que comptar el temps per setmanes (granularitat gruixuda). De manera similar, en el cas dels Productes, cada variant del mateix article es pot considerar com un producte (per exemple, diferents talles i colors de pantalons) o agrupar-se com un sol producte (per exemple, pantalons genèrics).

Com pots veure, la granularitat afecta la cardinalitat tant de les **dimensions** com de la **taula de fets**. Com més gran sigui la granularitat (grà més fi), més gran serà el nombre final de registres a la **taula de fets**.

> Com més gran sigui la **granularitat** de les **taules de dimensions**, més gran serà la cardinalitat de la **taula de fets**.

Quan la granularitat és major, és comú voler tenir subtotals parcials. Per exemple, si tenim una **taula de fets** amb vendes per dia, podria ser interessant tenir totals setmanals o mensuals. Aquestes dades es poden calcular mitjançant sumes parcials, però és comú afegir registres a la **taula de fets** on s'emmagatzemen aquests càlculs per millorar el rendiment de les consultes. En aquest cas, tant les dades de granularitat fina com les de granularitat gruixuda estaran disponibles a la mateixa **taula de fets**, augmentant encara més la seva cardinalitat. ([Viquipèdia](https://es.wikipedia.org/wiki/Tabla_de_hechos))

## Agregació

L'agregació és un procés de càlcul en què es resumeixen les dades dels registres detallats, la qual cosa sovint dóna com a resultat mesures de granularitat gruixuda. Quan les dades es resumeixen, els detalls ja no estan disponibles directament per a l'analista, ja que s'eliminen de la **taula de fets**. Aquesta operació generalment es realitza amb les dades més antigues per seguir tenint aquesta informació, fins i tot si està resumida, mentre que els registres obsolets es poden eliminar de la **taula de fets** per alliberar espai. ([Viquipèdia](https://es.wikipedia.org/wiki/Tabla_de_hechos))

## Tipus de dades adequats

Com es va mencionar anteriorment, les **taules de fets** sovint emmagatzemen milions de registres. Per tant, **és crucial no malgastar memòria**, i s'han d'utilitzar els tipus de dades adequats. Si una mesura es pot emmagatzemar en un camp enter, no s'ha de definir aquest camp com un tipus enter llarg o real. De manera similar, si una magnitud requereix decimals i les seves característiques ho permeten, és millor utilitzar un tipus real simple que un tipus real de doble precisió. Tingues en compte que triar un tipus de dades sobre un altre pot comportar inicialment una diferència d'uns quants bytes per registre, però quan es tracta de centenars de milions de registres en una **taula de fets**, aquesta diferència esdevé significativa (per exemple, 5 bytes x 200 milions de registres = 1 GB de memòria). ([Viquipèdia](https://es.wikipedia.org/wiki/Tabla_de_hechos))

[Enfocament de Disseny per Manejar Dimensions i Fets d'Arribada Tardana](https://www.disoln.org/2013/12/Design-Approach-to-Handle-Late-Arriving-Dimensions-and-Late-Arriving-Facts.html)

### Dimensions

- Emmagatzemen els atributs que ens permeten tenir **diferents perspectives sobre els esdeveniments**.

- Tindran les **PKs de les relacions** amb les taules de fets.

Exemples:

- Productes.
- Subcategories.
- Clients.
- Empleats.

#### La dimensió del Temps

En qualsevol **_DWH_**, pots trobar diversos cubells amb les seves **_taules de fets_** plenes de registres sobre alguna variable d'interès per al negoci que s'ha d'estudiar. Com es va mencionar anteriorment, cada **_taula de fets_** estarà envoltada de diverses **_taules de dimensions_**, segons els paràmetres que millor serveixin per dur a terme l'anàlisi dels fets que vols estudiar. Un paràmetre que gairebé amb seguretat serà comú a tots els cubells és el Temps, ja que l'habitual és emmagatzemar els esdeveniments a mesura que succeeixen amb el temps, obtenint així una sèrie temporal de la variable a estudiar.

Atès que el Temps és una dimensió present en pràcticament qualsevol cubell d'un sistema **_OLAP_**, mereix atenció especial. En dissenyar la dimensió del Temps (tant per a un **_esquema en estrella_** com per a un **_esquema en cop de neu_**), s'ha de tenir cura especial, ja que es pot fer de diverses formes i no totes són igualment eficients. La forma més comuna de dissenyar aquesta taula és establir la data o data/hora com a **_PK_** de la taula (taula de temps 1). Aquest disseny no és el més recomanat, ja que la majoria de sistemes de gestió de bases de dades consideren que és més costós cercar en camps de tipus "data" o "datetime". Aquests costos es redueixen si el camp clau és de tipus enter. A més, una dada enter sempre ocupa menys espai que una dada de tipus data (el camp clau es repetirà en milions de registres a la taula de fets i això pot ocupar molt espai). Per tant, el disseny de la taula de temps, si s'utilitza un camp "TimeID" enter com a clau primària (taula de temps 2).

**Taula de temps (1)**

| Data (PK) | Data/hora |
| :-------- | :---------: |
| Any        | char(4)     |
| Trimestre | char(6)     |
| Mes        | char(10)    |

**Taula de temps (2)**

| TimeID (PK) | Enter |
| :---------- | :-----: |
| Data         | Data/hora |
| Any           | char(4)     |
| Trimestre   | char(6)     |
| Mes           | char(10)   |

En completar la taula de Temps, si has triat un camp enter per a la clau, existeixen dues opcions: la més immediata és assignar valors numèrics consecutius (1, 2, 3, 4...) per a diferents valors de data. L'altra opció consistiria en assignar valors numèrics del tipus "yyyymDDM", és a dir, els primers quatre dígits del valor del camp indiquen l'any de la data, els següents dos el mes i els dos últims el dia. Aquesta segona forma proporciona una avantatge sobre la primera, ja que d'aquesta manera es aconsegueix que les dades numèriques en si mateixes proporcionin informació sobre a quina data es refereixen. Per exemple, si es troba el valor 20040723 a la taula de fets, sabrem que es refereix al 23 de juliol de 2004. D'altra banda, amb el primer mètode, es podrien trobar valors com 8456456, i per saber a quina data es refereix aquest valor, es tindria que fer una consulta a la taula de temps.

A més del camp clau TimeID, la **_taula de fets_** ha de contenir altres camps que també són importants de considerar. Aquests camps serien:

- Un camp "any": que contindria valors com '2002', '2003', '2004'...
- Un camp "mes": aquí es poden ingressar els valors 'gener', 'febrer'... (o en forma abreujada: 'gen', 'feb'...). Tot i que això no és incorrecte, es pot millorar si s'acompanya el nom del mes amb l'any al qual pertany. Per exemple: 'gener 2004', 'febrer 2004'... D'aquesta manera, s'optimitza la cerca dels valors d'un mes específic, ja que amb el primer mètode, si es necessiten els valors pertanyents al mes de "gener 2003", tota aquesta informació està continguda en un sol camp, el "mes", i no seria necessari consultar també el camp de l'any.
- Un camp "mesID": aquest camp hauria de ser de tipus enter i serviria per emmagatzemar valors del tipus 200601 (per a 'gener 2006') o 200602 (per a 'febrer 2006'). D'aquesta manera, és possible organitzar i agrupar per mes.

De manera anàloga al que s'ha fet amb el camp del mes, es podrien afegir més camps com "Època de l'any", "Trimestre", "Quinzena", "Setmana" de tipus text per poder mostrar-los, i els seus anàlegs de tipus enter "Època de l'any_ID", "TrimestreID", "QuinzenaID", "SetmanaID" per poder agrupar i ordenar. En general, es pot afegir un camp per a cada nivell de granularitat desitjat.

Un altre camp especial que es pot afegir és el "Dia de la setmana" ('dilluns', 'dimarts'...). Aquest camp sol afegir-se per poder realitzar estudis sobre el comportament dels dies de la setmana en general (no el primer dilluns del mes de gener d'un any específic, aquest tipus d'estudi generalment no és d'interès), i per aquesta raó, aquest camp no necessita estar acompanyat del mes o any com els camps anteriors. També es pot afegir el seu camp dual "ID" de tipus enter per poder ordenar i agrupar si és necessari.

Amb les addicions descrites, podríem tenir una taula de Temps com la que es mostra a la figura "Taula de Temps (3)". Això seria vàlid per a un disseny en estrella. Per a un disseny en cop de neu, la taula de Temps es tindria de desglossar en tantes taules com nivells jeràrquics contingui. Observa que els camps "ID" són tots de tipus enter, ja que serà en aquests camps on es realitzaran la majoria d'operacions i aquestes es realitzaran de manera més eficient en dades enters.

**Taula de temps (3)**

| TimeID (PK)   | Enter |
| :------------ | :-----: |
| Data          | Data/hora |
| Any            | char(4) |
| Trimestre    | char(6) |
| TrimestreID | Enter |
| Mes            | char(10)|
| MesID          | Enter |
| Quinzena    | char(10)|
| QuinzenaID | Enter |
| Setmana     | char(10)|
| SetmanaID  | Enter |
| Dia             | char(10)|
| DiaID         | Enter |
| DiaSetmana | char(10)|
| DiaSetmanaID | Enter |

### Granularitat de la dimensió i jerarquies

Cada dimensió es pot referir a conceptes com Temps, Productes, Clients, Àrea Geogràfica, etc. Ara bé, cada dimensió es pot mesurar de diferents maneres segons la granularitat desitjada. Per exemple, per a la dimensió Àrea Geogràfica, podríem considerar 'localitats', 'províncies', 'regions', 'països' o 'continentes'.

**La granularitat de les taules de fets i dimensions sempre ha de ser la mateixa**; d'altra manera, no es podrà consultar la informació amb el grau de granularitat més alt.

![004-Granularitat de la Zona Geogràfica](https://i.imgur.com/vDzQjC2.png)  
_Granularitat de la Zona Geogràfica (imatge004)_

La unitat de mesura (per localitats, províncies, etc.) determinarà aquesta granularitat. Com més petita sigui aquesta unitat de mesura, més fina serà aquesta granularitat (grà més fi); si les unitats de mesura són més grans, llavors parlem de granularitat gruixuda (grà gruix).

En molts casos, és interessant tenir les dades en diversos nivells de granularitat, és a dir, és important pel negoci poder consultar les dades (seguin l'exemple de les zones) per localitats, províncies, etc. En aquests casos, es crea una jerarquia dins de la dimensió, ja que tenim diversos nivells d'associació de les dades (amb altres dimensions com el temps, es podrien crear nivells jeràrquics del tipus 'dies', 'setmanes', 'mesos', etc.).

![005-Detall de la Taula de Dimensió](https://i.imgur.com/gHgpzeR.png)  
_Detall de la Taula de Dimensió (imatge005)_

Quan les **_taules de dimensions_** associades a una **_taula de fets_** no reflecteixen cap jerarquia (per exemple: les Zones són sempre només 'províncies', el temps es mesura només en 'dies', etc.), el cubell resultant serà en forma d'estrella. En aquest model, hi ha una **_taula de fets central_** envoltada de tantes taules com dimensions, i a més de la **_taula de fets_**, només hi haurà una taula per a cada dimensió.

![006-Esquema Estrella de 5 dimensions](https://i.imgur.com/4HPriuo.png)  
_Esquema Estrella de 5 dimensions (imatge006)_

Quan una o més de les dimensions del cubell reflecteixen algun tipus de jerarquia, existeixen dos enfocaments pel que fa a la forma com han de dissenyar-se les taules de dimensió. El primer consisteix a reflectir tots els nivells jeràrquics d'una dimensió dins d'una sola taula. En aquest cas, també tindríem un **_esquema en estrella_** com el descrit anteriorment, és a dir, en el model tindríem taules no normalitzades per millorar el rendiment.

L'altre enfocament consisteix a aplicar les regles de normalització **_RDB_** a les dimensions. Aquestes regles estan dissenyades per evitar redundàncies de dades en augmentar el nombre de taules; d'aquesta manera, la informació s'emmagatzema en menys espai. Aquest disseny dóna com a resultat un esquema en forma de cop de neu. Aquesta forma d'organitzar les dimensions d'un cubell **_OLAP_** té una desavantatge en comparació amb el model en estrella que no compensa l'estalvi d'espai d'emmagatzematge. En les aplicacions OLAP, el recurs crític no és tant l'espai d'emmagatzematge com el temps de resposta del sistema a les consultes dels usuaris, i s'ha demostrat que els models en forma de cop de neu tenen un temps de resposta més llarg que els models en estrella.

Sol ser millor reduir les dimensions en el **_DWH_** i obtenir un **_esquema en estrella_** en lloc d'un **_esquema en cop de neu_**. Per tant, **normalitzar les taules pot ser la pitjor solució, a diferència del model DBR**.

![007-Esquema en Cop de Neu de 5 dimensions](https://i.imgur.com/1Ata1st.png)  
_Esquema en Cop de Neu de 5 dimensions (imatge007)_

# Taules de fets desnormalitzades

Imagina la mateixa situació:

Tens la mateixa taula en una fulla de càlcul amb les mateixes columnes, relacionada amb Venedors (taula Allowances):

| ID de l'Empleat | Nom de l'Empleat | Data d'Inici | Data de Fi | Zona de Vendes | Comentaris                      | Assignacions Mensuals |
| :---------: | :------------ | :--------: | :--------: | :--------- | :---------------------------- | -----------------: |
| 601         | John Smith    | 01/01/2021 | 31/03/2022 | O          | Estarà allà durant 455 dies | $456.24            |
| 601         | John Smith    | 01/04/2022 | 31/08/2022 | E          | Estarà allà durant 153 dies | $348.51            |
| 601         | John Smith    | 01/09/2022 | 31/12/2022 | C          | Estarà allà durant 112 dies | $132.97            |

Volem relacionar aquesta taula amb la **taula de fets** de Vendes que prové de la base de dades **OLTP**. També volem relacionar aquestes dues taules, però ara no tenim un **DWH**. Establirem la relació a **BI DM**. A més, el Cap de Vendes desitja revisar fàcilment aquesta taula (desnormalitzada) sense haver de revisar-la dia a dia.

Ara, no podem relacionar-la per dia amb la taula de Dimensió de Dates perquè no podem descomprimir aquesta taula. Calcularem les assignacions mitjançant càlculs, però no mitjançant relacions. Això és més complicat que l'exemple anterior.

En desnormalitzar-la, obtenim aquesta taula:

| ID de l'Empleat | Nom de l'Empleat | Data d'Inici 01 | Data de Fi 01 | Zona de Vendes 01 | Comentaris 01                   | Assignacions Mensuals 01 | Data d'Inici 02 | Data de Fi 02 | Zona de Vendes 02 | Comentaris 02                   | Assignacions Mensuals 02 | Data d'Inici 03 | Data de Fi 03 | Zona de Vendes 03 | Comentaris 03                   | Assignacions Mensuals 03 |
| :---------: | :------------ | :-----------: | :----------: | :------------ | :---------------------------- | --------------------: | :---------: | :-----------: | :----------: | :------------ | :---------------------------- | :-----------: | :----------: | :------------ | :---------------------------- | --------------------: |
| 601         | John Smith    | 01/01/2021    | 31/03/2022   | O             | Estarà allà durant 455 dies | $456.24               | 01/04/2022    | 31/08/2022   | E             | Estarà allà durant 153 dies | $348.51               | 01/09/2022    | 31/12/2022   | C             | Estarà allà durant 112 dies | $132.97               |

Quants registres tindrà aquesta nova taula desnormalitzada? En tindrà 3, la qual cosa facilitarà molt la revisió per al Cap de Vendes.

Aquesta tasca també es pot realitzar fàcilment utilitzant **Python** i **Pandas**.

A més, aquesta taula tampoc és una taula de dimensió, sinó una taula de fets. Ara, l'ID de l'Empleat i la Zona de Vendes són claus externes (**FKs**), però hem de calcular mètriques a **BI** perquè la Data no és un atribut en aquesta solució.

Tot i que podem respondre preguntes, requerirà un esforç innecessari a **BI**.

Per cert, ara les Assignacions i les Vendes no estan relacionades directament a **DM**; estan relacionades a través de taules de dimensió (Empleats, Zones de Vendes, però no amb Dates).

# Taules de fets descomprimides

Imagina el següent escenari:

Tens una taula en una fulla de càlcul amb les següents columnes, relacionades amb Venedors (anomenada "Allowances"):

| ID de l'Empleat | Nom de l'Empleat | Data d'Inici | Data de Fi   | Zona de Vendes | Comentaris                      | Assignacions Mensuals |
| :---------:     | :------------       | :--------:      | :--------:     | :---------      | :---------------------------- | -----------------:     |
| 601             | John Smith          | 01/01/2021      | 31/03/2022     | O               | Estarà allà durant 455 dies | $456.24                |
| 601             | John Smith          | 01/04/2022      | 31/08/2022     | E               | Estarà allà durant 153 dies | $348.51                |
| 601             | John Smith          | 01/09/2022      | 31/12/2022     | C               | Estarà allà durant 112 dies | $132.97                |

Desitges relacionar aquesta taula amb la **taula de fets** de Vendes que prové de la base de dades **OLTP**. També desitges relacionar aquestes dues taules en el teu **DWH**.

A més, necessites calcular el cost per dia en assignacions. Per exemple, el 1 de gener de 2021, John Smith rep $456.24/31 = $14.72, i el 31 de desembre de 2022, rep $132.97/31 = $4.29 en assignacions.

La taula de Vendes (**taula de fets**) té una granularitat diària. Per tant, has de assegurar-te que aquesta taula també tingui la mateixa granularitat. En altres paraules, has de "descomprimir" aquesta taula perquè es vegi de la següent manera:

| ID de l'Empleat | Data       | Zona de Vendes | Assignacions Mensuals |
| :---------:     | :--------:  | :---------      | -----------------:     |
| 601             | 01/01/2021  | O               | $14.72                |
| 601             | 01/02/2021  | O               | $14.72                |
| 601             | 01/03/2021  | O               | $14.72                |
...
| 601             | 31/12/2022  | C               | $4.29                 |

Quants registres tindrà aquesta nova taula "descomprimida"? Tindrà un total de 720 registres (amb granularitat diària), calculats com 455 + 153 + 112 = 720.

Aquesta tasca es pot realitzar fàcilment en **Python** i **Pandas** durant el procés d'**ETL**, utilitzant una eina com **Pentaho Data Integration**.

De fet, aquesta taula no és una taula de dimensió sinó una taula de fets. L'ID de l'Empleat, la Data i la Zona de Vendes són claus externes (**FKs**), i les assignacions diàries són una mètrica.

Amb aquesta estructura, pots respondre preguntes com:

- Quants costos va tenir John Smith en assignacions el 2021?
- Quant vam pagar en assignacions a tots els venedors al març de 2022?
- Quina quantitat vam pagar en assignacions entre l'1 de juliol de 2022 i el 15 de setembre de 2022 a la zona de vendes "O" (Oest)?

Per cert, les Assignacions i les Vendes no estan relacionades directament al **DWH**; estan relacionades a través de taules de dimensió (com Empleats, Dates i Zones de Vendes).

# Data Warehouse (DWH), Data Lake (DL) i Data Lakehouse (DLH)

## **_DWH_**

**Característiques**:
- El **repositori central que conté dades estructurades** (columnes i files) obtingudes a través d'un procés d'**_ETL_** des de sistemes transaccionals, fitxers .CSV, etc.

**Utilitzat per**:
- **_Analistes de Negocis_**

**Utilitzat en**:
- **_Informes_**
- **_BSC_** (Quadre de Comandament Integral)
- **_BI_** (Intel·ligència de Negocis)

![008-DWH](https://i.imgur.com/1NDE7J7.png)
 _DWH (imatge008)_

## **_Llac de Dades (DL)_**

**Característiques**:
- El **repositori de dades estructurades** des de **_ETL_** (taules i vistes de bases de dades, fitxers .CSV, etc.), **dades semiestructurades i no estructurades** (com imatges, vídeos, pàgines web, etc.).
- Les dades es poden emmagatzemar i consultar fàcilment.
- Es pot considerar un **_repositori de dades brutes_**.

**Utilitzat per**:
- **_Científics de Dades_**
- **_Enginyers de Dades_**
- **_Analistes de Dades_**

**Utilitzat en**:
- **_ML_** (Aprenentatge Automàtic)
- **_Analítica Predictiva_**
- **_Descobriment de Dades_**
- **_Perfilat_**

![009-DL](https://i.imgur.com/RtAStZl.png)  
 _DL (imatge009)_

# DWH vs. DL

| Característiques | DWH | DL |
| :-------------- | :-- | :- |
| Dades | Optimitzat per analitzar dades relacionades de fonts com bases de dades transaccionals, bases de dades operatives i aplicacions de negocis. | Format per dades no relacionades de fonts com llocs web, xarxes socials, dispositius IoT i aplicacions mòbils. |
| Esquema | L'estructura de dades es defineix abans de la implementació per optimitzar les consultes (esquema en escriure). | Emmagatzema informació sense definir una estructura de dades. Permet implementar sense ni tan sols conèixer les preguntes comercials (esquema en llegir). |
| Qualitat de Dades | Les dades es netegen, enriqueixen i transformen per servir com a "única font de veritat". | Qualsevol dada, que potser o no hagi passat per un procés de neteja i transformació (dades crues). |
| Usuaris | Analistes de negocis. | Científics de dades, enginyers de dades i analistes de dades (quan s'utilitza informació neta). | Informes d'Anàlisi, taules de comandament i BI. Aprenentatge automàtic, anàlisi predictiu, descobriment de dades i perfilat. |

## Data Lakehouse (DLH)

**Característiques**:
- Combina les millors característiques de **_DWH_** i **_DL_**.
- Permet el consum ràpid de dades emmagatzemades per a **_BI_**, informes, **_DS_** (Ciència de Dades) i **_ML_** (Aprenentatge Automàtic).

![010-DLH 01](https://i.imgur.com/Vu3X94C.png)  
_DLH (imatge010)_

![011-DLH 02](https://i.imgur.com/a0LdoXX.png)  
_DLH (imatge011)_

![012-DLH 03](https://i.imgur.com/kNFF8J3.png)  
_DLH (imatge012)_

## Tipus d'esquemes dimensionals

### El cub com a representació multidimensional de dades

El cub és una representació multidimensional de les dades emmagatzemades en un **_DWH_**. Les dimensions estan representades pels marges del cub (per això no has de pensar necessàriament només en 3D), i els fets es troben en cada cel·la on s'intersequen.

Per exemple, un cub podria representar la taula de fets "Vendes" i les taules de dimensió "Temps", "Producte" i "Ubicació" (de la botiga). El cub està compost per cel·les que contenen dades de vendes associades amb una combinació específica de valors de dimensió. En altres paraules, una cel·la pot contenir la quantitat de vendes d'un producte en un mes i regió específics.

![013-Representació de DWH com a cub 01](https://i.imgur.com/8z58vEP.png)  
_DWH (imatge013)_

![014-Representació de DWH com a cub 02](https://i.imgur.com/IOxAIGJ.png)  
_DWH (imatge014)_

### Esquema estrella

En les bases de dades utilitzades per a **_DWH_**, un **_esquema estrella_** és un model de dades que té una taula de fets (taula **_fact_**) que conté les dades per a l'anàlisi, envoltada de taules de dimensió (taules **_dim_**). Aquest aspecte, una taula de fets més gran (o central) envoltada de radis o taules més petites, és el que s'assembla a una estrella, donant a aquest tipus de construcció el seu nom.

Les taules de dimensió sempre tindran una clau primària simple (**_PK_**), mentre que a la taula de fets, la clau primària (**_FK_**) estarà composta per les **_PK_** de les taules de dimensió. ([Viquipèdia](https://ca.wikipedia.org/wiki/Esquema_en_estrella))

**_Avantatges_**:
- Estructura simple i fàcil d'entendre.
- Permet consultes ràpides i eficients en conjunts de dades grans.
- Facilita l'anàlisi multidimensional i la presa de decisions informades.
- Admet l'addició de noves dimensions sense alterar la taula de fets existent.

**_Desavantatges_**:
- No és adequat per a bases de dades transaccionals.
- No és flexible per a casos que requereixen una major complexitat en l'estructura de dades.
- La redundància de dades pot ser un problema quan les dimensions es superposen.
- Requereix una planificació cuidadosa i una anàlisi prèvia per determinar l'estructura òptima del model.

![015-Esquema estrella](https://i.imgur.com/rnSXwhh.png)  
_Esquema estrella (imatge015)_

### Esquema de cop de neu

En les bases de dades utilitzades en **_DWH_**, un **_esquema de cop de neu_** és una estructura una mica més complexa que l'**_esquema estrella_**. **_Succeeix quan alguna de les dimensions s'implementa amb més d'una taula de dades_**. **_El propòsit és normalitzar les taules i, per tant, reduir l'espai d'emmagatzematge eliminant la redundància de dades, però té el contrapunt de generar un pitjor rendiment_** a causa de la necessitat de crear més taules de dimensió i més relacions entre les taules (JOIN), la qual cosa impacta directament en el rendiment. ([Viquipèdia](https://ca.wikipedia.org/wiki/Esquema_en_cop_de_neu))

![016-Esquema de cop de neu](https://i.imgur.com/eF9u4nd.png)  
_Esquema de cop de neu (imatge016)_

![017-Tipus d'esquemes dimensionals](https://i.imgur.com/z79R1m2.png)  
_Tipus d'esquemes dimensionals (imatge017)_

**_Avantatges_**:
- Redueix la redundància de dades i l'espai d'emmagatzematge.
- Permet un millor control de la integritat de dades i la qualitat de dades.
- Més adequat per a situacions amb múltiples relacions entre taules de dimensió.
- Proporciona major flexibilitat per afegir noves dimensions.

**_Desavantatges_**:
- Pot ser més complex i difícil d'entendre que l'**_esquema estrella_**.
- Pot requerir més temps i recursos per construir i mantenir que l'**_esquema estrella_**.
- Pot tenir un major cost de processament de consultes a causa de la necessitat d'unió addicional entre taules normalitzades.

Un exemple seria Productes amb Categories. La dimensió Productes estaria relacionada amb la dimensió Categories.

**_Molt important!_**: Els models mai no han de relacionar-se a través de taules de fets; és a dir, **mai no hem de relacionar directament dues taules de fets**. Sempre, les relacions entre models es porten a terme a través de les dimensions que comparteixen.

## Llista d'imatges

- **img001**: Del curs "Data Warehousing i modelatge OLAP" de Platzi.
- **img002**: Del curs "Data Warehousing i modelatge OLAP" de Platzi.
- **img003**: Del curs "Data Warehousing i modelatge OLAP" de Platzi.
- **img004**: De Wikipedia.
- **img005**: Del curs "Data Warehousing i modelatge OLAP" de Platzi.
- **img006**: Del curs "Data Warehousing i modelatge OLAP" de Platzi.
- **img007**: Del curs "Data Warehousing i modelatge OLAP" de Platzi.
- **img008**: Del curs "Data Warehousing i modelatge OLAP" de Platzi.
- **img009**: Del curs "Data Warehousing i modelatge OLAP" de Platzi.
- **img010**: Del curs "Data Warehousing i modelatge OLAP" de Platzi.
- **img011**: Del curs "Data Warehousing i modelatge OLAP" de Platzi.
- **img012**: Del curs "Data Warehousing i modelatge OLAP" de Platzi.
- **img013**: Del curs "Data Warehousing i modelatge OLAP" de Platzi.
- **img014**: Del curs "Data Warehousing i modelatge OLAP" de Platzi.
- **img015**: Del curs "Data Warehousing i modelatge OLAP" de Platzi.
- **img016**: Del curs "Data Warehousing i modelatge OLAP" de Platzi.
- **img017**: Del curs "Data Warehousing i modelatge OLAP" de Platzi.

## Descàrrec de responsabilitat

**_Platzi_** (abans conegut com Mejorando.la) és una plataforma d'educació en línia a Amèrica Llatina. Va ser fundada el 2011 per l'enginyer colombià **Freddy Vega** i el científic de la computació guatemalenc **Christian Van Der Henst**. [Wikipedia](https://ca.wikipedia.org/wiki/Platzi)

## Ús de ChatGPT

**_Ús de ChatGPT 3.5_**

Aquest projecte ha estat verificat per ortografia, sintaxi i contingut utilitzant [**_ChatGPT 3.5_**](https://chat.openai.com/chat).  

Referència:  
[OpenAI. (2023). ChatGPT (versió del 19 de setembre de 2023) \[Large Language Model\]](https://chat.openai.com/chat)