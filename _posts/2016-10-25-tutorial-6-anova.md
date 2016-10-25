---
title       : Tutorial 6 - Comparing several means (ANOVA)
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
categories  : ['R', 'ANOVA', 'regression']
---



## Comparing Several Means: ANOVA

   **Jenna Blumenthal**  
   Tutorial #: 6  
   MIE 1402

---


## ANOVA as an omnibus test
- Allows us to compare the means of three or more groups without inflating the error (see _Field_, pg 399)  

  __Eg inflating the error:__
  + Suppose we consider the safety of a drug in terms of the occurrences of different types of side effects. As more types of side effects are considered, it becomes more likely that the new drug will appear to be less safe than existing drugs in terms of at least one side effect [Wikipedia](https://en.wikipedia.org/wiki/Multiple_comparisons_problem)
  
- Produces an $$F$$-statistic, which compares the systematic variance to the unsystematic variance
 + "is the $$explained\, variance$$ $$>$$ $$unexplained\, variance$$, overall?"

---

## ANOVA as Regression
- Both just configurations of the General Linear Model (GLM)
- How well does our model (individual groupings) predict the data, compared to the error?
  + This is the same as: are our groups significantly different?
  + Regression with a categorial predictor variable

---

### Data we are using:
  + [https://studysites.sagepub.com/dsur/study/articles.htm](https://studysites.sagepub.com/dsur/study/articles.htm)



<style type="text/css">
.deck-container td { padding: 5px 20px}
</style>


| person|dose      | libido|
|------:|:---------|------:|
|      1|Placebo   |      3|
|      2|Placebo   |      2|
|      3|Placebo   |      1|
|      4|Placebo   |      1|
|      5|Placebo   |      4|
|      6|Low Dose  |      5|
|      7|Low Dose  |      2|
|      8|Low Dose  |      4|
|      9|Low Dose  |      2|
|     10|Low Dose  |      3|
|     11|High Dose |      7|
|     12|High Dose |      4|
|     13|High Dose |      5|
|     14|High Dose |      3|
|     15|High Dose |      6|

---

## ANOVA as Regression

- Does dose (placebo, low or high) predict libido? (regression-speak)
- Can also say: is libido in these 3 groups significantly different? (ANOVA-speak)
 
![plot of chunk unnamed-chunk-4](/R-Tutorials-1/figure/source/2016-10-25-tutorial-6-anova/unnamed-chunk-4-1.png)

---

## ANOVA as Regression

- If our predictor variable (dose) has 3 ordered levels (placebo, low dose, high dose)

We can set our regression equation to:

$$libido_{i} = b_{0} + b_{2high_{i}} + b_{1low_{i}} + \epsilon_{i}$$

Where:

$$b_0 = \overline{X}_{placebo}$$  
$$b_1 = \overline{X}_{low dose} - \overline{X}_{placebo}$$  
$$b_2 = \overline{X}_{high dose} - \overline{X}_{placebo}$$   

- We are asking: Does using group means to predict libido scores perform better than using the overall mean?

---

### Try this yourself:


```r
lm.1 <- lm(libido ~ dose, data = data)
```

- calculate the means of each dose group
- prove to yourself that regression = ANOVA = GLM



---

## ANOVA as Regression

Moral of the story: ANOVA can always be represented by the multiple regression equation in which the \# of predictor variables is one **less** than the \# of categories of the independent variable.

![plot of chunk unnamed-chunk-7](/R-Tutorials-1/figure/source/2016-10-25-tutorial-6-anova/unnamed-chunk-7-1.png)

---

## ANOVA in R


```r
viagraModel <- aov(libido ~ dose, data = data)
summary(viagraModel)
```

```
##             Df Sum Sq Mean Sq F value Pr(>F)  
## dose         2  20.13  10.067   5.119 0.0247 *
## Residuals   12  23.60   1.967                 
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

- First row: $$SS_{M} = 20.13$$ (model)
- Second row $$SS_{R} = 23.60$$ (error)
- $$F$$ value: ratio of $$MS_{M}/MS_{R}$$

---

## Planned contrasts
- $$F$$-ratio does not tell us where differences between groups lie
- Two approaches:

- __Planned contrasts:__ break down variance accounted for by the model into component parts (specific hypothesis)  

  + Not going to go into the theory today, but I highly recommend _Field_ 419-424. This is one of those times where looking through the math helps.
  
-  __Post hoc tests:__ compare every group but use a stricter acceptance criterion (no specific hypothesis)  

---

## Built-in planned contrasts in R

- R actually sets default constrasts for your grouping variable

```r
summary.lm(viagraModel)
```

```
## 
## Call:
## aov(formula = libido ~ dose, data = data)
## 
## Residuals:
##    Min     1Q Median     3Q    Max 
##   -2.0   -1.2   -0.2    0.9    2.0 
## 
## Coefficients:
##               Estimate Std. Error t value Pr(>|t|)   
## (Intercept)     2.2000     0.6272   3.508  0.00432 **
## doseLow Dose    1.0000     0.8869   1.127  0.28158   
## doseHigh Dose   2.8000     0.8869   3.157  0.00827 **
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 1.402 on 12 degrees of freedom
## Multiple R-squared:  0.4604,	Adjusted R-squared:  0.3704 
## F-statistic: 5.119 on 2 and 12 DF,  p-value: 0.02469
```

---

- Compares each group to the base (first) group
  + Is each dose signficantly different than the placebo?

```r
contrasts(data$dose)
```

```
##           Low Dose High Dose
## Placebo          0         0
## Low Dose         1         0
## High Dose        0         1
```

- If we wanted to set our own contrasts
  + e.g. is any viagra (low or high) significantly different than the placebo, and are high and low doses different from each other?


```r
contrast_1 <- c(-2,1,1) # placebo vs viagra
contrast_2 <- c(0,-1,1) # high vs low

contrasts(data$dose) <- cbind(contrast_1, contrast_2)
```


```
##           contrast_1 contrast_2
## Placebo           -2          0
## Low Dose           1         -1
## High Dose          1          1
```

---

## More complex ANOVAs

![](/R-Tutorials-1/figure/source/2016-10-25-tutorial-6-anova/anova_naming.jpg)
---

## More complex ANOVAs

- Benefit of using ANOVA is you can look at the effect of more than one independent variable, and how they interact
  + Main effects
  + Interaction effects

- In R:


```r
aov_model <- aov(outcome ~ predictor1*predictor2, data = data_frame)
```

---

## References

Field A, Miles J, Field Z. Discovering Statistics Using R. London: Sage; 2012.

[https://en.wikipedia.org/wiki/Multiple_comparisons_problem](https://en.wikipedia.org/wiki/Multiple_comparisons_problem)


