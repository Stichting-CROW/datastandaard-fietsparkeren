## Events

De datastandaard voorziet ook in communicatie over stallingsevents. 

```
POST /event/new
POST /event/acb123def456/add
```
```json
{
  "id": "...",
  "type": "x"
}
```


| ID | Event.originType | 
| -- | -- |
| m | Medewerker van een stalling, teller, handhaver |
| s | Geautomatiseerde sensor van een kluisdeur, stallingsrek, camera |
| e | Eigenaar zelf |

| ID | Event.updateType | 
| -- | -- |
| i | in, toevoeging, aanmelding, check-in |
| o | afmelding, uitloggen, check-uit -> is dat |
| x | status veranderd (onbekend of in/uit) |

| ID | Event.type | 
| -- | -- | -- |
| s | stalling |
| h | handhaving, waarschuwing; bv. hinderlijke plaatsing | -> link parallel met DAF |
| x | handhaving, verwijdering; bv. naar fietsdepot |
| v | vermissing |

<aside class='note' title='notities'>

- is vermissing, verwijdering duidelijk genoeg wat betreft `updateType`? 
- welke gegevens zijn te gevoelig?
- bewaarduur
- voor één fiets moet dataportaal bijhouden wat dier status is, met opvolgende POSTs of juist dat aan de serverkant query'en

</aside>