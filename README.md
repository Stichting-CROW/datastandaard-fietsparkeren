# Datastandaard fietsparkeren

Dit document beschrijft het dataformaat van de Datastandaard Fietsparkeren. De eerste versie is een ontwerp, gebaseerd op het SPDP-formaat, dat beoogt voor zowel bewaakte stallingen als straattellingen te kunnen worden gebruikt.

### Body
| Field				| Type				| Required	| Description													|
| ----------------- | ----------------- | --------- | ------------------------------------------------------------- |
| query				| Object			| no		| In geval van GET-request: een object met de query parameters	|
| timestamp			| ISO8601 timestamp	| yes		| UTC timestamp van request										|
| survey			| Survey			| yes		| 																|
| staticData		| StaticData[]		| yes		| Statische data (sectienaam, adres, positie, ...)      		|
| dynamicData   	| DynamicSection[]	| yes		| Dynamische data (bezettingsdata, fietstellingen, ...)			|

### Survey Object
| Field				| Type				| Required	| Description													|
| ----------------- | ----------------- | --------- | ------------------------------------------------------------- |
| id				| string			| yes		| Een uuid, random of eventueel samengesteld					|
| area				| GeoJSON			| no		| GIS polygonen die het volledige onderzoeksgebied afbakenen. Zie https://en.wikipedia.org/wiki/GeoJSON	|
| client			| string			| no		| Opdrachtgever													|
| executor			| string			| no		| Uitvoerder													|
| startDate			| ISO8601 timestamp	| no		| Startdatum van het onderzoek									|
| endDate			| ISO8601 timestamp	| no		| Einddatum van het onderzoek									|

### StaticData object
| Field				| Type				| Required	| Description													|
| ----------------- | ----------------- | --------- | ------------------------------------------------------------- |
| sections			| StaticSections[]  | yes		|                                                               |

### StaticSection object
| Field                     | Type                | Required    | Description                                                |
| ------------------------- | ------------------- | ----------- | ---------------------------------------------------------- |
| id                        | string              | yes         | Een uuid, random of eventueel samengesteld                 |
| timestamp                 | ISO8601 timestamp   | conditional | Tijdstip van de meting. Alleen verplicht bij sectie type#1 (hoogste niveau) |
| geolocation				| GeoJSON			  | no		    | Positie van deze sectie									 |

### DynamicSection object
| Field                     | Type                | Required    | Description                                                |
| ------------------------- | ------------------- | ----------- | ---------------------------------------------------------- |
| id                        | string              | yes         | Een uuid, random of eventueel samengesteld                 |
| timestamp                 | ISO8601 timestamp   | conditional | Tijdstip van de meting. Alleen verplicht bij sectie type#1 (hoogste niveau) |
| surveyId                  | string		      | yes         | Id van de survey waartoe deze meting behoort               |
| source                    | string              | yes         | id van de instantie die deze data aangeleverd heeft		|
| parkingCapacity           | number              | no        | Totaal aantal plekken                                    |
| parkingCapacityTimestamp  | ISO8601 timestamp   | no        | Tijdstip van meting aantal plekken                       |
| space		                | Space Object		  | conditional | Alleen als de space homogeen is en als er geen sub |
| sections                  | DynamicSection[]    | no        | Verzameling van subsecties		                       |
|                           |                     |           |   Er zitten dus subsections in een subsection            |
|                           |                     |           |   Dit mag maximaal 3 lagen diep                          |
| notes                     | Note object         | no        | Notities over de meting in deze sectie                   |
| vacantSpaces              | number              | no        | Aantal vrije plekken                                     |
| occupiedSpaces            | number              | no        | Aantal bezette plekken                                   |
| occupation                | Occupation[]        | no        | Verzameling van Occupation-objecten                      |

### Note object
| Field               | Type                | Required               | Description                                                  |
| ------------------- | ------------------- | ---------------------- | ------------------------------------------------------------ |
| open                | boolean             | no                     | is deze area, bijv. een stalling geopend?                    |
| holiday             | boolean             | no                     | Vakantie?                                                    |
| event               | boolean             | no                     | Evenement (markt, kermis, ...)?                              |
| underConstruction   | boolean             | no                     | Werkzaamheden?                                               |
| remark              | string              | no                     | Vrij tekstveld                                               |
| ?                   | ?                   | no                     | Nader te bepalen                                             |

### Space object - definiëring van een plek aan de hand van properties
| Field                | Type               | Required                | Description                                                 |
| -------------------- | ------------------ | ----------------------- | ------------------------------------------------------------|
| type                 | number             | no                      | SpaceTypeID; tenminste 1 veld dient gegeven te zijn			|
| level                | number             | no                      | 0=onder, 1=boven                                            |
| vehicles             | Vehicle[]          | no                      | Uitsluitend geschikt voor deze voertuigen                   |
| ?                    | ?                  | no                      | Nieuw te definiëren plekeigenschappen                      	|
| ?                    | ?                  | no                      | Nieuw te definiëren plekeigenschappen                       |

