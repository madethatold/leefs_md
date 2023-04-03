## python_lambda

在Python中有两种函数，一种是def定义的函数，另一种是lambda函数，也就是大家常说的匿名函数

```python
lambda [arg1 [,arg2,.....argn]]:expression
```

关于此处的args是参数列表，有非常多的形式：

```python
a, b
a=1, b=2
*args
**kwargs
a, b=1, *args
空
......
```

expression是表达式，其中出现的参数在之前的args中有定义，此表达式只能单行。



lambda 函数是匿名的：
所谓匿名函数，通俗地说就是没有名字的函数。lambda函数没有名字。
lambda 函数有输入和输出：
输入是传入到参数列表argument_list的值，输出是根据表达式expression计算得到的值。
lambda 函数拥有自己的命名空间：
不能访问自己参数列表之外或全局命名空间里的参数，只能完成非常简单的功能

```py
lambda x, y: x*y			# 函数输入是x和y，输出是它们的积x*y
lambda:None					# 函数没有输入参数，输出是None
lambda *args: sum(args)		# 输入是任意个数参数，输出是它们的和(隐性要求输入参数必须能进行算术运算)
lambda **kwargs: 1			# 输入是任意键值对参数，输出是1
```

### lambda表达式的用法

将lambda函数赋值给一个变量，通过这个变量间接调用该lambda函数。

```py
add = lambda x,y:x+y
print(add(1,2))
```

map()根据提供的函数对指定序列做映射

```py
# ===========一般写法：===========
# 1、计算平方数
def square(x):
	return x ** 2

map(square, [1,2,3,4,5])	# 计算列表各个元素的平方
# 结果：
[1, 4, 9, 16, 25]

# ===========匿名函数写法：============
# 2、计算平方数，lambda 写法
map(lambda x: x ** 2, [1, 2, 3, 4, 5])
# 结果：
[1, 4, 9, 16, 25]	 

# 3、提供两个列表，将其相同索引位置的列表元素进行相加
map(lambda x, y: x + y, [1, 3, 5, 7, 9], [2, 4, 6, 8, 10])
# 结果：
[3, 7, 11, 15, 19]
```

reduce() 函数会对参数序列中元素进行累积。用传给 reduce 中的函数 function（有两个参数）先对集合中的第 1、2 个元素进行操作，得到的结果再与第三个数据用 function 函数运算，最后得到一个结果。

```python
# ===========一般写法：===========
# 1、两数相加
def add(x, y):            
	return x + y

reduce(add, [1, 3, 5, 7, 9])    # 计算列表元素和：1+3+5+7+9
# 结果：
25

"""
===========执行步骤解析：===========
调用 reduce(add, [1, 3, 5, 7, 9])时，reduce函数将做如下计算：
1	先计算头两个元素：f(1, 3)，结果为4；
2	再把结果和第3个元素计算：f(4, 5)，结果为9；
3	再把结果和第4个元素计算：f(9, 7)，结果为16；
4	再把结果和第5个元素计算：f(16, 9)，结果为25；
5	由于没有更多的元素了，计算结束，返回结果25。
"""
# ===========匿名函数写法：===========
# 2、两数相加，lambda 写法
reduce(lambda x, y: x + y, [1, 2, 3, 4, 5])
# 结果：
15

# 当然求和运算可以直接用Python内建函数sum()，没必要动用reduce。
	
# 3、但是如果要把序列 [1, 3, 5, 7, 9] 变换成整数 13579，reduce就可以派上用场：
from functools import reduce

def fn(x, y):
	return x * 10 + y

reduce(fn, [1, 3, 5, 7, 9])
# 结果：
13579

```

sorted() 函数对所有可迭代的对象进行排序操作

sort 是 list 的一个方法，而 sorted 可以对所有可迭代的对象进行排序操作。
list 的 sort 方法返回的是对已经存在的列表进行操作，无返回值，而内建函数 sorted 方法返回的是一个新的 list，而不是在原来的基础上进行的操作。

`sorted(iterable[, cmp[, key[, reverse]]])
参数说明：
iterable  ----> 可迭代对象。
cmp       ----> 比较的函数，这个具有两个参数，参数的值都是从可迭代对象中取出，此函数必须遵守的规则为，大于则返回1，小于则返回-1，等于则返回0。
key        ----> 主要是用来进行比较的元素，只有一个参数，具体的函数的参数就是取自于可迭代对象中，指定可迭代对象中的一个元素来进行排序。
reverse  ----> 排序规则，reverse = True 降序 ， reverse = False 升序（默认）。
返回值：
返回重新排序的列表。`

