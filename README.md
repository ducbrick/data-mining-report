***Finding similar items***

# Introduction
## What is "finding similar items"
General infos
## What is "similarity", how are items considered similar
List & explain more deeply some criteria/models of similarity measurement
## Why does it matter
General infos
It matters because many real world problem require finding similar items 😥  
Many higher-level algorithms like clustering or classification depend on accurate item similarity measurement. wtf do I even write here :( 

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
+ Represent image as *feature vector* (color, shapes, layout, objects, etc)

### Financial fraud detection
+ Determine if transactions, accounts, behaviors, patterns are similar to known fraudulent ones
+ Representation: feature vector/node embedding

# Techniques

