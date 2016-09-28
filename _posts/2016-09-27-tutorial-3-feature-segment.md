---
title       : Tutorial 3 - Feature Extraction and Segmentation
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
categories  : ['R', 'RStudio', 'unsupervised analysis']
---


## Feature Extraction and Segmentation

   **Jenna Blumenthal**  
   Tutorial #: 3  
   MIE 1402

---

## Exploratory factor analysis



- We'll use the iris data again
- Simple dataset, usually you would use this technique for data that has many variables that you would like to condense or explore relationships

<style type="text/css">
table { width: 90%; font-size: 24}
</style>

| Sepal.Length | Sepal.Width | Petal.Length | Petal.Width | Species |
|:------------:|:-----------:|:------------:|:-----------:|:-------:|
|     5.1      |     3.5     |     1.4      |     0.2     | setosa  |
|     4.9      |     3.0     |     1.4      |     0.2     | setosa  |
|     4.7      |     3.2     |     1.3      |     0.2     | setosa  |
|     4.6      |     3.1     |     1.5      |     0.2     | setosa  |
|     5.0      |     3.6     |     1.4      |     0.2     | setosa  |
|     5.4      |     3.9     |     1.7      |     0.4     | setosa  |

---

### 1. Extract the same number of factors as variables


{% highlight r %}
pc1 <- principal(iris[1:4], 4)
pc1
{% endhighlight %}



{% highlight text %}
## Principal Components Analysis
## Call: principal(r = iris[1:4], nfactors = 4)
## Standardized loadings (pattern matrix) based upon correlation matrix
##                RC1   RC3   RC2   RC4 h2       u2 com
## Sepal.Length  0.53  0.85  0.00  0.00  1  1.1e-16 1.7
## Sepal.Width  -0.17 -0.04  0.98 -0.01  1  4.4e-16 1.1
## Petal.Length  0.78  0.54 -0.28  0.14  1 -4.4e-16 2.2
## Petal.Width   0.89  0.41 -0.20 -0.06  1  0.0e+00 1.5
## 
##                        RC1  RC3  RC2  RC4
## SS loadings           1.71 1.18 1.09 0.02
## Proportion Var        0.43 0.30 0.27 0.01
## Cumulative Var        0.43 0.72 0.99 1.00
## Proportion Explained  0.43 0.30 0.27 0.01
## Cumulative Proportion 0.43 0.72 0.99 1.00
## 
## Mean item complexity =  1.6
## Test of the hypothesis that 4 components are sufficient.
## 
## The root mean square of the residuals (RMSR) is  0 
##  with the empirical chi square  0  with prob <  NA 
## 
## Fit based upon off diagonal values = 1
{% endhighlight %}

---

### 2. Examine scree plot
- Eigenvalues plotted against the factor number
- Look for point of inflection ("elbow")

{% highlight r %}
plot(pc1$values, type = "b")
{% endhighlight %}

![plot of chunk unnamed-chunk-4](/R-Tutorials-1/figure/source/2016-09-27-tutorial-3-feature-segment/unnamed-chunk-4-1.png)

---

### 3. Choose number of factors to extract

- Look at eigenvalues (Kaiser's criterion > 1, Jolliffe's criterion > 0.7)
- Examine scree plot
- May be theoretical


{% highlight r %}
pc2 <- principal(iris[1:4], 2)
pc2
{% endhighlight %}



