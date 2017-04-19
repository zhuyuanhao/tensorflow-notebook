源文件：
https://www.tensorflow.org/code/tensorflow/contrib/framework/python/ops/variables.py

variable包提供定义更方便的定义变量的方式：
```python
# Model Variables
weights = slim.model_variable('weights',
                              shape=[10, 10, 3 , 3],
                              initializer=tf.truncated_normal_initializer(stddev=0.1),
                              regularizer=slim.l2_regularizer(0.05),
                              device='/CPU:0')
model_variables = slim.get_model_variables()

# Regular variables
my_var = slim.variable('my_var',
                       shape=[20, 1],
                       initializer=tf.zeros_initializer())
regular_variables_and_model_variables = slim.get_variables()

my_model_variable = CreateViaCustomCode()
# Letting TF-Slim know about the additional variable.
slim.add_model_variable(my_model_variable)
```
