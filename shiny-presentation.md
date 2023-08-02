---
title: "Analysis of the COVID-19 Dashboard"
author: "Rati Nimkar"
date: "3/8/2023"
output:
  slidy_presentation: default
  html_document:
  number_sections: yes
  toc: yes
  ioslides_presentation: default
mode: selfcontained
job: Reproducible Pitch Presentation
subtitle: COVID-19 Data Hub
highlighter: highlight.js
widgets: bootstrap
---





# Description

This presentation is done for the Coursera course Developing Data Products. The final assignment was to create a Shiny app and a reproducible pitch

Access the shiny app [here](https://benthecoder.shinyapps.io/COVID-19_Dashboard/)

This will be going into how the shiny app was formed, and where the data comes from. 


# COVID-19 Data Hub 

The data was provided with a package by the COVID-19 Data Hub, which you can access [here](https://covid19datahub.io/articles/api/r.html)

Look at the Documentation [here](https://covid19datahub.io/articles/doc/data.html) 

Download the data [here](https://covid19datahub.io/articles/data.html)

Citation

> Guidotti et al., (2020). COVID-19 Data Hub. Journal of Open Source Software, 5(51), 2376, https://doi.org/10.21105/joss.02376


# Define UI

The COVID19 R package provides an imtegration with COVID-19 Data Hub via the covid19() function.

In our UI we define the following inputs
* `country` - country name   
* `type` - metric (confirmed, tests, recovered, etc.)   
* `level` - country, region or city   
* `dates` - start and end dates  


```r
# barebone structure of the UI
ui <- fluidPage(
    
    selectInput("country"...)
    selectInput("type"...)
    selectInput("level"...)
    dateRangeInput("date"...)
    
    plotlyOutput("covid19plot")
    
)
```


# Server Logic

Below shows the server.R code, as you can see we connect inputs (from the UI) to the `covid19()` function to fetch the data. WIth the data, we then use plotly to render a plot, and output it from the UI. 


```r
# Define server logic
server <- function(input, output) {
    output$covid19plot <- renderPlotly({
        if (!is.null(input$country)) {
            x <- covid19(
                    country = input$country,
                    level = input$level,
                    start = input$date[1],
                    end = input$date[2]
                )
            color <- paste0("administrative_area_level_", input$level)
            plot_ly(x = x[["date"]],
                    y = x[[input$type]],
                    color = x[[color]],
                    type = 'scatter',
                    mode = 'lines')
            
        }
    })
    
}
```

