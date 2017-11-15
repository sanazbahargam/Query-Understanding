# Query-Understanding

## Language identification:
Applying Monte Carlo Techniques to Language Identification paper a wide variety of language identification algorithms perform poorly for inputs of fewer than 50 characters (query size usually less than 50).
We can use query features, document features, and the searcher’s country in ML model. 
## Character Filtering:
  * Unicode Normalization
  * Removing Accents
  * Ignoring Capitalization
## Tokenization:
  * Punctuation
  * Hyphens and Apostrophes
  * In chinese/japanese, it’s necessary to employ a word segmentation algorithm specific to that language
## Spelling Correction:
## Stemming and Lemmatization
## Query Rewriting (to increase both precision and recall):
1. Query expansion (increases recall) by expanding the query with its
  * Abbreviations
Using a Dictionary	
supervised machine learning, we train a model using examples of abbreviations in context (e.g., the sequence of surrounding words). How to get training data:
Manually (expensive)
automatically identify potential abbreviations using patterns (parentheses, first letter of word and abbreviation). Pattern matching won’t catch all abbreviations, and it will also encounter false positives.
unsupervised machine learning using both surface similarity (matching first letter, etc) and  semantic similarity (e.g. word2vec)
  * Synonyms
Similar to abbreviations, we can use dictionary, supervised and unsupervised learning. The difference is we can’t use surface similarity (like abbreviations) and we can only rely on semantic similarity; so we can encounter false positives. So if we’re using Word2vec, we can require minimum cosine similarity.
  * Stemming 
  * spelling correction
  * Scoring function: Query expansion uses query rewriting to increase the number of search results. Now we need to design the scoring function to rank results that match because of query expansion. 
We can treat them equally (ok for abbreviations not for synonyms). 
We can apply a discount to matches from query expansion (by a constant, etc) or reflect the expected change in meaning (e.g. a function of cosine similarity).
2. Query relaxation (increases recall) opposite of query expansion. We can replace the query with a simpler form (removing adjectives, etc).
  * stop words
  * Specificity
  * syntactic analysis
  * semantic analysis
3. Query Segmentation:
4. Query scoping (increase precision): Query scoping is a powerful technique to increase precision by leveraging the explicit structure of the corpus and the implicit structure of queries
  * Query Tagging, query tagging is a special case of named-entity recognition (NER)

