### Enkele voorbeelden

#### Ophalen van alle data van een bepaald onderzoek
`GET /surveys?surveyId=0202_2020`  
[Response](./examples/API4/GET_survey.json)  

#### Ophalen van de statische data van een bepaald onderzoek
`GET /staticdata?surveyId=0202_2020`  
[Response](./examples/API4/GET_static.json)    

#### Ophalen van de volledige dynamische data van een bepaald onderzoek
`GET /dynamicdata?surveyId=0202_2020&depth=4`  
[Response](./examples/API4/GET_dynamic_depth4.json)  

#### Ophalen van de beknopte dynamische data van een bepaald onderzoek
`GET /dynamicdata?surveyId=0202_2020`  
[Response](./examples/API4/GET_dynamic_depth1.json) - de default-waarde voor depth = 1, dus daarom wordt alle data platgeslagen op de wortel van de sectieboom.  
Alleen dynamische data, want de parameter *data* is niet gegeven en de defaultwaarde is 'dynamic'.  

#### Opvragen van data van een bepaalde sectie in een gegeven periode
`GET /dynamicdata?sectionId=arnhem_ketelstraat_oneven&startDate=2020-11-23T0:00:00&endDate=2020-11-24T0:00:00`   
[Response](./examples/API4/GET_dynamic_depth1_single_section.json)  

#### Opvragen van statische data in een bepaald gebied, in een cirkel met straat 1km vanaf een gegeven punt
`GET /dynamicdata?geoPoint=5.90802,51.98173,1000`  
[Response](./examples/API4/GET_static.json)  

#### Opvragen van statische data in een bepaald gebied
`GET /dynamicdata?geoPolygon=52.370216,4.895168,53.370216,4.895168,53.370216,5.895168,52.370216,4.895168&relation=within`  
[Response](./examples/API4/GET_dynamic_depth1.json)  
*relation=within* geeft aan dat de gevonden secties zich volledig in de polygoon moeten bevinden  

### API 5 - realtime data lezen vanuit dataportal voor t.b.v. webapplicaties
Gebruikers van API 5 - de datastroom tussen het dataportal en de webapplicaties - zijn vooral geïnteresseerd in realtime data. Per secties dus slechts één resultaat. Door *latest* op te nemen in de url weet de API dat het om een dergelijk request gaat.  
Het gaat in de *latest*-requests altijd om dynamicdata, dus die kan worden weggelaten uit het pad

#### Ophalen van de huidige bezetting van een bepaalde sectie tot op voorzieningsnivea
`GET /latest?sectionId=arnhem_ketelstraat_oneven&depth=4`  
[Response](./examples/API5/GET_dynamic_depth4.json)  

#### Opvragen van beknopte data (depth=1) van een bepaalde sectie
`GET /latest?sectionId=arnhem_ketelstraat_oneven`  
[Response](./examples/API5/GET_dynamic_depth1_single_section.json)  

#### Opvragen van data in een bepaald gebied, in een cirkel met straal 200m vanaf een gegeven punt
`GET /latest?geopoint=5.90802,51.98173,200`  
[Response](./examples/API5/GET_dynamic_depth1.json)  



__Enige algemene endpoints ten behoeve van ontwikkeling GUI's__
Een overzicht van alle organisaties  
`GET /organisations`

Eén organisatie  
`GET /organisations/defietsentellers`

Een overzicht van alle organisaties die tellingen hebben laten uitvoeren  
`GET /authorities`

Een overzicht van alle organisaties die tellingen hebben uitgevoerd  
`GET /contractors`

## 3. Praktijkvoorbeelden van dynamische data  

Afbeeldingen zeggen vaak meer dan woorden. Daarom een tweetal voorbeelden met afbeeldingen, die de essentie van de datastandaard direct duidelijk maken.
__Voorbeeld 1: een straattelling in een doorsnee straat: Dorpsstraat, Ons Dorp__  
Een straattelling van een straat met een fietsenrek met 8 plekken, waarin 3 fietsen staan. 1 fiets staat naast het rek en twee fietsen staan tegen de gevel van een gebouw.  

