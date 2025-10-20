### Giới thiệu Thuật toán Ball Tree

**Ball Tree** là một cấu trúc dữ liệu cây nhị phân được thiết kế đặc biệt để tổ chức các điểm dữ liệu trong không gian đa chiều, nhằm mục đích tăng tốc quá trình tìm kiếm lân cận gần nhất.

Điểm khác biệt cốt lõi của Ball Tree so với các cấu trúc khác như $k$-D Tree là ở cách phân vùng không gian: thay vì sử dụng siêu phẳng căn chỉnh theo trục tọa độ (axis-aligned hyperrectangles), Ball Tree sử dụng _siêu hình cầu (hypersphere)_, hay còn gọi là _"ball"_, để bao quanh các tập hợp con của dữ liệu.

### Cấu trúc cây

Mỗi node $N$ trong Ball-Tree đại diện cho một siêu cầu $n$ chiều chứa một tập con các điểm dữ liệu $P_N$, và có các thuộc tính sau:

- `center(N)`: tâm của siêu cầu (thường là trọng tâm của các điểm dữ liệu trong node).
- `radius(N)`: bán kinh siêu cầu, thường được định nghĩa là $max_{x \in P_N} ||x - center(N)||$.
- `left` và `right`: hai node con của $N$, lần lượt chứa các tập dữ liệu $P_l$, $P_r$ với $P_l \cup P_r = P_N$ và $P_l \cap P_r = \emptyset$.

### Cách dựng cây

#### 1. Nguyên tắc chung

Có nhiều thuật toán để dựng Ball-Tree nhưng một phương pháp phổ biến nhất đó là đệ quy top-down (phân cực): bắt đầu từ `root` chứa toàn bộ dữ liệu, ở mỗi node, nếu số điểm > `leaf_size` thì tách node thành hai node con bằng một phép phân vùng và tiếp tục đệ quy.

#### 2. Ball-Tree truyền thống (Moore's ball-tree)

1. Tính trọng tâm $\bar{x} = \frac{1}{n} \sum_{i=1}^{n} x_i$ hoặc có thể chọn ngẫu nhiên.
2. Chọn điểm cực 1 là điểm xa nhất so với trọng tâm:
   $$p^L = \arg\max_{x \in P_N} ||x-\bar{x}||$$
3. Chọn điểm cực 2 là điểm xa điểm cực 1 nhất:
   $$p^R = \arg\max_{x \in P_N} ||x - p^L||$$
4. Gán mỗi điểm $x$ sang con trái nếu $||x - p^L|| \leq ||x - p^R||$, ngược lại gán sang phải.
5. Với mỗi node con, tính lại $center$ và $radius = \max||x-center||$.

**Pseudocode Ball-tree (Moore’s heuristic)**

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

**Áp dụng cho bài toán $k$-NN**

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

- Trung bình: $\mathcal{O}(d \log N)$
- Xấu nhất: $\mathcal{O}(dN)$

#### 3. Ball\*-Tree

**Ball\*-Tree** là phiên bản cải tiến của Ball-Tree truyền thống, được phát triển để khắc phục việc xây dựng cây không cân bằng của Ball-Tree. Bằng cách xem xét sự phân bố toàn cục của dữ liệu để tìm ra siêu mặt phẳng phân tách phù hợp, hiệu quả hơn, Ball\*-Tree tạo ra các vùng con cân bằng hơn từ đó tối ưu cấu trúc cây và cải thiện tốc độ truy vấn.

1. Áp dụng PCA tính vector riêng tốt lớn nhất (vector đầu tiên):

$$\mathbf{w}_{(1)}=\arg\underset{\mathbf{w}}{\max} \frac{\mathbf{w}^T\mathbf{X}^T\mathbf{X}\mathbf{w}}{\mathbf{w}^T\mathbf{w}}$$

