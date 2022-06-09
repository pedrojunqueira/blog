---
title: "Python property class and decorator"
date: 2020-09-16T06:03:39+09:30
draft: false
toc: false
images:
tags:
  - python
---

About 2 months ago I considered my self an intermediate python user.

This level of knowledge was enough to work on pandas data transformation and use most of the data science API.

However, when it comes to software engineering and developing production, tested code I realized that there was a lot to learn about programming concepts and how that would work with python.

 My intermediate knowledge and experience already gave me lots of power but when I started to dig into source code and also at work when I had to browse to some large code base I felt that I did not know lots of things in python and had to get my s*** together and become a proficient advanced and fluent python user.

I am talking about advanced concepts like property, decorator, inheritance, mro, getters, setters and the other OOP paradigm like interfaces and encapsulation that was necessary to up my game.

In this post I will give an introduction of the property class and property decorator.

When someone start learning about classes one of the first concepts we learn is class attribute and instance attribute which is not the scope of this post. I assume that this is already a clear concept. If not you can refresh [here](https://www.python-course.eu/python3_class_and_instance_attributes.php)

The property class helps to manage class attribute where you can control the mutability of an instance property and also set some conditions you want an instance attribute to have.

A classical example which is widely used in many example is temperature.

Say was want to create a class that manages objects temperature and convert between Celsius and Fahrenheit.

The absolute zero temperature is -273 Celsius and you would not want to allow anyone to set a temperature below this value.

A way to manage this is via property.

There are two ways you can do it, i.e. using the `property` class itself or the `@property` decorator.

The documentation doc string in python is very good actually. Lets print it.

`help(property)`

Here is an exert of it with a quick example to get you started.

```python


class C(object):


  def getx(self):
    return self._x

  def setx(self, value):
    self._x = value

  def delx(self): 
    del self._x

  x = property(getx, setx, delx, "I'm the 'x' property.")

```

The property class take 4 arguments and returns the property attribute

`property(fget=None, fset=None, fdel=None , doc=None) -> property attribute`

`fget` is a function to be used for getting an attribute value, and likewise

`fset` is a function for setting

and `fdel` a function for deleting, 
an attribute. 

The decorator alternative makes it simpler

``` python
class C(object):

@property
def x(self):
  "I am the 'x' property."
  return self._x 

@x.setter
def x(self, value):
  self._x = value

@x.deleter 
def x(self):
  del self._x

```

This where just template examples. Lets now write our temperature class that I took from [programviz](https://www.programiz.com/python-programming/property)

```python

# Using @property decorator
class Celsius:
    def __init__(self, temperature=0):
        self.temperature = temperature

    def to_fahrenheit(self):
        return (self.temperature * 1.8) + 32

    @property
    def temperature(self):
        print("Getting value...")
        return self._temperature

    @temperature.setter
    def temperature(self, value):
        print("Setting value...")
        if value < -273.15:
            raise ValueError("Temperature below -273 is not possible")
        self._temperature = value


# create an object
human = Celsius(37)

print(human.temperature) 

print(human.to_fahrenheit())

coldest_thing = Celsius(-300) # this is going to raise an exception as it is below 273.15

# output of the program (commented out)

> Setting value...
> Getting value...
> 37
> Getting value...
> 98.60000000000001
> Setting value...


Traceback (most recent call last):
  File "temperature_.py", line 28, in <module>
    coldest_thing = Celsius(-300)
  File "temperature_.py", line 3, in __init__
    self.temperature = temperature
  File "temperature_.py", line 17, in temperature
    raise ValueError("Temperature below -273 is not possible")
ValueError: Temperature below -273 is not possible

```

Note that when an instance of class initialize it firsts sets the value then it gets.

I got confused with this as sometimes I used the property to set an immutable property but on the example it made it mutable with the `@temperature.setter` decorator.

If you pay attention to the example on the documentation it does not initiate with a default value and the `x`. So let's implement it as in the help example

```python

class C(object):

    @property 
    def x(self):  
        "I am the 'x' property."
        return self._x 

    @x.setter
    def x(self, value):
        self._x = value 

    @x.deleter 
    def x(self):
        del self._x

c = C(3)

print(c.x)

# output

Traceback (most recent call last):
  File "c.py", line 19, in <module>
    print(c.x)
  File "c.py", line 6, in x
    return self._x 
AttributeError: 'C' object has no attribute '_x'
```

So if we add the `__init__`

```python
class C(object):

    def __init__(self, x=0):
        self.x = x

    @property 
    def x(self):  
        "I am the 'x' property."
        return self._x 

    @x.setter
    def x(self, value):
        self._x = value 

    @x.deleter 
    def x(self):
        del self._x


c = C(3)

print(c.x)

c.x = 23

print(c.x)

# output

>0
>23
```
Actually what happened is that when it `__init__` the instance it set the value to the default value.

If you want to make it immutable like having a value that does not change just only implement set method.

Let's demonstrate it

```python

class C(object):

    def __init__(self):
        self._x = 42

    @property 
    def x(self):  
        "I am the 'x' property."
        return self._x 

c = C()

print(c.x)

c.x = 23

# output

>42

Traceback (most recent call last):
  File "c.py", line 24, in <module>
    c.x = 23
AttributeError: can't set attribute
```

Note that this time on the `__init__` I set the value `42` to the `_x` underscore version to set it to a value at initialization and never allow it to change.

I think this is it for property. 

If you inspect source code in GitHub you will see it a lot and now you know what is going on when you see it.


