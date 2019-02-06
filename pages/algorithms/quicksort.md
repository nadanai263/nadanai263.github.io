---
layout: page
title: "QuickSort"
date: 2018-12-20
description: Randomized QuickSort is an example of a randomized algorithm which sorts an array in O(Nlog(N)) time, on average. This is due to the fact that an efficient division of the array should result in subproblems of roughly equal size; and that random pivot choices are likely to achieve this division on average.
---

Randomized QuickSort is an example of a randomized algorithm which sorts an array in O(Nlog(N)) time, on average. This is due to the fact that an efficient division of the array should result in subproblems of roughly equal size; and that random pivot choices are likely to achieve this division on average.

---

#### 1. An inefficient algorithm: choose first element as pivot each time

```python
def Partition_first(a,l,r):
    # First element as pivot
    
    pivot = a[l]
    i = l+1
    for j in range(l+1,r):
        if a[j]<pivot:
            swap1=a[j]
            swap2=a[i]
            a[j]=swap2
            a[i]=swap1
            i=i+1
        else:
            pass
    swap3=a[l]
    swap4=a[i-1]
    a[i-1]=swap3
    a[l]=swap4
    return(a,i)

def QuickSort_first(a,l,r):
    # Inefficient QuickSort with 1st element as pivot each time
    
    # Base case
    if r<=l+1:
        return(a)
    
    # Recursive calls
    else:
        a,pivot=Partition_first(a,l,r)
        a=QuickSort_first(a,l,pivot-1)
        a=QuickSort_first(a,pivot,r)
        return(a)
    
def test_quicksort(a):
    assert np.array(QuickSort_first(a,0,len(a))).all()==np.sort(a).all()
    print('Passed')

a = [i for i in range(20)]
shuffle(a)
print(a)
test_quicksort(a)
print(QuickSort(a,0,len(a)))
```

    [9, 14, 5, 2, 1, 6, 18, 4, 13, 12, 8, 0, 19, 3, 7, 16, 11, 17, 10, 15]
	Passed
	[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19]

---

#### 2. A much better algorithm: randomized pivots

```python
def Partition_RS(a,l,r):
    
    # 1. Pick pivot randomly
    ind = random.choice([i for i in range(l,r)]) # index of random pivot
    pivot = a[ind]
    
    # 2. Put pivot to beginning of array
    swap5 = a[l]
    swap6 = a[ind]
    a[l] = swap6
    a[ind] = swap5
      
    # 3. Keep track of elements less than pivot with index i
    i = l+1
    
    # 4. Put all elements less than pivot between l and i
    for j in range(l+1,r):
        if a[j]<pivot:
            swap1=a[j]
            swap2=a[i]
            a[j]=swap2
            a[i]=swap1
            i=i+1
        else:
            pass
        
    # 5. Move pivot to right of i (all elements less on left)
    # Pivot is now in correct position.
    swap3=a[l]
    swap4=a[i-1]
    a[i-1]=swap3
    a[l]=swap4
    return(a,i) # i is index of first element on RHS; i-1 is pivot index

def QuickSort_random(a,l,r):
    # Efficient QuickSort using random pivots
    
    # Base case
    if r<=l+1:
        return(a)
    
    # Recursive calls
    else:
        a,pivot=Partition_RS(a,l,r)
        a=QuickSort_random(a,l,pivot-1)
        a=QuickSort_random(a,pivot,r)
        return(a)
    
def test_quicksort(a):
    assert np.array(QuickSort_random(a,0,len(a))).all()==np.sort(a).all()
    print('Passed')

a = [i for i in range(20)]
shuffle(a)
print(a)
test_quicksort(a)
print(QuickSort_random(a,0,len(a)))
```

	[14, 6, 16, 13, 0, 12, 2, 18, 8, 7, 3, 11, 1, 4, 10, 17, 19, 15, 9, 5]
    Passed
    [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19]

---

#### 3. Conclusions

Jupyter notebooks are available for all algorithms on my [GitHub repository](https://github.com/nadanai263/datasciportfolio). 