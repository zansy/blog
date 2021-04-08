title: 剑指offer刷题记录（20210409 更新/1）

author: zansy

tags:
  - 小结

categories:
  - 解题观察日记

toc: true

date: 2021-04-09 01:59:00

---
拜托offer快快来，简单记录读书笔记和专门的刷题记录
<!--more-->
## 1. 数组中重复的数字（easy）
[数组中重复的数字](https://leetcode-cn.com/problems/shu-zu-zhong-zhong-fu-de-shu-zi-lcof/)

找出数组中重复的数字。

在一个长度为 n 的数组 nums 里的所有数字都在 0～n-1 的范围内。数组中某些数字是重复的，但不知道有几个数字重复了，也不知道每个数字重复了几次。请找出数组中任意一个重复的数字。

示例 1：
```
输入：
[2, 3, 1, 0, 2, 5, 3]
输出：2 或 3 
```
2021.04.09

---
简单的题就应该想出多种办法解决，首先可以用HashSet或者HashMap不赘述，这里用的是原地重排，既然n个数中只有0-n-1才算不重复，那就把数组重排为nums[i] = i就好了。
```java
class Solution {
    public int findRepeatNumber(int[] nums) {
        for (int i = 0; i < nums.length; i++){
            int flag = 0;
            while (nums[i] != i){
                int shouldGo = nums[i];
                if (nums[i] == nums[shouldGo])return nums[i];
                int temp = nums[i];
                nums[i] = nums[shouldGo];
                nums[shouldGo] = temp;
            }
        }
        return 0;
    }
}
```