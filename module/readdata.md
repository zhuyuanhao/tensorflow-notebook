读取数据共有3种方法:
* 直接供给数据：使用其他方法获取数据后，使用Feeding机制提供给TF
* 加载所有数据：小数据量时，一次读取所有数据保存在TF的constant或variable中
* 从文件（队列）读取：在TF中使用队列读取文件名列表

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
