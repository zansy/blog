title: Leetcode 1. Two Sum(HashMap)
author: zansy
tags:
  - 水
categories:
  - 解题观察日记
date: 2018-06-18 09:55:00
---
Given int[] and a target number, return the sequence of the two numbers that can be added to get the target number.
<!--more-->

<a href="https://leetcode.com/problems/two-sum/description/">See original question</a>
### Ideas
Go through the given int[], for every number A, store its partner's value as a key and A's location as a value.

### Code
```
public class Solution {
    public int[] twoSum(int[] numbers, int target) {
        HashMap<Integer,Integer> map = new HashMap<>();

        for (int i = 0; i < numbers.length; i++) {
            if (map.get(numbers[i]) != null) {
                int[] result = {map.get(numbers[i]), i};
                return result;
            }
            map.put(target - numbers[i], i);
        }
        int[] result = {};
        return result;
    }
}
```