---
layout: page
title: "ClosestPair"
date: 2018-11-29
description: Strassen
---


#### 1. The algorithm

```python
def mergesortby(pairs,sortby):
#    Return sorted array by either x or y column    
#    sortby=0 corresponds to sorting by x
#    sortby=1 corresponds to sorting by y
    
    # First split into two arrays
    n = pairs.shape[0] 
    A = pairs[:math.floor(n/2),:]
    B = pairs[math.floor(n/2):,:]

    # Base case
    if A.shape[0]==1 and B.shape[0]==1:
        if A[0,sortby]<B[0,sortby]:
            C = np.concatenate((A,B),axis=0)
        else:
            C = np.concatenate((B,A),axis=0)
            
    elif A.shape[0]==1 and B.shape[0]==2:
        
        B = mergesortby(B, sortby)
        
        if A[0,sortby]<B[0,sortby]:
            C = np.concatenate((A,B),axis=0)
        elif A[0,sortby]<B[1,sortby]:
            C = np.vstack([B[0,:], A[0,:], B[1,:]])
        else:
            C = np.vstack([B[0,:], B[1,:], A[0,:]])
        
    else:      
        # Recursive calls
        A = mergesortby(A, sortby)
        B = mergesortby(B, sortby)
        
        # Merge
        C = np.zeros((n,2))
        i=0
        j=0
        for k in range(n):
            if i==A.shape[0]:
                C[k:,:]=B[j:,:]
                break
            elif j==B.shape[0]:
                C[k:,:]=A[i:,:]
                break
            elif A[i,sortby]<B[j,sortby]:
                C[k,:]=A[i,:]
                i+=1
            else:
                C[k,:]=B[j,:]
                j+=1   
                
    return (C)

def ClosestPair(Px,Py):
    
    n = Px.shape[0] 
    
    # Base case
    if n==3:
        
        Px1 = Px[0,:]
        Px2 = Px[1,:]
        Px3 = Px[2,:]
        
        distances = [
            np.linalg.norm(Px1-Px2),
            np.linalg.norm(Px2-Px3),
            np.linalg.norm(Px1-Px3)        
        ]
        
        pairs = [
            (Px1,Px2),
            (Px2,Px3),
            (Px1,Px3)          
        ]
        
        best = distances[0]
        P,Q = pairs[0]
        for i in range(len(distances)):
            if distances[i]<best:
                best = distances[i]
                P,Q = pairs[i]
            else:
                pass

        return(P,Q)
        
    elif n==2:

        P = Px[0,:]
        Q = Px[1,:]

        return(P,Q)
    
    else:
            
        Q = Px[:math.floor(n/2),:]
        R = Px[math.floor(n/2):,:]

        # O(n) sorting of Qy starting from ordered Py
        Qx = Q
        Qy = []
        for i in range(n):
            if (Qx[0,0]<=Py[i,0])&(Py[i,0]<=Qx[-1,0]):
                Qy.append(Py[i,:])
            else:
                pass
        Qy = np.array(Qy)

        # O(n) sorting of Ry starting from ordered Py
        Rx = R
        Ry = []
        for i in range(n):
            if (Rx[0,0]<=Py[i,0])&(Py[i,0]<=Rx[-1,0]):
                Ry.append(Py[i,:])
            else:
                pass
        Ry = np.array(Ry)
        
        p1,q1 = ClosestPair(Qx,Qy)
        p2,q2 = ClosestPair(Rx,Ry)

        D1 = np.linalg.norm(p1-q1)
        D2 = np.linalg.norm(p2-q2)
        delta = min(D1,D2)

        # Closest split pair

        # O(n) construction of selected points in vertical strip
        xbar = Px[math.floor(n/2),0]
        Sy = []    
        for i in range(n):
            if abs(Py[i,0]-xbar)<delta:
                Sy.append(Py[i,:])
            else:
                pass
        Sy = np.array(Sy)

        ny = Sy.shape[0]
        best = delta
        bestpair = None
        for i in range(ny-1):
            for j in range(i+1, min(i+7,ny)):
                pi,qi = Sy[i,:], Sy[j,:]
                if np.linalg.norm(pi-qi)<best:
                    best = np.linalg.norm(pi-qi)
                    bestpair = (pi,qi)

        if bestpair!=None:
            (pi,qi)=bestpair
            return(pi,qi)        
        else:
            if D1<D2:
                return(p1,q1)
            else:
                return(p2,q2)

np.random.seed()
pairs = np.random.randn(20,2)
Px = mergesortby(pairs,0)
Py = mergesortby(pairs,1)

P,Q = ClosestPair(Px,Py)

plt.plot(pairs[:,0], pairs[:,1],'o');
plt.plot(P[0],P[1],'o'); 
plt.plot(Q[0],Q[1],'o'); ax=plt.gca(); ax.set_aspect('equal'); plt.show()
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

<img src="/assets/images/CP.png" width="50%" alt="me" align="center" hspace="40" vspace="40">

#### 2. Conclusions

Jupyter notebooks are available for all algorithms on my [GitHub repository](https://github.com/nadanai263/datasciportfolio). 