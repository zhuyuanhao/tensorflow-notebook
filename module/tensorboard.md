TensorBoard可以用于显示训练图模型，展示某些量(比如loss, weights)的变化，查看图片等。

TensorBoard读取保存summary数据的文件来获取信息。基本流程为：

1. 建立graph，并且使用`tf.summary.*(name, tensor)`指定要获取的tensor。
1. 使用`all_summaries = tf.summary.merge_all()`将所有summary的Op合并起来
1. 新建一个`summary_writer = tf.summary.FileWriter(logdir, graph=None)`，指定要保存的数据的路径，还可以指定对应的graph
1. 一般每隔n次迭代，运行`summary = sess.run(all_summaries)`获取结果，添加到`summary_writer.add_summary(summary, step)`中。
1. 启动tensorboard服务，使用`tensorboard --logdir=path/to/log-directory`或`python -m tensorflow.tensorboard --logdir=...`
1. 打开浏览器，查看`localhost:6006`获取结果

参考资料：
* 代码&文档：https://github.com/tensorflow/tensorflow/tree/r1.1/tensorflow/tensorboard
* 使用实例：
  * https://www.tensorflow.org/get_started/summaries_and_tensorboard
  * https://www.tensorflow.org/get_started/embedding_viz
  * https://www.tensorflow.org/get_started/graph_viz
