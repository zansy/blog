title: Algorithms Part II
author: zansy
tags: []
categories:
  - 读书摘录
date: 2018-06-10 04:13:00
---
Notes from https://www.coursera.org/learn/algorithms-part2/home/week/1 
<!--more-->
## Undirected Graphs
### Introduction to Graphs
- Euler tour: a cycle that uses each edge exactly once, go look around the graph and touch every edge in it.
- Hamilton tour: a cycle that uses each vertex exactly once.
- minimal spanning tree: the shortest set of edges, or the best way to connect all of the vertices.

### Graph API
- the adjacency list representation, that's where we keep a vertex index array where, for every vertex, we maintain a list of the vertices that are adjacent to that.

### Depth-First Search
- Goal: Find all vertices connected to s (and a corresponding path).

```
//pseudo cod
To visit a vertex v :
    - Mark vertex v as visited.
    - Recursively visit all unmarked vertices adjacent to v.
//code
private void dfs(Graph G, int v)
{
    marked[v] = true;
    for (int w : G.adj(v))
        if (!marked[w])
        {
            dfs(G, w);
            edgeTo[w] = v;
        }
} 
```

#### 深度优先搜索的非递归实现
- 思路：用到栈s来存储，用一个数组visited（初始所有元素为false，代表未访问）来做访问的标记。
- 具体步骤：从给定的搜索的第一个顶点loc开始，将其压入堆栈，并标记visited[loc]为true表示已访问；之后循环进行如下操作，只要栈不为空，取出最上方元素，将其记录（本文直接打印，若有需要，可将其存入数组or Vector），然后去打所有的邻接顶点，只要还未访问（visited相应值为false），就将其压入栈，并标记为已访问。当栈中元素为0时，算法结束时，而所有访问过的点，均被标记过，并被压入栈中过，只要图是连通的，那么就做到所有点都被输出或记录。

### Breadth-First Search
- Breadth-first is not a recursive algorithm, it uses a queue as a axillary data structure And it's also quite simple to explain. So what we're going to do is we're going to put the source vertex on a queue and then repeat the following until the queue is empty. Remove the next vertex from the queue in order. Then add to the queue all unmarked vertices that are adjacent to these and mark them and just keep doing that until the queue is empty.
- replace `queue` with stack ,and this algorithm can work as DFS.

```
// pseudo code
BFS (from source vertex s)
    
Put s onto a FIFO queue, and mark s as visited.
Repeat until the queue is empty:
    remove the least recently added vertex v
    add each of v's unvisited neighbors to the queue
    and mark them as visited

//code
private void bfs(Graph G, int s)
{
    Queue<Integer> q = new Queue<Integer>();
    q.enqueue(s);
    marked[s] = true;
    while (!q.isEmpty())
    {
        int v = q.dequeue();
        for (int w : G.adj(v))
        {
            if (!marked[w])
            {
                q.enqueue(w);
                marked[w] = true;
                edgeTo[w] = v;
            }
        } // end for
    } // end while
} // end bfs
```

### Connected Components
- So recall an equivalence relation has these three properties. Every vertex is connected to itself. If v is connected to w then w is connected to v. And if v is connected to w and w to x then v is connected to x.
- GOAL: Preprocess graph to answer queries of the form is v connected to w? in constant time. Depth-first search. Yes.

```
// pseudo code
Connected components

initialize all vertices v as unmarked.
For each unmarked vertex v, run DFS to identify all
vertices discovered as part of the same component.

//code
private boolean[] marked;
// id[v] = (index or ID of component)
private int[] id; 
// record component index
private int count; 

public CC(Graph G) {
    marked = new boolean[G.V()];
    id = new int[G.V()];
    for (int v = 0; v < G.V(); v++) {
        if (!marked[v])
        {
            // run DFS from one vertex
            // in each component
            dfs(G, v);
            count++; 
        } // end if
    } // end for
} // end CC

private void dfs(Graph G, int v)
{
    marked[v] = true;
    // all vertices discovered in same 
    // call of dfs have same id
    id[v] = count ;
    for (int w : G.adj(v))
        if (!marked[w])
            dfs(G, w);
}
```