---
layout: page
title: "Heaps and median maintenance"
date: 2019-03-12
description: Here we code up our first data structure, the heap. This can be implemented using a balanced complete binary tree. Heaps are particularly suitable for operations involving repeated calculations of minimum or maximum values; an example is median maintenance, or determining the median of a changing array.
---

Here we code up our first data structure, the heap. This can be implemented using a balanced complete binary tree. Heaps are particularly suitable for operations involving repeated calculations of minimum or maximum values; an example is median maintenance, or determining the median of a changing array.

---

#### 1. The data structure

```python
class BinaryMinHeap():
    # Maintains a list arranged in heap format
    
    def __init__(self):
        self.heapList = [0]
        self.currentSize = 0
        
    def bubbleUp(self,i):
        while i//2 > 0: # while you're not at the root
            if self.heapList[i] < self.heapList[i//2]: 
                # if child is less than parent then swap positions
                tmp = self.heapList[i//2]
                self.heapList[i//2] = self.heapList[i]
                self.heapList[i] = tmp
            i = i//2 # now you're at parent's position
    
    def bubbleDown(self,i):
        while (i*2)<=self.currentSize: # while you're still in the tree
            
            # figure out which is the smallest child
            mc = self.minChild(i) 
            
            if self.heapList[i] > self.heapList[mc]:
                # if smallest child is less than parent then swap position
                tmp = self.heapList[i]
                self.heapList[i] = self.heapList[mc]
                self.heapList[mc] = tmp
            i = mc # now you're at child's position
            
    def minChild(self,i):
        # Return which child is smaller
        # if right child doesn't exist, then return left child
        # else return the smaller of the two children
        
        if i*2+1 > self.currentSize:            
            return i*2
        else: 
            if self.heapList[i*2] < self.heapList[i*2+1]:
                return i*2
            else:
                return i*2+1
            
    def Insert(self,k):
        self.heapList.append(k) # add new value to end of heaplist
        self.currentSize = self.currentSize+1
        self.bubbleUp(self.currentSize) # percolate up as necessary to maintain heap invariant
      
    def ExtractMin(self):
        minvalue = self.heapList[1] 
        self.heapList[1] = self.heapList[self.currentSize] # copy last item into root
        self.currentSize = self.currentSize - 1
        self.heapList.pop() # remove last item from list
        self.bubbleDown(1) # percolate down as necessary to maintain heap invariant
        return minvalue
        
    def buildHeap(self,alist):
        # Start in middle of input array
        # Working upwards to root, arrange children in heap structure
        # This takes O(n) time
        
        i = len(alist)//2
        self.currentSize = len(alist)
        self.heapList = [0] + alist[:]
        while (i>0):
            self.bubbleDown(i)
            i = i-1
        
```
```python
class BinaryMaxHeap():
    # Maintains a list arranged in heap format
    
    def __init__(self):
        self.heapList = [0]
        self.currentSize = 0
        
    def bubbleUp(self,i):
        while i//2 > 0: # while you're not at the root
            if self.heapList[i] > self.heapList[i//2]: 
                # if child is less than parent then swap positions
                tmp = self.heapList[i//2]
                self.heapList[i//2] = self.heapList[i]
                self.heapList[i] = tmp
            i = i//2 # now you're at parent's position
    
    def bubbleDown(self,i):
        while (i*2)<=self.currentSize: # while you're still in the tree
            
            # figure out which is the smallest child
            mc = self.maxChild(i) 
            
            if self.heapList[i] < self.heapList[mc]:
                # if largest child is more than parent then swap position
                tmp = self.heapList[i]
                self.heapList[i] = self.heapList[mc]
                self.heapList[mc] = tmp
            i = mc # now you're at child's position
            
    def maxChild(self,i):
        # Return which child is larger
        # if right child doesn't exist, then return left child
        # else return the larger of the two children
        
        if i*2+1 > self.currentSize:            
            return i*2
        else: 
            if self.heapList[i*2] < self.heapList[i*2+1]:
                return i*2+1
            else:
                return i*2
            
    def Insert(self,k):
        self.heapList.append(k) # add new value to end of heaplist
        self.currentSize = self.currentSize+1
        self.bubbleUp(self.currentSize) # percolate up as necessary to maintain heap invariant
      
    def ExtractMax(self):
        maxvalue = self.heapList[1] 
        self.heapList[1] = self.heapList[self.currentSize] # copy last item into root
        self.currentSize = self.currentSize - 1
        self.heapList.pop() # remove last item from list
        self.bubbleDown(1) # percolate down as necessary to maintain heap invariant
        return maxvalue
        
    def buildHeap(self,alist):
        # Start in middle of input array
        # Working upwards to root, arrange children in heap structure
        # This takes O(n) time
        
        i = len(alist)//2
        self.currentSize = len(alist)
        self.heapList = [0] + alist[:]
        while (i>0):
            self.bubbleDown(i)
            i = i-1
        
```
```python
# Example usage:

heap=BinaryMinHeap()
data = [5,9,11,14,18,19,21,33,17,27]
heap.buildHeap(data)
print(heap.heapList)
heap.ExtractMin()
```

	[0, 5, 9, 11, 14, 18, 19, 21, 33, 17, 27]
	5

#### 2. Median maintenance example

```python
def medianMaint(data):
    # Median maintenance
    
    medians = []
    Hlow=BinaryMaxHeap()
    Hhigh=BinaryMinHeap()
    Hlow.buildHeap([])
    Hhigh.buildHeap([])

    # First element goes into Hlow
    Hlow.Insert(data[0])
    medians.append(data[0])
    
    # Second element
    if data[1]>data[0]:
        Hhigh.Insert(data[1])
        medians.append(data[0])
    elif data[1]<data[0]:
        Hlow.Insert(data[1])
        Hhigh.Insert(Hlow.ExtractMax())
        medians.append(data[1])
    
    for newelem in data[2:]:

        if newelem < Hlow.heapList[1]:
            Hlow.Insert(newelem)

        elif newelem > Hhigh.heapList[1]:
            Hhigh.Insert(newelem)

        elif newelem > Hlow.heapList[1] and newelem < Hhigh.heapList[1]:
            Hlow.Insert(newelem) # insert to lower heap if in between

        if Hlow.currentSize-Hhigh.currentSize==2:
            Hhigh.Insert(Hlow.ExtractMax()) 
        elif Hhigh.currentSize-Hlow.currentSize==2:
            Hlow.Insert(Hhigh.ExtractMin())

        if Hlow.currentSize==Hhigh.currentSize:
            medians.append(Hlow.heapList[1])
        elif Hlow.currentSize>Hhigh.currentSize:
            medians.append(Hlow.heapList[1])
        elif Hlow.currentSize<Hhigh.currentSize:
            medians.append(Hhigh.heapList[1])

    return(medians)

data = [5,9,11,14,18,19,21,33,17,27]
medians = medianMaint(data)
print(medians)

```

	[5, 5, 9, 9, 11, 11, 14, 14, 17, 17]

---

#### 3. Conclusions

Jupyter notebooks are available for all algorithms on my [GitHub repository](https://github.com/nadanai263/datasciportfolio). 