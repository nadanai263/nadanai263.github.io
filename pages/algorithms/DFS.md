---
layout: page
title: "Depth-first search"
date: 2019-03-02
description: A recursive algorithm can also search the graph in O(n+m), but burrows as deeply as possible into the graph before backtracking. This is known as depth-first search. 
---

A recursive algorithm can also search the graph in O(n+m), but burrows as deeply as possible into the graph before backtracking. This is known as depth-first search. 

---

#### 1. The algorithm

```python
# Example graph from lecture, represented as an adjacency list
G = {"s":["a","b"], "a":["s","c"], "b":["s","c","d"], "c":["a","d","e"], "d":["b","c","e"], "e":["c","d"]}
explored = {"s":True, "a":False, "b":False, "c":False, "d":False, "e":False}
N = {}
count=1 # Keep track of nodes explored

def DFS(G,s,explored,N,count):
    explored[s]=True
    N[s]=count
    print('Node',s,'count',count)
    count+=1
    
    # Recursively call DFS on daughter nodes
    for node in G[s]:
        if explored[node]==False:
            explored,N,count=DFS(G,node,explored,N,count)
            
    return(explored,N,count)
    
explored,N,count=DFS(G,"s",explored,N,count)            
            
print(explored)
print(N)

```

    Node s count 1
    Node a count 2
    Node c count 3
    Node d count 4
    Node b count 5
    Node e count 6
    {'s': True, 'a': True, 'b': True, 'c': True, 'd': True, 'e': True}
    {'s': 1, 'a': 2, 'c': 3, 'd': 4, 'b': 5, 'e': 6}

---

#### 2. Conclusions

Jupyter notebooks are available for all algorithms on my [GitHub repository](https://github.com/nadanai263/datasciportfolio). 