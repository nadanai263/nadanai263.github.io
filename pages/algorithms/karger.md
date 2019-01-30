---
layout: page
title: "Karger's randomised contraction"
date: 2019-01-30
description: Karger's randomised contraction algorithm randomly contracts edges on a graph, and finds minimum cuts with a small but nonzero probability. Repeating this process many times results in increasing probability of finding the minimum cut. A naive algorithm can therefore be useful, thanks to the laws of probability.
---
Karger's randomised contraction algorithm randomly contracts edges on a graph, and finds minimum cuts with a small but nonzero probability. Repeating this process many times results in increasing probability of finding the minimum cut. A naive algorithm can therefore be useful, thanks to the laws of probability.

---

#### 1. The algorithm

```python
def KargerStep(G):
# Performs a Karger step on graph G
# G is represented as an adjacency matrix in dict format
# Each key represents a node, and its value is the list of all 
# nodes connected to it

    # 1. Select two connected nodes A and B at random
    A = random.choice(list(G.keys())) # select A from list of keys
    B = random.choice(G[A]) # select B from inside A

    # 2. Add all of B's connections to A
    for node in G[B]:
        G[A].append(node)

    # 3. Change all pointers to B to A instead
    for j in G[B]: # for all nodes connected to B
        lst=G[j]
        for i in range(len(lst)): 
            if lst[i]==B:
                lst[i]=A
     
    # 4. Get rid of self-loops
    while A in G[A]:
        G[A].remove(A)
        
    # 5. Get rid of node B
    del G[B]
    
    return(G)

def randomContract(G):
# Random contraction is just sequence
# of Karger steps which terminates when
# only two nodes are left

    while len(G)>2:
        G = KargerStep(G)
    nodes=[key for key in G.keys()]
    cut=len(G[nodes[0]])
    print(cut)
    return(cut)

# Find minimum cut

cuts=[] 
for i in range(10):
    with open('./algorithms_data/MinCut.txt', 'r') as file:
        striplines=[line.replace('\t', ' ').replace('\n','') for line in file.readlines()]

        numlist=[]
        G={} # graph as adjacency list dictionary
        for i in range(len(striplines)):
            numlist.append([int(element) for element in striplines[i].split()])
            G[numlist[i][0]] = numlist[i][1:] # first element is node, the rest are nodes connected to it 

        cuts.append(randomContract(G))
        
print('Min cut =', min(cuts))
```

    17
    20
    129
    61
    48
    25
    587
    219
    97
    27
    Min cut = 17

---

#### 2. Conclusions

Jupyter notebooks are available for all algorithms on my [GitHub repository](https://github.com/nadanai263/datasciportfolio). 