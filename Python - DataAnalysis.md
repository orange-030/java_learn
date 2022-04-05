# Numpy

## Array

### 方法

#### astype

```python
np.zeros([2, 4])
-> array([[0., 0., 0., 0.],
       	  [0., 0., 0., 0.]])
       	  
np.zeros([2, 4]).astype(int)
-> array([[0, 0, 0, 0],
          [0, 0, 0, 0]])
```



#### sort

```

```



### 属性

#### size

```python
np.array([[1, 2, 3, 4, 5], [6, 7, 8, 9, 10]]).size
-> 10
```



#### shape

```python
np.array([[1, 2, 3, 4, 5], [6, 7, 8, 9, 10]]).shape
-> (2, 5)
```



## Axis

axis=0，沿着纵轴进行操作

axis=1，沿着横轴进行操作

<img src="http://store.secretcamp.cn/uPic/image-202104091151557862021040911515616179403160rBcWl0rBcWl.png" alt="image-20210409115155786" style="zoom: 33%;" />



## 索引

### 索引与切片

### 布尔索引

### 神奇索引

```python
arr = np.array([[1, 2, 3], [4, 5, 6], [7, 8, 9], [10, 11, 12]])
-> array([[ 1,  2,  3],
          [ 4,  5,  6],
          [ 7,  8,  9],
          [10, 11, 12]])

arr[[2,3]]
-> array([[ 7,  8,  9],
          [10, 11, 12]])

arr[np.array([2,3])]
-> array([[ 7,  8,  9],
          [10, 11, 12]])
```



## 生成序列

### np.arange

```python
np.arange(1, 10)
-> array([1, 2, 3, 4, 5, 6, 7, 8, 9])

np.arange(-1, 1, 0.1)
-> array([-1.00000000e+00, -9.00000000e-01, -8.00000000e-01, -7.00000000e-01,
          -6.00000000e-01, -5.00000000e-01, -4.00000000e-01, -3.00000000e-01,
          -2.00000000e-01, -1.00000000e-01, -2.22044605e-16,  1.00000000e-01,
           2.00000000e-01,  3.00000000e-01,  4.00000000e-01,  5.00000000e-01,
           6.00000000e-01,  7.00000000e-01,  8.00000000e-01,  9.00000000e-01])
```





### np.zeros

```python
np.zeros((2,5))
-> array([[0., 0., 0., 0., 0.],
          [0., 0., 0., 0., 0.]])
```



### np.ones

```python
np.ones((2,5))
-> array([[1., 1., 1., 1., 1.],
          [1., 1., 1., 1., 1.]])
```





## 随机数

### np.random.rand

和 np.random.random 效果相同

生成 [0, 1] 之间的随机数

```python
np.random.rand(5)
-> array([0.82426507, 0.15981161, 0.27524656, 0.46256674, 0.3980079 ])
```



### np.random.randn

Return a sample (or samples) from the "standard normal" distribution.

正态分布

```python
np.random.rand(5)
-> array([ 1.50266805,  0.66866102, -0.66158713,  0.43952222,  0.08274748])
```



### np.random.randint

```python
np.random.randint(1, 20, 5)
-> array([ 5,  7, 19, 11,  8])
```



### np.random.uniform

Draw samples from a uniform distribution.

```
uniform(low=0.0, high=1.0, size=None)
```



### np.random.choice

Generates a random sample from a given 1-D array

```python
arr = np.random.randint(1, 10, size=5)
-> array([6, 8, 2, 6, 5])

# replace=False 表示不放回抽样，p表示每个元素被抽取的概率，如果不传则为None，表示均匀抽取
np.random.choice(arr, size=3, replace=False, p=np.array([0.1, 0.2, 0.3, 0.4, 0]))
```



## 拆分

### np.split

Split an array into multiple sub-arrays as views into `ary`.

