TF-Slim(`tensorflow.contrib.slim`)是TensorFlow中的一个轻量级库，用来方便的定义、训练和评估模型，TF-Slim的代码可以和TensorFlow的其他代码自由组合。

参考资料：
* 源码及文档：https://github.com/tensorflow/tensorflow/tree/master/tensorflow/contrib/slim
* Slim模型仓库：https://github.com/tensorflow/models/tree/master/slim

## 组件
* `arg_scope`: 新增的域定义，带有更丰富的默认参数
* `data`: 读数据模块，包含数据工具data providers, parallel_reader, decoding
* `queues`: 用于控制QueueRunner的上下文管理器
* `variables`: 更方便的操作变量
* `layers`: Layer模块，包含很多预定义的Layer，比Operator抽象层次更高
* `losses`: Loss函数
* `learning`: 训练模型
* `evaluation`: 评估模型
* `metrics`: 评估指标
* `nets`: 常见模型的实现

