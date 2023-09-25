# Modelatge Dimensional de Dades (DDM) - cat -

# Procés DDM (Modelatge Dimensional de Dades)

1. Triar el **procediment de negoci (del qual tenim coneixement)**.
2. Declarar la **"granularitat"** (**nivell de detall**).
3. Identificar les **dimensions** (**atributs**).
4. Identificar els **fets** (**mètriques**).

## Taules de dimensió i de fet

> "La feina consisteix a fer preguntes, tantes com pugueu, i enfrontar-vos a la manca de respostes precises amb certa humilitat."
> [Arthur Miller]

> El **_DDM_** utilitza un conjunt de tècniques i conceptes utilitzats al disseny d'un **_DWH_** (Magatzem de Dades). Es considera diferent del model **_ER_**. El **_DDM_** no implica necessàriament una **_RDB_** (Base de Dades Relacional); el mateix enfocament de modelatge, a nivell lògic, es pot utilitzar en qualsevol forma física, com arxius plans o bases de dades multidimensionals. ([Wikipedia](https://es.wikipedia.org/wiki/Modelado_dimensional))

> En un sistema **_DWH_** o **_OLAP_**, la construcció de **_Cubs OLAP_** requereix una **_taula de fet_** i diverses **_taules de dimensió_**. Aquestes acompanyen la **_taula de fets_** i determinals paràmetres (dimensions) sobre els quals depenals fets registrats a la **_taula de fets_**.
Quan es construeixen cubs **_OLAP_**, les **_taules de dimensió_** són elements que contenen atributs (o camps) utilitzats per restringir i agrupar les dades emmagatzemades a una **_taula de fet_** quan es realitzen consultes en un entorn **_DWH_** o **_DM_**.
Aquestes dimensions són paràmetres dels quals depenen altres dades, que seran l'objecte d'estudi i anàlisi i estan contingudes a la **_taula de fet_**. Les **_taules de dimensió_** ajuden a dur a terme aquest estudi/anàlisi proporcionant informació sobre les dades de la **_taula de fet_**, pel que es pot dir que en un cub **_OLAP_**, la **_taula de fet_** conté les dades d'interès i les **_taules de dimensió_** contenen metadades sobre aquests fets. ([Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_dimensi%C3%B3n))

En essència, **les taules de dimensió fan preguntes, i les taules de fet proporcionen respostes**.

### Fets

- Emmagatzemen les **mètriques** que volem consultar amb les perspectives que proporcionen les dimensions.

- Contenen les **claus foranes (FKs) de les relacions** amb les taules de dimensions.

Exemples:

- Vendes.
- Productivitat.

