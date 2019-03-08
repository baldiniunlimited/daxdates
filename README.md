# Date logic for DAX

This repo holds sample templates and implementations for time intelligence and
other date logic in Microsoft's BI suite, including Power BI, Power Pivot, Power
Query, and SSAS Tabular.

## PQ DimDate

[PQ DimDate](PQ%20DimDate.pbix) holds a date dimension generated entirely in M
script and forms a basis for most useful time intelligence patterns in DAX.

This file has been updated to handle alternative fiscal start months. The new version has an additional parameter to indicate the month number (of the calendar year, obviously) which is the start of a custom fiscal calendar. This version is not yet performing well enough to link into the master branch.
