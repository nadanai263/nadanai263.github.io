---
layout: page
title: "Selection Sort"
date: 2018-11-29
description: SelectionSort is a simple but inefficient sorting algorithm, compared to MergeSort.
---


#### 1. The algorithm

```python
def selsort(a0):
    # Selection sort
    # 1. Find minimum value of array a
    # 2. Append to b, then remove value from a
    # 3. Repeat
    # Complexity = theta(n^2) = O(n^2)
    
    a = a0.copy() # Make copy of original
    m = len(a)
    
    b = []
    for i in range(m):
        n = len(a)
        minimum = a[0]
        minimum_index = 0

        for j in range(n):
            if a[j] < minimum:
                minimum = a[j]
                minimum_index = j
        del(a[minimum_index])
        b.append(minimum)
    
    return(b)
    
def test_selsort(a):
    assert np.sort(a).all()==np.array(selsort(a)).all()
    print('Passed')

a = [i for i in range(50)]
shuffle(a)
print(a)
test_selsort(a)
b=selsort(a)
print(b)
```

    [6, 2, 49, 23, 15, 21, 7, 22, 19, 18, 16, 3, 30, 8, 44, 32, 26, 1, 48, 33, 0, 25, 35, 17, 20, 27, 4, 38, 29, 39, 5, 28, 13, 37, 11, 46, 31, 36, 12, 43, 24, 47, 14, 9, 41, 45, 42, 40, 10, 34]
    Passed
    [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 32, 33, 34, 35, 36, 37, 38, 39, 40, 41, 42, 43, 44, 45, 46, 47, 48, 49]


#### 2. Conclusions

Jupyter notebooks are available for all algorithms on my [GitHub repository](https://github.com/nadanai263/datasciportfolio). 