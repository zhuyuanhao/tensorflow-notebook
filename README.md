# TensorFlow概述
* 张量(Tensor)表示数据，可以看作是一个有类型的多维数组
* 操作(op)表示计算，op获得0个或多个Tensor，执行某种计算，产生0个或多个Tensor
* 图 (graph) 来表示计算任务，图由操作(op)组成
* 变量(Variable)维护执行过程中的状态
* 图必须在会话(Session)里被启动。会话将图的op分发到设备上，同时提供执行op的方法，并将产生的tensor返回

TensorFlow程序通常被组织成构建阶段和执行阶段：
* 构建阶段，op的执行步骤被描述成一个图结构
* 执行阶段，启动会话执行图。在神经网络的训练中，会不断循环得到图中的loss op的结果，根据loss更新图中的变量，然后进入下一次循环

TensorFlow没有采用在Python外部独立运行某个耗时操作的方式，而是先让我们描述一个交互操作图，然后完全将其运行在Python外部。这与Theano或Torch的做法类似。
因此Python代码的目的是用来构建这个可以在外部运行的计算图，以及安排计算图的哪一部分应该被运行。

TF提供底层的TensorFlow Core API实现所有的功能，推荐给熟悉深度学习的用户使用，同时提供更高级抽象的API供普通用户使用，比如`tf.contrib.learn`。其中`contrib`表示该API还在开发中。

# 整体架构
![](/assets/tensorflow_architecture.png)

# 版本1.0 接口兼容问题
tf1.0版本将不兼容之前的tf0.n版本的接口，不兼容的部分参考文档：
https://www.tensorflow.org/install/migration
提供了转换脚本，但只可以将部分的不兼容接口替换掉，最后还是需要手动检查。
    

