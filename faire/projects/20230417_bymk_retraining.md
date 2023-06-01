# Brands you may like retraining

Have some notes from Wei Jiang
https://www.notion.so/faire/Damien-Wei-7c9d29947d8c45e1ba99b05c95320c09?pvs=4

The notes that we have are 
- use `generate_dag.py` on the brands you may like in `/Users/damien/development/datascience/airflow/dags/search_personalization_ranking/ranking_model_trainer/include`
- then we have the following commands to push into the docker image
```
# this is the generation step above
cd ~/Development/datascience && python airflow/dags/search_personalization_ranking/ranking_model_trainer/include/generate-dag-files.py

# this is putting it into the docker image
cd apps/ranking_model_trainer/ && make login build push_dev && cd ../..
```

Looking for baseline brands-you-may-like

In the API search (reminder: https://www.faire.com/api/ranking/info ) we find the possible recommenders in production right now:
- brand-similar_brands_v1
- brand_new_arrivals_rtr_v2_relaunch
- brand_new_arrivals_rtr_v4

Surface is brand-page-similar-brands



We have the ranking model:
- ranking_model_byml_retrain_v5_trimmed_features_test.json

