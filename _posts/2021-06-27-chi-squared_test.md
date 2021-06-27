---
layout: post
title: Chi-Squared Test
categories: [Fin]
---

Chi-Squared test will tell us that whether some observation is a random coincident or in fact a statistical significant result that can be applied or extrapolated on to the whole population. 

To run a Chi-Squared test you can't use relative values (e.g. percentages), you have to use absolute values.

You will need to specify:

- significance level

  A threshold value <u>below</u> which you believe something is significant highly correlated. Expressed in terms of percentage. We will use this to gauge p-value.

You will need to calculate:

- p-value

  In Excel, this can be done with the function: *CHITEST(\<actual_range\>, \<expected_range\>)*

Eg. 1

Let's say you have a table of observation:

|      | Positive | Negative |
| ---- | -------- | -------- |
| Cat  | 45590    | 8980     |
| Dog  | 34040    | 11390    |

Now you want to check whether the observations has any specific dependence on specie (here cat/dog). 

So you ask the question "How would an ideal table look if there was no dependence on specie?" 

The answer to the above question is already very simple. You weight each specie population with the fraction of total positive population and fraction of total negative population. Doing this will give you a new table which we call as the Expectation table:

|      | Positive | Negative |
| ---- | -------- | -------- |
| Cat  | 43660    | 10910    |
| Dog  | 36340    | 9090     |

And now you want to test the significance level of your Expectation table using Chi-Square test.

You see that your Observation table is different from Expectation table.

The Chi-Square test will tell you whether this difference is a random difference or a statistically significant difference.

While constructing, by default we assume that it is a random difference. The test will validate or disprove our assumption.

>  Chi-Square test is a test designed to test the **probability of independence**.



Ok, so then we need to calculate **p-value**. 

p-value for the above expectation and observation is: 1.75E-26

Now, say our significance level is 0.05 (*i.e.* 5%) 

Therefore, 

sign_level = 0.5

If p-value < sign_level:

​	"NOT RANDOM"

else:

​	"Independent"


