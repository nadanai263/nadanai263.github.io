---
layout: page
title: "Strassen's algorithm for matrix multiplication"
date: 2018-11-29
description: Strassen
---


#### 1. The algorithm

```python
def strassen(X,Y):
    
    # Multiply X, Y which are even-n square numpy matrices
    # Have to be powers of 2
    
    assert X.shape==Y.shape
    
    size = X.shape[0]
    
    if size==1:
        return(X*Y)
    
    else:
    
        divide = math.floor(size/2)

        A = X[:divide,:divide]
        B = X[:divide,divide:]
        C = X[divide:,:divide]
        D = X[divide:,divide:]

        E = Y[:divide,:divide]
        F = Y[:divide,divide:]
        G = Y[divide:,:divide]
        H = Y[divide:,divide:]

        P1 = strassen(A, F-H)
        P2 = strassen(A+B, H)
        P3 = strassen(C+D, E)
        P4 = strassen(D, G-E)
        P5 = strassen(A+D, E+H)
        P6 = strassen(B-D, G+H)
        P7 = strassen(A-C, E+F)

        XYtop = np.concatenate((P5+P4-P2+P6, P1+P2), axis=1)
        XYbottom = np.concatenate((P3+P4, P1+P5-P3-P7), axis=1)
        XY = np.concatenate((XYtop,XYbottom), axis=0)

        return(XY)

def test_strassen(X,Y):
    assert strassen(X,Y).all()==np.matmul(X,Y).all()
    print('Passed')

X = np.random.random((8,8))
Y = np.random.random((8,8))

test_strassen(X,Y)
strassen(X, Y)
```

    Passed
    Out[171]:
    array([[2.55596279, 1.94783815, 1.10329285, 2.30437997, 3.69951132,
            2.62029632, 2.38280403, 3.07778555],
           [1.52833142, 1.92019922, 0.78909696, 1.57048572, 2.51380782,
            1.49848432, 1.87051234, 1.73546606],
           [2.0459175 , 1.86083439, 0.93051027, 2.06539343, 3.32804502,
            2.34142695, 2.00949828, 2.77255579],
           [1.4830342 , 1.40267057, 0.70692158, 1.06232912, 1.80067324,
            1.29414781, 1.57865288, 0.88626149],
           [2.71149346, 2.29872734, 1.11132668, 2.56906291, 3.77715065,
            2.4782436 , 2.85456021, 2.78861926],
           [2.48602132, 2.49249884, 1.32698918, 2.22082118, 3.66552026,
            2.60752235, 2.44163521, 3.15223396],
           [1.80675943, 2.23486216, 0.97437879, 1.79508611, 3.07729639,
            1.85804229, 2.05261341, 2.36328019],
           [2.55738162, 1.4226754 , 0.90247665, 2.20558671, 2.90664071,
            2.3333175 , 2.26134369, 2.29634003]])


#### 2. Conclusions

Jupyter notebooks are available for all algorithms on my [GitHub repository](https://github.com/nadanai263/datasciportfolio). 