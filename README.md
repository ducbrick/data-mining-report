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
## 1. Độ tương đồng Jaccard của các Tập hợp (Jaccard Similarity of Sets)
* **Độ tương đồng Jaccard** giữa hai tập hợp $S$ và $T$ được định nghĩa là tỉ lệ giữa kích thước của phần giao và kích thước của phần hợp.

## 2. Kỹ thuật Shingling (Shingling of Documents)
* **K-Shingles:** Định nghĩa k-shingle là bất kỳ chuỗi con nào có độ dài k tìm thấy trong tài liệu.
* **Hashing Shingles:** Sử dụng hàm băm để ánh xạ các chuỗi dài k này thành các số bucket ngắn hơn, giúp nén dữ liệu và tạo điều kiện thao tác thuận lợi hơn.

## 3. Similarity-Preserving Summaries of Sets
* **Minhashing :** Kỹ thuật thay thế các tập hợp lớn bằng các biểu diễn nhỏ hơn nhiều gọi là **"chữ ký" (signatures)**, trong đó độ tương đồng Jaccard của các tập hợp ban đầu có thể được ước tính từ chữ ký của chúng.

## 4. Locality-Sensitive Hashing - LSH for Documents
**LSH** là một kỹ thuật chung để tìm các cặp mục có độ tương đồng cao mà không cần phải kiểm tra tất cả các cặp có thể.
* **LSH cho Chữ ký Minhash:** Hình thức LSH được thiết kế cụ thể cho các tài liệu được biểu diễn bằng chữ ký Minhash.
* Kỹ thuật này thường sử dụng kỹ thuật **dải băng (banding technique)** để tạo ra các **cặp ứng viên (candidate pairs)**.

## 5. Các Thước đo Khoảng cách (Distance Measures)
Các loại thước đo:

* **Khoảng cách Jaccard (Jaccard Distance):** Là 1 trừ đi độ tương đồng Jaccard.
* **Khoảng cách Euclidean (Euclidean Distances):** Thước đo khoảng cách thông thường trong không gian $n$ chiều (còn gọi là $L_2$-norm).
* **Khoảng cách Cosine (Cosine Distance):** Đo lường góc giữa hai vectơ.
* **Khoảng cách Edit (Edit Distance):** Khoảng cách giữa hai chuỗi dựa trên số lần chèn (insertions) và xóa (deletions) ký tự để chuyển đổi từ chuỗi này sang chuỗi kia.
* **Khoảng cách Hamming (Hamming Distance):** Khoảng cách giữa hai vectơ Boolean hoặc bitstrings có cùng độ dài, đếm số vị trí khác nhau.

## 6. Lý thuyết về hàm nhạy vị trí (Theory of Locality-Sensitive Functions)

* Gồm các cách khuếch đại một họ hàm nhạy vị trí bằng cách sử dụng các phép xây dựng **AND** và **OR** để điều chỉnh xác suất tìm ra các cặp tương đồng.

## 7. LSH cho các thước đo khoảng cách khác (LSH Families for Other Distance Measures)
* LSH cho **Khoảng cách Hamming**.
* **Siêu phẳng Ngẫu nhiên (Random Hyperplanes)** và LSH cho **Khoảng cách Cosine**.
* LSH cho **Khoảng cách Euclidean**.

## 8. Các Phương pháp phát hiện mức độ tương đồng cao (Methods for High Degrees of Similarity)

Khi yêu cầu về mức độ tương đồng rất cao (gần 1):

* **Lọc dựa trên Độ dài (Length-Based Filtering):** So sánh các đối tượng có độ dài (số lượng phần tử) gần như nhau.
* **Lập chỉ mục Tiền tố (Prefix Indexing):** Sử dụng các ký hiệu ở đầu (tiền tố) của chuỗi để xây dựng chỉ mục và giảm số lượng so sánh cần thiết.
* Sử dụng thông tin **Vị trí** và **Độ dài Hậu tố (Suffix Length):** Cải tiến việc lập chỉ mục bằng cách xét cả vị trí của ký hiệu trong tiền tố và độ dài của phần còn lại của chuỗi.

