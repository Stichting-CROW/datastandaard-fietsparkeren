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
| client			| Company			| no		| Opdrachtgever													|
| providers			| Company[]			| no		| Uitvoerders													|
| startDate			| ISO8601 timestamp	| no		| Startdatum van het onderzoek									|
| endDate			| ISO8601 timestamp	| no		| Einddatum van het onderzoek									|

### Company Object
| Field				| Type				| Required	| Description													|
| ----------------- | ----------------- | --------- | ------------------------------------------------------------- |
| id				| string			| yes		| Unieke id, bijv. postcode gemeente        					|
| name				| string			| no		| Naam van de klant                                         	|
| address	    	| string			| no		| Adres van de klant                                         	|
| city  	    	| string			| no		| Plaats van de klant                                         	|

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
| providerId                | string              | yes         | id van de instantie die deze data aangeleverd heeft		|
| parkingCapacity           | number              | no        | Totaal aantal plekken                                    |
| parkingCapacityTimestamp  | ISO8601 timestamp   | no        | Tijdstip van meting aantal plekken                       |
| space		                | Space     		  | conditional | Alleen als de space homogeen is en als er geen sub |
| sections                  | DynamicSection[]    | no        | Verzameling van subsecties		                       |
|                           |                     |           |   Er zitten dus subsections in een subsection            |
|                           |                     |           |   Dit mag maximaal 3 lagen diep                          |
| notes                     | Note                | no        | Notities over de meting in deze sectie                   |
| vacantSpaces              | number              | no        | Aantal vrije plekken                                     |
| occupiedSpaces            | number              | no        | Aantal bezette plekken                                   |
| occupation                | Count[]          | no        | Verzameling van Occupation-objecten                      |

### Note object
| Field               | Type                | Required               | Description                                                  |
| ------------------- | ------------------- | ---------------------- | ------------------------------------------------------------ |
| open                | boolean             | no                     | is deze area, bijv. een stalling geopend?                    |
| holiday             | boolean             | no                     | Vakantie?                                                    |
| event               | boolean             | no                     | Evenement (markt, kermis, ...)?                              |
| underConstruction   | boolean             | no                     | Werkzaamheden?                                               |
| remark              | string              | no                     | Vrij tekstveld                                               |

### Space object - definiëring van een plek aan de hand van properties
| Field                | Type               | Required                | Description                                                 |
| -------------------- | ------------------ | ----------------------- | ------------------------------------------------------------|
| type                 | string             | no                      | SpaceTypeID; tenminste 1 veld dient gegeven te zijn			|
| level                | number             | no                      | 0=onder, 1=boven                                            |
| vehicles             | Vehicle[]          | no                      | Uitsluitend geschikt voor deze voertuigen                   |

### Count object
| Field                | Type               | Required               | Description                                                  |
| -------------------- | ------------------ | ---------------------- | ------------------------------------------------------------ |
| vehicle              | Vehicle            | no                     | Leeg betekent dat het voertuigtype niet bekend is            |
| numberOfVehicles     | number             | yes                    | Aantal gestalde voertuigen                                   |

### Vehicle object
| Field                | Type               | Required               | Description                                                  |
| -------------------- | ------------------ | ---------------------- | ------------------------------------------------------------ |
| type                 | string             | no                     | Zie tabel vehicle.type                                       |
| propulsion           | string             | no                     | Zie tabel vehicle.propulsion                                 |
| state                | string             | no                     | Bijv.  'wrak', 'puncture'                                    |
| accessoires          | string[]           | no                     | Verzameling vrije tekstlabels m.b.t. accessoires, bijv: ['voorkrat', 'achterzitje', 'fietstassen'] |
| parkState            | string[]           | no                     | Verzameling vrije tekstlabels m.b.t. parkeerdetails, bijv: ['naast rek', 'dubbel in nietje']       |
| owner                | string             | no                     | Zie tabel vehicle.owner                                      |

---

