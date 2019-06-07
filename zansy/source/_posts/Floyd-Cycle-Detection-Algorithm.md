title: Floyd Cycle Detection Algorithm
author: zansy
tags:
  - 女娲补天
categories:
  - 解题观察日记
date: 2019-06-07 14:25:50
---
Floyd判圈算法(Floyd Cycle Detection Algorithm)，又称龟兔赛跑算法(Tortoise and Hare Algorithm)，是一个可以在有限状态机、迭代函数或者链表上判断是否存在环，求出该环的起点与长度的算法。
<!--more-->

## 介绍
如果有限状态机、迭代函数或者链表上存在环，那么在某个环上以不同速度前进的2个指针必定会在某个时刻相遇。

同时显然地，如果从同一个起点(即使这个起点不在某个环上)同时开始以不同速度前进的2个指针最终相遇，那么可以判定存在一个环，且可以求出2者相遇处所在的环的起点与长度。

## LeetCode相关题
### 287
#### 描述
[Find the Duplicate Number](https://leetcode.com/problems/find-the-duplicate-number/)
给出n+1个无序数，数组中值为1~n，找出重复的数字（必存在也只存在一个）。

约束如下：
1. 不能改变原数组
2. 空间复杂度O(1) 
3. 时间复杂度最多O(n<sup>2</sup>)
4. 重复数字可能不止重复一次

#### 思路
快慢指针思想，fast 和 slow 是指针, nums[slow] 走的每一步。fast每次走两步，slow每次走一步，最终走了slow走了n步与fast相遇，fast走了2*n。

注意 nums 数组中的数字都是在 1 到 n 之间的(在数组中进行游走不会越界)，这也是题目中设置**n+1个无序数，数组中值为1~n**的用意。

因为有重复数字的出现, 所以这个游走必然是成环的, 环的入口就是重复的元素，例如[2, 5, 9, 6, 9, 3, 8, 9, 7, 1]

 0 | 1 |2 |3 |4 |5 |6 |7 |8 |9 
:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:
 2| 5 | 9 |6 |9 |3 |8|9|7|1


只走一步的慢指针构造成链表就是：2-><font color = 'red'>[9]</font>->1->5->3->6->8->7-><font color = 'red'>[9]</font>，也就是在[9]处循环，9就称为该环的入口。

即数组配合下标，抽象成链表问题，按照寻找链表环入口的思路来做。

快慢指针会在环内的[9]->1->5->3->6->8->7->[9]任何一个节点追上，不一定是在[9]处相碰，事实上会在7处碰上。因此，必须另起一个for循环定位环入口位置[9]。这里需要数学证明。

既然快慢指针在环内的某处已经相碰了。那么，第二个for循环遍历时，慢指针还是在不停的绕环走，但是必定和find指针在环入口处相碰。

#### 参考代码
```Java
class Solution {
    public int findDuplicate(int[] nums) {
       int tortoise = nums[0];
        int hare = nums[0];
        do {
            tortoise = nums[tortoise];//每次走一步
            hare = nums[nums[hare]];//每次走两步
        } while (tortoise != hare);

        // Find the "entrance" to the cycle.
        int finder = nums[0];
        while (finder != tortoise) {
            finder = nums[finder];
            tortoise = nums[tortoise];
        }
        return finder;
    }
}
```

### 142
[Linked List Cycle II](https://leetcode.com/problems/linked-list-cycle-ii/)