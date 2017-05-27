TF使用字符串指代特定的设备：`/cpu:0`、`/gpu:0`、`/gpu:1`

Operation在哪个设备上执行，是由Operation的实现（是否有CPU和GPU的实现）和指派语句共同决定的。

默认情况下，如果Operation有GPU的实现，则会分配到`/gpu:0`上，且除第一个外的其它 GPU 默认是不参与计算的。

## 指定设备
使用语句`with tf.device("/gpu:1"):`可以手动指定Op的设备分配位置。
```python
with tf.device('/gpu:1'):
  a = tf.constant([1.0, 2.0, 3.0, 4.0, 5.0, 6.0], shape=[2, 3], name='a')
  b = tf.constant([1.0, 2.0, 3.0, 4.0, 5.0, 6.0], shape=[3, 2], name='b')
  c = tf.matmul(a, b)
```

如果指定设备不存在，则会抛出`InvalidArgumentError`错误。如果想让TF自动选择一个可以使用的设备，设置`allow_soft_placement=True`
```python
sess = tf.Session(config=tf.ConfigProto(allow_soft_placement=True))
```

## 查看设备分配
生成Session时，指定配置`log_device_placement=True`，则会打印出Operation和Tenor的具体设备位置
```python
sess = tf.Session(config=tf.ConfigProto(log_device_placement=True))
```

## GPU使用
默认情况下，能通过环境变量`CUDA_VISIBLE_DEVICES`被看到的所有GPU的内存都会被占用。TF允许动态分配需要的内存或者只占用固定比例的内存。

* **动态分配需要的内存**：随着Session执行不断分配内存，且申请的内存不回收（加到TF的内存池中管理）。
  ```python
  config = tf.ConfigProto()
  config.gpu_options.allow_growth = True
  session = tf.Session(config=config, ...)
  ```
* **占用固定比例内存**：指定所有GPU占用的内存比例，注意不能每个GPU指定不同比例。
  ```python
  config = tf.ConfigProto()
  config.gpu_options.per_process_gpu_memory_fraction = 0.4
  session = tf.Session(config=config, ...)
  ```






