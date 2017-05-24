Operation是最基本的执行单位，Op也被称为node。一个Op读取0个或多个Tensor，执行计算，返回0个或多个Tensor。

使用`tf.placeholder(tf.float32)`添加一个占位Op，在运行Session时，指定实际的数据填充这个占位Op，如果placeholder没有被填充数据，则会报错。

```python
a = tf.placeholder(tf.float32)
b = tf.placeholder(tf.float32)
adder_node = a + b  # + provides a shortcut for tf.add(a, b)
print(sess.run(adder_node, {a: 3, b:4.5}))
print(sess.run(adder_node, {a: [1,3], b: [2, 4]}))

7.5
[ 3.  7.]
```

## 参考资料
* API：https://www.tensorflow.org/api_docs/python/tf/Operation




