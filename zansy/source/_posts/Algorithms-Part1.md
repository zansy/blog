title: Algorithms-Part1
author: zansy
tags: 

- Coursera

categories:
  - 上课笔记
date: 2019-06-08 22:21:15
---
Notes from https://www.coursera.org/learn/algorithms-part1/
<!--more-->

# Union-Find

- Apply the union-find data type to solve problems in science, engineering, and industry.
- Define the union-find (or disjoint sets) data type.
- Compare the performance of different algorithms for the union-find data type.
- Design different algorithms (quick find, quick union, weighted quick union, path compression) for the union-find data type.
- Develop Java implementations of different algorithms for the union-find data type.
- Use the parent-link representation to represent tree data structures.

## Quick Find
 - an algorithm for solving *the dynamic connectivity problem*, called **Quick-find**. 

- Data structure: 
  - Integer array id[] of length N. With each entry equal to its index.
  - Interpretation: p and q are connected iff they have the same id.


![0, 5 and 6 are connected](/images/image-20190608222745616.png)

- **Find**: Check if p and q have the same id.
- **Union**: To merge components containing p and q, <font color = "red">change all entries whose id equals id[p] to id[q]</font>.

### Java implementation: 
```java
public class QuickFindUF{
    private int[] id;
    public QuickFindUF(int N)
    {
        id = new int[N];
        for (int i = 0; i < N; i++)
            id[i] = i;
    }
    public boolean connected(int p, int q)
    { return id[p] == id[q]; }
    public void union(int p, int q)
    {
        int pid = id[p];
        int qid = id[q];
        for (int i = 0; i < id.length; i++)
            if (id[i] == pid) id[i] = qid;
    }
}
```
- <font color = 'red'>Quick-find defect</font>:
  - Union too expensive (N array accesses).
  - Trees are flat, but too expensive to keep them flat.

## Quick Union
- Quick Find is too slow for huge problems. **Quick-union** is an alternative.
- Data structure.
  - Integer array id[] of length N.
  - Interpretation: id[i] is parent of i.
  - Root of i is id[id[id[...id[i]...]]].(keep going until it doesn’t change)

![root of 3 is 9](/images/image-20190608224811928.png)
- **Find**. Check if p and q have the same root.
- **Union**. To merge components containing p and q, <font color = 'red'>set the id of p's root to the id of q's root</font>.
### Java implementation
```Java
public class QuickUnionUF {
    private int[] id;
    public QuickUnionUF(int N) {
        id = new int[N];
        for (int i = 0; i < N; i++) id[i] = i;
    }
    private int root(int i) {
        while (i != id[i]) i = id[i];
        return i;
    }
    public boolean connected(int p, int q) {
        return root(p) == root(q);
    }
    public void union(int p, int q) {
        int i = root(p);
        int j = root(q);
        id[i] = j;
    }
}
```
- <font color = 'red'>Quick-union defect</font>:
  - Trees can get tall.
  - Find too expensive (could be N array accesses).

## Quick-Union Improvements

### Improvement 1: weighting

- Weighted quick-union:
  - Modify quick-union to avoid tall trees.
  - Keep track of size of each tree (number of objects).
  - Balance by linking root of smaller tree to root of larger tree.

![weighted](/images/image-20190619143633671.png)

- **Data structure**: Same as quick-union, but <font color = "red">maintain extra array sz[i] to count number of objects in the tree rooted at i</font>.

- **Find**. Identical to quick-union `return root(p) == root(q);`

- **Union**. Modify quick-union to:

  - Link root of smaller tree to root of larger tree.
  - Update the sz[] array.

  ```java
  int i = root(p);
  int j = root(q);
  if (i == j) return;
  if (sz[i] < sz[j]) { id[i] = j; sz[j] += sz[i]; }
  else { id[j] = i; sz[i] += sz[j]; } 
  ```

  

- **Proposition**. Depth of any node x is at most **lg N**.

### Improvement 2: path compression

- **Quick union with path compression**. Just after computing the root of p, set the id of each examined node to point to that root.

![before](/images/image-20190619163705844.png)

![after](/images/image-20190619163733989.png)

- **Two-pass implementation**: <font color = 'red'>add second loop to root() to set the id[] of each examined node to the root</font>.
- **Simpler one-pass variant**: Make every other node in path point to its<font color = 'red'> grandparent (thereby halving path length)</font>.

```Java
private int root(int i){
  while (i != id[i]){
    id[i] = id[id[i]];
    i = id[i];
  }
  return i;
}
```

- It's amazing fact that was eventually proved by Friedman and Sachs, that *there is no linear time algorithm for the union find problem*. But **weighted quick union with path compression** in practice is close enough that it's going to enable the solution of huge problems.

## Union-find applications

- Percolation: A model for many physical systems:
  - N-by-N grid of sites.
  - Each site is open with probability p (or blocked with probability 1 – p).
  - System percolates <u>iff top and bottom are connected by open sites</u>.

![Percolation](/images/image-20190620022258204.png)

- Monte Carlo simulation:
  - Initialize N-by-N whole grid to be blocked.
  - Declare random sites open until top connected to bottom.
  - Vacancy percentage estimates p*.

- Dynamic connectivity solution to estimate percolation threshold:
  - Create an object for each site and name them 0 to N 2 – 1.
  - Sites are in same component if connected by open sites.
  - Percolates iff any site on bottom row is connected to site on top row. (<font color = 'red'>brute-force algorithm: N<sup>2</sup> calls to connected()</font>)

![check whether an N-by-N system percolates](/images/image-20190620022753355.png)

- UPDATE: **Introduce 2 virtual sites** (and connections to top and bottom). Percolates iff virtual top site is connected to virtual bottom site. (<font color ='red'>efficient algorithm: only 1 call to connected()</font>)

![Clever trick](/images/image-20190620023043451.png)

>Q. How to model opening a new site?
>A. Mark new site as open; connect it to all of its adjacent open sites. (<font color = 'red'>up to 4 calls to union()</font>)

![opening a new site](/images/image-20190620023248968.png)

##QUIZ

### 1. Social network connectivity.

Given a social network containing n members and a log file containing m timestamps at which times pairs of members formed friendships, design an algorithm to determine the earliest time at which all members are connected (i.e., every member is a friend of a friend of a friend ... of a friend). Assume that the log file is sorted by timestamp and that friendship is an equivalence relation. The running time of your algorithm should be mlogn or better and use extra space proportional to n.

---

*Hint:* union−find.

### 2. Union-find with specific canonical element.

Add a method 𝚏𝚒𝚗𝚍() to the union-find data type so that 𝚏𝚒𝚗𝚍(𝚒) returns the largest element in the connected component containing *i*. The operations, 𝚞𝚗𝚒𝚘𝚗(), 𝚌𝚘𝚗𝚗𝚎𝚌𝚝𝚎𝚍(), and 𝚏𝚒𝚗𝚍() should all take logarithmic time or better.

For example, if one of the connected components is {1, 2, 6, 9}, then the 𝚏𝚒𝚗𝚍() method should return 9 for each of the four elements in the connected components.

---

*Hint:* maintain an extra array to the weighted quick-union data structure that stores for each root 𝚒 the large element in the connected component containing 𝚒.

### 3. Successor with delete.

Given a set of n*n* integers S = { 0, 1, ... , n-1 } and a sequence of requests of the following form:

- Remove x from S
- Find the *successor* of x: the smallest y in S such that *y* ≥ *x*.

design a data type so that all operations (except construction) take logarithmic time or better in the worst case.

---

