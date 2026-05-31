## 语法基础

#### 快读模板

```python
import sys
input = lambda:sys.stdin.readline().strip()
```



#### 内置排序算法 sorted()

可以对任何可迭代对象进行排序，返回一个新的排序后的列表。

> sorted(iterable, key=None, reverse=False)
>
> iterable：需要排序的可迭代对象
>
> key：排序规则，可以传入一个函数，指定排序依据
>
> reverse：是否反向排序，默认为False（升序）

```python
word = ["apple", "banana", "kiwi", "cherry"]
print(sorted(words, key=len, reverse=True))
# 输出：['banana', 'cherry', 'apple', 'kiwi']

# 结合 lambda
print(sorted(words, key=lambda x: x[1]))
# 输出：['banana', 'cherry', 'kiwi', 'apple']
```



#### 处理常见输入格式

```python
# 1.单行多个整数
a, b, c = map(int, input().split())  # 输入：1 2 3

# 2.多行多个整数
n = int(input())  # 输入行数
lst = [int(input()) for _ in range(n)]  # 读取 n 行整数

# 3.矩阵输入
n, m = map(int, input().split())  # 输入矩阵大小
matrix = [list(map(int, input().split())) for _ in range(n)]  # 读取 n 行 m 列的矩阵
```



## 语法进阶

#### 列表推导器

> [expression for item in iterable if condition]
>
> - expression: 表达式，用于生成列表中的元素
> - item: 可迭代对象中的每个元素
> - iterable: 可迭代对象（如列表、字符串、range等）
> - condition: 可选，用于过滤元素的条件

```python
# 过滤 1~20 中的偶数并取平方
evens = [i**2 for i in range(1, 21) if i % 2 == 0]
# 输出：[4, 16, 36, 64, 100, 144, 196, 256, 324, 400]

# 将输入字符串列表转换为整数列表
input_data = list(map(int, input().split()))  # 假设输入 "1 2 3 4 5"
# 使用列表推导器实现
input_data = [int(x) for x in input().split()]
# 输出：[1, 2, 3, 4, 5]

# list(int) 转 int
num = int(''.join(map(str, nums)))
```

**列表特性**

比较大小的时候，不管长度如何，依次比较到第一个元素不相等的位置。比如 [1, 2, 3] < [2, 3] 因为在比较 1 < 2 的时候就终止。



#### range 函数

> range(start, stop, step)
>
> - start: 序列起始值（包含）
> - stop: 序列结束值（不包含）
> - step: 步长，默认为1

range返回的是一个range对象，是一个惰性序列，节省内存。如果需要列表，可以用list()函数将其转换为列表。

```python
seq = range(10, 0, -1)  # 倒序数组start应该是最大值，end应该是最小值
```



#### 字符串

`s1.startswith(s2, beg = 0, end = len(s2))`： 用于检查字符串 s1 是否以字符串 s2 开头。是则返回 True。如果指定 beg 和 end，则在 s1 [beg: end] 范围内查找。

使用 `ascii_lowercase` 遍历 26 个字母：

```python
from string import ascii_lowercase
cnt = {ch: 0 for ch in ascii_lowercase}
```



#### 队列（先进先出）

```python
from collections import deque

q = deque()  # 初始化
q.append(x)  # 从右侧入队
q.appendleft(x)  # 从左侧入队
x = q.popleft()  # 从左侧出队（O(1)）
len(q)  # 获取队列长度
q.extend(可迭代元素)  # 向右侧添加可迭代元素
q.extendleft(可迭代元素)    
q.count(1)  # 统计元素个数 1
```



#### 栈（先进后出）

```python
stk = []
stk.append(x)  # 入栈
stk.pop()  # 出栈（默认弹出最后一个元素，O(1)）
stk[-1]  # 获取栈顶元素不移除
stk.get(key, default_value=None)  # 返回 key 对应的 value，不存在则返回 default_value
stk.keys()  # 键构成的可迭代对象
stk.values()  # 值构成的可迭代对象
stk.items()  # 键值对构成的可迭代对象
stk = defaultdict(list)  # 指定了具有默认值空列表的字典
stk[key] = value  # 创建一个键值对
```



#### 栈与队列小技巧

