设计高性能的TF程序的原则是：

1. 使用源码安装TF
1. 使用Queue读取数据
1. 在CPU上做数据预处理

  如果在GPU上做预处理，数据的流动路径是：CPU -> GPU (preprocessing) -> CPU -> GPU (training)，如果在CPU上做：CPU (preprocessing) -> GPU (training)。同时在CPU上预处理可以节省GPU时间用于其他的计算。
  
1. 图片使用NCHW的格式

  TF支持NHWC(TensorFlow default)和NCHW(cuDNN default)格式的数据。在CPU上NHWC比较快，在GPU上NCHW比较快。
  
1. 将共享参数放在GPU上
1. 使用混合的BN (fused batch norm)

  非混合的BN将使用多个kernel来完成，混合BN只用一个kernel。
  ```python
  bn = tf.contrib.layers.batch_norm(
          input_layer, fused=True, data_format='NCHW'
          scope=scope, **kwargs)
  ```
  
参考资料：
* https://www.tensorflow.org/performance/performance_guide
* https://www.tensorflow.org/performance/performance_models
* https://github.com/tensorflow/benchmarks/tree/master/scripts/tf_cnn_benchmarks