---
title: "Python regex basics to never forget"
date: 2020-09-01T20:21:51+09:30
draft: false
toc: false
images:
tags:
  - regex
  - python
---

I have to confess that regex is a weakness this is why I am writing this post.

Whenever I can I try to avoid using regex if the problem can be used in a different way, but sometimes I have to inevitably use regex.

to import

`import re`

This is the only part that I usually remember :)

Regex are patters represented by symbols

for example 

```python
pattern = 'a'

test_string = 'a cat'

result = re.match(pattern, test_string)
# Output: <re.Match object; span=(0, 1), match='a'>
```

Usually the method match is not very useful but there is a better one `re.findall`

```python

# Program to extract numbers from a string

import re

string = 'hello 12 hi 89. Howdy 34'
pattern = '\d+'

result = re.findall(pattern, string) 
print(result)

# Output: ['12', '89', '34']

```
it returns a list with all matches of the pattern

Another useful one is `re.sub` to substitute

```python

# Program to remove all whitespaces
import re

# multiline string
string = 'abc 12\
de 23 \n f45 6'

# matches all whitespace characters
pattern = '\s+'

# empty string
replace = ''

new_string = re.sub(pattern, replace, string) 
print(new_string)

# Output: abc12de23f456
```
Different from match that match exactly the search lookup if there is a pattern in the string

`re.search`

```python
import re

string = "Python is fun"

# check if 'Python' is at the beginning
match = re.search('\APython', string)

if match:
  print("pattern found inside the string")
else:
  print("pattern not found")  

# Output: pattern found inside the string
```

No finalise, and this is a matter os preference, is the compile object

in the example above you could have done the re.search by

```python
p = re.compile('\APython')
p.search("Python is fun")

# Output: <re.Match object; span=(0, 6), match='Python'>
```
I got almost if not all examples from programviz which is a great resource and the best on the internet I could find on regex documentation.

For example of patterns it can be consulted [here](https://www.programiz.com/python-programming/regex)

also there is a very good regex in "general" site where you can test your patterns and there is also a cheat sheet

https://regexr.com/

