---
layout: post
title: math.inf and math.nan
subtitle: Two math concepts from the Python math module
---

This post is about two mathematical concepts. They're not numbers as such, but are helpful in numerical computations.

The first of these I'd like to discuss here nan, which stands for "not a number". This tool originated in computer science and is used to distinguish between numerical values and all other values. 

`nan` has been available since Python was first created, but has experienced a few changes as new versions have been introduced.

The first way to create a `nan` in Python was by creating a special kind of float using the `'nan'` string as an input. 

```python
 Nan = float('nan')
```

Note that the resulting object is of type float.

```python
>>> type(nan)
<class 'float'>
```

Infinity is another mathematical concept that is not a number. Like with nan, the original way to work with in early versions of Python was creating a float.

```python
 Inf = float('inf')
```

Note that is was also possible to create negative infinity in the same way:

```python
 Neg_inf = float('-inf')
```

Starting with Python 3.5, it became possible to use defined constants from the math module for both nan and infinity. The first step to using these is always to import the math module.

```python
import math
```

We can then create variables set to nan or infinity using the standard `packagename.value` syntax like so:

```python
Pos_inf = math.inf
Neg_inf = -math.inf
Not_a_num = math.nan
```

Note the use of capital letters when working with constants.

The math module comes with built in methods to check for infinity or finiteness. 

```python
>>> math.isinf(math.inf)
True
>>> math.isfinite(math.inf)
False
````

Similarly, the math module allows you to check for nan with a built in method:

```python
>>> math.isnan(math.nan)
True
````

nan is never equal to anything, not even itself. Remembering this can help understand behavior that can otherwise be very puzzling:

```python
>>> nan = math.nan
>>> math.nan == nan
False
```
