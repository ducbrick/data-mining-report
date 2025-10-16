### Giới thiệu Thuật toán Ball Tree

**Ball Tree** (hay Metric Tree) là một cấu trúc dữ liệu cây nhị phân được thiết kế đặc biệt để tổ chức các điểm dữ liệu trong không gian đa chiều, nhằm mục đích tăng tốc quá trình tìm kiếm lân cận gần nhất.

Điểm khác biệt cốt lõi của Ball Tree so với các cấu trúc khác như $k$-D Tree là ở cách phân vùng không gian: thay vì sử dụng siêu phẳng căn chỉnh theo trục tọa độ (axis-aligned hyperrectangles), Ball Tree sử dụng _siêu hình cầu (hypersphere)_, hay còn gọi là _"ball"_, để bao quanh các tập hợp con của dữ liệu.

### Cấu trúc cây

Mỗi node $N$ trong Ball-Tree đại diện cho một siêu cầu $n$ chiều chứa một tập con các điểm dữ liệu $P_N$, và có các thuộc tính sau:

- `center(N)`: tâm của siêu cầu (thường là trọng tâm của các điểm dữ liệu trong node).
- `radius(N)`: bán kinh siêu cầu, thường được định nghĩa là $max_{x \in P_N} ||x - center(N)||$.
- `left` và `right`: hai node con của $N$, lần lượt chứa các tập dữ liệu $P_l$, $P_r$ với $P_l \cup P_r = P_N$ và $P_l \cap P_r = \emptyset$.

### Cách dựng cây

#### 1. Nguyên tắc chung

Có nhiều thuật toán để dựng Ball-Tree nhưng một phương pháp phổ biến nhất đó là đệ quy top-down (phân cực): bắt đầu từ `root` chứa toàn bộ dữ liệu, ở mỗi node, nếu số điểm > `leaf_size` thì tách node thành hai node con bằng một phép phân vùng và tiếp tục đệ quy.

#### 2. Thuật toán phân tách ball-tree truyền thống (Moore's ball-tree)

1. Tính trọng tâm $\bar{x} = \frac{1}{n} \sum_{i=1}^{n} x_i$ hoặc có thể chọn ngẫu nhiên.
2. Chọn điểm cực 1 là điểm xa nhất so với trọng tâm:
   $$p^L = \arg\max_{x \in P_N} ||x-\bar{x}||$$
3. Chọn điểm cực 2 là điểm xa điểm cực 1 nhất:
   $$p^R = \arg\max_{x \in P_N} ||x - p^L||$$
4. Gán mỗi điểm $x$ sang con trái nếu $||x - p^L|| \leq ||x - p^R||$, ngược lại gán sang phải.
5. Với mỗi node con, tính lại $center$ và $radius = \max||x-center||$.



Pseudocode Ball-tree (Moore’s heuristic)

```python
function CONSTRUCT_BALLTREE(D, max_leaf_size):
    input: D, tập các điểm dữ liệu; max_leaf_size kích thước tối đa của một node.
    output: B, nút gốc của Ball Tree.

    
    N = |D|
    center = CENTROID(X)                  		# vector trung bình
    radius = max_{x in D} ||x - center||
    node = new Node(center=center, radius=radius)
    if N <= max_leaf_size:
      node.points = D
      node.is_leaf = True
      return node
    else
      pL = argmax_{x in D} ||x - center||    	# xa nhất tính từ tâm
      pR = argmax_{x in D} ||x - pL||        	# xa nhất tính từ pL

      D_left = { x in D | ||x - pL|| <= ||x - pR|| }
      D_right= D \ D_left
      node.left  = BUILD_BALLTREE(D_left, max_leaf_size)
      node.right = BUILD_BALLTREE(D_right, max_leaf_size)
      return node
    end if
end function
```

#### 3. Ball*-Tree

__Ball*-Tree__ là phiên bản cải tiến của Ball-Tree truyền thống