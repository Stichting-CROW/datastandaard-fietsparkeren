## Dataschema’s

De data in de datastandaard valt uiteen in drie hoofdblokken:

1. data over het onderzoek (*survey*)
2. statische data (static data): gegevens over de meetgebieden (sections): id, naam en geografische afbakening. Deze data verandert zelden of nooit. 
3. dynamische data (*dynamische data*): tellingen en metingen in de meetgebieden

De velden en zoekfuncties die in deze datastandaard zijn opgenomen, MOETEN worden ondersteund door de dataportal. Het staat de dataportals vrij om zelf extra datavelden en extra functionaliteit te bieden.


| Field         | Type              | Required  | Description                                                   |
| ------------- | ----------------- | --------- | -------------------------------------------------------------- |
| `survey`      | Survey            | no        | Gegevens over het onderzoek                                   |
| `staticData`  | StaticData        | no        | Statische data (sectienaam, adres, positie, ...)              |
| `dynamicData` | DynamicData       | no        | Dynamische data (bezettingsdata, fietstellingen, ...)         |
|{.data}

### Surveys

Het datablok Survey bevat data over het onderzoek. Geïnitieerd door wie? Uitgevoerd door wie? Wanneer? Waar? Al deze informatie kan worden ingestuurd, maar is niet verplicht.

Bij het insturen van een Survey kan de dataportal ervoor kiezen de gebruiker zijn eigen `Survey.id` te laten kiezen. Inzender van de nieuwe Survey wordt eigenaar van deze inzending. Als dat het geval is, dient er een 400-error (Bad request) gegenereerd te worden als er een `Survey.id` worden gekozen dat al bestaat. In geval de inzender de eigenaar is van de bestaande survey, vindt er een update plaats. Ondersteuning van deze functionalitet is door het dataportal zelf in te bepalen.

