title: Algorithms-Part1(last update:2019.06.28)
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

*Hint:* use the modification of the union−find data discussed in the previous question.

#  Stacks and Queues

## Stacks

### linked-list implementation

```Java
//inner class
private class Node{
  String item;
  Node next;
}
```

- Stack pop

```Java
public String pop(){
  String item = first.item;
  first = first.next;
  return item;
}
```

- Stack push

```Java
public void push(String item){
  Node oldfirst = first;
  first = new Node();
  first.item = item;
  first.next = oldfirst;
}
```

![Stack push-linked-list implementation](/images/image-20190626140827899.png)

### array implementation

- **push()**: add new item at s[N].

- **pop()**: remove item from s[N-1].

![Use array s[] to store N items on stack](/images/image-20190626141049901.png)

## Queue

### linked-list implementation

```java
//inner class
private class Node{
  String item;
  Node next;
}
```

- enqueue

```Java
public void enqueue(String item){
  Node oldlast = last;
  last = new Node();
  last.item = item;
  last.next = null;
  if (isEmpty()) first = last;
  else oldlast.next = last;
}
```

- dequeue

```Java
public String dequeue(){
  String item = first.item;
  first = first.next;
  if (isEmpty()) last = null;
  return item;
}
```

### resizing array implementation

- Use array q[] to store items in queue.
- **enqueue():** add new item at q[tail].
- **dequeue():** remove item from q[head].
- Update head and tail modulo the capacity.
- Add resizing array.

## applications

- Stack applications
  - Parsing in a compiler.
  - Java virtual machine.
  - Undo in a word processor.
  - Back button in a Web browser.
  - PostScript language for printers.
  - Implementing function calls in a compiler.

### Evaluate infix expressions

- **Two-stack algorithm**. [E. W. Dijkstra]
  - Value: push onto the<u> value stack</u>.
  - Operator: push onto the<u> operator stack</u>.
  - <font color = 'red'>Left parenthesis: ignore</font>.
  - Right parenthesis: <font color = 'red'>pop operator and two values; push the result</font> of applying that operator to those values onto the operand stack

![Arithmetic expression evaluation](/images/image-20190627132425839.png)

```java
public class Evaluate{
    public static void main(String[] args){
        Stack<String> ops = new Stack<String>();
        Stack<Double> vals = new Stack<Double>();
        while (!StdIn.isEmpty()) {
            String s = StdIn.readString();
            if (s.equals("(")) ;
            else if (s.equals("+")) ops.push(s);
            else if (s.equals("*")) ops.push(s);
            else if (s.equals(")")){
                String op = ops.pop();
                if (op.equals("+")) vals.push(vals.pop() + vals.pop());
                else if (op.equals("*")) vals.push(vals.pop() * vals.pop());
            }
            else vals.push(Double.parseDouble(s));
        }
        StdOut.println(vals.pop());
    }
}
```

# Elementary Sorts

## selection sort

- selection sort
  - In iteration i, find index min of <font color ='red'>smallest remaining</font> entry.
  - <font color = 'red'>Swap</font> a[i] and a[min].

```Java
for (int i = 0; i < N; i++){
    int min = i;
    for (int j = i+1; j < N; j++)
        if (less(a[j], a[min])) min = j;
    exch(a, i, min);
}
```

![Selection sort](/images/image-20190628002123641.png)

根据位置 在数集合中找位置对应的数放入

- Running time **insensitive to input**: <font color = 'green'>Quadratic time, even if input is sorted</font>.
- Data movement is minimal: <u>Linear number of exchanges</u>.

## insertion sort

- insertion sort: In iteration i, swap a[i] with each larger entry to its left.

```Java
public static void sort(Comparable[] a){
    int N = a.length;
    //Moving from right to left, exchange a[i] with each larger entry to its left.
    for (int i = 0; i < N; i++)
        for (int j = i; j > 0; j--)
            if (less(a[j], a[j-1]))
                exch(a, j, j-1);
            else break;
}
```



![Insertion sort](/images/image-20190628002833173.png)

一个不断扩张的有序数列

- **Best case.** If *the array is in ascending order*, insertion sort makes N- 1 compares and 0 exchanges.
- **Worst case.** If *the array is in descending order* (and no duplicates), insertion sort makes ~ ½ N<sup>2</sup> compares and ~ ½ N<sup>2</sup> exchanges.

## shell sort

- Shell sort: Move entries more than one position at a time by h-sorting the array.

![an h-sorted array is h interleaved sorted subsequences](/images/image-20190628005206715.png)

![Shellsort example: increments 7, 3, 1](/images/image-20190628172255771.png)

>finding **the best increment sequence** is a research problem that has confounded people for quite a long time.

```Java
public static void sort(Comparable[] a){
    int N = a.length;
    int h = 1;
    while (h < N/3) h = 3*h + 1; // 1, 4, 13, 40, 121, 364, ...
    while (h >= 1){ // h-sort the array.
        for (int i = h; i < N; i++){
            for (int j = i; j >= h && less(a[j], a[j-h]); j -= h)
                exch(a, j, j-h);
        }

        h = h/3;
    }
}
```

跳着比较和调整，每次缩小间隔，直到完全有序

>We have to do a few extra passes to do the higher sorts but <u>the each element moves only a little bit on each path</u> and that's how Shellsort gains its **efficiency**.(subquadratic)

## Shuffling

- suppose you have a deck of cards, one of the things that you might want to try to do is to <u>simply rearrange those cards into random order</u>, that's called **shuffling**.
- Knuth shuffle
  - In iteration i, pick integer r between 0 and i uniformly at random.
  - Swap a[i] and a[r].

