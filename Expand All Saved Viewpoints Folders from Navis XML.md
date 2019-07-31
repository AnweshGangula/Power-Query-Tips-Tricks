## Expand All Function:

Make sure the name of the function is "ExpandAll"

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
    
## Rename Columns function:
    
In the above function (ExpadAll), we are using another function at the last step (OutputTabe) called "Rename Columns" to rename and sort the View/Viewfolder columns on their folder order respectively. 

Below is the code for the function:
    
    let
    Source = (TableName as table) =>
     let
        ReorderColumns = Table.ReorderColumns(TableName,List.Sort(Table.ColumnNames(TableName),each Text.Length(_))),
        RenameColumns = Table.TransformColumnNames(ReorderColumns, each if Text.Contains(_,"view.") then "View " & Number.ToText(Number.RoundDown(((Text.Length(_)-29)/10))) else if Text.Contains(_,"viewfolder.") then "Viewfolder " & Number.ToText(Number.RoundDown(((Text.Length(_)-24)/10))) else "remove"),
        RemoveColumns = Table.RemoveColumns(RenameColumns, List.Select(Table.ColumnNames(RenameColumns), each Text.Contains(_, "remove")))
     in 
         RemoveColumns
    in
        Source
