---
title: 《算法导论》第四章：分治策略
date: 2016-06-29 22:59:14
tags: [读书笔记, 算法]
---
在第二章的归并排序中已经使用了分治策略。在分治策略中，我们递归地求解一个问题，在每层递归中应用如下三个步骤：

- **分解**步骤将问题划分为一些子问题，子问题的形式于原问题一样，只是规模更小。
- **解决**步骤递归地求解出子问题。如果子问题的规模足够小，则停止递归，直接求解。
- **合并**步骤将子问题的解组合成原问题的解。

当问题足够大，需要递归求解时，我们称之为**递归情况**。当子问题变得足够小，不需要递归时，我们说递归已经“触底”，进入了**基本情况**。

## 最大子数组问题
假设有以下数组：`[13, -3, -25, 20, -3, -16, -23, 18, 20, -7, 12, -5, -22, 15, -4, 7]`，我们的目的是从中寻找一个连续子数组，使得数组元素之和是所有子数组中最大的。我们称这样的连续子数组为**最大子数组**。

### 暴力求解方法
我们当然可以通过暴力方法来求解：简单尝试所有可能的子数组，从中找出最大子数组。`n` 个元素一共有 `1 + 2 + 3 + ... + n` 个子数组，也就是这种方法的运行时间为 `Ω(n²)`。

### 分治策略的求解方法
假定我们要寻求子数组 A[low .. high] 的最大子数组，如果使用分治策略，这意味着我们需要把数组拆分成两个规模尽量相等的子数组，找到子数组的中点位置，假设为 `mid`，然后考虑求解两个子数组 A[low .. mid] 和 B[mid + 1 .. high。

此时，A[low .. high] 的任何连续子数组 A[i .. j] 所处的位置必然是以下三种情况之一：

- 完全位于子数组 A[low .. mid] 中，因此 low <= i <= j <= mid。
- 完全位于子数组 A[mid + 1 .. high] 中，因此 mid <= i <= j <= high。
- 跨越了中点，因此 low <= i <= mid < j <= high。

所以，A[low .. high] 的最大子数组也必定是这三种情况之一，我们只需要找出这三种情况的最大值。我们可以递归地求解 A[low .. mid] 和 A[mid + 1 .. high] 的最大子数组，因为这两个子问题仍是最大子数组问题。接着寻找跨越中点的最大子数组，然后在三种情况中选取和最大者。

要找出跨越中点的子数组很容易，因为它必定跨越中点，所以它一定由 A[i .. mid] 和 A[mid + 1 .. j] 组成，我们只需要找出两边最大的子数组，然后将其合并即可。

过程 FIND-MAX-CROSSING-SUBARRAY 接收数组 A 和下标 low、mid 和 high 为输入，返回一个下标元组划定跨越中点的最大子数组的边界，并返回最大子数组的和。

FIND-MAX-CROSSING-SUBARRAY(A, low, mid, high)
```
left-sum = -∞
sum = 0
for i = mid downto low
  sum = sum + A[i]
  if sum > left-sum
    left-sum = sum
    max-left = i
right-sum = -∞
sum = 0
for j = mid + 1 to high
  sum = sum + A[j]
  if sum > right-sum
    right-sum = sum
    max-right = j
return (max-left, max-right, left-sum + right-sum)
```

有了 FIND-MAX-CROSSING-SUBARRAY， 我们即可设计求解最大子数组问题的分治算法的伪代码了：

FIND-MAXIMUM-SUBARRAY(A, low, high)
```
if high == low
  return (low, high, A[low])
else
  mid = [(low + high) / 2]
  (left-low, left-high, left-sum) = FIND-MAXIMUM-SUBARRAY(A, low, mid)
  (right-low, right-high, right-sum) = FIND-MAXIMUM-SUBARRAY(A, mid + 1, high)
  (cross-low, cross-high, cross-sum) = FIND-MAX-CROSSING-SUBARRAY(A, low, mid, high)
  if left-sum >= right-sum and left-sum >= cross-sum
    return (left-low, left-high, left-sum)
  else right-sum >= left-sum and right-sum >= cross-sum
    return (right-low, right-high, right-sum)
  else
    return (cross-low, cross-high, cross-sum)
```

要理解为什么通过递归就能求出左子数组和右子数组的最大值，关键在于想明白**递归之后子数组问题会合并**。

### Javascript 实现
```javascript
function findMaxCrossingSubarray(arr, low, mid, high) {
  var leftIndex = 0, rightIndex = 0;
  // left
  var leftMax = -999;
  var leftSum = 0;
  for(var i = mid; i >= low; i--) {
    leftSum += arr[i];
    if(leftSum > leftMax) {
      leftMax = leftSum;
      leftIndex = i;
    }
  }

  // right
  var rightMax = -999;
  var rightSum = 0;
  for(var j = mid + 1; j <= high; j++) {
    rightSum += arr[j];
    if(rightSum > rightMax) {
      rightMax = rightSum;
      rightIndex = j;
    }
  }

  return {
    leftIndex: leftIndex,
    rightIndex: rightIndex,
    max: leftMax + rightMax
  }
}

function findMaximumSubarray(arr, low, high) {
  if(low == high) {
    return {
      leftIndex: low,
      high: high,
      max: arr[low]
    }
  } else {
    var mid = Math.floor((low + high) / 2);
    var left = findMaximumSubarray(arr, low, mid);
    var right = findMaximumSubarray(arr, mid + 1, high);
    var cross = findMaxCrossingSubarray(arr, low, mid, high);
    if(left.max >= right.max && left.max >= cross.max) {
      return left;
    } else if (right.max >= left.max && right.max >= cross.max) {
      return right;
    } else {
      return cross;
    }
  }
}

var arr = [13, -3, -25, 20, -3, -16, -23, 18, 20, -7, 12, -5, -22, 15, -4, 7];
var maxSubarray = findMaximumSubarray(arr, 0, arr.length - 1);
/**
 * leftIndex: 7
 * max: 43
 * rightIndex: 10
 */
console.log(maxSubarray);
```

### 分治算法分析
和归并排序一样，FIND-MAXIMUM-SUBARRAY 的运行时间
```
T(n) = ⊙(1)              若 n = 1
T(n) = 2T(n / 2) + ⊙(n)  若 n > 1
```
解为 `T(n) = ⊙(nlgn)`

*最大子数组问题实际上还存在一个线性时间的算法，并未使用分治方法*