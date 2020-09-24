# Datastandaard fietsparkeren

Dit document beschrijft het dataformaat van de Datastandaard Fietsparkeren. De eerste versie is een ontwerp, gebaseerd op het SPDP-formaat, dat beoogt voor zowel geautomatiseerde tellingen in bewaakte stallingen als incidentele straattellingen te kunnen worden gebruikt.

Het door CROW gepubliceerde [schematische overzicht van de verschillende datastromen](/diagram_crow_api_overzicht.png) in het fietsparkeren onderscheidt een zestal API's:

| ID | Omschrijving |
| -- | ------------------------------------------------------------------------------ |
| 1  | Van stalling naar exploitant |
| 2  | Van exploitant naar MaaS-provider |
| 3  | Van exploitant / straatteller naar dataportal  |
| 4  | Van dataporal naar analist |
| 5  | Van dataportal naar webapplicaties | 
| 6  | Van exploitant naar exploitatietools | 

Onderstaand ontwerp is in eerste instantie bedoeld voor de API's 3, 4 en 5, maar kan ook als leidraad dienen voor de overige datastromen.

## Indeling

* __1. Omschrijving datastandaard__: de datastandaard per object uitgeplozen en uitgelegd
* __2. Beveiliging__: 
* __3. API Requests__: geeft een aantal praktijkvoorbeelden voor de API's 3 (POST-requests) en 4 (GET-requests). De objecten uit hoofstuk 1 worden hier in JSON-format gebruikt.

---

## 1. Omschrijving datastandaard
De data in de datastandaard valt uiteen in drie hoofdblokken:
1. data over het onderzoek (survey)
2. statische data (static data): gegevens over de meetgebieden (sections): id, naam en geografische afbakening. Deze data verandert zelden of nooit. 
3. dynamische data (dynamische data): tellingen en metingen in de meetgebieden

De velden en zoekfuncties die in deze datastandaard zijn opgenomen, __moeten__ worden ondersteund door een dataportal. Het staat de dataportals vrij om zelf extra datavelden en extra functionaliteit te bieden.


| Field				| Type				| Required	| Description													|
| ----------------- | ----------------- | --------- | ------------------------------------------------------------- |
| survey			| Survey			| no		| Gegevens over het onderzoek																|
| staticData		| StaticData		| no		| Statische data (sectienaam, adres, positie, ...)      		|
| dynamicData   	| DynamicData   	| no		| Dynamische data (bezettingsdata, fietstellingen, ...)			|

---

### 1.1 Survey
Het datablok Survey bevat data over het onderzoek. Geïnitieerd door wie? Uitgevoerd door wie? Wanneer? Waar? Al deze informatie kan worden ingestuurd, maar is niet verplicht.

Bij het insturen van een Survey kan de dataportal ervoor kiezen de gebruiker zijn eigen surveyId te laten kiezen. Als dat het geval is, dient er een 400-error (Bad request) gegenereerd te worden als er een surveyId worden gekozen dat al bestaat. In geval de inzender ook de inzender is van de bestaande survey, kan er een update plaatsvinden. Ondersteuning van deze functionalitet is door het dataportal zelf in te bepalen.

Het SurveyID kan gebruikt worden om data van verschillende secties en bronnen te groeperen door dynamische data te labelen met het veld surveyId. Zie voor meer details het kopje Dynamic Data

#### Survey - gegevens over een onderzoek
| Field				| Type				| Required	| Description													|
| ----------------- | ----------------- | --------- | ------------------------------------------------------------- |
| id				| string			| no		| Een uuid, random of eventueel samengesteld. Indien bij een POST-request niet gegeven, dan maakt de dataportal zelf een ID en geeft deze terug in de respons |
| area				| GeoJSON			| no		| GIS polygonen die het volledige onderzoeksgebied afbakenen. Zie https://en.wikipedia.org/wiki/GeoJSON	|
| authority			| Organization		| no		| Opdrachtgever													|
| contractors		| Organization[]	| no		| Bronhouders	=> andere term. Bronhouder? Bekijk document datastandaard fietsttellingen |
| startDate			| ISO8601 timestamp	| no		| Startdatum van het onderzoek									|
| endDate			| ISO8601 timestamp	| no		| Einddatum van het onderzoek									|

