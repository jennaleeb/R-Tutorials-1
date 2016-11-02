---
title       : Tutorial 7 - Logistic Regression
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
categories  : ['R', 'regression']
---



## Logistic Regression

   **Jenna Blumenthal**  
   Tutorial #: 7  
   MIE 1402

---

## Introduction

- Logistic regression is simply regression with an outcome variable that is **categorical**
- The predictor variables are continuous or categorical
  + if the predictor variables are categorical only, would use a chi-squared test (_Field_, Ch. 18)
- For example, in medical research:
  + given a set of cancer patients, what is the likelihood that their tumors are cancerous vs. benign?
  + use existing database to establish which variables are influential in predicting malignancy

---

## Logistic regression equation

- We are instead predicting the **probability** of $$Y$$ occuring, rather than the **value** of $$Y$$

$$ P(Y) = \frac{1}{1+e^{-(b_{0}+b_{1}X_{1}+b_{2}X_{2}...)}} $$

- Since we cannot apply linear regression when the outcome variable is categorical (would violate the assumption of linearity)
  
- We instead take the logarithm of the equation
- This allows us to express a non-linear relationship in a linear way
  + $$P(Y)$$ is continuous, and varies from 0 to 1

---

## Assessing the model

- for a given occurance, $$Y$$ will either be 0 (didn't occur) or 1 (did occur)
- so $$P(Y)$$ is the _chance_ that it occured
- To assess whether our model fits the data, we can compare the **observed** and **predicted** values of the outcome
- The measure is called the **log-likelihood**
  + Analogous to $$SS_{R}$$ in that it is an indicator of how much unexplained information there is after a model has been fitted
  + Large value = lots of unexplained observations

$$\text{log-likelihood} = \sum_{i=1}^{N}[Y_{i}ln(P(Y_{i}))+(1-Y_{i})ln(1-P(Y_{i})))]$$

---

## Assessing the model

$$\text{log-likelihood} = \sum_{i=1}^{N}[Y_{i}ln(P(Y_{i}))+(1-Y_{i})ln(1-P(Y_{i})))]$$

- recall the $$ln$$ distribution:

![plot of chunk unnamed-chunk-2](/R-Tutorials-1/figure/source/2016-11-01-tutorial-7-anova/unnamed-chunk-2-1.png)

- What happens when  
  $$Y_{i} = 0$$?  
  $$Y_{i} = 1$$?

---

## Deviance statistic
- deviance = $$-2 x LL$$
- more convenient because it has a chi-square distribution
- easier to calculate significance of value
- We use the deviance to compare models (similar to **aov**(model_1, model_2))
  + Does our model improve prediction?  
  
$$\chi^{2}=2LL(new) - 2LL(baseline)$$  
$$df = k_{new} - k_{baseline}$$
- In linear regression, the baseline model is the mean
- In logistic, it is the category with the highest **frequency**
  + The category with the higher # of cases (0 or 1) is our "best guess"

---

