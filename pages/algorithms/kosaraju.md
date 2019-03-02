---
layout: page
title: "Kosaraju"
date: 2019-03-02
description: Kosaraju's algorithm computes the strongly-connected components of a directed graph in two DFS calls. The first call is done on a reversed graph, and results in a list of finishing times for each node. Calling DFS again on the forward graph, in decreasing order of finishing time, explores the SCCs one at a time. 
---

Kosaraju's algorithm computes the strongly-connected components of a directed graph in two DFS calls. The first call is done on a reversed graph, and results in a list of finishing times for each node. Calling DFS again on the forward graph, in decreasing order of finishing time, explores the SCCs one at a time.  

---

#### 1. The algorithm

```python
# FOR SUBMISSION
# Set max recursion limit large

import sys, threading
sys.setrecursionlimit(800000)
threading.stack_size(67108864)

def DFS_time(Grev,s,explored,finishingtime,time):
    explored[s]=True
    
    # Recursively call DFS on daughter nodes
    for node in Grev[s]:
        if explored[node]==False:
            explored,finishingtime,time=DFS_time(Grev,node,explored,finishingtime,time)            
    finishingtime[s]=time
    time+=1

    return(explored,finishingtime,time)

def DFS_leader(G,s,explored,leaders,leader,count):
    explored[s]=True
    count+=1
    leaders[s]=leader
    # Recursively call DFS on daughter nodes
    for node in G[s]:
        if explored[node]==False:
            explored,leaders,count=DFS_leader(G,node,explored,leaders,leader,count)

    return(explored,leaders,count)

def main():
    
    # 1.1 Make dict for adjacency list
    
    LENGTH = 875714
    NODES = (np.arange(LENGTH)+1).tolist()
    FILENAME = 'SCC'
    PATH = './algorithms_data/' + FILENAME + '.txt'
    finishingtime = {}
    leaders = {}
    time=1

    with open(PATH, 'r') as file:

        G = {}
        for line in tqdm(file.readlines()):
            splits=line.split()
            try:
                a = G[int(splits[0])]
                a.append(int(splits[1]))
                G[int(splits[0])] = a
            except:
                G[int(splits[0])] = [int(splits[1])]

    # Add missing nodes as blanks
    keys = [key for key in G.keys()]
    diff = set(NODES).symmetric_difference(set(keys))
    for item in diff:
    #    print('Added to G', item)
        G[item]=[]
    #print(G)


    # 1.2 Make reversed graph
    with open(PATH, 'r') as file:

        Grev = {}
        for line in tqdm(file.readlines()):
            splits=line.split()
            try:
                a = Grev[int(splits[1])]
                a.append(int(splits[0]))
                Grev[int(splits[1])] = a
            except:
                Grev[int(splits[1])] = [int(splits[0])]

    # Add missing nodes as blanks
    keys = [key for key in Grev.keys()]
    diff = set(NODES).symmetric_difference(set(keys))
    for item in diff:
    #    print('Added to Grev', item)
        Grev[item]=[]
    #print(Grev)

    # FIRST DFS PASS
    explored = {}
    for key in NODES:
        explored[key]=False 

    for s in tqdm(NODES[::-1]):
        if explored[s]==False:
            explored,finishingtime,time=DFS_time(Grev,s,explored,finishingtime,time)

    # SECOND DFS PASS
    explored = {}
    for key in finishingtime.keys():
        explored[key]=False
    keys = [item for item in finishingtime.keys()]
    keys.reverse()

    SCCtrack = []
    for s in tqdm(keys):
        leader=s
        count=0
        if explored[s]==False:        
            explored,leaders,count=DFS_leader(G,s,explored,leaders,leader,count)
        SCCtrack.append(count)

    # Count SCCs and print the five largest ones
    SCCcount = np.flip(np.sort(SCCtrack),axis=0)
    print(SCCcount[:5])

thread = threading.Thread(target=main)
thread.start()

```

    100%|██████████| 5105043/5105043 [00:10<00:00, 500895.07it/s] 
    100%|██████████| 5105043/5105043 [00:10<00:00, 484738.73it/s]
    100%|██████████| 875714/875714 [00:02<00:00, 359582.10it/s]
    100%|██████████| 875714/875714 [00:01<00:00, 441430.18it/s]
    [434821    968    459    313    211]

---

#### 2. Conclusions

Jupyter notebooks are available for all algorithms on my [GitHub repository](https://github.com/nadanai263/datasciportfolio). 