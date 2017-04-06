TensorFlow Python库有一个默认图 (default graph)，如果op没有指定图，则会被添加到默认图。

Graph表示一个完整的计算任务。Graph是由节点所表示的操作Op组成的一个图状结构。Graph需要在Session中才能执行。