```python
np.split(ary, indices_or_sections, axis=0)
Parameters
----------
ary : ndarray
    Array to be divided into sub-arrays.
indices_or_sections : int or 1-D array
    If `indices_or_sections` is an integer, N, the array will be divided
    into N equal arrays along `axis`.  If such a split is not possible,
    an error is raised.
    
Returns
-------
sub-arrays : list of ndarrays
    A list of sub-arrays as views into `ary`.  
    
----------    
arr = np.arange(10)
np.split(arr, 2)
-> [array([0, 1, 2, 3, 4]), array([5, 6, 7, 8, 9])]

np.split(arr, [3, 5, 8, 9])
-> [array([0, 1, 2]), array([3, 4]), array([5, 6, 7]), array([8]), array([9])]
```



## 堆叠合并

### np.hstack

Stack arrays in sequence horizontally (column wise).

沿水平方向合并



```python
arr1 = np.array([1, 2, 3, 4, 5])
arr2 = np.array([5, 7, 10, 8, 20])
np.hstack([arr1,arr2])

-> array([ 1,  2,  3,  4,  5,  5,  7, 10,  8, 20])

	(5,) + (5,) -> (10,)
```



### np.vstack

Stack arrays in sequence vertically (row wise).

沿垂直方向合并

```python
arr1 = np.array([1, 2, 3, 4, 5])
arr2 = np.array([5, 7, 10, 8, 20])
np.hstack([arr1,arr2])

-> array([[ 1,  2,  3,  4,  5],
      	 [ 5,  7, 10,  8, 20]])

	(5,) + (5,) -> (2,5)
```





### np.dstack

Stack arrays in sequence depth wise (along third dimension).

沿深度方向合并

```python
arr1 = np.array([1, 2, 3, 4, 5])
arr2 = np.array([5, 7, 10, 8, 20])
np.dstack([arr1, arr2])

-> array([[[ 1,  5],
       	 	 [ 2,  7],
       	 	 [ 3, 10],
         	 [ 4,  8],
        	 [ 5, 20]]])

	(5,) + (5,) -> (1, 5, 2)
```



### np.concatenate

```python
concatenate((a1, a2, ...), axis=0, out=None)

Join a sequence of arrays along an existing axis.

Parameters
----------
a1, a2, ... : sequence of array_like
    The arrays must have the same shape, except in the dimension
    corresponding to `axis` (the first, by default).
axis : int, optional
    The axis along which the arrays will be joined.  If axis is None,
    arrays are flattened before use.  Default is 0.
out : ndarray, optional
    If provided, the destination to place the result. The shape must be
    correct, matching that of what concatenate would have returned if no
    out argument were specified.
    
Returns
-------
res : ndarray
    The concatenated array.
    
a = np.array([[1, 2], [3, 4]])
b = np.array([[5, 6]])

np.concatenate([a, b], axis=0)
-> array([[1, 2],
          [3, 4],
          [5, 6]])

np.concatenate((a, b.T), axis=1)
-> array([[1, 2, 5],
          [3, 4, 6]])
```



## 各类运算

### np.sum

```python
arr = np.array([[1, 2, 3, 4, 5], [6, 7, 8, 9, 10]])

np.sum(arr, 0)
-> array([ 7,  9, 11, 13, 15])

np.sum(arr, 1)
-> array([15, 40])

np.sum(arr)
-> 55
```



### np.delete

```python
np.delete(arr, obj, axis=None)

Parameters
----------
arr : array_like
    Input array.
obj : slice, int or array of ints
    Indicate indices of sub-arrays to remove along the specified axis.
    
np.delete([1,2,3,4,5], [0,4])
-> array([2, 3, 4])
```



### np.cumsum

`numpy.cumsum(a, axis=None, dtype=None, out=None)`

axis=0，按照行累加。

axis=1，按照列累加。

axis不给定具体值，就把numpy数组当成一个一维数组。

```

```



### np.diff

计算离散差值

第一行： [2-1, 3-2, 4-3, 5-4]

