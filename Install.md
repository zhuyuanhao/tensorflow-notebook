参考：https://www.tensorflow.org/install/

使用包管理器安装的方式参考上面的文档，这里记录源码安装时的步骤。

# 源码安装
参考：https://www.tensorflow.org/install/install_sources

1. 下载tf代码
```
$ git clone https://github.com/tensorflow/tensorflow
$ cd tensorflow
$ git checkout r1.0
```
2. 安装bazel
  
  安装说明： https://bazel.build/versions/master/docs/install.html#compiling-from-source
```
# 下载包 bazel-<VERSION>-dist.zip
# https://github.com/bazelbuild/bazel/releases
$ wget https://github.com/bazelbuild/bazel/releases/download/0.4.4/bazel-0.4.4-dist.zip
$ unzip bazel-0.4.4-dist.zip -d bazel-0.4.4
$ cd bazel-0.4.4
$ PATH=/mnt/lustre/share/jdk1.8.0_91/bin/:$PATH bash ./compile.sh
$ cp output/bazel ../../env/bin/
```
3. 安装python及依赖包
```
$ wget https://www.python.org/ftp/python/2.7.13/Python-2.7.13.tgz
$ tar xzvf Python-2.7.x.tgz
$ cd Python-2.7.x/
$ ./configure --enable-shared --prefix=/my/path/to/python-2.7
$ make
$ make install
```
设置Python环境变量：
```
export PATH=/my/path/to/python-2.7/bin:$PATH
export LD_LIBRARY_PATH= /my/path/to/python-2.7/lib:$LD_LIBRARY_PAT‌​H
```
安装setuptools：
```
$ wget https://pypi.python.org/packages/03/79/e56106d77d3d2ae607ba1e1d7f8c3ed61880f7decba1f253feb714b7eafe/setuptools-34.3.1.zip
$ tar xzvf setuptools-x.x.x.tar.gz
$ cd setuptools-x.x.x
$ python setup.py install          # 安装时可能提示许多依赖包不存在，按照安装setuptools的方式安装即可
```
安装pip：
```
$ wget https://pypi.python.org/packages/11/b6/abcb525026a4be042b486df43905d6893fb04f05aac21c32c638e939e447/pip-9.0.1.tar.gz
$ tar xzvf pip-9.0.1.tar.gz
$ cd pip-9.0.1
$ python setup.py build
$ python setup.py install
```
安装依赖包：
```
$ pip install wheel numpy
```
4. 安装CUDA和CUDNN
https://developer.nvidia.com/cuda-toolkit-archive
下载特定版本CUDA
根据合适参数安装CUDA
```
$ bash cuda_7.5.18_linux.run
```
解压CUDNN并移动到CUDA目录下
```
$ mv cudnn-5.0/include/cudnn.h ../env/cuda-7.5/include/
$ mv cudnn-5.0/lib64/libcudnn* ../env/cuda-7.5/lib64/
```
5. 设置环境变量
```bash
DL_HOME=/mnt/lustre/share/dlenv/tensorflow1.0/env
export PATH=$DL_HOME/bin:$DL_HOME/cuda-7.5/bin:$DL_HOME/python-2.7/bin:$PATH
export LD_LIBRARY_PATH=$DL_HOME/python-2.7/lib:$DL_HOME/cuda-7.5/lib64:$LD_LIBRARY_PATH
# 有些时候没有找到java包，添加参数
export JAVA_HOME=/mnt/lustre/share/jdk1.8.0_91/
export PATH=$JAVA_HOME/bin:$PATH
```
6. 编译tensorflow
$ cd tensorflow
$ ./configure          # 需要配置很多项，注意cudnn5.0的版本选项为5，不是5.0
$ bazel build --config=opt --config=cuda //tensorflow/tools/pip_package:build_pip_package
$ bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg

7. 安装tensorflow
$ pip install /tmp/tensorflow_pkg/tensorflow-1.0.1-py2-none-any.whl

8. 验证安装
$ python
>>> import tensorflow as tf
>>> hello = tf.constant('Hello, TensorFlow!')
>>> sess = tf.Session()
>>> print(sess.run(hello))

