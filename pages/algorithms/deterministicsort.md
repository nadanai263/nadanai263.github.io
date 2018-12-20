---
layout: page
title: "Deterministic QuickSort"
date: 2018-12-20
description: Deterministic QuickSort - to be updated
---

Deterministic QuickSort - to be updated

---

#### 1. The algorithm

```python
def Partition_DS(a,l,r):
    # Use 'median of medians' as pivot
    
    c=[]
    lengtha=len(a)//5
    
    if len(a)%5==0:
        # if length a is divisible by 5
        maxi=lengtha
    else:
        # if not then we have one smaller subarray left over
        maxi=lengtha+1
        
    for i in range(maxi): 
        # split a into subarrays length 5
        subs=a[(i*5):(i*5)+5]

        if len(subs)==1:
        # if final subarray has length 1 then just return it
            c.append(subs[0])
        else:
        # otherwise call mergesort and return median
            sortedsubs=mergesort(subs)
            ind=len(sortedsubs)//2 # pick median
            c.append(sortedsubs[ind]) # append medians 
        
    pivot = DSelect(c,len(c),len(c)//2) # Choose median of medians pivot
    ind = [i for i,val in enumerate(a) if val==pivot][0] # index of pivot
    pivot = a[ind] 
    # Swap first and chosen elements
    swap5 = a[l]
    swap6 = a[ind]
    a[l] = swap6
    a[ind] = swap5
        
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
    
    return(a,i-1) # i is index of first element on RHS; i-1 is pivot index
 
def DSelect(a,length,order):

    if length==1:
        return a[0]
    else:
        a,j=Partition_DS(a,0,len(a))
        if j==order-1:
            return a[j]
        elif j>order-1:
            return DSelect(a[:j],j,order)
        elif j<order-1:
            return DSelect(a[j+1:],len(a)-j-1,order-j-1) 
        
def QuickSort_det(a,l,r):
    # Efficient QuickSort using deterministic pivots
    
    # Base case
    if r<=l+1:
        return(a)
    
    # Recursive calls
    else:
        a,pivot=Partition_DS(a,l,r)
        a=QuickSort_det(a,l,pivot-1)
        a=QuickSort_det(a,pivot,r)
        return(a)
    
def test_quicksort(a):
    assert np.array(QuickSort_det(a,0,len(a))).all()==np.sort(a).all()
    print('Passed')

a = [i for i in range(20)]
shuffle(a)
print(a)
test_quicksort(a)
print(QuickSort_det(a,0,len(a)))
```

    -

---

#### 2. Conclusions

Jupyter notebooks are available for all algorithms on my [GitHub repository](https://github.com/nadanai263/datasciportfolio). 