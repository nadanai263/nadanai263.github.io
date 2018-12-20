---
layout: page
title: "Karatsuba algorithm for integer multiplication"
date: 2018-11-29
description: The Karatsuba algorithm is a fast algorithm for multiplying together integers, which beats the standard O(N^2) algorithm by using a recursive divide and conquer design.
---

The Karatsuba algorithm is a fast algorithm for multiplying together integers, which beats the standard O(N^2) algorithm by using a recursive divide and conquer design.

---

#### 1. The algorithm

```python
# Karatsuba Multiplication (optimised)

def karatsuba_full(x,y):
    # Given a length-n number x, we can express it as two length-n/2 numbers 
    # shifted appropriately (i.e. multiplied by 10^(n/2))
    
    # To string
    X = str(int(x))
    Y = str(int(y))
   
    sizeX = len(X)
    sizeY = len(Y)

    # 1. Base case
    if sizeX==1 or sizeY==1:
        return(x*y)
        
    else:
        
        m = min(sizeX,sizeY)
        split = math.floor(m/2)
        
        a = int(X[:-split])
        b = int(X[-split:])              
        c = int(Y[:-split])
        d = int(Y[-split:])
        
        # Recursion calls
        R1 = karatsuba_full(a,c) 
        R2 = karatsuba_full(b,d)
        R3 = karatsuba_full(a+b,c+d) # these may be different lengths        
        return(10**(split*2)*R1 + 10**(split)*(R3 - R1 - R2) + R2) # Keep correct base
    
def test_karatsuba(a,b):
    assert karatsuba_full(a,b)==a*b
    print('Passed')   

test_karatsuba(7006652,123123)
karatsuba_full(3141592653589793238462643383279502884197169399375105820974944592,2718281828459045235360287471352662497757247093699959574966967627) 
```

    Passed
    8539734222673567065463550869546574495034888535765114961879601127067743044893204848617875072216249073013374895871952806582723184

---

#### 2. Conclusions

Jupyter notebooks are available for all algorithms on my [GitHub repository](https://github.com/nadanai263/datasciportfolio). 