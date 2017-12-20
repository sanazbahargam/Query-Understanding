# Query-Understanding

## Language identification:
Statistical approaches to language identification take advantage of distributional characteristics of the text they analyze.
In the paper, "Applying Monte Carlo Techniques to Language Identification", authors show that a wide variety of language identification algorithms perform poorly for inputs of fewer than 50 characters (query size is usually less than 50).
We can use query features, document features, and the searcher’s country in an ML model. 
Papers:
- A Survey of Language Identification Techniques
and Applications
## Character Filtering:
  * Unicode Normalization
  * Removing Accents
  * Ignoring Capitalization
## Tokenization:
  * White space characters
  * Punctuation
  * Hyphens and Apostrophes
  * In chinese/japanese, it’s necessary to employ a word segmentation algorithm specific to that language.
## Spelling Correction:
  * Fraction of misspelled search queries ~ 10% to 15%
  * [How to Write a Spelling Corrector](http://norvig.com/spell-correct.html)
  * Offline:
    * Indexing Tokens (mapping substrings of tokens (character n-grams) to tokens)
    * Building a language model (that tells us a priori probability of a query)
    * Building an Error Model (that tells us the probability of a query string given the intended query, in antoher word  estimates the probability of a particular misspelling, given an intended query.)
  * At query time:
    * Candidate generation (e.g. retrieval all tokens within an edit distance of 1 of the misspelled query)
    * Scoring (Prob (candidate | query) ∝ Prob (query | candidate) * Prob (candidate))
    * Presenting suggestions
## Stemming and Lemmatization
  * To increase recall
## Query Rewriting (to increase both precision and recall):
1. Query expansion (increases recall) by expanding the query with its
    * Abbreviations
Using a Dictionary or using a supervised machine learning. In case of using a mahcine learning approach, we train a model using examples of abbreviations in context (e.g., the sequence of surrounding words). How to get training data:
      * Manually (expensive)
      * Automatically identify potential abbreviations using patterns (parentheses, first letter of word and abbreviation). Pattern matching won’t catch all abbreviations, and it will also encounter false positives.
      * Unsupervised machine learning using both surface similarity (matching first letter, etc) and  semantic similarity (e.g. word2vec)
    * Synonyms
Similar to abbreviations, we can use dictionary, supervised and unsupervised learning. The difference is we can’t use surface similarity (like abbreviations) and we can only rely on semantic similarity; so we can encounter false positives. So if we’re using Word2vec, we can require minimum cosine similarity.
    * Stemming 
    * spelling correction
    * Scoring function: 
     Query expansion uses query rewriting to increase the number of search results. Now we need to design the scoring function to rank results that match because of query expansion. 
      * We can treat them equally (ok for abbreviations not for synonyms). 
      * We can apply a discount to matches from query expansion (by a constant, etc) or reflect the expected change in meaning (e.g. a function of cosine similarity).
2. Query relaxation (increases recall) opposite of query expansion. We can replace the query with a simpler form (removing adjectives, etc).
    * stop words
    * Specificity
      * Inverse Document Frequency: (should take care of edge cases like proper names or misspelled words)
      * Lexical Databases (knowledge graph e.g. [WordNet](https://en.wikipedia.org/wiki/WordNet): useful for comparing tokens with a hierarchical relationship
    * syntactic analysis: To determine which tokens are optional e.g. using part-of-speech tagger to preserves the head noun and removes one or more of its modifiers. Cute fluffly cat -> fluffy cat
    * semantic analysis: We can use the Word2vec model to embed words and phrases into a vector space that captures their semantics. This embedding allows us to recognize how much the query tokens overlap with one another in meaning, which in turn helps us estimate the consequence of ignoring a token.  For example "polo shirt" -> "polo" is  but "dress shirt" -> "shirt" completely changes the query.
3. Query Segmentation (increase precision): divides the search query into a sequence of semantic units, e.g. “machine learning” framework. Then we can auto-phrase segments or couple auto-phrasing with query expansion approaches like stemming, lemmatization, and synonym expansion. Finally, if we’re using query relaxation, it’s important for relaxation to respect query segmentation by not breaking up segments. We can do Query Segmentation by using:
    *  Dictionary Approach
    * Statistical Approach:  create a dictionary from a document corpus. We analyze the corpus to find collocations and kepp the sequences that co-occur more than expected. 
    * Supervised Machine Learning: a binary classification problem at the token level to decide whether it continues the current segment or begins a new one (represent the examples as feature vectors including token frequencies, mutual information for bigrams, part-of-speech tags, etc.)
4. Query scoping (increase precision): Query scoping is a powerful technique to increase precision by leveraging the explicit structure of the corpus and the implicit structure of queries. Query scoping often relies on query segmentation. We determine an entity type for each query segment, and then restrict matches based on an association between entity types and document fields. e.g. black (color) michael kors (brand) dress (category)
    * Query Tagging, query tagging is a special case of named-entity recognition (NER)
5. NER (at high level distinguish between named entities and terms (no name or type) and also classify named entities in text into pre-defined categories such as the names of persons, organizations, locations, etc)
    * Rule-based recognizers (low accuracy)
    * Machine-learned, mainly LSTM (requires a collection of segmented, labeled queries) 
6. Taxonomies and Ontologies: We can implement query understanding by mapping queries to taxonomies or ontologies. The simplest approach is to treat taxonomy nodes as categories and train a classifier to map queries to categories, e.g., mapping the query knapsack to the node Bags and Purses -> Backpacks.
    * A taxonomy is a hierarchical classification system: a tree that starts from a universal root concept and progressively divides it into more specific child concepts. 
    * While taxonomies represent a collection of topics with “is-a” relationships, ontologies make it possible to express a much richer collection of objects and relationships, such as “has-a” and “use-a”. 
8. Autocomplete
    * Autocomplete predicts complete search queries from partial ones. It’s a way to guide searchers to successful search experiences. We can model this process using conditional probability. 
    * We can use historical query logs to compute the probability of a query based on how frequently it appears in the log. 
    * Drawbacks:
      * However query frequency in the logs doesn’t take into account recency, seasonality, or other time-dependent factors. 
      * Also, non-temporal factors: we may want to build a regression model using features like location, gender, session context, etc
      * The partial query may not be a prefix, for example, we shouldn’t assume Pr(mens pants|pa)=0 just because pa isn’t a prefix of mens pants
      * The partial query may be misspelled, so it’s necessary to combine autocomplete with spelling correction
      * You may want to exclude autocomplete suggestions from the logs when computing query probabilities, in order to avoid a positive feedback loop.
9. Instant Search
Instant search goes a step beyond autocomplete: instead of suggesting search queries, it shows searchers actual search results     * as they type.

***

# Contextual Query Understanding
1. Session Context
    * Recognizing Query Refinement
      * When a searcher performs two searches in a row, it’s often the case that the second search is an attempt to refine the first.
      * Narrowing: e.g., iphone games -> free iphone games. (improves precision)
      * Broadening: e.g., iphone kids games -> iphone games (improves recall)
      * Lateral refinement, e.g., iphone games -> android games.
    * Personalization using Session Context: if searching for shirts, pants, socks in the same seesion, search engine should preserve attributes like gender and size. 
2. Location as Context

    * Location as an Implicit Part of the Query, the search engine has to determine which queries have local intent (application like Google or Apple Maps, or business directory like Yelp, then nearly all queries carry local intent)
      * E.g. search for restaurants
    * Location as an Intent Signal	
      * Sometime location isn’t part of the query intent, but it’s still useful as a signal. For example, football means something different in the United States than it does in the rest of the English-speaking world. People searching for jackets are likely to have preferences that reflect local climate.
    * To locate a document:
      * Using entity recognition to find locations in the form of addresses or location names
      * Using the locations of searchers engaging with a document to locate that document (There are various ways to geolocate searchers, the simplest being to use their IP addresses).
	
3. Seasonality
    * Whether it’s the time of year or the time of day, the time when someone performs a search sometimes help us determine the searcher’s intent.  Certain queries are more frequent at particular times of year, Sometimes, it’s not the query probability but the intent that is time-sensitive (searching for jacket).  When queries exhibit seasonality, temporal context helps us better estimate their probability — which is particularly useful for autocomplete. 
    * How do we determine that a query is seasonal? For frequent queries, we can measure the variance of when queries occur over time and compare it to that of queries performed throughout the year. The variance of a random variable with uniform distribution between the real values a and b is 1/12 (b-a)².  In general, the variance correlates negatively to seasonality.
