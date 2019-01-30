---
layout: page
title: Algorithms
description: implementations of some famous algorithms in python
---

  * 30 Jan 2019 » [Karger](/pages/algorithms/karger.html) Karger's randomised contraction algorithm randomly contracts edges on a graph, and finds minimum cuts with a small but nonzero probability. Repeating this process many times results in increasing probability of finding the minimum cut. A naive algorithm can therefore be useful, thanks to the laws of probability.

  * 20 Dec 2018 » [QuickSort](/pages/algorithms/quicksort.html) Randomized QuickSort is an example of a randomized algorithm which sorts an array in O(Nlog(N)) time, on average. This is due to the fact that an efficient division of the array should result in subproblems of roughly equal size; and that random pivot choices are likely to achieve this division on average.

  * 29 Nov 2018 » [ClosestPair](/pages/algorithms/closestpair.html) This is a more involved example of a divide and conquer algorithm; it uses results of MergeSort as well a trick to calculate only a small number of split pairs, or pairs of points from either side of the divide. 

  * 29 Nov 2018 » [Strassen](/pages/algorithms/strassen.html) Strassen's magical algorithm beats the standard matrix multiplication because it computes seven, instead of eight independent operations at each recursive call. How did he come up with it? No one knows!

  * 29 Nov 2018 » [CountInversions](/pages/algorithms/countinversions.html) The number of inversions in an array can be counted with a simple modification of MergeSort.

  * 29 Nov 2018 » [SelectionSort](/pages/algorithms/selectionsort.html) This is a simple but inefficient sorting algorithm, compared to MergeSort.

  * 29 Nov 2018 » [MergeSort](/pages/algorithms/mergesort.html) This is the canonical example of a recursive divide and conquer algorithm. It recursively divides the input array, resulting in log(N) levels; at each level the arrays are merged in N time, resulting in an overall O(Nlog(N)) performance. 

  * 29 Nov 2018 » [Karatsuba](/pages/algorithms/karatsuba.html) The Karatsuba algorithm is a fast algorithm for multiplying together integers, which beats the standard O(N^2) algorithm by using a recursive divide and conquer design.

[Go back](/index.html).
