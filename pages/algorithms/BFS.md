---
layout: page
title: "Breadth-first search"
date: 2019-03-02
description: Using a queue, a graph can be searched in O(n+m) time one layer at a time. 
---

Using a queue, a graph can be searched in O(n+m) time one layer at a time. 

---

#### 1. The algorithm

```python
# Example graph from lecture, represented as an adjacency list
G = {"s":["a","b"], "a":["s","c"], "b":["s","c","d"], "c":["a","d","e"], "d":["b","c","e"], "e":["c","d"]}
explored = {"s":True, "a":False, "b":False, "c":False, "d":False, "e":False}
N = {}
L = {}

Q=[]
Q.append("s") # Initialise queue with first node
L={"s":0} # Keep track of layers
count=1 # Keep track of nodes explored

while Q!=[]:
    v=Q.pop(0) # Dequeue from front
    
    # Look at nodes connected to v
    # If any of them unexplored, explore
    # and add to queue
    for node in G[v]:
        if explored[node]==False:
            explored[node]=True
            Q.append(node)
            N[node]=count
            L[node]=L[v]+1
            count+=1
print(explored)
print(N) # Order of nodes explored
print(L) # Layer of node
```

    {'s': True, 'a': True, 'b': True, 'c': True, 'd': True, 'e': True}
    {'a': 1, 'b': 2, 'c': 3, 'd': 4, 'e': 5}
    {'s': 0, 'a': 1, 'b': 1, 'c': 2, 'd': 2, 'e': 3}

---

#### 2. A bigger example

