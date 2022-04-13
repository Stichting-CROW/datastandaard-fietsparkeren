# Notation

Not all tabular formats are alike in capabilities.
Therefore, the specifications below are flexible in describing how and where the information is stored.
An implementation SHOULD follow the specification in how that information should be translated into the standard data structures.

## Sheets, Layers, Files

In a XLSX-file, different column layouts MUST be in different Sheets (not in-sheet Tables).
In a Shapefile or Geopackage, different column layouts MUST be in different Layers.
When using CSV files, each column layouts MUST belong in a different file.

## Datatypes

The tabular formats differ strongly in their native (and naïve) support for cell-level datatypes.
Any exchange format MAY only support the string datatype for all values.
