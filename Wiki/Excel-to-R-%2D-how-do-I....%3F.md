A barrier to transitioning from using Excel to using R is that an experienced Excel user may wonder "how do I do that simple Excel task (e.g. summing a column) in R?" 

The purpose of this page is to explain how to do Excel tasks in R. We focus on methods that can be applied to the [R data frame](https://www.rdocumentation.org/packages/base/versions/3.6.2/topics/data.frame), using the [dplyr](https://www.rdocumentation.org/packages/dplyr/versions/0.7.8) package for data manipulations.  

dplyr logic focuses around the pipe **%>%** with shortcut ctrl+shift+m. Essentially everything on the left-hand side of **%>%** gets "piped" into the next argument. This avoids having long lines of code defining new outputs based on previous outputs.

R comes in with a built-in dataset called "iris". We'll use this for all examples so you can recreate them in your local area.

**REMEMBER:** R is case sensitive, so all references to column names/entries need to be as-is in the dataset you are looking at. [Functions exist](https://www.rdocumentation.org/packages/janitor/versions/1.2.0/topics/clean_names) that can translate all your columns to lower or snake case for ease!

| **Common Excel Task** | **Example with iris dataset** | **How to do in R dataframe (with dplyr)** |
|--|--|--|
|Select specific columns| Select only species and petal length  | `iris %>% select(Species, Petal.Length)`|
| List unique entries in field (column) | Find the unique entries for the "Species" column in iris | ` iris %>% select(Species) %>% distinct()` |
| Filter/select based on criteria | Filter for sepal length >4 and sepal width <`2.5`, but NOT "versicolor" species | ` iris %>% filter(Sepal.Length > 4 & Sepal.Width <2.5 & Species != "versicolor") ` |
| Filter for multiple criteria in same column | Filter for all "setosa" and "versicolor" species | `iris %>% filter(Species %in% c("setosa", "versicolor")`|
| If else with OR | Create new column called "size_group" based on size of petal. "Large" petals are >4 in length or >1.5 in width, everything else is "Small"| ` iris %>% mutate(size_group = if_else( Petal.Length > 4 | Petal.Width >1.5, "Large", "Small"))`|
| Multiple if else |  Create new column called "flower_price". "Setosa" species with petal length > 3 are "top band", "Versicolor species with petal length < 4 are "low band", everything else is "mid band" | `iris %>%  mutate(flower_price = case_when(Species == "setosa" & Petal.Length > 1.5 ~"top band", Species == "versicolor" & Petal.Length < 4 ~"low_band", TRUE ~ "mid_band"))`
| COUNTIF | Count the number of species if they have a petal length >1.5 | `iris %>% filter(Petal.Length > 1.5 ) %>% group_by(Species) %>% count()`|
| SUMIF | Sum petal width of species if sepal width <`3`| `iris %>% filter(Sepal.Width <3) %>% group_by(Species) %>% summarise(Petal.Width = sum(Petal.Width))`|
| VLOOKUP | Lookup to a table called "lookup" which has a list of species | `iris %>%  left_join(lookup, by.x="Species", by.y ="plant_species")`|
| Order by | Order dataset by descending petal width | `iris %>% arrange(desc(Petal.Width))` | 
