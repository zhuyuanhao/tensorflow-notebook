Variable用来保存和更新参数，使用Tensor保存实际的数据。
Variable必须显示初始化后才能被使用，可以被保存在磁盘中或从磁盘读取。
相关API：
* Variable：https://www.tensorflow.org/api_guides/python/state_ops
* Saver：https://www.tensorflow.org/api_docs/python/tf/train/Saver

## 新建

变量需要通过seesion初始化后，才能在session中使用。这一初始化步骤为，为初始值指定具体值（本例当中是全为零），并将其分配给每个变量,可以一次性为所有变量完成此操作。
```python
W = tf.Variable(tf.zeros([784,10]))
b = tf.Variable(tf.zeros([10]))
init = tf.global_variables_initializer()
sess.run(init)
```