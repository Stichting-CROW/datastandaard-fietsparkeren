## Overzicht

In het fietsparkeerlandschap kunnen we globaal 6 datastromen onderscheiden:  
![schematische overzicht van de verschillende datastromen](/images/diagram_crow_api_overzicht.png)  

<table class='index'>

| ID | Omschrijving |
| -- | ------------------------------------------------------------------------------ |
| 1  | Van stalling naar exploitant |
| 2  | Van exploitant naar MaaS-provider |
| 3  | Van exploitant / straatteller naar dataportal  |
| 4  | Van dataportal naar analist |
| 5  | Van dataportal naar webapplicaties | 
| 6  | Van exploitant naar exploitatietools | 

</table>

Onderstaand ontwerp is in eerste instantie bedoeld voor de API's 3, 4 en 5, maar kan ook als leidraad dienen voor de overige datastromen.

## Indeling

* __1. Beschrijving datastandaard__: de datastandaard per object uitgeplozen en uitgelegd
  - Surveys
  - Statische data
  - Dynamische data
* __2. API Requests__: geeft een aantal praktijkvoorbeelden voor de API's 3 (POST-requests), 4 en 5 (GET-requests). De objecten uit hoofstuk 1 worden hier in JSON-formaat gebruikt.
* __3. Praktijkvoorbeelden van dynamische data__  
* __4. Beveiliging__: 
