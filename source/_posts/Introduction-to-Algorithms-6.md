---
title: 《算法导论》第六章：堆排序
date: 2016-07-03 23:54:10
tags: [读书笔记, 算法]
---
堆排序引入了另一种算法设计技巧：使用“堆”的数据结构来进行信息管理。堆不仅用在堆排序中，而且它也可以构造一种有效的优先队列。

## 堆排序
如下图，（二叉）堆是一个数组，它可以被看成一个近似的完全二叉树：

![](http://7xo08n.com1.z0.glb.clouddn.com/blog/Introduction-to-Algorithms-6/01.png)

树的每一个节点，对应数组中的一个元素，除了最底层外，该树是完全充满的，而且是从左到右填充。

我们给表示堆的数组 A 两个属性，`A.length` 和 `A.heap-size`

- A.length: 数组元素的个数
- A.heap-size: 表示有多少个堆元素存储在该数组中

也就是说，A[1 .. A.length] 是整个数组，但只有 A[1 .. A.heap-size] 存放的是堆的有效元素。（0 <= A.heap-size <= A.length）

树的根节点是 A[1], 这样给定一个节点下标 i， 那么它的父结点、左孩子、右孩子的下标为：

```
// 父节点下标
PARENT(i)
  return 向下取整(i / 2)

// 左孩子下标
LEFT(i)
  return 2 * i

// 右孩子下标
RIGHT(i)
  return 2 * i + 1
```

二叉堆可以分为两种形式：**最大堆** 和 **最小堆**。
在最大堆中，**最大堆性质**是指除了根以外的所有结点 i 都要满足：
```
A[PARENT(i)] >= A[i]
```
也就是说，某个结点的值至多与其父结点一样大。堆的根节点保存的是堆的最大元素，而且在任一子树中，该子树所包含的所有结点都不大于该子树根结点的值。

最小堆正好相反，**最小堆性质**是指除了根以外的所有结点 i 都有：
```
A[PARENT(i)] <= A[i]
```

在堆排序算法中，我们使用的是最大堆。

### 构造最大堆

#### step 1 维护最大堆性质

要进行堆排序，首先要将数组构造成一颗最大堆。

MAX-HEAPIFY 是用于维护最大堆性质的重要过程。它的输入为数组 A 和一个下标 i。在调用 MAX-HEAPIFY 时，我们假定**根结点为 LEFT(i) 和 RIGHT(i) 的二叉树都是最大堆**，当 A[i] 小于任一孩子时，则需要把 A[i] 和最大的孩子交换顺序。MAX-HEAPIFY 通过让 A[i] 的值在最大堆中“逐级下降”，从而使以下标为根结点的子树重新遵循最大堆的性质。

*MAX-HEAPIFY(A, i) 伪代码*
```
// 左孩子下标
l = LEFT(i)
// 右孩子下标
r = RIGHT(i)
// 比较根结点和左孩子的大小
if l <= A.heap-size and A[l] > A[i]
  // 左孩子比根结点大
  largest = l
else
  largest = i
// 比较根结点和右孩子的大小
if r <= A.heap-size and A[r] > A[largest]
  // 右孩子比左孩子和根结点都大
  largest = r

if largest != i
  // 如果存在孩子比根结点大，让根结点和最大的孩子交换顺序
  exchange A[i] with A[largest]
  // 递归调用
  MAX-HEAPIFY(A, largest)
```

#### step 2 构建最大堆

利用 MAX-HEAPIFY，我们可以自底向上把大小为 n 的数组 A[1 .. n] 转换为最大堆，但实际上，子数组 a(N / 2 + 1 .. n) 中的元素都是树的叶子结点。每个叶子结点都可以看成只包含一个元素的堆。所以过程 MAX-HEAPIFY 只需对其他结点调用即可。

*BUILD-MAX-HEAP(A) 伪代码*
```
A.heap-size = A.length
for i = 向下取整(A.length / 2) downto 1
  MAX-HEAPIFY(A, i)
```

#### step 3 堆排序算法

构建好最大堆之后，因为数组中最大元素总在根结点 A[1] 中，通过把根节点和 A[n] 进行互换，可以让该元素放在正确的位置，然后将结点 n 从堆中去掉（减少 A.heap-size 即可），在剩余结点中，继续维护最大堆的性质，不断重复这一过程，直到堆的大小降到 2。

*HEAP-SORT(A) 伪代码*
```
BUILD-MAX-HEAP(A)
for(i = A.length downto 2)
  // 将堆中最大的元素移动到正确的位置
  exchange A[1] with A[i]
  // 将刚才最大的元素剔除出堆
  A.heap-size = A.heap-size - 1
  // 维护最大堆性质
  MAX-HEAPIFY(A, i)
```

### Javascript 实现
```javascript
function max_heapify(arr, i) {
  var l = i * 2 + 1;
  var r = i * 2 + 2;
  var largest = i;

  if(l < arr.heap_size && arr[l] > arr[i]) {
    largest = l;
  } else {
    largest = i;
  }

  if(r < arr.heap_size && arr[r] > arr[largest]) {
    largest = r;
  }

  if(largest != i) {
    [arr[i], arr[largest]] = [arr[largest], arr[i]];
    max_heapify(arr, largest);
  }
}

function build_max_heap(arr) {
  arr.heap_size = arr.length;
  for(var i = Math.floor(arr.heap_size / 2) - 1; i >= 0; i--) {
    max_heapify(arr, i);
  }
}

function heap_sort(arr) {
  build_max_heap(arr);
  for(var i = arr.length - 1; i >= 1; i--) {
    [arr[0], arr[i]] = [arr[i], arr[0]];
    arr.heap_size = arr.heap_size - 1;
    max_heapify(arr, 0);
  }
}

var arr = [16, 4, 10, 14, 7, 9, 3, 2, 8, 1];
heap_sort(arr);
console.log(arr); [1, 2, 3, 4, 7, 8, 9, 10, 14, 16]
```