### Voorbeelden

#### API 3 – Metingen groeperen in 1 onderzoek (= 'survey')  
##### Stap 1: zorg dat alle betrokken partijen (opdrachtgevers/authorities, tellers/contractors) bekend zijn bij de dataportal
Check met 
`GET /organisations` welke instanties er al bekend zijn.  
[Response](./examples/API3/responses/GET_organisations.json)  
  
<pre class='example json' data-include='../examples/API3/responses/GET_organisations.json' data-include-format='text'></pre>

Mis je instanties, voeg ze toe met:  
`POST /organisations` [Body zonder surveyId](./examples/API3/requests/POST_new_organisation.json)  
[Response](./examples/API3/responses/POST_new_organisation.json)  

<pre class='example json' title="Body (zonder surveyId)" data-include='../examples/API3/requests/POST_new_organisation.json' data-include-format='text'></pre>
<pre class='example json' title="Response" data-include='../examples/API3/responses/POST_new_organisation.json' data-include-format='text'></pre>

##### Stap 2: meld je onderzoek (survey) aan
`POST /surveys` [Body met surveyId](./examples/API3/requests/POST_new_survey_with_id.json)  
Een id van de survey mag door exploitant zelf gekozen worden. Suggestie gebruik [CBS codes](https://www.cbs.nl/nl-nl/onze-diensten/methoden/classificaties/overig/gemeentelijke-indelingen-per-jaar/indeling-per-jaar/gemeentelijke-indeling-op-1-januari-2020) en unieke gegevens uit het onderzoek, bijv. < CBS_nr_gemeente >_< jaartal > => 0202_2020.  

`POST /surveys` [Body zonder surveyId](./examples/API3/requests/POST_new_survey_without_id.json)  

<pre class='example json' title='Body zonder surveyId' data-include='../examples/API3/requests/POST_new_survey_without_id.json' data-include-format='text'></pre>

De instantie die de survey instuurt, wordt 'eigenaar' van dit onderzoek. 

Een Survey met een `surveyId` dat al bestaat, zal resulteren in een 400-error. Behalve als deze POST wordt gedaan door de eigenaar van het onderzoek. In dat geval wordt er een update van het onderzoek uitgevoerd.

Indien geen `surveyId` is gegeven, maakt de server zelf een id en geeft deze terug in de response.  

[Response](./examples/API3/responses/POST_new_survey.json)  

<pre class='example json' title="Response" data-include='../examples/API3/responses/POST_new_survey.json' data-include-format='text'></pre>


##### Stap 3: koppel statische data aan bestaand onderzoek
`POST /staticsection` [Body](./examples/API3/requests/POST_static_section.json)  
<pre class='example json' title="Body" data-include='../examples/API3/requests/POST_static_section.json' data-include-format='text'></pre>  
[Response](./examples/API4/POST_static_section.json)  - In geval van een succesvolle opslag, bevat de response het opgeslagen object.  

Het `id` van een statische sectie is, net als het `surveyID`, door de opsturende instantie zelf samen te stellen. Suggestie: prefix het sectionId met het `surveyId` om een unieke id te garanderen, bijvoorbeeld: `< surveyId >_< straatnaam >`

Als er al een sectie met hetzelfde `id` bestaat, krijgt de response code 409 (Conflict). Tenzij de POST wordt gedaan door de owner van de sectie. In dat geval wordt de oude sectie overschreven.

Het is niet verplicht veld `id` mee te geven in de request. Als er geen `id` gegeven is, wordt deze door de API gegenereerd en is deze zichtbaar in de respons, dat immers het volledig opgeslagen object bevat.

##### Stap 3: Sla losse metingen op
`POST /dynamicdata` [Body](./examples/API3/requests/POST_dynamic_data.json)

<pre class='example json' title="Body" data-include='../examples/API3/requests/POST_dynamic_data.json' data-include-format='text'></pre>

Door het veld surveyId mee te geven aan de meting, worden deze gekoppeld aan een onderzoek.  
Zonder het veld surveyId worden de data niet gekoppeld aan een onderzoek en zullen ze dus ook niet gevonden worden bij een zoekopdracht naar een surveyId!

Je kunt dus zowel dynamische als statische data afzonderlijk koppelen aan een onderzoek.   
   
Dynamische data kan sterk gecomprimeerd worden door alleen de totalen op te slaan. Dit zal in de praktijk vaak voorkomen.  
`POST /dynamicdata` [Body met een alleen totalen](./examples/API3/requests/POST_compressed_dynamic_section.json)  

<pre class='example json' title="Body met een alleen totalen" data-include='../examples/API3/requests/POST_compressed_dynamic_section.json' data-include-format='text'></pre>

#### API 4 — data opvragen
##### Ophalen van alle data van een bepaald onderzoek
`GET /surveys?surveyId=0202_2020`  
[Response](./examples/API4/GET_survey.json)  
<pre class='example json' data-include='../examples/API4/GET_survey.json' data-include-format='text'></pre>

##### Ophalen van de statische data van een bepaald onderzoek
`GET /staticdata?surveyId=0202_2020`  
[Response](./examples/API4/GET_static.json)    

<pre class='example json' data-include='../examples/API4/GET_static.json' data-include-format='text'></pre>


##### Ophalen van de volledige dynamische data van een bepaald onderzoek
`GET /dynamicdata?surveyId=0202_2020&depth=4`  
[Response](./examples/API4/GET_dynamic_depth4.json)  

<pre class='example json' data-include='../examples/API4/GET_dynamic_depth4.json' data-include-format='text'></pre>


##### Ophalen van de beknopte dynamische data van een bepaald onderzoek
`GET /dynamicdata?surveyId=0202_2020`  
<pre class='example json' data-include='../examples/API4/GET_dynamic_depth1.json' data-include-format='text'></pre>


De default-waarde voor depth = 1, dus daarom wordt alle data platgeslagen op de wortel van de sectieboom.  
Alleen dynamische data, want de parameter *data* is niet gegeven en de defaultwaarde is 'dynamic'.  

##### Opvragen van data van een bepaalde sectie in een gegeven periode
`GET /dynamicdata?sectionId=arnhem_ketelstraat_oneven&startDate=2020-11-23T0:00:00&endDate=2020-11-24T0:00:00`   
[Response](./examples/API4/GET_dynamic_depth1_single_section.json)  

<pre class='example json' data-include='../examples/API4/GET_dynamic_depth1_single_section.json' data-include-format='text'></pre>


##### Opvragen van statische data in een bepaald gebied, in een cirkel met straat 1km vanaf een gegeven punt
`GET /dynamicdata?geoPoint=5.90802,51.98173,1000`  
[Response](./examples/API4/GET_static.json)  

<pre class='example json' data-include='../examples/API4/GET_static.json' data-include-format='text'></pre>


##### Opvragen van statische data in een bepaald gebied
`GET /dynamicdata?geoPolygon=52.370216,4.895168,53.370216,4.895168,53.370216,5.895168,52.370216,4.895168&relation=within`  
[Response](./examples/API4/GET_dynamic_depth1.json)  

<pre class='example json' data-include='../examples/API4/GET_dynamic_depth1.json' data-include-format='text'></pre>

*relation=within* geeft aan dat de gevonden secties zich volledig in de polygoon moeten bevinden  

### API 5 - realtime data lezen vanuit dataportal voor t.b.v. webapplicaties
Gebruikers van API 5 - de datastroom tussen het dataportal en de webapplicaties - zijn vooral geïnteresseerd in realtime data. Per secties dus slechts één resultaat. Door *latest* op te nemen in de url weet de API dat het om een dergelijk request gaat.  
Het gaat in de *latest*-requests altijd om dynamicdata, dus die kan worden weggelaten uit het pad

#### Ophalen van de huidige bezetting van een bepaalde sectie tot op voorzieningsnivea
`GET /latest?sectionId=arnhem_ketelstraat_oneven&depth=4`  
[Response](./examples/API5/GET_dynamic_depth4.json)  

<pre class='example json' data-include='../examples/API5/GET_dynamic_depth4.json' data-include-format='text'></pre>


#### Opvragen van beknopte data (depth=1) van een bepaalde sectie
`GET /latest?sectionId=arnhem_ketelstraat_oneven`  
[Response](./examples/API5/GET_dynamic_depth1_single_section.json)  

<pre class='example json' data-include='../examples/API5/GET_dynamic_depth1_single_section.json' data-include-format='text'></pre>


#### Opvragen van data in een bepaald gebied, in een cirkel met straal 200m vanaf een gegeven punt
`GET /latest?geopoint=5.90802,51.98173,200`  
[Response](./examples/API5/GET_dynamic_depth1.json)  

<pre class='example json' data-include='../examples/API5/GET_dynamic_depth1.json' data-include-format='text'></pre>




__Enige algemene endpoints ten behoeve van ontwikkeling GUI's__
Een overzicht van alle organisaties  
`GET /organisations`

Eén organisatie  
`GET /organisations/defietsentellers`

Een overzicht van alle organisaties die tellingen hebben laten uitvoeren  
`GET /authorities`

Een overzicht van alle organisaties die tellingen hebben uitgevoerd  
`GET /contractors`

## Praktijkvoorbeelden van dynamische data  

Afbeeldingen zeggen vaak meer dan woorden. Daarom een tweetal voorbeelden met afbeeldingen, die de essentie van de datastandaard direct duidelijk maken.

### Straattelling

In de *Dorpsstraat*, te *Ons Dorp*,  staat een fietsenrek met 8 plekken, waarin 3 fietsen staan. 
1 fiets staat naast het rek en twee fietsen staan tegen de gevel van een gebouw.  

<figure>
<img alt="Dorpsstraat" src="../images/dorpsstraat-waarneming.drawio.svg" />
<figcaption>Waarneming Dorpsstraat</figcaption>
</figure>

De straat is een sectie. 
Deze exacte locatie daarvan wordt vastgelegd in de statische data, bijvoorbeeld:  

<div class='example'>

```json
"id": "Dorpsstraat_OnsDorp"
"naam": "Dorpsstraat"
"geoLocation": ...
```
</div>

De dynamische data kan er visueel zo uit zien, als de tellers zeer gedetailleerd te werk gaan. Zo kan bijvoorbeeld zelfs worden aangegeven dat één van de fietsen in het rek een lekke band heeft.

<figure>
<img src="../images/dorpsstraat-volledig.drawio.svg" alt="Dorpsstraat volledig"/>
<figcaption>Dorpsstraat volledig</figcaption>
</figure>

Echter, in de praktijk zal een telling er misschien eerder zo uit zien:

<figure>
<img src="../images/dorpsstraat-zonderfietstype.drawio.svg" alt="Dorpsstraat telling op fietstype"/>
<figcaption>Dorpsstraat telling op fietstype</figcaption>
</figure>

Of zelfs nog eenvoudiger, in geval van een weinig gedetailleerde telling:

<figure>
<img src="../images/dorpsstraat-zondersectie.drawio.svg" alt="Dorpsstraat sectie"/>
<figcaption>Dorpsstraat sectie</figcaption>
</figure>

Voor bepaalde analysedoeleinden is het grijze blokje telling zelfs niet eens zo interessant. Met de juiste zoekopdrachten in de API kan een respons als deze volstaan, zelfs als de meest gedetailleerde telling is opgeslagen:

<figure>
<img src="../images/dorpsstraat-beknopt.drawio.svg" alt="Dorpsstraat beknopt"/>
<figcaption>Dorpsstraat beknopt</figcaption>
</figure>

### Stationsstalling

Prorail beheert een groot aantal stationsstallingen in Nederland en laat deze automatisch tellen door sensoren in de rekken (HBF). 
Een typische Prorailstalling is opgedeeld in rijen. 
Iedere rij heeft een aantal voorzieningen met lage en hoge rekken. 
In dit voorbeeld heb ik er een sectie met bromfietsvlakken bijgetekend, iets wat Prorail-stallingen ongetwijfeld zullen hebben, maar waarop vooralsnog geen geautomatiseerde tellingen op plaatsvinden.  

<figure>
<img alt="Stationsstalling" src="../images/stationsstalling-waarneming.drawio.svg" />
<figcaption>Stationsstalling</figcaption>
</figure>

Dit ziet er in de datastandaard zo uit:  

<figure>
<img alt="Stationsstalling volledig" src="../images/stationsstalling-volledig.drawio.svg" />
<figcaption>Stationsstalling volledig</figcaption>
</figure>

#### Data opvragen

Je kunt talloze manieren bedenken waarop een analist de data wil doorzoeken. De vraag daarbij is in hoeverre deze functionaliteit de dataportal moet worden ondersteund. De analist kan na opvraag van de complete dataset uiteraard ook zelf de data uitpluizen. 
Als er in de dataportal metingen tot op het grootste detailniveau zijn opgeslagen, zoals bovenstaande voorbeeld, dan kunnen datasets door de tijd heen en enorm groot worden. Dat is zeker het geval als het gaat om automatische tellingen die meerdere keren per uur worden ingestuurd, Enige filtermogelijkheden in de dataportal is dan geen slechte benadering. 
Daarom stelt de dataportal enkele zoekfunctionaliteiten verplicht voor de dataportals.

#### Filter op diepte

Gedetailleerde informatie over de bezetting geeft behoorlijk grote bomen, die niet voor iedere analist van deze data even interessant is. Als deze analist de waarnemingen van een stationsstalling door de tijd opvraagt, kunnen de responsen enorm groot worden. Als 90% van de data voor de analist niet relevant is, is dit natuurlijk onzinnig.
Daarom stelt de datastandaard een beperkt aantal zoekfunctionaliteiten verplicht. De belangrijkste van deze is dat je de mate van detail, de diepte (depth), bij een zoekopdracht kunt  aangeven aan de API. 
Stel dat in het dataportal de meest gedetailleerde boom is opgeslagen:

<figure>
<img alt="Stationsstalling volledig" src="../images/stationsstalling-volledig.drawio.svg" />
<figcaption>Stationsstalling volledig</figcaption>
</figure>

Als een analist alleen geïnteresseerd is in de bezetting van de totale stalling, vraagt hij data op op diepte 1. De API geeft hem dan dit resultaat:  

<figure>
<img alt="Stationsstalling beknopt" src="../images/stationsstalling-beknopt.drawio.svg" />
<figcaption>Stationsstalling beknopt</figcaption>
</figure>

Als een stalling iedere 2 minuten data opslaat en een analist vraagt de data op van een hele maand, scheelt dat uiteraard enorm in de datastroom en daarmee in de performance van de API.  