Het `Survey.id` kan gebruikt worden om data van verschillende secties en bronnen te groeperen door dynamische data te labelen met het veld `Survey.id`. Zie voor meer details [Dynamic Data](#dynamische-data).

#### `Survey`

Een survey bevat vaste gegevens over een onderzoek.

| Field           | Type     | Required | Description |
| --------------- | ---------| -------- | ----------- |
| `id`            | string   | yes      | Een uuid, random of eventueel samengesteld. Indien bij een POST-request niet gegeven, dan maakt de dataportal zelf een ID en geeft deze terug in de respons |
| `name`          | string   | yes      | Naam van het onderzoek |
| `geoLocation`   | GeoJSON  | no       | Geografische afbakening van het gehele onderzoeksgebied volgens [[rfc7946]]. |
| `authorityId`   | string   | no       | `Organisation.id` van de Opdrachtgever - Alleen te gebruiken voor insturen van data |
| `contractorIds` | string[] | no       | `Organisation.id` van de contractors - Alleen te gebruiken voor insturen van data |
| `license`       | string   | no       | Licentie van het gebruik van de data |
|{.data}

#### `Organisation`

De gegevens over een opdrachtgever of een uitvoerende instantie.


| Field                | Type                | Required    | Description                                                    |
| ----------- | ----------- | --------- | ------------------------------------------------------------- |
| `id`                  | string            | yes            | Unieke id, bijv. CBS-code gemeente of provincie. |
| `name`                | string            | yes            | Naam van de instantie                                 
|{.data}

(Hier zijn optioneel extra velden op te nemen, maar deze zijn geen onderdeel van de datastandaard)

### Statische data

Statische data is die data van secties die niet of nauwelijks aan verandering onderhevig zijn. Dat is bijvoorbeeld het geval bij de geografische afbakening. 
Mocht het zo zijn dat de geografische afbakening wijzigt, dan verdient het aanbeveling een nieuwe statische sectie aan te maken. Aanpassingen van bestaande secties gelden namelijk ook voor reeds ingestuurde data, wat kan leiden tot verwarring bij de interpretatie van historische data.
Of en hoe een statische sectie gewijzigd kan worden, valt buiten het bestek van deze standaard en wordt overgelaten aan de dataportals.
  
De parkeercapaciteit van een sectie lijkt op het eerste gezicht statisch. Toch is deze op advies van telinstanties niet opgenomen in de statische data. Wegwerkzaamheden en  herindelingen van straten hebben te vaak invloed op de parkeercapaceit van straatsecties. In bewaakte stallingen kan het voorkomen dat bepaalde secties gedurende bepaalde periodes gereserveerd zijn.

#### `StaticData`

| Field                | Type                      | Required    | Description                                                            |
| ----------- | ----------------- | --------- | ------------------------------------------- |
| `result`          | StaticSection[]   | yes            | Verzameling van statische secties           |
|{.data}

#### `StaticSection`

| Field             | Type                | Required | Description                                                |
| ----------------- | ------------------- | -------- | ---------------------------------------------------------- |
| `id`                | string              | yes      | Een uuid, random of eventueel samengesteld                 |
| `geoLocation`                | GeoJSON                  | no           | Geografische afbakening van deze sectie volgens [[rfc7946]]. Kan gebruikt worden voor geo-zoekopdrachten. |
| `validFrom`         | [[ISO8601]] timestamp   | no       | Vanaf dit tijdstip mag er dynamische data in deze sectie worden geschreven |
| `validThrough`      | [[ISO8601]] timestamp   | no       | Tot dit tijdstip mag er dynamische data in deze sectie worden geschreven |
| `owner`             | string              | no       | organisationId: Eigenaar van deze sectie. Alleen deze organistatie mag wijzigingen aanbrengen aan deze sectie |
|{.data}

<div class='issue' data-number="4"></div>
  
### Dynamische data

Dynamische data, oftewel: de tellingen, daar is uiteraard waar het in deze datastandaard om te doen is. Dynamische data is een verzameling secties/meetgebieden. In een meetgebied kunnen weer subsecties worden ondergebracht en in de subsecties weer nieuwe subsecties. Zo ontstaat er een boom aan secties, die in de onderstaande tabellen 'sectieboom' wordt genoemd. 
Secties zonder subsecties, dus de uiteinden van de sectieboom, heten 'bladeren'. 
Zo kunnen een straat worden opgedeeld in linker- en rechterzijde met aan elke kant diverse parkeervoorzieningen. 

<aside class="example" title="Boom- en bladeren">

Een sectieboom kan er als volgt uit zien:  

- Sectie (Dorpsstraat)  
  - Subsectie (Even zijde)  
    - Subsubsectie (Rek) → dit is een blad  
    - Subsubsectie (Verzameling nietjes) → dit is een blad  
  - Subsectie (Oneven zijde)  
    - Subsubsectie (gevel) → dit is een blad  

</aside>

Om de ontwikkeling van een dataportal niet te complex te maken, is het aantal secties in secties voorlopig begrensd op 3 lagen.  

<aside class='def'>

In de bladeren van de sectieboom MOET gedetailleerde teldata worden doorgegeven, verpakt in `Count`-objecten.

</aside>

Zie document [Dynamische data in de fietsparkeerstandaard](../docs/20190924-dataformaat-fietstellingen-v2-10.pdf) voor een beknopte a-technische uitleg van het telprincipe in de datastandaard  

<div class='issue' data-number='5'></div>

#### `DynamicData`

| Field                | Type                      | Required    | Description                                                    |
| ----------- | ----------------- | --------- | ------------------------------------------------------------- |
| `result`          | DynamicSection[]  | yes        | Verzameling secties met stallingsdata                         |
|{.data}

#### `DynamicSection`

| Field                     | Type                | Required    | Description                                                |
| ------------------------- | ------------------- | ----------- | ---------------------------------------------------------- |
| `id`                      | string              | yes         | id van deze dynamische sectie, indien niet meegestuurd bij schrijven, wordt deze gegenereerd door de API      |
| `staticSectionId`         | string              | yes         | id van de statische sectie waartoe deze dynamische data behoort       |
| `timestamp`               | [[ISO8601]] timestamp   | conditional | Tijdstip van de meting. Alleen verplicht in de stam van de sectieboom |
| `surveyId`                | string                  | conditional | Id van de survey waartoe deze meting behoort |
| `authorityId`             | string                  | conditional | Id van de opdrachtgever van deze meting |
| `contractorId`            | string              | conditional | Id van de instantie die deze data aangeleverd heeft. Bij een POST-request dient dit door het dataportal gevuld te worden met de username van de inzender |
| `parkingCapacity`         | number              | conditional | Totaal aantal plekken, verplicht in de bladeren van de sectieboom           |
| `parkingCapacityTimestamp`| [[ISO8601]] timestamp   | no          | Tijdstip van meting aantal plekken                       |
| `vacantSpaces`            | number              | conditional | Aantal vrije plekken, verplicht in de bladeren van de sectieboom    |
| `occupiedSpaces`          | number              | conditional | Aantal bezette plekken, verplicht in de bladeren van de sectieboom  |
| `count`                   | Count[]             | no          | Verzameling van Count-objecten                      |
| `space`                        | Space                   | no          | Alleen toegestaan in de bladeren van de sectieboom  |
| `sections`                | DynamicSection[]    | no          | Verzameling van subsecties. Er zitten dus subsections in een subsection. Dit mag maximaal 3 lagen diep                          |
| `note`                   | Note                | no          | Notities over de meting in deze sectie                   |
|{.data}

De velden surveyId, authorityId en contractorId kunnen gebruikt worden bij het filteren van data bij de zoekopdrachten van de API's 4 en 5.
Je zou bijvoorbeeld kunnen alle metingen van een bepaald onderzoek die zijn uitgevoerd door een bepaalde contractor kunnen opvragen. Zie *API Requests* voor meer details over zoekopdrachten.

De velden `parkingCapacity`, `vacantSpaces` en `occupiedSpaces` en `count` zijn VEREIST in de bladeren van de sectieboom.

Indien ze in de takken niet gegeven zijn, kunnen `vacantSpaces`, `occupiedSpaces` en `occupation` berekend worden door alle `vacantSpaces`, etc. van onderliggende sections op te tellen.

### `Space`

Definiëring van een plek aan de hand van properties

| Field                | Type               | Required                | Description                                                 |
| -------------------- | ------------------ | ----------------------- | ------------------------------------------------------------|
| `type`               | string             | no                      | SpaceTypeID; tenminste 1 veld dient gegeven te zijn            |
| `level`              | number             | no                      | 0=onder, 1=boven                                            |
| `vehicles`           | Vehicle[]          | no                      | Deze space is uitsluitend geschikt voor genoemde voertuigen |
|{.data}

#### `Space.type`

| ID | spaceType             |
| -- | --------------------- |
| `x`  | buiten voorziening    |
| `r`  | rek                   |
| `n`  | nietjes               |
| `v`  | vak                   |
| `w`  | voor fietsenwinkel    |
| `a`  | anders                |
|{.data}

<div class='issue' data-number='6'></div>

### `Vehicle`

| Field                | Type               | Required               | Description                                                  |
| -------------------- | ------------------ | ---------------------- | ------------------------------------------------------------ |
| `type`               | string             | no                     | Zie tabel Vehicle.type                                       |
| `propulsion`         | string[]           | no                     | Zie tabel Vehicle.propulsion                                 |
| `appearance`         | string             | no                     | Zie tabel Vehicle.appearance                                 |
| `state`              | VehicleState[]     | no                     | Zie tabel VehicleState                                      |
| `parkState`          | string[]           | no                     | Zie tabel Vehicle.parkState                                  |
| `accessoires`        | Accessoire[]       | no                     | Zie tabel Accessoire                                |
| `owner`              | string             | no                     | Zie tabel Vehicle.owner                                      |
|{.data}

#### `Accessoire`

| Field                | Type               | Required               | Description                                                  |
| -------------------- | ------------------ | ---------------------- | ------------------------------------------------------------ |
| `type`               | string             | no                     | Zie tabel Vehicle.accessoire.typen                          |
| `position`           | string             | no                     | Zie tabel Vehicle.accessoire.positions                      |
|{.data}

##### `Vehicle.accessoire.typen` 

| ID | Omschrijving          |
| -- | --------------------- |
| `z`  | zitje                 |
| `t`  | fietstas              |
| `r`  | rek                   |
| `b`  | bak / mand            |
|{.data}

##### `Vehicle.accessoire.positions` 

| ID | Omschrijving          |
| -- | --------------------- |
| `v`  | voor                  |
| `a`  | achter                |
|{.data}


#### `VehicleState`

| Field                | Type               | Required               | Description                                                  |
| -------------------- | ------------------ | ---------------------- | ------------------------------------------------------------ |
|`type`                | string             | no                     | Zie tabel Vehicle.state.type                          |
|`position`            | string             | no                     | Zie tabel Vehicle.state.position                      |
|{.data}

##### `Vehicle.state.type` 

| ID | Omschrijving          |
| -- | --------------------- |
| `w`  | wrak                  |
| `l`  | lekke band            |
| `z`  | zonder zadel          |
| ...| ...                   |
|{.data}

##### `Vehicle.state.position`

| ID | Omschrijving          |
| -- | --------------------- |
| `v`  | voor                  |
| `a`  | achter                |
|{.data}

#### `Vehicle.parkState`

| ID | Omschrijving          |
| -- | --------------------- |
| `n`  | naast voorziening     |
| `d`  | dubbel                |
| ...| ...                   |
|{.data}

#### `Vehicle.type`

Classificatie naar wettelijke voertuigcategorie.

| ID | Voertuigtype          | Omschrijving                                                                   |
| -- | --------------------- | ------------------------------------------------------------------------------ |
| `f`  | fiets                 | Geen kenteken en geen verzekeringsplaatje                                      |
| `c`  | bakfiets              | Fiets met bak                                                                  |
| `s`  | snorfiets             | kentekenplaat is blauw, met een wit kader, wit opschrift en een hologram       |
| `b`  | bromfiets             | kentekenplaat is geel, met een zwart kader, zwart opschrift en een hologram    |
| `m`  | motorfiets            | NL geel of blauw, internationaal anders                                        |
| `g`  | gehandicaptenvoertuig | driewieler, scootmobiel, rolstoel, etc                                         |
| `a`  | anders                |                                                                                |
|{.data}

#### `Vehicle.propulsion`

| ID | Aandrijving     | Omschrijving                                                                   |
| -- | --------------- | ------------------------------------------------------------------------------ |
| `s`  | Spierkracht     | bv traditionele fiets of voetganger                                            |
| `e`  | Elektrisch      | bv e-bike                                                                      |
| `b`  | Brandstof       | bv traditionele bromfiets, motorfiets                                          | 
|{.data}

#### `Vehicle.appearance`

| ID | Verschijningsvorm |
| -- | --------------- |
| `k`  | Kinderfiets    |
| `r`  | Racefiets      | 
| `l`  | Ligfiets       | 
| `b`  | Bakfiets / Transportfiets       | 
| `f`  | Fietskar       | 
| `v`  | Vouwfiets       | 
| `m`  | Mountainbike    | 
| `d`  | Driewieler       | 
| `t`  | Tandem       | 
|{.data}

#### `Vehicle.owner`

| ID | Eigenaar              | Omschrijving                                                                   |
| -- | --------------------- | ------------------------------------------------------------------------------ |
| `p`  | Privé                 | Privéfiets                                                                     |
| `l`  | Lease                 | Leasefiets, zoals Swap Bikes                                                   |
| `h`  | Huur                  | Huurfiets, zoals OV-fiets                                                      |
|{.data}

### `Count`

| Field                | Type               | Required               | Description                                                  |
| -------------------- | ------------------ | ---------------------- | ------------------------------------------------------------ |
|`vehicle`             | Vehicle            | no                     | Niet gegeven betekent dat het voertuigtype niet bekend is |
|`numberOfVehicles`    | number             | yes                    | Aantal gestalde voertuigen                                   |
|{.data}

### `Note`

| Field               | Type                | Required               | Description                                                  |
| ------------------- | ------------------- | ---------------------- | ------------------------------------------------------------ |
|`isOpen`             | boolean             | no                     | is deze area, bijv. een stalling geopend?                    |
|`isHoliday`          | boolean             | no                     | Vakantie?                                                    |
|`isEvent`            | boolean             | no                     | Evenement (markt, kermis, ...)?                              |
|`isUnderConstruction`| boolean             | no                     | Werkzaamheden?                                               |
|`remark`             | string              | no                     | Vrij tekstveld                                               |
|{.data}
