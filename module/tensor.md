Tensor代表最底层的数据结构，所有的数据都是以Tensor的形式存储。Tensor可以看作一个带类型的多维数组，类型不可修改但维度可以修改。在计算图中，一个Op操作返回一个或多个Tensor作为输出，另一个Op操作读入这些Tensor进行计算，从而将整个计算图中的Op操作连接起来。

Tensor类似于Caffe中的Blob，不过与Blob不同的是，对于一张图片，Tensor的四个维度分别是`[batch, height, width, channel]`，而Blob的为`[batch, channel, height, width]`。

Tensor中的Rank指数组的维数，使用一个整数表示。Shape指数组维度所组成的数组，使用`python list/tuple`或`tf.TensorShape`类型表示。

使用`t[i, j, k]`可以访问Tensor的元素。

Tensor的数据类型

|Data type   |Python type |Description
|:---        |:---        |:------
|DT_FLOAT    |tf.float32  |32 bits floating point.
|DT_DOUBLE   |tf.float64  |64 bits floating point.
|DT_INT8 tf.i|nt8 8 bits s|igned integer.
|DT_INT16    |tf.int16    |16 bits signed integer.
|DT_INT32    |tf.int32    |32 bits signed integer.
|DT_INT64    |tf.int64    |64 bits signed integer.
|DT_UINT8    |tf.uint8    |8 bits unsigned integer.
|DT_UINT16   |tf.uint16   |16 bits unsigned integer.
|DT_STRING   |tf.string   |Variable length byte arrays. Each element of a Tensor is a byte array.
|DT_BOOL tf.b|ool Boolean.|
|DT_COMPLEX64|    tf.compl|ex64    Complex number made of two 32 bits floating points: real and imaginary parts.
|DT_COMPLEX12|8   tf.compl|ex128   Complex number made of two 64 bits floating points: real and imaginary parts.
|DT_QINT8    |tf.qint8    |8 bits signed integer used in quantized Ops.
|DT_QINT32   |tf.qint32   |32 bits signed integer used in quantized Ops.
|DT_QUINT8   |tf.quint8   |8 bits unsigned integer used in quantized Ops.