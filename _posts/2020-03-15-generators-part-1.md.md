---
layout: post
title: Generators part 1
subtitle: How generators help you write more pythonic code
---

# Generators in Python - part 1

### Understanding what generators are and how they're helpful

There's a certain number of topics that are covered in every course or tutorial on Python. Variables, for and while loops, comparative structures, functions. These are the main building blocks to get a foothold in the language and in coding generally. 

Generators fall outside of this commonly-agreed base knowledge. That's a pity because they're actually pretty powerful. They allow us to work with very large or even infinite data in a way that can be reconciled with limited memory resources available.

One of the reasons they don't get the coverage they should is probably because explaining what they are and how they work can be tricky without going into how other python structures work under the hood.

In this tutorial we'll try to explain generators by starting from the beginning and giving as much context as possible.

In that spirit, let's start with something that's definitely _not_ a generator:


```python
number_of_dwarves = 7
```

Here we have an integer saved in a variable. As this is just a single integer, we wouldn't expect to be able to iterate over it (what is there to iterate over?). But let's try anyway and then take a closer look at the error message we get.


```python
for dwarf in number_of_dwarves:
    print(dwarf)
```


    ---------------------------------------------------------------------------

    TypeError                                 Traceback (most recent call last)

    <ipython-input-2-87c4989018e9> in <module>
    ----> 1 for dwarf in number_of_dwarves:
          2     print(dwarf)


    TypeError: 'int' object is not iterable


Python responds with a `TypeError`, and tells us the int object isn't iterable.

This implies that when something _is_ iterable, it is of a type that allows iteration. We can illustrate what an iterator is and how it works with an example which is probably more familiar to you: iterating over a list.

Let's start by creating a list:


```python
dwarves = ['Doc', 'Grumpy', 'Happy', 'Sleepy', 'Bashful', 'Sneezy', 'Dopey']

```

Now let's try iterating over it in the usual way using a _for_ loop:


```python
for dwarf in dwarves:
    print(dwarf)
```

    Doc
    Grumpy
    Happy
    Sleepy
    Bashful
    Sneezy
    Dopey


Evidently, that worked. The question is why and how.

Let's peek under the hood. What kind of object is `dwarves`?


```python
type(dwarves)
```




    list



If you're a bit rusty on object-oriented Python, then what comes next might require a bit of explaining. The above code snippet told us that `dwarves` belongs to a class of object called _list_. Each member of a class shares certain attributes with the other members of the same class. Among other things, they share certain abilities called _methods_. Python classes are a topic unto themselves, but for now this is all you need to keep in mind. 

Python allows us list an object's methods using the `dir()` function.


```python
dir(list)
```




    ['__add__',
     '__class__',
     '__contains__',
     '__delattr__',
     '__delitem__',
     '__dir__',
     '__doc__',
     '__eq__',
     '__format__',
     '__ge__',
     '__getattribute__',
     '__getitem__',
     '__gt__',
     '__hash__',
     '__iadd__',
     '__imul__',
     '__init__',
     '__init_subclass__',
     '__iter__',
     '__le__',
     '__len__',
     '__lt__',
     '__mul__',
     '__ne__',
     '__new__',
     '__reduce__',
     '__reduce_ex__',
     '__repr__',
     '__reversed__',
     '__rmul__',
     '__setattr__',
     '__setitem__',
     '__sizeof__',
     '__str__',
     '__subclasshook__',
     'append',
     'clear',
     'copy',
     'count',
     'extend',
     'index',
     'insert',
     'pop',
     'remove',
     'reverse',
     'sort']



You'll notice some method names are preceded and followed by double underscores (dunders). This naming convention indicates methods that are used by Python to operate certain basic features of the language.

The one we're particularly interested in here is `__iter__`. We can call this on an object, such as our `dwarves` list.

Let's see what happens to the `dwarves` list when we call this method on it.


```python
dwarf_iterator = iter(dwarves)

type(dwarf_iterator)
```




    list_iterator



Calling the `__iter__` method on our list created an object of the `list_iterator` type. 

Iterators are objects that can be iterated upon, that is to say they support the returning of each of their components one by one. In the case of lists, this means that the list iterator supports behavior in which Python return one list item at a time. This method is baked into lists and is what makes for loops possible.

When we say that objects such as lists, dictionaries, tuples or strings can be iterated upon, what we're actually saying is that it's possible to get an iterator from them.

So how does the iterator actually work? It works its magic thanks to two things: the `next()` method and the `StopIteration` exception.

