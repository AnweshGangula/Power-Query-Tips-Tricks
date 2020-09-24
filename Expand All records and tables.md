## Expand All Function:

Make sure the name of the function is "ExpandAll"

Below functions uses another function called "Rename Columns" in the last step (OutputTable) which you can [find here](https://github.com/Gangula2/Power-Query-Tips-Tricks/blob/master/Rename%20Multiple%20Columns.md)

    let
        Source = (TableToExpand as table, optional ColumnNumber as number) =>
        let
         ActualColumnNumber = if (ColumnNumber=null) then 0 else ColumnNumber,
         ColumnName = Table.ColumnNames(TableToExpand){ActualColumnNumber},
         ColumnContents = Table.Column(TableToExpand, ColumnName),
         ColumnsToExpand = List.Select(List.Distinct(List.Combine(List.Transform(ColumnContents, each if _ is table then Table.ColumnNames(_) else {}))), each (_ = "view" or _ = "viewfolder" or _ = "Attribute:name")),
         NewColumnNames = List.Transform(ColumnsToExpand, each ColumnName & "." & _),
         CanExpandCurrentColumn = List.Count(ColumnsToExpand)>0,
         ExpandedTable = if CanExpandCurrentColumn then Table.ExpandTableColumn(TableToExpand, ColumnName, ColumnsToExpand, NewColumnNames) else TableToExpand,
         NextColumnNumber = if CanExpandCurrentColumn then ActualColumnNumber else ActualColumnNumber+1,
         OutputTable = if NextColumnNumber>(Table.ColumnCount(ExpandedTable)-1) then #"Rename Columns"(#"ExpandedTable") else ExpandAll(ExpandedTable, NextColumnNumber)
    /* 
    1. The above step is a recursive step, where it checks if there's any columns that can be expanded. If there are no such columns, it refers the "Rename Columns" function.
    2. The reason we created another function is because the last step is a recursive step, we cannot add more steps after steps after OutputTable.
    3. Refer to the following Stackoverflow question: https://stackoverflow.com/questions/57222986/power-query-recursive-function-add-more-steps-after-the-recursive-step 
    */

        in
         OutputTable
    in
        Source
