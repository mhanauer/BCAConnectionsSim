---
title: "BCA for Connections"
output: html_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```
Test BCA with simulations
2 benefits

We have estimates from about 6 months so everything will need to be doubled, because we are analyzing everything on an annual basis.  

Medical expenses : 1500 with with an sd of 20.  Can draw from a normal distribution, because negative values means there was more expenses from pre to post 

Job income increase per year: 10000 per year

Program Costs: 5000 per year

Probably just to be safe assume about a 20% variation

Want to rep the costs 
What is the opportunity cost: What could they have been doing otherwise reasonably (maybe use the employment percentage from baseline and assume that for fourty years).  They could have had a year of 5 hours a week that they could have been doing
```{r}
med_expen = rnorm(n = 10000, mean = 1500, sd = 1500*.2)
job_income = rnorm(n = 10000, mean = 1500, sd = 1500*.2)
program_costs = rep(800000, 10000)
opp_cost = rnorm(n = 10000, mean = 7.25*5*52*72, sd = (7.25*5*52*72)*.2)
total_costs = program_costs + opp_cost
```
Next step create 40 sets of 10,000 just do this for one at a time
That is stupid, just create  400,000 random draws and create frame with 40 variables one for each 
```{r}
med_expen_40 = matrix(rep(rnorm(n = 400000, mean = 1500, sd = 100)), ncol = 40, nrow = 10000)
```
Now we need run a loop for each of the 40 variables.  Wonder if I can do an apply function and go down the rows and create a function with the npv thing
```{r}
library(FinCal)
dim(med_expen_40)
npv_med_40 = apply(med_expen_40, 1, function(x){npv(r = .03,x)})
head(npv_med_40)
summary(npv_med_40)

bca = npv_med_40/total_costs
```
Let us try a loop for the discount rate
Cannot have a loop inside a function that is already looping I guess.  Try to fix later.
```{r}
npv_med_40_loop = NULL
discount_rate = c(10:50)/100
for(i in 1:length(discount_rate)){
  npv_med_40_loop[[i]]= npv_med_40 = apply(med_expen_40, 1, function(x){npv(r = [[i]],x)})
}

```



Test to see if it doing what you think it is doing.  Take the first row of 40 and see if it matches the first value for npv_med_40

It is correct!!!!
```{r}
npv(r = .03, med_expen_40[1,])
```
Now run a quick t.test to evaluate if there is a statistically significant difference from 1 
```{r}
t.test(bca, mu = 1)
```


Need to discount these benefits over time

If we have 40 time points we can have four different values
Then we run this 10,000 times.  
This gives us 10,000 npv for costs and do this benefits.
Then get the BCA ratio for all 10,000
Then run a bayesian t.test and see if there is a statistically significant difference from 1.  Can use a slightly informed prior, but probably matter that much with 10,000.

So we can have 40 diffent
```{r}
library(FinCal)

npv(r = .03,med_expen[1:10])
```






