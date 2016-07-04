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

堆排序的时间复杂度是 `θ(nlgn)`。

## 优先队列

上面讲了使用堆实现堆排序算法，除此之外，堆还有一个常见的应用：作为高效的优先队列。

和堆一样，优先队列也有两种形式：**最大优先队列**和**最小优先队列**。

优先队列是一种用来维护由一组元素构成的集合 S 的数据结构，其中的每一个元素都有一个相关的值，称为**关键字**。

一个最大优先队列支持以下操作：

- INSERT(S, x): 把元素 x 插入集合 S 中。
- MAXIMUM(S): 返回 S 中具有最大键字的元素。
- EXTRACT-MAX(S): 去掉并返回 S 中的具有最大键字的元素。
- INCREASE-KEY(S, x, k): 将元素 x 的关键字值增加到 k，这里假设 k 的值不小于 x 的原关键字值。

最大优先队列的应用有很多，其中一个就是在共享计算机系统的作业调度。最大优先队列记录将要执行的各个作业以及它们之间的相对优先级。当一个作业完成或者被中断后，调度器调用 EXTRACT-MAX 从所有的等待作业中选出具有最高优先级的作业来执行。在任何时候，调度器可以调用 INSERT 把一个新作业加入到队列中来。

相应地，**最小优先队列**支持的操作包括 `INSERT`、`MINIMUM`、`EXTRACT-MIN` 和 `DECREASE-KEY`。

最小优先队列可以被用于基于事件驱动的模拟器。队列中保存要模拟的事件，每个事件都有一个发生时间作为其关键字。事件必须按照发生的时间顺序进行模拟，因为某一事件的模拟结果可能会触发对其他事件的模拟。在每一步，模拟程序调用 EXTRACT-MIN 来选择下一个要模拟的事件。当一个新事件产生时，模拟器通过调用 INSERT 将其插入最小优先级队列中。

### MAXIMUM

我们先讨论如何实现最大优先队列。首先，HEAP-MAXIMUM 可以实现 MAXIMUM 的操作。

*HEAP-MAXIMUM(A)*
```
// 最大堆的根结点即最大优先队列中最大的关键字
return A[1]
```

### EXTRACT-MAX

过程 HEAP-EXTRACT-MAX 实现 EXTRACT-MAX 操作，它与 HEAPSORT 过程中的 `for` 循环体部分相似。

*HEAP-EXTRACT-MAX(A)*
```
if A.heap-size < 1
  error "heap underflow"
// 堆的根结点即是最大值
max = A[1]
// 将最后一个结点放到根结点
A[1] = A[A.heap-size]
A.heap-size = A.heap-size - 1
// 维持最大堆性质
MAX-HEAPIFY(A, 1)
```

### INCREASE-KEY

过程 HEAP-INCREASE-KEY 实现 INCREASE-KEY 操作。当将元素 A[i] 的关键字更新为新值后，因为增大 A[i]坑内会让新的堆违反最大堆性质，所以在从当前结点到根结点的路径上，为新增的关键字寻找恰当的插入位置。当前元素会不断地与其父结点进行比较，如果当前元素的关键字较大，则当前元素与父结点进行交换。这一过程一直重复到当前元素的关键字小于其父结点为止。

在优先队列中，我们更希望增加关键字的优先队列元素由对应的数组下标 i 来标识：

*HEAP-INCREASE-KEY(A, i, key)*
```
if key < A[i]
  error "new key is smaller than current key"
// 替换对应的关键字
A[i] = key
// 为新增关键字寻找正确的位置
while i > 1 and A[PARENT(i)] < A[i]
  // 若新增关键字大于父结点值，交换它们
  exchange A[i] and A[PARENT(i)]
  i = PARENT(i)
```

### INSERT

过程 MAX-HEAP-INSERT 实现 INSERT 操作。它的输入是要被插入到最大堆 A 中的新元素的关键字。MAX-HEAP-INSERT 首先通过增加一个关键字为 `-∞` 的叶结点来扩展最大堆，然后调用 HEAP-INCREASE-KEY 来将新结点设置为对应的关键字，同时保持最大堆的性质。

*MAX-HEAP-INSERT(A, key)*
```
// 扩展最大堆
A.heap-size = A.heap-size + 1
// 给新结点赋值
A[heap-size] = -∞
// 将新结点的值替换成对应关键字
HEAP-INCREASE-KEY(A, A.heap-size, key)
```