Let's look at each of these in more detail.


```python
next(dwarf_iterator)
```




    'Doc'



Calling `next()` causes the iterator object return the zeroeth item in the list.


```python
next(dwarf_iterator)
```




    'Grumpy'



Calling `next()` again caused the next item in the list to be returned.

Think about this for a moment. 

We normally call a function, potentially with some arguments passed to it. The function runs and returns a result. The return statement that causes the function to stop running, also causes all variables that are in the scope of that function to evaporate. They're not persisted, so that if we call the function again with the same argument it'll start off as if we had never run it before and we could expect it to return the same result.

That's not what happened here. When we called `next()` a second time the `dwarf_iterator` object didn't return the very first item in the list again, but rather returned the second one. It remembered where is was in the list the same way you migh remember where you left off while reading a book next time you pick it up. 
This property is called _state_. We say that `dwarf_iterator` has kept state from the first time it was used, and so it remembered what the next item in line would be if it was called upon with `next()` again.

Let's see what happens if we call `next()` on `dwarf_iterator` more times than there are elements in the list:



```python
next(dwarf_iterator)
next(dwarf_iterator)
next(dwarf_iterator)
next(dwarf_iterator)
next(dwarf_iterator)
next(dwarf_iterator)
```


    ---------------------------------------------------------------------------

    StopIteration                             Traceback (most recent call last)

    <ipython-input-11-0b231db5947f> in <module>
          4 next(dwarf_iterator)
          5 next(dwarf_iterator)
    ----> 6 next(dwarf_iterator)
    

    StopIteration: 


This results in a `StopIteration` exception.

What we have done here is demonstrate what is happening under the hood whenever a for loop is used to iterate over the elements in a list. The `StopIteration` exception may be unfamiliar for a lot of readers but that's because when we're using a for loop to iterate over a list, this exception is caught and calls a `break`.

We have laid the foundations for talking about generators here. We have learned that calling a for loop on a list results in an iterator object being created transparently to the user. This object allows us to get successive items from the iterable, while remembering what item we were on between calls. When more items are called than there are items in the list, a `StopIteration` exception results, but is caught and instead a `break` statement is run.

Iterables are called iterable because they can be turned into iterators using things like for loops.

Let's move on to generators, then. Consider the following function:


```python
def snow_white():
    yield 'Doc'
    yield 'Grumpy'
    yield 'Happy'
    yield 'Sleepy'
    yield 'Bashful'
    yield 'Sneezy'
    yield 'Dopey'
```

This may look like a strange sort of function. It has no return statements. Instead it has a succession of yield statements. 

Let's create an instance of this and store it in a variable so that we can take a closer look at it.


```python
apple = snow_white()
```

Now let's check the type of this object.


```python
type(apple)
```




    generator



So, this is a generator object. Generators are iterators, and this implies that we should be able to run the `next()` function on them.


```python
next(apple)
```




    'Doc'




```python
next(apple)
```




    'Grumpy'



Let's compare how this generator function is working to what we would expect from a normal function. 

With a normal function we would have a `return` statement that marks the exit point from that function. Once we exit the function at that point, all variables and anything else about the function are lost. Another way of thinking about this is to say the function is reset, so that the next time we call it, it will start at the beginning and only run until it encounters a first return statement.

In this case we defined a function with the usual `def` keyword, but this time instead of using `return` we used `yield`. 

This had two consequences. Firstly it turned this into a generator function, as we saw with the `type()` command. Secondly, each time we called `next()` on this function, it not only returned a value, but it returned _the next value_. This implies the function was remembering where it left off, otherwise known as keeping _state_.

Next, let's see what happens if we keep calling `next()` on `apple` until we run out of items in our function.


```python
next(apple)
next(apple)
next(apple)
next(apple)
next(apple)
next(apple)
```


    ---------------------------------------------------------------------------

    StopIteration                             Traceback (most recent call last)

    <ipython-input-23-36f44c84de87> in <module>
    ----> 1 next(apple)
          2 next(apple)
          3 next(apple)
          4 next(apple)
          5 next(apple)


    StopIteration: 


This results in a `StopIteration` exception, just like when we exhausted the iterator we created above using the `dwarves` list. 

What we have just demonstrated, then, is that a generator function allows to create our own iterators that have state, and raise a `StopIteration` exception when we exhaust them.

In future posts we'll dive deeper into generators. Among other things we'll look at how their use affects performance, and when and where to use them.


```python

```
