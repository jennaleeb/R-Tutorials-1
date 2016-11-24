---
title       : Tutorial 10 - Non-parametric tests and categorical data
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
categories  : ['R', 'non-parametric', 'categorical']
---



## Non-parametric tests and categorical data

   **Jenna Blumenthal**  
   Tutorial #: 10  
   MIE 1402

---

## Introduction

- Up until now, we've looked at various approaches to look for differences between means
- But these rely on the parametric assumptions
- Not always possible to correct for using transformations, segmentation, etc.
- Instead, use tests that don't rely on these assumptions
- Most of these work by **ranking** the data

---

## General 'analogies'
<table style="text-align: left;"></table>

<style type="text/css">
table { text-align: left}
.deck-container td {border: 3px solid #95a5a6; padding: 7px}
</style>

| Parametric              	| Non-parametric                         	|
| Independent t-test      	| Wilcoxon rank-sum                      	|
| Dependent t-test        	| Wilcoxon signed-rank                   	|
| Independent ANOVA       	| Kruskal-Wallis / Jonckheere-Terpstra    |
| Repeated-measures ANOVA 	| Friedman's ANOVA                       	|

---

## Theory of the Wilcoxon rank-sum test

- rank the scores, ignoring which group they originated
- if there is _no_ difference between groups, you would assume there would be some ranked high, some ranked low, in both groups
  - sum of ranks: group 1 $$\approx$$ group 2
  
- once we've ranked the data, sum up the ranks in each group
- correct for the # of people in each group (otherwise big group = large sum of ranks)
  mean rank = $$\frac{N(N+1)}{2}$$
- $$W$$ = sum of ranks - mean rank

---

### Significance and effect size

- Take the smaller of $$W_{1}$$, $$W_{2}$$ 
- **R** calculates the associate $$p$$ value
  - either by Monte Carlo simulation
  - or if $$N>40$$, **R** assumes the distribution of the $$W$$ statistic is normal
    - then can also calculate effect size using $$r = \frac{z}{\sqrt{N}}$$


---

## Wilcoxon signed-rank test

- Scores come from the same entity
- Test the assumption of normality of the differences between the scores (Shapiro-Wilk etc)
- Calculate difference between scores
- Add up all the (+)'s and all the (-)'s
- The smaller values is used as the test statistic $$T$$
- Again correct for group size using the mean ($$\bar{T}$$)
- Find $$z$$ score and $$p$$ value using
  $$z = \frac{T - \bar{T}}{SE_{\bar{T}}}$$

---

## Comparing two groups in R

Both independent (rank-sum) and dependent (signed-rank) Wilcoxon can be run using:


```r
model <- wilcox.test(group1, group2, paired = TRUE/FALSE, correct = FALSE)
```

---

## Kruskal-Wallis test

- comparing multiple independent groups
- However, recall that the $$F$$ statistic can be robust to violations of assumptions
  - see Sec. 10.3 and Jane Superbrain 10.2 (pg 412-414)
- can also use this non-parametric approach
- Test statistic derived from ranking the data regardless of group, adding up the ranks in each group
- Similarly, the Jonckheere-Terpstra statistic tests for an ordered pattern (trend) in the groups you are comparing

---

## Friedman's ANOVA

- Several conditions, same entities
- Again, test statistic derived from ranking (but this time each group is ranked, within-participant)
- Ranks added up, produces test statistic

---

## R example

Grab data: [http://bit.ly/2flElhq](http://bit.ly/2flElhq)


```r
data <- read.delim("coulrophobia.dat")
str(data)
```

```
## 'data.frame':	60 obs. of  2 variables:
##  $ infotype: Factor w/ 4 levels "Advert","Exposure",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ beliefs : int  5 3 1 3 4 4 5 5 3 5 ...
```

---

## Testing assumptions


```r
by(data$beliefs, data$infotype, shapiro.test)
```

```
## data$infotype: Advert
## 
## 	Shapiro-Wilk normality test
## 
## data:  dd[x, ]
## W = 0.89718, p-value = 0.08619
## 
## -------------------------------------------------------- 
## data$infotype: Exposure
## 
## 	Shapiro-Wilk normality test
## 
## data:  dd[x, ]
## W = 0.86665, p-value = 0.03013
## 
## -------------------------------------------------------- 
## data$infotype: None
## 
## 	Shapiro-Wilk normality test
## 
## data:  dd[x, ]
## W = 0.60343, p-value = 2.738e-05
## 
## -------------------------------------------------------- 
## data$infotype: Story
## 
## 	Shapiro-Wilk normality test
## 
## data:  dd[x, ]
## W = 0.85504, p-value = 0.02048
```

---

## Testing assumptions


```r
library(car)
leveneTest(data$beliefs ~ data$infotype)
```

```
## Levene's Test for Homogeneity of Variance (center = median)
##       Df F value  Pr(>F)  
## group  3  2.4165 0.07593 .
##       56                  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

---

## Running the K-W test in R


```r
clown.model.1 <- kruskal.test(data$beliefs ~ data$infotype, data = data)
clown.model.1
```

```
## 
## 	Kruskal-Wallis rank sum test
## 
## data:  data$beliefs by data$infotype
## Kruskal-Wallis chi-squared = 17.058, df = 3, p-value = 0.0006876
```

```r
library(pgirmess)
kruskalmc(data$beliefs ~ data$infotype, data = data)
```

```
## Multiple comparison test after Kruskal-Wallis 
## p.value: 0.05 
## Comparisons
##                   obs.dif critical.dif difference
## Advert-Exposure 21.266667     16.82428       TRUE
## Advert-None     13.700000     16.82428      FALSE
## Advert-Story    23.166667     16.82428       TRUE
## Exposure-None    7.566667     16.82428      FALSE
## Exposure-Story   1.900000     16.82428      FALSE
## None-Story       9.466667     16.82428      FALSE
```

---

### More focused comparisons


```r
contrasts(data$infotype)
```

```
##          Exposure None Story
## Advert          0    0     0
## Exposure        1    0     0
## None            0    1     0
## Story           0    0     1
```

```r
levels(data$infotype) <- c("None", "Advert", "Story", "Exposure") # re-order levels
kruskalmc(data$beliefs ~ data$infotype, data = data, cont = "two-tailed") # compare all to first level
```

```
## Multiple comparison test after Kruskal-Wallis, treatments vs control (two-tailed) 
## p.value: 0.05 
## Comparisons
##                obs.dif critical.dif difference
## None-Advert   21.26667     15.26651       TRUE
## None-Story    13.70000     15.26651      FALSE
## None-Exposure 23.16667     15.26651       TRUE
```

---

## Categorical data

- So far we've mostly looked at predicting continuous variables (except logistic regression)
- For categorical, we instead look at frequencies rather than the mean
- Consider a simple contingency table, where we analyze the # of 'things' that fall into each combination of categories

---

## Pearson Chi-Square test

- Test for relationship between 2 categorical variables
- Compare the frequencies you observe vs. the frequencies you would expect to see by chance

  $$\chi^{2} = \frac{\sum (observed_{ij} - model_{ij})^{2}}{model_{ij}}$$ (where $$i,j$$ are rows/column numbers of contingency table)
  
- So what is the 'model'?
  - In ANOVA: group means
  - Here: expected frequency $$model_{ij} = \frac{row-total_{i} \times column-total_{j}}{total -observations}$$
  - So in a 2 x 2 table, each cell would have a $$model_{ij}$$

---

## Pearson Chi-Square test

- once $$\chi^{2}$$ is calculated, we can find the $$dof = (r-1)(c-1)$$
- So for 2 x 2 table, dof = 1, then look at the distribution

### Assumptions:
- check to make sure no expected frequencies are < 5
- independence: can't use $$\chi^{2}$$ test on a repeated-measures design

- Break down significant test w/ standardized residuals
  - analogous to breaking down a significant ANOVA

  $$standardized\,residual = \frac{observed_{ij} - model_{ij}}{\sqrt{model_{ij}}}$$
  - behaves like a z-score, can assess signficance directly

---

## Categorical analysis in R

Grab data: [http://bit.ly/2fZwh7r](http://bit.ly/2fZwh7r)


```r
bike_data <- read.delim("Handlebars.dat")
bike_data
```

```
##        Hands Nationality Frequency
## 1 One Handed       Dutch       120
## 2 One Handed     English        17
## 3 Two Handed       Dutch       578
## 4 Two Handed     English       154
```

---


```r
# Create contingency table
one_hand <- subset(bike_data$Frequency, bike_data$Hands == "One Handed")
two_hand <- subset(bike_data$Frequency, bike_data$Hands == "Two Handed")
bike_table <- rbind(one_hand, two_hand)
bike_table
```

```
##          [,1] [,2]
## one_hand  120   17
## two_hand  578  154
```

```r
colnames(bike_table) <- c("Dutch", "English")
```

---


```r
library(gmodels)
CrossTable(bike_table, fisher = FALSE, chisq = TRUE, expected = TRUE, sresid = TRUE)
```

---


```
## 
##  
##    Cell Contents
## |-------------------------|
## |                       N |
## |              Expected N |
## | Chi-square contribution |
## |           N / Row Total |
## |           N / Col Total |
## |         N / Table Total |
## |-------------------------|
## 
##  
## Total Observations in Table:  869 
## 
##  
##              |  
##              |     Dutch |   English | Row Total | 
## -------------|-----------|-----------|-----------|
##     one_hand |       120 |        17 |       137 | 
##              |   110.041 |    26.959 |           | 
##              |     0.901 |     3.679 |           | 
##              |     0.876 |     0.124 |     0.158 | 
##              |     0.172 |     0.099 |           | 
##              |     0.138 |     0.020 |           | 
## -------------|-----------|-----------|-----------|
##     two_hand |       578 |       154 |       732 | 
##              |   587.959 |   144.041 |           | 
##              |     0.169 |     0.689 |           | 
##              |     0.790 |     0.210 |     0.842 | 
##              |     0.828 |     0.901 |           | 
##              |     0.665 |     0.177 |           | 
## -------------|-----------|-----------|-----------|
## Column Total |       698 |       171 |       869 | 
##              |     0.803 |     0.197 |           | 
## -------------|-----------|-----------|-----------|
## 
##  
## Statistics for All Table Factors
## 
## 
## Pearson's Chi-squared test 
## ------------------------------------------------------------
## Chi^2 =  5.437138     d.f. =  1     p =  0.01971294 
## 
## Pearson's Chi-squared test with Yates' continuity correction 
## ------------------------------------------------------------
## Chi^2 =  4.904869     d.f. =  1     p =  0.02678109 
## 
## 
```

---

## Effect size

- most useful measures is the odds ratio

$$\textrm{odds}_{\textrm{Dutch bike one-handed}} = \frac{\textrm{number that bike one-handed}}{\textrm{number that bike two-handed}}$$

$$\textrm{odds}_{\textrm{English bike one-handed}} = \frac{\textrm{number that bike one-handed}}{\textrm{number that bike two-handed}}$$

$$\textrm{odds ratio} = \frac{\textrm{odds}_{\textrm{Dutch bike one-handed}}}{\textrm{odds}_{\textrm{English bike one-handed}}}$$

---

## References

Field A, Miles J, Field Z. Discovering Statistics Using R. London: Sage; 2012.
