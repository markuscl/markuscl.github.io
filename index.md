--- 
title       : The population forecast App
subtitle    : My first slidify presentation
author      : Me
job         : Datascientist wannabee
framework   : io2012        # {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
hitheme     : tomorrow     # 
widgets     : []            # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft}
knit        : slidify::knit2slides

---

## What I want to show you in this presentation

Here ist my App on Shiny-Server

[PopulationApp](http://palatinaleon.shinyapps.io./CourseProject)


The complete code of the App you will find on Github, just follow the `Links`, here I just present some important aspects

> - Of course UI.R <br>
>  [Ui.R@Github](https://github.com/markuscl/CourseProject/blob/master/ui.R)

> - Server.R <br>
> [Server.R@Github](https://github.com/markuscl/CourseProject/blob/master/server.R)

> - global.R <br>
> [global.R@Github](https://github.com/markuscl/CourseProject/blob/master/global.R)


---
## UI.R

Here the most important for the drop-down list of the scenarios 
as `select input` 

```r
selectInput("variant", "Choose forecast variant:", 
            choices = c("Variante 1-W1 EJ", "Variante 1-W2 EJ", "Variante 2-W1 EJ",
                         "Variante 2-W2 EJ", "Variante 3-W1 EJ", "Variante 3-W2 EJ",
                         "Variante 4-W1 EJ", "Variante 4-W2 EJ", "Variante 5-W1 EJ",
                         "Variante 5-W2 EJ", "Variante 6-W1 EJ", "Variante 6-W2 EJ"),
                         selected = "Variante 1-W1 EJ")     
```      

The values for the multiple years can be started with an animated `sliderInput` 
starting from 2009 until 2060

```r
 sliderInput("year", "Year:", min=2009,max=2060, value= 2009, format = "0000",
                  step = 1, animate=
                    animationOptions(interval=300, loop=TRUE))
```

---
## Server.R

Here on the server side the data of the various forecasts is read in using the `xlsx` - package 
which is pretty slow but only for demonstration purposes.<br>
As the it should be possible to choose among various scenarios the data must be assigned reactive
```r
  dataSet_names <- reactive({ 
         dataset <- read.xlsx("Data//VorausberechnungDeutschland.xls",
                               input$variant, startRow =11, endRow=12)
         dataset <- sapply(dataset, function(x) as.character(x))
         })
``` 

---
## Server.R cont.

In a final step the data is assigned to plot the population pyramid
```r
output$pyramid <- renderPlot({   
                sheet = dataSet_sheet()  
                names = dataSet_names()  
                names[105] <- "100 and older"
                names(sheet) <- names      
                colm = (input$year - 2009)*3 + 1
                colf = (input$year - 2009)*3 + 2
                py.Males <- as.numeric(sheet[colm,5:105])
                names(py.Males) <- names[5:105]
                py.Females <- as.numeric(sheet[colf,5:105])    
                pyramids(Left  = py.Males,   Llab  = "Males", Right = py.Females, 
                Rlab  ="Females",   Laxis = seq(0,1000,100), Csize =0.8, Cstep =5,
                main  = paste("German Population pyramid\n for Scenario :",
                input$variant, "\n (in 100 thousand)")) 
```

---
## Example for year 2009 and Variante 1-W1 EJ




```r
pyramids(Left=py.Males,Llab="Males",Right=py.Females,Rlab="Females",
Laxis=seq(0,1000,500),Csize=0.5, Cstep=10, main=text)
```

![plot of chunk unnamed-chunk-2](assets/fig/unnamed-chunk-2.png) 