#### Organization - gegevens over een opdrachtgever of een uitvoerende instatie
| Field				| Type				| Required	| Description													|
| ----------------- | ----------------- | --------- | ------------------------------------------------------------- |
| id				| string			| yes		| Unieke id, bijv. CBS-code gemeente. Kan worden gebruikt in query's |
| name				| string			| no		| Naam                                             	|

---

### 1.2 Static Data
Statische data is data over een sectie die niet of nauwelijks aan verandering onderhevig is. Dat is bijvoorbeeld het geval bij de geografische afbakening. 
Mocht het zo zijn dat de geografische afbakening wijzigt, dan verdient het aanbeveling een nieuwe statische sectie aan te maken. Aanpassingen van bestaande secties gelden namelijk ook voor reeds inegstuurde data, wat kan leiden tot foutieve bepalingen van bijvoorbeeld de relatieve bezetting.
Of en hoe een statische sectie gewijzigd kan worden, valt buiten het bestek van deze standaard en wordt overgelaten aan de dataportals.
  
N.B.: de parkeercapaciteit van een sectie lijkt op het eerste gezicht statisch. Toch is deze op advies van telinstanties niet opgenomen in de statische data. Wegwerkzaamheden en  herindelingen van straten hebben te vaak invloed op de parkeercapaceit van straatsecties. In bewaakte stallingen kan het voorkomen dat bepaalde secties gedurende bepaalde periodes gereserveerd zijn.

#### StaticData
| Field				| Type				| Required	| Description													|
| ----------------- | ----------------- | --------- | ------------------------------------------------------------- |
| sections			| StaticSection[]   | yes		|                                                               |

#### StaticSection
| Field                     | Type                | Required    | Description                                                |
| ------------------------- | ------------------- | ----------- | ---------------------------------------------------------- |
| id                        | string              | yes         | Een uuid, random of eventueel samengesteld                 |
| timestamp                 | ISO8601 timestamp   | no          | Tijdstip van de vaststelling sectie                        |
| geoLocation				| GeoJSON			  | no		    | Positie van deze sectie. Kan gebruikt worden voor geo-zoekopdrachten |


### 1.3 Dynamic Data
Dynamische data, oftewel: de tellingen, is uiteraard waar het in deze datastandaard om te doen is. Dynamische data is een verzameling secties/meetgebieden. In een meetgebied kunnen weer subsecties worden ondergebracht en in de subsecties weer nieuwe subsecties. Zo ontstaat er een boom aan secties, die in de onderstaande tabellen 'sectieboom' wordt genoemd. Secties zonder subsecties, dus de uiteinden van de sectieboom, hetem 'bladeren'. Zo kunnen een straat worden opgedeeld in linker- en rechterzijde met aan elke kant diverse parkeervoorzieningen. 

Een sectieboom kan er als volgt uit zien:  
Sectie (Dorpsstraat)
  Subsectie (Even zijde)
    Subsubsectie (Rek) => dit is een blad  
    Subsubsectie (Verzameling nietjes) => dit is een blad  
  Subsectie (Oneven zijde)
    Subsubsectie (gevel) => dit is een blad  

Om de ontwikkeling van een dataportal niet te complex te maken, is het aantal secties in secties voorlopig begrensd op 3 lagen.  

__Belangrijk: Er mag alleen gedetailleerde teldata, verpakt in zogenaamde 'Count'-objecten, worden doorgegeven in de bladeren van de sectieboom!!!__ 

