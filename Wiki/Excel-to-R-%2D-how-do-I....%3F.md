A barrier to transitioning from using Excel to using R is that an experienced Excel user may wonder "how do I do that simple Excel task (e.g. summing a column) in R?" 

The purpose of this page is to explain how to do Excel tasks in R. We focus on methods that can be applied to the [R data frame](https://www.rdocumentation.org/packages/base/versions/3.6.2/topics/data.frame), using the [dplyr](https://www.rdocumentation.org/packages/dplyr/versions/0.7.8) package for data manipulations.  


| **Common Excel Task** | **How to do in R dataframe (with dplyr)** |
|--|--|
| List unique entries in field (column) | unique(dfname\$fieldname)<br/><br/>or if factors, can do: levels(dfname\$fieldname) |
| Filter/select based on criteria | filter(dfname, fieldname == value) <br/><br/>filter(dfname, grepl("search_term",field_name)) # filters based on containing string<br/><br/>filter(dfname, (URN == "141006" \| URN == "138262" \| URN == "141164")) # example of filtering with OR|
| Select specific columns| select(colname1, colname2,….) |   
| If else with OR | e.g. mutate(var_name = ifelse(ks2_type_1718 == "AC" \| ks2_type_1718 == "ACC",1,0))  |
| Countifs (count of occurences of each entry in column) | dfname %>% count(fieldname) |
| Sum a column  | sum(dfname$fieldname) |
|Arrange / sort in order| arrange(fieldname)<br/><br/>arrange(match(band,c("IL","OL","F","R")) # specific order for fieldname band|
|Maximum of data values in a given <em>row</em>|pmax(fieldname1,fieldname2,fieldname3)|
|Pivot |pivot_longer() "lengthens" data, increasing the number of rows and decreasing the number of columns. <br/><br/>The inverse transformation is pivot_wider().|


<h3>Useful other links / resources to consider:</h3>

https://atrebas.github.io/post/2019-03-03-datatable-dplyr/

https://www.rforexcelusers.com/excel-r-function-formula-list/

https://www.r-bloggers.com/r-for-excel-users-pivot-tables-vlookups-in-r/

https://github.com/dfe-analytical-services/coffee-and-coding/tree/master/20181121_sql-and-excel-to-r 

