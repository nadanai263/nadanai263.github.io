---
layout: page
title: "CountInversions"
date: 2018-11-29
description: The number of inversions in an array can be counted with a simple modification of MergeSort.
---
The number of inversions in an array can be counted with a simple modification of MergeSort.

#### 1. The algorithm

```python
def sort_and_count(a):
    # Count number of inversions in array a
    # using merge_sort
    
    n = len(a)
    
    # 1. Base case:
    if n==1:
        return(a, 0)
    
    else:
        
        # Split a into two parts
        a1 = a[:math.floor(n/2)]
        a2 = a[math.floor(n/2):]

        B,x = sort_and_count(a1)
        C,y = sort_and_count(a2)
        
        # Merge B and C, return D and z
        m = len(B)+len(C)
        D = []
        z = 0
        
        i=0
        j=0
        for k in range(m):
            if i==int(len(B)):
                D=D+C[j:]
                break
            elif j==int(len(C)):
                D=D+B[i:]  
                break
            elif B[i]<C[j]:
                D.append(B[i])
                i+=1
            else:
                D.append(C[j])
                j+=1   
                z = z + len(B[i:]) # Number of split inversions is number of remaining elements in B

        return(D, x+y+z)

sort_and_count([1,3,5,2,4,6,23,4])    
```

    ([1, 2, 3, 4, 4, 5, 6, 23], 7)


#### 2. Conclusions

Jupyter notebooks are available for all algorithms on my [GitHub repository](https://github.com/nadanai263/datasciportfolio). 