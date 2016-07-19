---
title: 《算法导论》第二章：算法基础
date: 2016-06-27 02:47:19
tags: [读书笔记, 算法]
---
这一章考察求解了排序问题中的**插入排序**算法，并引用算法设计中的**分治法**来开发**归并排序**的算法。

<!-- more -->

## 插入排序

插入排序的工作方式就像许多人排序一手扑克牌。开始时手中一张牌都没有，然后我们每起一次牌，都会将它与手中的牌作比较，从左到右（或者从右到左）来找到这张牌的正确位置，一直到起到最后一张。这个算法的关键之处就在于**你拿在手上的牌始终都是已经排好序的**。

### 伪代码
INSERTATION-SORT(A)
```
for j = 2 to A.length
  key = A[j]
  // Insert A[j] into the sorted sequence A[1..j-1]
  i = j - 1
  while i > 0 and A[i] > key
    A[i + 1] = A[i]
    i = i - 1
  A[i + 1] = key
```
*（伪代码的数组下标从1开始）*

`for` 循环对应每一次起牌，`key` 代表起上的新牌，`while` 循环表示将新牌和手上的牌从右到左比较，直到找到比新牌小的牌的位置，最后将新牌放到这张牌的后面。

### Javascript 实现
```javascript
function insertion_sort(arr) {
  for(var i = 1; i < arr.length; i++) {
    var key = arr[i];
    var j = i - 1;

    while(j >= 0 && arr[j] > key) {
      arr[j + 1] = arr[j];
      j--;
    }
    arr[j + 1] = key;
  }
  console.log(arr)
}

var a = [3, 1, 4, 5, 7, 2, 11, 13, 0, 9];
insertion_sort(a); //[0, 1, 2, 3, 4, 5, 7, 9, 11, 13]
```

### 插入排序算法的分析
在最好情况，也就是输入数组本身已经从小到大排序了，该算法运行时间为
```
T(n) = an + b
```
是** n 的线性函数**

在最坏的情况，也就是输入数组完全按倒序排列，每一次起到新的牌，都需与手中所有的牌作比较。运行时间为
```
T(n) = an² + bn + c
```
是** n 的二次函数**

我们记插入排序具有最坏情况运行时间 `θ(n²)`

## 归并排序

### 分治法
归并排序在结构上是递归的，完全遵循分治模式，典型地符合分治法的思想：**将原问题分解为几个规模较小但类似于原问题的子问题，递归地求解这些子问题，然后再合并这些子问题的解来建立原问题的解。**

### 伪代码
归并排序将原数组拆分成两个序列，再将每个序列继续拆分成两个序列，直到拆成长度为 1，然后递归开始回升，两两序列合并为排序好的新序列，一直到合并成为新数组。

归并排序的关键就在于合并两个已经排序好的序列，我们通过调用 `MERGE(A, p, q, r)` 来完成合并，其中 A 是数组，p, q, r 是数组的下标，且 p ≤ q ≤ r。该过程假设 A[p..q] 和 A[q + 1..r] 都已经排好序，也就是这个方法用于合并两个已排好序的序列。

MERGE(A, p, q, r)
```
n1 = q - p + 1
n2 = r - q
let L[1..n1 + 1] and R[1..n2 + 1] be new arrays
for i = 1 to n1
  L[i] = A[p + i - 1]
for j = 1 to n2
  R[j] = A[q + j]
L[n1 + 1] = ∞
L[n2 + 1] = ∞
i = 1
j = 1
for k = p to r
  if L[i] <= R[j]
    A[k] = L[i]
    i = i + 1
  else
    A[k] = R[j]
    j = j + 1
```
代码中在每个序列的最后插入了一个**哨兵**，用于简化代码，避免判断是否有堆为空（因为最后一个下标 ++ 完会越界）

该方法只要执行 n 次，即能将两个序列合并为新序列。

现在 MERGE 方法就能作为归并排序算法中的一个子程序，接下来需要对数组进行拆分。这个方法排序数组 A[p..r] 中的元素，如果 p ≥ r, 则该子数组最多有一个元素，所以已经排好序，否则，将 A[p..r] 分成 A[p..q] 和 A[q + 1..r]。

MERGE-SORT(A, p, r)
```
if p < r
  q = 「(p + r) / 2」
  MERGE-SORT(A, p, q)
  MERGE-SORT(A, q + 1, r)
  MERGE(A, p, q, r)
```

### Javascript 实现
```javascript
function merge(arr, p, q, r) {
  var n1 = q - p + 1;
  var n2 = r - q;
  var L = [], R = [];
  for(var i = 0; i < n1; i++) {
    L[i] = arr[p + i];
  }

  for(var j = 0; j < n2; j++) {
    R[j] = arr[q + 1 + j];
  }

  // 合并
  i = 0, j = 0;

  for(var k = p; k <= r; k++) {
    if(j >= n2 || L[i] <= R[j]) {
      arr[k] = L[i];  
      i++;
    } else if(i >= n1 || L[i] > R[j]) {
      arr[k] = R[j];
      j++;
    }
  }
}

function merge_sort(arr, p, r) {
  if(p < r) {
    var q = Math.floor((p + r) / 2);
    merge_sort(arr, p, q);
    merge_sort(arr, q + 1, r);  
    merge(arr, p, q, r);
  }

}

var arr = [3, 2, 6, 3, 11, 10, 17, 0, 1, 9];
merge_sort(arr, 0, a.length - 1)
console.log(arr); // [0, 1, 2, 3, 3, 6, 9, 10, 11, 17]
```

### 归并排序算法的分析
归并排序是将数组递归拆解再合并，可以将问题分解为一颗递归树，运行时间为 `θ(nlgn)`
