Variable用来保存和更新参数，使用Tensor保存实际的数据。
Variable必须显示初始化后才能被使用，可以被保存在磁盘中或从磁盘读取。
相关API：
* Variable：https://www.tensorflow.org/api_guides/python/state_ops
* Saver：https://www.tensorflow.org/api_docs/python/tf/train/Saver

## 新建
使用下列方式可以新建一个变量
```python
biases = tf.Variable(tf.zeros([200]), name="biases")
```
新建Variable时需要传入一个Tensor作为它的初始值，这个Tensor一般由其他Op得到，参考[常量、随机量Op](https://www.tensorflow.org/api_guides/python/constant_op)。
`tf.Variable()`将在计算图中增加三个Op操作
* 变量Op，用于保存Variable的值
* 初始化Op，实际上是一个tf.assign操作
* 产生初始化的值的Op

`tf.Variable()`的返回值是一个`tf.Variable`类型的Python对象。

将变量的新建放在`with tf.device(...):`区块中可以指定变量的位置，使用`tf.train.replica_device_setter`可以简化并行处理时的设备分配。
```python
with tf.device("/cpu:0"):
  v = tf.Variable(...)

with tf.device("/gpu:0"):
  v = tf.Variable(...)

with tf.device("/job:ps/task:7"):
  v = tf.Variable(...)
```
注意变量的更新操作（`tf.Variable.assign`或`tf.train.Optimizer`中的变量更新操作等）必须在同一个设备中执行，Incompatible device placement directives will be ignored when creating these operations。

## 初始化
变量使用之前必须初始化。
可以使用tf.variables_initializer的方式，手动传入所有Variable来完成初始化。
可以在所有的计算图都构造完成后，使用`tf.global_variables_initializer()`添加一个初始化Op执行所有Variable的的初始化操作，然后在Session初始化后第一步就运行这个初始化Op。
```python
W = tf.Variable(tf.zeros([784,10]))
b = tf.Variable(tf.zeros([10]))
init = tf.global_variables_initializer()
with tf.Session() as sess:
  sess.run(init)
  ...
```

由于这个初始化Op对所有Variable的初始化是并行执行的，如果某个Variable的初始化值依赖于另一个Variable，可以使用另一个的`Variable.initialized_value()`获得。
```
w_twice = tf.Variable(W.initialized_value() * 2.0, name="w_twice")
```

## 变量共享
TensorFlow使用Variable Scope机制提供在多处需要使用同一个Variable的方法。
* `var = tf.get_variable(<name>, <shape>, <initializer>)`: 返回名称为name的变量，如果不存在则新建该变量并返回。注意共享变量使用initializer函数初始化Variable，tf.Variable()使用Tensor初始化Variable。
* `tf.variable_scope(<scope_name>/variable_scope_object, reuse=None)`: 返回某个变量域，可以使用字符串或其他变量域对象，在不同的scope中可以使用同样的变量名调用`tf.get_variable()`函数，它们在计算图中的变量名会变为`scope/name`。
* `tf.get_variable_scope()`获取当前`variable_scope`。
* 要想获取共享变量，需要在进入变量域时使用`tf.variable_scope(<scope_name>, reuse=True)`，或者在变量域内调用`reuse_variables()`函数。如果没有调用该函数而使用相同的变量，会报错ValueError。如果设置了reuse而变量不存在（不能reuse），也会报ValueError。注意默认情况下，子域将继承父域的reuse变量。

```python
def conv_relu(input, kernel_shape, bias_shape):
    weights = tf.get_variable("weights", kernel_shape,
        initializer=tf.random_normal_initializer())
    biases = tf.get_variable("biases", bias_shape,
        initializer=tf.constant_initializer(0.0))
    conv = tf.nn.conv2d(input, weights,
        strides=[1, 1, 1, 1], padding='SAME')
    return tf.nn.relu(conv + biases)

def my_image_filter(input_images):
    # 在不同的variable_scope中可以使用相同的name
    with tf.variable_scope("conv1"):
        relu1 = conv_relu(input_images, [5, 5, 32, 32], [32])
    with tf.variable_scope("conv2"):
        return conv_relu(relu1, [5, 5, 32, 32], [32])

with tf.variable_scope("image_filters") as scope:
    # 在同一个variable_scope中共享相同的变量需要reuse_variables()
    result1 = my_image_filter(image1)
    scope.reuse_variables()
    # 也可以用 tf.get_variable_scope().reuse_variables()
    result2 = my_image_filter(image2)
```

子变量域默认将包含父变量域名
```python
with tf.variable_scope("foo") as foo_scope:
    assert foo_scope.name == "foo"
with tf.variable_scope("bar"):
    # 使用字符串时，在子变量域中的域名包含外层变量域
    with tf.variable_scope("baz") as other_scope:
        assert other_scope.name == "bar/baz"
        # 使用变量域对象进入其他变量域不受子域规则约束
        with tf.variable_scope(foo_scope) as foo_scope2:
            assert foo_scope2.name == "foo"  # Other scope
```

`tf.variable_scope()`将隐含的打开一个`tf.name_scope()`，所以在`variable_scope`中的所有Op都会包含name作为前缀。`variable_scope`和`name_scope`也可以嵌套使用，子域将包含父域的名称。注意使用变量域对象进入其他变量域不受子域规则约束。

variable_scope可以包含一个默认的initializer函数，供当前域及子域的所有get_variable函数做初始化使用。
```
with tf.variable_scope("foo", initializer=tf.constant_initializer(0.4)):
    v = tf.get_variable("v", [1])
    ...
```
## 变量的存储
使用`tf.train.Saver`可以为计算图的所有Variable或指定的部分Variable增加ave和restore操作。并且`tf.train.Saver`类提供了保存和读取并恢复Variable的方法。

Saver保存变量的文件被称为Checkpoint文件，可以看作一个二进制的字典，使用`tf.Variable.name`作为Key，实际Tensor数据作Value。使用`tensorflow/tensorflow/python/tools/inspect_checkpoint.py`文件的`print_tensors_in_checkpoint_file()`函数可以查看二进制文件中的值。

保存变量
```python
v1 = tf.Variable(..., name="v1")
v2 = tf.Variable(..., name="v2")
init_op = tf.global_variables_initializer()
saver = tf.train.Saver()
with tf.Session() as sess:
  sess.run(init_op)
  ...
  save_path = saver.save(sess, "/tmp/model.ckpt")
  print("Model saved in file: %s" % save_path)
```

恢复变量
```python
v1 = tf.Variable(..., name="v1")
v2 = tf.Variable(..., name="v2")
saver = tf.train.Saver()
with tf.Session() as sess:
  # 此时并不需要运行sess.run(init_op)
  saver.restore(sess, "/tmp/model.ckpt")
  print("Model restored.")
  ...
```

存储部分变量
在初始化时通过传入一个变量字典，可以指定Saver存储的变量，并且使用字典的Key作为变量存储的Key。注意如果只恢复了部分变量，其余变量一定要初始化。
```
saver = tf.train.Saver({"my_v2": v2})
```