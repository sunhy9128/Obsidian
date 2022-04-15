#originPython 
> zip()是Python的一个内建函数，在官方的文档中，它的描述是这样的：Make an iterator that aggregates elements from each of the iterables.Returns an iterator of tuples, where the i-th tuple contains the i-th element from each ...

> 这么描述显然有些抽象，让我们直接观察一下它的运行结果：
> 
> ```
> >>> a = ['a', 'b', 'c', 'd']
> >>> b = ['1', '2', '3', '4']
> >>> list(zip(a, b))
> [('a', '1'), ('b', '2'), ('c', '3'), ('d', '4')]
> ```
> 
> 很明显，对于我们的两个`list`，`a`和`b`，`list(zip(a, b))`生成了一个列表。在这个列表中，每个元素是一个`tuple`；对于第`i`个元组，它其中的内容是`(a[i-1], b[i-1])`。这样的操作，与压缩软件的 “压缩” 十分接近。如果我们继续在`zip()`中加入更多的参数，比如`zip(a, b, c, d)`，那么在将它转换成`list`之后，结果当然就是`[(a[0], b[0], c[0], d[0]), (a[1], b[1], c[1], d[1]), ..., (a[n-1], b[n-1], c[n-1], d[n-1])]`。
> 
> 事实上，在 Python 3 中，为了节省空间，`zip()`返回的是一个`tuple`的迭代器，这也是我们为什么要调用`list()`将它强制转换成`list`的原因。不过，Python 2 中，它直接返回的就是一个列表了。