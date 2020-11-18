#  Introduction

R Shiny is used extensively within the department to showcase data from a variety of sources, both from static Excel-based files as well as dynamic databases.

The value of R shiny is in its customisability - whilst pre-built programs/packages such as Power BI, Tableau, Microstrategy Analytics and others offer a foundation upon which to work at high speed, they are proprietary and require licensing, as well as offering less freedom in how to manipulate data, and indeed the data sources accepted.

R Shiny in conjunction with R connect offers developers a rapid way of loading data from source to end users without requiring users to install additional software. In addition, R shiny is just one aspect of R, a statistical programming language that continues to develop at speed due to being open source. This means that R shiny offers a far wider variety of analyses and design elements (e.g. machine learning outputs, interactive charts, highly customised layouts), it only requires a developer to install the requisite package.

# Installation and Training

R shiny can be installed in the same way as any other R package. In the R command line type:

`install.packages("R shiny")`

Since R Shiny is open source, there are a wide variety of pages offering a run through of the fundamental basics, as well as thousands of blogs discussing how to run particular elements and format them in particular ways. There is far too much information to include in a single wiki. As a starter for ten, we recommend:

1) [The R Studio R shiny Tutorial](https://shiny.rstudio.com/tutorial/)
2) [The Swirl Package](https://swirlstats.com/) - Particularly helpful if you want to increase your understanding of R more generally as well.
3. [The original R studio R Shiny tutorial](https://rstudio.github.io/shiny/tutorial/#hello-shiny) - Now deprecated


# R shiny app setup

Once you've reviewed the shiny tutorials above, you should be more comfortable with what global, server, and ui.R scripts are doing. Any data links to databases should be referenced in global, whilst server is the part of the app that sits on a user's local machine, showing them a personalised view (subject to their filters/system settings etc), and ui defines how the webpage looks.

Below, a copy of our package calls used within JRT are visible. These will be the driving force behind your app, and will likely all be required by some or all parts of your app for it to function. Whilst I've commented the names of the packages and what they're designed for, some are discussed in more detail in the subsequent sections.

```
# shiny app development and appearance
library(shiny) # R shiny
library(shinyjs) # R shiny js package
library(shinydashboard) # R shiny dashboard
library(shinyLP) 

library(shinyBS) # R shiny dashboard
library(shinycssloaders) # CSS
library(huxtable) # Latex and HTML tables
library(shinyhelper) # Helper buttons
library(config) # manage environment specific config values


# data import and manipulation
library(odbc) # Database connections
library(DT) # DT
library(dplyr) # Data manipulation within the R environment
library(dbplyr) # Data manipulation with databases
library(magrittr)
library(tidyverse) # a comprehensive collection of data manipulaton packages
library(lubridate) # Conversion of date variables into a variety of formats
library(zoo) # Useful for handling time series data
library(reshape2) # Allows the restructuring of data from wide to long-format (and vice versa)
library(stringi) # Allows manipulation of strings/text/natural language processing tools
library(data.table) # Allows fast aggregation of large (100Gb) datasets as well as joins and modification of created tables

# Plotting
library(scales) # a package for customising and handling axes and labels within plots
library(plotly) # Interactive charts within R shiny

# Data Outputs
library(openxlsx) # Creation of XLSX files outputs

```

# Loading from databases

## The config.yml file

This is more of an optional addition to any R shiny app rather than a must-have. Essentially, it configures any subsequent connections to a database you may create, allowing a specific keyword to be referenced rather than necessitating each connection to be referenced when needed.

A copy of our config.yml file is below:

```
default:
  amp_jrp:
    driver: "SQL Server Native Client 11.0"
    server: "T1PRANMSQL\\SQLPROD,60125"
    database: "MA_DS_S_JRP"
    uid: ""
    pwd: ""
    trusted: "Yes"
    encoding: "LATIN1"
    
production:
  amp_jrp:
    driver: "ODBC Driver 13 for SQL Server"
    server: "T1PRANMSQL.ad.hq.dept,60125"
    database: !expr Sys.getenv("JRP_DATABASE")
    uid: !expr Sys.getenv("JRP_DB_UID")
    pwd: !expr Sys.getenv("JRP_DB_PWD")
    trusted: "No"
    encoding: ""
```

This configures the app to look at the server `T1PRANMSQL\SQLPROD,60125` in our database called `MA_DS_S_JRP` under the variable known as `amp_jrp`. This works with the odbc package, allowing a connection to be made within the global.R script with the following:

```
# Read config file file to get details of db connections
dsn_jrp <- config::get("amp_jrp")

# Connect to JRP databases
jrp_conn <- dbConnect(odbc::odbc(),
                      Driver = dsn_jrp$driver,
                      Server = dsn_jrp$server,
                      Database = dsn_jrp$database,
                      UID = dsn_jrp$uid,
                      PWD = dsn_jrp$pwd,
                      Trusted_Connection = dsn_jrp$trusted,
                      encoding = dsn_jrp$encoding)
                      
# Load Trust level summary table from database
trust_level_summary  <- tbl(jrp_conn, in_schema("dbo", "Trust_level_Updated")) %>% collect() 

```

Multiple database connections can be set up in this way and referenced. the `tbl() %>% collect()` keywords are telling R to connect to the database, review table contents and collect it all together ready for use in a data table called `trust_level_summary`.

# Data Ouputs - 

## Tabular (DataTable)

[DataTable](https://rstudio.github.io/DT/) is a package originally adapted from Javascript, but which allows huge functionality in HTML-based tables. It offers searchable and sorted tables, conditional formatting, as well as allowing currency/percentage formatting. A lot of this additional functionality will come via the options = list() functionality in-built into datatable. 
See the [DataTable options](https://datatables.net/reference/option/) page associated with Javascript that covers many of the options available for which the keywords are the same.

## Charts

# Exporting data to Excel download sheets

Our JRT uses the [openxlsx](https://cran.r-project.org/web/packages/openxlsx/openxlsx.pdf). This package is responsible for allowing users to download filtered views of our data tables, as well as the return templates that regions fill out for reentry into the tool. It offers a variety of formatting option keywords such as freeze panes, setting custom column widths and wrapped text, assigning borders, custom data validation, and more.