2. Chiếu tập dữ liệu $\mathbf{X}$ lên trụ của $\mathbf{w}_{(1)}$ được tập các giá trị sau khi chiếu:

$$T=\mathbf{w}_{(1)}.\mathbf{X}= \{t_1, t_2,\dots,t_n\}$$

3. Xác định siêu mặt phẳng cắt vuống góc với trục của $\mathbf{w}_{(1)}$ tại điểm $t_c$:

$$t_c = \arg \min \frac{|N_2 - N_1|}{N}+\alpha(\frac{t_c - t_{min}}{t_{max} - t_{min}})$$

Trong đó:

- $t_{min} = \underset{i}\min t_i$, $t_{max} = \underset{i}\max t_i$
- $N_1 + N_2 = N$

- Tham số $\alpha \gt 0$ giúp điều chỉnh mức độ ưu tiên giữa cân bằng số lượng và kích thước bán kính

4. Phân vùng dữ liệu $\mathbf{X}$ thành hai tập $\mathbf{X}^R$ và $\mathbf{X}^L$ dựa theo $t_c$:

$\mathbf{X}^R = \{{\mathbf{x}_i}|\mathbf{x}_i \in \mathbf{X}, t_i \lt t_c\}$

$\mathbf{X}^L = \{{\mathbf{x}_i}|\mathbf{x}_i \in \mathbf{X}, t_i \ge t_c\}$

5. Tính $center$ và $radius$ sau khi phân chia.

**Pseudocode Ball\*-Tree**

```py
function BUILD_BALLSTAR(X, max_leaf_size, alpha, S):
    N = |X|
    center = CENTROID(X)
    radius = max_{x in X} ||x - center||
    node = new Node(center=center, radius=radius)
    if N <= max_leaf_size:
        node.points = X
        node.is_leaf = True
        return node

    # 1) Apply PCA: compute first principal component w1
    w1 = FIRST_EIGENVECTOR(X)   # argmax_w (w^T X^T X w) / (w^T w)

    # 2) Project points onto w1 (1D coordinates)
    T = [ t_i = x_i . w1  for x_i in X ]
    t_min = min(T);  t_max = max(T)

    # 3) Search for best split threshold t_c in [t_min, t_max]
    best_tc = None; best_score = +inf
    # Partition the interval [t_min, t_max] into S equal segments; test midpoints
    for s in 1..S:
        tc_s = t_min + (s - 0.5) * (t_max - t_min)/S   # midpoint of segment s
        # Determine partitions by tc_s:
        X_left  = { x_i | t_i < tc_s }
        X_right = { x_i | t_i >= tc_s }
        N1 = |X_left|;  N2 = |X_right|

        score = (abs(N2 - N1) / N) + alpha * ( (tc_s - t_min) / (t_max - t_min) )
        if score < best_score:
            best_score = score
            best_tc = tc_s
            best_left = X_left; best_right = X_right

    node.left  = BUILD_BALLSTAR(best_left,  max_leaf_size, alpha, S)
    node.right = BUILD_BALLSTAR(best_right, max_leaf_size, alpha, S)
    return node
```

Độ phức tạp:

- Trung bình: $\mathcal{O}(S.Nd \log N)$
  - $N \log N$ cho tổng số điểm qua các tầng
  - $d$ để tính PCA bậc 1
  - $S$ số điểm cắt được thử trong $[t_{min}, t_{max}]$
- Xấu nhất: $\mathcal{O}(S.N^2d)$ khi cây lệch do dữ liệu phân bố kém hoặc PCA không tách được

**Thêm node**

