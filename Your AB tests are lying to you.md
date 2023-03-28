# Your AB tests are lying to you

Great talk by John Cleavanger at Stitch Fix, following the basic
principle that if we are making a selection from experiments that
passed an experimental test, the selection process will bias the
size of the effect.

Roughly speaking, this is similar to regression to the mean. Any
experiment has a signal plus sampling noise, and so an experiment
that passes an acceptance threshold is more likely to have helpful
contributions from signal noise. Therefore, looking at accepted experiments,
we would expect the effect size to be over-inflated.

This is a particular problem when you have many different AB tests
and you combine their effects. A publically accessible article that
makes similar claims is [this airbnb article](https://medium.com/airbnb-engineering/selection-bias-in-online-experimentation-c3d67795cceb). A more academic treatment, still by folks at Airbnb, can be 
found at [this arXiv link](https://arxiv.org/pdf/1910.03788.pdf)

## Simplest example of the problem

If we have a standard AB test with a significance level of 5%, then
if I run 10,000 experiments that have no effect, I would expect to
declare statistical significance in 500 of them.

If I were to measure the lift (recall the true lift is 0, as we are
positing there is no true effect) then the experiments would have results
censored away from zero. If we only took the positive effects, we would
be adding 250 positive effects instead of getting zero!

We know when we run an AB test that we have a risk of generating a
false positive. What John C and the airbnb teams are reminding us of
is that this will also bias the effect size.

I liked the way this point was made:
- standard AB tests are written to support a go/no-go decision, and have an explicit false positive if no effect rate
- standard AB tests are not designed to do effective estimation of effect size; if you are trying to do this you need to correct for the false positive rate

## Correction strategies

**Replication**
If the problem is really sampling error, then repeating the test on the
positive results can reduce the error. In the example of 10k experiments
with true lift of zero, the first round of experiments will yield 500 
positive results. A second round would reduce this to only 25 experiments
that pass.

**Power your experiment**
If the power of your experiment is 1, then you won't get inflation. 
This argument does not completely make sense to me, I would have thought given the 
example that we would want the false positive rate to go to zero.

The basic advice would be to increase the sample size in either case.
In both cases you would need to run the experiment for longer, increasing the
cost of running your experiments.

**Simulate to estimate the inflation**
Roughly determine how much your results are inflated via monte carlo simulation,
and then use that to correct for inflation.

Does require for us to make assumptions.

## How to simulate

This is the way it was done in JC's talk, although I think a prior approach would be
be better than a point estimate.

e.g. control is a binomial with p=0.5, and a treatment of p=0.505 as the effect (true size)

Simulate the difference in effect sizes with sample size N.
Measure the effect size in a large number of simulations, and compare that to your actual lift that you created.
You can get a distribution of inflation amounts, so you know how much to correct.

HOWEVER, the problem we actually have is that we don't know the true effect. 
So we can do the usual treatment -- assume the result of the experiment is the actual lift, then rerun the simulation to determine the presumtive lift.
The logic is a little bit circular. If we are up for making a stronger set of assumptions,
we could posit a prior on experiment results, and then estimate the most likely lift and estimate the correction factor.