### spaceTypeIDs - indeling volgens Trajan [Whitepaper fietsparkeerdrukonderzoek](./Whitepaper_fietsparkeerdrukonderzoek_1.0.pdf), p. 10
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

### Voetuigeigenschappen volgens wettelijke voettuigcategorie, naar type aandrijving en naar breedte, zoals beschreven in [2019.09.24dataformaatfietstellingenv2.10_fietstypen](./2019.09.24dataformaatfietstellingenv2.10_fietstypen.pdf) pagina's 16 en 17

### vehicle.type
| ID | Voertuigtype          | Omschrijving                                                                   |
| -- | --------------------- | ------------------------------------------------------------------------------ |
| f  | Fiets                 | Geen kenteken en geen verzekeringsplaatje                                      |
| bf | Bakfiets              | Fiets met bak                                                                  |
| s  | Snorfiets             | kentekenplaat is blauw, met een wit kader, wit opschrift en een hologram       |
| b  | Bromfiets             | kentekenplaat is geel, met een zwart kader, zwart opschrift en een hologram    |
| m  | Motorfiets            | NL geel of blauw, internationaal anders                                        |
| g  | Gehandicaptenvoertuig | driewieler, scootmobiel, rolstoel, etc                                         |
| a  | Anders                |                                                                                |

### vehicle.propulsion: s=spierkracht, b=brandstofmotor, e=elektrische motor
| ID  | Aandrijving           | Omschrijving                                                                   |
| --  | --------------------- | ------------------------------------------------------------------------------ |
| s   | Spierkracht           | bv traditionele fiets of voetganger                                            |
| se  | Elektrische hulpmotor | bv e-fiets, speed pedelec                                                      |
| e  | Alleen elektrisch     | bv e-bromfiets                                                                 |
| sb | Brandstof hulpmotor   | bv Sparta-met                                                                  |
| b  | alleen brandstof      | bv traditionele bromfiets, motorfiets                                          | 

### vehicle.owner
| ID | Eigenaar              | Omschrijving                                                                   |
| -- | --------------------- | ------------------------------------------------------------------------------ |
| p  | Privé                 | Privéfiets                                                                     |
| l  | Lease                 | Leasefiets, zoals Swap Bikes                                                   |
| h  | Huur                  | Huurfiets, zoals OV-fiets                                                      |

---

De velden vacantSpaces, occupiedSpaces en occupation zijn alleen aanwezig aan de bladeren van de sectieboom 

De vacantSpaces, etc. van een section kunnen bepaald worden door alle vacantSpaces, etc. van onderliggende sections op te tellen

---

