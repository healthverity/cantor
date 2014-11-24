Cantor
======

Cantor provides utilities for estimating the cardinality
of large sets.

The algorithms herein are parallelizable, and a Hadoop
wrapper class is provided for convenience.

It employs most of the HyperLogLog++ algorithm as seen in 
[this paper](http://research.google.com/pubs/pub40671.html), 
excluding the sparse scheme, and using a simple linear 
interpolation instead of kNN. In addition, it can use MinHash 
structures to estimate cardinalities of intersections of these 
sets, as described in 
[this blog post](http://tech.adroll.com/blog/data/2013/07/10/hll-minhash.html).

Both HyperLogLog and MinHash require a precision
parameter. Basic guidelines are available as follows,
and `HLLCounter.MIN_P = 4 <= p <= 18 = HLLCounter.MAX_P`.

####HyperLogLog p @ 99.7% Confidence
p   |    Relative Error
---:|---:
4   |    75%
5   |    65%
6   |    47%
7   |    32%
8   |    23%
9   |    16%
10  |    10%
11  |    8%
12  |    5%
13  |    4%
14  |    2.5%
15  |    2%
16  |    1.3%
17  |    1%
18  |    0.7%

####MinHash k @ 99% Confidence
**Relative Error** | **Intersection Size -->** |        |      |       | *
:------------------|--------------------------:|-------:|-----:|------:|-----:
-                  | 0.01%                     | 0.1%   |1.0%  | 5.0% |10.0%
100%               | 90000                     | 9000   |900   | 170   |75
50%                | 313334                    | 31334  |3134  | 587   |280
25%                | -                         | 116800 |11520 | 2208  |1040
10%                | -                         | -      |68455 | 13128 |6210

This MinHash k table can be generated by using `minhash_k.py` in the `utils`
directory. For now, the only requirement is scipy, which you can install with
`pip install -r utils/requirements.txt`. Then, for example, you can do:

```
%> ./utils/minhash_k.py --jaccard 0.0001 --error 1 --confidence 0.99
MinHash k:	       90000
Error at k:	       1.0
%> ./utils/minhash_k.py --jaccard 0.01 --error 0.25 --confidence 0.99
MinHash k:	       11520
Error at k:	       0.25
%> ./utils/minhash_k.py --jaccard 0.01 --error 0.25 --confidence 0.90
MinHash k:	       4800
Error at k:	       0.25
```

Additional information is available with `./utils/minhash_k.py --help`.