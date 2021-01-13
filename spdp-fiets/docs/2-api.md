## 2. API- requests
### algemene regels
Alle GET-requests moeten in een wrapper-object gestoken worden met het resultaat in een property 'result'.  
Dus `GET /staticdata`:  
Respons:  
```
{  
  "result": [  
    StaticSection,  
    ...  
  ]  
}  
```
Dit is bedoeld om de API de mogelijkheid te geven metadata over de zoekresultaten mee te geven, bijvoorbeeld data over paginering, zoekfilters, errors, etc.  
Uitgezonderd van deze regel zijn responses die expliciet om 1 object vragen, zoals:  
`/organiations/defietsentellers`  
Respons:  

```
{  
  "id": "defietsentellers,  
  "name: "De Fietsentellers BV"  
}  
```

### API 3 - data van stallingsexploitant of fietsteller opslaan in dataportal
API 3 is de ontvangde zijde van de dataportal API. Straattellers of stallingssoftware kunnen met POST-requests hun data opsturen naar het dataportal.
In de body's van deze POST-requests dienen ze gebruik te maken van boven beschreven objecten.  

De datastandaard schrijft voor dat data in elk geval in afzonderlijke blokken Survey, Static Data en Dynamic Data opgeslagen dient te worden. Er zijn dus tenminste 3 POSTs nodig om een volledige Survey, inclusief data op te slaan.

Alle genoemde requests beginnen met een /. Voor deze schuine streep komt uiteraard de base-url van de API. In het geval van de pilot in VeiligStallen is de base-url https://remote.veiligstallen2.nl/rest/api

### Metingen groeperen in 1 onderzoek (= 'survey')  
#### Stap 1: zorg dat alle betrokken partijen (opdrachtgevers/authorities, tellers/contractors) bekend zijn bij de dataportal
Check met 
`GET /organisations` welke instanties er al bekend zijn.  
[Response](./examples/API3/responses/GET_organisations.json)  
  
Mis je instanties, voeg ze toe met:  
`POST /organisations` [Body zonder surveyId](./examples/API3/requests/POST_new_organisation.json)  
[Response](./examples/API3/responses/POST_new_organisation.json)  
  
