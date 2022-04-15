#Tensorflow

> tf.slice(input_, begin, size, name=None)：按照指定的下标范围抽取连续区域的子集
> 
> tf.gather(params, indices, validate_indices=None, name=None)：按照指定的下标集合从 axis=0 中抽取子集，适合抽取不连续区域的子集
> 
> **输出：**
> 
> ```
> input = [[[1, 1, 1], [2, 2, 2]],
>    [[3, 3, 3], [4, 4, 4]],
>    [[5, 5, 5], [6, 6, 6]]]
> tf.slice(input, [1, 0, 0], [1, 1, 3]) // [[[3, 3, 3]]]
> tf.slice(input, [1, 0, 0], [1, 2, 3]) ==> [[[3, 3, 3],
>            [4, 4, 4]]]
> tf.slice(input, [1, 0, 0], [2, 1, 3]) // [[[3, 3, 3]],
>            [[5, 5, 5]]]
>             
> tf.gather(input, [0, 2]) // [[[1, 1, 1], [2, 2, 2]],
>         [[5, 5, 5], [6, 6, 6]]]
> ```
> 
> 假设我们要从 input 中抽取 [[[3, 3, 3]]]，这个输出在 inputaxis=0 的下标是 1，axis=1 的下标是 0，axis=2 的下标是 0-2，所以 begin=[1,0,0]，size=[1,1,3]。