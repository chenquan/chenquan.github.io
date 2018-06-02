---
layout: post
title: 将Keras作为tensorflow的精简接口
categories: [Keras,TensorFlow]
description: 将Keras作为tensorflow的精简接口
header-mask:  0.3
tags: 
    - Keras
    - TensorFlow
---

![](/img/picture/keras-tensorflow-logo.jpg)

在tensorflow中调用Keras层
让我们以一个简单的例子开始：MNIST数字分类。

我们将以Keras的全连接层堆叠构造一个TensorFlow的分类器，

```python
import tensorflow as tf
sess = tf.Session() # 建立TensorFlow回话机制

from keras import backend as K
# 将已建立的回话机制传给Keras调用
K.set_session(sess) 
```

然后，我们开始用tensorflow构建模型：

```python
# 定义网络输入数据的占位符
inputs = tf.placeholder(tf.float32, shape=(None, 784))
outputs = labels = tf.placeholder(tf.float32, shape=(None, 10))
```
用Keras可以加速模型的定义过程：
```python
from keras.layers import Dense

# Keras 层可以调用TensorFlow的Tensor
x = Dense(128, activation='relu')(inputs)  # 使用relu激活函数的128个神经单元的全连接层
x = Dense(128, activation='relu')(x)
preds = Dense(10, activation='softmax')(x)  # 输入层使用softmax激活函数，10个神经单元（对应分类数）

```

定义损失函数：
```python
from keras.objectives import categorical_crossentropy
loss = tf.reduce_mean(categorical_crossentropy(outputs, preds))
```

然后，我们可以用tensorflow的优化器来训练模型：
```python
from tensorflow.examples.tutorials.mnist import input_data
mnist_data = input_data.read_data_sets('MNIST_data', one_hot=True)

train_step = tf.train.AdmaOptimizer(0.5).minimize(loss)
with sess.as_default():
    for i in range(100):
        batch = mnist_data.train.next_batch(50)
        train_step.run(feed_dict={img: batch[0],
                                  labels: batch[1]})
```
模型评估：
```python
from keras.metrics import categorical_accuracy as accuracy

acc_value = accuracy(y, preds)
with sess.as_default():
    print acc_value.eval(feed_dict={img: mnist_data.test.images,
                                    labels: mnist_data.test.labels})
```

我们只是将Keras作为生成从tensor到tensor的函数（op）的快捷方法而已，优化过程完全采用的原生tensorflow的优化器，而不是Keras优化器，因此我们压根不需要Keras的Model

**注意：虽然有点反直觉，但Keras的优化器要比TensorFlow的优化器快大概`5-10%`。虽然这种速度的差异基本上没什么差别。**

**训练和测试行为不同**
有些Keras层，如`BN`，`Dropout`，在训练和测试过程中的行为不一致，你可以通过打印`layer.uses_learning_phase`来确定当前层工作在训练模式还是测试模式。

如果你的模型包含这样的层，你需要指定你希望模型工作在什么模式下，通过Keras的`backend`你可以了解当前的工作模式：
```python
from keras import backend as K
print(K.learning_phase())
```

在使用回话机制的过程中，向`feed_dict`中传递`1（训练模式）或0（测试模式）`即可指定当前工作模式：
```python
# 训练模型
train_step.run(feed_dict={x: batch[0], labels: batch[1], K.learning_phase(): 1})

# 测试模型
train_step.run(feed_dict={x: batch[0], labels: batch[1], K.learning_phase(): 0})

```

一个训练和测试行为不同的例子：

```python
from keras.layers import Dropout,Dense
from keras import backend as K
import tensorflow as tf

inputs = tf.placeholder(tf.float32, shape=(None, 784))
outputs = tf.placeholder(tf.float32, shape=(None, 10))

x = Dense(128, activation='relu')(inputs)
x = Dropout(0.5)(x)
x = Dense(128, activation='relu')(x)
x = Dropout(0.5)(x)
preds = Dense(10, activation='softmax')(x)

loss = tf.reduce_mean(categorical_crossentropy(labels, preds))

train_step = tf.train.GradientDescentOptimizer(0.5).minimize(loss)
with sess.as_default():
    for i in range(100):
        batch = mnist_data.train.next_batch(50)
        train_step.run(feed_dict={img: batch[0],
                                  labels: batch[1],
                                  K.learning_phase(): 1})

acc_value = accuracy(labels, preds)
with sess.as_default():
    print acc_value.eval(feed_dict={img: mnist_data.test.images,
                                    labels: mnist_data.test.labels,
                                    K.learning_phase(): 0})
```

