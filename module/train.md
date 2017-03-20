TensorFlow知道整个计算图，它使用自动微分法找到对于各个变量的损失的梯度值。在运行时会使用梯度下降来更新参数。整个模型的训练可以通过反复地运行内置Optimizer来完成。
```python
optimizer = tf.train.GradientDescentOptimizer(0.01)
train = optimizer.minimize(loss)
for i in range(1000):
sess.run(train, {x:x_train, y:y_train})
```