---
layout: page
title: "MergeSort"
date: 2018-11-29
description: MergeSort is the canonical example of a recursive divide and conquer algorithm. It recursively divides the input array, resulting in log(N) levels; at each level the arrays are merged in N time, resulting in an overall O(Nlog(N)) performance. 
---

MergeSort is the canonical example of a recursive divide and conquer algorithm. It recursively divides the input array, resulting in log(N) levels; at each level the arrays are merged in N time, resulting in an overall O(Nlog(N)) performance. 

---

#### 1. The algorithm

```python
def mergesort(a):
    # Return sorted array
    # Only works if n has is power of 2
    # Complexity 6nlog(n) + 6n
    
    # First split into two arrays
    n = len(a)    
    A = a[:math.floor(n/2)]
    B = a[math.floor(n/2):]

    # Base case
    if len(A)==1 and len(B)==1:
        if A<B:
            C = A+B
        else:
            C = B+A
            
    elif len(A)==1 and len(B)==2:
        
        B = mergesort(B)
        
        if A[0]<B[0]:
            C = A+B
        elif A[0]<B[1]:
            C = [B[0],A[0],B[1]] 
        else:
            C = [B[0],B[1],A[0]] 
        
    else:      
        # Recursive calls
        A = mergesort(A)
        B = mergesort(B)
        
        # Merge
        C = []
        i=0
        j=0
        for k in range(n):
            if i==int(len(A)):
                C=C+B[j:]
                break
            elif j==int(len(B)):
                C=C+A[i:]  
                break
            elif A[i]<B[j]:
                C.append(A[i])
                i+=1
            else:
                C.append(B[j])
                j+=1   
                
    return (C)

def test_mergesort(a):
    assert np.array(mergesort(a)).all()==np.sort(a).all()
    print('Passed')

a = [i for i in range(20)]
shuffle(a)
print(a)
test_mergesort(a)
print(mergesort(a)) 
```

    [6, 3, 7, 16, 0, 5, 18, 1, 15, 13, 4, 11, 9, 10, 12, 17, 19, 14, 2, 8]
    Passed
    [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19]

---

#### 2. Conclusions

Jupyter notebooks are available for all algorithms on my [GitHub repository](https://github.com/nadanai263/datasciportfolio). 