Graph表示一个完整的计算任务，包括由Op组成的操作集合和由Tensor组成的数据集合。Graph需要在Session中才能执行。

**注意**：建图过程不是线程安全的，必须在单线程中进行或自定义同步操作。

## 参考资料
* API：
    * https://www.tensorflow.org/api_docs/python/tf/Graph
    * https://www.tensorflow.org/api_docs/python/tf/GraphKeys
* 相关操作：https://www.tensorflow.org/api_guides/python/framework

## 默认图
系统环境中有一个默认图 (default graph)，如果op没有指定图，则会被自动添加到默认图。
获取默认图：`tf.get_default_graph()`
重设环境中的默认图：
```python
g = tf.Graph()
with g.as_default():
    ...
```

## 名称空间(name scope)
一个Graph中包含一系列的名称空间(name scope)，这些名称空间可以通过它们的名字形成嵌套关系。

使用`tf[.Graph].name_scope(name/scope)`会返回一个上下文管理器。根据参数不同，将返回不同的上下文：

1. 传入一个其他name scope的上下文，则会返回该scope的上下文管理器
2. 传入None或空字符串，则返回顶层scope（空scope）的上下文管理器
3. 传入一个不以`/`结尾的字符串，则新建一个name scope，并添加到当前的Graph中。新name scope的名字会已它所在的上下文环境中的scope的名字做前缀，组成嵌套关系。如果这个嵌套后的name已存在，则TF会自动调用`self.unique_name(name)`产生一个新的name，一般是在原name字符串后添加下划线和数字序号，如`"conv_1"`。
4. 传入以`/`结尾的字符串，则返回该name所指的name scope，此时该name scope的名称不与它上下文的scope嵌套。如果该name scope不存在，则新建这个scope并添加到Graph。

名称空间的使用：

1. 名称空间会影响在此上下文中定义的Variable, Operation, Tensor的name成员，但不会影响`tf.get_variable()`定义的Variable。
  ```python
  with tf.name_scope("conv"):
    var1 = tf.get_variable(name='var1', ...)
    var2 = tf.Variable(name='var2', ...)
    sum1 = tf.add(var1, var2, name='add')
  print(sum1.op.name)    # conv/add
  print(sum1.name)       # conv/add:0
  print(var1.name)       # var1:0
  print(var2.name)       # conv/var2:0
  ```
2. TF中如果Variable, Tensor, Operation的定义名称相同（Op命名规则不包含序号，Var和Tensor包含序号），则会自动重命名，以保证不同对象有不同的name。
  ```python
  with tf.name_scope("conv"):
    var1 = tf.get_variable(name='obj', ...)
    var2 = tf.Variable(name='obj', ...)
    var3 = tf.Variable(name='obj', ...)
    var4 = tf.Variable(name='obj', ...)
    sum1 = tf.add(var1, var2, name='obj')
  print(sum1.op.name)    # conv/obj_3
  print(sum1.name)       # conv/obj_3:0
  print(var1.name)       # obj:0
  print(var2.name)       # conv/obj:0
  print(var3.name)       # conv/obj_1:0
  print(var4.name)       # conv/obj_2:0
  ```

## 定义图

* `tf[.Graph].device(device_name_or_function)`定义运行图所使用的设备，并返回一个上下文管理器
```python
with tf.device('/gpu:0'): 
    ...
with tf.device('/cpu:0'): 
    ...
```
* `tf.container(container_name)`创建一个用于保存带状态操作（Variable, Queue）的resource container并返回一个上下文管理器，环境中带状态的操作都会包含在这个container中。container可以使用`tf.Session.reset()`释放，其中所有操作都将置为未初始化状态。
```python
with g.container('experiment0'):
  v1 = tf.Variable([1.0])
  v2 = tf.Variable([2.0])
```

## 图的元素集合（collections）
图可以包含任意多个Op集合（collections），每个集合包含相关的操作，此处的集合不是set，同一元素可以添加多次。集合名称可以自定义，一些预定义的集合名称包含在`tf.GraphKeys`类中，系统中其他操作会使用图中预定义的集合完成，比如`tf.Optimizer`操作默认优化`tf.GraphKeys.TRAINABLE_VARIABLES`中的`Variables`。
相关操作：
```python
# 使用默认图
tf[.Graph].add_to_collection(name, value)
tf[.Graph].get_collection(name, scope=None)
```

预定义Op集合：
* `GLOBAL_VARIABLES`: 所有Variable的集合，在分布式环境中也能共享。`tf.Variable()`和`tf.get_variable()`会将变量添加到这个集合中。可以使用`tf.global_variables()`获取。
* `LOCAL_VARIABLES`: 进程内部Variable集合，常用于临时变量，一般不保存在Checkpoint文件中。可以使用`tf.contrib.framework.local_variable()`添加。使用`tf.local_variables()`获取。
* `MODEL_VARIABLES`: 属于模型本身的变量集合，Forward, load/save model 时会用到。可以使用`tf.contrib.framework.model_variable()`添加。
* `TRAINABLE_VARIABLES`: optimizer时使用的Variable集合。新建变量时包含`trainable=True`参数的都添加到这个集合。可以使用`tf.trainable_variables()`获取。
* `MOVING_AVERAGE_VARIABLES`: 包含moving average值的Variable集合。使用`tf.train.ExponentialMovingAverage`的`apply()`方法可以将变量添加到这个集合。使用`tf.moving_average_variables()`获取。
* `SUMMARIES`: summary的Tensor集合。用于`tf.summary.merge_all(key=tf.GraphKeys.SUMMARIES)`。
* `QUEUE_RUNNERS`: QueueRunner对象的集合。使用`tf.train.add_queue_runner()`将默认添加QueueRunner到这个集合。用于`tf.train.start_queue_runners()`的默认集合。
* `REGULARIZATION_LOSSES`: 默认的regularization losses
* `WEIGHTS`: 默认的weights
* `BIASES`: 默认的biases
* `ACTIVATIONS`: 默认的activations

## 依赖控制
使用`tf[.Graph].control_dependencies(control_inputs)`定义一个控制依赖，并返回一个上下文管理器
```
with g.control_dependencies([a, b, c]):
    # d,e 将在a,b,c 执行完之后运行
    d = …
    e = …
```