![001-DM](https://i.imgur.com/6KaLmDY.png)  
_DM (imatge 001)_

![002-Taula de fets](https://i.imgur.com/nP9xodv.png)  
_Taula de fets (imatge 002)_

![003-Taula de fets Vendes](https://i.imgur.com/nAjHiaZ.png)  
_Taula de fets Vendes (imatge 003)_

La taula central (Vendes) és la **_taula de fets_** d'un **_esquema d'estrella_**, envoltada per cinc taules (Producte, Temps, Magatzem, Promoció i Client), que constitueixen les cinc dimensions que componen les Vendes. En aquesta taula, s'emmagatzemen, en aquest cas, les unitats venudes i el preu obtingut per aquestes vendes; aquests són els fets o mesures de negoci emmagatzemades. ([Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_hechos)).

#### Mesures o mètriques de negoci (fets)

Les mesures més útils per incloure en una **_taula de fets_** són additives, és a dir, aquelles mesures que es poden sumar, com la quantitat de productes venuts, els costos de producció o els ingressos obtinguts per les vendes. Aquestes són mesures numèriques que es poden calcular sumant diverses quantitats a la taula. En conseqüència, els **_fets_** que s'emmagatzemaran en una taula de fets gairebé sempre seran valors numèrics sencers o reals. ([Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_hechos))

#### Cardinalitat de la taula de fets

Les **_taules de fets_** poden contenir un gran nombre de files, a vegades centenars de milions de registres quan abasten un o més anys de la història d'una organització. Aquesta cardinalitat estarà principalment limitada per la cardinalitat de les **_taules de dimensions_**. Per exemple, si tens una **_taula de fets "FT"_** amb tres dimensions, D1, D2 i D3, el nombre màxim d'elements que tindrà la taula de fets FT serà:

**$Card(FT) = Card(D1) * Card(D2) * Card(D3)$**

On $Card(x)$ és la cardinalitat de la taula.

**_¡Molt important!_**: Això significa que les **_taules de dimensions_** sempre s'han de construir abans que la **_taula de fets_** al model.

Naturalment, aquestes cardinalitats no són fixes. Per exemple, si una de les dimensions es refereix als Clients, cada vegada que es registra un nou client, la cardinalitat de la **_taula de fets_** augmentarà. Una de les dimensions sol ser el Temps, que es pot mesurar de diverses formes (hores, dies, setmanes, etc.). No obstant això, avança contínuament, i perquè el sistema funcioni, s'han d'afegir periòdicament registres a la taula de la dimensió del Temps, la qual cosa també augmenta la cardinalitat de la **_taula de fets_**. Aquesta és la raó principal per la qual les **_taules de fets_** poden contenir milions de registres. ([Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_hechos))  

#### Granularitat (grà) 

Una característica important que defineix una **_taula de fets_** és el nivell de granularitat de les dades que emmagatzema. La **_granularitat_** es refereix al nivell de detall de les dades que emmagatzema, representant el nivell més atòmic mitjançant el qual es defineix la informació a la **_taula de fets_**. Per exemple, comptar el temps en hores (granularitat fina) no és el mateix que comptar el temps per setmanes (granularitat gruixuda). De manera similar, al cas dels Productes, cada variant del mateix article es pot considerar com un producte (per exemple, diferents talles i colors de pantalons) o agrupar-se com un sol producte (per exemple, pantalons genèrics).

Com es pot veure, la granularitat afecta la cardinalitat tant de les **_dimensions_** com de la **_taula de fets_**. Com més gran sigui la granularitat (grà més fi), major serà el nombre final de registres a la **_taula de fets_**.

> Com més gran sigui la **_granularitat_** de les **_taules de dimensions_**, major serà la cardinalitat de la **_taula de fets_**.

Quan la **_granularitat_** és major, és comú voler tenir subtotals parcials. Per exemple, si tenim una **_taula de fets_** amb vendes per dia, podria ser interessant tenir totals setmanals o mensuals. Aquestes dades es poden calcular mitjançant sumes parcials, però és comú afegir registres a la **_taula de fets_** on s'emmagatzemen aquests càlculs per millorar el rendiment de les consultes. En aquest cas, tant les dades de granularitat fina com de granularitat gruixuda estaran disponibles a la mateixa **_taula de fets_**, augmentant encara més la seva cardinalitat. ([Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_hechos))

#### Agregació

L'agregació és un procés de càlcul al qual les dades dels registres de detall es resumeixen, generalment resultant en mesures de granularitat gruixuda. Quan les dades es resumeixen, els detalls ja no estan directament disponibles per a l'analista, ja que s'eliminen de la **_taula de fets_**. Aquesta operació generalment es realitza amb les dades més antigues per seguir tenint aquesta informació, fins i tot si està resumida, mentre que els registres obsolets es podaliminar de la **_taula de fets_** per alliberar espai. ([Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_hechos))

#### Tipus de dades adequats

Com es va mencionar anteriorment, les **_taules de fets_** sovint emmagatzemen milions de registres. Per tant, **_és crucial no malgastar memòria_**, i hem de fer servir els tipus de dades apropiats. Si una mesura es pot emmagatzemar en un camp de tipus enter, no hauríem de definir aquest camp com un enter llarg o un tipus real. De la mateixa manera, si una magnitud requereix decimals i les seves característiques ho permeten, és millor fer servir un tipus real simple que un tipus real de doble precisió. Tingues en compte que triar un tipus de dades en lloc d'un altre pot inicialment resultar en una diferència de només uns pocs bytes per registre, però quan es tracta de centenars de milions de registres en una **_taula de fets_**, aquesta diferència esdevé significativa (per exemple, 5 bytes x 200 milions de registres = 1 GB de memòria). ([Wikipedia](https://es.wikipedia.org/wiki/Tabla_de_hechos))

[Enfocament de disseny per gestionar dimensions i fets que arriben tard](https://www.disoln.org/2013/12/Design-Approach-to-Handle-Late-Arriving-Dimensions-and-Late-Arriving-Facts.html)


## Tipus d'esquemes dimensionals

### El cub com a representació multidimensional de dades

El cub és una representació multidimensional de les dades emmagatzemades en un **_DWH_** (Data Warehouse). Les dimensions estan representades pels costats del cub (per això no necessàriament has de pensar només en 3D), i els fets es troben a cada casella on es creuen.

Per exemple, un cub podria representar la taula de fets "Vendes" i les taules de dimensions "Temps", "Producte" i "Ubicació" (de la botiga). El cub està format per caselles que contenen dades de vendes associades a una combinació específica de valors de dimensions. En altres paraules, una casella pot contenir la quantitat de vendes d'un producte en un mes i una regió específics.

![004-Representació de DWH com un cub 01](https://i.imgur.com/8z58vEP.png)  
_DWH (imatge 004)_

![005-Representació de DWH com un cub 02](https://i.imgur.com/IOxAIGJ.png)  
_DWH (imatge 005)_

### Esquema en estrella

En les bases de dades utilitzades als **_DWH_** (magatzems de dades), un **_esquema en estrella_** és un model de dades que consta d'una **_taula de fets_** que conté les dades per a l'anàlisi, envoltada de **_taules de dimensions_**. Aquest aspecte, una **_taula de fets_** més gran (o central) envoltada de taules més petites, que s'assembla a una estrella, és d'on prové el nom d'aquesta construcció.

Les **_taules de dimensions_** sempre tindran una clau primària simple (**_PK_**), mentre que a la **_taula de fets_**, la clau primària (**_FK_**) estarà composta per les claus primàries (**_PK_**) de les taules de dimensions. ([Wikipedia](https://es.wikipedia.org/wiki/Esquema_en_estrella))

**_Avantatges_**:
- Estructura simple i fàcil d'entendre.
- Permet consultes ràpides i eficients en conjunts de dades grans.
- Facilita l'anàlisi multidimensional i la presa de decisions informades.
- Admet afegir noves dimensions sense alterar la taula de fets existent.

**_Inconvenients_**:
- No és adequat per a bases de dades transaccionals.
- No és flexible per a casos que requereixen major complexitat a l'estructura de dades.
- La redundància de dades pot ser un problema quan les dimensions es solapen.
- Requereix una planificació cuidadosa i una anàlisi prèvia per determinar l'estructura òptima del model.

![006-Esquema en estrella](https://i.imgur.com/rnSXwhh.png)  
_Esquema en estrella (imatge 006)_

### Esquema en forma de floc de neu

A les bases de dades utilitzades en **_DWH_** (magatzems de dades), un **_esquema en forma de floc de neu_** és una estructura una mica més complexa que l'**_esquema en estrella_**. **_Es produeix quan qualsevol de les dimensions s'implementa amb més d'una taula de dades_**. **_El propòsit és normalitzar les taules i, d'aquesta manera, reduir l'espai d'emmagatzematge eliminant la redundància de dades, però té la contrapartida de generar un pitjor rendiment_** a causa de la necessitat de crear més taules de dimensions i més relacions entre les taules (JOINs), la qual cosa impacta directament al rendiment.  ([Wikipedia](https://es.wikipedia.org/wiki/Esquema_en_copo_de_nieve))

![007-Esquema en forma de floc de neu](https://i.imgur.com/eF9u4nd.png)  
_Esquema en forma de floc de neu (imatge 007)_

![008-Tipus d'esquemes dimensionals](https://i.imgur.com/z79R1m2.png)  
_Tipus d'esquemes dimensionals (imatge 008)_

**_Avantatges_**:
- Redueix la redundància de dades i l'espai d'emmagatzematge.
- Permet un millor control de la integritat de dades i la qualitat de dades.
- S'adapta millor a situacions amb múltiples relacions entre taules de dimensions.
- Proporciona major flexibilitat per afegir noves dimensions.

**_Inconvenients_**:
- Pot ser més complex i difícil d'entendre que l'**_esquema en estrella_**.
- Pot requerir més temps i recursos per construir i mantenir que l'**_esquema en estrella_**.
- Pot tenir un cost de processament de consultes més alt a causa de la necessitat de joins addicionals entre taules normalitzades.

Un exemple seria Productes amb Categories. La dimensió de Productes estaria relacionada amb la dimensió de Categories.

**_¡Molt important!_**: Els models mai han d'estar relacionats a través de taules de fets; és a dir, **mai hem de relacionar directament dues taules de fets**. Sempre, les relacions entre models es realitzen a través de les dimensions que comparteixen.

## Magatzem de Dades (DWH), Llac de Dades (DL) i Magatzem del Llac de Dades (DLH)

### **_DWH_** (Magatzem de Dades)

**Característiques**:
- És el **repositori central que conté dades estructurades** (files i columnes) obtingudes a través d'un procés **_ETL_** des de sistemes transaccionals, arxius .CSV, etc.

**Utilitzat per**:
- **_Analistes de Negocis_**

**Utilitzat en**:
- **_Informes_**
- **_BSC_** (Quadre de Comandament Integral)
- **_BI_** (Intel·ligència de Negocis)

![009-DWH](https://i.imgur.com/1NDE7J7.png)
_DWH (imatge 009)_

#### **_Llac de Dades (DL)_** (Data Lake)

**Característiques**:
- És el **repositori de dades estructurades** obtingudes del **_ETL_** (taules i vistes de bases de dades, arxius .CSV, etc.), **dades semi-estructurades i no estructurades** (com imatges, vídeos, pàgines web, etc.).
- Les dades es poden emmagatzemar i consultar fàcilment.
- Es pot considerar un **_repositori de dades en brut_**.

**Utilitzat per**:
- **_Científics de Dades_**
- **_Enginyers de Dades_**
- **_Analistes de Dades_**

**Utilitzat en**:
- **_ML_** (Aprenentatge Automàtic)
- **_Analítica Predictiva_**
- **_Descobriment de Dades_**
- **_Perfilat_**

![010-DL](https://i.imgur.com/RtAStZl.png)  
 _DL (imatge 010)_

### **_DWH_** vs. **_DL_**

| Característiques | DWH | DL |
| :-------------- | :-- | :- |
| Dades | Optimitzat per analitzar dades relacionades de fonts com bases de dades transaccionals, bases de dades operatives i aplicacions empresarials. | Format per dades no relacionades de fonts com llocs web, xarxes socials, dispositius IoT i aplicacions mòbils. |
| Esquema | L'estructura de dades es defineix abans de la implementació per optimitzar les consultes (esquema en escriptura). | Emmagatzema informació sense definir una estructura de dades. Permet implementar sense ni tan sols conèixer les preguntes de negocis (esquema en lectura). |
| Qualitat de dades | Les dades es netegen, enriqueixen i transformen per servir com la "font única de veritat". | Qualsevol dada, que pot o no haver passat per un procés de neteja i transformació (dades en brut). |
| Usuaris | Analistes de negocis. | Científics de dades, enginyers de dades i analistes de dades (quan s'utilitza informació neta). | Analítica, informes, quadres de comandament i BI. Aprenentatge automàtic, analítica predictiva, descobriment de dades i perfilat. |

### Data Lakehouse (**_DLH_**)

**Característiques**:
- Combina les millors característiques tant de **_DWH_** com de **_DL_**.
- Permet el consum ràpid de dades emmagatzemades per a **_BI_** (Intel·ligència de Negocis), informes, **_DS_** (Ciència de Dades) i **_ML_** (Aprenentatge Automàtic).

![011-DLH 01](https://i.imgur.com/Vu3X94C.png)  
_DLH (imatge 011)_

![012-DLH 02](https://i.imgur.com/a0LdoXX.png)  
_DLH (imatge 012)_

![013-DLH 03](https://i.imgur.com/kNFF8J3.png)  
_DLH (imatge 013)_

## Algunes de les pitjors pràctiques al Modelatge Dimensional de Dades

1. Anar directament a la **fase física**.

2. **Confondre el** **_model ER_** **amb el** **_DDM_**.  
   - Configurar Excels com a fonts de dades sempre amb estructures normalitzades.  
   - No definir clarament les claus de relació de dades.  
   - Una base de dades normalitzada és millor que una no normalitzada al **_DDM_**.

3. **No tenir empleats de TI al disseny del** **_DDM_** **(per exemple, tenir Directors o Gerents de negocis)**.

4. **Errors a les definicions de taules de fets i taules de dimensions**.  
   - **Relacionar taules de fets directament**.  
   - Definir més atributs a les dimensions del que és necessari per a la taula de fets sol·licitada.  
   - Les taules de dimensions no pregunten res.  
   - Les taules de fets no responen res.  
   - **No carregar dades atòmiques a estructures dimensionals**.  
   - No reduir les dimensions al **_DDM_**.  
   - **Confondre subdimensions amb jerarquies de dades**.  
   - No resoldre correspondències de molts a molts a les taules de fets.  
   - No resoldre correspondències de molts a molts a les taules de dimensions.  
   - No emmagatzemar descripcions a taules de dimensions.  
   - **No assegurar-se que totes les dimensions relacionades amb el mateix fet tinguin el mateix nivell de granularitat**.
   - **Dissenyar primer les taules de fets que les de dimensió**.

5. **Manca d'un bon enteniment de les regles de negoci**.  
   - Fallar en avaluar constantment els requisits i realitats per proporcionar una solució de **_BI_** que sigui acceptada pels usuaris de negocis i que recolzi el seu procés de presa de decisions.  
   - No crear **l'estructura dels models dimensionals basada en** els **processos de negoci** que anem a contemplar, **específicament**.

6. **Errors en la granularitat**.  
   - No considerar correctament la granularitat de les dimensions.  
   - **No considerar la granularitat correcta de les taules de fets, per exemple, per permetre "usabilitat" per a Directors o Gerents**.  
   - **Definir diferents nivells de granularitat entre la taula de dimensions i la taula de fets**.

7. **Considerar l'ETL innecessari**.  
   - **Moure l'ETL al** **_BI_**, **en comptes d'utilitzar la eina correcta de bon principi**.

8. **Considerar el DWH innecessari**.  
   - No utilitzar el **_DWH_** perquè la gent pensa que és innecessari.  
   - Prescindir del **_DWH_** i anar directament al disseny del **_DDM_** en la solució de **_BI_**.

## T'ha interessat l'article?

Si tens cap dubte amb relació al que s'ha exposat en aquest article, si us plau, fes-m'ho saber als comentaris.

Si t'ha agradat el contingut d'aquest article, deixa un comentari o un "m'agrada". A més, si consideres que és prou bo o pot ajudar altres usuaris de la xarxa, comparteix la publicació en aquesta plataforma. D'aquesta manera, tots junts podem democratitzar el coneixement i potser ajudar altres persones que ho necessitin.

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
- **img013**: Del curs "Data Warehousing i modelatge OLAP" de Platzi.

## Descàrrec de Responsabilitat

**_Platzi_** (abans conegut com Mejorando.la) és una plataforma d'educació en línia a Amèrica Llatina. Va ser fundada el 2011 per l'enginyer colombià **Freddy Vega** i el científic de la computació guatemalenc **Christian Van Der Henst**. ([Wikipedia](https://ca.wikipedia.org/wiki/Platzi))

## Ús de ChatGPT

**_Ús de ChatGPT 3.5_**

Aquest projecte ha estat verificat per ortografia, sintaxi i contingut utilitzant [**_ChatGPT 3.5_**](https://chat.openai.com/chat).  

Referència:  
[OpenAI. (2023). ChatGPT (versió del 27 de setembre de 2023) \[Large Language Model\]](https://chat.openai.com/chat)