---
title       : Tutorial 5 - Data manipulation and reporting
subtitle    : 
author      : Jenna Blumenthal
job         : MIE 1402
framework   : deckjs        # {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
hitheme     : tomorrow      # 
widgets     : []            # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft}
knit        : slidify::knit2slides
layout      : post
comments    : true
categories  : ['R', 'RStudio', 'workflow', 'graphs', 'syntax', 'data manipulation']
---

## Data Manipulation and Reporting

   **Jenna Blumenthal**  
   Tutorial #: 5  
   MIE 1402

---

## Creating an R Markdown Document in RStudio

1. File > New File > R Markdown
2. YAML header contains document metadata
  - title
  - author
  - date
  - output (html, pdf, word)
3. Knit to generate the document

---

## Learning the syntax

- For simple text, R Markdown follows markdown formatting syntax

  - [https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)

- For R, surround your text with


<pre>```{r}
data(cars)
summary(cars)
```</pre>

---

## Options for R Chunks

- `echo=FALSE` hides R code
- `include=FALSE` prevent code and results from appearing
- `eval=FALSE` dont run chunk
- `message=FALSE` hides messages
- `warnings=FALSE` hides warnings

---

## Tables


```r
library(knitr)
kable(head(mtcars), format = "html")
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;">   </th>
   <th style="text-align:right;"> mpg </th>
   <th style="text-align:right;"> cyl </th>
   <th style="text-align:right;"> disp </th>
   <th style="text-align:right;"> hp </th>
   <th style="text-align:right;"> drat </th>
   <th style="text-align:right;"> wt </th>
   <th style="text-align:right;"> qsec </th>
   <th style="text-align:right;"> vs </th>
   <th style="text-align:right;"> am </th>
   <th style="text-align:right;"> gear </th>
   <th style="text-align:right;"> carb </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Mazda RX4 </td>
   <td style="text-align:right;"> 21.0 </td>
   <td style="text-align:right;"> 6 </td>
   <td style="text-align:right;"> 160 </td>
   <td style="text-align:right;"> 110 </td>
   <td style="text-align:right;"> 3.90 </td>
   <td style="text-align:right;"> 2.620 </td>
   <td style="text-align:right;"> 16.46 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 4 </td>
   <td style="text-align:right;"> 4 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Mazda RX4 Wag </td>
   <td style="text-align:right;"> 21.0 </td>
   <td style="text-align:right;"> 6 </td>
   <td style="text-align:right;"> 160 </td>
   <td style="text-align:right;"> 110 </td>
   <td style="text-align:right;"> 3.90 </td>
   <td style="text-align:right;"> 2.875 </td>
   <td style="text-align:right;"> 17.02 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 4 </td>
   <td style="text-align:right;"> 4 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Datsun 710 </td>
   <td style="text-align:right;"> 22.8 </td>
   <td style="text-align:right;"> 4 </td>
   <td style="text-align:right;"> 108 </td>
   <td style="text-align:right;"> 93 </td>
   <td style="text-align:right;"> 3.85 </td>
   <td style="text-align:right;"> 2.320 </td>
   <td style="text-align:right;"> 18.61 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 4 </td>
   <td style="text-align:right;"> 1 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Hornet 4 Drive </td>
   <td style="text-align:right;"> 21.4 </td>
   <td style="text-align:right;"> 6 </td>
   <td style="text-align:right;"> 258 </td>
   <td style="text-align:right;"> 110 </td>
   <td style="text-align:right;"> 3.08 </td>
   <td style="text-align:right;"> 3.215 </td>
   <td style="text-align:right;"> 19.44 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:right;"> 1 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Hornet Sportabout </td>
   <td style="text-align:right;"> 18.7 </td>
   <td style="text-align:right;"> 8 </td>
   <td style="text-align:right;"> 360 </td>
   <td style="text-align:right;"> 175 </td>
   <td style="text-align:right;"> 3.15 </td>
   <td style="text-align:right;"> 3.440 </td>
   <td style="text-align:right;"> 17.02 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:right;"> 2 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Valiant </td>
   <td style="text-align:right;"> 18.1 </td>
   <td style="text-align:right;"> 6 </td>
   <td style="text-align:right;"> 225 </td>
   <td style="text-align:right;"> 105 </td>
   <td style="text-align:right;"> 2.76 </td>
   <td style="text-align:right;"> 3.460 </td>
   <td style="text-align:right;"> 20.22 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:right;"> 1 </td>
  </tr>
</tbody>
</table>

<br>
*looks better in document than in these slides*

---

## Plots


```r
library(ggplot2)
ggplot(mtcars, aes(disp, drat)) + geom_point()
```

![plot of chunk unnamed-chunk-2](/R-Tutorials-1/figure/source/2016-10-11-tutorial-5-data-manipulation-reporting/unnamed-chunk-2-1.png)

---

## Data manipulation with reshape2

- `reshape2` makes it easy to transform data between wide and long formats
- Why/when would you need to use this?
  - running regressions, other statistical tests ( `lm()` requires long format)
  - plotting data ( `ggplot` requires long format)
  - Generally record data in wide format

---

## 'Wide' vs 'Long' data

### Wide: each column is a variable



```
##   Ozone Solar.R Wind Temp Month Day
## 1    41     190  7.4   67     5   1
## 2    36     118  8.0   72     5   2
## 3    12     149 12.6   74     5   3
## 4    18     313 11.5   62     5   4
## 5    NA      NA 14.3   56     5   5
## 6    28      NA 14.9   66     5   6
```

---

### Long: column for various variable types and a column for their values




```
##   variable value
## 1    Ozone    41
## 2    Ozone    36
## 3    Ozone    12
## 4    Ozone    18
## 5    Ozone    NA
## 6    Ozone    28
```

```
##     variable value
## 200     Wind  14.9
## 201     Wind  20.7
## 202     Wind   9.2
## 203     Wind  11.5
## 204     Wind  10.3
## 205     Wind   6.3
```

```
##     variable value
## 454     Temp    63
## 455     Temp    70
## 456     Temp    77
## 457     Temp    75
## 458     Temp    76
## 459     Temp    68
```

---

### Helpful for plotting data 

```r
ggplot(airqual.long, aes(variable, value)) + geom_boxplot()
```

![plot of chunk unnamed-chunk-6](/R-Tutorials-1/figure/source/2016-10-11-tutorial-5-data-manipulation-reporting/unnamed-chunk-6-1.png)

---

## Melt: wide to long


```r
airqual.long <- melt(airquality, id.vars = c("Month", "Day"),
  variable.name = "climate_variable", 
  value.name = "climate_value")
  
head(airqual.long)
```

```
##   Month Day climate_variable climate_value
## 1     5   1            Ozone            41
## 2     5   2            Ozone            36
## 3     5   3            Ozone            12
## 4     5   4            Ozone            18
## 5     5   5            Ozone            NA
## 6     5   6            Ozone            28
```

- must include which variables you would like to maintain in wide format using `id.vars`

---

## Cast: long to wide

![Caption for the picture.](/R-Tutorials-1/figure/source/2016-10-11-tutorial-5-data-manipulation-reporting/dcast-illustration.png)

---


```r
head(dcast(airqual.long, Month + Day ~ climate_variable, value.var = "climate_value"))
```

```
##   Month Day Ozone Solar.R Wind Temp
## 1     5   1    41     190  7.4   67
## 2     5   2    36     118  8.0   72
## 3     5   3    12     149 12.6   74
## 4     5   4    18     313 11.5   62
## 5     5   5    NA      NA 14.3   56
## 6     5   6    28      NA 14.9   66
```

---

## References

1. [An Introduction to reshape2 - Sean Anderson](http://seananderson.ca/2013/10/19/reshape.html)



