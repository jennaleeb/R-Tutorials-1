---
title       : Tutorial 9 - Repeated-Measures and Mixed Design
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
categories  : ['R', 'ANOVA']
---



## Repeated-Measures and Mixed Design

   **Jenna Blumenthal**  
   Tutorial #: 9  
   MIE 1402

---

## Introduction

- 'Repeated-measures' - when the same entities participate in all conditions of an experiment or provide data at multiple time points
- Previously, we have looked at differences between several means when different entities contribute to different means
- BUT - the assumption of independence is now violated
  - data taken under different experimental conditions (but same entity) are likely to be related

---

## Sphericity

- Instead, we assume that the relationship between pairs of experimental conditions is similar (sphericity)
  - equality of variances of the _differences_ between treatment levels
  - you need at least 3 conditions for sphericity to be an issue (i.e. you need to test for it)

<p style="text-align: center;">$$var_{A-B} \approx var_{A-C} \approx var_{B-C}$$
</p>

- Can also be assessed with Mauchly's test (null hypothesis: variances between conditions are equal)

---

## Dealing with violations of sphericity
(to produce a valid $$F$$-ratio)

1. Apply a correction factor to the degrees of freedom in the $$F$$-ratio:
  - Greenhouse-Geisser correction $$\hat\varepsilon$$ (varies between 1 & $$\frac{1}{k-1}$$)
  - Hynh-Feld correction $$\tilde\varepsilon$$ (less conservative)

2. Use multivariate analysis of variance (MANOVA)
  - don't need assumption of sphericity
  
3. Analyse data as a multilevel model
  - regression where we can include multiple observations form the same entities

---

## Theoretical framework

- In independent ANOVA, within-participant variance is our $$SS_{R}$$
- In repeated measures, some of the within-participant variation comes from the effects of our experimental manipulation
- as w/ independent design, the $$F$$-ratio is the variation due to experimental manipulation ($$SS_{M}$$) vs. variation due to random factors ($$SS_{R}$$)

![](/R-Tutorials-1/figure/source/2016-11-15-tutorial-9-repeated/partitioning_variance.jpg)
*Field*, p. 556

---

## Repeated-measures in R

