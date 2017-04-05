Tensor代表最底层的数据结构，所有的数据都是以Tensor的形式存储。Tensor可以看作一个带类型的多维数组，类型不可修改但维度可以修改。在计算图中，一个Op操作返回一个或多个Tensor作为输出，另一个Op操作读入这些Tensor进行计算，从而将整个计算图中的Op操作连接起来。

Tensor类似于Caffe中的Blob，不过与Blob不同的是，对于一张图片，Tensor的四个维度分别是`[batch, height, width, channel]`，而Blob的为`[batch, channel, height, width]`。

Tensor中的Rank指数组的维数，Shape指数组维度所组成的数组。