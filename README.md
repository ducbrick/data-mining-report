***Finding similar items***

# Introduction
## What is "finding similar items"
[See](intro-what-is-finding-similar.md)

## Specific/real-world applications
### Recommendation system
+ Popularly used in many domains
+ "Users who viewed this items also viewed"
+ "Similar items"
+ Directly retrieves recommendations similar to user's preferred items
    + Visually similar items
    + Name, description, metadata
    + Songs: genre, artist, tempo, key, time signature, phrases, etc.
    + Movies: topic, genre, actors, directors, viewers, etc.
    + Other extracted data
+ Indirectly retrieves recommendations through other similar users
    + Personal informations: age, gender, location, preferences, demographic, etc.
    + User interaction: Visited items, purchased items, clicks, etc.

### Plagiarism/duplicate detection
+ Determine the level of similarity between a document and an existing one
+ Textual similarity
    + Lexical similarity: simple exact/near exact text matching
    + Structure/syntactic similarity: structure/ordering of words, sentences or paragraphs
    + Sematic similarity: meaning of words and sentences

### Reverse image search
+ Find photos/images similar to input one
+ Remove noises
+ Measure similarity by color, shapes, layout, objects, etc

### Financial fraud detection
+ Determine if transactions, accounts, behaviors, patterns are similar to known fraudulent ones

# Data representation
real-world data are almost never of primitive types (simple numeric, categorical, etc) => Need to represent data in a format so that similarity measurement & searching techniques can be applied

## Final representations/formats
The representations/formats of the output data after running input data through transformations. Similarity search algorithms can then be applied on these output data.

### Vector embeddings
+ Numeric vector/array, eg,. `(0.12, -0.37, 0.84, 0.09, ...)`
+ Most common
+ Obtained through a function `f(input) -> output in R^d`, where:
    + `f`: embedding model
    + `d`: embedding dimension of the output vectors
+ Applicable pairwise similarity mesures: *Euclidean*, *Manhattan*, or [*Lp-norm*](en.wikipedia.org/wiki/Lp_space) in general, also *Cosine* and *dot product*
+ More specific representations:
    + Dense vector, low-dimensional
    + Sparse vector, high-dimentional (for models like *TF-IDF* and *Bag of words*)
    + Node-level and graph-level embedding (when input data is structured like a graph)
+ Real-world application: vector databases store data as vector embeddings for fast nearest neighbors lookup

### Probability and statistic
+ Model uncertainty/variation instead of representing features of an entry as a vector embedding
+ Example: *LDA* (*Latent-Dirichlet Allocation*):
    + Describe each document as a probability distribution over topics (eg,. 60% sport, 30% technology, 10% health)
    + Similarity is then measured using metrics like *Kullback–Leibler divergence* or *Jensen–Shannon distance*

### Hashing/signature
+ Compact hash code
+ Example application: *LSH*
+ Useful in very large-scale dataset, where representing entries as vector embeddings is unperformant

### Token sequence
+ Ordered list of *token* (small meaningful unit of data in a data entry, eg,. word, subword in text, identifier, symbol, operator in programming language, DNA symbol, event)
+ Specific representations include *k-shingles*
+ Obtained thru *tokenization*
+ Captures order and context
+ Often converted into vector embeddings/hash signatures as the final format, but some measurements can be applied directly:
    + *Edit distance*: number of changes to make 2 tokens become identical
    + *Jaccard similarity*: ratio of overlapping tokens between 2 sequences
    + *LCS*: Longest common subsequence

## Transforming different data types

# What is "similarity", how are items considered similar
[See](similarity-measures.md)

# Similarity seach techniques

## Brute force

## KD (k-dimensional) tree

## Ball tree

## Locality-sensitive hashing

## Hierarchical Navigable Small World Graph

## Product quantization
