<!--toc:start-->
- [Introduction](#introduction)
  - [What is "finding similar items"](#what-is-finding-similar-items)
  - [Specific/real-world applications](#specificreal-world-applications)
    - [Recommendation system](#recommendation-system)
    - [Plagiarism/duplicate detection](#plagiarismduplicate-detection)
    - [Reverse image search](#reverse-image-search)
    - [Financial fraud detection](#financial-fraud-detection)
- [Data representation](#data-representation)
  - [Final representations/formats](#final-representationsformats)
    - [Vector embeddings](#vector-embeddings)
    - [Probability and statistic](#probability-and-statistic)
    - [Hashing/signature](#hashingsignature)
    - [Token sequence](#token-sequence)
  - [Transforming different data types](#transforming-different-data-types)
    - [Numeric data](#numeric-data)
    - [Text data (N gram is bad just use embeddings)](#text-data-n-gram-is-bad-just-use-embeddings)
    - [Time-series data](#time-series-data)
    - [Image data](#image-data)
    - [Graph data](#graph-data)
- [What is "similarity", how are items considered similar](#what-is-similarity-how-are-items-considered-similar)
- [Similarity search techniques](#similarity-search-techniques)
  - [Brute force](#brute-force)
  - [KD (k-dimensional) tree](#kd-k-dimensional-tree)
  - [Ball tree](#ball-tree)
  - [Locality-sensitive hashing](#locality-sensitive-hashing)
    - [Tổng quan về Locality-sensitive families:](#tổng-quan-về-locality-sensitive-families)
    - [Tổng quan về Locality-sensitive hashing:](#tổng-quan-về-locality-sensitive-hashing)
    - [Locality-sensitive hashing cho minhash signatures:](#locality-sensitive-hashing-cho-minhash-signatures)
      - [Kĩ thuật phân dải (banding technique):](#kĩ-thuật-phân-dải-banding-technique)
  - [K-NNG](#k-nng)
    - [NN-Descent](#nn-descent)
    - [K-NN search on a K-NNG](#k-nn-search-on-a-k-nng)
    - [Insertion](#insertion)
      - [Local insertion](#local-insertion)
      - [Periodical rebuild](#periodical-rebuild)
    - [Deletion](#deletion)
      - [Local deletion](#local-deletion)
    - [Summary](#summary)
  - [Small-World Graph](#small-world-graph)
  - [Hierarchical Navigable Small World Graph](#hierarchical-navigable-small-world-graph)
  - [Product quantization](#product-quantization)
<!--toc:end-->

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
Different data types, for different similarity measurement, require different transformations
### Numeric data
Numeric data can be used directly for similarity measurements. However transformations are still needed:
1. Normalization
### Text data (N gram is bad just use embeddings)
1. Bag of Words
   + Each document is represented as a **vector** of size V, where V equals the size of the vocabulary (the number of unique words from all documents)
   + Each dimension is the count (or weight, like TF-IDF) of the corresponding word
2. K-shingles
   + a k-shingles is a substring of length k that appears in a text.
   + by breaking a document into unique k-shingles, you get a set that represent the document.
		- example here
   + Shingles can be hashed to reduce size. However, the space needed is still large. We want to replace sets with smaller representations called "signatures"
3. Minhashing
   + Characteristic matrix: each column is a set, the rows are the elements
   +  To minhash a set from the matrix: pick a permutations of the rows. The minhash value of the set is the row number of the first row where the column has a 1
   +  Now, to construct a **minhash signature** for a set S, we use many (n) permutations of the rows: $h_1, h_2,..., h_n$. the vector $\[h_1(S), ..., h_n(S)]$  is the signature of the set S.
   +  Unfortunately, just permutating a large characteristic matrix explicitly is already time-consuming
       + How to compute minhash signatures then?
4. Word embeddings / Document embeddings
   + Represent words by small, dense vector. The idea is that similar words are near each other in high-dimensional space
   + Technique: Word2Vec: Skip-gram, CBOW
     - Explain Skip-gram here? (sir this is not a NLP class) 
   + We want to represent whole documents, not just words. Doc2Vec is an extension of Word2Vec that create document vector embeddings
5. LDA: Latent topic model
   +

### Time-series data
Audio data is time-series data btw
1. SAX
2. Discrete Wavelet Transform
   + We consider the use of wavelet transformations as a dimensionality reduction technique to permit efficient similarity search over high-dimensional time-series data
   + [Too long will read later](https://infolab.usc.edu/csci599/Fall2003/Time%20Series/Similarity%20search%20over%20time-series%20data%20using%20wavelets.pdf)
3. Discrete Fourier Transform
### Image data
1. CNN
   + More embeddings :(
### Graph data



# What is "similarity", how are items considered similar
[See](similarity-measures.md)

# Similarity search techniques

## Brute force

## KD (k-dimensional) tree
[See](https://hackmd.io/@lto5/BkIeellAel)

## Ball tree
[Ball-tree](ball-tree.md)

## Locality-sensitive hashing
### Tổng quan về Locality-sensitive families:
Một họ hàm băm nhạy cục bộ (Locality-sensitive families) cần phải có đủ 3 tiêu chí sau để có thể hiệu quả trong việc phân biệt rõ ràng các cặp phần tử có độ tương đồng cao và thấp khác nhau:  
1. Tính nhạy với sự tương đồng: đây là thuộc tính cốt lõi của hàm băm để phân biệt được các cặp tương đồng hay không tương đồng:
   - Nếu 2 phần tử có độ tương đồng cao, hàm băm phải có xác suất cao tạo ra cùng một kết quả.
   - Nếu 2 phần tử có độ tương đồng thấp, hàm băm phải có xác suất thấp tạo ra cùng một kết quả.
2. Tính độc lập về mặt thống kê: tức là kết qủa các hàm băm trong cùng một họ hàm không ảnh hưởng đến nhau. Điều này cho phép ta sủ dụng quy tắc nhân xác suất để có thể ước lượng xác suất cho kết quả của hai hay nhiều hàm băm cùng họ.
3. Tính hiệu quả, theo hai cách:
   - Họ hàm băm phải có thể xác định được các cặp ứng viên trong thời gian nhanh hơn nhiều so với việc so sánh tất cả các cặp trong O(n<sup>2</sup>). Ví dụ: Kĩ thuật minhash đáp ứng được tiêu chí này vì việc tính toán minhash cho tất cả phần tử chỉ mất độ phức tạp tuyến tính O(n), sau đó những tập có gái trị tương đồng sẽ được gom lại trong một bucket. Từ đây việc kiểm tra chỉ cần thực hiện trong các bucket này thay vì cả tập hợp ban đầu.
   - Họ hàm băm phải có khả năng kết hợp để cải thiện hiệu suất: một hàm băm có thể chưa phân loại chính xác nên ta có thể kết hợp nhiều hàm băm lại với nhau để tạo ra những hàm tốt hơn, phòng tránh các trường hợp dương tính giả hay âm tính giả (những cặp không giống nhau được gom chung vào một bucket và ngược lại).

### Tổng quan về Locality-sensitive hashing:
Đây là một kĩ thuật để giúp ta tìm được những phần tử có độ tương đồng cao nhanh chóng và hiệu quả, bằng cách sử dụng các hàm băm nhạy cục bộ (locality-sensitive functions) để băm các phần tử nhiều lần vào những bucket, sao cho những phần tử có độ tương đồng cao sẽ có xác suất cao được băm vào cùng một bucket và những phần tử có độ tương đồng thấp thì sẽ có xác suất cao được băm vào những bucket khác nhau. Bằng cách này, ta có thể giảm đáng kể khối lượng tính toán khi chỉ cần so sánh những phần tử trong cùng một bucket với nhau thay vì phải so sánh tất cả các cặp phần tử.

### Locality-sensitive hashing cho minhash signatures:
#### Kĩ thuật phân dải (banding technique):
1. Tổng quan: nếu ta đã thực hiện minhashing để có được ma trận chữ kí (signature matrix), một cách hiệu quả để thực hiện phép băm là sử dụng kĩ thuật phân dải (banding). Giả sử ma trận chữ kí có n hàng, ta chia n hàng này thành b dải, mỗi dải gồm r hàng. Với mỗi dải, ta sử dụng một hàm băm để băm các vector cột trong dải đó vào nhiều bucket khác nhau. Chúng ta có thể sử dụng một hàm băm cho tất cả các dải, nhưng ta sẽ dùng bộ bucket riêng cho mỗi dải, để những vector cột giống nhau ở các dải khác nhau không bị gom chung một bucket. Sau đó những cặp phần tử được băm vào cùng một bucket ở mỗi dải sẽ trở thành những cặp ứng viên để kiểm tra chính xác độ tương đồng.
2. Phân tích:
- Giả sử một cặp phần tử có độ tương đồng Jaccard là s, người ta chứng minh được rằng xác suất minhash signatures của chúng trùng khớp nhau trong một hàng bất kì cũng là s. Ta sẽ tính toán xác suất những phần tử này trở thành cặp ứng viên:
   + Xác suất signature của chúng trùng khớp trong mọi hàng của một dải: s<sup>r</sup>
   + Xác suất signature của chúng không trùng khớp trong ít nhất một hàng của một dải: 1 - s<sup>r</sup>
   + Xác suất signature của chúng không trùng khớp trong ít nhất một hàng của mỗi dải: (1 - s<sup>r</sup>)^<sup>b</sup>
   + Xác suất signature của chúng trùng khớp trong mọi hàng của ít nhất một dải và do đó trở thành cặp ứng viên: 1 - (1 - s<sup>r</sup>)^<sup>b</sup>  
- Khi ta vẽ đồ thị của công thức trên bất kể b, r thế nào và s chạy từ 0 đến 1, ta nhận được một đường cong hình chữ S rất đặc trưng với 3 phần:
   + Phần bên trái thấp, gấn 0.
   + Phần giữa tăng vọt đột ngột.
   + Phần bên phải cao, gần 1.
- Phần dốc ở giữa giúp ta lọc dữ liệu đúng như mong muốn. Điểm mà đường cong bắt đầu dốc lên xấp xỉ bằng (1/b)<sup>(1/r)</sup>. Bằng cách điều chỉnh b và r, ta có thể di chuyển ngưỡng này cho phù hợp với bài toán. 

## K-NNG
+ Mentioned/introduced in:
    + [Relative neighborhood graphs and their relatives](Relative_neighborhood_graphs_and_their_relatives.pdf)
    + [The relative neighborhood graph of a finite planar set](RNG.pdf)
    + [Fast Approximate Nearest-Neighbor Search with k-Nearest Neighbor Graph](Fast_Approximate_Nearest-Neighbor_Search_with_k-Ne.pdf)
+ A graph where every node `u` has edges to `k` other nodes that is closest to it
+ Many (approximate) similarity search algos can be performed on an already constructed K-NNG with better time complexity than brute-force
+ The problem then becomes an efficient construction algorithm
+ Brute force: `O(n^2 * d)` where
    + `n` is the number of nodes
    + The function to measure distance between 2 nodes `d(u, v)` has time complexity of `O(d)`
```
for every node u                                 <- O(n)
    for every other node v                       <- O(n)
        compute d(u, v)                          <- O(d)
    find v-k which is the k-th nearest node to u <- O(n) average (quickselect)
    find every node v closer to u than v-k       <- O(n)
    add them to u's neighbor list                <- O(k)
```
### NN-Descent
+ Introduced in [Efficient K-Nearest Neighbor Graph Construction for Generic Similarity Measures](nndescent.pdf)
+ An algorithm to efficiently construct a approximate *K-NNG (K-Nearest Neighbors Graph)*
+ Sacrifice accuracy in favor of speed
+ Based on the assumption: A neighbor of a neighbor is likely also a direct neighbor
+ Assumption -> Heuristic, only approximate result, not exact
+ The basis:
    + For every node `u`, randomly picks `k` other nodes as its nearest neighbors
    + Run many iterations, in each, for every node `u`, computes distance between `u` and its neighbors' neighbors, improve its neighbor list accordingly
    + Stop iterating once no further notable improvements can be made
```
for every node u
    randomly or heuristically pick k other nodes

loop
    for every node u
        for every v neighboring u
            for every p neighboring v other than u
                compute d(u, p)
        find k closest nodes to u, update u's neighbor list

    if number of updates is less than threshold
        stop loop
```
+ Time complexity: `O(m * n * k^2 * d)`
    + `m` is the number of iterations
    + Worst case: same as brute-force
    + In practice: `m * k <<<< n` -> very efficient, authors claim `O(n^1.14 * d)` found on empirical dataset
+ Advantages:
    + General: works with any arbitary distance function `d(u, v)`
    + Scalable: typically has acceptable time complexity
    + Space efficient: constructs the K-NNG once and improves it in-place, minimal extra data are maintained
    + Accurate: High accuracy (compared to brute-force) for real dataset, authors claim `>90%` and out-perform *Recursive Lanczos Partitioning* and *LSH*
    + Easy to implement
### K-NN search on a K-NNG
+ Input: 
    + `q`: the query node, the distance function `d()` should be able to be used on `q`
    + `k`: the number of nodes to search for (***NOTE:*** this `k` is different from the parameter `k_graph` used to construct the K-NNG)
+ Core idea: approximately search for the result by searching for node(s) closest to `q` and picking their neighbors
    + Search for `U`: the list of nodes closest to `q` (`|U|` may be tweaked to favor either accuracy or speed)
    + For each node `u` in `U`, consider `u` and its neighbors as result candidates
    + Select `k` best candidates previously picked
    + Time complexity: `O(k + |U| * k_graph)`
+ Brute-force: `O(n * d)` where `n` is the number of nodes in the K-NNG
+ Heuristic/meta-heuristic algorithms can be applied, example:
    + Randomly picks a number of starting nodes, mark them as *discovered*
    + Pick a discovered unvisited node `u` and visit it
    + For every `v` neighboring `u`, compute `d(q, v)` and mark it as discovered
    + Repeat until maximum number of iterations has been reached, or the best discovered node hasn't been updated recently, or other conditions
    + Pick `k` best discovered nodes as result
    + Time complexity: `O(m * k_graph * d)` where
        + `m`: the number of iteration
        + `k_graph`: the number of neighbor each node has
        + Worst case: `O(n * d)`
        + In practice: much more time efficient
```
randomly pick starting_nodes

for every u in starting_nodes
    compute d(q, u)
    add u to min_heap and explored_nodes

loop
    u = min_heap.pop
    update best_discovered_node

    for every v neighboring u
        if v not in explored_nodes
            compute d(q, v)
            add v to min_heap and explored_nodes 

    if stopping conditions met
        stop loop

pick k best nodes from explored_nodes
```

### Insertion
+ K-NNG doesn't naturally support efficient incremental updates and is instead better fitted for processing batch query
+ There are a few approaches to handle this problem
#### Local insertion
+ Suppose a new node `u` needs to be inserted into an existing K-NNG
+ `u`'s neighbor list can be obtained by performing a KNN search for `u`, on the graph
+ After that, `u`'s neighbors' neighbor list may need to be updated
+ Pros:
    + Fast, no need to rebuild the entire graph
    + Decent enough for small scale or infrequent insertions
+ Cons:
    + Inaccuracy accumulates over time
    + Not as accurate as a full rebuild
#### Periodical rebuild
+ Expand upon the previous solution
+ After a number of insertions, perform a full rebuild of the entire graph to reduce inaccuracy
+ Pros: Ensure temporary inaccuracy is capped to a limit, and immediately after being rebuilt, the graph is as accurate as the construction algorithm allows it to be
+ Cons: Rebuilding is computationally intensive
### Deletion
+ Even more tricky than insertion
#### Local deletion
+ Same idea as local insertion
+ When removing node `u` from an existing K-NNG, update, adjust and optimize only its neighbors' neighbor list
+ Pros: Fast
+ Cons: Inaccuracy increases after every deletion
+ Possible solution: periodical rebuild
### Summary
+ Pros:
    + (Technically) Applicable for any distance function
    + Captures the local similarity structure of data
    + Fast and fairly accurate query
+ Cons:
    + Expensive to build
    + Not as effective when having to deal with dynamic updates
    + Construction algorithm and parameter `k` affects the quality of the graph and the balance between accuracy vs speed

## Small-World Graph

## Hierarchical Navigable Small World Graph

## Product quantization
