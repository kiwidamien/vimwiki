# CUPED - Controlled Experiment using Pre-Experiment Data

## High level
Use covariates to reduce the variance in an A/B test, to increase the power of the test

## Start with the example from https://towardsdatascience.com/understanding-cuped-a822523641af

Assume that we have some users that we have been observing before the test starts. We have a new ad campaign, and want to do a standard A/B test.et's look at the revenue of the cell before the test starts  (i.e. everyone gets the control) and then after the test starts.

| id | cell      | pre-revenue | during-revenue |
|    |           |             |                |
|    |           |             |                |
| 0  | control   | 5.32        | 8.36           |
| 1  | treatment | 2.97        | 7.75           |
| 2  | control   | 4.69        | 9.02           |
| 3  | control   | 5.82        | 8.54           |
| 4  | control   | 5.23        | 8.91           |
|    |           |             |                |
|    |           |             |                |
|    |           |             |                |
|    |           |             |                |
|    |           |             |              |i

etc.

If we ignore the `pre-revenue` column (i.e. the revenue of the user in the month before the experiment started) we would just have the setupfor a standar A/B test. We could average the results in the `during-revenue` experiment, stratified on cell, and use that to determine the average treatment effect. 

If assignment to the cells was done randomly, this would be a causal estimate of the size of the intervention (the ATE)

Running a linear regression `Y ~ cell` gives us an estimate of the coefficent of the cell indicator variable of 1.7914, with a stand error o 0.3.1.

Now let's see how CUPED can be used.

## CUPED

We have the following linear equation: $Y=\alpha + \beta C$, where C is an indicator variable (0 for control, 1 for treatment). To use CUPED weneed a variable X with the following properties:
- it is not affected by the treatment
- it has a known expectation E[X]
The we can define $Y_c=Y-\theta (X - E[X])$. We see that $E[Y_c]=E[Y]$, because the average of the adjustment term is zero. The variance is where thhings are a little ore interesting

```
Var(Y_c) = Var(Y - theta X f + theta E[X])
         = Var(Y - theta X)
	 = Var(Y) + theta^2 Var(X) -2 theta Cov(X, Y)
```

Here $\theta$ is any real number. We can choose to minimize this function by choosing $\theta$ to take the specific value Cov(X, Y)/Var(X).
Note this is the OLS estimate of the coefficent between X and Y!

The variance of Yc is Var(Yc)=Var(Y)(1-rho^2)

We can simplify even further, since theta E[X] is contant, we can look at $Y2=Y-\theta X$ in the regression. We can compare the difference of the Y2 in the different cells to get an average of the treatment and the control. 

On the simulated data set in the article, the procedure was
1. Use X as the pre-test revenue (everyone in the control group). This is uncorrelated that the assignment, since the assignment is happening randomly, but should be corrected with the during-test revenue as "big spenders" are still big spenders
2. Do a regression of Y1 = Y0 - theta X to estimate theta
3. Then calculate Y2 = Y1 - theta Y0
4. Look at the differenc of Y2 between the test and control groups

## CUPED vs other variance reduction techniques

CUPED is similar in flavor to difference in difference approaches, where we could imagine loooking at the difference in pre-test revenues (by cell) and then look at the differences in post-test revenues (by cell).
