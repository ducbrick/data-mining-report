
## What is "similarity", how are items considered similar

### 1. Tương đồng là gì?

Trong data mining, "độ tương đồng" được hiểu là độ đo mức độ giống nhau giữa hai đối tượng dữ liệu hay mức độ chồng lặp khi biểu diễn chúng dưới dạng tập hợp hoặc vector đặc trưng.

### 2. Các đối tượng như thế nào thì được coi là tương đồng?

Hai đối tượng được coi là tương đồng khi chúng chia sẻ nhiều đặc trung chung nhau, tức là có mức độ trùng lặp cao giữa các biểu diễn của chúng.

### 3. Làm thế nào để xác định độ tương đồng giữa các đối tượng?

Các tiêu chí đo lường độ tương đồng được chia thành 2 nhóm chính tùy thuộc vào cách biểu diễn dữ liệu:

- Phép đo độ tương đồng (Similarity Measures)

  Các phương pháp này thường được áp dụng khi đối tượng của dữ liệu được biểu diễn dưới dạng tập hợp các phần tử rời rạc. VD: Tài liệu (tập hợp các từ khóa), sản phẩm (tập hợp các thuộc tính),...

  Một phương pháp đơn giản và phổ biến được kể đến là **Độ tương đồng Jaccard (Jaccard Similarity)** hay còn được biết đến với một số cái tên khác là Jaccard Index và Jaccard coefficient. Phương pháp này được định nghĩa như sau:

  > Độ tương đồng Jaccard của 2 tập hợp $S$ và $T$, ký hiệu $\text{SIM}(S, T)$, là tỉ lệ giữa kích thước phần giao của $S$ và $T$ với kích thước phần hợp của chúng:
  >
  > $$\text{SIM}(S, T) = \frac{|S \cap T|}{|S \cup T|}$$

- Phép đo khoảng cách (Distance Measures)

  Với tập dữ liệu có các đối tượng dạng số hoặc có thể ánh xạ thành vector trong không gian đặc trưng, phép đo khoảng cách được áp dụng để phản ánh đúng, đầy đủ và chặt chẽ mối tương quan giữa các đối tượng trong không gian dữ liệu.

  Một số phương pháp đo khoảng cách được kể đến:

  **1. Khoảng cách Euclid (Euclidean Distances)**
  
  Cho 2 vector $\mathbf{x} = [x_1, x_2, \ldots, x_n]$ và $\mathbf{y} = [y_1, y_2, \ldots, y_n]$ trong không gian $n$ chiều, khi đó khoảng cách giữa 2 vectors này được tính bằng công thức:


  $$d(\mathbf{x}, \mathbf{y}) = \sqrt{\sum_{i=1}^{n} (x_i - y_i)^2}$$
  
  Khoảng cách Euclidean thông thường còn được gọi là L2-norm thường được dùng cho  dữ liệu dạng số liên tục hoặc các đối tượng có thể biểu diễn dưới dạng vector trong không gian $n$ chiều ví dụ như một số bài toán phân cụm,...

  Trong hợp tổng quát (Lr-norm), với hằng số $r$ bất kỳ, khoảng cách này được tính bằng công thức khoảng cách Minkowski (Minkowski Distance):

  $$d(\mathbf{x}, \mathbf{y}) = \left(\sum_{i=1}^{n} |x_i - y_i|^r\right)^{1/r}$$

  Khi $r = 2$ ta có khoảng các Euclidean thông thường

  Trường hợp $r = 1$ ta có công thức cho $L_1\text{-norm}$ hay *Manhattan distance*


  **2. Khoảng cách Jaccard (Jaccard Distance)**

  Công thức:

  $$d(x, y) = 1 - \text{SIM}(x, y)$$

  Khác với Jaccard Similar dùng để đo độ giống nhau của 2 tập hợp rời rạc, Jaccard Distance được dùng để đo độ khác nhau giữa chúng. Ưu điểm của phương pháp này là dễ dàng tính toán và mở rộng, phù hợp khi chúng ta không quan tâm đến tần suất xuất hiện của đối tượng trong các tập hợp mà chỉ quan tâm chúng có hoặc không có.

  **3. Khoảng cách Cosin (Cosine Distance)**

  Công thức:

  $$\text{Cosine}(\mathbf{x}, \mathbf{y}) = \frac {\mathbf{x} \cdot \mathbf{y}}{||\mathbf{x}|| \cdot ||\mathbf{y}||}$$


  Đây là một phương pháp quen thuộc để tính góc giữa 2 vector trong không gian Euclid.

  Cosine Distance thường được áp dụng cho các tập dữ liệu thưa và có độ lớn khác nhau khi hướng của vector là yếu tố quyết định sự tương đồng, thay vì khoảng cách tuyệt đối hoặc độ lớn của các thành phần.
  
  **4. Khoảng cách Edit (Edit Distance)**

  Khi dữ liệu là dạng chuỗi (string) ta sử dụng khoảng cách Edit để tính toán độ tương đồng.

  Định nghĩa: Cho 2 chuỗi $x$ và $y$, khi đó khi Edit Distance giữa $x$ và $y$ (kí hiệu $d(x, y)$) được định nghĩa là số lượng thao tác thêm, xóa các ký tự đơn nhỏ nhất để chuyển chuỗi $x$ thành chuỗi $y$.

  Ngoài ra, phương pháp này có thể tính toán dựa vào độ dài của chuỗi $x$, $y$ và độ dài chuỗi con chung dài nhất của chúng (Longest Common Subsequence - LCS).

  Công thức:

  $$d(x, y) = L(x) + L(y) - 2 \times L(LCS(x, y))$$

  Trong đó:
    
    + $L(x)$ là độ dài chuỗi $x$
    + $L(y)$ là độ dài chuỗi $y$
    + $L(LCS(x, y))$ là độ dài chuỗi con chung dài nhất giữa $x$ và $y$

  Edit Distance thường được áp dụng cho các bài toán kiểm tra chính tả, khớp tên, so sánh DNA, nhận dạng mẫu, fuzzy matching,...


  **5. Khoảng cách Hamming (Hamming Distance)**

  Khoảng cách Hamming giữa hai véctơ được định nghĩa là số lượng thành phần mà hai vector khác nhau.

  Công thức:

  $$d(\mathbf{x}, \mathbf{y}) = \sum_{i=0}^{n} |x_i - y_i|$$

  Hamming Distance thường được dùng khi dữ liệu là chuỗi nhị phân hoặc chuỗi ký tự có cùng độ dài. Được áp dụng trong các bài toán về tìm lỗi, tìm kiếm vector tương tự, so khớp mẫu,...
