# Remove multiple space in text
This code lets you choose a column from a table and remove multiple spaces from in between the text.

This is advanced version of [Text.Trim](https://docs.microsoft.com/en-us/powerquery-m/text-trim)

    let
        Source = (column_to_edit as list) => let
                main_Step = Table.FromList(column_to_edit),
                #"Split Column by Delimiter" = Table.SplitColumn(main_Step, "Column1", Splitter.SplitTextByDelimiter(" ", QuoteStyle.Csv)),
                #"Unpivoted Columns" = Table.UnpivotOtherColumns(#"Split Column by Delimiter", {}, "Attribute", "Value"),
                #"Filtered Rows" = Table.SelectRows(#"Unpivoted Columns", each [Value] <> null and [Value] <> ""),
                #"Pivoted Column" = Table.Pivot(#"Filtered Rows", List.Distinct(#"Filtered Rows"[Attribute]), "Attribute", "Value"),
                #"Remaining Column names" = Table.ColumnNames(#"Pivoted Column"),
                #"Merged Columns" = Table.CombineColumns(#"Pivoted Column",#"Remaining Column names",Combiner.CombineTextByDelimiter(" ", QuoteStyle.None),"Merged")
            in
                #"Merged Columns"
    in
        Source

## Issues:
But this does not work when you use this in the same query by invoking custom function.
This will only work if you invoke it as a new query.
