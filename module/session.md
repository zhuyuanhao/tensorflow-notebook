Tensorflow依赖于一个高效的C++后端来进行计算。与后端的这个连接叫做session。Session控制着tf运行时的状态，构建时如果没有传入任何参数将启动默认图。使用`session.run(tensor)`表示取回当前图的tensor的结果。session分配相关的Op到CPU或GPU上执行，这些方法执行后，将产生的tensor返回。在Python中，返回的tensor是numpy.ndarray对象；在C/C++中, 返回的 tensor是tensorflow::Tensor实例。
  InteractiveSession可以更加灵活地构建你的代码。它能让你在运行图的时候，插入一些计算图，这些计算图是由某些操作(operations)构成的。