```py
function INSERT(root, x):
    # descend greedily by projection
    node = root
    path = empty stack
    while not node.is_leaf:
        push(path, node)
        proj = dot(node.w1, x)          					# O(d)
        if proj < node.tc:
            node = node.left
        else:
            node = node.right
    # node is leaf
    push(path, node)
    append node.points, x              						# O(1) amortized
    node.n += 1
    # update center and radius incrementally if stored sums:
    update_centroid_radius_incremental(node, x)  	# O(d)
    # bubble updates up the path (update center/radius)
    while path not empty:
        p = pop(path)
        recompute_center_radius_from_children(p)  # O(d) if incremental sums stored
    # if overflow leaf, rebuild that leaf (split)
    if |node.points| > max_leaf_size:
        new_subtree = BUILD_BALLSTAR(node.points, max_leaf_size, α, S)
        replace node in parent by new_subtree
```

Độ phức tạp:

- Trung bình: $\mathcal{O}(d \log N)$
- Xấu nhất: $\mathcal{O}(d N)$ do cây lệnh hoặc phải rebuild nhiều

**Xóa node**

```py
function DELETE(root, x):
    # Find and remove x - descent using projection (may miss if PCA changed; assume exact search found)
    node = root
    path = empty stack
    while not node.is_leaf:
        push(path, node)
        proj = dot(node.w1, x)           						# O(d)
        if proj < node.tc:
            node = node.left
        else:
            node = node.right
    # node is leaf: try remove
    if x not in node.points:
        # fallback: must search whole tree (expensive) or return False
        return False
    remove x from node.points          							# O(m) to find-and-remove where m = |node.points|
    node.n -= 1
    update_centroid_radius_after_removal(node)  		# O(d) if incremental maintained
    # bubble updates upward
    while path not empty:
        p = pop(path)
        recompute_center_radius_from_children(p)  	# O(d)
    # optional: if leaf too small, merge/rebuild parent
    if node.n < min_leaf_size:
        parent = parent(node)
        sibling = sibling_of(node)
        merged = sibling.points ∪ node.points
        if |merged| ≤ max_leaf_size:
            parent.is_leaf = True
            parent.points = merged
            delete parent.left/right children
            recompute_center_radius(parent)
        else:
            # rebuild parent subtree from merged
            parent_subtree = BUILD_BALLSTAR(merged, max_leaf_size, α, S)
            replace parent by parent_subtree
    return True
```

Độ phức tạp:

- Trung bình: $\mathcal{O} (d \log N)$
- Xấu nhất: $\mathcal{O} (d N)$

**Áp dụng trong bài toán $k$-NN**

```py
function KNN_SEARCH(root, q, k):
    # max-heap H of size <= k (key = distance, largest at top)
    H = empty max-heap
    # recursive search
    function SEARCH(node):
        if node.is_leaf:
            for x in node.points:
                d = dist(q, x)                      													# O(d)
                if H.size < k: H.push((d,x))
                else if d < H.top().d:
                    H.pop(); H.push((d,x))
            return
        # compute lower bounds to children
        d_left_min  = max(0, dist(q, node.left.center)  - node.left.radius)  	# O(d)
        d_right_min = max(0, dist(q, node.right.center) - node.right.radius) 	# O(d)
        # visit nearer child first
        if d_left_min < d_right_min:
            if H.size < k or d_left_min <= H.top().d:
                SEARCH(node.left)
            if H.size < k or d_right_min <= H.top().d:
                SEARCH(node.right)
        else:
            if H.size < k or d_right_min <= H.top().d:
                SEARCH(node.right)
            if H.size < k or d_left_min <= H.top().d:
                SEARCH(node.left)
    SEARCH(root)
    return items of H sorted ascending
```

Độ phức tạp:

- Trung bình: $\mathcal{O} (d \log N)$
- Xấu nhất: $\mathcal{O} (d N)$

> [!TODO]
>
> Compare 2 these approachs

---

### Tài liệu tham khảo

[1] [New Algorithms for Efficient High-Dimensional Nonparametric Classification](https://www.jmlr.org/papers/volume7/liu06a/liu06a.pdf)

[2] [Ball\*-tree: Efficient spatial indexing for constrained nearest-neighbor earch in metric spaces](https://arxiv.org/pdf/1511.00628)
