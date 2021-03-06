title: '动态规划看背包问题[1]'
author: zansy
tags:
  - 小结
categories:
  - 解题观察日记
date: 2019-04-27 23:19:00
---
背包问题又分为0-1背包，完全背包，多维背包，多重背包。是经典的动态规划问题
<!--more-->
### 概述
- 动态规划：通过组合子问题的解而解决整个问题。
- 动态规划和分治法的区别：动态规划分解得到的子问题往往不是相互独立的，其通过记录已解决的子问题，可以避免因使用分治法而导致的重复计算。
- 动态规划的核心是**状态**和**状态转移方程**

### 0-1背包问题描述
- 背包问题主要可以分为 0-1背包问题、完全背包、多维背包、多重背包。
- 0-1背包：给定`n种物品`和一个`容量为c`的背包，`第i种物品的重量是weights[i]，其价值为values[i]`，背包问题是如何使选择装入背包内的物品，使得装入背包中的物品的总价值最大。其中，**每种物品只有全部装入背包或不装入背包两种选择**。
- 简单例题：
![0-1背包例题](/images/pasted-1.png)
- **规划方向**通用思路：
1. 确定状态：f(i,j) 表示**把前i个物品装到容量为j的背包中的最大总重量**
2. 确定状态转移方程：f(i,j) = max{f(i-1, j),f(i-1, j-weights[i])+values[i]} 前者不放入i，后者放入i
3. 确定边界：i = 0 时为0
4. 最终答案为f(n,c)

- 图示：
![过程理解图](/images/pasted-2.png)
- 代码
```
int n = 5;
int capacity = 20;
int[] weights = {0, 2, 3, 4, 5, 9};// 第一个为0
int[] values = {0, 3, 4, 5, 8, 10};
int[][] f = new int[n + 1][capacity + 1];
for (int i = 1; i <= n; i++) {
    for (int j = 1; j <= capacity; j++) {
        if (j < weights[i]) {//如果物品太重了，就直接抛弃
            f[i][j] = f[i - 1][j];
        } else {//否则就从拿和不拿中选出一个最大值。
            f[i][j] = Math.max(f[i - 1][j], f[i - 1][j - weights[i]] + values[i]);
        }
    }
}
```

- 二维数组结果：
![二维数组结果](/images/pasted-3.png)
### 优化：一维数组
### LeetCode 相关例题解析