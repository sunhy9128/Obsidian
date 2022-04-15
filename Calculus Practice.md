
# Calculus Practice
Date: 2022-02-25 10:29
Source: [Calculus Practice](http://d2l.ai/chapter_preliminaries/calculus.html)

1.  Plot the function $𝑦=𝑓(𝑥)=𝑥^3−\frac1𝑥$ and its tangent line when $𝑥=1$.
2.  Find the gradient of the function $𝑓(𝐱)=3𝑥^2+5𝑒^{𝑥_2}$.
3.  What is the gradient of the function $𝑓(𝐱)=‖𝐱‖_2$?
4.  Can you write out the chain rule for the case where $𝑢=𝑓(𝑥,𝑦,𝑧)$ and $𝑥=𝑥(𝑎,𝑏)$, $𝑦=𝑦(𝑎,𝑏)$, and $𝑧=𝑧(𝑎,𝑏)$?
#### Exercise 1

```python

import numpy as np
from IPython import display
import matplotlib.pyplot as plt
from fractions import Fraction


def f(x):
    return x**3 - 1/x
def numerical_lim(f, x, h):
    return (f(x + h) - f(x)) / h
def use_svg_display():  #@save
    """Use the svg format to display a plot in Jupyter."""
    display.set_matplotlib_formats('svg')
def set_figsize(figsize=(3.5, 2.5)):  #@save
    """Set the figure size for matplotlib."""
    use_svg_display()
#@save
def set_axes(axes, xlabel, ylabel, xlim, ylim, xscale, yscale, legend):
    """Set the axes for matplotlib."""
    plt.xlabel(xlabel)
    plt.ylabel(ylabel)
    plt.xscale(xscale)
    plt.yscale(yscale)
    plt.xlim(xlim)
    plt.ylim(ylim)
    if legend:
        plt.legend(legend)
    plt.show()
#@save
def plot(X, Y=None, xlabel=None, ylabel=None, legend=None, xlim=None,
         ylim=None, xscale='linear', yscale='linear',
         fmts=('-', 'm--', 'g-.', 'r:'), figsize=(3.5, 2.5), axes=None):
    """Plot data points."""
    if legend is None:
        legend = []

    set_figsize(figsize)

    # Return True if `X` (tensor or list) has 1 axis
    def has_one_axis(X):
        return (hasattr(X, "ndim") and X.ndim == 1 or isinstance(X, list)
                and not hasattr(X[0], "__len__"))

    if has_one_axis(X):
        X = [X]
    if Y is None:
        X, Y = [[]] * len(X), X
    elif has_one_axis(Y):
        Y = [Y]
    if len(X) != len(Y):
        X = X * len(Y)
    for x, y, fmt in zip(X, Y, fmts):
        if len(x):
            plt.plot(x, y, fmt)
        else:
            axes.plot(y, fmt)
    set_axes(axes, xlabel, ylabel, xlim, ylim, xscale, yscale, legend)
```
#### Exercise 2
![[Pasted image 20220225103957.png]]
#### Exercise 3
![[Pasted image 20220225104027.png]]
Explaination: ![](https://latex.codecogs.com/svg.latex?%7B%7C%7C%5Cmathbf%7Bx%7D%7C%7C_2=%5Csqrt%7B%5Cmathbf%7BX%7D%5E%7B%5Ctop%7D%7B%5Cmathbf%7BX%7D%7D%7D)
use the chain rule:
![[Pasted image 20220225104051.png]]
![[Pasted image 20220225104057.png]]
![[Pasted image 20220225104100.png]]
