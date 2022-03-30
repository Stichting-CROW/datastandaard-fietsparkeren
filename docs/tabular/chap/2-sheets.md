# Column Definitions

## Layer `SurveyArea`

| Column                    | Relates to | Description |
| ------------------------- | ---------- | ----------- |
| surveyarea.id             |
| surveyarea.localId        |
| surveyarea.geolocation    |
| surveyarea.parentId       |
| surveyarea.parentLocalId  |
| surveyarea.validFrom      |
| surveyarea.validThrough   |
| surveyarea.authority      |
| surveyarea.name           |
| surveyarea.surveyAreaType |

## Layer `Survey`

| Column                     | Relates to | Description |
| -------------------------- | ---------- | ----------- |
| survey.id                  |
| survey.localId             |
| survey.name                |
| survey.authority           |
| survey.contractors         |
| survey.surveyAreas.id      |
| survey.surveyAreas.localId |

The reference to
the dataset license,  
what measurement protocol was used, and
which vehicle categories were used
cannot be indicated in the tabular exchange format.

## Layer `ParkingFacility_static`

| Column                              | Relates to | Description |
| ----------------------------------- | ---------- | ----------- |
| parkingfacility.id                  | ...        | Identifier  |
| parkingfacility.localId             |
| parkingfacility.owner               |
| parkingfacility.geolocation         |
| parkingfacility.name                |
| parkingfacility.locationFeatureType |
| parkingfacility.validFrom           |
| parkingfacility.validThrough        |
| parkingfacility.authority           |

## Layer `Section_static`

| Column                    | Relates to | Description |
| ------------------------- | ---------- | ----------- |
| section.id                |
| section.localId           |
| parkingfacility.id        |
| parkingfacility.localId   |
| section.name              |
| section.layout            |
| section.geolocation       |
| section.parkingSystemType |
| section.vehicleOwnerType  |
| section.level             |
| section.validFrom         |
| section.validThrough      |

## Layer `DynamicSection`

| Column                       | Relates to | Description |
| ---------------------------- | ---------- | ----------- |
| section.id                   |
| section.localId              |
| parent.id                    |
| parent.localId               |
| section.name                 |
| section.layout               |
| section.beheerder            |
| section.parkingSystemType    |
| section.vehicleOwnerType     |
| section.level                |
| section.validFrom            |
| section.validThrough         |
| section.authority            |
| survey.id                    |
| observation.id               |
| observation.observationType  |
| observation.observationFrom  |
| observation.observationUntil |
| observation.contractorId     |
| observation.note             |
| occupation.id                |
| occuptation.measurementType  |
| occupation.totalParked       |
| <var>VehicleCategoryId</var> |

The variable <var>VehicleCategoryId</var> is replaced by the ID of the concrete vehicle categories used in the current Survey.
Unknown columns SHOULD correspond with the ID of a defined canonical vehicle.
