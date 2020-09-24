# Rename Columns function:
    
In the other function ([ExpadAll](https://github.com/Gangula2/Power-Query-Tips-Tricks/blob/master/Expand%20All%20records%20and%20tables.md)), we are using another function at the last step (OutputTabe) called "Rename Columns" to rename and sort the View/Viewfolder columns on their folder order respectively. 

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
