TFRecords是一种Protobuf的二进制文件，可以保存任意格式的数据。

## 数据格式
TFRecords使用Protobuf格式描述数据，在TF中的格式类型为：
* `tf.train.Example`
* `tf.train.Features`：包含一个字符串到`Feature`的字典
* `tf.train.Feature`
* `tf.train.BytesList`
* `tf.train.FloatList`
* `tf.train.Int64List`

原始Protobuf定义为
```protobuf
// tensorflow/core/example/example.proto
message Example {
  Features features = 1;
};

// tensorflow/core/example/feature.proto
message Features {
  map<string, Feature> feature = 1;
};

message Feature {
  oneof kind {
    BytesList bytes_list = 1;
    FloatList float_list = 2;
    Int64List int64_list = 3;
  }
};

message BytesList {
  repeated bytes value = 1;
}
message FloatList {
  repeated float value = 1 [packed = true];
}
message Int64List {
  repeated int64 value = 1 [packed = true];
}
```

## 写数据
将数据定义为`tf.train.Example`的格式，然后通过`tf.python_io.TFRecordWriter`写入到TFRecords文件。

```python
filename = "/path/to/tfrecords"
writer = tf.python_io.TFRecordWriter(filename)
for [label, img] in data:
    example = tf.train.Example(features=tf.train.Features(feature={
            # value必须是python list格式
            "label": tf.train.Feature(int64_list=tf.train.Int64List(value=[label])),
            'img': tf.train.Feature(bytes_list=tf.train.BytesList(value=[img]))
        }))
    writer.write(example.SerializeToString())  #序列化为字符串
writer.close()
```
其他示例：
* tensorflow/examples/how_tos/reading_data/convert_to_records.py

## 读数据
### python格式
使用`tf.train.Example`自带的`ParseFromString()`可以将TFRecords解析为python数据格式。
```python
filename = "/path/to/tfrecords"
for serialized_example in tf.python_io.tf_record_iterator(filename):
    example = tf.train.Example()
    example.ParseFromString(serialized_example)
    img = example.features.feature['img'].bytes_list.value
    label = example.features.feature['label'].int64_list.value
    print image, label
```
### Tensor格式
使用`tf.parse_example`或`tf.parse_single_example`，可以将TFRecords解析为Tensor。
```python
filename = "/path/to/tfrecords"
for serialized_example in tf.python_io.tf_record_iterator(filename):
    example = tf.parse_single_example(serialized_example,
                   features={
                     'label': tf.FixedLenFeature([], tf.int64),
                     'img' : tf.FixedLenFeature([], tf.string),
                   })
    img = tf.decode_raw(example['img'], tf.uint8)
    img = tf.reshape(img, [224, 224, 3])
    img = tf.cast(img, tf.float32) * (1. / 255) - 0.5
    label = tf.cast(example['label'], tf.int32)
    print img, label
```
### 队列中读取TFRecords
使用tf.TFRecordReader，可以产生TFRecords数据队列。tf.TFRecordReader能够记住tfrecord的位置，每次调用`read()`函数会获得下一条数据。
```python
def read_and_decode(filename):
	#根据文件名生成一个队列
    filename_queue = tf.train.string_input_producer([filename])
    reader = tf.TFRecordReader()
    _, serialized_example = reader.read(filename_queue)   #返回文件名和文件
    features = tf.parse_single_example(serialized_example,
                                       features={...})
    ...
    return img, label

img, label = read_and_decode("train.tfrecords")
img_batch, label_batch = tf.train.shuffle_batch([img, label],
                                                batch_size=30, capacity=2000,
                                                min_after_dequeue=1000)
init = tf.initialize_all_variables()
with tf.Session() as sess:
    sess.run(init)
    coord = tf.train.Coordinator()
    threads = tf.train.start_queue_runners(sess=sess, coord=coord)
    for i in range(3):
        val, label= sess.run([img_batch, label_batch])
```