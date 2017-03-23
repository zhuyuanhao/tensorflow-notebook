参考：https://www.tensorflow.org/install/

# 安装Nvidia驱动及CUDA、CuDNN
默认的tf包需要CUDA8.0和CuDNN5.1
Nvidia安装文档：http://docs.nvidia.com/cuda/cuda-installation-guide-linux/

## 1. 安装Nvidia驱动
驱动不建议从run.sh文件安装，容易引起图形界面依赖问题，导致安装后无法登陆。
```bash
# 1. 清理老版本或者安装失败的驱动，这一步可以根据情况使用
$ sudo apt-get autoremove --purge nvidia-*
$ sudo reboot

# 2. 在终端安装
# 先log out，按Ctrl+Alt+F1进入文本模式（命令行界面），登录账户
$ sudo service lightdm stop
$ sudo apt-get update
$ sudo apt-get install nvidia-current    # 或者nvidia-375
$ sudo service lightdm start

# 如果提示有进程在使用nvidia文件
$ lsof /dev/nvidia*        # 检查哪个进程在使用，kill这些进程
```

## 2. 安装CUDA及CuDNN
在官网下载相应的CUDA8.0及CuDNN5.1包，直接运行安装。
* CUDA8.0 默认安装在`/usr/local/cuda-8.0/`下，且软连接到`/usr/local/cuda/`
* CuDNN5.1 解压后包含一个头文件和若干库文件，头文件拷贝到`/usr/local/cuda-8.0/include/`下，库文件拷贝到`/usr/local/cuda-8.0/lib64/`下

添加环境变量
```bash
export CUDA_HOME=/usr/local/cuda
export PATH=/usr/local/cuda/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH
```

# 安装包安装
```
# 可以安装cuda性能测试工具
$ sudo apt-get install libcupti-dev

# pip安装ensorflow
$ pip install tensorflow      # Python 2.7; CPU support (no GPU support)
$ pip3 install tensorflow     # Python 3.n; CPU support (no GPU support)
$ pip install tensorflow-gpu  # Python 2.7; GPU support
$ pip3 install tensorflow-gpu # Python 3.n; GPU support

# 如果上面安装失败，可以手动指定
$ sudo pip  install --upgrade TF_PYTHON_URL   # Python 2.7
$ sudo pip3 install --upgrade TF_PYTHON_URL   # Python 3.N 
# TF_PYTHON_URL在：
# https://www.tensorflow.org/install/install_linux#TF_PYTHON_URL
```

# 源码安装
参考：https://www.tensorflow.org/install/install_sources

## 1. 下载tf代码
```
$ git clone https://github.com/tensorflow/tensorflow
$ cd tensorflow
$ git checkout r1.0
```

## 2. 安装bazel
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

## 3. 安装python及依赖包
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

## 4. 安装CUDA和CUDNN
在 https://developer.nvidia.com/cuda-toolkit-archive
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

## 5. 设置环境变量
```bash
DL_HOME=/mnt/lustre/share/dlenv/tensorflow1.0/env
export PATH=$DL_HOME/bin:$DL_HOME/cuda-7.5/bin:$DL_HOME/python-2.7/bin:$PATH
export LD_LIBRARY_PATH=$DL_HOME/python-2.7/lib:$DL_HOME/cuda-7.5/lib64:$LD_LIBRARY_PATH
# 有些时候没有找到java包，添加参数
export JAVA_HOME=/mnt/lustre/share/jdk1.8.0_91/
export PATH=$JAVA_HOME/bin:$PATH
```
一般添加到一个文件中，比如`env.source`。以后每次只要执行`source env.source`就能载入tensorflow环境了。

## 6. 编译tensorflow
```
$ cd tensorflow
$ ./configure          # 需要配置很多项，注意cudnn5.0的版本选项为5，不是5.0
$ bazel build --config=opt --config=cuda //tensorflow/tools/pip_package:build_pip_package
$ bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg
```

## 7. 安装tensorflow
```bash
$ pip install /tmp/tensorflow_pkg/tensorflow-1.0.1-py2-none-any.whl
```

## 8. 验证安装
```python
$ python
>>> import tensorflow as tf
>>> hello = tf.constant('Hello, TensorFlow!')
>>> sess = tf.Session()
>>> print(sess.run(hello))
```
