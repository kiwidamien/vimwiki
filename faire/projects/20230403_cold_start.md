# Cold start project

Started 2023-04-03
Link in notion: https://www.notion.so/faire/Category-Page-Ranking-with-Cold-Start-Features-9b128827abe64829802d96b8a0e7e73a?pvs=4
Initiative: f5

### Context

When we open up the category pages, we get a grid of tiles that link to PDPs -- the "product-grid".
The product grid in a category page is referred to as the CatNav, and the ranking of products on that
page is personalized via a RTR stack, which is basically the feature store + an XG boost model.


## Goal:
Improve the CatNav product-ranking real-time ranking (RTR) by using new cold start features. Here "cold-start" refers to the retailers, not the brand.

Task 1: Review material on CatNav
Task 2: Review the feature store
Task 3: Review querting offline features store tables
Task 4: Update the real-time ranking code in the backend so that newly defined features are fetched for CatNav ranking.
Task 5: Review the experimentation guide, prepare and launch an experiment

## Notes on metrics
Here are some of the metrics used so far
- AUC
- NDGC: Non-discounted cumulative gain, described [here](https://towardsdatascience.com/demystifying-ndcg-bee3be58cfe0). Basic motiviation is that errors at the top of a list of a ranking are weighted higher.

## Task 1

There are suggested features to look at already (note that these are features that should be applicable to new retailers.

Place we can look for features currently used in production are in the API:
* https://www.faire.com/api/ranking/info
   * Note that you have to be logged into faire.com in order for this link to work
In this particular case, the name of the models used for ranking are pretty straightforward
- catnav_v15_same_product
- catnav_v15_same_brand
If we needed to find this information, a useful trick is to look for the experiment page in notion, and then look for the experimental setting. In this case, we would find `CATNAV_RANKING_V1_5_LABEL_ENGINEERING_230226`. Searching for this string in the `backend` repo, we can find the allocation plan by searching for the experiment setting string. The names of the allocation variables align with the sections in the output of the API.

Questions:
- What is the aovScore? Seems like it would be average order value/volume. Why would we remove it?
  (context: comment sayts we do not plan to update/remove the aov component due to limited resources)
  - aov is `Average Order Volume` (see [glossary](https://www.notion.so/faire/13caca4a7a94443588b2fa79cee54965?v=df1d12419a744e22b46332db9ee9f84c&pvs=4))
  - Preliminary answer is that we are training vs a conversion model, and the AOV component may also be affected. We don't want to eliminate the aov component, but we are not explicitly updating it and are just assuming it is unaffected.
- What is F30D ("Train models with sample wieght to make it F30D brands-friendly)?
  - F30D means first 30 days, could be products or brands or both

- How do I find features that are cold start friendly for retailers?
   - Look at prod.retailer or canonical.retailer for what features are available real-time
   - Am having trouble accessing `prod.retailer`, but I have found `canonical.retailer` on Snowflake, which (from my understanding) is a daily(?) ETLed snopshot of the real time prod retailer, with minimal inference or transformation.
- (related to previous question)
  In the previous part, we learned about the features that were available for a cold start retailer. In the [project document](https://www.notion.so/faire/Category-Page-Ranking-with-Cold-Start-Features-9b128827abe64829802d96b8a0e7e73a?pvs=4) the suggested features are geographical and store type based. I noted that in the `canonical.retailer` table we only have `predicted_store_type` as a field. In Max's store page, we have an explict store type (and it seems like a reasonable thing to ask about in onboarding). *Why doesn't canonical have store_type as a field?*
  - Presumably the `predicted_store_type` comes from features like IS_APPAREL and there is little/no additional value to reusing those fields
- `business_type` exists as a feature, but is very messy. Just looking at US data, there are 6.7k unique values, the top five being
  1. Brick & Mortar Store (45.8k)
  2. Pop up Store (40.9k)
  3. Pop-up Store (16.9k)
  4. Online Only (12.9k)
  5. (1.1k)
  Note that this is in an aggregation (not individual retailers). You would need to reweigh the rows.
- The plot thickens when looking at `Max's fake store`. We see that on the admin page (impersonate `r_3ha7oytk`) is listed as "Home Decor Store" but when looking at `analytics.canonical.retailers` we have `predicted_store_type` as "home".

- State is blank for 75% of the US retailers (also have issues with retailers in multiple states)
  - We have 0 instances of a city without a state
  - We have about 54% of our retailers with no state, no city, and no zip code
    - thus 46% of retailers have at least one of state, city, and zip code
    - knowing any of these is enough to get state, so we can recover the state for 46% of retailers in the US


### Store type

Document on store type: https://www.notion.so/faire/Retailer-Store-Type-cc7c12a55a0440128bc956b452cdf3c3

Bulleted notes from the doc
- Store type data is contained in `service_knowledge_graph.retailer_store_types`.
- Has generally been unowned and seen little development. Main POC now is Tim Lew
- In the Faire Signup flow, there are 13 store types + other that retailers can choose from. There are also 51 curated store types (not on the quiz).
- The Faire Style Quiz store type are stored in `ANALYTICS.ETL_DATASCIENCE.RETAILER_STORE_TYPE_COALESCED:STYLE_QUIZ_STORE_TYPE`
- Since Fall 2019 it has been mandatory to choose a store type when filling out a new retailer.
- We have the `predicted_store_type` that maps to 43 categories.
- There is a google store type ontology as well, with 4500 categories. There is a hand-crafted mapping from the google taxonomy to the 43 curated categories, so we can use the google ontology if the curated one is missing, but map into the same 43 spaces.
- Search v2.8 Features importance are here: https://docs.google.com/spreadsheets/d/1uRKF2XYNgL-8nEuDyiRoojOQ99C8LvVaO87YD29KPyE/edit#gid=811932973


Note that the curated (43 categories) are the `predicted_store_type` in `canonical.retailers`, see the comment here:
> For now, KG recommends continuing to use curated store type for analyses for the sake of continuity (note, this is the predicted_store_type field in canonical.retailers ). Given the aforementioned shortcomings in curated store type, KG does have the following recommendations for anyone using curated store type...



### Cold start embeddings

### Meeting with Wei Jiang

Ask about Cat v15 experiment
Ask about the NDCG metric on the experiment page
Ask about imputed features for retailers
Ask about CatNav in general

This [blog post](https://craft.faire.com/real-time-ranking-at-faire-part-2-the-feature-store-3f1013d3fe5d) on the feature store is really nice. 
However, this paragraph puzzled me:

> To train a ML ranking model in our real-time feature ecosystem, instead of backfilling features for arbitrary training data, we use a log-and-wait approach. This means that we log all precomputed and real-time computed feature values for ranked entities each time they’re served.

It make sense to me when we are trying to retrain the weights of a model on the same set of features, because we would already have the appropriate real-time features at the time of inference. It is less clear to me how this approach works when introducing new real-time features. My guess is that we would either need to backfill, we could reliably access the pieces needed for the computation, or have a data collection period prior to the experiment.  Check this assumption.

### Feature store information
https://www.notion.so/faire/Features-101-21a2c34cae944c5b9f48c67256e384b6?pvs=4

above is a document about the mechanics of adding a new feature to the feature store.
- `machine_learning` schema contains the offline features
- `kinesis` schema contains the online features

There was a nice example query in the document. The problem was that we want to determine the credit limit we want to give a new reatiler upon signup. Because it is a new retailer, we can only look at features from the online store. This query gives the results
```sql
select
  parent_token,
  feature_name,
  feature_value
from
  kinesis.rule_results
where
  review_queue = 'RETAILER_SIGN_UP_REVIEW_QUEUE' and
  run_id = 'online'
```

### Current goal

- Add a single family of features:
  - fraction of brand ordered in country on in last N days, N in 7,30,60,90.
  - question: using these because of uniformity, although  would prefer to pick multiples of 7. Is there freedom here?
  - Need to create features in feature store and backfill
  - Add to the catnav v15 script that I get from Wei
- Can look for brand features from the offline feature store with this query:
```sql
select *
from feature_store.feature_registry
where ds = dateadd(day, -1, current_date()) and available_online=TRUE and feature_name LIKE 'brand_%' 
```
- can look at `count_ordering_retailers_trailing_7days` as an example of how to get order information (will need to group by country as well). From the table above, information can be found in `sql/brand_features/brand_num_ordering_retailers.sql`
- call the above path `S`. We are looking in `datascience/airflow/dags/feature_store/features/` + `S`

Breaking down a little big more
- https://github.com/Faire/datascience/blob/master/airflow/dags/feature_store/features/sql/brand_features/brand_geo_features.sql contains code for getting brand order addresses, and getting the country from those.
- need to make a decision about what to do if there are no brands in the country of the retailer (is this even a thing?)

## Tactical informationkk a

## Task 3

## Task 4

## Task 5
