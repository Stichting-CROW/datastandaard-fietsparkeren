# Datastandaard fietsparkeren

Dit document beschrijft het dataformaat van de Datastandaard Fietsparkeren. De eerste versie is een ontwerp, gebaseerd op het SPDP-formaat, dat beoogt voor zowel bewaakte stallingen als straattellingen te kunnen worden gebruikt.


### facility/location object
| Field               | Type                | Required               | Description                                                  |
| ------------------- | ------------------- | ---------------------- | ------------------------------------------------------------ |
| id                  | string              | yes                    | Een uuid, random of eventueel samengesteld                   |
| timestamp           | ISO8601 timestamp   | yes                    | UTC timestamp, bijvoorbeeld 2020-07-02T11:14:00Z             |
| parkingCapacity     | number              | no                     | Totaal aantal plekken                                        |
| vacantSpaces        | number              | no                     | Aantal vrije plekken                                         |
| sections            | Section[]           | no                     | Verzameling van section objecten                             |



### section object
| Field               | Type                | Required               | Description                                                  |
| ------------        | ------------------- | ---------------------- | ----------------------------------------------------------   |
| id                  | string              | no                     | Binnen locatie een unieke id                                 |
| parkingCapacity     | number              | no                     | Totaal aantal plekken                                        |
| vacantSpaces        | number              | no                     |                                                              |
| subsections         | Subsection[]        | no                     | Verzameling van Subsection-objecten                          |


### subsection object
| Field               | Type                | Required               | Description                                                  |
| ------------        | ------              | ---------------------- | ----------------------------------------------------------   |
| id                  | string              | no                     | Binnen sectie een unieke id                                  |
| parkingCapacity     | number              | yes                    | Totaal aantal plekken                                        |
| vacantSpaces        | number              | no                     |                                                              |
| space               | Space Object        | no                     |                                                              |
| occupation          | Occupation[]        | no                     | Verzameling van Occupation-objecten                          |


### space object
| Field                | Type                  | Required               | Description                                                  |
| -------------------- | --------------------  | ---------------------- | ------------------------------------------------------------ |
| type                 | Enum('nietje,rek,...) | conditionally required | Tenminste 1 veld van een space-object dient gegeven te zijn  |
| level                | number                | conditionally required | 0=laag, 1=hoog                                               |
| ?                    | ?                     | conditionally required | Nieuw te definiëren plekeigenschappen                        |
| ?                    | ?                     | conditionally required | Nieuw te definiëren plekeigenschappen                        |


### occupation object
| Field                | Type               | Required               | Description                                                  |
| -------------------- | ------------------ | ---------------------- | ------------------------------------------------------------ |
| vehicle              | Vehicle object     | no                     | Leeg betekent dat het voertuigtype niet bekend is            |
| n                    | number             | yes                    | Aantal gestalde voertuigen                                   |


### vehicle object
| Field                | Type               | Required               | Description                                                  |
| -------------------- | ------------------ | ---------------------- | ------------------------------------------------------------ |
| type                 | number             | conditionally required | Voertuigtype; Tenminste 1 veld dient gegeven te zijn         |
| propulsion           | number             | conditionally required | Aandrijving                                                  |
| width                | number             | conditionally required | breedtecategorie                                             |
| ?                    | ?                  | conditionally required | Nieuw te definiëren plekeigenschappen                        |
| ?                    | ?                  | conditionally required | Nieuw te definiëren plekeigenschappen                        |


### Voetuigeigenschappen volgens wettelijke voettuigcategorie, naar type aandrijving en naar breedte, zoals beschreven in 2019.09.24dataformaatfietstellingenv2.10_fietstypen.pdf pagina's 16 en 17

#### vehicle.type
| ID | Voertuigtype          | Omschrijving                                                                   |
| -- | --------------------- | ----------------- ------------------------------------------------------------ |
| 1  | Fiets                 | Geen kenteken en geen verzekeringsplaatje                                      |
| 2  | Snorfiets             | kentekenplaat is blauw, met een wit kader, wit opschrift en een hologram       |
| 3  | Bromfiets             | kentekenplaat is geel, met een zwart kader, zwart opschrift en een hologram    |
| 4  | Motor                 | NL geel of blauw, internationaal anders                                        |
| 5  | Gehandicaptenvoertuig | geen kenteken, wel verzekeringsplaatje                                         |
| 6  | Bijzondere bromfiets  | geen kenteken, wel verzekeringsplaatje                                         |
| 7  | Landbouwvoertuig      | nog geen kenteken                                                              |
| 8  | Voetganger            |                                                                                |

#### vehicle.propulsion
| ID | Aandrijving           | Omschrijving                                                                   |
| -- | --------------------- | ----------------- ------------------------------------------------------------ |
| 1  | Spierkracht           | bv traditionele fiets of voetganger                                            |
| 2  | Elektrische hulpmotor | bv e-fiets, speed pedelec                                                      |
| 3  | Alleen elektrisch     | bv e-bromfiets                                                                 |
| 4  | Brandstof hulpmotor   | bv Sparta-met                                                                  |
| 5  | alleen brandstof      | bv traditionele bromfiets                                                      | 

#### vehicle.width
| ID | Breedte               | Omschrijving                                                                   |
| -- | --------------------- | ----------------- ------------------------------------------------------------ |
| 1  | 0 – 0,75m             | bijvoorbeeld: fiets op twee wielen                                             |
| 2  | 0,75 – 1,5m           | bijvoorbeeld: bakfiets of bromfiets                                            |
| 3  | > 1,50m               | bijvoorbeeld: personenauto                                                     |


#### Example:

```json
{
	"id": "990214A1-69FC-4B3F-8E5C7733F97CB8CF",
	"timestamp": "2020-06-01T13:45:00",
	"parkingCapacity": 140,
	"vacantSpaces": 50,
	"sections": [
		{
			"id": "begane_grond",
			"parkingCapacity": 140,
			"vacantSpaces": 110,
			"subsections": [
				{
					"parkingCapacity": 80,
					"vacantSpaces": 10,
					"space": {
						"type": "nietje"
					},
					"occupation": [
						{
							"vehicle": {
								"type": 1,
							},
							"n": 70
						}
					]
				},
				{
					"parkingCapacity": 60,
					"vacantSpaces": 40,
					"space": {
						"type": "rek",
						"level": 0
					},
					"occupation": [
						{
							"vehicle": {
								"type": 1
							},
							"n": 18
						},
						{
							"vehicle": {
								"type": 2, 
								"propulsion": 2
							},
							"n": 2
						}
					]
				},
			]
		},
		{
			"id": "verdieping_1",
			"subsections": [
				{
					"space": {
						"type": "nietje",
					},
					"parkingCapacity": 80,
					"vacantSpaces": 10,
					"occupation": [
						{
							"vehicle": {
								"type": 1
							},
							"n": 70
						}
					]
				},
				{
					"spaceType": {
						"type": "rek",
						"level": 0
					},
					"parkingCapacity": 6,
					"vacantSpaces": 4,
					"occupation": [
						{
							"vehicle": {
								"type": 1
							},
							"n": 1
						},
						{
							"vehicle": {
								"type": 2,
								"propulsion": 2
							},
							"n": 1
						}
					]
				}
			]
		}
	]
}
```