![Dorpsstraat](/images/Dorpsstraat_waarneming.png)  

De straat is een sectie. Deze exacte locatie daarvan wordt vastgelegd in de statische data, bijvoorbeeld:  
ID: Dorpsstraat_OnsDorp  
Naam: Dorpsstraat  
Geo-locatie: Polygon[ hier een reeks coördinaten ]  
De dynamische data kan er visueel zo uit zien, als de tellers zeer gedetailleerd te werk gaan. Zo kan bijvoorbeeld zelfs worden aangegeven dat één van de fietsen in het rek een lekke band heeft.  

![Dorpsstraat volledig](/images/Dorpsstraat_volledig.png)  

Echter, in de praktijk zal een telling er misschien eerder zo uit zien:  

![Dorpsstraat telling op fietstype](/images/Dorpsstraat_fietstype.png)  

Of zelfs nog eenvoudiger, in geval van een weinig gedetailleerde telling:  

![Dorpsstraat sectie](/images/Dorpsstraat_sectie.png)  

Voor bepaalde analysedoeleinden is het grijze blokje telling zelfs niet eens zo interessant. Met de juiste zoekopdrachten in de API kan een respons als deze volstaan, zelfs als de meest gedetailleerde telling is opgeslagen:  

![Dorpsstraat beknopt](/images/Dorpsstraat_beknopt.png)  

__Voorbeeld 2: een doorsnee stationsstalling__  
Prorail beheert een groot aantal stationsstallingen in Nederland en laat deze automatisch tellen door sensoren in de rekken. Een typische Prorailstalling is opgedeeld in rijen. Iedere rij heeft een aantal voorzieningen met lage en hoge rekken. In dit voorbeeld heb ik er een sectie met bromfietsvlakken bijgetekend, iets wat Prorail-stallingen ongetwijfeld zullen hebben, maar waarop vooralsnog geen geautomatiseerde tellingen op plaatsvinden.  

![Stationsstalling](/images/Stationstalling_waarneming.png)  

Dit ziet er in de datastandaard zo uit:  

![Stationsstalling volledig](/images/Stationstalling_volledig.png)  

__Data opvragen__
Je kunt talloze manieren bedenken waarop een analist de data wil doorzoeken. De vraag daarbij is in hoeverre deze functionaliteit de dataportal moet worden ondersteund. De analist kan na opvraag van de complete dataset uiteraard ook zelf de data uitpluizen. 
Als er in de dataportal metingen tot op het grootste detailniveau zijn opgeslagen, zoals bovenstaande voorbeeld, dan kunnen datasets door de tijd heen en enorm groot worden. Dat is zeker het geval als het gaat om automatische tellingen die meerdere keren per uur worden ingestuurd, Enige filtermogelijkheden in de dataportal is dan geen slechte benadering. 
Daarom stelt de dataportal enkele zoekfunctionaliteiten verplicht voor de dataportals.

__Filter op diepte__
Gedetailleerde informatie over de bezetting geeft behoorlijk grote bomen, die niet voor iedere analist van deze data even interessant is. Als deze analist de waarnemingen van een stationsstalling door de tijd opvraagt, kunnen de responsen enorm groot worden. Als 90% van de data voor de analist niet relevant is, is dit natuurlijk onzinnig.
Daarom stelt de datastandaard een beperkt aantal zoekfunctionaliteiten verplicht. De belangrijkste van deze is dat je de mate van detail, de diepte (depth), bij een zoekopdracht kunt  aangeven aan de API. 
Stel dat in het dataportal de meest gedetailleerde boom is opgeslagen:

![Stationsstalling volledig](/images/Stationstalling_volledig.png)  

Als een analist alleen geïnteresseerd is in de bezetting van de totale stalling, vraagt hij data op op diepte 1. De API geeft hem dan dit resultaat:  

![Stationsstalling beknopt](/images/Stationstalling_beknopt.png)  

Als een stalling iedere 2 minuten data opslaat en een analist vraagt de data op van een hele maand, scheelt dat uiteraard enorm in de datastroom en daarmee in de performance van de API.  