{% highlight text %}
## Principal Components Analysis
## Call: principal(r = iris[1:4], nfactors = 2)
## Standardized loadings (pattern matrix) based upon correlation matrix
##                RC1   RC2   h2     u2 com
## Sepal.Length  0.96  0.05 0.92 0.0774 1.0
## Sepal.Width  -0.14  0.98 0.99 0.0091 1.0
## Petal.Length  0.94 -0.30 0.98 0.0163 1.2
## Petal.Width   0.93 -0.26 0.94 0.0647 1.2
## 
##                        RC1  RC2
## SS loadings           2.70 1.13
## Proportion Var        0.68 0.28
## Cumulative Var        0.68 0.96
## Proportion Explained  0.71 0.29
## Cumulative Proportion 0.71 1.00
## 
## Mean item complexity =  1.1
## Test of the hypothesis that 2 components are sufficient.
## 
## The root mean square of the residuals (RMSR) is  0.03 
##  with the empirical chi square  1.72  with prob <  NA 
## 
## Fit based upon off diagonal values = 1
{% endhighlight %}

---

### 4. Decide whether to use rotation

- Orthogonal or oblique
- Does it make sense theoretically? 
- Does it improve things?


{% highlight r %}
pc3 <- principal(iris[1:4], 2, rotate = "oblimin")
{% endhighlight %}



{% highlight text %}
## Loading required namespace: GPArotation
{% endhighlight %}



{% highlight r %}
pc3
{% endhighlight %}



{% highlight text %}
## Principal Components Analysis
## Call: principal(r = iris[1:4], nfactors = 2, rotate = "oblimin")
## Standardized loadings (pattern matrix) based upon correlation matrix
##                TC1   TC2   h2     u2 com
## Sepal.Length  1.00  0.20 0.92 0.0774 1.1
## Sepal.Width  -0.02  0.99 0.99 0.0091 1.0
## Petal.Length  0.94 -0.16 0.98 0.0163 1.1
## Petal.Width   0.93 -0.11 0.94 0.0647 1.0
## 
##                        TC1  TC2
## SS loadings           2.75 1.08
## Proportion Var        0.69 0.27
## Cumulative Var        0.69 0.96
## Proportion Explained  0.72 0.28
## Cumulative Proportion 0.72 1.00
## 
##  With component correlations of 
##       TC1   TC2
## TC1  1.00 -0.27
## TC2 -0.27  1.00
## 
## Mean item complexity =  1
## Test of the hypothesis that 2 components are sufficient.
## 
## The root mean square of the residuals (RMSR) is  0.03 
##  with the empirical chi square  1.72  with prob <  NA 
## 
## Fit based upon off diagonal values = 1
{% endhighlight %}

---

## Segmentation: Cluster Analysis

- Unsupervised learning algorithm that tries to cluster data based on their similarity
- Again, we'll use the iris data




{% highlight r %}
ggplot(iris, aes(Petal.Length, Petal.Width, color = Species)) + geom_point()
{% endhighlight %}

![plot of chunk unnamed-chunk-8](/R-Tutorials-1/figure/source/2016-09-27-tutorial-3-feature-segment/unnamed-chunk-8-1.png)

---

## Clustering in R


{% highlight r %}
set.seed(20)
irisCluster <- kmeans(iris[, 3:4], 3, nstart = 20)
irisCluster$centers
{% endhighlight %}



{% highlight text %}
##   Petal.Length Petal.Width
## 1     1.462000    0.246000
## 2     4.269231    1.342308
## 3     5.595833    2.037500
{% endhighlight %}

- nstart is the number of random starting assignments
- R then selects the one the with lowest within-cluster variation
- we use the set.seed() function to ensure that we get the same randon value when we re-run the code

--- &two-col

***{name: left}

Cluster assignments vs actual species

| setosa| versicolor| virginica|
|------:|----------:|---------:|
|     50|          0|         0|
|      0|         48|         4|
|      0|          2|        46|

***{name: right}

![plot of chunk unnamed-chunk-11](/R-Tutorials-1/figure/source/2016-09-27-tutorial-3-feature-segment/unnamed-chunk-11-1.png)

---

## References

Field A, Miles J, Field Z. Discovering Statistics Using R. London: Sage; 2012.

[https://www.r-bloggers.com/k-means-clustering-in-r/](https://www.r-bloggers.com/k-means-clustering-in-r/)

