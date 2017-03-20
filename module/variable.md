一个变量代表着TensorFlow计算图中的一个值，能够在计算过程中使用，甚至进行修改。
变量需要通过seesion初始化后，才能在session中使用。这一初始化步骤为，为初始值指定具体值（本例当中是全为零），并将其分配给每个变量,可以一次性为所有变量完成此操作。
```python
W = tf.Variable(tf.zeros([784,10]))
b = tf.Variable(tf.zeros([10]))
init = tf.global_variables_initializer()
sess.run(init)
```