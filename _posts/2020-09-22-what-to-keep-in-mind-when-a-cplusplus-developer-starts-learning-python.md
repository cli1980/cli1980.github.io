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
## Object copy must be done explicitly
In C++ or C, object copy happens all the time behind the scene, through the assignment operator('='), return value or arguments that have been passed to a function. However Python doesn't support object copy directly and either the assignment operator, return value or parameter passing only takes the reference of an object. This needs to be kept in mind especially when objects are passed to a function, as long as they're not the immutables such as integer, any change made on them inside the function is actually on original objects sitting outside the function. 

Therefore the object ownership in python must be very clear in Python.
```python
def append(l, element):
    l.append(element)
    return l

l1 = ['hello']
l2 = append(l1, 'world')
print(l1)
print(l2)
```
The goal of above code is to get a new list by appending a string to the given list, and the output shows:
```
['hello', 'world']
['hello', 'world']
``` 
Now we see, though we got the list we wanted, the original one has also been modified inside the _append_ function accidentally, and to fix the issue we have to create a new list object using the constructor of the built-in **list** class:
```python
def append(l, element):
    l2 = list(l)
    l2.append(element)
    return l2

l1 = ['hello']
l2 = append(l1, 'world')
print(l1)
print(l2)
```
```
['hello']
['hello', 'world']
```
To make a copy of any object in Python, you have to rely on the built-in **copy** module, which supports both shallow copy and deep copy.

The _copy.copy()_ does the shallow copy, which creates a new object and inserts references there to the objects found inside the original one. Thus the new copy looks very similar to the original one, both have the same property set inside, however the items(if they're also objects) from two independent objects point to the same thing.

Let's modify the above code example a little bit by changing the elements in the list from immutable string to character list:
```python
def append(l, element):
    r = list(l)
    r.append(element)
    return r

l1 =[['h', 'e', 'l', 'l', 'o']]
l2 = append(l1, ['w', 'o', 'r', 'l', 'd'])
l1[0][0] = 'H'
print(l1)
print(l2)
```
```
[['H', 'e', 'l', 'l', 'o']]
[['H', 'e', 'l', 'l', 'o'], ['w', 'o', 'r', 'l', 'd']]
```
Here we're creating the new object inside _append_ function through built-in **list** class which does a shallow copy essentially, and the result is although we got a new list l2 from l1 after calling _append_ the items there are being shared by both. To fix the issue _copy.deepcopy()_ has to be used:
```python
import copy
def append(l, element):
    r = copy.deepcopy(l)
    r.append(element)
    return r

l1 =[['h', 'e', 'l', 'l', 'o']]
l2 = append(l1, ['w', 'o', 'r', 'l', 'd'])
l1[0][0] = 'H'
print(l1)
print(l2)
```
```
[['H', 'e', 'l', 'l', 'o']]
[['h', 'e', 'l', 'l', 'o'], ['w', 'o', 'r', 'l', 'd']]
```
A deep copy constructs a new object and then recursively inserts copies of the objects (rather than reference) found inside the original one. The brute force approach may worries some C++ developers, and it is worrisome to some extent due to the potential recursive loop or other comprehensive scenarios. Fortunately this approach can be overridden for user defined classes through customized ```__deepcopy__``` functions.
```python
import copy

class Bundle:
    def __init__(self, name):
        self.content = {"name": name}

    def __deepcopy__(self, memo):
        return Bundle("clone")

b1 = Bundle("first")
b2 = copy.deepcopy(b1)
b1.content["name"] = "last"

print(b1.content["name"])
print(b2.content["name"])
```
The above code overrides the deepcopy behavior of Bundle class and the the object constructed from deepcopy gets "clone" as the name in its content:
```
last
clone
```
[^1]: Advanced C++ uses std:function<R(args)> to wrap functions as objects, allowing developers to use functions as object to some extent.