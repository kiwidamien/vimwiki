# Priors and AB tests

Question: what if we accepted experiments that had a positive lift, without checking for significance?

The argument for doing it is that while we might accept some results that are not significant,
if there are no ongoing costs, we should still be moving in the generally positive direction.

Jonathan Landy had a shot at giving a principled answer to this question.

## Reconstruction of JL's argument (bev min 2022-10-20)

* call the true gain $g_i = 1 + \gamma_i$
* The collective true lift is $G=\prod_i g_i$
* Consider k=100 experiments, each with a point estimate and sigma at 0.5%
	* $\gamma_i\sim N(\mu, \sigma^2)$, $\mu=\sigma=0.005$
	* If the experiments are not large enough, we might not accept any of the experiments
	* If we accept all the experiments, the true total gain will be log normal $F(k\mu, k\sigma^2)$
* If the apriori liklihood of a positive gain is small, then most of the positive point
  estimate examples we see will be false positives.
* Need to consider MAP estimates
	* $p(\gamma_i|x) \sim N(\mu_m, \sigma^2_m)$ where $\sigma^2_m=(1/\sigma^2_0 + 1/\sigma^2_a)^{-1}$ and $\mu_m = (\mu_0/\sigma_0^2+\mu_1/\sigma_a^2)\times\sigma^2_a$
	* here a "0" subscript means true unmeasured outcome, and "a" is the experimental result
* JL showed when the distribution of true effects was -0.005 and std of 0.005 ($\mu_0=-0.005, \sigma_0=0.005$)
	* accepting all point estimates that have positive lift in experiments leads to a distribution with G peaked around 0.98
	* accepting all MAP estimates that have positive lift in experiments leads to a distribution with G peaked around 1.015

This is a quantification that if there is a prior that most effects will be negative, which
should be true in optimized parts of the business, there is a risk of us making things worse
by accepting everything with a positive point estimate.

If we are confident in placing a prior, we can make an MAP estimate, and gain a positive gain
gain, even if we don't wait for statistical significance.

An interesting observation is that a more mature business is more likely to be optimized already,
so the prior should be overall negative (we are already close to the optima!) whereas a new business
can get away with being more sloppy.

The nice part of this is that it is close to how businesses usually operate (initially they are scrapy
and don't "have time" for statistical significance) but as they grow more mature their view on
experimentation becomes more sophisticated.

The counter-intuitive part is as you grow a business larger, there is more percieved confidence
that we know what we are doing. It probably would come as a shock to most PMs that this might
be true, but that even with the expertise this is the time where most ideas will have negative ROI!
