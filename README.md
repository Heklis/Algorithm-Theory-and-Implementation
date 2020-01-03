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

