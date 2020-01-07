# Algorithm-Theory-and-Implementation
对部分算法的简单理论解释和Python代码实现

[TOC]

## 数学

### pow(x, n)

快速幂算法.

递归：二分计算 `pow(x, n // 2)`，再分奇偶讨论。对负数 `n`，需要计算 `1 / pow(x, -n)`.

循环：使 n 的二进制从最低位 (LSB) 到最高位 (MSB) 表示为 $b_0,b_1,...,b_m$，那么有 $n=\sum_{i} b_{i} * 2^{i}$，那么有 $x^n = \prod x ^ {b_i * 2 ^ i}$。其中 $x ^ {2 ^ i}$ 可以由 $x ^ {2 ^ {i-1}}$ 平方得到，这样就节省了计算。

```python
class Solution:
    def myPow(self, x: float, n: int) -> float:
        return x ** n

class Solution:
    # 暴力，超时
    def myPow(self, x: float, n: int) -> float:
        if n < 0:
            x = 1 / x
            n = -n
        res = 1
        while n > 0:
            res *= x
            n -= 1
        return res


class Solution:
    # 快速幂算法，递归
    def myPow(self, x: float, n: int) -> float:
        def helper(x: float, n: int):
            if n == 0:
                return 1
            half = helper(x, n // 2)
            if n % 2 == 0:
                return half * half
            else:
                return half * half * x

        if n < 0:
            return 1 / helper(x, -n)
        else:
            return helper(x, n)


class Solution:
    # 快速幂算法，非递归
    def myPow(self, x: float, n: int) -> float:
        neg = False
        if n < 0:
            n = -n
            neg = True
        res = 1
        while n > 0:
            if n % 2 == 1:
                res *= x
            x *= x
            n = n >> 1
        if neg:
            return 1 / res
        return res
```

### 最大公约数

辗转相除法即欧几里得算法，计算公式：gcd(a, b) = gcd(b, a mod b)。

```python
def gcd(a, b):
    return gcd(b, a % b) if b != 0 else a
```

### 开平方



## 排序

### 简单选择排序

应该是最自然的思路。选择排序的思想是，从全部序列中选取最小的，与第0个元素交换，然后从第1个元素往后找出最小的，与第一个元素交换，再从第2个元素往后选取最小的，与第2个元素交换，直到选取最后一个元素。

```python
def selection_sort(a):
    n = len(a)
    for i in range(n):
        min_idx = i
        # 从后续数组中找到最小元素
        for j in range(i + 1, n):
            if a[j] < a[min_idx]:
                min_idx = j
        # 将后续数组中的最小元素和前一个元素交换
        a[i], a[min_idx] = a[min_idx], a[i]
```

### 冒泡排序

冒泡排序的思想是，从第0个元素到第n-1个元素遍历，若前面一个元素大于后面一个元素，则交换两个元素，这样可将整个序列中最大的元素冒泡到最后，然后再从第0个到第n-2遍历，如此往复，直到只剩一个元素。

相比较简单选择排序，具有**稳定性**。而且还可以改进：在内层循环之前设置一个标记变量，用于标记循环是否进行了交换，在内层循环结束时，若判断没有进行交换，则说明剩下的序列中，每个元素都小于等于后面一个元素，即已经有序，可终止循环。这样，冒泡排序的最好时间复杂度可以提升到O(n)。

```python
def bubble_sort(a):
    n = len(a)
    for i in reversed(range(n)):
        no_swap = True
        # 把最大元素交换到当前的数组最右边（相等不交换）
        for j in range(i):
            if a[j] > a[j+1]:
                a[j], a[j+1] = a[j+1], a[j]
                no_swap = False
        # 没有发生交换，数组已经有序
        if no_swap:
            break
```

### 简单插入排序

每次将未排序序列的第一个元素，插入到已排序序列中的合适位置。假设初始的有序序列为第0个元素（本文描述的序号都从0开始），只有一个元素的序列肯定是有序的，然后从原先序列的第1个元素开始到第n-1个元素遍历，每次将当前元素插入到它之前序列中的合适位置。

```python
def insertion_sort(a):
    n = len(a)
    for i in range(1, n):
        tmp = a[i]
        j = i - 1
        while j >= 0 and a[j] > tmp:
            a[j+1] = a[j]
            j -= 1           
        a[j+1] = tmp
```

### 希尔排序

简单插入排序的一种改进。将增量从 n/2 折半递减到1，针对每个增量进行一次简单插入排序。在增量递减的过程中，数组逐渐有序，能够有效减少元素的移动次数。

```python
def shell_sort(a):
    n = len(a)
    step = n // 2
    while step > 0:
        for i in range(step, n):
            tmp = a[i]
            j = i - step
            while j >= 0 and a[j] > tmp:
                a[j+step] = a[j]
                j -= step
            a[j+step] = tmp
        step //= 2
```

### 归并排序

分为两个部分：分组和合并，第一步是递归地二分待排序数组，直至子数组中只有一个元素再返回；第二步是由下至上不断地合并有序的子数组。

```python
def merge(left, right):
    res = []
    while left and right:
        if left[0] <= right[0]:
            res.append(left.pop(0))
        else:
            res.append(right.pop(0))
    if left:
        res += left
    if right:
        res += right
    return res


def merge_sort(a):
    if len(a) == 1:
        return a
    mid = len(a) // 2
    # 分组：递归地排序数组的左边和右边部分
    left = merge_sort(a[:mid])
    right = merge_sort(a[mid:])
    # 合并左右两个有序数组为一个有序数组
    return merge(left, right)
```

