# My Awesome Book

1. Tensor

  Tensor是包含类型的多维数组
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
* 主页：https://www.tensorflow.org
* 中文文档：http://wiki.jikexueyuan.com/project/tensorflow-zh/
* Github：https://github.com/tensorflow/
    * 项目：https://github.com/tensorflow/tensorflow
    * 模型示例：https://github.com/tensorflow/models
    * 性能测试：https://github.com/tensorflow/benchmarks
