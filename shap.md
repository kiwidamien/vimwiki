# Feature importance: SHAP values

SHapley Additive exPlanations (SHAP) is a game theory based way of assigning feature importance.
One of the best QUICK introductions is here:
https://shap.readthedocs.io/en/latest/example_notebooks/overviews/An%20introduction%20to%20explainable%20AI%20with%20Shapley%20values.html

This is one of the best introductions I have read that goes into detail with the mathematics (and in particular explaining why SHAP value calculations are NP hard)
https://towardsdatascience.com/shap-explained-the-way-i-wish-someone-explained-it-to-me-ab81cc69ef30


Very roughly:
- We have our inputs X, and our target $f(X)$
- The sum of all SHAP values for each component of X is $f(X) - E(f(X))$
- To compute the SHAP value of a particular feature $X_i$, we need to look at $E[f(X)|X_i=x_i]$ and compare to E[f(X)]

A much more detailed formation can be found in the online book "Interpretable Machine Learning": https://christophm.github.io/interpretable-ml-book/shap.html


## Pros
- they are additiative
- they are local, or can be
- they are often used globallym, by averaging over the data set

## Cons
- KernelSHAP is slow
- TreeSHAP produces unintuitive feature attributions

## Technical paper describing limitations of the SHAPLEY value
https://arxiv.org/pdf/2002.11097.pdf


## Summary of the technical details from the towardsdatascience

Take a model for predicting salaries with three features: age, gender, job

Let's look at 8 models (the power set of features):
1. no features
2. age only
3. gender only
4. job only
5. age and gender
6. age and job
7. gender and job
8. all featured (age, gender, job)

We can look at a particular row and look at the predictions for each model
1. 50k (i.e. average of the training set)
2. 40  (just using age as a regressor)
3. 48
4. 100
5. 39
6. 85
7. 95
8. 83

We can lay out the models as a directed graph, where two models are connected if there is exactly one new feature in the downstream model.
So 1 is the root, which are connected directly to 2,3,4 as our three single feature models, etc. In pictures
```
0-features:           1
                    / l \
		  /   l   \
	        /     l     \
1-features:   2       3      4
	      | \    / \    / |
	      |  \ /     \ /  l
	      l  / \     / \  l
	      l /   \   /   \ l
2-features:   5       6       7
               \      l      /
	         \    l    /
		   \  l  /
3-features:           8
```

The nodes represent models, the edges represent the marginal contribution of the newly added feature to a submodel.

For example, model 2 adds age as the only feature, which moves us from 50k to 40k. The marginal contribution of age is -10k.

There are 4 edges that contain a marginal contribution. Let's look at how we calculate the marginal contributions for age

| transition                          | values     | marginal contribution | weight |   |
|-------------------------------------|------------|-----------------------|--------|---|
| 1 --> 2, {} --> {age}               | 50 --> 40  | -10                   | 1/3    |   |
| 3 --> 5, {gender} --> {gender, age} | 48 --> 39  | -9                    | 1/6    |   |
| 4 --> 6, {job} --> {job, age}       | 100 --> 85 | -15                   | 1/6    |   |
| 7 --> 8, {gender, job} --> {all}    | 95 --> 83  | -12                   | 1/3    |   |

We haven't calculated weights yet, but are including them so we don't have to remake the table. Before calculating the weights, let's use them in the obvious way. The SHAP contribution of the feature `age` to the prediction is

```
SHAP age on this row = (1/3)(-10) + (1/6)(-9) + (1/6)(-15) + (1/3)(-12)
                     = 11.3 
```
Now to how the weights are calculated.  Here are some properties of the weights:
- the sum of all weights is normalized
- the sum of all weights from all 0 to 1 feature models is the same as the sum of all the weights from 1 to 2 feature models, etc
- the values of all feature 0 to feature 1 models should all be the same, all feature 1 to feature 2 models should all be the same, etc

That is, if the weights are labelled by the number of features of the terminal nodes, in our diagram we have $w_1$, $w_2$, and $w_3$. We know for a single feature like age, the weights are $w_1$, $w_2$, $w_2$, and $w_3$, so we have
- w1+2w2+w3=1  (normalization)
- w1=w3        (each have three edges)

A way I find easier is that the sum of weight coming out of root node is one, and all weight must flow through child nodes with equal weight along each edge. So this is a flow problem. Looking at the root node, it has three edges, so $w_1=1/3$. Each of those children has two edges, so the weight is evenly ditributed as $w_2=1/6$. We see that at each level of "we have F features" we can make a cut of the graph, so the sum of all weights entering or exiting a level is 1.

You can use combinatorics to show that the weight of an edge terminating at $f$ features is `1/(f*(F choose f))`, where F is the total number of features. So in this case:

- w1 = 1/(1*(3 C 1)) = 1/3
- w2 = 1/(2*(3 C 2)) = 1/6
- w3 = 1/(3*(3 C 3)) = 1/3

Note that we needed 2^F different models, so approximations have to be employed to make the calculation tractable.

TODO: I moved from w1--4, to w1--3, while redefining w3 in the process. Tidy this up.
TODO: Add motivation for WHY the weights are decided to follow these properties, rather than just giving them.

