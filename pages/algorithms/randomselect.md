---
layout: page
title: "RandomSelect"
date: 2019-02-06
description: Selection is fundamentally easier than sorting. Using the partitioning function from QuickSort, we can extract the ith-order statistic in O(N) time on average using the randomised selection algorithm.
---

Selection is fundamentally easier than sorting. Using the partitioning function from QuickSort, we can extract the ith-order statistic in O(N) time on average using the randomised selection algorithm.

---

#### 1. The algorithm

```python
def Partition_RS(a,l,r):
    
    pivot = random.choice(a) # Choose random pivot
    ind = [i for i,val in enumerate(a) if val==pivot][0] # index of random pivot
    pivot = a[ind]
    
    # Put pivot to left of array
    swap5 = a[l]
    swap6 = a[ind]
    a[l] = swap6
    a[ind] = swap5

    # Keep track of elements less than pivot with index i
    i = l+1
    
    # Put all elements less than pivot between l and i
    for j in range(l+1,r):
        if a[j]<pivot:
            swap1=a[j]
            swap2=a[i]
            a[j]=swap2
            a[i]=swap1
            i=i+1
        else:
            pass
    
    # Put pivot to right of all elements less than it.
    # Pivot is now in correct position
    swap3=a[l]
    swap4=a[i-1]
    a[i-1]=swap3
    a[l]=swap4
    
    return(a,i-1) # i is index of first element on RHS; i-1 is pivot index
 
def RSelect(a,length,order):
# on average, O(N) algorithm for selecting i-th order statistic of array

    if length==1:
        return a[0]
    else:
        a,j=Partition_RS(a,0,len(a))

        if j==order-1: # If we happen to select correct value then return it
            return a[j]
        
        elif j>order-1: # Else if our value is bigger then recurse on LHS
            return RSelect(a[:j],j,order)
        
        elif j<order-1: # Otherwise if our value is smaller then recurse on RHS
            return RSelect(a[j+1:],len(a)-j-1,order-j-1) 

a = [i for i in range(1,100)]
shuffle(a)
print(a)
val=RSelect(a,len(a),42)
print(val)
```

    [46, 24, 37, 25, 17, 77, 71, 33, 51, 35, 10, 28, 99, 73, 64, 86, 78, 84, 66, 61, 93, 82, 56, 70, 92, 36, 58, 67, 44, 45, 1, 9, 27, 68, 53, 42, 43, 59, 26, 18, 39, 50, 13, 30, 75, 60, 23, 8, 89, 95, 40, 31, 72, 94, 90, 96, 41, 54, 63, 16, 19, 12, 74, 4, 11, 5, 7, 21, 80, 52, 91, 48, 79, 49, 47, 2, 22, 57, 88, 81, 15, 87, 29, 34, 20, 98, 83, 69, 76, 55, 6, 3, 85, 65, 38, 62, 14, 32, 97]
    42

#### 2. Conclusions

Jupyter notebooks are available for all algorithms on my [GitHub repository](https://github.com/nadanai263/datasciportfolio). 