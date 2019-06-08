title: Algorithms-Part1
author: zansy
tags: []
categories:
  - 读书摘录
date: 2019-06-08 22:21:15
---
Notes from https://www.coursera.org/learn/algorithms-part1/
<!--more-->
## Quick Find
 - an algorithm for solving *the dynamic connectivity problem*, called **Quick-find**. 

- Data structure: 
  - Integer array id[] of length N. With each entry equal to its index.
  - Interpretation: p and q are connected iff they have the same id.


![0, 5 and 6 are connected](/images/image-20190608222745616.png)

- **Find**: Check if p and q have the same id.
- **Union**: To merge components containing p and q, change all entries whose id equals id[p] to id[q].

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
- **Union**. To merge components containing p and q,set the id of p's root to the id of q's root.
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