```python
# Different adjacency list representation
# from mincut example

with open('./algorithms_data/MinCut.txt', 'r') as file:
    striplines=[line.replace('\t', ' ').replace('\n','') for line in file.readlines()]

    numlist=[]
    G={} # graph as adjacency list dictionary
    for i in range(len(striplines)):
        numlist.append([int(element) for element in striplines[i].split()])
        G[numlist[i][0]] = numlist[i][1:] # first element is node, the rest are nodes connected to it 

explored = dict((el,False) for el in G.keys())
N = {}
L = {}

# Set node to begin search
firstnode=3
Q=[]
Q.append(firstnode) # Initialise queue with first node
explored[firstnode]=True
L={firstnode:0} # Keep track of layers
count=1 # Keep track of nodes explored

while Q!=[]:
    v=Q.pop(0) # Dequeue from front
    
    # Look at nodes connected to v
    # If any of them unexplored, explore
    # and add to queue
    for node in G[v]:
        if explored[node]==False:
            explored[node]=True
            Q.append(node)
            N[node]=count
            L[node]=L[v]+1
            count+=1

print(N) # Order of nodes explored
print(L) # Layer of node
```

    {48: 1, 123: 2, 134: 3, 109: 4, 41: 5, 17: 6, 159: 7, 49: 8, 136: 9, 16: 10, 130: 11, 141: 12, 29: 13, 176: 14, 2: 15, 190: 16, 66: 17, 153: 18, 157: 19, 70: 20, 114: 21, 65: 22, 173: 23, 104: 24, 194: 25, 54: 26, 180: 27, 128: 28, 188: 29, 197: 30, 105: 31, 51: 32, 94: 33, 116: 34, 183: 35, 33: 36, 63: 37, 184: 38, 168: 39, 179: 40, 162: 41, 11: 42, 83: 43, 193: 44, 165: 45, 50: 46, 95: 47, 30: 48, 24: 49, 47: 50, 189: 51, 147: 52, 199: 53, 186: 54, 129: 55, 74: 56, 156: 57, 182: 58, 138: 59, 28: 60, 167: 61, 152: 62, 154: 63, 13: 64, 142: 65, 110: 66, 90: 67, 80: 68, 10: 69, 12: 70, 101: 71, 92: 72, 143: 73, 72: 74, 82: 75, 148: 76, 181: 77, 106: 78, 86: 79, 36: 80, 169: 81, 163: 82, 61: 83, 111: 84, 161: 85, 177: 86, 108: 87, 44: 88, 117: 89, 170: 90, 77: 91, 43: 92, 88: 93, 103: 94, 21: 95, 187: 96, 59: 97, 178: 98, 192: 99, 58: 100, 164: 101, 171: 102, 26: 103, 112: 104, 191: 105, 62: 106, 195: 107, 25: 108, 91: 109, 39: 110, 71: 111, 158: 112, 131: 113, 46: 114, 5: 115, 38: 116, 37: 117, 185: 118, 18: 119, 118: 120, 124: 121, 56: 122, 53: 123, 68: 124, 175: 125, 87: 126, 120: 127, 15: 128, 42: 129, 73: 130, 99: 131, 4: 132, 75: 133, 151: 134, 64: 135, 35: 136, 7: 137, 113: 138, 45: 139, 14: 140, 122: 141, 40: 142, 67: 143, 19: 144, 121: 145, 22: 146, 78: 147, 137: 148, 166: 149, 149: 150, 32: 151, 85: 152, 27: 153, 76: 154, 126: 155, 140: 156, 69: 157, 133: 158, 9: 159, 23: 160, 144: 161, 100: 162, 139: 163, 98: 164, 119: 165, 79: 166, 52: 167, 146: 168, 81: 169, 60: 170, 145: 171, 97: 172, 34: 173, 84: 174, 125: 175, 102: 176, 31: 177, 198: 178, 132: 179, 115: 180, 172: 181, 155: 182, 8: 183, 174: 184, 127: 185, 135: 186, 93: 187, 107: 188, 150: 189, 96: 190, 1: 191, 20: 192, 160: 193, 55: 194, 6: 195, 57: 196, 196: 197, 200: 198, 89: 199}

    {3: 0, 48: 1, 123: 1, 134: 1, 109: 1, 41: 1, 17: 1, 159: 1, 49: 1, 136: 1, 16: 1, 130: 1, 141: 1, 29: 1, 176: 1, 2: 1, 190: 1, 66: 1, 153: 1, 157: 1, 70: 1, 114: 1, 65: 1, 173: 1, 104: 1, 194: 1, 54: 1, 180: 2, 128: 2, 188: 2, 197: 2, 105: 2, 51: 2, 94: 2, 116: 2, 183: 2, 33: 2, 63: 2, 184: 2, 168: 2, 179: 2, 162: 2, 11: 2, 83: 2, 193: 2, 165: 2, 50: 2, 95: 2, 30: 2, 24: 2, 47: 2, 189: 2, 147: 2, 199: 2, 186: 2, 129: 2, 74: 2, 156: 2, 182: 2, 138: 2, 28: 2, 167: 2, 152: 2, 154: 2, 13: 2, 142: 2, 110: 2, 90: 2, 80: 2, 10: 2, 12: 2, 101: 2, 92: 2, 143: 2, 72: 2, 82: 2, 148: 2, 181: 2, 106: 2, 86: 2, 36: 2, 169: 2, 163: 2, 61: 2, 111: 2, 161: 2, 177: 2, 108: 2, 44: 2, 117: 2, 170: 2, 77: 2, 43: 2, 88: 2, 103: 2, 21: 2, 187: 2, 59: 2, 178: 2, 192: 2, 58: 3, 164: 3, 171: 3, 26: 3, 112: 3, 191: 3, 62: 3, 195: 3, 25: 3, 91: 3, 39: 3, 71: 3, 158: 3, 131: 3, 46: 3, 5: 3, 38: 3, 37: 3, 185: 3, 18: 3, 118: 3, 124: 3, 56: 3, 53: 3, 68: 3, 175: 3, 87: 3, 120: 3, 15: 3, 42: 3, 73: 3, 99: 3, 4: 3, 75: 3, 151: 3, 64: 3, 35: 3, 7: 3, 113: 3, 45: 3, 14: 3, 122: 3, 40: 3, 67: 3, 19: 3, 121: 3, 22: 3, 78: 3, 137: 3, 166: 3, 149: 3, 32: 3, 85: 3, 27: 3, 76: 3, 126: 3, 140: 3, 69: 3, 133: 3, 9: 3, 23: 3, 144: 3, 100: 3, 139: 3, 98: 3, 119: 3, 79: 3, 52: 3, 146: 3, 81: 3, 60: 3, 145: 3, 97: 3, 34: 3, 84: 3, 125: 3, 102: 3, 31: 3, 198: 3, 132: 3, 115: 3, 172: 3, 155: 3, 8: 3, 174: 3, 127: 3, 135: 3, 93: 3, 107: 4, 150: 4, 96: 4, 1: 4, 20: 4, 160: 4, 55: 4, 6: 4, 57: 4, 196: 4, 200: 4, 89: 4}
    
---

#### 3. Conclusions

Jupyter notebooks are available for all algorithms on my [GitHub repository](https://github.com/nadanai263/datasciportfolio). 