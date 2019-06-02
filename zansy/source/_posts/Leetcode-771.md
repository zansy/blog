title: Leetcode 771. Jewels and Stones
author: zansy
tags:
  - 水
categories:
  - 解题观察日记
date: 2018-03-29 11:37:00
---
两组字符串J和S，返回S字符串中有多少J字符串中的字母。
<!--more-->
<a href="https://leetcode.com/problems/jewels-and-stones/description/">查看原题</a>
### 题意
两组字符串J和S，返回S字符串中有多少J字符串中的字母。
### 思路
### 代码
```
int have=0,i,j;
    for(i=0;i<strlen(S);i++){
        for(j=0;j<strlen(J);j++){
            if(S[i]==J[j]){
                have++;
            }
        }
    }
return have;

```