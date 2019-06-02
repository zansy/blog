title: Boyer-Moore Voting Algorithm 摩尔投票算法
author: zansy
tags:
  - 女娲补天
categories:
  - 解题观察日记
date: 2019-05-18 23:35:00
---
波义尔摩尔投票算法是一种使用线性时间复杂度和常数空间复杂度来找到数组的主要元素（出现超过一半次数的元素）。
<!--more-->

核心就是**对拼消耗**。

看到有个解释很有趣：
>玩一个诸侯争霸的游戏，假设你方人口超过总人口一半以上，并且能保证每个人口出去干仗都能一对一同归于尽。最后还有人活下来的国家就是胜利。再多就大混战，最差所有人都联合起来对付你（对应你每次选择作为计数器的数都是众数），或者其他国家也会相互攻击（会选择其他数作为计数器的数），但是只要你们不要内斗，最后肯定你赢。最后能剩下的必定是自己人。

在做[leetcode第229题](https://leetcode.com/problems/majority-element-ii/)时，要求挑出长度为n的无序数组中出现大于n/3次的数，额外要求空间O(1)时间线性，于是学习了一下这个算法。

先看样例：[1,1,1,3,3,2,2,2]
因为数出现次数大于三分之一，所以最多只返回两个不同的数。因此有三个不相同的即可互相抵消，从而设立候选人1，候选人2，并记录它们分别获得的票数。
每一步记录如下：

![解题思路](/images/pasted-21.png)

最后从总票数中再进行计算，找出票数多于三分之一的。这一步不可省略，因为前面只是筛选出了票数最多的两位候选人，但仍然要保障票数多于三分之一。例如[3,2,3]，虽然最终剩下3和2，但2的票数只有1，因此舍去。

根据解题思路可以获得代码如下：
```Java
class Solution {
    public List<Integer> majorityElement(int[] nums) {
        List<Integer> answer = new ArrayList<Integer>();
        int candidate1 = 0,candidate2 = 0,count1 = 0, count2 = 0;
        for(int i= 0; i < nums.length; i++){
            if(nums[i] == candidate1){//先分别和两位候选人比较
                count1++;
            }else if(nums[i] == candidate2){
                count2++;
            }else if(count1 == 0){//如果有候选位空槽则直接带着当前票数进入
                candidate1 = nums[i];
                count1 = 1;
            }else if(count2 == 0){
                candidate2 = nums[i];
                count2 = 1;
            }else {
                count1--;
                count2--;
            }
        }
        count1 = count2 = 0;
        for(int i = 0; i < nums.length; i++){
            if(nums[i] == candidate1) count1++;
            else if(nums[i] == candidate2) count2++;
        }
        if(count1 > nums.length/3) answer.add(candidate1);
        if(count2 > nums.length/3) answer.add(candidate2);
        return answer;
    }
}
```