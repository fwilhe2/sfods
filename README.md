# sfods
Simplified Flat Open Document Spreadsheet File Format
is an experiment to create a simplified version of the [open document spreadsheet](https://en.wikipedia.org/wiki/OpenDocument_technical_specification#Document_representation) file format.

The idea is to support a small subset of the features of regular spreadsheet files with a vastly simplified format.
Those files are simple enough that they can be written by hand or easily generated via programs or scripts, similar to CSV.
But other than CSV, SFODS files can have formulas and define the type of cells making it a much more capable format.

For example, consider an export of a budget which might look like this in CSV format:

```csv
Date,Expense,Amount Spent, % of Budget
2022-02-02,Eating out at the Restaurant,97.00 €,48.50%
```
This CSV file can be imported into a spreadsheet application, but this requires some manual work every time.
Converting this into a spreadsheet which can do useful stuff like calculating leftover budget is more effort than it should be.

As an alternative, consider the following xml file:

```xml
<spreadsheet>
  <table name="Expenses">
    <row>
      <cell type="string"> <text><![CDATA[Date]]></text> </cell>
      <cell type="string"> <text><![CDATA[Expense]]></text> </cell>
      <cell type="string"> <text><![CDATA[Amount]]></text> </cell>
      <cell type="string"> <text><![CDATA[% of Budget]]></text> </cell>
    </row>
    <row>
      <cell value="2022-02-02" type="date"/>
      <cell type="string"> <text><![CDATA[Eating out at the Restaurant]]></text> </cell>
      <cell value="97" type="currency" currency="EUR"/>
      <cell value="0.485" formula="of:=AMOUNT/YEARLY_BUDGET" type="percentage"/>
    </row>
  </table>
  <table name="Values">
    <row>
      <cell value="200" type="currency" currency="EUR" />
    </row>
  </table>
  <named-expressions>
    <named-range name="AMOUNT" base-cell-address="$Expenses.$C$2" cell-range-address="$Expenses.$C$2:.$C$4" />
    <named-range name="YEARLY_BUDGET" base-cell-address="$Values.$A$1" cell-range-address="$Values.$A$1" />
  </named-expressions>
</spreadsheet>
```

The file is more complicated to create, but still simple compared to other spreadsheet formats and it has the advantage that it supports formulas, named ranges and data types.
Also it does not contain the large amounts of metadata which Flat ODS files produced by LibreOffice contain.
This makes it much more suitable to put spreadsheet files in version control because the diffs between commits don't contain a lot of noise.

This format can be converted without loss into odf or xlsx files which allows opening/editing them in a spreadsheet application without the manual effort of configuring CSV import.

Additionally to xml, json and yaml can also be used to define the files.

Here's the same example in yaml:

```yaml
tables:
  - name: Expenses
    rows:
      - cells:
          - type: string
            text: Date
          - type: string
            text: Expense
          - type: string
            text: Amount
          - type: string
            text: "% of Budget"
      - cells:
          - value: 2022-02-02
            type: date
          - type: string
            text: Eating out at the Restaurant
          - value: "97"
            type: currency
            currency: EUR
          - value: "0.485"
            type: percentage
            formula: of:=AMOUNT/YEARLY_BUDGET
  - name: Values
    rows:
      - cells:
          - value: "200"
            type: currency
            currency: EUR
namedExpressions:
  namedRanges:
    - name: AMOUNT
      baseCellAddress: $Expenses.$C$2
      cellRangeAddress: $Expenses.$C$2:.$C$4
    - name: YEARLY_BUDGET
      baseCellAddress: $Values.$A$1
      cellRangeAddress: $Values.$A$1
```
