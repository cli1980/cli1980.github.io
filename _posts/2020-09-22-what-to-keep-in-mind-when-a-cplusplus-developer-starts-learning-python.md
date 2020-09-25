---
layout: single
title: "What to keep in mind when a C++ developer starts learning python"
categories: Note
tags:
  - C++
  - python
published: true
---
## Both functions and classes are first citizens
When we say an entity is considered as "first citizen" of a programming language, that usually means it can be passed directly to other functions as arguments, be returned from other functions, be assigned to variables and be put to data structures in the same way as objects are treated, in some cases it can even own properties like how the objects does.

Though this is a common feature of many advanced languages, especially for those who claims to support functional programming, it still looks kind of weird when a C++ developer first tries Python. In the world of C and C++, it is very popular to use pointers (or references in other words)[^1] to store, transfer and return functions, and a function is nothing but a piece of code that has been wrapped up for the purpose of reuse; as for the class, which categorizes the objects, we can't even find its track from the runtime environment in C++ world (though it does exist somewhere). 

These all changed in the Python world, both functions and classes there are treated just as objects.

Below is an example of how "classes" are treated like objects:
```python
class fruit:
    edible = True
    pass

class metal:
    edible = False
    pass

for c in [fruit, metal]:
    # '__name__' is a special attribute of a class while 
    # 'edible' is another one defined explicitly
    msg = "edible" if c.edible else "not edible"
    print(c.__name__ + " is " + msg)
```
Output:
```
fruit is edible
metal is not edible
```
The other example is about functions, which can be treated like objects too:
```python
def cook():
    return "food"

def wash():
    return "car"
    
for f in [cook, wash]:
    print("calling " + f.__name__ + " on : " + f())
```
Output:
```
calling cook on: food
calling wash on: car
```

## The structure of an object is NOT static
When we say the structure of an object is fixed, usually we mean the properties (or attributes in other words) that belongs to the object are always predefined somewhere. The first thing a C or C++ developer learnt when he or she jump to the language is that anything you'd like to use in your code has to be **defined**, and once it's defined there is no way to change its structure at all, e.g. there is a structure(or class) defined as below:
```c++
struct complex
{
    float real;
    float image;
};
```
Then any instance of the complex struct can only have a property of "real" and the other property of "image" for ever, it is impossible for the instance to have other new properties unless a new structure is defined or inherited from the existing one. That's how we consider the structure being static, which is rooted to how a structure is managed in the C, C++ or any other lower level programming language, let's say, all the objects there are tightly bound to the memory overlay.

However, advanced languages have more than enough reasons not to associate memory offset to object properties, they use advanced data structures to manage it instead, for instance a map(or dictionary in other words). 

Python uses maps too, and properties of an object are all put to its built-in map ```__dict__```. We'll define a complex class in python as below:
```python
class complex:
    def __init__(self):
        self.real = 0.0
        self.image = 0.0
c = complex()
```
And to access the "real" part of a complex, following statements are equal:
```python
c.real = 1.5
c.__dict__["real"] = 1.5
```
As anyone can guess, the outcome would be that the structure of an object is no longer static, which means new property can be added to an object at any time even though it is "**defined**" somewhere from a class. For example, you can have a colorful complex number if that makes you happier:
```python
c.color = "red"
```
You can also have all the complex numbers be with color at some point in your code:
```python
complex.color = "red"
```
Though not explicit, removing an existing property from an object is possible too:
```python
del complex.color
```


[^1]: Advanced C++ uses std:function<R(args)> to wrap functions as objects, allowing developers to use functions as object to some extent.