```py
# ===========一般用法：===========
# 1、简单排序
a = [5,7,6,3,4,1,2]
b = sorted(a)       # 使用sorted，保留原列表，不改变列表a的值
print(a)
[5, 7, 6, 3, 4, 1, 2]
print(b)
[1, 2, 3, 4, 5, 6, 7]

# ===========匿名函数用法：===========
L=[('b',2),('a',1),('c',3),('d',4)]
# 2、利用参数 cmp 排序
sorted(L, cmp=lambda x,y:cmp(x[1],y[1]))
# 结果：
[('a', 1), ('b', 2), ('c', 3), ('d', 4)]
# 3、利用参数 key 排序
sorted(L, key=lambda x:x[1])
# 结果：
[('a', 1), ('b', 2), ('c', 3), ('d', 4)]

# 4、按年龄升序
students = [('john', 'A', 15), ('jane', 'B', 12), ('dave', 'B', 10)]
sorted(students, key=lambda s: s[2])
# 结果：
[('dave', 'B', 10), ('jane', 'B', 12), ('john', 'A', 15)]
# 5、按年龄降序
sorted(students, key=lambda s: s[2], reverse=True)
# 结果：
[('john', 'A', 15), ('jane', 'B', 12), ('dave', 'B', 10)]

```

filter() 函数用于过滤序列，过滤掉不符合条件的元素，返回由符合条件元素组成的新列表。

该接收两个参数，第一个为函数，第二个为序列，序列的每个元素作为参数传递给函数进行判，然后返回 True 或 False，最后将返回 True 的元素放到新列表中。

```py
# ===========一般用法：===========
# 1、过滤出列表中的所有奇数
def is_odd(n):
	return n % 2 == 1
		 
newlist = filter(is_odd, [1, 2, 3, 4, 5, 6, 7, 8, 9, 10])
print(list(newlist))
# 结果： [1, 3, 5, 7, 9]

# ===========匿名函数用法：===========
# 2、将列表[1, 2, 3]中能够被3整除的元素过滤出来
newlist = filter(lambda x: x % 3 == 0, [1, 2, 3])
print(list(newlist))
# 结果： [3]

```

## python_zip_enumerate

```python
for i, j, k in zip(L1, L2, L3):
    print(i, j, k)

L = list('abcd')
for index, value in enumerate(L):
    print(index, value)    

# 压缩    
zipped = list(zip(L1, L2, L3))
# 解压缩
list(zip(*zipped)) # 三个元组分别对应原来的列表
```

## numpy的创建

### 使用array

```python
list = [1, 2, 3, 4]
a = np.array(list)  # [1 2 3 4]
b = np.array([[1, 2], [3, 4]])
'''
[[1 2]
 [3 4]]
'''
print(type(a))  # <class 'numpy.ndarray'>
```

### numpy.asarray

array和asarray都可以将结构数据转化为ndarray，但是主要区别就是当数据源是ndarray时，array仍然会copy出一个副本，占用新的内存，但asarray不会。

```python
a = (1, 2, 3, 1)  # tuple
anumpy = np.asarray(a).astype(str)  # ['1' '2' '3' '1']
```

### 使用arange



```python
a = np.arange(10)  # [0 1 2 3 4 5 6 7 8 9]
b = np.arange(4, 20, 2)  # [4 6 8 10 12 14 16 18]
c = b.reshape(2,-1)
'''
[[ 4  6  8 10]
 [12 14 16 18]]
'''
```

### 使用random

```python
np.random.seed(1)  # 设置随机数种子，每次产生随机数前调用seed()
a = np.random.random(4)  # [0.76420273 0.45728841 0.07046036 0.26388213] 生成的是0-1之间的随机数
b = np.random.randint(1, 5, 10)  # [1 3 4 3 4 3 2 3 1 1]
c = np.random.random((3, 4))
'''
[[0.95418985 0.68647588 0.50099484 0.44337593]
 [0.38420351 0.08882329 0.58113123 0.34936513]
 [0.69255483 0.23830084 0.9891052  0.13820249]]
'''
d = np.random.randn(2, 4)  # randn函数返回一个或一组样本，具有标准正态分布
'''
[[ 1.40776863  1.1974462   0.79404053  0.73055881]
 [ 0.69167405 -0.05479887 -0.55090562 -1.5815302 ]]
'''
e = np.random.normal(loc=2, scale=0.2, size=5)  
# 指定期望，方差，长度 随机生成  [1.91327774 1.95570372 2.10550096 2.1649593  1.92160148]
f = np.random.uniform(1, 10, 5)  
# 生成指定范围，指定长度的均勻分布的随机数 [5.4295624  8.83888267 2.10785695 9.61165623 7.39604238]
np.random.shuffle(f)  # 打乱顺序
```

### 使用zeros

```python
a = np.zeros([3, 4], dtype="int32")
b = np.zeros([2, 1])  # ，默认数据类型是浮点
```

### 使用linspace

```python
a = np.linspace(start=1,stop=50,num=10,endpoint=True,retstep=False,dtype="int32")
'''
endpoint true则包含stop，否则不包含
retstep true则生成的数组显示间距，否则不显示
[1 6 11 17 22 28 33 39 44 50]
'''
```

