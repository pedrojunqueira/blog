---
title: "Why I stopped using Stack Overflow"
date: 2023-02-16T17:16:10+10:30
draft: false
toc: false
images:
tags:
  - ai
  - python
---

Stack Overflow is great and it saved me many times. I only use it to read and usually do not ask questions and do not contribute. Despite been great for me I had a bad experience dealing with a person. I once asked a question and all that I got was a down vote, which is bad to your "points" in the community.

The reason is that the question was answered already somewhere in different words and because of this trauma I never asked a question again and any question I ever asked after that has already been answered somehow somewhere in the internet and Google was my best friend for this.

Then came the ChatGTP. I will be honest with you. I was impressed but meh... not that much. I thought the hype was a bit overrated. My first impression is that it was very good for producing texts and code snippets and that was it. However, I never actually used for work. Then today I gave it a go and boy that was good to increase my productivity.

I needed to write a simple function in Python to generate a list of floats in a particular pattern. It would take me somewhere between 10 to 20 minutes to test research about it and refactor roughly.

Then I though. Why not give chatGPT a go?

I tried to describe what I wanted and in my first attempt was

`writhe in Python 3.9 a function that returns a list of floats with the following parameters size 
of the list to be returned and the chunks of data will have length of n size with gaps in between`

note that I even had some spelling errors 🙈

it returned almost what I wanted

```python
import random

def generate_floats(size, n):
    # Initialize an empty list to store the floats
    result = []

    # Generate chunks of floats with length n
    while len(result) < size:
        # Generate a chunk of floats of length n
        chunk = [random.uniform(0, 1) for _ in range(n)]
        result.extend(chunk)

        # If the total length of the result list is larger than the size parameter, break the loop
        if len(result) >= size:
            break

        # Insert a gap between the chunks
        gap = [0.0] * (n // 2)
        result.extend(gap)

    # Trim the result list to the correct size
    result = result[:size]

    return result
```

The only missing thing was also to have the size of the gap as a parameter then I asked.

`Can you generate again but in a way I also control the size of the gap between the random chuncks?`

then 🤯 OMG !

```python
import random

def generate_floats(size, n, gap_size):
    # Initialize an empty list to store the floats
    result = []

    # Generate chunks of floats with length n
    while len(result) < size:
        # Generate a chunk of floats of length n
        chunk = [random.uniform(0, 1) for _ in range(n)]
        result.extend(chunk)

        # If the total length of the result list is larger than the size parameter, break the loop
        if len(result) >= size:
            break

        # Insert a gap between the chunks
        gap = [0.0] * gap_size
        result.extend(gap)

    # Trim the result list to the correct size
    result = result[:size]

    return result
```

That was exactly what I needed just refactored a bit and changed to add the range of the floats.

Conclusion is that Stack Overflow is over for me from now onwards.

With chatGPT my productivity in this case increase by a factor of 10-20x.

Also thinking to sign up for Co-Pilot in Github which uses chatGTP under the hood. I never tested but will explore soon.
