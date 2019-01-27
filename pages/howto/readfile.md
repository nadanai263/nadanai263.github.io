---
layout: page
title: "How to: read a file in python"
date: 2019-01-27
description: How to parse a file line-by-line
---

This is a demonstration of parsing a file in python. As an example we'll read a file which contains an adjacency list representation of a graph: in this file, the first element of each row corresponds to a node, while the rest of the elements in the same row correspond to other nodes connected to the first node. The most basic way is to use the `readlines()` method. This reads the file line-by-line, and puts each line into an array. Then, we can inspect the first element of the array by printing it:

```python
with open('graph.txt', 'r') as file:
    lines = file.readlines()

lines[0]
```

    Output: 
    '1\t37\t79\t164\t155\t32\t87\t39\t113\t15\t18\t78\t175\t140\t200\t4\t160\t97\t191\t100\t91\t20\t69\t198\t196\t\n'

As you can see, the first element is a string of numbers, each separated by `'\t'`, and ending with `'\n'`. We'll strip these elements out and rearrange the data into a numerical list:

```python
striplines = [line.replace('\t', ' ').replace('\n','') for line in lines]
striplines[0]
```

    Output: 
    '1 37 79 164 155 32 87 39 113 15 18 78 175 140 200 4 160 97 191 100 91 20 69 198 196 '

Finally, we'll use the `split()` method to convert the string to a list:

```python
numlist=[]
for i in range(len(striplines)):
    numlist.append([int(element) for element in striplines[i].split()])

numlist[0]
```

    Output: 
    [1, 37, 79, 164, 155, 32, 87, 39, 113, 15, 18, 78, 175, 140, 200, 4, 160, 97, 191, 100, 91, 20, 69, 198, 196]




