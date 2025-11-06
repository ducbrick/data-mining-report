### Giới thiệu Thuật toán Ball Tree

**Ball Tree** là một cấu trúc dữ liệu cây nhị phân được thiết kế đặc biệt để tổ chức các điểm dữ liệu trong không gian đa chiều, nhằm mục đích tăng tốc quá trình tìm kiếm lân cận gần nhất.

Điểm khác biệt cốt lõi của Ball Tree so với các cấu trúc khác như $k$-D Tree là ở cách phân vùng không gian: thay vì sử dụng siêu phẳng căn chỉnh theo trục tọa độ (axis-aligned hyperrectangles), Ball Tree sử dụng _siêu hình cầu (hypersphere)_, hay còn gọi là _"ball"_, để bao quanh các tập hợp con của dữ liệu.

### Cấu trúc cây

Mỗi node $N$ trong Ball-Tree đại diện cho một siêu cầu $n$ chiều chứa một tập con các điểm dữ liệu $P_N$, và có các thuộc tính sau:

- `center(N)`: tâm của siêu cầu (thường là trọng tâm của các điểm dữ liệu trong node).
- `radius(N)`: bán kinh siêu cầu, thường được định nghĩa là $max_{x \in P_N} ||x - center(N)||$.
- `left` và `right`: hai node con của $N$, lần lượt chứa các tập dữ liệu $P_l$, $P_r$ với $P_l \cup P_r = P_N$ và $P_l \cap P_r = \emptyset$.

### Cách dựng cây

**Nguyên tắc chung**

Có nhiều thuật toán để dựng Ball-Tree nhưng một phương pháp phổ biến nhất đó là đệ quy top-down (phân cực): bắt đầu từ `root` chứa toàn bộ dữ liệu, ở mỗi node, nếu số điểm > `leaf_size` thì tách node thành hai node con bằng một phép phân vùng và tiếp tục đệ quy.

**Thuật toán Ball-Tree**
Các bước chính để phân vùng tập điểm $P_N$ tại node $N$:

1. Tính trọng tâm $\bar{x} = \frac{1}{n} \sum_{i=1}^{n} x_i$ hoặc có thể chọn ngẫu nhiên.
2. Chọn điểm cực 1 là điểm xa nhất so với trọng tâm:
   $$p^L = \arg\max_{x \in P_N} ||x-\bar{x}||$$
3. Chọn điểm cực 2 là điểm xa điểm cực 1 nhất:
   $$p^R = \arg\max_{x \in P_N} ||x - p^L||$$
4. Gán mỗi điểm $x$ sang con trái nếu $||x - p^L|| \leq ||x - p^R||$, ngược lại gán sang phải.
5. Với mỗi node con, tính lại $center$ và $radius = \max||x-center||$.

**Pseudocode**

```python
function CONSTRUCT_BALLTREE(D, max_leaf_size):
    input: D - data points; max_leaf_size
    output: B - root node Ball Tree.


    N = |D|
    center = CENTROID(X)                      # mean vector
    radius = max_{x in D} ||x - center||
    node = new Node(center=center, radius=radius)
    if N <= max_leaf_size:
      node.points = D
      node.is_leaf = True
      return node
    else
      pL = argmax_{x in D} ||x - center||     # farthest from centroid
      pR = argmax_{x in D} ||x - pL||         # farthest from pL

      D_left = { x in D | ||x - pL|| <= ||x - pR|| }
      D_right= D \ D_left
      node.left  = BUILD_BALLTREE(D_left, max_leaf_size)
      node.right = BUILD_BALLTREE(D_right, max_leaf_size)
      return node
```

Độ phức tạp:

- Trung bình: $\mathcal{O}(Nd \log N)$
- Xấu nhất: $\mathcal{O}(N^2 d)$ (split rất lệch)

**Thêm node**

```py
function INSERT(node, x):
    if node.is_leaf:
        node.points.append(x)
        if |node.points| > max_leaf_size:
            rebuild(node)
    else:
        if dist(x, node.left.center) < dist(x, node.right.center):
            INSERT(node.left, x)
        else:
            INSERT(node.right, x)
    update(node.center, node.radius)
```

Độ phức tạp:

- Trung bình: $\mathcal{O}(d \log N)$
- Xấu nhất: $\mathcal{O}(dN)$ nếu cây bị lệch về một phía.

**Xóa node**

```py
function DELETE(node, x):
    if node.is_leaf:
        remove(x, node.points)
    else:
        if dist(x, node.left.center) < dist(x, node.right.center):
            DELETE(node.left, x)
        else:
            DELETE(node.right, x)
    update(node.center, node.radius)
```