第二行： [7-5, 10-7, 9-10, 20-8]

```python
arr = np.array([[1, 2, 3, 4, 5], [5, 7, 10, 8, 20]])
np.diff(arr)

-> array([[ 1,  1,  1,  1],
       	  [ 2,  3, -2, 12]])
```



### np.any

```python
arr = np.random.randint(0, 5, (4, 5))
arr = np.vstack([arr, np.zeros((1, 5)), np.ones((1, 5))])

-> [[0. 0. 3. 1. 0.]
	  [2. 2. 4. 3. 4.]
	  [1. 0. 4. 3. 2.]
	  [0. 3. 0. 3. 2.]
	  [0. 0. 0. 0. 0.]
	  [1. 1. 1. 1. 1.]] 
```

对矩阵所有元素做与操作，所有为 True 则返回 True

```python
np.any(arr)
-> True

np.any(arr, axis=0)
-> [ True  True  True  True  True]

np.any(arr, axis=1)
-> [ True  True  True  True False  True]
```



### np.all

```
arr = np.random.randint(0, 5, (4, 5))
arr = np.vstack([arr, np.zeros((1, 5)), np.ones((1, 5))])

-> [[0. 0. 3. 1. 0.]
	  [2. 2. 4. 3. 4.]
	  [1. 0. 4. 3. 2.]
	  [0. 3. 0. 3. 2.]
	  [0. 0. 0. 0. 0.]
	  [1. 1. 1. 1. 1.]] 
```

对矩阵所有元素做或操作，一个为 True 则返回 True

```python
np.any(arr)
-> False

np.any(arr, axis=0)
-> [False False False False False]

np.any(arr, axis=1)
-> [False True False False False True]
```





## 条件运算

### np.where

`np.where(condition, [x], [y])`

1. np.where(条件)

```python
arr = np.arange(1,10)
np.random.shuffle(arr)
for i in range(5):
    x = np.arange(1,10)
    np.random.shuffle(x)
    arr = np.vstack([arr, x])
arr1 = arr
arr1[0,0] = 1
arr2 = np.hstack([np.zeros([arr.shape[0], 1]), arr, np.zeros([arr.shape[0], 1])]).astype(int)
```



如果不传 x 和 y，则返回值是一个元组，含义是返回的索引位置

第一位是沿着 axis=0 搜索，0,0 表示有两个 1 出现在第一行，后面的 1,2,3,4,5 表示后面几行各出现一个 1

第二位是沿着 axis=1 搜索，0,5 表示第一行出现了两个 1 ，分别在 0,5 的位置，后面的 1,8,5,8,6 分别对应每一行 1 出现的索引位置。 

> 这里的返回值不会有结构化信息，会直接展平维度，例如沿axis=1时是不知道返回的索引是针对哪一行的，只知道针对所有行而言出现了几次。

```python
arr1
-> array([[1, 8, 7, 5, 3, 1, 6, 2, 4],
          [3, 1, 5, 8, 2, 4, 9, 6, 7],
          [8, 9, 4, 7, 5, 6, 2, 3, 1],
          [3, 8, 2, 7, 5, 1, 4, 9, 6],
          [7, 5, 6, 9, 2, 4, 8, 3, 1],
          [7, 3, 4, 2, 5, 6, 1, 9, 8]])

np.where(arr1 == 1)
-> (array([0, 0, 1, 2, 3, 4, 5]), array([0, 5, 1, 8, 5, 8, 6]))
```



2. np.where(条件，条件成立，条件不成立)

```python
a = np.array([[1, 2, 3, 4, 5]])
b = np.array([[6, 7, 8, 9, 10]])
cond = np.array([True, False, True, False, True])
np.where(cond, a, b)

-> array([[1, 7, 3, 9, 5]])
```



返回值是一个元组，分别表示两个索引方向 axis=0 and axis=1



