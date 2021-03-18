A barrier to transitioning from using Excel to using R is that an experienced Excel user may wonder "how do I do that simple Excel task (e.g. summing a column) in R?" 

The purpose of this page is to explain how to do Excel tasks in R. We focus on methods that can be applied to the [R data frame](https://www.rdocumentation.org/packages/base/versions/3.6.2/topics/data.frame), using the [dplyr](https://www.rdocumentation.org/packages/dplyr/versions/0.7.8) package for data manipulations.  


| **Common Excel Task** | **How to do in R dataframe (with dplyr)** |
|--|--|
| List unique entries in field (column) | unique(dfname\$fieldname)<br/><br/>or if factors, can do: levels(dfname\$fieldname) |
| Filter/select based on criteria | filter(dfname, fieldname == value) <br/><br/>filter(dfname, grepl("search_term",field_name)) # filters based on containing string<br/><br/>filter(dfname, (URN == "141006" \| URN == "138262" \| URN == "141164")) # example of filtering with OR|
| Select specific columns|   |
|   |  |
| If else with OR |  |
| Countifs |  |
| List unique entries in field (column) |  |