Zie document [Dynamische data in de fietsparkeerstandaard](https://github.com/Stichting-CROW/datastandaard-fietsparkeren/blob/master/Dynamische_data_in_de_fietsparkeerstandaard.pdf) voor een beknopte a-technische uitleg van het telprincipes in de datastandaard  

Hieronder volgt een opsomming van de te gebruiken datablokken. Voor de niet technici: een setje dubbele haken [ ] betekent dat het gaat om een verzameling van meerdere objecten.
Bijvoorbeeld: Count[] betekent dat er meerdere telblokken in dit veld kunnen zitten.

#### DynamicData
| Field				| Type				| Required	| Description													|
| ----------------- | ----------------- | --------- | ------------------------------------------------------------- |
| sections			| DynamicSection[]   | yes		|                                                               |

#### DynamicSection
| Field                     | Type                | Required    | Description                                                |
| ------------------------- | ------------------- | ----------- | ---------------------------------------------------------- |
| id                        | string              | yes         | Een uuid, random of eventueel samengesteld                 |
| timestamp                 | ISO8601 timestamp   | conditional | Tijdstip van de meting. Alleen verplicht in de stam van de sectieboom |
| surveyId                  | string		      | conditional | Id van de survey waartoe deze meting behoort |
| authorityId               | string		      | conditional | Id van de opdrachtgever van deze meting |
| contractorId              | string              | conditional | Id van de instantie die deze data aangeleverd heeft. Bij een POST-request dient dit door het dataportal gevuld te worden met de username van de inzender |
| parkingCapacity           | number              | conditional | Totaal aantal plekken, verplicht in de bladeren van de sectieboom           |
| parkingCapacityTimestamp  | ISO8601 timestamp   | no          | Tijdstip van meting aantal plekken                       |
| vacantSpaces              | number              | conditional | Aantal vrije plekken, verplicht in de bladeren van de sectieboom    |
| occupiedSpaces            | number              | conditional | Aantal bezette plekken, verplicht in de bladeren van de sectieboom  |
| count                     | Count[]             | no          | Verzameling van Count-objecten                      |
| space		                | Space     		  | no          | Alleen toegestaan in de bladeren van de sectieboom  |
| sections                  | DynamicSection[]    | no        | Verzameling van subsecties		                       |
|                           |                     |           |   Er zitten dus subsections in een subsection            |
|                           |                     |           |   Dit mag maximaal 3 lagen diep                          |
| notes                     | Note                | no        | Notities over de meting in deze sectie                   |

De velden surveyId, authorityId en contractorId kunnen gebruikt worden bij het filteren van data bij de zoekopdrachten van de API's 4 en 5.
Je zou bijvoorbeeld kunnen alle metingen van een bepaald onderzoek die zijn uitgevoerd door een bepaalde contractor kunnen opvragen. Zie *API Requests* voor meer details over zoekopdrachten.


### Space - definiëring van een plek aan de hand van properties
| Field                | Type               | Required                | Description                                                 |
| -------------------- | ------------------ | ----------------------- | ------------------------------------------------------------|
| type                 | string             | no                      | SpaceTypeID; tenminste 1 veld dient gegeven te zijn			|
| level                | number             | no                      | 0=onder, 1=boven                                            |
| vehicles             | Vehicle[]          | no                      | Uitsluitend geschikt voor deze voertuigen                   |

### Count 
| Field                | Type               | Required               | Description                                                  |
| -------------------- | ------------------ | ---------------------- | ------------------------------------------------------------ |
| vehicle              | Vehicle            | no                     | Niet gegeven of leef object betekent dat het voertuigtype niet bekend is |
| numberOfVehicles     | number             | yes                    | Aantal gestalde voertuigen                                   |

### Vehicle 
| Field                | Type               | Required               | Description                                                  |
| -------------------- | ------------------ | ---------------------- | ------------------------------------------------------------ |
| type                 | string             | no                     | Zie tabel vehicle.type                                       |
| propulsion           | string             | no                     | Zie tabel vehicle.propulsion                                 |
| state                | string[]           | no                     | Zie tabel vehicle.state                                      |
| parkState            | string[]           | no                     | Zie tabel vehicle.parkState                                  |
| accessoires          | string[]           | no                     | Zie tabel vehicle.accessoire                                 |
| owner                | string             | no                     | Zie tabel vehicle.owner                                      |

### Note
| Field               | Type                | Required               | Description                                                  |
| ------------------- | ------------------- | ---------------------- | ------------------------------------------------------------ |
| open                | boolean             | no                     | is deze area, bijv. een stalling geopend?                    |
| holiday             | boolean             | no                     | Vakantie?                                                    |
| event               | boolean             | no                     | Evenement (markt, kermis, ...)?                              |
| underConstruction   | boolean             | no                     | Werkzaamheden?                                               |
| remark              | string              | no                     | Vrij tekstveld                                               |

---
Onderstaande lijstjes geven de mogelijk waarden die voor diverse velden mogelijk zijn. Lijstjes die eindigen met ... zijn voor uitbreiding vatbaar.

### spaceTypeIDs - indeling volgens Trajan [Whitepaper fietsparkeerdrukonderzoek](https://github.com/Stichting-CROW/datastandaard-fietsparkeren/blob/master/20190924-dataformaat-fietstellingen-v2-10.pdf), p. 10
| ID | spaceType             |
| -- | --------------------- |
| x  | Buiten voorziening    |
| r  | Rek                   |
| n  | Nietjes               |
| v  | Vak                   |
| vf | Fietsvak              |
| vb | Bromfietsvak          |
| vfb| Gemengd vak           |
| w  | Voor fietsenwinkel    |
| a  | Anders                |

### vehicle.accessoires 
| ID | Omschrijving          |
| -- | --------------------- |
| z  | zitje                 |
| zv | voorzitje             |
| za | achterzitje           |
| t  | fietstas              |
| tv | fietstas voor         |
| ta | fietstas achter       |
| r  | rek                   |
| rv | rek voor              |
| rv | rek achter            |
| b  | bak / mand            |
| bv | bak voor              |
| ba | vak achter            |
| ...| ...                   |

### vehicle.state 
| ID | Omschrijving          |
| -- | --------------------- |
| w  | wrak                  |
| l  | lekke band            |
| lv | lekke band voor       |
| la | lekke band achter     |
| z  | zonder zadel          |
| ...| ...                   |

### vehicle.parkState
| ID | Omschrijving          |
| -- | --------------------- |
| n  | naast voorziening     |
| d  | dubbel                |
| ...| ...                   |
.
Voetuigeigenschappen volgens wettelijke voettuigcategorie, naar type aandrijving en naar breedte, zoals beschreven in [2019.09.24dataformaatfietstellingenv2.10_fietstypen](./2019.09.24dataformaatfietstellingenv2.10_fietstypen.pdf) pagina's 16 en 17
.
### vehicle.type
| ID | Voertuigtype          | Omschrijving                                                                   |
| -- | --------------------- | ------------------------------------------------------------------------------ |
| f  | Fiets                 | Geen kenteken en geen verzekeringsplaatje                                      |
| c  | Bakfiets              | Fiets met bak                                                                  |
| s  | Snorfiets             | kentekenplaat is blauw, met een wit kader, wit opschrift en een hologram       |
| b  | Bromfiets             | kentekenplaat is geel, met een zwart kader, zwart opschrift en een hologram    |
| m  | Motorfiets            | NL geel of blauw, internationaal anders                                        |
| g  | Gehandicaptenvoertuig | driewieler, scootmobiel, rolstoel, etc                                         |
| a  | Anders                |                                                                                |

### vehicle.propulsion: s=spierkracht, b=brandstofmotor, e=elektrische motor
| ID | Aandrijving            | Omschrijving                                                                   |
| -- | ---------------------- | ------------------------------------------------------------------------------ |
| s  | Spierkracht            | bv traditionele fiets of voetganger                                            |
| se | Elektrische hulpmotor  | bv e-fiets, speed pedelec                                                      |
| e  | Alleen elektrisch      | bv e-bromfiets                                                                 |
| sb | Brandstof hulpmotor    | bv Sparta-met                                                                  |
| b  | alleen brandstof       | bv traditionele bromfiets, motorfiets                                          | 

### vehicle.owner
| ID | Eigenaar              | Omschrijving                                                                   |
| -- | --------------------- | ------------------------------------------------------------------------------ |
| p  | Privé                 | Privéfiets                                                                     |
| l  | Lease                 | Leasefiets, zoals Swap Bikes                                                   |
| h  | Huur                  | Huurfiets, zoals OV-fiets                                                      |

---

De velden parkingCapacity, vacantSpaces en occupiedSpaces en count zijn verplicht in de bladeren van de sectieboom  

Indien ze in de takken niet gegeven zijn, kunnen vacantSpaces, occupiedSpaces en occupation berekend worden door alle vacantSpaces, etc. van onderliggende sections op te tellen

---

## 2. Beveiliging API
Voor het insturen van data zijn credentials vereist, die bij de beheerders van het dataportal verkregen kunnen worden.
Om discussies over de voor- en nadelen van diverse authenticatie-protocollen te voorkomen, schrijft de datastandaard niet voor op welke manier deze beveiliging uitgevoerd dient te worden.  

Dataportals kunnen er ook voor kiezen om bepaalde data, query's of andere functionaliteit voor te behouden een geautoriseerde gebruikers. Ook hierin schrijft de datastandaard niets voor.  

---

## 3. API- requests

### API 3 - data van stallingsexploitant of fietsteller opslaan in dataportal
API 3 is de ontvangde zijde van de dataportal API. Straattellers of stallingssoftware kunnen met POST-requests hun data opsturen naar het dataportal.
In de body's van deze POST-requests dienen ze gebruik te maken van boven beschreven objecten.  

De datastandaard schrijft voor dat data in elk geval in afzonderlijke blokken Survey, Static Data en Dynamic Data opgeslagen dient te worden. Er zijn dus tenminste 3 POSTs nodig om een volledige Survey, inclusief data op te slaan.

Alle genoemde requests beginnen met een /. Voor deze schuine streep komt uiteraard de base-url van de API. In het geval van de pilot in VeiligStallen is de base-url https://remote.veiligstallen2.nl/rest/api

### Metingen groeperen in 1 onderzoek (= 'survey')  
#### Stap 1: meld je onderzoek (survey) aan
`POST /surveys` [Body](./examples/API3/requests/POST_new_survey_with_id.json)  
Een id van de survey mag door exploitant zelf gekozen worden. Suggestie gebruik [CBS codes](https://www.cbs.nl/nl-nl/onze-diensten/methoden/classificaties/overig/gemeentelijke-indelingen-per-jaar/indeling-per-jaar/gemeentelijke-indeling-op-1-januari-2020) en unieke gegevens uit het onderzoek, bijv. < CBS_nr_gemeente >_< jaartal > => 0202_2020.  

`POST /surveys` [Body](./examples/API3/requests/POST_new_survey_without_id.json)  

De instantie die de survey instuurt, wordt 'eigenaar' van dit onderzoek. 

Een Survey met een id dat al bestaat, zal resulteren in een 400-error. Behalve als deze POST wordt gedaan door de eigenaar van het onderzoek. In dat geval wordt er een update van het onderzoek uitgevoerd.

Indien geen surveyId is gegeven, maakt de server zelf een id en geeft deze terug in de response.  

[Response](./examples/API3/responses/POST_new_survey.json)  

#### Stap 2: koppel statische data aan bestaand onderzoek
`POST /staticdata` [Body](./examples/API3/requests/POST_static_data.json)  

Door het veld surveyId mee te geven aan de secties, worden de secties gekoppeld aan een onderzoek.  
Zonder het veld surveyId worden de statische secties niet gekoppeld aan een onderzoek en zullen ze dus ook niet gevonden worden bij een zoekopdracht naar een surveyId.

Het Id van een statische sectie is, net als het surveyID, door de opsturende instantie zelf samen te stellen. Suggestie: prefix het sectionId met het surveyId om een unieke id te garanderen, bijvoorbeeld: < surveyId >_< straatnaam >

### Stap 3: Sla losse metingen op
`POST /dynamicdata` [Body](./examples/API3/requests/POST_dynamic_data.json)

Door het veld surveyId mee te geven aan de secties, worden de metingen gekoppeld aan een onderzoek.  
Zonder het veld surveyId worden de statische secties niet gekoppeld aan een onderzoek en zullen ze dus ook niet gevonden worden bij een zoekopdracht naar een surveyId.

Je kunt dus zowel dynamische als statische data afzonderlijk koppelen aan een onderzoek.   
.   
Dynamische data kan sterk gecomprimeerd worden door alleen de totalen op te slaan. Dit zal in de praktijk vaak voorkomen.  
[Body met een alleen de totalen](./examples/API3/requests/POST_compressed_dynamic_section.json)  


### API 4 - data opvragen

__Filter op diepte__
Gedetailleerde informatie over de bezetting geeft behoorlijk grote bomen, die niet voor iedere analist van deze data even interessant is. Als deze analist de waarnemingen van een stationsstalling door de tijd opvraagt, kunnen de responsen enorm groot worden. Als 90% van de data voor de analist niet relevant is, is dit natuurlijk onzinnig.
Daarom stelt de datastandaard een beperkt aantal zoekfunctionaliteiten verplicht. De belangrijkste van deze is dat je de mate van detail, de diepte (depth), bij een zoekopdracht kunt  aangeven aan de API. 
Stel dat in het dataportal de meest gedetailleerde boom is opgeslagen:  
 
Als een analist alleen geïnteresseerd is in de bezetting van de totale stalling, vraagt hij data op op diepte 1. De API geeft hem dan dit resultaat:  
 
Als een stalling iedere 2 minuten data opslaat en een analist vraagt de data op van een hele maand, scheelt dat uiteraard enorm in de datastroom en daarmee in de performance van de API.  

__Filter op inhoud__
Om data-analisten te helpen hun weg te vinden in de gestaag groeiende data van een dataportal, stelt de datastandaard een aantal zoekfunctionaliteiten verplicht. Zo moet een er minimaal gefilterd kunnen worden op:  
* Data binnen een bepaalde tijdspanne (startDate, endDate)
* Data van een bepaalde secties (sectionId)
* Data van een bepaald onderzoek (surveyId)
* Data van een bepaalde opdrachtgever (authorityId)
* Data van een bepaalde dataleverancier (contractorId)
* Data binnen een bepaalde geo-polygoon of geo-punt + radius

Deze filters moeten met elkaar gecombineerd kunnen worden.  
  
De praktijk moet uitwijzen of deze lijst voldoende is om aan alle wensen van de data-analisten te voldoen. Indien nodig zullen er meer zoekfuncties aan deze lijst worden toegevoegd.  

__Filter op tijdspanne__  
Gebruik bij het zoeken de url-parameters startDate en endData. Tijdstippen worden altijd doorgegeven in ISO8601 timestamp formaat.  
Dus:  
`?startDate=2020-01-01T0:00:00&endDate=2020-02-01T0:00:00`  

__Filter op sectie__   
Als een analist alleen data wil van een bepaald fietstype, bijvoorbeeld gewone fietsen:  
`?sectionID=:sectionID`  
Of meerdere secties:  
`?sectionID=sectionID1,sectionID2,sectionID3`  

__Filter op onderzoek__  
Data die ingestuurd wordt door een dataleverancier kan worden voorzien van een onderzoeksID (surveyID). Als deze ID gegeven is, is het uiteraard mogelijk hierop te filteren:  
`?surveyID=:surveyID`  

__Filter op opdrachtgever__  
Hetzelfde idee al filteren op onderzoek: als de data voorzien is van de ID van een opdrachtgever, kan hierop gefilterd worden:  
`?authorityID=: surveyID`  

__Filter op dataleverancier__  
De dataportal kan de data die wordt ingestuurd voorzien van de ID van de leverancier van deze data. Als dat gebeurt, kan er uiteraard gezocht worden op dit ID:  
`?contractorID=:contractorId`  

__Filter op geo-data__
Als de statische data van secties is voorzien van exacte geo-coördinaten, moeten deze secties gevonden kunnen worden aan de van een geo-zoekopdracht.  
Zoek op een punt + straal van dit punt in meters:  
* zoeken op secties die de gegeven cirkel overlappen: relation=intersect. Dit is de default.  
`?geopoint=52.370216,4.895168,1000&relation=intersect`  
* zoeken op secties die geheel binnen de gegeven cirkel overlappen: relation=within.  
`?geopoint=52.370216,4.895168,1000&relation=within`  

__Filter op polygoon__
Geef een polygoon mee in de query, aan de hand waarvan de corresponderende secties worden gezocht.  
Er zijn twee manieren voor deze query:  
* zoeken op secties die de gegeven polygoon overlappen: relation=intersect. Dit is de default.  
`?geopolygon=52.370216,4.895168,53.370216,4.895168,53.370216,5.895168,52.370216,4.895168&relation=intersect`  
* zoeken op secties die geheel binnen de gegeven polygoon overlappen: relation=within.  
`?geopolygon=52.370216,4.895168,53.370216,4.895168,53.370216,5.895168,52.370216,4.895168&relation=within`  


### Een overzicht van alle query parameters
| param     		| type		| values                                             	|
| ----------------- |---------- | ----------------------------------------------------- |
| surveyId			| string	| Alleen data van dit onderzoek    						|
| authorityId      	| string	| Alleen data van deze opdrachtgever         			|
| contarctorId  	| string	| Alleen data van deze dataleverancier      			|
| data  			| string	| survey, static en/of dynamic (default)               	|
| depth 		    | number	| Aantal te bevragen sectie-lagen vanaf gegeven pad	    |
|					| 		 	| default = 1                           				|
|					|			|														|
| startDate			| UTC timestamp	| Selectie op timestamp. Section.timestamp >= startDate 	|
| endDate			| UTC timestamp	| Selectie op timestamp. Section.timestamp <= endDate    	|
|					|			|														|
| geopoint  	    | list met coördinaten en straal | lat, lng, radius (in meters)	|
| geopolygon		| list met coördinaten | lat1,lng1,lat2,lng2,lat3,lng3,...,...,lat1,lng1	|
| relation        	| string    | 'intersect' (default) of 'within'	|

----

### Enkele voorbeelden

#### Ophalen van alle data van een bepaald onderzoek
`GET ?surveyId=0202_2020&depth=4&data=survey,static,dynamic`  
[Response](./examples/API4/GET_survey_static_dynamic.json)  

#### Ophalen van de statische data van een bepaald onderzoek
`GET ?surveyId=0202_2020&data=static`  
[Response](./examples/API4/GET_static.json)    

#### Ophalen van de volledige dynamische data van een bepaald onderzoek
`GET ?surveyId=0202_2020&depth=4`  
[Response](./examples/API4/GET_dynamic_depth4.json)  

#### Ophalen van de beknopte dynamische data van een bepaald onderzoek
`GET ?surveyId=0202_2020`  
[Response](./examples/API4/GET_dynamic_depth1.json) - de default-waarde voor depth = 1, dus daarom wordt alle data platgeslagen op de wortel van de sectieboom.  
Alleen dynamische data, want de parameter *data* is niet gegeven en de defaultwaarde is 'dynamic'.  

#### Opvragen van data van een bepaalde sectie in een gegeven periode
`GET ?sectionId=arnhem_ketelstraat_oneven&startDate=2020-11-23T0:00:00&endDate=2020-11-24T0:00:00`   
[Response](./examples/API4/GET_dynamic_depth1_single_section.json)  

#### Opvragen van statische data in een bepaald gebied, in een cirkel met straat 1km vanaf een gegeven punt
`GET ?geopoint=5.90802,51.98173,1000&data=static` 
[Response](./examples/API4/GET_static.json)  

#### Opvragen van statische data in een bepaald gebied
`GET ?geopolygon=52.370216,4.895168,53.370216,4.895168,53.370216,5.895168,52.370216,4.895168&relation=within&data=static`   
[Response](./examples/API4/GET_dynamic_depth1.json)  
*relation=within* geeft aan dat de gevonden secties zich volledig in de polygoon moeten bevinden  

### API 5 - realtime data lezen vanuit dataportal voor t.b.v. webapplicaties
Gebruikers van API 5 - de datastroom tussen het dataportal en de webapplicaties - zijn vooral geïnteresseerd in realtime data. Per secties dus slechts één resultaat. Door *latest* op te nemen in de url weet de API dat het om een dergelijk request gaat.  

#### Ophalen van de huidige bezetting van een bepaalde sectie tot op voorzieningsnivea
`GET /latest?sectionId=arnhem_ketelstraat_oneven&depth=4`   
[Response](./examples/API5/GET_dynamic_depth4.json)  

#### Opvragen van beknopte data (depth=1) van een bepaalde sectie
`GET /latest?sectionId=arnhem_ketelstraat_oneven` [Response](./examples/API5/GET_dynamic_depth1_single_section.json)  

#### Opvragen van data in een bepaald gebied, in een cirkel met straal 200m vanaf een gegeven punt
`GET /latest?geopoint=5.90802,51.98173,200&data=static` 
[Response](./examples/API5/GET_dynamic_depth1.json)  