```Java
public static void shuffle(Object[] a) {
    int N = a.length;
    for (int i = 0; i < N; i++) {
        int r = StdRandom.uniform(i + 1);
        exch(a, i, r);
    }
}
```

> it's key that the uniform random number be **between 0 and i - 1**.

每拿到一张牌，随机插入已有的牌中。

## Convex hull

- The **convex hull** of a set of N points is <u>the smallest perimeter fence enclosing the points</u>.

![Smallest convex set containing all the points](/images/image-20190628123254775.png)

### application: motion planning

**Robot motion planning.** Find shortest path in the plane from s to t that avoids a polygonal obstacle.

![Robot motion planning](/images/image-20190628124013060.png)

Shortest path is either straight line from s to t or it is one of two polygonal chains of convex hull.

### application: farthest pair

**Farthest pair problem.** Given N points in the plane, find a pair of points with <u>the largest Euclidean distance between them</u>.

![Farthest pair problem](/images/image-20190628124429239.png)

Farthest pair of points are extreme points on convex hull.

### Graham scan

- Graham scan
  - Choose point p with <u>smallest y-coordinate</u>.(Define a **total order**, comparing by y-coordinate.)
  - <u>Sort points</u> by *polar angle with p*.(Define a total order for each point p.)
  - Consider points in order; discard unless it create a <font color = 'red'>counterclockwise turn</font>.

- Given three points a, b, and c, is a → b→ c a counterclockwise turn? equals to **is c to the left of the ray a→b ?**

![Implementing ccw](/images/image-20190628131155088.png)

![Determinant gives 2x signed area of planar triangle.](/images/image-20190628132255947.png)

- (b<sub>x</sub> − a<sub>x</sub> )(c<sub>y</sub> − a<sub>y</sub> ) − (b<sub>y</sub> − a<sub>y</sub> )(c<sub>x</sub> − a<sub>x</sub> )    `(b - a) × (c - a)`
  - If signed area > 0, then a→ b→ c is counterclockwise.
  - If signed area < 0, then a→ b→ c is clockwise.
  - If signed area = 0, then a→ b→ c are collinear.

```Java
public static int ccw(Point2D a, Point2D b, Point2D c){
    double area2 = (b.x-a.x)*(c.y-a.y) - (b.y-a.y)*(c.x-a.x);
    if (area2 < 0) return -1; // clockwise
    else if (area2 > 0) return +1; // counter-clockwise
    else return 0; // collinear
}
```

# Mergesort

## Mergesort

- Basic plan.
  - Divide array into two halves.
  - <font color = 'red'>Recursively</font> sort each half.
  - Merge two halves.

- [Mergesort](https://visualgo.net/en/sorting)
- Goal. Given two **sorted subarrays** a[lo] to a[mid] and a[mid+1] to a[hi], replace with sorted subarray a[lo] to a[hi].

![two sorted subarrays](/images/image-20190702004539414.png)

### Java implementation (Merging)

```Java
private static void merge(Comparable[] a, Comparable[] aux, int lo, int mid, int hi) {
  assert isSorted(a, lo, mid); // precondition: a[lo..mid] sorted
  assert isSorted(a, mid+1, hi); // precondition: a[mid+1..hi] sorted
  for (int k = lo; k <= hi; k++)
    aux[k] = a[k];
  int i = lo, j = mid+1;
  for (int k = lo; k <= hi; k++) {
    if (i > mid) a[k] = aux[j++];
    else if (j > hi) a[k] = aux[i++];
    else if (less(aux[j], aux[i])) a[k] = aux[j++];
    else a[k] = aux[i++];
  }
  assert isSorted(a, lo, hi); // postcondition: a[lo..hi] sorted
}
```
### Java implementation (Mergesort)
```Java
private static void sort(Comparable[] a, Comparable[] aux, int lo, int hi) {
  if (hi <= lo) return;
  int mid = lo + (hi - lo) / 2;
  sort(a, aux, lo, mid);
  sort(a, aux, mid+1, hi);
  merge(a, aux, lo, mid, hi);
}
```

![result after recursive call](/images/image-20190702005145903.png)

# Quicksort

## Quicksort

- Basic plan.
  - **Shuffle** the array.
  - **Partition** so that, for some j
    - entry a[j] is in place
    -  no larger entry to the left of j
    -  no smaller entry to the right of j
  - **Sort** each piece recursively.
- Quicksort **partitioning**. 
  - Repeat until i and j pointers **cross**.
    - Scan i from left to right so long as (a[i] < a[lo]).
    - Scan j from right to left so long as (a[j] > a[lo]).
    - Exchange a[i] with a[j]
  - When pointers **cross**.
    - Exchange a[lo] with a[j].

### Java code for partitioning

```Java
private static int partition(Comparable[] a, int lo, int hi) {
    int i = lo, j = hi+1;
    while (true) {
        while (less(a[++i], a[lo]))
            if (i == hi) break;
        while (less(a[lo], a[--j]))
            if (j == lo) break;

        if (i >= j) break;
        exch(a, i, j);
    }
    exch(a, lo, j);
    return j;
}
```

### Quicksort: Java implementation

```Java
public class Quick {
    private static int partition(Comparable[] a, int lo, int hi) {}
    public static void sort(Comparable[] a) {
        sort(a, 0, a.length - 1);
    }
    private static void sort(Comparable[] a, int lo, int hi) {
        if (hi <= lo) return;
        int j = partition(a, lo, hi);
        sort(a, lo, j-1);
        sort(a, j+1, hi);
    }
} 
```

![Quicksort trace](/images/image-20190705095059876.png)

- Quicksort is **not stable**.

选择一组数列中的第一个为基准数，置换所有比基准数小的和比基准数大的数字，最后把基准数放到整个数列中它最后该在的位子。递归地把它前后乱序的数列按同样的办法整理好。