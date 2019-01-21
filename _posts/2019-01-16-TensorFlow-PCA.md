---
layout: post
title: 使用TensorFlow实现PCA算法
categories: [PCA]
tags: [TensorFlow,PCA]
description: 使用TensorFlow实现PCA算法
---

![](\img\picture\pca.png)



PCA（Principal Component Analysis，主成分分析）是一种常用的数据分析方法。PCA通过线性变换将原始数据变换为一组各维度线性无关的表示，可用于提取数据的主要特征分量，常用于高维数据的降维。 

具体原理参考：[博客](https://blog.csdn.net/u010376788/article/details/46957957)



```python
import tensorflow as tf
# 使用Eager Execution动态图机制
tf.enable_eager_execution()
import numpy as np
from sklearn import datasets
import seaborn as sns
import matplotlib.pyplot as plt
```

## 1.加载数据集

```python
data = datasets.load_iris(return_X_y=False)
```

## 2.实现PCA算法
```python
#PCA
def pca(x,dim = 2):
    '''
    	x:输入矩阵
    	dim:降维之后的维度数
    '''
    with tf.name_scope("PCA"):
        
        m,n= tf.to_float(x.get_shape()[0]),tf.to_int32(x.get_shape()[1])
        assert not tf.assert_less(dim,n)
        mean = tf.reduce_mean(x,axis=1)
        # 去中心化
        x_new = x - tf.reshape(mean,(-1,1))
        # 无偏差的协方差矩阵
        cov = tf.matmul(x_new,x_new,transpose_a=True)/(m - 1) 
        # 计算特征分解
        e,v = tf.linalg.eigh(cov,name="eigh")
        # 将特征值从大到小排序，选出前dim个的index
        e_index_sort = tf.math.top_k(e,sorted=True,k=dim)[1]
        # 提取前排序后dim个特征向量
        v_new = tf.gather(v,indices=e_index_sort)
        # 降维操作
        pca = tf.matmul(x_new,v_new,transpose_b=True)
    return pca
```

## 3.详解

- 去中心化：$$x_i \gets x_i - \frac{1}{m}  \cdot \sum_{i=1}^{m} x_i $$等价于$$X  \gets X - E(X)$$

```python
mean = tf.reduce_mean(x,axis=1)
# 去中心化
x_new = x - tf.reshape(mean,(-1,1))
```

- 无偏差的协方差：$$\frac {X^TX}{m -1}$$

```python
# 无偏差的协方差矩阵
cov = tf.matmul(x_new,x_new,transpose_a=True)/(m - 1) 
```

- 特征分解

```python
# 计算特征分解
e,v = tf.linalg.eigh(cov,name="eigh")
# 将特征值从大到小排序，选出前dim个的index
e_index_sort = tf.math.top_k(e,sorted=True,k=dim)[1]
# 提取前排序后dim个特征向量
v_new = tf.gather(v,indices=e_index_sort)
```

- 降维操作

```python
# 降维操作
pca = tf.matmul(x_new,v_new,transpose_b=True)
```

## 4.执行PCA算法

```python
pca_data = tf.constant(np.reshape(data.data,(data.data.shape[0],-1)),dtype=tf.float32)
pca_data = pca(pca_data,dim=2)
```

## 5.展示效果

```python
color_mapping = {0: sns.xkcd_rgb['bright purple'],\
        1: sns.xkcd_rgb['pale red'], 2: sns.xkcd_rgb['ochre']}
colors = list(map(lambda x: color_mapping[x], data.target))
plt.scatter(pca_data[:, 0], pca_data[:, 1], c=colors)
plt.show()
```

![](\img\picture\pca_result.png)

在线代码链接：[点击](https://colab.research.google.com/drive/161enhMcCS177JoCH2by4O5H_H7gJgoWO)(需科学上网)

