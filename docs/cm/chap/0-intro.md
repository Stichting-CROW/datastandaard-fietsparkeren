# Introductie

Tellingen van gestalde fietsen, bijvoorbeeld om de parkeerdruk te bepalen, waarmee nieuwe stallingsvoorzieningen gemotiveerd kunnen worden, worden veelal uitvraag-specifiek vormgegeven.
De _CROW datastandaard fietsparkeren_ biedt een inmeetprotocol, een informatiemodel en een API-beschrijving om niet alleen consistente metingen te krijgen, maar ook die op een standaard manier uit te wisselen.

<figure id='fig-datastandaard-overzicht' class='overlarge'><img src="assets/conceptueel-model.drawio.svg">
<figcaption>Conceptueel model fietsparkeren.</figcaption></figure>

Bovenstaande <a href='#fig-datastandaard-overzicht'></a> toont eigenschappen van en relaties tussen de verschillende concepten in dit model.
De gekleurde kaders geven globaal aan uit welke "onderdelen" de datastandaard fietsparkeren bestaat.
Merk op dat de meeste relaties binnen een kader liggen.

<!--
  In sync houden:
  - Kopje in SVG en kopje in onderstaande
  - Link naar juiste paragraaf (ReSpec helpt)
-->

1. [Metadata onderzoek](#onderzoeken-en-inwinningen):
   Omvat de concepten omtrent het parkeeronderzoek, opdrachtgevers en opdrachtnemers en onderzoeksgebieden.
1. [Observatie en meting](#tellingen-metingen-en-capaciteit):
   Omvat bezettingstelling en capaciteitsmeting, in overeenstemming met [[iso-19156-2011]] en [[ogc-om]].
1. [Stalling en parkeervoorziening](#stallingen-en-stallingsvoorzieningen):
   Beschrijft de fysieke indeling van de stalling in subsecties, parkeersystemen en individuele plekken.
   Deze beschrijving is in overeenstemming met [[crow-291]].
1. [Voertuig en voertuigstaat](#fietsen-en-andere-voertuigen):
   Omvat één fysiek objecttype en verschillende enumeratielijsten om een voertuig uitgebreid te beschrijven.
