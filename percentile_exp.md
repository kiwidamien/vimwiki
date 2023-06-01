# Experimentation with percentiles

[This article](https://towardsdatascience.com/how-wish-a-b-tests-percentiles-35ee3e4589e7) looks at percentile experimentation at Wish.com

They break down an experimental methodology given in [this arXiv preprint](https://arxiv.org/pdf/1903.08762.pdf): "Large scale online experimentation with Quantile metrics"

## Basic idea

We know that we are not just interested in measuring the average. Instead we are interested in measuring the (say) P95 loading time of a page.

The problem is that at a user level, the requests are going to be highly correlated. This is because most experiments randomize users, which
ensures that the difference between users is randomized. However, performance measures are likely correlated, which violates the iid assumptions of
our tests. That is, we are doing analysis at the level of requests, but allocation at the level of users.

This can lead to significant false positive rates, when we pool experiments. This can be shown by AA tests.

Intuition:
* Let's have a control with latency 300ms, a treatment with latency 100ms
* If the samples are the same size, then we have a pooled effect of 200 ms
* In the pooled set, the bulk of the results are coming from the treatment with the larger average

Now if you do an A/A test, there is 