#### Stap 2: meld je onderzoek (survey) aan
`POST /surveys` [Body met surveyId](./examples/API3/requests/POST_new_survey_with_id.json)  
Een id van de survey mag door exploitant zelf gekozen worden. Suggestie gebruik [CBS codes](https://www.cbs.nl/nl-nl/onze-diensten/methoden/classificaties/overig/gemeentelijke-indelingen-per-jaar/indeling-per-jaar/gemeentelijke-indeling-op-1-januari-2020) en unieke gegevens uit het onderzoek, bijv. < CBS_nr_gemeente >_< jaartal > => 0202_2020.  

`POST /surveys` [Body zonder surveyId](./examples/API3/requests/POST_new_survey_without_id.json)  

De instantie die de survey instuurt, wordt 'eigenaar' van dit onderzoek. 

Een Survey met een id dat al bestaat, zal resulteren in een 400-error. Behalve als deze POST wordt gedaan door de eigenaar van het onderzoek. In dat geval wordt er een update van het onderzoek uitgevoerd.

Indien geen surveyId is gegeven, maakt de server zelf een id en geeft deze terug in de response.  

[Response](./examples/API3/responses/POST_new_survey.json)  

#### Stap 3: koppel statische data aan bestaand onderzoek
`POST /staticdata` [Body](./examples/API3/requests/POST_static_data.json)  

Door het veld surveyId mee te geven aan de secties, worden de secties gekoppeld aan een onderzoek.  
Zonder het veld surveyId worden de statische secties niet gekoppeld aan een onderzoek en zullen ze dus ook niet gevonden worden bij een zoekopdracht naar een surveyId.

Het Id van een statische sectie is, net als het surveyID, door de opsturende instantie zelf samen te stellen. Suggestie: prefix het sectionId met het surveyId om een unieke id te garanderen, bijvoorbeeld: < surveyId >_< straatnaam >

### Stap 3: Sla losse metingen op
`POST /dynamicdata` [Body](./examples/API3/requests/POST_dynamic_data.json)

Door het veld surveyId mee te geven aan de secties, worden de metingen gekoppeld aan een onderzoek.  
Zonder het veld surveyId worden de statische secties niet gekoppeld aan een onderzoek en zullen ze dus ook niet gevonden worden bij een zoekopdracht naar een surveyId.

Je kunt dus zowel dynamische als statische data afzonderlijk koppelen aan een onderzoek.   
   
Dynamische data kan sterk gecomprimeerd worden door alleen de totalen op te slaan. Dit zal in de praktijk vaak voorkomen.  
`POST /dynamicdata` [Body met een alleen totalen](./examples/API3/requests/POST_compressed_dynamic_section.json)  

### API 4 - data opvragen
Op dezelfde manier het insturen van data kan de data ook weer worden opgevraad. De POST-requests veranderen in GET-requests:  
`GET /surveys?query_params`
`GET /staticdata?query_params`
`GET /dynamicdata?query_params`

De `query_params` dienen als zoekopdrachten, zodat heel specifiek naar data gezocht kan worden. Dat kan op diverse manieren. De datastandaard stelt een minimaal aantal zoekopties verplicht:

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
`?authorityID=:authorityID`  

__Filter op dataleverancier__  
De dataportal kan de data die wordt ingestuurd voorzien van de ID van de leverancier van deze data. Als dat gebeurt, kan er uiteraard gezocht worden op dit ID:  
`?contractorID=:contractorId`  

__Geo zoekopdrachten__
Als de statische data van secties is voorzien van exacte geo-coördinaten, moeten deze secties gevonden kunnen worden aan de van een geo-zoekopdracht.  

__Filter op geo-data: polygoon__  
Geef een polygoon mee in de query, aan de hand waarvan de corresponderende secties worden gezocht.  
Er zijn twee manieren voor deze query:  
* zoek secties die de gegeven polygoon overlappen: relation=intersects. Dit is de default.  
`?geopolygon=4.895168,52.370216,4.895168,53.370216,5.895168,53.370216,4.895168,52.370216&relation=intersects`  
* zoek secties die geheel binnen de gegeven polygoon vallen: relation=within.  
`?geopolygon=4.895168,52.370216,4.895168,53.370216,5.895168,53.370216,4.895168,52.370216&relation=within`  

__Sorteren__
Dynamische data moet gesorteerd kunnen worden opgevraagd. Dat kan met de parameters `orderBy` en `orderDirection`:  
`dynamicdata?orderBy=timestamp&orderDirection=ASC` - sorteert op timestamp van oudste naar nieuwste  
`dynamicdata?orderBy=count.numberOfVehicles&orderDirection=DESC` - sorteert op aantal voertuigen van hoog naar laag  

### Een overzicht van alle query parameters
| param     		| type		| values                                             	|
| ----------------- |---------- | ----------------------------------------------------- |
| surveyid			| string	| Alleen data van dit onderzoek    						|
| authorityid   | string	| Alleen data van deze opdrachtgever         			|
| contarctorid  | string	| Alleen data van deze dataleverancier      			|
| depth 		    | number	| Aantal te bevragen sectie-lagen vanaf gegeven pad  default = 1                           				|
| startdate			| UTC timestamp	| Selectie op timestamp. Section.timestamp >= startDate 	|
| enddate			  | UTC timestamp	| Selectie op timestamp. Section.timestamp <= endDate    	|
|					      |			      |	        													|
| geopolygon		| list met coördinaten | lat1,lng1,lat2,lng2,lat3,lng3,...,...,lat1,lng1	|
| georelation  	| string    | 'intersects' (default) of 'within'	|
|					      |			      |	        													|
| orderBy     	| string    | veldnaam waarop gesorteerd wordt	|
| orderDirection    	| string    | ASC (default) of DESC, in combinatie met orderBy	|

Query-params dienen hoofdletterongevoelig te zijn, dus authorityid=abc is hetzelfde als authorityID=abc
