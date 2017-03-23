## 代码结构
编译后的代码结构：
```shell
README.md                // 文档
RELEASE.md
CONTRIBUTING.md
ISSUE_TEMPLATE.md
LICENSE
ACKNOWLEDGMENTS
ADOPTERS.md
AUTHORS
bazel-bin -> .../execroot/tensorflow/bazel-out/local_linux-opt/bin
bazel-genfiles -> .../execroot/tensorflow/bazel-out/local_linux-opt/genfiles
bazel-out -> .../execroot/org_tensorflow/bazel-out
bazel-tensorflow -> .../execroot/org_tensorflow
bazel-testlogs -> .../execroot/tensorflow/bazel-out/local_linux-opt/testlogs
configure                // 安装时的编译选项脚本
WORKSPACE                // bazel项目文件
BUILD
bower.BUILD
models.BUILD
tools/                   // bazel工具配置文件和python环境变量
├── bazel.rc
├── bazel.rc.template
└── python_bin_path.sh
util/                    // python环境变量
└── python
    ├── BUILD
    ├── python_config.sh
    ├── python_include -> .../python-2.7/include/python2.7
    └── python_lib -> .../python-2.7/lib/python2.7/site-package
third_party/             // 第三方库的bazel编译文件
├── BUILD
├── common.bzl
├── curl.BUILD
├── eigen3/
├── eigen.BUILD
├── farmhash.BUILD
├── gif.BUILD
├── gmock.BUILD
├── gpus/
├── grpc.BUILD
├── hadoop/
├── jemalloc.BUILD
├── jpeg/
├── jsoncpp.BUILD
├── libxsmm.BUILD
├── linenoise.BUILD
├── llvm/
├── nanopb.BUILD
├── nasm.BUILD
├── nccl.BUILD
├── pcre.BUILD
├── png.BUILD
├── py/
├── six.BUILD
├── swig.BUILD
├── sycl/
└── zlib.BUILD
tensorflow/                  // tf代码
├── __init__.py                  // python包文件
├── tf_exported_symbols.lds      // 编译：链接器文件
├── tf_version_script.lds
├── BUILD                        // 编译：bazel
├── tensorflow.bzl
└── workspace.bzl
├── examples/                    // 文档：示例模型
├── g3doc/                       // 文档：官网页面的源文件
├── cc/                          // 接口文件：C++
├── python/                      // 接口文件：python                       
├── java/                        // 接口文件：java
├── go/                          // 接口文件：go
├── c/                           // 代码：统一C API接口 
├── compiler/                    // 代码：自定义编译器的文件，比如xla       
├── contrib/                     // 代码：扩展包tf.contrib.*的文件
├── opensource_only/             // 代码：只有engin3的一个头文件
├── stream_executor/             // 代码：并行计算文件
├── tensorboard/                 // 代码：可视化工具tensorboard文件
├── tools/                       // 代码：各种工具，benchmark、ci等
├── user_ops/                    // 代码：用于自定Op，包含Op示例
├── core/                        // 核心代码：tf的C++实现代码
│   ├── BUILD                        // 编译：bazel
│   ├── debug/                       // debug文件
│   ├── example/                     // 解析proto的例子
│   ├── user_ops/                    // 一个自定义op的例子
│   ├── public/                      // 头文件：session, version
│   ├── protobuf/                    // 若干.proto文件
│   ├── lib/                         // 公共基础库，如gif、gtl(google模板库)、hash、histogram等
│   ├── platform/                    // 系统接口文件，如cuda, file, env等
│   ├── framework/                   // 基础功能模块，如log, memory, tensor, op_def等
│   ├── ops/                         // 基本ops运算，ops梯度运算，io相关的ops，控制流和数据流操作
│   ├── kernels/                     // 核心Op，如matmul, conv2d, argmax, batch_norm等
│   ├── graph/                       // 计算流图相关操作，如construct, partition, optimize, execute等
│   ├── common_runtime/              // 公共运行库，包含session, graph, device, memory等
│   ├── distributed_runtime/         // 分布式执行模块，如rpc session, rpc master, rpc worker, graph manager
└───└── util/                        // 一些工具文件
```