# Soorten API's: 
[Schematisch overzicht van de API'in het fietsparkeren](./diagram_crow_api_overzicht.png)  

| ID | Omschrijving |
| -- | ------------------------------------------------------------------------------ |
| 1  | Van stalling naar exploitant |
| 2  | Van exploitant naar MaaS-provider |
| 3  | Van exploitant / straatteller naar dataportal  |
| 4  | Van dataporal naar analist |
| 5  | Van dataportal naar webapplicaties | 
| 6  | Van exploitant naar exploitatietools | 


# API 3 - data van stallingsexploitant of fietsteller opslaan in dataportal
## Nieuwe data opslaan (POST)
### Metingen groeperen in 1 onderzoek (= 'survey')  
#### Stap 1: meld je onderzoek (survey) aan, eventueel voorzien van data  
POST /surveys [Body](./API3/1_POST_new_survey.json)  
Een id van de survey mag door exploitant zelf gekozen worden. Suggestie: <postcode>_<source>_<date> => 1000_DeFietsTellers_2020.  
Indien geen surveyId gegeven, dan maakt de server zelf een id en geeft deze terug in de response.  

#### Stap 2: Stuur nieuwe data in op bestaande survey  
POST /surveys/:surveyId [Body](./API3/2_POST_update_survey.json)
als er nog geen onderzoek met gegeven surveyId bestaat, dan wordt deze aangemaakt  

### Losse metingen, dus geen deel van een onderzoek
POST /sections [Body](./API3/2_POST_sections.json)

### Voorbeelden:
Stuur  bezettingsdata voor een eenvoudige sectie: alleen capaciteit en bezetting zijn bekend

Stuur realtime bezettingsdata voor een complexe sectie: in alle voorzieningen en subvoorzieningen zijn alle soorten fietsen geteld
POST /surveys/:surveyId [Body](./API3/2_POST_complex_sections.json)

Stuur realtime bezettingsdata voor een eenvoudige sectie: alleen capaciteit en bezetting zijn bekend
POST /surveys/:surveyId [Body](./API3/2_POST_simple_sections.json)

## zoekopdrachten (GET)
### query-parameters voor GET-requests
| param     		| type		| values                                             	|
| ----------------- |---------- | ----------------------------------------------------- |
| source			| string	| Alleen data van deze bron     						|
| survey			| string	| Alleen data van dit onderzoek    						|
| data  			| string	| survey, staticData en/of dynamicData, default = alle data	|
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
| spaceType		    | number	| Alleen data voor dit type voorziening 				|
| spaceLevel        | number	| Alleen data voor deze verdieping                      |
|					|			| default = alle voorzieningen 							|
|					|			|														|
| startDate			| UTC timestamp	| Selectie op timestamp. Area.timestamp >= startdate 	|
| endDate			| UTC timestamp	| Selectie op timestamp. Area.timestamp < startdate		|
| groupBY			| string	| Lijst van kenmerken waarop de data gegroepeerd wordt		|

# API 4 - data lezen vanuit dataportal voor analysedoeleinden

### Ophalen van alle data van een bepaald onderzoek
GET /surveys/:surveyId?depth=4 [Response](./API4/GET_survey.json)  

### Ophalen van data van een bepaald onderzoek op straat- of stallingsniveau
GET /surveys/:surveyId [Response](./API4/GET_survey_depth1.json) - de default-waarde voor depth = 1, dus daarom wordt alle data platgeslagen op area-niveau  

### Opvragen van data van een bepaalde onderzoek op straat- of stallingsniveau
GET /surveys/sections/ketelstraat_oneven/?startDate=2020-11-23T0:00:00&endDate=2020-11-24T0:00:00 [Response](./API4/GET_section.json)  

### Opvragen van data van een bepaalde onderzoek op straat- of stallingsniveau, uitgesplitst op type voertuig
/surveys/sections/ketelstraat_oneven/?startDate=2020-11-23T0:00:00&endDate=2020-11-24T0:00:00&groupBy=vehicleType [Response](./API4/GET_groupby.json)  

### Selectie op vehicle: alle data voor een area over gewone fietsen
/sections/ketelstraat_oneven/?vehicleType=1&startDate=2020-11-23T0:00:00&endDate=2020-11-24T0:00:00 [Response](./API4/GET_section_depth1_fiets.json)  

# API 5 - data lezen vanuit dataportal voor t.b.v. webapplicaties
### Ophalen van de huidige bezetting van een bepaalde sectie tot op voorzieningniveau
GET /sections/ketelstraat_oneven/latest?depth=4 [Response](./API5/GET_section.json)  

### Opvragen van data van een bepaalde onderzoek op straat- of stallingsniveau
GET /sections/ketelstraat_oneven/latest [Response](./API5/GET_section_depth1.json)  

### Opvragen van data in een bepaald gebied
GET /sections/ketelstraat_oneven/latest?lat=5.107&long=52.08926&radius=5000 [Response](./API5/GET_realtime_sections_in_city.json)  

### Opvragen van data in een bepaalde sectie uitgesplitst op fietstype
GET /sections/ketelstraat_oneven/latest?lat=5.107&groupBy=vehicleType&long=52.08926&radius=5000 [Response](./API5/GET_realtime_section_groupby.json)  

### Opvragen van data in een bepaalde sectie voor gewonen fietsen
GET /sections/ketelstraat_oneven/latest?lat=5.107&vehicleType=1&long=52.08926&radius=5000 [Response](./API5/GET_realtime_section_depth1_fiets.json)  
