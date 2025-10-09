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

# What is "similarity", how are items considered similar
[See](similarity-measures.md)

# Similarity seach techniques


## 1. Locality-Sensitive Hashing - LSH for Documents
**LSH** là một kỹ thuật chung để tìm các cặp mục có độ tương đồng cao mà không cần phải kiểm tra tất cả các cặp có thể.
* **LSH cho Chữ ký Minhash:** Hình thức LSH được thiết kế cụ thể cho các tài liệu được biểu diễn bằng chữ ký Minhash.
* Kỹ thuật này thường sử dụng kỹ thuật **dải băng (banding technique)** để tạo ra các **cặp ứng viên (candidate pairs)**.

## 2. Lý thuyết về hàm nhạy vị trí (Theory of Locality-Sensitive Functions)

* Gồm các cách khuếch đại một họ hàm nhạy vị trí bằng cách sử dụng các phép xây dựng **AND** và **OR** để điều chỉnh xác suất tìm ra các cặp tương đồng.

## 3. LSH cho các thước đo khoảng cách khác (LSH Families for Other Distance Measures)
* LSH cho **Khoảng cách Hamming**.
* **Siêu phẳng Ngẫu nhiên (Random Hyperplanes)** và LSH cho **Khoảng cách Cosine**.
* LSH cho **Khoảng cách Euclidean**.

## 4. Các Phương pháp phát hiện mức độ tương đồng cao (Methods for High Degrees of Similarity)

Khi yêu cầu về mức độ tương đồng rất cao (gần 1):

* **Lọc dựa trên Độ dài (Length-Based Filtering):** So sánh các đối tượng có độ dài (số lượng phần tử) gần như nhau.
* **Lập chỉ mục Tiền tố (Prefix Indexing):** Sử dụng các ký hiệu ở đầu (tiền tố) của chuỗi để xây dựng chỉ mục và giảm số lượng so sánh cần thiết.
* Sử dụng thông tin **Vị trí** và **Độ dài Hậu tố (Suffix Length):** Cải tiến việc lập chỉ mục bằng cách xét cả vị trí của ký hiệu trong tiền tố và độ dài của phần còn lại của chuỗi.

