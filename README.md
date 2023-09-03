# Survival Analysis in R: Analyses, Packages and Functions

## Kaplan-Meier Analysis
The KM analysis is the most basic of the analyses -- it estimates survival probabilities and generates a KM curve. To be useful, you must use the survival probabilities to compare survival between groups, visualize survival curves, and identify potential differences in survival patterns.

### Package: Survival
```
install.packages("survival")
install.packages("ggplot")
library(survival)
library(ggplot2)
```
### Functions:

### 1. "survfit"
**'survfit'** fits a simple survival model. This is the function used to create survival curves in R based on the Kaplan-Meier estimator. It estimates the KM survival curves for the given data. 

For example:

```
km_fit <- survfit(Surv(fu_time, death) ~ 1)
```

**'Surv(fu_time, death)'** defines the survival object for the analysis. The **'Surv'** function is used to specify the time-to-event data, where fu_time represents the follow-up time and death represents the event indicator (1 if an event occurred, 0 if censored). ~ 1 is the formula specification for the survival analysis. Tilde (~) separates the response variable **'(Surv(fu_time, death))'** from the explanatory variable. In this case, 1 indicates that there are no explanatory variables, meaning we are interested in estimating the overall survival curve without considering any specific grouping factors.

**'km_fit'** is the survfit object obtained from the KM analysis. Plotting **'km_fit'** will show show the survival curve:

```
plot(km_fit)
```

