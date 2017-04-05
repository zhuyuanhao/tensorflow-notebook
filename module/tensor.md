Tensor代表最底层的数据结构，所有的数据都是以Tensor的形式存储。Tensor可以看作一个带类型的多维数组，类型不可修改但维度可以修改。在计算图中，一个Op操作返回一个或多个Tensor作为输出，另一个Op操作读入这些Tensor进行计算，从而将整个计算图中的Op操作连接起来。

所有使用Tensor的地方都可以使用能被[tf.convert_to_tensor](https://www.tensorflow.org/api_docs/python/tf/convert_to_tensor)接受的python对象。

在Session运行前Tensor都是不存在的，只有运行Session后才能访问Tensor中的数据。
```python
c = tf.constant([[1.0, 2.0], [3.0, 4.0]])
d = tf.constant([[1.0, 1.0], [0.0, 1.0]])
e = tf.matmul(c, d)
sess = tf.Session()
result = sess.run(e)
```

## 参考资料
* 简要说明：https://www.tensorflow.org/programmers_guide/dims_types
* API：https://www.tensorflow.org/api_docs/python/tf/Tensor

Tensor类似于Caffe中的Blob，不过与Blob不同的是，对于一张图片，Tensor的四个维度分别是`[batch, height, width, channel]`，而Blob的为`[batch, channel, height, width]`。

Tensor中的Rank指数组的维数，使用一个整数表示。Shape指数组维度所组成的数组，使用`python list/tuple`或`tf.TensorShape`类型表示。

一般使用一个Op操作返回的结果Tensor。

使用`t[i, j, k]`可以访问Tensor的元素。

**Tensor的数据类型**

|Data type   |Python type |Description
|:---        |:---        |:------
|DT_FLOAT    |tf.float32  | 浮点数
|DT_DOUBLE   |tf.float64  |
|DT_INT8     |tf.int8     | 有符号整数
|DT_INT16    |tf.int16    |
|DT_INT32    |tf.int32    |
|DT_INT64    |tf.int64    |
|DT_UINT8    |tf.uint8    | 无符号整数
|DT_UINT16   |tf.uint16   |
|DT_STRING   |tf.string   | 每个元素是一个字节数组
|DT_BOOL     |tf.bool     | 布尔类型
|DT_COMPLEX64|tf.complex64| 复数（实部和虚部各32位）
|DT_COMPLEX128|tf.complex128|
|DT_QINT8    |tf.qint8    | quantized Ops（定点数）
|DT_QINT32   |tf.qint32   |
|DT_QUINT8   |tf.quint8   |