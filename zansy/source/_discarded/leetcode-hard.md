title: LeetCode 难题汇总（20190517 更新/1）
author: zansy
tags: []
categories:
  - 解题观察日记
toc: true
date: 2019-05-16 22:33:00
---
Hard学习题，分类/题意/思路/代码。
<!--more-->
## 数组
### 基础
#### 41
[First Missing Positive](https://leetcode.com/problems/first-missing-positive/)

给出一组未排序的数，需要找到缺失的最小正整数，样例如下：

Input | Output
:-:|:-:
[1,2,0] |3
[3,4,-1,1] | 2
[7,8,9,11,12] | 1
[-1,4,2,1,9,10] | 3

思路出来的话其实并不难。假设一个理想状态：一组打乱的连续正整数，正常排好序的话，每位数和它目标位置的索引应该是 `nums[i] = i+1`关系。

思路扩充到一组打乱的不连续正整数，也可以根据这个关系进行数组排序，然后遍历该排序后的数组，找到第一个空位即可。

再扩充到不连续的正负整数混杂数，只要关心正数排序后位置即可，找出第一个不符合`nums[i] = i+1`关系的数组索引index，index+1就是该数组缺失的最小正整数。
>run in O(n) time and uses constant extra space

难点在于时间复杂度要求O(n)，空间复杂度要求常数级别，那么首先考虑原地排序，原地排序考虑交换，只关心正整数的排序，根据`nums[i] = i+1`，把每一位正整数放到该在的地方，交换目标位置的数。同时观察换来的目标位置的数，也为它进行置换。前句话包含了一个while而不是if条件。对每个需要置换的数进行考量，要求是正整数，且可去的位置数组不越界，且目标位置上的数本身是不符合`nums[i] = i+1`关系的。

一个小细节：如果该数组是从1开始的连续正整数，那么直接输出它的长度+1。比如说[1,2]，输出3即可。
```
class Solution {
    public int firstMissingPositive(int[] nums) {
        int missedNumber = nums.length+1;
        for(int i = 0; i < nums.length; i++){
            /*注意是while
            当发现位置上的数并不符合条件时，就建立循环把每一位换来的数字送往应该在的位置
            若目标位置上已经是正确的数字则不必改动*/
            while (nums[i] > 0 && nums[i] < nums.length && nums[i] != nums[nums[i]-1]){
                
                //和最终位置上的数字交换
                int temp = nums[nums[i]-1];
                nums[nums[i]-1] = nums[i];
                nums[i] = temp;
            }
        }
        for(int i = 0; i < nums.length; i++){
            if(nums[i] != i+1) {
                missedNumber = i + 1;
                break;
            }
        }
        return missedNumber;
    }
}
```