![kmfit](https://github.com/1Genevieve/Survival_Analysis/blob/master/kmfit2.JPG)

**'km_fit'** also contains the survival probabilities at different time points, and other relevant information such as the number at risk and the number of events at each time point. **'km-fit'** can be used to plot the survival curve or perform further analyses on the survival data. 

### 2. "surv" 
**'Surv'** produces the KM estimates of the probability of survival over time. For instance, we want to extract the survival probabilities from **'km_fit'** object generated by the **'survfit'** function. We use the **'summary'** function in R. 

```
km_summary <- summary(km_fit)
surv_prob <- km_summary$surv
```

The **'summary'** function is applied to the **'survfit'** object, **'km_fit'** to calculate summary statistics, including the survival probabilities at different time points. These are stored in **'km_summary'**. You can use the **'km_summary'** object to access and analyze the survival probabilities at different time points obtained from the KM analysis. The specific statistics include number at risk, number of events, survival probability and confidence intervals depending on the settings and options used in the survfit object and the summary function:

![kmsummary](https://github.com/1Genevieve/Survival_Analysis/blob/master/kmsummary.JPG)

By assigning **'km_summary$surv'** to the object **'surv_prob'**, you extract the survival probabilities as a vector:

![survprob](https://github.com/1Genevieve/Survival_Analysis/blob/master/survprob.JPG)

A further example:

```
summary(km_fit, times = c(1:7,30,60,90*(1:10))) 
```

By specifying the times argument, you can request summary statistics for the KM survival curve represented by **'km_fit'** at specific time points. In this case, the code requests summary statistics at time points 1, 2, 3, 4, 5, 6, 7, 30, 60, 90, 180, 270, 360, 450, 540, 630, 720, 810, 900:

![summary](https://github.com/1Genevieve/Survival_Analysis/blob/master/summary1.JPG)

## Log-Rank Test
Compared to the KM anaylysis, the log-rank test determines whether there is a significant difference in the survival curves or survival probabilities of groups. It is a non-parametric test.

### Function: "Survdiff" 
**'survdiff'** compares the survival curves of 2 groups using the log rank test. It calculates the test statistic and p-value for comparing survival curves. For example, the survival curves of two groups based on gender are compared: 

```
survdiff(Surv(fu_time, death) ~ gender, rho=0) 
```

![survdiff](https://github.com/1Genevieve/Survival_Analysis/blob/master/survdiff1.JPG)

THe chi-squared test statistic measures the discrepancy between the observed data and the expected data under the null hypothesis. In the context of survival analysis, the chi-squared statistic compares the observed survival data among different groups. It provides a measure of the discrepancy between the observed survival data and the expected survival data under the null hypothesis.

**Null hypothesis** - the chi-squared test starts with a null hypothesis, which assumes that there is no difference between the groups being compared. In the case of survdiff, it assumes that the survival curves of the groups are identical.

**Expected Data** - under the null hypothesis, the expected survival data is calculated based on the assumption of no difference between the groups. The expected data represents what would be observed if the null hypothesis is true.

**Observed Data** - actual data collected or obtained from the study.

**Interpretation** - the obtained chi-squared statistic is compared to a chi-squared distribution with a specific number of degrees of freedom. A higher chi-squared value indicates a larger difference between the groups, while a smaller value suggests a smaller difference or similarity. The degrees of freedom depend on the number of categories or groups being compared. The resulting p-value indicates the probability of obtaining a chi-squared statistic as extreme as the observed one, assuming the null hypothesis is true.

The chi-squared statistic of 0.1 with 1 degree of freedom and a p-value of 0.8 indicates that the observed differences in the data are likely due to chance or random variation. There is weak evidence to suggest a significant difference between the groups being compared based on the chi-squared test results.

Plot of KM curves:

```
km_gender_fit <- survfit(Surv(fu_time, death) ~ gender)
plot(km_gender_fit)
```

![km_gender](https://github.com/1Genevieve/Survival_Analysis/blob/master/kmfit_gender.JPG)

## Cox Proportional Hazards Model


### Package: Survminer
```
install.packages("survminer")
library(survminer)
```
**'Survminer'** is a powerful package for visualizing and interpreting survival analysis results. It complements the **'Survival'** package and provides a range of functions to create informative and aesthetically pleasing survival plots and summary tables. It's particularly useful for communicating complex survival analysis results to both technical and non-technical audiences.

### Function: "Coxph"

**Example 1: continuous predictor**
```
cox <- coxph(Surv(fu_time, death)~age, data = g)
summary (cox)
```
![coxph_age](https://github.com/1Genevieve/Survival_Analysis/blob/master/coxph_age.JPG)

**data=g** - takes variables straight from dataset **g**

**coef** - estimated log hazard ratio. Quantifies the logarithm of the hazard ratio with age as predictor variable.

**exp(coef)** - exponential of the coefficient. The hazard ratio itself or relative risk. Taking the exponential of the coefficient (i.e., exp(coef)) gives the hazard ratio associated with a one-unit increase in age. If exp(coef) > 1, a one-unit increase in age leads to an increased hazard (higher risk). If exp(coef) < 1, a one-unit increase in age leads to a decreased hazard (lower risk). Thus, every year increase in age increases the hazard in the older group by 6%. The increase is linear meaning older people have the same 6% increase in risk of death as younger people every year.

**Hazard ratio** - ratio of hazard rates between two groups (usually defined by a binary variable or a unit change in a continuous variable). HR>1 suggests an increased hazard rate in the compared group, HR<1 suggests a decreased hazard rate, and HR=1 suggests no difference in hazard rates. A HR (exp(coef)) significantly different from 1 (often assessed by the associated p-value) indicates that the variable has a significant effect on the hazard of an event occurring. For example, an HR of 2 suggests that the event is occurring approximately twice as fast in one group compared to the other.

Both **coef** and **exp(coef)** are valuable for interpreting the impact of predictor variables on survival outcomes in a Cox regression model. **exp(coef)** provides a more intuitive interpretation in terms of risk change, while **coef** is important for understanding the magnitude of the effect on the log hazard ratio.

**exp(-coef)** - how the hazard changes when the predictor variable decreases by one unit.

**se(coef)** - standard error of the coefficient estimate. It represents the standard deviation of the sampling distribution of the coefficient estimate. It is a measure of the uncertainty associated with the coefficient estimate. Smaller standard errors indicate more precise estimates, while larger standard errors indicate more uncertainty.

**z** - a measure of how many standard deviations the coefficient estimate is away from zero. Positive z-scores indicate that the coefficient is larger than expected by chance, while negative z-scores indicate that the coefficient is smaller than expected by chance.

**Pr(>|z|)** - the p-value associated with the z-score. It measures the probability of obtaining a z-score as extreme as the observed one if the null hypothesis is true (i.e., the coefficient is zero or has no effect). A small p-value (typically less than a chosen significance level, often 0.05) suggests that the coefficient is statistically significant, indicating that it's unlikely to have occurred by chance alone. A large p-value suggests that the coefficient is not statistically significant, meaning that it could plausibly have arisen due to random variation.

**exp(coef±1.96 X se(coef))** - confidence interval of the hazard ratio. It provides a range within which the true hazard ratio is likely to lie.

**Concordance index** -  a measure of the discriminatory power of a survival model. It assesses the ability of the model to correctly order pairs of subjects with different survival times. It measures how well the model can distinguish between subjects who experienced an event at different times. It ranges from 0 to 1. A value of 0.5 indicates random predictions (similar to flipping a coin), while a value of 1 indicates perfect predictions (the model correctly ranks all pairs of subjects).

**Example 2: categorical predictor**
```
cox <- coxph(Surv(fu_time, death) ~ ethgrp, data = g)
summary (cox)
```
![coxph_ethgrp](https://github.com/1Genevieve/Survival_Analysis/blob/master/coxph.JPG)

**Example 3: multiple predictors**
```
mcox <- coxph(Surv(fu_time, death) ~ age + gender + copd + prior_dnas + ethgrp)
summary (mcox)
```
![mcox](https://github.com/1Genevieve/Survival_Analysis/blob/master/mcox.JPG)


## Tests of the Proportional Hazard Assumption

**Martingale test of proportional hazards assumption for Example 1:
```
ap <- cox.zph(cox)
print(ap)
plot(ap)
```
![residuals_age](https://github.com/1Genevieve/Survival_Analysis/blob/master/res_age.JPG)

