# Date logic for DAX

This repo holds sample templates and implementations for time intelligence and
other date logic in Microsoft's BI suite, including Power BI, Power Pivot, Power
Query, and SSAS Tabular.

## PQ DimDate

[PQ DimDate](PQ%20DimDate.pbix) holds a date dimension generated entirely in M
script and forms a basis for most useful time intelligence patterns in DAX.

### Notes on the date dimension

Useful columns:

- Index columns capture the notion of "periods since the start of the calendar".
  These hold the invariant that, given a period `N`, the prior period is `N-1`
  and the subsequent period is `N+1`. These are the basis for all date shifting
  logic if you choose to abstain from using the time intelligence primitives in
  the DAX language. Note that year and date can serve as their own indices (they
  hold the invariant). These columns also serve as the sort-by columns for their
  display columns. Index columns are defined for the following time
  granularities.
  - Quarter
  - Month
  - Week
- Offset columns capture the concept of "periods from today's date", where
  "today" is defined by `DateTime.LocalNow`. It is likely useful to replace this
  with the most recent date of data in your fact table. Obviously, these will
  depend on regular dataset refreshes to actually remain current. These are most
  useful in reporting filters (report- page- or visual-level or in slicers), as
  they allow a literal value to be selected, but provide "dynamic" behavior as
  the data in the dataset changes. This is especially useful for things that
  can't be easily expressed in Power BI's relative date slicers, e.g. current
  quarter in an offset fiscal calendar, or if you replace `DateTime.LocalNow`
  with your most recent fact data, a rolling period of dates with populated
  data. Negative offsets are in the past and positive offsets are in the future.
  You may prefer textual fields that read as, e.g. "5 days ago". Construction of
  such a display field is left as an exercise for the consumer. Offsets are
  defined for the following time granularities:
  - Year
  - Quarter
  - Month
  - Week
  - Date
- TD fields are again relative to "today" (see above in the offset section for
  using your most recent fact date instead). These are populated for all
  parallel periods. E.g. if the dataset is refreshed on March 15, 2019, then YTD
  is populated for all dates that fall between January 1 and March 15
  (inclusive), for all years, not just 2019. This becomes very useful for
  reporting filters. A common example might be `YearOffset IN {0, -1} &&
  YTD=TRUE`. This would show dates in the current and prior years, but only up
  to "today" for each year.
- DayOf fields indicate a date's position in various periods. These fields
  handle leap years very specifically. In short, a given date will always have
  the same DayOfYear and DayOfQuarter regardless of leap year status. March 1 is
  always `DayOfYear=60` and `DayOfQuarter=60`, even in non-leap years, when it
  would normally be counted as 59 for each of these. Thus, comparing DayOfYear
  across years gets you to the same relative date.

### Fiscal calendars

This template can handle fiscal years with an offset start date, which otherwise
conform to the standard calendar year (i.e. your calendar contains months which
start and end with the standard calendar months - quarters need not align). The
start month can be set with the FiscalStartMonth parameter. A value of 1
indicates there is no fiscal calendar, which will omit all fiscal attributes. A
value of 2-12 will trigger the fiscal logic. FiscalStartMonth values outside
1-12 are not supported. When fiscal logic is indicated, additional fields are
populated to support fiscal analysis, which align with the definitions above.

