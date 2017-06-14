读取数据共有3种方法:
* **供给数据(Feeding)**： 在TensorFlow程序运行的每一步， 让Python代码来供给数据
* **预加载数据**： 在TensorFlow图中定义常量或变量来保存所有数据(仅适用于数据量比较小的情况)
* **从文件读取数据**： 在TensorFlow图的起始， 让一个输入管线从文件中读取数据

参考资料：
I/O Op：https://www.tensorflow.org/api_guides/python/io_ops

## Feeding机制
TF的feeding机制允许将数据写入到计算图中的任意Tensor中，甚至包括Variable和Constants，一般使用tf.placeholder节点作为feed的目标。计算时，tf.placeholder如果没有被赋值，则会报错。

Feeding机制通过在`run()`或`eval()`调用中使用`feed_dict={}`参数修改计算图中的Tensor。

```python
with tf.Session():
  input = tf.placeholder(tf.float32)
  classifier = ...
  print(classifier.eval(feed_dict={input: my_python_preprocessing_fn()}))
```

## 加载所有数据
使用tf.constant加载所有数据时，数据会拷贝到计算图中，浪费内存。使用tf.Variable，在计算时feed数据，可以减少这部分内存浪费。

然后，使用`tf.train.slice_input_producer`和`tf.train.batch`来读取一个batch数据，这两个Op都使用Queue实现，并添加各自的QueueRunner到`Graph's QUEUE_RUNNER`集合中。
* `tf.train.slice_input_producer`：将Tensor切分成不含第一维的单个Tensor (即[n,a,b,c]变成n个[a,b,c])，可以shuffle
* `tf.train.batch`：合并batch个Tensor，生成一个大Tensor

参考：
* https://github.com/tensorflow/tensorflow/blob/r1.1/tensorflow/examples/how_tos/reading_data/fully_connected_preloaded.py
* https://github.com/tensorflow/tensorflow/blob/r1.1/tensorflow/examples/how_tos/reading_data/fully_connected_preloaded_var.py

## 从文件读取