### Other ways to assess the model
(we won't go over)
- $$R$$, $$R^{2}$$
  + Hosmer and Lemeshow's $$R^{2}_{L}$$
  + Cox and Snell's $$R^{2}_{CS}$$
  + Nagelkerke's $$R^{2}_{N}$$
- Each have their own theoretical background and slightly different equations (_Field_, 317-18)
- AIC, BIC

---

## Odds ratio

- More important to interpreting logistic regression is the value of the odds ratio, $$e^{B}$$
  + Indicator of the change in odds resulting from a **unit change** in the predictor
  
- The **"odds"** of an event occuring is:  

$$\text{odds} = \frac{P(event)}{P(no\,event)}$$

Where:  

$$P(event\,Y) = \frac{1}{1+e^{-b_{0}+b_{1}X_{1}+b_{2}X_{2}...}}$$

$$P(no\,event\,Y) = 1 - P(event\,Y)$$

---

## Odds ratio

So, the odds **ratio** is

$$\Delta odds = \frac{\text{odds after a unit change in predictor}}{\text{original odds}}$$

- if > 1, as the predictor increases, the odds of the outcome occuring increase

---

## Logistic regression in R

How does GRE, GPA and prestige of undergrad institution influence admission into graduate school? [(UCLA Institute for Digital Research and Education)](http://www.ats.ucla.edu/stat/r/dae/logit.htm)

```r
data <- read.csv("http://www.ats.ucla.edu/stat/data/binary.csv")
head(data)
```

```
##   admit gre  gpa rank
## 1     0 380 3.61    3
## 2     1 660 3.67    3
## 3     1 800 4.00    1
## 4     1 640 3.19    4
## 5     0 520 2.93    4
## 6     1 760 3.00    2
```

---


```r
library(mlogit)

### Let's start by just looking at GPA
```

```r
model.1 <- glm(admit ~ gpa, data = data, family = "binomial")
summary(model.1)
```

```
## 
## Call:
## glm(formula = admit ~ gpa, family = "binomial", data = data)
## 
## Deviance Residuals: 
##     Min       1Q   Median       3Q      Max  
## -1.1131  -0.8874  -0.7566   1.3305   1.9824  
## 
## Coefficients:
##             Estimate Std. Error z value Pr(>|z|)    
## (Intercept)  -4.3576     1.0353  -4.209 2.57e-05 ***
## gpa           1.0511     0.2989   3.517 0.000437 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## (Dispersion parameter for binomial family taken to be 1)
## 
##     Null deviance: 499.98  on 399  degrees of freedom
## Residual deviance: 486.97  on 398  degrees of freedom
## AIC: 490.97
## 
## Number of Fisher Scoring iterations: 4
```

- The residual deviance is lower than the null deviance (model is now predicting the outcome more accurately than baseline)
- Question of how _much_ better the model predicts the outcome is assessed using the $$\chi^{2}$$ statistic:
$$\chi^{2} = 499.98 - 486.97 = 13.01$$

---

Or in R:


```r
model.1.Chi <- model.1$null.deviance - model.1$deviance
round(model.1.Chi, 2)
```

```
## [1] 13.01
```

Find the probability associated with this chi-square statistic by using the pchisq() function, and the difference in degrees of freedom

```r
df <- model.1$df.null - model.1$df.residual
df
```

```
## [1] 1
```

```r
1 - pchisq(model.1.Chi, df) # this is the probability we want
```

```
## [1] 0.0003100148
```

So we can report:  
$$\chi^{2}(1) = 13.01, p < .001 $$

---

### Assessing coefficients

- similar to linear regression, the $$b$$ estimate replaces the value in our original equation
- represents the change in the logit of the outcome variable associated with a one-unit change in the predictor variable
  + the natural logarithm ($$ln$$) if the odds of $$Y$$ occuring
- $$z$$-statistic tells us whether the $$b$$ coefficient is significantly different from 0

---

### Odds ratio

$$\frac{\text{odds after a unit change in the predictor}}{\text{original odds}}$$


```r
exp(model.1$coefficients)
```

```
## (Intercept)         gpa 
##  0.01280926  2.86082123
```

---

## More than one predictor


```r
str(data)
```

```
## 'data.frame':	400 obs. of  4 variables:
##  $ admit: int  0 1 1 1 0 1 1 0 1 0 ...
##  $ gre  : int  380 660 800 640 520 760 560 400 540 700 ...
##  $ gpa  : num  3.61 3.67 4 3.19 2.93 3 2.98 3.08 3.39 3.92 ...
##  $ rank : int  3 3 1 4 4 2 1 2 3 2 ...
```

```r
data$rank <- factor(data$rank)
```

---

```r
model.2 <- glm(admit ~ gpa + gre + rank, data = data, family = "binomial")
summary(model.2)
```

```
## 
## Call:
## glm(formula = admit ~ gpa + gre + rank, family = "binomial", 
##     data = data)
## 
## Deviance Residuals: 
##     Min       1Q   Median       3Q      Max  
## -1.6268  -0.8662  -0.6388   1.1490   2.0790  
## 
## Coefficients:
##              Estimate Std. Error z value Pr(>|z|)    
## (Intercept) -3.989979   1.139951  -3.500 0.000465 ***
## gpa          0.804038   0.331819   2.423 0.015388 *  
## gre          0.002264   0.001094   2.070 0.038465 *  
## rank2       -0.675443   0.316490  -2.134 0.032829 *  
## rank3       -1.340204   0.345306  -3.881 0.000104 ***
## rank4       -1.551464   0.417832  -3.713 0.000205 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## (Dispersion parameter for binomial family taken to be 1)
## 
##     Null deviance: 499.98  on 399  degrees of freedom
## Residual deviance: 458.52  on 394  degrees of freedom
## AIC: 470.52
## 
## Number of Fisher Scoring iterations: 4
```

---

### Interpretation

- for every unit change in **gre** and **gpa**, we get the value that the log odds value increases by
- But for interpreting **rank** recall:

```r
contrasts(data$rank)
```

```
##   2 3 4
## 1 0 0 0
## 2 1 0 0
## 3 0 1 0
## 4 0 0 1
```
- For **rank**, we are getting the change in log odds that occurs when you compare institution with rank 2 versus rank 1


```r
exp(model.2$coefficients)
```

```
## (Intercept)         gpa         gre       rank2       rank3       rank4 
##   0.0185001   2.2345448   1.0022670   0.5089310   0.2617923   0.2119375
```
- Now we can say that for a one unit increase in gpa, the odds of being admitted to graduate school (versus not being admitted) increase by a factor of 2.23

---

## Comparing models

- You can use the difference in deviance equation
- Or in R, simply:


```r
anova(model.1, model.2)
```

```
## Analysis of Deviance Table
## 
## Model 1: admit ~ gpa
## Model 2: admit ~ gpa + gre + rank
##   Resid. Df Resid. Dev Df Deviance
## 1       398     486.97            
## 2       394     458.52  4    28.45
```

```r
1 - pchisq(28.45, 4)
```

```
## [1] 1.010924e-05
```

---

## References

Field A, Miles J, Field Z. Discovering Statistics Using R. London: Sage; 2012.

R Data Analysis Examples: Logit Regression. UCLA Institute for Digital Research and Education. http://www.ats.ucla.edu/stat/r/dae/logit.htm
