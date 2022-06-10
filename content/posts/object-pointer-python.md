---
title: "Understand how objects behave in memory Python"
date: 2022-06-10T21:16:44+09:30
draft: false
toc: false
images:
tags:
  - python
---

Sometimes debugging take more time than you are willing to spend on it. Usually you do not want to waste any time debugging but the life of a software developer is debugging all the time.

I came across a interesting one.

I often see mini examples on twitter about a code snippet of how objects behave in Python and some unintended consequences may cause your code to do things you were not expecting, yet it totally makes sense.

Here is a simple version of the example of how objects behave.

```python

obj_a = [1, 2, 3]
obj_b = obj_a
obj_b.append(4)
print(obj_a)

```

This will print

```bash
[1, 2, 3, 4]
```

Simply because the objects in memory are the same.

In python you can check this by passing the object to the build-in function `id`

```python

assert id(obj_a) == id(obj_b)

```

I used to see this then meh 🤷‍♂️ interesting to know.

This is good thing to be aware because in a program you can have objects being executed in one part of your program and affecting others that you do not want.

This week I spent a great amount of time to figure out a bug 🐛 in a program and here a snippet that reproduces the issue.

```python
bizarre_list = []
my_dict = { "key": [] }

for i in range(10):
    bizarre_list.append(i)
    if i == 6:
        print(bizarre_list)
        my_dict["key"].append(bizarre_list)

print(bizarre_list)

print(my_dict)
```

output

```bash
[0, 1, 2, 3, 4, 5, 6]
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
{'key': [[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]]}
```

When I appended the list to the dict key list it kept on adding items to the list in both the dict and the bizarre_list. This is what python does, but I wanted a different behavior. The intention is to the list be at the state in iteration `i == 6`

to achieve this just use the python module `copy` and the function `deepcopy`

```python

from copy import deepcopy

bizarre_list = []
my_dict = { "key": [] }

for i in range(10):
    bizarre_list.append(i)
    if i == 6:
        print(bizarre_list)
        bizarre_list_snapshot = deepcopy(bizarre_list)
        my_dict["key"].append(bizarre_list_snapshot)

print(bizarre_list)

print(my_dict)

```

output

```bash
[0, 1, 2, 3, 4, 5, 6]
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
{'key': [[0, 1, 2, 3, 4, 5, 6]]}
```

This is it for this post.

cheers

Pedro
