title: 最小生成树小结
author: zansy
tags:
  - 小结
categories:
  - 解题观察日记
date: 2018-01-05 22:14:00
---
Prim和Kruskal 思路 模版
<!--more-->
### 最小生成树概念
构造连通网的最小代价生成树
### Prim算法
#### 思路
- 从一个点开始，列出到到每点的距离（如果是非周边的点，距离用极大值代替），找出最小相连边。

- 如果最小相连边的点未访问过，则标记，并行到该点处，更新到每点的距离，重复找出下一点的操作，直到点全部访问过了为止。
#### 图表示
![Prim](https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1515812966&di=2de2b3c75ca47d8d10cee0db3ee221f8&imgtype=jpg&er=1&src=http%3A%2F%2Fpic002.cnblogs.com%2Fimages%2F2011%2F282432%2F2011052215540276.jpg)

#### 所需
一个二维数组图，一个记录访问的数组，一个保存最小距离的数组，一个可活动的点。
![保存最小距离的数组](http://wx2.sinaimg.cn/mw690/b11f7046gy1fewva7y440j20li05awen.jpg)
#### 模版代码
<a href="http://acm.hdu.edu.cn/showproblem.php?pid=1863">查看原题</a>
```
#include <iostream>
using namespace std;
int main(int argc, char *argv[])
{
    int n,m,c1,c2,cost,low[105],map[105][105];
    while(cin>>n>>m&&n){
        int total=0;
        for(int i=1;i<=m;i++){//初始化邻接表
            for(int j=1;j<=m;j++){
                if(i==j)map[i][j]=0;
                else
                    map[i][j]=999999;
            }
        }
        for(int i=1;i<=m;i++){//初始化low数组
            low[i]=999999;
        }
        while(n--){
            cin>>c1>>c2>>cost;
            if(cost<map[c1][c2]){
                map[c1][c2]=map[c2][c1]=cost;
            }
        }
        low[1]=0;
        for(int i=1;i<=m;i++){
            low[i]=map[1][i];
        }
        for(int i=1;i<m;i++){
            int least=999999,leastnumber=0;
            for(int j=1;j<=m;j++){
                if(low[j]<least&&low[j]!=0){
                    least=low[j];
                    leastnumber=j;
                }
            }
            if(leastnumber!=0){
                low[leastnumber]=0;
                total+=least;
                for(int k=1;k<=m;k++){
                    if(map[leastnumber][k]<low[k]){
                        low[k]=map[leastnumber][k];
                    }
                }
            }
        }
        int flag=1;
        for(int i=1;i<=m;i++){//如果low数组中有一个点是无穷，则无最小生成树
            if(low[i]==999999){
                flag=0;
                break;
            }
        }
        if(flag==0)cout<<"?"<<endl;
        else cout<<total<<endl;
    }
    return 0;
}
```
### Kruskal
#### 思路
从所有边中依次选择代价最小的，如果该边上的顶点未被收录，将该边加入，否则舍去选择下一条，直到所有点都被收录为止。
#### 图表示
![Kruskal](http://www.xdwy.com.cn/DOWN/course/sffxysj/mnst/mnst1.files/image011.gif)
#### 模版代码
<a href="http://acm.hdu.edu.cn/showproblem.php?pid=5253">查看原题</a>
```
#include <iostream>
#include <stdio.h>
#include <algorithm>
using namespace std;
const int N=1005;
const int M=N*N;
int num[N][N];
int father[M];
struct Edge{
    int u,v,w;//本位，前位，两者间的距离
}edge[2*M];
int cmp(Edge a,Edge b){//不能省略
    return a.w<b.w;
}
int find(int x){
    if(x!=father[x]){//寻找已入集结点的父结点
        father[x]=find(father[x]);
    }
    return father[x];
}
int Kruskal(int id){
    sort(edge+1,edge+id,cmp);//将边从小到大排
    int cost=0;
    for(int i=1;i<id;i++){
        int x=find(edge[i].u);
        int y=find(edge[i].v);
        if(x!=y){
            father[x]=y;//入集
            cost+=edge[i].w;
        }
    }
    return cost;
}
int main(){
    int t,n,m;
    cin>>t;
    for(int loop=1;loop<=t;loop++){
        //cin>>n>>m;
        //？？？cin时间超限，同学说因为scanf比较快……
        scanf("%d %d",&n,&m);
        for(int i=0;i<n;i++){
            for(int j=0;j<m;j++){
                father[i*m+j]=i*m+j;//未入集点初始化
                //cin>>num[i][j];
                scanf("%d",&num[i][j]);
            }
        }
        int id=1;
        for(int i=0;i<n;i++){
            for(int j=0;j<m;j++){
                int u=i*m+j;
                if(i>0){
                    int v=(i-1)*m+j;
                    edge[id].u=u;
                    edge[id].v=v;
                    edge[id++].w=abs(num[i][j]-num[i-1][j]);//存储上边长度
                }
                if(j>0){
                    int v=i*m+j-1;
                    edge[id].u=u;
                    edge[id].v=v;
                    edge[id++].w=abs(num[i][j]-num[i][j-1]);//存储左边长度
                }
            }
        }
        int cost=Kruskal(id);
        cout<<"Case #"<<loop<<":"<<endl;
        cout<<cost<<endl;
    }
    return 0;
}
```