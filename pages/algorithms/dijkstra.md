---
layout: page
title: "Dijkstra"
date: 2019-03-06
description: Dijkstra's algorithm finds the shortest path between two elements in a directed graph with non-negative edge lengths. It does this by searching through edges connecting the explored and unexplored parts of the graph. A naive implementation runs in O(mn) time, while an implementation using a heap data structure runs in O(mlog(n)) time.
---

Dijkstra's algorithm finds the shortest path between two elements in a directed graph with non-negative edge lengths. It does this by searching through edges connecting the explored and unexplored parts of the graph. A naive implementation runs in O(mn) time, while an implementation using a heap data structure runs in O(mlog(n)) time.

---

#### 1. The naive O(mn) implementation

```python
# Example graph from lecture, represented as a nested-dict adjacency list with tuples
# indicating edge weights
G = {"s":{"v":1, "w":4}, "v":{"w":2, "t":6}, "w":{"t":3}, "t":{}}

X = ["s"] # vertices processed so far
A = {"s":0}

while len(A)<len(G):
    crossingedges = [] # list of edges between explored and unexplored sectors
    dijkstra = [] # list of dijkstra greedy critera
    for node1 in A.keys(): # start point is vertex in explored sector
        for node2 in G[node1].keys():
            if not node2 in A.keys(): # endpoint is vertex not in explored sector
                crossingedges.append(node2)
                dijkstra.append(A[node1] + G[node1][node2])                
    # Select edge with minimum Dijkstra value
    argmin = np.argmin(dijkstra)
    X.append(crossingedges[argmin])
    A[crossingedges[argmin]] = dijkstra[argmin]
    
# Take into account nodes which have no shortest path to source
TOTALNODES = [item for item in G.keys()]
PROCESSED = [item for item in X]
diff = set(TOTALNODES).symmetric_difference(set(PROCESSED))
orphans = [item for item in diff]

for node in orphans:
    X.append(node)
    A[node] = 1000000 # Set to a large number

print(A)
```

	{'s': 0, 't': 6, 'v': 1, 'w': 3}

```python
# Coursera assignment
# Get graph into same format as above

with open('./algorithms_data/Dijkstra.txt', 'r') as file:
    striplines=[line.replace('\t', ' ').replace('\n','') for line in file.readlines()]

    G = {}
    
    for i in range(len(striplines)):
        splitline = striplines[i].split()
        nodes = {}
        G[splitline[0]] = nodes
        for j in range(len(splitline)-1):
            keyvalues = splitline[j+1].split(',')
            nodes[keyvalues[0]] = int(keyvalues[1])
        

X = ['1'] # vertices processed so far
A = {'1':0}

while len(A)<len(G):
    crossingedges = [] # list of edges between explored and unexplored sectors
    dijkstra = [] # list of dijkstra greedy critera
    for node1 in A.keys(): # start point is vertex in explored sector
        for node2 in G[node1].keys():
            if not node2 in A.keys(): # endpoint is vertex not in explored sector
                crossingedges.append(node2)
                dijkstra.append(A[node1] + G[node1][node2])                
    # Select edge with minimum Dijkstra value
    argmin = np.argmin(dijkstra)
    X.append(crossingedges[argmin])
    A[crossingedges[argmin]] = dijkstra[argmin]

# Take into account nodes which have no shortest path to source
TOTALNODES = [item for item in G.keys()]
PROCESSED = [item for item in X]
diff = set(TOTALNODES).symmetric_difference(set(PROCESSED))
orphans = [item for item in diff]

for node in orphans:
    X.append(node)
    A[node] = 1000000 # Set to a large number

for vertex in ['7','37','59','82','99','115','133','165','188','197']:
    print(A[vertex])
```

	2599
	2610
	2947
	2052
	2367
	2399
	2029
	2442
	2505
	3068
    
---

#### 2. Conclusions

Jupyter notebooks are available for all algorithms on my [GitHub repository](https://github.com/nadanai263/datasciportfolio). 