- 快速判断空队列/栈：if not q:  # 判空
- 栈的翻转：stack[::-1]  # 用切片获取逆序
- 队列转列表：list(q)  # 将 deque 转换为普通列表
- 一次性初始化：deque([1, 2, 3])  # 用迭代器初始化队列



#### map 映射函数

```python
# map(function, iterable, ...)

# 计算平方数
def square(x):
    return x ** 2

# 计算列表各个元素的平方
map(square, [1,2,3,4,5])
# [1, 4, 9, 16, 25]

# 使用 lambda 匿名函数
map(lambda x: x ** 2, [1, 2, 3, 4, 5])
# [1, 4, 9, 16, 25]

# 提供了两个列表，对相同位置的列表数据进行相加
map(lambda x, y: x + y, [1, 3, 5, 7, 9], [2, 4, 6, 8, 10])
# [3, 7, 11, 15, 19]
```



## 库函数

#### Counter

```python
from collections import Counter

list1 = ["a", "a", "a", "b", "c", "c", "f", "g", "g", "g", "f"]
dic = Counter(list1)
# dic = 	Counter({'a': 3, 'g': 3, 'c': 2, 'f': 2, 'b': 1})

list1 = ["a", "a", "a", "b", "c", "f", "g", "g", "c", "11", "g", "f", "10", "2"]
print(Counter(list1).most_common(3))
# 结果：[('a', 3), ('g', 3), ('c', 2)]

list1 = ["a", "a", "a", "b", "c", "f", "g", "g", "c", "11", "g", "f", "10", "2"]
print(Counter(list1).most_common(1))
# 结果：[('a', 3)]
# most_common(k) 时间复杂度 O(nlog⁡k)。
```



#### bisect

`bisect(a, x, lo = 0, hi = len(nums))`

- 给定一个单调不减的数组 a，在其 [lo, hi) 区间中，返回第一个严格大于 x 的下标位置
- 时间复杂度 O(log n)

```python
from bisect import *
#      0  1  2  3  4    5
arr = [1, 9, 9, 9, 200, 500]

# 查找插入位置
bisect(arr, 3)  # 输出：1 (第一个大于 3 的索引)
bisect(arr, -99)  # 输出：0 (第一个大于 -99 的索引)
bisect(arr, 1000)  # 输出：6 (第一个大于 1000 的索引，此时为数组长度)

# 查找大于等于
# bisect(arr, x - 1)

# 逆序数组，找到小于 x 的位置索引
# arr = [-x for x in arr]
# bisect(arr, -x)

# 查找首个出现
# bisect_left(arr, x)
```





## 算法入门

#### 埃氏筛（O(nloglogn)）

求解素数

```python
primes = []
is_prime = [True] * (n + 1)
is_prime[1] = is_prime[0] = False

for i in range(2, int(math.sqrt(n)) + 1):  # i * i <= n
    if is_prime[i]:
        for j in range(i * i, n + 1, i):
            is_prime[j] = False
for i in range(2, n + 1):
    if is_prime[i]: primes.append(i)
```



#### 逆波兰表达式

```python
def eval_rpn(tokens: list[str]):
    stack = []
    ops = {
        '+': lambda a, b: a + b,
        '-': lambda a, b: a - b,
        '*': lambda a, b: a * b,
        '/': lambda a, b: int(a / b),  # 向零取整（python默认向下取整）
    }
    
    for t in tokens:
        if t in ops:
            b = stack.pop()
            a = stack.pop()
            stack.append(ops[t](a, b))  # a 在前，b 在后
        else:
            stack.append(int(t))
    return stack[0]
```



#### 根据身高重建队列

```python
def reconstructQueue(self, people: List[List[int]]):
    # [7, 0],[4, 4],[7, 1],[5, 0],[6, 1],[5, 2]
    people.sort(key=lambda x: (-x[0], x[1]))  # 按照x[0]降序、x[1]升序排列
    # [7, 0],[7, 1],[6, 1],[5, 0],[5, 2],[4, 4]
    
    res = []
    for i, p in enumerate(people):
        h, k = p[0], p[1]
        if k == i:
            res.append(p)
        elif k < i:
            res.insert(k, p)
    # [7, 0],[6, 1],[7, 1],[5, 0],[5, 2],[4, 4]
    return res
```

