# TensorFlow概述
* 张量(Tensor)表示数据，可以看作是一个类型化的多维数组
* 操作(op)表示计算，op获得0个或多个Tensor，执行某种计算，产生0个或多个Tensor
* 图 (graph) 来表示计算任务，图由操作(op)组成
* 变量(Variable)维护执行过程中的状态
* 图必须在会话(Session)里被启动。会话将图的op分发到设备上，同时提供执行op的方法，并将产生的tensor返回

TensorFlow程序通常被组织成构建阶段和执行阶段：
* 构建阶段，op的执行步骤被描述成一个图
* 执行阶段，启动会话执行图。在神经网络的训练中，会不断循环得到图中的loss op的结果，根据loss更新图中的变量，然后进入下一次循环

TensorFlow Python库有一个默认图 (default graph)，如果op没有指定图，则会被添加到默认图。


TF提供底层的TensorFlow Core API实现所有的功能，推荐给熟悉深度学习的用户使用，同时提供更高级抽象的API供普通用户使用，比如`tf.contrib.learn`。其中`contrib`表示该API还在开发中。

# 版本1.0 接口兼容问题
tf1.0版本将不兼容之前的tf0.n版本的接口，不兼容的部分参考文档：
https://www.tensorflow.org/install/migration
提供了转换脚本，但只可以将部分的不兼容接口替换掉，最后还是需要手动检查。

1. 变量
  一个变量代表着TensorFlow计算图中的一个值，能够在计算过程中使用，甚至进行修改。
  变量需要通过seesion初始化后，才能在session中使用。这一初始化步骤为，为初始值指定具体值（本例当中是全为零），并将其分配给每个变量,可以一次性为所有变量完成此操作。
  ```python
  W = tf.Variable(tf.zeros([784,10]))
  b = tf.Variable(tf.zeros([10]))
  init = tf.global_variables_initializer()
  sess.run(init)
  ```

1. 占位符
  占位符，可以在TensorFlow运行某一计算时根据该占位符输入具体的值。  
  ```python
  x = tf.placeholder("float", shape=[None, 784])
  y = tf.placeholder("float", shape=[None, 10])
  sess.run(loss, {x:[...], y:[...]})
  ```
1. Op

  Operation是最基本的执行单位，一个Op获得0个或多个Tensor，执行计算，产生0个或多个Tensor。

1. Graph

  Graph表示一个完整的计算任务。Graph是由节点所表示的操作Op组成的一个图状结构。Graph需要在Session中才能执行
    
1. Session

  Tensorflow依赖于一个高效的C++后端来进行计算。与后端的这个连接叫做session。Session控制着tf运行时的状态，构建时如果没有传入任何参数将启动默认图。使用`session.run(op)`表示取回当前图的op操作的结果。session分配相关的Op到CPU或GPU上执行，这些方法执行后，将产生的tensor返回。在Python中，返回的tensor是numpy.ndarray对象；在C/C++中, 返回的 tensor是tensorflow::Tensor实例。
  InteractiveSession可以更加灵活地构建你的代码。它能让你在运行图的时候，插入一些计算图，这些计算图是由某些操作(operations)构成的。

1. 执行图
  TensorFlow 程序通常被组织成一个构建阶段和一个执行阶段. 在构建阶段, op   的执行步骤 被描述成一个图. 在执行阶段, 使用会话执行执行图中的 op.

  例如, 通常在构建阶段创建一个图来表示和训练神经网络, 然后在执行阶段反复执行图中的训练 op.

1. 构建图
  TensorFlow Python 库有一个默认图 (default graph), 默认的op 构造器可以为其增加节点. op 构造器的返回值代表被构造出的 op 的输出, 这些返回值可以传递给其它 op 构造器作为输入.

1. 计算损失函数
  
1. 训练模型
  TensorFlow知道整个计算图，它使用自动微分法找到对于各个变量的损失的梯度值。在运行时会使用梯度下降来更新参数。整个模型的训练可以通过反复地运行内置Optimizer来完成。
  ```python
  optimizer = tf.train.GradientDescentOptimizer(0.01)
  train = optimizer.minimize(loss)
  for i in range(1000):
    sess.run(train, {x:x_train, y:y_train})
  ```
1. 模型评估
1. 指定设备
  如果机器上有超过一个可用的 GPU, 除第一个外的其它 GPU 默认是不参与计算的. 为了让 TensorFlow 使用这些 GPU, 你必须将 op 明确指派给它们执行. with tf.device("/gpu:1") 语句用来指派特定的 CPU 或 GPU 执行操作

1. 设计原则
  TensorFlow没有采用在Python外部独立运行某个耗时操作的方式，而是先让我们描述一个交互操作图，然后完全将其运行在Python外部。这与Theano或Torch的做法类似。
  因此Python代码的目的是用来构建这个可以在外部运行的计算图，以及安排计算图的哪一部分应该被运行。
 
# 参考资料

