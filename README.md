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

### section object - een parkeervoorziening, bijvoorbeeld een verzameling nietjes
| Field               | Type                | Required               | Description                                                  |
| ------------        | ------------------- | ---------------------- | ----------------------------------------------------------   |
| id                  | string              | no                     | Binnen locatie een unieke id                                 |
| parkingCapacity     | number              | no                     | Totaal aantal plekken                                        |
| vacantSpaces        | number              | no                     |                                                              |
| space               | Space Object        | no                     |                                                              |
| occupation          | Occupation[]        | no                     | Verzameling van Occupation-objecten                          |
| sections            | Section[]           | no                     | Verzameling van Sections binnen Sections-objecten            |

### space object - definiëring van een plek aan de hand van properties
| Field                | Type              | Required               | Description                                                   |
| -------------------- | ----------------- | ---------------------- | ------------------------------------------------------------- |
| type                 | number            | conditionally required | SpaceTypeID (zie onder);Tenminste 1 veld dient gegeven te zijn|
| level                | number            | conditionally required | 0=laag, 1=hoog                                                |
| ?                    | ?                 | conditionally required | Nieuw te definiëren plekeigenschappen                         |
| ?                    | ?                 | conditionally required | Nieuw te definiëren plekeigenschappen                         |

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


#### spaceTypeIDs - indeling volgens Trajan 'Whitepaper fietsparkeerdrukonderzoek 1.0.pdf' p. 10
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
			"occupation": [
				{
					"vehicle": {
						"type": 1,
					},
					"n": 70
				},
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
			],
			"sections": [
				{
					"parkingCapacity": 80,
					"vacantSpaces": 10,
					"space": {
						"type": 2
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
						"type": 1,
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
			"parkingCapacity": 86,
			"vacantSpaces": 14,
			"occupation": [
				{
					"vehicle": {
						"type": 1
					},
					"n": 70
				},
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
			],
			"sections": [
				{
					"space": {
						"type": 2,
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
						"type": 1,
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

