# Inleiding

In het fietsparkeerlandschap kunnen we globaal 6 datastromen onderscheiden:
tussen de systemen van de stalling, de exploitant, MaaS-providers, straattellers naar dataportals en gebruikers van die data, zoals analisten en webapplicaties.

<figure><img alt="Schematisch overzicht van de verschillende datastromen" src="../images/diagram_crow_api_overzicht.drawio.svg"/>
<figcaption>Schematisch overzicht van de verschillende datastromen</figcaption>
</figure>

| ID      | Omschrijving                                  |
| ------- | --------------------------------------------- |
| API 1   | Van stalling naar exploitant                  |
| API 2   | Van exploitant naar MaaS-provider             |
| API 3   | Van exploitant / straatteller naar dataportal |
| API 4   | Van dataportal naar analist                   |
| API 5   | Van dataportal naar webapplicaties            |
| API 6   | Van exploitant naar exploitatietools          |
| {.data} |

Deze datastandaard is in eerste instantie bedoeld voor de API's 3, 4 en 5.
Het kan ook als leidraad dienen voor de overige datastromen.

De bronnen gebruikt voor de informatiemodellering zijn:
_SPDP_ (Gegevensstandaard voor autoparkeren),
[_Mobivoc_][mobivoc] (Vocabulair voor mobiliteitsgegevens) en
[_Open Velopark_][velopark] (_Bicycle Parking Facility Vocabulary_)
Daarmee is geprobeerd een informatiemodel op te stellen dat duurzaam gebruikt kan worden:
uitbreidingen zijn mogelijk, mappings naar andere modellen voor gegevensdeling zijn eveneens mogelijk.

[velopark]: https://data.velopark.be/openvelopark/vocabulary
[mobivoc]: http://schema.mobivoc.org/
