# Replace Multiple Strings in Power Query

below are the steps to replace/remove multiple strings from a column using Power Query. You can find the explanation of these steps in the following [blog post](https://guru--g.blogspot.com/2019/06/removereplace-multiple-values-in-column.html)
 or in th following 2 videos.
* [Replace/Substitute multiple strings](https://www.youtube.com/watch?v=O_TnEFbX7KI)
* [Remove Multiple Strings](https://www.youtube.com/watch?v=QDB_ypnHqos)
 
## Replace/Substitute string with new string
replace the values in **{"$","a"},{"¥","b"},{"¢","c"},{"£","d"}** in below step to match your strings

    = Table.TransformColumns(#"Previous Step",{{"Column Header", each List.Accumulate({{"$","a"},{"¥","b"},{"¢","c"},{"£","d"}},_,(string,replace) => Text.Replace(string,replace{0},replace{1}))}})


## Remove multiple stings
replace the values in **â¬£Â£€$** in below step to match your strings

    = Table.TransformColumns(#"Previous Step",{{"Column Header", each List.Accumulate(Text.ToList("â¬£Â£€$"),_,(String,Remove) => Text.Replace(String,Remove,""))}})
