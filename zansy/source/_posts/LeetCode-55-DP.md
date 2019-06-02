title: '从 LeetCode#55 入门动态规划'
author: zansy
tags:
  - 女娲补天
categories:
  - 解题观察日记
date: 2019-05-24 05:28:00
---
参考资料：[Jump Game Solution](https://leetcode.com/articles/jump-game/)
<!--more-->

## 题目
[55. Jump Game](https://leetcode.com/problems/jump-game/)
给出一组无序的非负整数，每个数字是指在其索引的位置上能往右走的最大步数，问在第一位是否能走到数组最后一位。

- 样例1

>**Input:** [2,3,1,1,4]
**Output:** true
**Explanation:** Jump 1 step from index 0 to 1, then 3 steps to the last index.

- 样例2

>**Input:** [3,2,1,0,4]
**Output:** false
**Explanation:** You will always arrive at index 3 no matter what. Its maximum jump length is 0, which makes it impossible to reach the last index.

## 简介
- **动态规划（Dynamic programming，简称DP）**通过把原问题分解为相对简单的子问题的方式求解复杂问题。常常适用于有重叠子问题和最优子结构性质的问题。

- 适用情况：
	1. **最优子结构性质**。如果问题的最优解所包含的子问题的解也是最优的，我们就称该问题具有最优子结构性质（即满足最优化原理）
	2. **无后效性**。即子问题的解一旦确定，就不再改变，不受在这之后、包含它的更大的问题的求解决策影响。
	3. **子问题重叠性质**。子问题重叠性质是指在用递归算法自顶向下对问题进行求解时，每次产生的子问题并不总是新问题，有些子问题会被重复计算多次。

- 动态规划算法正是利用了子问题重叠性质，对每一个子问题只计算一次，然后将其计算结果保存在一个表格中，当再次需要计算已经计算过的子问题时，只是在表格中简单地查看一下结果，从而获得较高的效率。

## 题解

- Usually, solving and fully understanding a dynamic programming problem is a 4 step process:
	1. Start with the recursive backtracking solution
	2. Optimize by using a memoization table (top-down3 dynamic programming)
	3. Remove the need for recursion (bottom-up dynamic programming)
	4. Apply final tricks to reduce the time / memory complexity

### 第一阶段：回溯递归
在这一阶段我们很容易想到用递归，从第一个位置开始，跳到每一个能到的位置，当无法再前进的时候就回溯到上一个位置继续换个长度走，一直循环这个操作直到跳到最后一个位置。
这个阶段产生的代码为：
```java
public class Solution {
    public boolean canJumpFromPosition(int position, int[] nums) {
        if (position == nums.length - 1) {
            return true;
        }

        int furthestJump = Math.min(position + nums[position], nums.length - 1);
        for (int nextPosition = position + 1; nextPosition <= furthestJump; nextPosition++) {
            if (canJumpFromPosition(nextPosition, nums)) {
                return true;
            }
        }

        return false;
    }

    public boolean canJump(int[] nums) {
        return canJumpFromPosition(0, nums);
    }
}
```
改进：每次先尝试跳最大的步数：
```
// Old
for (int nextPosition = position + 1; nextPosition <= furthestJump; nextPosition++)
// New
for (int nextPosition = furthestJump; nextPosition > position; nextPosition--)
```

例如例题：

Index |0 |1 |2 |3 |4 |5 |6
:-: |:-: |:-: |:-: |:-: |:-: |:-: |:-: 
nums |1 |5 |2 |1 |0 |2 |0

从index0位置开始，先尽量大地跳到1，根据1对应的5步，尽量大地跳到6

### 第二阶段：动态规划至顶向下
这一阶段可以理解成优化过的回溯，我们存储结果，不必每次再重新计算。对数组中的每一个元素提出一个”可以达到最末位/不可以/未知“的标签组合，简写成G/B/U

首先将所有的标签设置成Unknow，除了最后一个
在递归回溯的时候把结果保存在memo组中

Index |0 |1 |2 |3 |4 |5 |6
:-: |:-: |:-: |:-: |:-: |:-: |:-: |:-: 
nums |2 |4 |2 |1 |0 |2 |0
memo |G |G |B |B |B |G |G 

这一阶段产生的代码：
```
enum Index {
    GOOD, BAD, UNKNOWN
}

public class Solution {
    Index[] memo;

    public boolean canJumpFromPosition(int position, int[] nums) {
        if (memo[position] != Index.UNKNOWN) {
            return memo[position] == Index.GOOD ? true : false;
        }

        int furthestJump = Math.min(position + nums[position], nums.length - 1);
        for (int nextPosition = position + 1; nextPosition <= furthestJump; nextPosition++) {
            if (canJumpFromPosition(nextPosition, nums)) {
                memo[position] = Index.GOOD;
                return true;
            }
        }

        memo[position] = Index.BAD;
        return false;
    }

    public boolean canJump(int[] nums) {
        memo = new Index[nums.length];
        for (int i = 0; i < memo.length; i++) {
            memo[i] = Index.UNKNOWN;
        }
        memo[memo.length - 1] = Index.GOOD;
        return canJumpFromPosition(0, nums);
    }
}
```

### 第三阶段：动态规划从底至上

从上至下到从下至上的转换关键在于回溯的消除。回溯的消除通常是通过反转”从上至下“阶段的步骤来达成的。这一阶段仍然将所有的先置为U，除了最后一位是G

Index |0 |1 |2 |3 |4 |5 |6
:-: |:-: |:-: |:-: |:-: |:-: |:-: |:-: 
nums |2 |4 |2 |1 |0 |2 |0
memo |U |U |U |U |U |U |G 

但不同的是我们从倒数第二位开始倒着看。对每一位算出能跳到的最远距离，然后在距离里的这些点看是否有能跳到最后一位的。该位置能跳到的最大距离中的位置有能跳到最末位的，显而易见该位置是也能跳到最末位的。

步骤如下：第五位最多能跳到第六位，第六位有标记G，那么第五位也可以标记一个G；第四位不能跳；第三位能跳到第四位，但是没有G，则自己也没有标签G；…；第一位能跳到第五位，检查第二位到第五位，但凡有一个位置是能跳到最末位的，则第二位自己也是能够跳到最末位的。

最终结果如下：

Index |0 |1 |2 |3 |4 |5 |6
:-: |:-: |:-: |:-: |:-: |:-: |:-: |:-: 
nums |2 |4 |2 |1 |0 |2 |0
memo |G |G |B |B |B |G |G

这一阶段产生的代码如下：
```
enum Index {
    GOOD, BAD, UNKNOWN
}

public class Solution {
    public boolean canJump(int[] nums) {
        Index[] memo = new Index[nums.length];
        for (int i = 0; i < memo.length; i++) {
            memo[i] = Index.UNKNOWN;
        }
        memo[memo.length - 1] = Index.GOOD;

        for (int i = nums.length - 2; i >= 0; i--) {
            int furthestJump = Math.min(i + nums[i], nums.length - 1);
            for (int j = i + 1; j <= furthestJump; j++) {
                if (memo[j] == Index.GOOD) {
                    memo[i] = Index.GOOD;
                    break;
                }
            }
        }

        return memo[0] == Index.GOOD;
    }
}
```

### 第四阶段：贪心算法
在这一阶段，仍然是最后一位设置为G，但是这一步已经不必用数组了，只注意最左的标签为G的位。第一步的话，索引6的位置是最左的标签为G的位，那么设置这样一个最左索引变量，这个变量在之后的操作中会不断得到更新。

接下去从右向左遍历每一位，检查它能跳到的范围中是否包含一个最左标签为G的位置（`currPosition + nums[currPosition] >= leftmostGoodIndex`），能的话更新最左标签索引，设置为当前索引。

这一阶段代码如下：
```
class Solution {
    public boolean canJump(int[] nums) {
        int lastCanJumpToTheEnd = nums.length-1;
        for (int i = lastCanJumpToTheEnd - 1; i >= 0; i--){
            if(i + nums[i] >= lastCanJumpToTheEnd) lastCanJumpToTheEnd = i;
        }
        return lastCanJumpToTheEnd == 0;
    }
}
```