---
title       : Tutorial 8 - ANCOVA
subtitle    : 
author      : Jenna Blumenthal
job         : MIE 1402
framework   : deckjs        # {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
hitheme     : tomorrow      # 
widgets     : [mathjax]     # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft}
knit        : slidify::knit2slides
layout      : post
comments    : true
categories  : ['R', 'ANCOVA']
---



## Analysis of Covariance (ANCOVA)

   **Jenna Blumenthal**  
   Tutorial #: 8  
   MIE 1402

---



## Introduction

- We can extend the regression equation for ANOVA to include continuous variable(s) that predict the outcome variable
- These _continuous_ variables that are NOT part of the experimental manipulation (but can influence the outcome) is called a covariate  
  
- Dealing with covariates relatively simple:
  + Enter the variable into regression model first (hierarchical)
  + Then, enter the dummy variables representing the experimental manipulation  

  
- Voila! Now we can see the effect of the experiment after the covariate(s) have been accounted for

---

## Reasons for including covariates

**1. Reduce within-group error variance**  

- Explain some of the 'unexplained' variance ($$SS_{R}$$)
  
**2. Elimination of confounds**  

- Remove bias of any variable that is known to influence the dependent variable

---

## Assumptions of ANCOVA

Same as ANOVA, plus:

**1. Independence of covariate and treatment effect**  

- if the covariate is to explain some of the $$SS_{R}$$, it must be independent of the experimental effect
- variance explained by treatment & covariate should not overlap
  + covariate will (statiscally speaking) reduce the experimental effect!
- problem can be avoided by randomizing participants, or matching experimental groups on the covariate
- to evaluate, run a t-test (or anova if > 2) to see if the groups differ significantly on levels of the covariate

---

## Example: mtcars


```r
kable(head(mtcars[,c("am","mpg","hp")]))
```



|                  | am|  mpg|  hp|
|:-----------------|--:|----:|---:|
|Mazda RX4         |  1| 21.0| 110|
|Mazda RX4 Wag     |  1| 21.0| 110|
|Datsun 710        |  1| 22.8|  93|
|Hornet 4 Drive    |  0| 21.4| 110|
|Hornet Sportabout |  0| 18.7| 175|
|Valiant           |  0| 18.1| 105|

_Note_  
**am:** type of transmission (auto or manual)  
**mpg:** miles per gallon  
**hp:** horsepower  

---

![plot of chunk unnamed-chunk-4](/R-Tutorials-1/figure/source/2016-11-09-tutorial-8-ancova/unnamed-chunk-4-1.png)

$$r_{hp,mpg}$$ = -0.7761684

---

## Assumption 1

Run a t-test to see if groups differ significantly on covariate


```r
mtcars$am <- as.factor(mtcars$am)
t.test(mtcars[mtcars$am == 0,]$hp, mtcars[mtcars$am == 1,]$hp)
```

```
## 
## 	Welch Two Sample t-test
## 
## data:  mtcars[mtcars$am == 0, ]$hp and mtcars[mtcars$am == 1, ]$hp
## t = 1.2662, df = 18.715, p-value = 0.221
## alternative hypothesis: true difference in means is not equal to 0
## 95 percent confidence interval:
##  -21.87858  88.71259
## sample estimates:
## mean of x mean of y 
##  160.2632  126.8462
```

---

## Assumptions of ANCOVA

**2. Homogeneity of Regression Slopes**  

- the slope of the regression line should be approximately the same in all groups
- i.e. relationship between covariate and outcome variable should not differ significantly between groups


```r
leveneTest(mtcars$hp, mtcars$am)
```

```
## Levene's Test for Homogeneity of Variance (center = median)
##       Df F value Pr(>F)
## group  1  0.1842 0.6709
##       30
```

---

## Running the ANCOVA Analysis

- Recall: we want to study the effect of **am** on **mpg**, taking into account the covariate **hp**


```r
result.1 <- aov(mpg~hp+am,data = mtcars)
summary.lm(result.1)
```

```
## 
## Call:
## aov(formula = mpg ~ hp + am, data = mtcars)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -4.3843 -2.2642  0.1366  1.6968  5.8657 
## 
## Coefficients:
##              Estimate Std. Error t value Pr(>|t|)    
## (Intercept) 26.584914   1.425094  18.655  < 2e-16 ***
## hp          -0.058888   0.007857  -7.495 2.92e-08 ***
## am1          5.277085   1.079541   4.888 3.46e-05 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 2.909 on 29 degrees of freedom
## Multiple R-squared:  0.782,	Adjusted R-squared:  0.767 
## F-statistic: 52.02 on 2 and 29 DF,  p-value: 2.55e-10
```

---

### What does it look like if we don't include the covariate?


```r
result.2 <- aov(mpg~am,data = mtcars)
summary.lm(result.2)
```

```
## 
## Call:
## aov(formula = mpg ~ am, data = mtcars)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -9.3923 -3.0923 -0.2974  3.2439  9.5077 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept)   17.147      1.125  15.247 1.13e-15 ***
## am1            7.245      1.764   4.106 0.000285 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 4.902 on 30 degrees of freedom
## Multiple R-squared:  0.3598,	Adjusted R-squared:  0.3385 
## F-statistic: 16.86 on 1 and 30 DF,  p-value: 0.000285
```

- Adjusted R-squared has gone down from 0.77 to 0.34
- Auto/Manual is explaining 'more' of the variance when we remove the effect of the covariate

---

### Interaction term


```r
result.3 <- aov(mpg~hp*am,data = mtcars)
result.3 <- aov(mpg~hp + am + hp:am ,data = mtcars)
summary.lm(result.3)
```

```
## 
## Call:
## aov(formula = mpg ~ hp + am + hp:am, data = mtcars)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -4.3818 -2.2696  0.1344  1.7058  5.8752 
## 
## Coefficients:
##               Estimate Std. Error t value Pr(>|t|)    
## (Intercept) 26.6248479  2.1829432  12.197 1.01e-12 ***
## hp          -0.0591370  0.0129449  -4.568 9.02e-05 ***
## am1          5.2176534  2.6650931   1.958   0.0603 .  
## hp:am1       0.0004029  0.0164602   0.024   0.9806    
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 2.961 on 28 degrees of freedom
## Multiple R-squared:  0.782,	Adjusted R-squared:  0.7587 
## F-statistic: 33.49 on 3 and 28 DF,  p-value: 2.112e-09
```

- confirms that the interaction between **am** and **hp** is non-significant (assumption 2)

---

## References

Field A, Miles J, Field Z. Discovering Statistics Using R. London: Sage; 2012.

[R - Analysis of Covariance (Tutorials Point)](https://www.tutorialspoint.com/r/r_analysis_of_covariance.htm)
