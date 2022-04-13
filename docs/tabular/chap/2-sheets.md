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

| Column                       | Relates to     | Process  | Required | Description                                                                      |
| ---------------------------- | -------------- | -------- | -------- | -------------------------------------------------------------------------------- |
| section.id                   | @id            |          |          | The URL identifier of the section.                                               |
| section.localId              | dct:identifier | ws-split |          | Any local identifier of the section.                                             |
| parent.id                    | @id            |          |          | The URL identifier of the parent section.                                        |
| parent.localId               | dct:identifier | ws-split |          | Any local identifier of the section.                                             |
| section.name                 | s:name         | lang     |          | The human-readable name of the section.                                          |
| section.layout               |                |          |          | The URL identifier of the layout protocol.                                       |
| section.beheerder            |                |          |          | The URL identifier of the ...                                                    |
| section.parkingSystemType    |                |          |          | The singular URL identifier of the parking system type for this parking section. |
| section.vehicleOwnerType     |                |          |          | The singular URL identifier of the vehicle owner type for this parking section.  |
| section.level                |                |          |          | The building level of the section.                                               |
| section.validFrom            |                |          |          | Datetime marking the start of this parking section.                              |
| section.validThrough         |                |          |          | Datetime marking the end of this parking section.                                |
| section.authority            |                |          |          | The singular URL identifier of the authority that created this section.          |
| survey.id                    | @id            |          |          | The URL identifier of the survey for which this section was measured.            |
| observation.id               | @id            |          |          | The URL identifier of the observation.                                           |
| observation.observationType  |                |          |          |
| observation.observationFrom  |                |          |          | Datetime when the observation started.                                           |
| observation.observationUntil |                |          |          | Datetime when the observation ended.                                             |
| observation.contractorId     |                |          |          | The URL identifier of the observing party.                                       |
| observation.note             |                |          |          | Any free text notes for the observation.                                         |
| occupation.id                | @id            |          |          | The URL identifier of the occupation measurement.                                |
| occuptation.measurementType  |                |          |          | The URL identifier of the measurement type.                                      |
| occupation.totalParked       |                |          |          | The number of parked vehicles in the section.                                    |
| <var>VehicleCategoryId</var> |                |          |          | The number of parked vehicles of the specified type in the section.              |

The variable <var>VehicleCategoryId</var> is replaced by the ID of the concrete vehicle categories used in the current Survey.
Unknown columns SHOULD correspond with the ID of a defined canonical vehicle.