Grab data: [http://bit.ly/2fT3QXW](http://bit.ly/2fT3QXW)


| Essay| tutor1| tutor2| tutor3| tutor4|
|-----:|------:|------:|------:|------:|
|     1|     62|     58|     63|     64|
|     2|     63|     60|     68|     65|
|     3|     65|     61|     72|     65|
|     4|     68|     64|     58|     61|
|     5|     69|     65|     54|     59|
|     6|     71|     67|     65|     50|

---

### Step 1: Format data


```r
dataLong <- melt(data, id = "Essay")
kable(dataLong)
```



| Essay|variable | value|
|-----:|:--------|-----:|
|     1|tutor1   |    62|
|     2|tutor1   |    63|
|     3|tutor1   |    65|
|     4|tutor1   |    68|
|     5|tutor1   |    69|
|     6|tutor1   |    71|
|     7|tutor1   |    78|
|     8|tutor1   |    75|
|     1|tutor2   |    58|
|     2|tutor2   |    60|
|     3|tutor2   |    61|
|     4|tutor2   |    64|
|     5|tutor2   |    65|
|     6|tutor2   |    67|
|     7|tutor2   |    66|
|     8|tutor2   |    73|
|     1|tutor3   |    63|
|     2|tutor3   |    68|
|     3|tutor3   |    72|
|     4|tutor3   |    58|
|     5|tutor3   |    54|
|     6|tutor3   |    65|
|     7|tutor3   |    67|
|     8|tutor3   |    75|
|     1|tutor4   |    64|
|     2|tutor4   |    65|
|     3|tutor4   |    65|
|     4|tutor4   |    61|
|     5|tutor4   |    59|
|     6|tutor4   |    50|
|     7|tutor4   |    50|
|     8|tutor4   |    45|

---

### Step 1: Format data


```r
names(dataLong) <- c("Essay", "Tutor", "Grade")
str(dataLong$Tutor)
```

```
##  Factor w/ 4 levels "tutor1","tutor2",..: 1 1 1 1 1 1 1 1 2 2 ...
```

---

### Step 2: Exploring the data

- parametric assumptions, graph the data, etc.
- contrasts?


```r
contrasts(dataLong$Tutor)
```

```
##        tutor2 tutor3 tutor4
## tutor1      0      0      0
## tutor2      1      0      0
## tutor3      0      1      0
## tutor4      0      0      1
```

- must be orthogonal if you want Type III sums of squares, see _Field_, 568, but we only have 1 variable

---

### Step 3: Compute ANOVA


```r
library(ez)
model.1 <- ezANOVA(dataLong, dv = .(Grade), wid = .(Essay), within = .(Tutor), detailed = TRUE)
```

---

### Step 4: Examine ANOVA

- 4.1 - check sphericity assumption


```
## $ANOVA
##        Effect DFn DFd        SSn      SSd           F            p p<.05
## 1 (Intercept)   1   7 130816.125  103.375 8858.165659 4.027213e-12     *
## 2       Tutor   3  21    554.125 1048.375    3.699893 2.784621e-02     *
##         ges
## 1 0.9912725
## 2 0.3248333
## 
## $`Mauchly's Test for Sphericity`
##   Effect         W          p p<.05
## 2  Tutor 0.1310624 0.04305676     *
## 
## $`Sphericity Corrections`
##   Effect       GGe      p[GG] p[GG]<.05       HFe      p[HF] p[HF]<.05
## 2  Tutor 0.5576185 0.06287878           0.7122543 0.04712856         *
```

---

### Step 5: Individual contrasts/post-hoc

- didn't really have a hypothesis so no informative contrasts


```r
pairwise.t.test(dataLong$Grade, dataLong$Tutor, paired = TRUE, p.adjust.method = "bonferroni")
```

```
## 
## 	Pairwise comparisons using paired t tests 
## 
## data:  dataLong$Grade and dataLong$Tutor 
## 
##        tutor1 tutor2 tutor3
## tutor2 0.022  -      -     
## tutor3 1.000  1.000  -     
## tutor4 0.261  0.961  0.637 
## 
## P value adjustment method: bonferroni
```

---

## Factorial repeated-measures design

- Nothing terribly new
- More than one independent variable, add it to ` within = .(var1, var2)`
- Now we look at main effects + interaction effects
- Again check sphericity
- Break down main effects using contrasts/post-hoc tests

---

## Mixed Designs

- Includes both between groups & repeated measures
- _Field_, Ch. 14 example: what do people look for in a partner?
- Independent variables:
  - Personality
  - Looks
  - Gender

---

## Mixed Design using `ezANOVA()`

(not going through all data set-up)





```r
model.2 <- ezANOVA(data = datingLong, dv = .(rating), wid = .(participant), between = .(gender), within = .(looks, personality), detailed = TRUE)
```

---


```r
model.2
```

```
## $ANOVA
##                     Effect DFn DFd        SSn       SSd            F
## 1              (Intercept)   1  18 846249.800  760.2222 2.003690e+04
## 2                   gender   1  18      0.200  760.2222 4.735457e-03
## 3                    looks   2  36  20779.633  882.7111 4.237325e+02
## 5              personality   2  36  23233.600 1274.0444 3.282498e+02
## 4             gender:looks   2  36   3944.100  882.7111 8.042699e+01
## 6       gender:personality   2  36   4420.133 1274.0444 6.244868e+01
## 7        looks:personality   4  72   4055.267 1992.6222 3.663253e+01
## 8 gender:looks:personality   4  72   2669.667 1992.6222 2.411596e+01
##              p p<.05          ges
## 1 7.013239e-29     * 9.942319e-01
## 2 9.458958e-01       4.073486e-05
## 3 9.594812e-26     * 8.088849e-01
## 5 7.689430e-24     * 8.255493e-01
## 4 5.234187e-14     * 4.454748e-01
## 6 1.974986e-12     * 4.737685e-01
## 7 1.101308e-16     * 4.523510e-01
## 8 1.107801e-12     * 3.522328e-01
## 
## $`Mauchly's Test for Sphericity`
##                     Effect         W         p p<.05
## 3                    looks 0.9602054 0.7081010      
## 4             gender:looks 0.9602054 0.7081010      
## 5              personality 0.9293298 0.5363446      
## 6       gender:personality 0.9293298 0.5363446      
## 7        looks:personality 0.6133545 0.5339382      
## 8 gender:looks:personality 0.6133545 0.5339382      
## 
## $`Sphericity Corrections`
##                     Effect       GGe        p[GG] p[GG]<.05       HFe
## 3                    looks 0.9617284 7.624114e-25         * 1.0744125
## 4             gender:looks 0.9617284 1.487026e-13         * 1.0744125
## 5              personality 0.9339944 2.056621e-22         * 1.0380537
## 6       gender:personality 0.9339944 9.442426e-12         * 1.0380537
## 7        looks:personality 0.7993543 9.003598e-14         * 0.9922411
## 8 gender:looks:personality 0.7993543 1.470422e-10         * 0.9922411
##          p[HF] p[HF]<.05
## 3 9.594812e-26         *
## 4 5.234187e-14         *
## 5 7.689430e-24         *
## 6 1.974986e-12         *
## 7 1.426883e-16         *
## 8 1.337876e-12         *
```

---

## References

Field A, Miles J, Field Z. Discovering Statistics Using R. London: Sage; 2012.
