类似于Variable，Queue是图中一个可修改内容的节点。通过`dequeue, enqueue`操作可以修改Queue的内容，这些操作本质上是一个接受Queue的指针作为参数的Op，但Python接口中被作为Queue的成员函数实现。

常用的Queue包括`tf.FIFOQueue, tf.RandomShuffleQueue`。
主要操作（定义时这些操作都返回相应的Op）：
* `__init__(capacity, dtypes, shapes=None)`
* `dequeue()`
* `dequeue_many(n)`
* `enqueue(vals)`
* `enqueue_many(vals)`

Queue的操作所在device必须和Queue的初始化device一致，Incompatible device placement directives will be ignored when creating these operations。

在读取数据时，常使用多个线程用于往Queue中写入数据，训练所在的线程从Queue中读取BatchSize个数据。由于Session是多线程的，所以多个写线程可以使用同一个Session写数据。

## tf.train.Coordinator
API：https://www.tensorflow.org/api_docs/python/tf/train/Coordinator

Coordinator用于协调多线程的终止。所有线程启动时绑定到同一个Coordinator，所有线程使用这个Coodinator的函数判断是否终止。
函数：
* `tf.train.Coordinator.request_stop()`: 设置状态为终止
* `tf.train.Coordinator.should_stop()`: 返回是否终止
* `tf.train.Coordinator.join()`: 阻塞等待所有线程终止

```python
def MyLoop(coord):
  while not coord.should_stop():
    ...do something...
    if ...some condition...:
      coord.request_stop()

coord = tf.train.Coordinator()

threads = [threading.Thread(target=MyLoop, args=(coord,)) for i in xrange(10)]

# Start the threads and wait for all of them to stop.
for t in threads:
  t.start()
coord.join(threads)
```

## tf.train.QueueRunner
API：https://www.tensorflow.org/api_docs/python/tf/train/QueueRunner

QueueRunner用于为Queue添加多个enqueue操作，每个enqueue操作会单独启动一个线程。添加的多线程使用Coodinator来协调终止或在出错时终止。
```python
# 创建包含Queue的Graph
example = ...ops to create one example...
queue = tf.RandomShuffleQueue(...)
enqueue_op = queue.enqueue(example)
inputs = queue.dequeue_many(batch_size)
train_op = ...use 'inputs' to build the training part of the graph...

# 运行Graph
sess = tf.Session()
# 添加Coodinator和QueueRunner
coord = tf.train.Coordinator()
qr = tf.train.QueueRunner(queue, [enqueue_op] * 4)
enqueue_threads = qr.create_threads(sess, coord=coord, start=True)

try:
    for step in xrange(1000000):
        if coord.should_stop():
            break
        sess.run(train_op)
except Exception, e:        # 捕获QueueRunner线程的Exception
    coord.request_stop(e)
finally:
    coord.request_stop()    # 可以重复调用
    coord.join(threads)
```