### Count object
| Field                | Type               | Required               | Description                                                  |
| -------------------- | ------------------ | ---------------------- | ------------------------------------------------------------ |
| vehicle              | Vehicle object     | no                     | Leeg betekent dat het voertuigtype niet bekend is            |
| numberOfVehicles     | number             | yes                    | Aantal gestalde voertuigen                                   |

### Vehicle object
| Field                | Type               | Required               | Description                                                  |
| -------------------- | ------------------ | ---------------------- | ------------------------------------------------------------ |
| type                 | number             | no                     | Voertuigtype; Tenminste 1 veld dient gegeven te zijn         |
| propulsion           | number             | no                     | Aandrijving                                                  |
| state                | string             | no                     | Bijv.  'wrak', 'puncture'                                    |
| accessoires          | string[]           | no                     | Verzameling asseccoires, bijv: ['voorkrat', 'achterzitje', 'fietstassen'] |
| parkstate            | string[]           | no                     | Verzameling parkeerdetails, bijv: ['naast rek', 'dubbel in nietje']       |
| owner                | number             | no                     | Owner type            |
| ?                    | ?                  | no                     | Nieuw te definiëren plekeigenschappen                        |
| ?                    | ?                  | no                     | Nieuw te definiëren plekeigenschappen                        |

---

### sectionTypeIDs
| ID | Naam              | Omschrijving                                                          |
| -- | ----------------- | --------------------------------------------------------------------- |
| 1  | Gebied            | Een straat of een straatzijde; een stalling                           |
| 2  | Deelgebied        | Stoep, rijbaan, verdieping van stalling, ...                          |
| 3  | Voorziening       | Een rek, een verzameling nietjes, bromfietsvakken, ...                |
| 4  | Deelvoorziening   | Bovenrek, onderrek, ...                                               |

### spaceTypeIDs - indeling volgens Trajan [Whitepaper fietsparkeerdrukonderzoek](./Whitepaper_fietsparkeerdrukonderzoek_1.0.pdf), p. 10
| ID | spaceType             |
| -- | --------------------- |
| 0  | Buiten voorziening    |
| 1  | Rek                   |
| 2  | Nietjes               |
| 3  | Vak                   |
| 4  | Gemengd vak           |
| 5  | Bromfietsvak          |
| 6  | Voor fietsenwinkel    |
| 99 | Overig                |

### Voetuigeigenschappen volgens wettelijke voettuigcategorie, naar type aandrijving en naar breedte, zoals beschreven in [2019.09.24dataformaatfietstellingenv2.10_fietstypen](./2019.09.24dataformaatfietstellingenv2.10_fietstypen.pdf) pagina's 16 en 17

### vehicle.type
| ID | Voertuigtype          | Omschrijving                                                                   |
| -- | --------------------- | ------------------------------------------------------------------------------ |
| 1  | Fiets                 | Geen kenteken en geen verzekeringsplaatje                                      |
| 2  | Snorfiets             | kentekenplaat is blauw, met een wit kader, wit opschrift en een hologram       |
| 3  | Bromfiets             | kentekenplaat is geel, met een zwart kader, zwart opschrift en een hologram    |
| 4  | Motor                 | NL geel of blauw, internationaal anders                                        |
| 5  | Gehandicaptenvoertuig | geen kenteken, wel verzekeringsplaatje                                         |
| 99 | Overig                |                                                                                |

### vehicle.owner
| ID | Eigenaar              | Omschrijving                                                                   |
| -- | --------------------- | ------------------------------------------------------------------------------ |
| 1  | Privé                 | Privéfiets                                                                     |
| 2  | Lease                 | Leasefiets, zoals Swap Bikes                                                   |
| 3  | Huur                  | Huurfiets, zoals OV-fiets                                                      |

### vehicle.propulsion
| ID | Aandrijving           | Omschrijving                                                                   |
| -- | --------------------- | ------------------------------------------------------------------------------ |
| 1  | Spierkracht           | bv traditionele fiets of voetganger                                            |
| 2  | Elektrische hulpmotor | bv e-fiets, speed pedelec                                                      |
| 3  | Alleen elektrisch     | bv e-bromfiets                                                                 |
| 4  | Brandstof hulpmotor   | bv Sparta-met                                                                  |
| 5  | alleen brandstof      | bv traditionele bromfiets                                                      | 

---

