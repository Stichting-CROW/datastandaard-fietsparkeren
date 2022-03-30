# Usage and conventional conversion

<aside class='note'>
Deze sectie beschrijft hoe het XLSX-uitwisselformaat zich vertaalt naar het RDF-uitwisselingsformaat.
Ten behoeve van implementeerders is deze sectie voor de rest in het Engels.
</aside>

This section describes how the XSLX exchange format SHOULD be programmatically transformed into the canonical RDF exchange format.
An implementation MAY also transform it into other formats, provided it follows the conversions of this section.

## Notation

The following table describes the algorithm to convert the cell values of a row into the appropriate objects and object values.
Where multiple Transformations are described for the same ( Sheets, Column ) combination, the transformations should be processed top-to-bottom.

`if |value|:` and `elsif |value|:` and `else:` indicate conditional processing in pseudo-code.

## Conversion of Sheet `DynamicSection`

| Sheet          | Column | Target                                | Note |
| -------------- | ------ | ------------------------------------- | ---- |
| DynamicSection | ID     | if empty: Assign a new, unused `@id`. |
| DynamicSection | ID     | else: `@id`.                          |