Độ phức tạp:

- Trung bình: $\mathcal{O}(d \log N)$
- Xấu nhất: $\mathcal{O}(dN)$

### Áp dụng Ball-tree cho bài toán $k$-NN

Ý tưởng: Để tìm $k$ lân cận gần nhất của một điểm truy vấn $q$ trong Ball-Tree, ta sử dụng phương pháp đệ quy với cắt tỉa (pruning) dựa trên khoảng cách từ $q$ đến các siêu cầu của các node con.

Khi có một điểm truy vấn `q`, ta có thể tính khoảng cách tối thiểu từ `q` đến bất kỳ điểm nào trong node đó bằng công thức:

$ D\_{min}(node) = \max(0, |q - center| - radius) $

Điều này có nghĩa là:

- Tất cả các điểm trong node đều cách `q` ít nhất là $D_{min}(node)$.
- Nếu $D_{min}(node)$ lớn hơn khoảng cách tới điểm xa nhất trong tập k-neighbor hiện tại ($D_{worst} = heap.max\_key$), thì không có điểm nào trong node đó có thể là ứng viên tốt hơn, và ta cắt tỉa node đó đi. 
- Ngược lại, nếu $D_{min}(node) \leq D_{worst}$, thì vẫn có khả năng tồn tại điểm tốt hơn trong node đó, và ta tiếp tục duyệt.

Việc so sánh khoảng cách nhỏ nhất từ `q` đến các node con nhằm ưu tiên duyệt node con gần hơn trước, giúp tìm được các neighbor tốt sớm hơn và cải thiện hiệu quả cắt tỉa.

```py
function BALLTREE_KNN(root, q, k):
  # root: ball-tree root
  # q: truy vấn
  # k: số neighbors cần tìm
  # heap: max-heap lưu (distance, point) với size <= k (max-key = worst current neighbor)
  heap = empty max-heap
  # recursion with pruning
  function SEARCH(node):
    if node is leaf:
      for p in node.points:
        d = dist(q, p)
        if heap.size < k:
          heap.push((d,p))
        else if d < heap.max_key:
          heap.replace_max((d,p))
      return
    # compute lower bound distance from q to node child:
    # d_min(child) = max(0, dist(q, child.center) - child.radius)
    dL = max(0, dist(q, node.left.center) - node.left.radius)
    dR = max(0, dist(q, node.right.center) - node.right.radius)
    # visit nearer child first to get good neighbors early (improve pruning)
    if dL < dR:
      if heap.size < k or dL <= heap.max_key: SEARCH(node.left)
      if heap.size < k or dR <= heap.max_key: SEARCH(node.right)
    else:
      if heap.size < k or dR <= heap.max_key: SEARCH(node.right)
      if heap.size < k or dL <= heap.max_key: SEARCH(node.left)
  end
  SEARCH(root)
  return heap.items sorted asc by distance
```

Độ phức tạp:

- Trung bình: $\mathcal{O}(d \log N)$:
  - $d$ để tính khoảng cách từ truy vấn đến các node con trong cây: $dist(q, p) = \sqrt{\sum_{i=1}^d (q_i - p_i)^2}$
  - $\log N$ số tầng trung bình của cây
- Xấu nhất: $\mathcal{O}(dN)$

### Kết luận

#### Ưu điểm

- Hiệu quả trong không gian nhiều chiều.
- Có thể sử dụng các phép đo khoảng cách khác nhau $\rightarrow$ phù hợp với nhiều loại dữ liệu.

#### Nhược điểm

- Cấu trúc phức tạp.
- Hiệu suất giảm khi chiều dữ liệu tăng.
- Cần phải xây dựng lại cây khi thêm/xóa node.
- Cần phải xác định kích thước lá tối đa trước khi xây dựng cây.

#### Nhận xét

- Ball-tree là một cấu trúc dữ liệu mạnh mẽ cho các bài toán tìm kiếm gần đúng trong không gian nhiều chiều.
- Việc xây dựng và duy trì cây có thể tốn kém, nhưng giúp tăng tốc đáng kể quá trình truy vấn.
- Cần phải thử nghiệm và điều chỉnh các tham số để đạt được hiệu suất tốt nhất cho từng bài toán cụ thể.

---

### Tài liệu tham khảo

[1] [New Algorithms for Efficient High-Dimensional Nonparametric Classification](https://www.jmlr.org/papers/volume7/liu06a/liu06a.pdf)
