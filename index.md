---
title       : Economic Indicator Shiny App
subtitle    : 10 Year Review and 12 Month Forecast
author      : Jim White
job         : 
framework   : io2012        # {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
hitheme     : tomorrow      # 
widgets     : []            # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft}
knit        : slidify::knit2slides
---

## Economic Indicators

<ol>
<li>Currently the Federal Reserve has over 382,000 data sets of time series data.</li>
<li>The data can be searched on the FRED website or can be downloaded via an API that is provided by the Federal Reserve Bank of St. Louis. Links are provided below regarding the API and FRED's online data resources.</li>
<li>Understanding data trends related to key economic indicators is important for business planning.</li>
<li>The purpose of this presentation and the accompanying shiny app is to demonstrate how this data may be retrieved and used to create charts and tables.</li>
<li>The economic indicators referenced in the shiny app are as follows: Unemployment Rate, Consumer Price Index (CPI) - Urban, Disposable Personal Income, Personal Savings, and Consumer Sentiment.</li>
</ol>


Links: [FRED's Economic Data] (https://research.stlouisfed.org/fred2/), [FRED's API] (https://research.stlouisfed.org/docs/api/fred/)<br/>
<span style="font-size:10pt; font-style: italic;">Using the API requires the acquisition of an API key - explained under General Documentation of the API website.</span>

Link to App: [Economic Indicator App] (https://jdwhite1949.shinyapps.io/Economic_Indicators/)

--- .class #id 

## Working with FRED Data
<ol start="6">
<li>From the website, data can be downloaded in Excel, space delimited, or comma separated, which may be easier to load into R, however, collecting data this way a manual process.</li>
<li>The process can be automated using the API, but the data format that is presented is either XML or json, which requires some understanding of how to process these formats into a useable form. The Economic Indicator shiny app makes this process easy.</li>
</ol>

For example here is a sample (one observation) of the data returned from FRED:

```r
library(XML)
load("freddata.RData")
fred.xml.doc <- xmlTreeParse(fred.data)
fred.xml.doc$doc$children$observations[[1]]
```

```
## <observation realtime_start="2016-02-26" realtime_end="2016-02-26" date="1948-01-01" value="3.4"/>
```
 

--- .class #id 

## Working with FRED Data (cont)
<ol start="8">
<li>The Economic Indicator shiny app pulls all of the "date" and "value" attributes from each of the observations and places them in a dataframe which allows for easier data analysis.</li>
<li>The user can input which economic indicator they wish to view by using the drop down box located in the left panel of the user interface. The selection device contains a description of the indicator. After makind a selection, the user must click the submit button in order to activate the data request.</li>
<li>Since the data series request is in an abbreviated form (e.g., UNRATE for Unemploment Rate) a csv file was created that contains these abbreviations. The r code then accesses the csv file, looks up the appropriate abbreviation for the "series id" and adds it to the URL request. For example, when the user selects the value "Unemploment Rate" the value "UNRATE" is returned:</li>
</ol>


```r
FedId <- read.csv("FREDID.csv", header = TRUE, stringsAsFactors = FALSE)
sel <- "Unemployment Rate" # this actuall would come from the user object
selection <- FedId[which(FedId$selBoxValue==sel), 2] 
selection # the value of selection is then passed to a function that retrieves the data.
```

```
## [1] "UNRATE"
```

--- .class #id 

## Working with FRED Data (cont)
<ol start="11">
<li>The advantage of having the indicator series id in a csv file is that it can be updated without changing any of the r code.</li>
<li>The series id is fed to a separate function (get.data) located in the server.R file. Data is downloaded, parsed, and put into a data frame format for easier analysis. The data is then converted to a time series object which can be used for forecasting and creating a plot. The code is below and the plot example is on next slide.</li>



```r
data.ts <- ts(ts.values, frequency = 12, start = c(2006, 1))
data.forecast <<- forecast(data.ts, 12) # data is forecast 12 months into future
p <- plot(data.forecast, main="10 Year Results & 12 Month Forecast", xlab="Years",
              ylab="Economic Indicator Units")
```
<li>Finally a table of forecast values are created using a user-defined function (get.table.data). Here is an example of the R code for generating the plot:</li>
<ol>

--- .class #id 

## Plot Example
Plot output for Economic Indicator app:
<img src="assets/fig/unnamed-chunk-5-1.png" title="plot of chunk unnamed-chunk-5" alt="plot of chunk unnamed-chunk-5" style="display: block; margin: auto auto auto 0;" />
The forecast are the blue line and are done via exponential smoothing. The gray 'fans' represent the 80 and 90% confidence intervals.

**Conclusion:** The Economic Indicator App is a demonstration of both the power of R and the availability of economic data that can help managers make better decisions. 

