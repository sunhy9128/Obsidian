#ML 
## 自动求导

以下代码展示了如何使用 tf.GradientTape() 计算函数 $L(w,b)=|Xw+b-y|^2$ 在  $w = (1, 2)^T, b = 1$ 时分别对 $w, b$ 的偏导数。其中
$$
X=
\begin{bmatrix}
1&2\\3&4
\end{bmatrix}
$$
$$
y=
\begin{bmatrix}
1\\2
\end{bmatrix}
$$

```python
import tensorflow as tf

X = tf.constant([[1., 2.], [3., 4.]])
y = tf.constant([[1.], [2.]])
w = tf.Variable(initial_value=[[1.], [2.]])
b = tf.Variable(initial_value=1.)
with tf.GradientTape() as tape:
    L = tf.reduce_sum(tf.square(tf.matmul(X, w) + b - y))
w_grad, b_grad = tape.gradient(L, [w, b])        # 计算L(w, b)关于w, b的偏导数
print(L, w_grad, b_grad)
```

输出：

```python
tf.Tensor(125.0, shape=(), dtype=float32)
tf.Tensor(
[[ 70.]
[100.]], shape=(2, 1), dtype=float32)
tf.Tensor(30.0, shape=(), dtype=float32)
```

## 线性回归

|年份|2013|2014|2015|2016|2017|
|:---|:---|:---|:---|:---|:---|
|房价|12000|14000|15000|16500|17500|
我们希望通过对该数据进行线性回归，即使用线性模型 $y = ax + b$ 来拟合上述数据，此处 $a$ 和 $b$ 是待求的参数。

### Numpy 下线性回归

首先，我们定义数据，进行基本的归一化操作。

```python
import numpy as np

X_raw = np.array([2013, 2014, 2015, 2016, 2017], dtype=np.float32)
y_raw = np.array([12000, 14000, 15000, 16500, 17500], dtype=np.float32)

X = (X_raw - X_raw.min()) / (X_raw.max() - X_raw.min())
y = (y_raw - y_raw.min()) / (y_raw.max() - y_raw.min())

# 梯度下降方法来求线性模型中两个参数 a 和 b 的值
a, b = 0, 0

num_epoch = 10000
learning_rate = 5e-4
for e in range(num_epoch):
    # 手动计算损失函数关于自变量（模型参数）的梯度
    y_pred = a * X + b
    grad_a, grad_b = 2 * (y_pred - y).dot(X), 2 * (y_pred - y).sum()

    # 更新参数
    a, b = a - learning_rate * grad_a, b - learning_rate * grad_b

print(a, b)
```

### TensorFlow 下线性回归

```python
import tensorflow as tf

X = tf.constant(X)
y = tf.constant(y)

a = tf.Variable(initial_value=0.)
b = tf.Variable(initial_value=0.)
variables = [a, b]

num_epoch = 10000
optimizer = tf.keras.optimizers.SGD(learning_rate=5e-4)
for e in range(num_epoch):
    # 使用tf.GradientTape()记录损失函数的梯度信息
    with tf.GradientTape() as tape:
        y_pred = a * X + b
        loss = tf.reduce_sum(tf.square(y_pred - y))
    # TensorFlow自动计算损失函数关于自变量（模型参数）的梯度
    grads = tape.gradient(loss, variables)
    # TensorFlow自动根据梯度更新参数
    optimizer.apply_gradients(grads_and_vars=zip(grads, variables))
```