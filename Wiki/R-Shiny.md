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

Charts are easy to include in R Shiny, and can be handled either as static or interactive charts using the renderPlot() function. 

They can be created either statically within the global.R script, or alternatively for charts that can be filtered using dropdowns, created in the server.R script and referenced within UI.R. The same keywords such as ggplot(), geom_bar() etc all apply.

The below script is taken from the JRT and has an automatic check to see if there is any data. If there isn't the plot automatically defaults to a 'Data not available' message.

```
   output$financial_plot <- renderPlot({
     if(nrow(esfa_chart_data()) == 0) {
       # print error/ warning message
       plot(1, 1,col="white",
            xlab="",
            ylab="", 
            axes=FALSE) 
       text(1,1,"Data not available")
     }else{
     p1 <- ggplot(esfa_chart_data(), aes(x=Group_ESFA, y=Count_ESFA)) +
       geom_bar(stat="identity")+
       geom_text(aes(x=Group_ESFA, y=Count_ESFA, label=Count_ESFA, group = Group_ESFA), color="white",
                 position = position_stack(vjust = .5), 
                 size = 4) +
       # scale_fill_manual(values = grp_colours) + 
       ggtitle("Current Financial Triggers") + 
       xlab("Financial Trigger")
     theme_minimal() +
       # Modify axes and text
       theme(axis.text.x = element_text(size = 14), 
             axis.text.y = element_text(size = 12),
             plot.title = element_text(size=12),
             # remove the vertical grid lines
             panel.grid.major.x = element_blank() ,
             # explicitly set the horizontal lines (or they will disappear too)
             panel.grid.major.y = element_line( size=.1, color="grey" ), 
             panel.grid.minor.y = element_blank())
     
     # Access heights of bars, find largest to set new y axis
     bar_max <- max(ggplot_build(p1)[['data']][[1]]$ymax) # where 1 is index 1st layer
     max_y <- bar_max + (0.2* 10^(as.integer(floor(log10(bar_max)))))
     brk_y <- ifelse(as.integer(floor(log10(max_y))) ==0, 
                     1,
                     ifelse(as.integer(floor(log10(max_y))) == 1, 
                            round(max_y / 10, digits = 0), 
                            ifelse(as.integer(floor(log10(max_y))) == 2, 
                                   round(max_y / 10, digits = -1), 
                                   100
                            )))
     
     p1+ scale_y_continuous(name = "", 
                            breaks = seq(0, max_y, brk_y), 
                            limits = c(0, max_y))
     }
   })

```

## Exporting data to Excel download sheets

Our JRT uses the [openxlsx](https://cran.r-project.org/web/packages/openxlsx/openxlsx.pdf) package. This package is responsible for allowing users to download filtered views of our data tables, as well as the return templates that regions fill out for reentry into the tool. It offers a variety of formatting option keywords such as freeze panes, setting custom column widths and wrapped text, assigning borders, custom data validation, and more.

In the example code below, we:

* Use addWorksheet to create a worksheet to add the trust_level_summary data table to
* Use freezePane to set which rows and columns will be frozen/active.
* Specify the columns we wish to include within the all_trust_summary download (which will be referenced on the UI.R script)
* Set all cells to have borders, with cells set to have useable filter dropdowns
* Set column widths using setColWidths()
* Save the workbook

```
output$all_trust_summary_download <- downloadHandler(
    filename = function() {
      paste0("Official-Sensitive All Trust Summary ", format(Sys.time(), "%Y-%m-%d %H-%m"), ".xlsx")
    },
    content = function(con) {
      output <- createWorkbook()
      addWorksheet(output, "All Trust Summary")
      freezePane(output, "All Trust Summary", firstActiveRow = 2, firstActiveCol = 4)
      writeData(output, "All Trust Summary",
                trust_level_summary %>% 
                  select(`Trust ID`,
                         `Company House Number`,
                         `Trust Name`,
                         `Trust Type`, 
                         `Lead RSC Region`,
                         `Lead LA`,
                         `Subregion`,
                         `UTC/Mixed UTC MAT`,
                         `Faith Trust`,
                         `No. of AP Schools`,
                         `Current Joint Risk Grouping`,
                         `Last month's Final Grouping`,
                         `This month's Autogrouping`,
                         `Educational Trigger`, 
                         `ESFA Financial Trigger`, 
                         `MAT Rating`,
                         `Capital Trigger`,
                         #`RDD Risk Group`, 
                         `FNTI Status`,
                         `RAT Grade`,
                         "No. of schools with SMART  high/v high risk assessment" = `Schools with SMART high or very high risk`,
                         `Schools currently graded Inadequate`,
                         `No. of Months in Priority`, 
                         `This month's RDD commentary`, 
                         `Last month's RDD commentary`, 
                         `This month's AMSD commentary` = "AMSD Commentary DL",
                         `This month's Capital commentary` = "This month's Capital commentary DL"
                  ) ,
                borders = "all",
                headerStyle = hs1, 
                colNames = TRUE, withFilter = TRUE) 
      setColWidths(output, sheet = 1, 
                   cols = c(1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26), 
                   widths = c(8.47, 15, 46.33, 15, 33, 17, 33, 15, 15, 15, 27, 27, 27, 35, 15, 22, 35, 12, 15, 15, 15, 15, 62.87, 62.87, 62.87, 62.87))
      saveWorkbook(wb = output, file = con)
    })
```