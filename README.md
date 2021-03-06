# Excel Templates

Excel templates is designed to build Excel spreadsheets by combining two parts:

1. A Excel file that works as a template defining layout and formatting information for the spreadsheet you're creating
2. A Clojure data in the form of maps and seqs that defines the contents to be inserted in the resulting spreadsheet.

## Why Use Excel Templates?

The spreadsheet is the preferred tool for data presentation and exploration for many users, especially in business.

Presenting data in Excel is much more compelling than sending out, for example, a Comma-Separated-Value (CSV) file. This is especially true when you add some simple formatting to accentuate structure and meaning in the data, for example by formatting numbers as dollars or percentages or by grouping together columns of related information.

## Using Excel Templates

When you use an excel template, sheets are handled row-by-row.

If data is supplied for the row, it replaces the data that was already in that row, except for nils which use the data or formula from the corresponding cell in the template. All cells use the format specified in the template sheet.

When multiple rows of data are supplied for a single row in the template, the template row is expanded into the target sheet. Each resulting row in the target has the formatting of the original template row.

If there's no data supplied for the row, the row is copied exactly (both data and formatting) from the template to the output workbook.

### Building your template

You build a template just like you'd build a regular Excel spreadsheet, but you can fill it with dummy data.

Use excel formatting commands to format cells, rows, and columns as you would like them. This includes number formatting, alignment, shading, etc. Add explanatory graphics (like text boxes, etc.) to communicate the results most clearly.

Save this template to a regular xlsx file from Excel. `excel-template` will look for templates by path name or in resources, so it's possible to include templates in you jar file by adding it to resources.

### Building the data

In your Clojure program, just create a plain old map as follows:

```clojure
{ Sheet-Name-1 { actions-for-that-sheet }
  Sheet-Name-1 { actions-for-that-sheet }
  ... }
```

Each set of actions is itself a map:

```clojure
{ row-num-1 [[replacement row 1]
             [replacement row 2]
              ... ]
  row-num-2 [[replacement row 1]
             ... ]}
```

For example, we can use the following:

```clojure
{"Squares" {3 [[1 1]
               [2 4]
               [3 9]]}}
```

To replace the second row of the spreadsheet with 3 rows each with a number and its square.

If "Squares" is the only worksheet in the template, we could simply
use the inner map:

```clojure
{3 [[1 1]
    [2 4]
    [3 9]]}
```

I show the data as vectors above, but it can actually be any seq.

### Generating a spreadsheet

To generate a new spreadsheet from code, use the `build-with-template` function, passing in three arguments:

1. The name of the input template workbook file or resource.
2. The name of the output workbook to create
3. The data to inject into the workbook.

Here is the code to create the workbook of squares discussed above:

```clojure
(require '[excel-templates.build :as excel])

(excel/build-with-template
   "squares-template.xlsx"
   "squares.xslx"
   {"Squares" {3 [[1 1]
                 [2 4]
                 [3 9]]}})
```

### Limitations

#### Formulas

Excel templates does not parse and relocate formulas on your sheets. This means that if you have a formula and you replace a single row on your template sheet with multiple data rows, the resulting formulas are likely to be wrong.

Due to limitations in POI's formula interpreter, some values of some formulas may not be updated correctly in output templates, but this should only be a problem with unusual functions.

### Compatibility

To date, Excel templates have only been tested with Clojure 1.6 and both Excel 2011 and Libre Office 4.1.3.2., but I think they should work with all recent versions of Clojure and Excel or Libre Office. If you have an problem, feel free to open an issue on the github page and I'll take a look.

## Acknowledgments

This library was created as part of a project for [Staples SparX][sparx] and they have graciously allowed me to open source it. If you're interested in a job creating great Clojure code, you should check them out.

Excel templates are built on top of the [Apache POI][poi] library which does an excellent job of creating the missing API for Excel workbooks (and other Microsoft Office products).

Christophe Grand's [enlive][enlive] library was the direct inspiration for this approach to doing templating using pure data and selectors. I wouldn't claim to have achieved the same degree of elegance as Christophe.

[sparx]: http://www.staples-sparx.com
[poi]: http://poi.apache.org
[enlive]: https://github.com/cgrand/enlive

## License

Copyright © 2014 Tom Faulhaber

Distributed under the Eclipse Public License either version 1.0 or (at
your option) any later version.
