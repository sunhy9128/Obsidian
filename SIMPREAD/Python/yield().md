#originPython

> 简单地讲，yield 的作用就是把一个函数变成一个 generator，带有 yield 的函数不再是一个普通函数，Python 解释器会将其视为一个 generator，调用 fab(5) 不会执行 fab 函数，而是返回一个 iterable 对象！在 for 循环执行时，每次循环都会执行 fab 函数内部的代码，执行到 yield b 时，fab 函数就返回一个迭代值，下次迭代时，代码从 yield b 的下一条语句继续执行，而函数的本地变量看起来和上次中断执行前是完全一样的，于是函数继续执行，直到再次遇到 yield。
```python
#!/usr/bin/python 
# -*- coding: UTF-8 -*- 
def fab(max): 
	n, a, b = 0, 0, 1 
	while n < max: 
		yield b # 使用 yield 
		# print b 
		a, b = b, a + b 
		n = n + 1
		
for n in fab(5): 
	print n
```
> 也可以手动调用 fab(5) 的 next() 方法（因为 fab(5) 是一个 generator 对象，该对象具有 next() 方法），这样我们就可以更清楚地看到 fab 的执行流程：

```shell
>>>f = fab(5) 
>>> f.next() 
1 
>>> f.next() 
1 
>>> f.next() 
2 
>>> f.next() 
3 
>>> f.next() 
5
>>> f.next() 
Traceback (most recent call last): File "<stdin>", line 1, in <module> 
StopIteration
```