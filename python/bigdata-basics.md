

---

#### 基本操作列表

1. 取matrix第一行或第一列
```python
x = np.random.random((5, 2)) // 5x2的矩阵
row0 = x[0, :]
column0 = x[:, 0]
```
2. 遍历numpy的arrays
```python
Z = np.arange(9).reshape(3, 3)
for k, v in np.ndenumerate(Z):
    print(k, v)
    
for k in np.ndindex(Z.shape):
    print(k, Z[k])
```

3. 标准正态分布

对于满足$N(\mu, \sigma^2)$ 的标准正态分布，使用 $\sigma * np.random.randn(...) + \mu$


```python
>>> np.random.randn()
2.1923875335537315 #random

Two-by-four array of samples from N(3, 6.25): #  注意 6.25 = 2.5 * 2.5

>>> 2.5 * np.random.randn(2, 4) + 3
```
4. np.meshgrid(x, y)
```python
x, y = np.arange(5), np.arange(3)	# x和y代表的分别是在x轴和y轴上采样的点
xs, ys = np.meshgrid(x, y)			# xs,ys则代表着通过x,y向量组合出来的mesh面上的所有的坐标点的横纵坐标向量，其中xs是所有的x坐标，ys是所有的y坐标，它们拥有同样的shape
z = np.sqrt(xs**2 + ys**2)			# 只所以拆分为xs, ys两个向量(而不是使用一个单独的vextex的向量)，是为了使用通用函数(ufunc)，通用函数是元素级函数的变体
```