1. 占位符
  占位符，可以在TensorFlow运行某一计算时根据该占位符输入具体的值。  
  ```python
  x = tf.placeholder("float", shape=[None, 784])
  y = tf.placeholder("float", shape=[None, 10])
  sess.run(loss, {x:[...], y:[...]})
  ```
1. Op

  Operation是最基本的执行单位，一个Op获得0个或多个Tensor，执行计算，产生0个或多个Tensor。

