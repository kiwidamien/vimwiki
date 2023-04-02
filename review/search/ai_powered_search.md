:review:search:data_science:

# AI Powered Search (Manning MEAP)

## Part 1: Modern Search Relevance

* Recommenders and Search really are on a continuum. Typically we think of Search as driven by user input,
  and recommendations as being more passive, but even direct search takes a lot of context from the user
  to determine what is relevant.
  * Examples of context are the location of the user (e.g. searching for lunch spots, or job applications)
  * Other examples are previous searches
* Different types of recommenders:
	* __Content-based__
	  - Matches attributes (typically between users-items, users-users, or items-items)
	  - Learns which features matter the most matching between users and items e.g. matches experience or skills on a job site
	  - If a user likes a particular item, we can use item-item similarity to recommend other items
	* __Behavior-based__ (collaborative filtering)
	  - This is similar to the netflix recommendation problem/matrix factorization problem
	  - Suffers from cold start problem
* Search results:
	* Searching for "lunch" should use the context of their current location
	* Searching for "driver", the location (e.g. airport) could differentiate between uber drivers, golf drivers, printer drivers, etc
* While recommenders and search systems exist on a continuum, we see issues from Conway's law.
        * Recommenders tend to be staffed by data scientists with very little background in information retrieval
	* Search tends to be staffed by engineers with little recommendation experience
	* Both dichotomies make it difficult to produce truly personalized search.
* Extracting good named entities requires good domain knowledge
        * This means understanding synonyms, phrases, related terms
	* named entity recognition allows us to search for "things" instead of just "strings" (text)
* Search Engine Technology
        * The book uses Apache Solr, but many of the techniques used can also be ported over to ElasticSearch
	* The book also uses MILVUS as the embedding based language model, but with recurrent or transformer models this may also need
	  to be updated.

**Chapter 3**

Starts off with a lot of discussion motivating different weightings, term frequency, and tf-idf, which I did not find that insightful.
There was a good list of other elements we could consider to boost rankings which was worth considering:
* Geospatial boosting: results near the user should be boosted
* Date boosting: boost more recent results
* Popularity boosting
* Field boosting
* Category boosting
* Phrase boosting (i.e. N grams inspired)
* Semantic Expansion: Documents containing other words or concepts that are highly related to the query keywords and context should be boosted

earned that Solr has its own meta language. This query boosts 10x for matches in the title fields, and 2.5x for the description field:
```solr
q={!type=edismax qf="title^10 description^2.5"}the cat in the hat
```

	  
	  
