参考：https://www.tensorflow.org/api_guides/python/array_ops

## 类型转换
```
tf.string_to_number
tf.to_double
tf.to_float
tf.to_bfloat16
tf.to_int32
tf.to_int64
tf.cast
tf.bitcast
tf.saturate_cast
```

## 形状转换
```
tf.broadcast_dynamic_shape
tf.broadcast_static_shape
tf.shape
tf.shape_n
tf.size
tf.rank
tf.reshape
tf.squeeze
tf.expand_dims
tf.meshgrid
```

## 切片和连接
```
tf.slice
tf.strided_slice
tf.split
tf.tile
tf.pad
tf.concat
tf.stack
tf.parallel_stack
tf.unstack
tf.reverse_sequence
tf.reverse
tf.reverse_v2
tf.transpose
tf.extract_image_patches
tf.space_to_batch_nd
tf.space_to_batch
tf.required_space_to_batch_paddings
tf.batch_to_space_nd
tf.batch_to_space
tf.space_to_depth
tf.depth_to_space
tf.gather
tf.gather_nd
tf.unique_with_counts
tf.scatter_nd
tf.dynamic_partition
tf.dynamic_stitch
tf.boolean_mask
tf.one_hot
tf.sequence_mask
tf.dequantize
tf.quantize_v2
tf.quantized_concat
tf.setdiff1d
```

## Fake quantization
```
tf.fake_quant_with_min_max_args
tf.fake_quant_with_min_max_args_gradient
tf.fake_quant_with_min_max_vars
tf.fake_quant_with_min_max_vars_gradient
tf.fake_quant_with_min_max_vars_per_channel
tf.fake_quant_with_min_max_vars_per_channel_gradient
```