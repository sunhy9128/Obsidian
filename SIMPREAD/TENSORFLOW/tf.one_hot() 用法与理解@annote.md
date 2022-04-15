# [tf.one_hot() 用法与理解](https://blog.csdn.net/fang_chuan/article/details/88559930?spm=1001.2101.3001.6650.5&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-5.pc_relevant_aa&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-5.pc_relevant_aa&utm_relevant_index=6) 
> tf.one_hot()
- 标签
> #Tensorflow   
> 建立时间：2022年03月18日 16:31:14
>

***
### Highlight

- 备注
  
- 时间
  2022年03月18日 16:31:14
- 标注
  > ```
  > tf.one_hot(
  >     indices,
  >     depth,
  >     on_value=None,
  >     off_value=None,
  >     axis=None,
  >     dtype=None,
  >     name=None
  > )
  > Returns a one-hot tensor(返回一个one_hot张量).
  > ```

- 标签
  

***
- 备注
  
- 时间
  2022年03月18日 16:31:59
- 标注
  > ```python
  > depth = 3 tf.one_hot(indices, depth)
  > # output: [3 x 3] # [[1., 0., 0.], # [0., 1., 0.], # [0., 0., 1.]] 
  > indices = [0, 2, -1, 1] 
  > # 输入数据(是个向量)的需要编码的索引是[0,2,-1,1] 
  > depth = 3 
  > tf.one_hot(indices, depth, on_value=5.0, off_value=0.0, axis=-1) 
  > # output: [4 x 3] # [[5.0, 0.0, 0.0],  # one_hot(0) 对位置0处的数据进行one_hot编码 
  > # [0.0, 0.0, 5.0], # one_hot(2) 对位置2处的数据进行one_hot编码 
  > # [0.0, 0.0, 0.0], # one_hot(-1) 对位置-1处的数据进行one_hot编码 
  > # [0.0, 5.0, 0.0]] # one_hot(1) 对位置1处的数据进行one_hot编码
  >``` 

- 标签
  

***
- 备注
  
- 时间
  2022年03月18日 16:32:01
- 标注
  > ```
  > indices = [0, 1, 2]  #输入数据(是个向量)需要编码的索引是[0,1,2]
  > depth = 3
  > tf.one_hot(indices, depth)  # output: [3 x 3]
  > # [[1., 0., 0.],
  > #  [0., 1., 0.],
  > #  [0., 0., 1.]]
  >  
  > indices = [0, 2, -1, 1]  #输入数据(是个向量)的需要编码的索引是[0,2,-1,1]
  > depth = 3
  > tf.one_hot(indices, depth,
  >            on_value=5.0, off_value=0.0,
  >            axis=-1)  # output: [4 x 3]
  > # [[5.0, 0.0, 0.0],  # one_hot(0)  对位置0处的数据进行one_hot编码
  > #  [0.0, 0.0, 5.0],  # one_hot(2)  对位置2处的数据进行one_hot编码
  > #  [0.0, 0.0, 0.0],  # one_hot(-1) 对位置-1处的数据进行one_hot编码
  > #  [0.0, 5.0, 0.0]]  # one_hot(1)  对位置1处的数据进行one_hot编码
  >  
  > indices = [[0, 2], [1, -1]]   #输入数据是个矩阵
  > depth = 3
  > tf.one_hot(indices, depth,
  >            on_value=1.0, off_value=0.0,
  >            axis=-1)  # output: [2 x 2 x 3]
  > # [[[1.0, 0.0, 0.0],   # one_hot(0)  对位置(0,0)处的数据进行one_hot编码
  > #   [0.0, 0.0, 1.0]],  # one_hot(2)  对位置(0,2)处的数据进行one_hot编码
  > #  [[0.0, 1.0, 0.0],   # one_hot(1)  对位置(1,1)处的数据进行one_hot编码
  > #   [0.0, 0.0, 0.0]]]  # one_hot(-1) 对位置(1,-1)处的数据进行one_hot编码
  > ```

- 标签
  

***

***
**one_hot()函数只会编码>=0的label，负数位置置为0；depth为索引的最大值+1，即label数组内最大的数字为depth-1，超过置0**
```python
tf.one_hot(indices=[-1, 0, 3, 4], depth=4)
# <tf.Tensor: shape=(4, 4), dtype=float32, 
numpy= array([[0., 0., 0., 0.],  
			  [1., 0., 0., 0.],  
			  [0., 0., 0., 1.],  
			  [0., 0., 0., 0.]], dtype=float32)>
```

**axis 参数代表位置方向，axis=1或者axis=-1，每个indice的onehot纵向查看，axis=0 横向查看，默认 -1横向**
```python
tf.one_hot(indices=[2, 3], depth=4, axis = -1)
# <tf.Tensor: shape=(2, 4), dtype=float32, 
numpy= array([[0., 0., 1., 0.],  
			  [0., 0., 0., 1.]], dtype=float32)>
tf.one_hot(indices=[2, 3], depth=4, axis = 0)
# <tf.Tensor: shape=(4, 2), dtype=float32, 
numpy= array([[0., 0.],  
			  [0., 0.],  
			  [1., 0.],  
			  [0., 1.]], dtype=float32)>
```