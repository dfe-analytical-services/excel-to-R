A barrier to transitioning from using Excel to using R is that an experienced Excel user may wonder "how do I do that simple Excel task (e.g. summing a column) in R?" 

The purpose of this page is to explain how to do Excel tasks in R. We focus on methods that can be applied to the [R data frame](https://www.rdocumentation.org/packages/base/versions/3.6.2/topics/data.frame), using the [dplyr](https://www.rdocumentation.org/packages/dplyr/versions/0.7.8) package for data manipulations.  

dplyr logic focuses around the "pipe" **%>%** with shortcut ctrl+shift+m. Essentially everything on the left-hand side of **%>%** gets "piped" into the next argument. This avoids having long lines of code defining new outputs based on previous outputs.

R comes in with a built-in dataset called "iris". We include examples applied to this dataset in the table below so that you can recreate them in your local area.

**REMEMBER:** R is case sensitive, so all references to column names/entries need to be as-is in the dataset you are looking at. [Functions exist](https://www.rdocumentation.org/packages/janitor/versions/1.2.0/topics/clean_names) that can translate all your columns to lower or snake case for ease!


<h2>How to do common Excel tasks in R:</h2>

| **Common Excel Task** | **How to do in R dataframe (with dplyr)** | **Example with iris dataset** |
|--|--|--|
| List unique entries in field (column) | `unique(dfname$fieldname)`<br/><br/>or if factors, can do: `levels(dfname$fieldname)` |Find the unique entries for the "Species" column in iris<br/><br/>` iris %>% select(Species) %>% distinct()` |
| Filter/select based on criteria | `filter(dfname, fieldname == value)` <br/><br/>`filter(dfname, grepl("search_term",field_name)) #filters based on containing string` <br/><br/> `filter(dfname, (URN == "141006" | URN == "138262" | URN == "141164")) # example of filtering with OR` | Filter for sepal length >4 and sepal width <`2.5`, but NOT "versicolor" species<br/><br/> `iris %>% filter(Sepal.Length > 4 & Sepal.Width <2.5 & Species != "versicolor")`|
| Select specific columns| `select(colname1, colname2,….)` | Select only species and petal length <br/><br/>  `iris %>% select(Species, Petal.Length)`|  
| If else with OR | e.g. `mutate(var_name = ifelse(ks2_type_1718 == "AC" | ks2_type_1718 == "ACC",1,0))`  | Create new column called "size_group" based on size of petal. "Large" petals are >4 in length or >1.5 in width, everything else is "Small"<br/><br/>  ` iris %>% mutate(size_group = if_else( Petal.Length > 4 | Petal.Width >1.5, "Large", "Small"))`|
| Multiple if else ||  Create new column called "flower_price". "Setosa" species with petal length > 3 are "top band", "Versicolor species with petal length < 4 are "low band", everything else is "mid band" <br/><br/> `iris %>%  mutate(flower_price = case_when(Species == "setosa" & Petal.Length > 1.5 ~"top band", Species == "versicolor" & Petal.Length < 4 ~"low_band", TRUE ~ "mid_band"))`
| CountIFs (count of occurences of each entry in column) | `dfname %>% count(fieldname)` |Count the number of species if they have a petal length >1.5 <br/><br/> `iris %>% filter(Petal.Length > 1.5 ) %>% group_by(Species) %>% count()`|
| Sum a column  | `sum(dfname$fieldname)` |
| SUMIF || Sum petal width of species if sepal width <`3`<br/><br/> `iris %>% filter(Sepal.Width <3) %>% group_by(Species) %>% summarise(Petal.Width = sum(Petal.Width))`|
| VLOOKUP || Lookup to a table called "lookup" which has a list of species <br/><br/> `iris %>%  left_join(lookup, by.x="Species", by.y ="plant_species")`|
|Arrange / sort in order| `arrange(fieldname)`<br/><br/>`arrange(match(band,c("IL","OL","F","R")) # specific order for fieldname band`|Order dataset by descending petal width <br/><br/>  `iris %>% arrange(desc(Petal.Width))` |
|Maximum of data values in a given <em>row</em>|`pmax(fieldname1,fieldname2,fieldname3)`|
|Pivot |`pivot_longer()` "lengthens" data, increasing the number of rows and decreasing the number of columns. <br/><br/>The inverse transformation is `pivot_wider()`.|
|Rename columns |`rename(new_field_name = "current_field_name") # current field name must be in quote marks`|
|Convert data to numeric format | e.g. `transform(fieldname = as.numeric(fieldname))` |
| Bind rows from different dataframes  |bind_rows, rbind <br/>e.g. `df1 <- df1 %>%  bind_rows(df2)`<br/>  note that fields must have same names in order to "bind"|
|Concatenate / Split  | e.g. <br/>`unite(LAcom_URN, LA_com, URN, sep = "_", remove = FALSE)` <br/><br/> `separate(LAcom_URN, into = c('LA_com','URN'), sep="_", remove = FALSE)`|
|group_by with summarise | `group_by(domicile, gender) %>% summarise(count = sum(count))` |

<h2>Other useful tasks / functions in R:</h2>

| **Task** | **How to do in R dataframe (with dplyr)**|
|--|--|
|Find dimensions (size) of dataset| `dim(dataset)`|
|list types for each attribute|`sapply(dataset, class)`<br/><br/>`str(df_name)`|
|Replace NA with zero|`replace(., is.na(.), 0)`|
|Drop all rows with NA in particular column|`drop_na(fieldname)`|
|Merge/join (note: you can use left_join / right_join etc instead of merge)|Inner join: `merge(x = df1, y = df2, by = "CustomerId") #Return only the rows in which the left table have matching keys in the right table.` <br/><br/> Outer join: `merge(x = df1, y = df2, by = "CustomerId", all = TRUE) #Returns all rows from both tables, join records from the left which have matching keys in the right table.`<br/><br/>  Left outer:` merge(x = df1, y = df2, by = "CustomerId", all.x = TRUE) #Return all rows from the left table, and any rows with matching keys from the right table.`<br/><br/> Right outer: `merge(x = df1, y = df2, by = "CustomerId", all.y = TRUE) #Return all rows from the right table, and any rows with matching keys from the left table.` <br/><br/>Cross join: `merge(x = df1, y = df2, by = NULL)`|
|Write to CSV|e.g.`write_csv(df_name, "school_estate/data/CMM.csv")`<br/>|

<h2>Useful other links / resources to consider:</h2>

Presentation by Jon Tecwyn on some of the most common dplyr "verbs": [dplyr_basic_verbs.pptx](/.attachments/dplyr_basic_verbs-032e312a-d732-4866-b303-a4f7e954b571.pptx)

data.table and dplyr tour: https://atrebas.github.io/post/2019-03-03-datatable-dplyr/

A short list of basic, commonly-used Excel formulas and their (base) R counterparts. https://www.rforexcelusers.com/excel-r-function-formula-list/

David Sands' Coffee & Coding on SQL/Excel to R: https://github.com/dfe-analytical-services/coffee-and-coding/tree/master/20181121_sql-and-excel-to-r 