```python
np.where(arr==5)

-> (array([0, 0, 1, 2, 3, 4]), array([0, 5, 3, 3, 2, 0]))
```



## 数组操作

### np.unique

```python
np.unique(np.array([[1, 2, 3, 3, 3], [1, 2, 4, 5, 6]]))

-> array([1, 2, 3, 4, 5, 6])
```



### np.squeeze

除去多维数组中，维数为1的维度

(1,3) -> (3,)

```python
np.squeeze(np.array([[1],[2],[3]]))

-> array([1, 2, 3])
```





## 列表运算

与 list 的运算

```python
arr = np.array([[1, 2, 3, 4, 5], [5, 7, 10, 8, 20]])
list = [1, 2, 3, 4, 5]
arr - list

-> array([[ 0,  0,  0,  0,  0],
       [ 4,  5,  7,  4, 15]])
```



## 格式化打印



# Pandas



# Matplotlib

## pylab

```python
"""
warning:
   Since heavily importing into the global namespace may result in unexpected
   behavior, the use of pylab is strongly discouraged. Use `matplotlib.pyplot`
   instead.

`pylab` is a module that includes `matplotlib.pyplot`, `numpy`, `numpy.fft`,
`numpy.linalg`, `numpy.random`, and some additional functions, all within
a single namespace. Its original purpose was to mimic a MATLAB-like way
of working by importing all functions into the global namespace. This is
considered bad style nowadays.
"""
```



## cm





```python
cmaps = [('Perceptually Uniform Sequential',
                            ['viridis', 'inferno', 'plasma', 'magma']),
         ('Sequential',     ['Blues', 'BuGn', 'BuPu',
                             'GnBu', 'Greens', 'Greys', 'Oranges', 'OrRd',
                             'PuBu', 'PuBuGn', 'PuRd', 'Purples', 'RdPu',
                             'Reds', 'YlGn', 'YlGnBu', 'YlOrBr', 'YlOrRd']),
         ('Sequential (2)', ['afmhot', 'autumn', 'bone', 'cool',
                             'copper', 'gist_heat', 'gray', 'hot',
                             'pink', 'spring', 'summer', 'winter']),
         ('Diverging',      ['BrBG', 'bwr', 'coolwarm', 'PiYG', 'PRGn', 'PuOr',
                             'RdBu', 'RdGy', 'RdYlBu', 'RdYlGn', 'Spectral',
                             'seismic']),
         ('Qualitative',    ['Accent', 'Dark2', 'Paired', 'Pastel1',
                             'Pastel2', 'Set1', 'Set2', 'Set3']),
         ('Miscellaneous',  ['gist_earth', 'terrain', 'ocean', 'gist_stern',
                             'brg', 'CMRmap', 'cubehelix',
                             'gnuplot', 'gnuplot2', 'gist_ncar',
                             'nipy_spectral', 'jet', 'rainbow',
                             'gist_rainbow', 'hsv', 'flag', 'prism'])]
```



## rcParams

查看所有默认属性，修改属性只需要修改字典对应的值即可

```python
>> plt.rcParams
```



恢复默认属性

```python
>> plt.rcdefaults()
```





## sytle

查看所有风格

```python
>> plt.style.available

['Solarize_Light2',
 '_classic_test_patch',
 'bmh',
 'classic',
 'dark_background',
 'fast',
 'fivethirtyeight',
 'ggplot',
 'grayscale',
 'seaborn',
 'seaborn-bright',
 'seaborn-colorblind',
 'seaborn-dark',
 'seaborn-dark-palette',
 'seaborn-darkgrid',
 'seaborn-deep',
 'seaborn-muted',
 'seaborn-notebook',
 'seaborn-paper',
 'seaborn-pastel',
 'seaborn-poster',
 'seaborn-talk',
 'seaborn-ticks',
 'seaborn-white',
 'seaborn-whitegrid',
 'tableau-colorblind10']
```



使用风格

```python
plt.style.use('ggplot')
```



## pyplot