De velden vacantSpaces, occupiedSpaces en occupation zijn alleen aanwezig aan de bladeren van de sectieboom 

De vacantSpaces van een section kan bepaald worden door alle vacantSpaces van onderliggende sections op te tellen

---
# API 3 - schrijfopdrachten van exploitant naar dataportal

## Nieuwe data opslaan (POST)
### Metingen groeperen in 1 onderzoek (= 'survey')  
#### Stap 1: meld je onderzoek (survey) aan, eventueel voorzien van data  
POST /surveys [Body](./1_POST_new_survey.json)  
Een id van de survey mag door exploitant zelf gekozen worden. Suggestie: <postcode>_<source>_<date> => 1000_DeFietsTellers_2020.  
Indien geen surveyId gegeven, dan maakt de server zelf een id en geeft deze terug in de response.  

#### Stap 2: Stuur nieuwe data in op bestaande survey  
POST /surveys/:surveyId [Body](./API3/2_POST_update_survey.json)
als er nog geen onderzoek met gegeven surveyId bestaat, dan wordt deze aangemaakt  

### Losse metingen, dus geen deel van een onderzoek
POST /sections [Body](./API3/2_POST_sections.json)


### Voorbeelden:
Stuur  bezettingsdata voor een eenvoudige sectie: alleen capaciteit en bezetting zijn bekend


Stuur realtime bezettingsdata voor een eenvoudige sectie: alleen capaciteit en bezetting zijn bekend
POST /surveys/:surveyId [Body](./API3/2_POST_simple_sections.json)

Stuur realtime bezettingsdata voor een eenvoudige sectie: alleen capaciteit en bezetting zijn bekend
POST /surveys/:surveyId [Body](./API3/2_POST_simple_sections.json)


## zoekopdrachten (GET)
### query-parameters voor GET-requests
| param     		| type		| values                                             	|
| ----------------- |---------- | ----------------------------------------------------- |
| depth 		    | number	| Aantal te bevragen sectie-lagen vanaf gegeven pad	    |
|					| 			|  In geval van bevraging vanaf sectieType=1			|
|					| 			| 1 = sectieType = 1                   					|
|					|		 	| 2 = sectieType = 1 en 2             				    |
|					| 		 	| 2 = sectieType = 1, 2 en 3           					|
|					| 		 	| 4 = de volledige boom                      			|
|					| 		 	| default = 1                           				|
|					|			|														|
| vehicleType		| number	| Alleen data voor dit voertuigtype						|
| vehiclePropulsion | number	| Alleen data voor dit voertuig met deze aandrijving	|
| vehicleOwner	 	| number	| Alleen data voor dit voertuig met deze eigenaar		|
|					|			| default = alle voertuigen 							|
|					|			|														|
| startDate			| UTC timestamp	| Selectie op timestamp. Area.timestamp >= startdate 	|
| endDate			| UTC timestamp	| Selectie op timestamp. Area.timestamp < startdate		|
| groupBY			| string	| Lijst van kenmerken waarop de data gegroepeerd wordt		|

### Ophalen van alle data van een bepaald onderzoek
GET /surveys/:surveyId?depth=4 [Response](./GET_survey.json)  

### Ophalen van data van een bepaald onderzoek op area-niveau
GET /surveys/:surveyId [Response](./GET_survey.json) - de default-waarde voor depth = 1, dus daarom wordt alle data platgeslagen op area-niveau  

### Opvragen van data van een bepaalde area
/sections/ketelstraat_oneven/?startDate=2020-11-23T0:00:00&endDate=2020-11-24T0:00:00 [Response](./GET_section.json)  

### Opvragen van data van een bepaalde area, uitgesplitst op type voertuig
/sections/ketelstraat_oneven/?startDate=2020-11-23T0:00:00&endDate=2020-11-24T0:00:00&groupBy=vehicleType [Response](./GET_groupby.json)  

### Opvragen van data van een section binnen een section (bijvoorbeeld de data van de op de stoep gestalde fietsen)
/sections/ketelstraat_oneven/trottoir?depth=3&startDate=2020-11-23T0:00:00&endDate=2020-11-24T0:00:00 [Response](./GET_subsection.json)  

### Selectie op vehicle: alle data voor een area over gewone fietsen
/sections/ketelstraat_oneven/?vehicleType=1&startDate=2020-11-23T0:00:00&endDate=2020-11-24T0:00:00 [Response](./GET_fiets.json)  

### Selectie op vehicle: alle data voor een area over elektrische fietsen
/sections/ketelstraat_oneven/?vehicleType=1&vehiclePropulsion=2&startDate=2020-11-23T0:00:00&endDate=2020-11-24T0:00:00 [Response](./GET_elekfiets.json)  
