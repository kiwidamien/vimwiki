Cold Start problem at Delivery Hero

(Solving the Item Cold Start Problem in E-commerce Search @ MICES 2023)

[This talk](https://www.youtube.com/watch?v=-SaJ3DxvozU) was an excellent overview of the cold-start problem, and more importantly, the approaches
that Delivery Hero (basically UberEats/DoorDash) took to onboarding new restaurants in their ranking.

They use the standard two-step process of retrevial (Elastic-Search) followed by Ranking.

For context on the size of the problem, new restaurants were initially showing up in position ~47 of
queries. The new restaurants also made a relatively small fraction of their client base, leading to 
measurement / precision issues.


## Artificial boosting

Move new restaurants up N spots in the ranking index, or add/multiply model score by some fixed amount.

Relatively simple, but no good way to pick the hyperparameters.

Implemented an A/B test, result was NEUTRAL.
Came away with more questions (did we pick the right boosting factor? Should it be constant? Should we have a secondary model for the boosts?)
Implemented model was a boost of the number of places.

## Cold Start Features

This approach was tried by YouTube and cited in their paper. It was to look at the "age" of items (YT: days since upload) which
translated to restaurant age. One thing not discussed in the talk was the importance of using rates (e.g orders is a pretty bad
feature -- orders per day, or orders in last 90 days, are much better as they give you an idea of the popularity of a restaurant that
is distinct from its age). 

This worked well for youtube because people are often searching for new content.
It did not work well for Delivery Hero. Data distribution was different.

A/B test performed, result was NEUTRAL.

**Takeaway** From the user perspective, is the user frequently looking for new items to consume? If so, this might
be a viable strategy. Otherwise unlikely to be successful.

## Dropout Net

Inspired by Neural Networks. Sets some of the feautures on established restaurants to zero to match that of new restaurants, and prevents the model
from developing an overreliance on the new features.

Ran A/B test:
* Results were NEUTRAL on new restaurants, so did not have desired effect
* Made performance on established restaurants measurably worse.

## Fill-in Missing Interactions

Developed a model to predict the values the new restaurants would have when they become established, and used those in the model.
Basically a form of MICE imputation / Bayesian priors on feature values. Would have loved to hear more details here.

Ran A/B tests, WORKED!!

## Hybrid Ranking

Assumption was that relying on historical features put new restaurants at a disadvantage, compared to established restaurants.
The elastic search model only used point in time features. So the approach was
* Do the retrieval step, with a ranking established by ElasticSearch Score
* For a new restaurant, it keeps the ranking it has from the ES score
* Established restaurants go through the standard ranking step, and are ordered (skipping spots taken by the new restaurants)

### Example

An example may help.
* A,B,C,... are established restaurants
* a,b,c,... are new restaurants

ES query returns:  A, b, B, c, D, E as results (with some scores that we rank by, then ignore)
The ML model is run on A, B, D, E. We get scores and place them in the order D, E, A, B.
Now we preserve the ES order for the new restaurants (b and c are in 2nd and 4th place from ES). We reorder established restaurants based on the model.

Final order:
```
D b E c A B
```

A/B test run and saw success! It was about as effective as the method `Fill-in Missing Interactions`.

### Commentary on this solution

The problem I have with this method is that there is no interpolation between new restaurants and established restaurants.
Instead you either have the model based score, or the ES based score. If they were on comparable scales, it would be possible
to do some form of linear interpolation between the two

e.g. 
```
Score = (A / (A + num_days)) * model_score + (num_days / (A + num_days)) * ES_score
```
It would not prevent high `model_score` displacing the new restaurants, so this is not a complete replacement.

## Things that were mentioned but not tried

- Multi-armed bandits, as a way of trying different explore-exploit tradeoffs w.r.t new restaurants

## Things that were not mentioned or tried

- Reweighting new restaurants
- Upsampling new restaurants 
