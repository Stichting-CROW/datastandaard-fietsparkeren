# Stationsstallingen (ProRail)

Dit protocol is gemaakt in 2021-2022 voor de ProRail stationstallingtelling najaar 2022.
Het beschrijft hoe gebieden ingemeten moeten worden, welke typen voertuigen worden onderscheiden en meer.

## Voertuigtypen

## Indeling ParkingFacility en Section

- Secties aggregeren per ( _Type parkeersysteem_ , _Voor voertuigtype_)

## Naamgeving ParkingFacility

De naam (`s:name`) gegeven aan een voorziening (`fp:ParkingFacility`) bestaat uit:

- ProRail Referentiesysteem Hectometerpunt [(Geocode)][geocode]
- "-"
- ProRail-stallingsnummer (aangevuld met voorloopnullen tot twee cijfers bij t/m 99 voorzieningen, tot drie cijfers bij meer dan 100 voorzieningen.)

[geocode]: https://data.overheid.nl/dataset/prorail-referentiesysteem

<aside class='example'>

```json
{
  "@type": "ParkingFacility",
  "name": "545-023.0-01" // Roosendaal
}
```

</aside>
