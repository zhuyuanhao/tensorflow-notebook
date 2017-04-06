Graph表示一个完整的计算任务，包括由Op组成的操作集合和由Tensor组成的数据集合。Graph需要在Session中才能执行。

**注意**：建图过程不是线程安全的，必须在单线程中进行或自定义同步操作。

## 参考资料
* API：
    * https://www.tensorflow.org/api_docs/python/tf/Graph
    * https://www.tensorflow.org/api_docs/python/tf/GraphKeys
* 相关操作：https://www.tensorflow.org/api_guides/python/framework

**默认图**
系统环境中有一个默认图 (default graph)，如果op没有指定图，则会被自动添加到默认图。
获取默认图：`tf.get_default_graph()`
重设环境中的默认图：
```python
g = tf.Graph()
with g.as_default():
    ...
```

**图Op集合（collections）**
图可以包含任意多个Op集合（collections），每个集合包含相关的操作。集合名称可以自定义，一些预定义的集合名称包含在`tf.GraphKeys`类中，系统中其他操作会使用图中预定义的集合完成，比如`tf.Optimizer`操作默认优化`tf.GraphKeys.TRAINABLE_VARIABLES`中的`Variables`。
相关操作：
```python
# 使用默认图
tf.add_to_collection
tf.get_collection
tf.get_collection_ref
```

预定义Op集合：
* `tf.GraphKeys.GLOBAL_VARIABLES`: 所有Variable的集合，在分布式环境中包含所有Variable。`tf.Variable()`和`tf.get_variable()`会将变量添加到这个集合中。使用`tf.global_variables()`可以获得。
* `LOCAL_VARIABLES`: 
* `MODEL_VARIABLES`: 
* `TRAINABLE_VARIABLES`: 
* `SUMMARIES`: 
* `QUEUE_RUNNERS`: 
* `MOVING_AVERAGE_VARIABLES`: 
* `REGULARIZATION_LOSSES`: 
* `WEIGHTS`: 
* `BIASES`: 
* `ACTIVATIONS`: 