参考：https://www.tensorflow.org/api_guides/python/nn

## 激活函数
```
tf.nn.relu
tf.nn.relu6
tf.nn.crelu
tf.nn.elu
tf.nn.softplus
tf.nn.softsign
tf.nn.dropout
tf.nn.bias_add
tf.sigmoid
tf.tanh
```

## 卷积
```
tf.nn.convolution
tf.nn.conv2d
tf.nn.depthwise_conv2d
tf.nn.depthwise_conv2d_native
tf.nn.separable_conv2d
tf.nn.atrous_conv2d
tf.nn.atrous_conv2d_transpose
tf.nn.conv2d_transpose
tf.nn.conv1d
tf.nn.conv3d
tf.nn.conv3d_transpose
tf.nn.conv2d_backprop_filter
tf.nn.conv2d_backprop_input
tf.nn.conv3d_backprop_filter_v2
tf.nn.depthwise_conv2d_native_backprop_filter
tf.nn.depthwise_conv2d_native_backprop_input
```

## 池化
```
tf.nn.avg_pool
tf.nn.max_pool
tf.nn.max_pool_with_argmax
tf.nn.avg_pool3d
tf.nn.max_pool3d
tf.nn.fractional_avg_pool
tf.nn.fractional_max_pool
tf.nn.pool
```

## 形态变换
```
tf.nn.dilation2d
tf.nn.erosion2d
tf.nn.with_space_to_batch
```

## 正则化
```
tf.nn.l2_normalize
tf.nn.local_response_normalization
tf.nn.sufficient_statistics
tf.nn.normalize_moments
tf.nn.moments
tf.nn.weighted_moments
tf.nn.fused_batch_norm
tf.nn.batch_normalization
tf.nn.batch_norm_with_global_normalization
```

## 损失函数
```
tf.nn.l2_loss
tf.nn.log_poisson_loss
```

## 分类
```
tf.nn.sigmoid_cross_entropy_with_logits
tf.nn.softmax
tf.nn.log_softmax
tf.nn.softmax_cross_entropy_with_logits
tf.nn.sparse_softmax_cross_entropy_with_logits
tf.nn.weighted_cross_entropy_with_logits
```

## 查询embedding tensors
```
tf.nn.embedding_lookup
tf.nn.embedding_lookup_sparse
```

## RNN
```
tf.nn.dynamic_rnn
tf.nn.bidirectional_dynamic_rnn
tf.nn.raw_rnn
```

## Connectionist Temporal Classification (CTC)
```
tf.nn.ctc_loss
tf.nn.ctc_greedy_decoder
tf.nn.ctc_beam_search_decoder
```

## 评估函数
```
tf.nn.top_k
tf.nn.in_top_k
```

## 抽样训练
**Sampled Loss Functions**
```
tf.nn.nce_loss
tf.nn.sampled_softmax_loss
```
**Candidate Samplers**
```
tf.nn.uniform_candidate_sampler
tf.nn.log_uniform_candidate_sampler
tf.nn.learned_unigram_candidate_sampler
tf.nn.fixed_unigram_candidate_sampler
```
**Miscellaneous candidate sampling utilities**
```
tf.nn.compute_accidental_hits
```
**Quantization ops**
```
tf.nn.quantized_conv2d
tf.nn.quantized_relu_x
tf.nn.quantized_max_pool
tf.nn.quantized_avg_pool
```