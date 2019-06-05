title: LeetCode 难题汇总（20190605 更新/5）
author: zansy
tags: []
categories:
  - 解题观察日记
toc: true
date: 2019-05-29 11:43:00
---
Hard学习题，分类/题意/思路/代码。
<!--more-->
## 数组 Array
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

2019.05.17
```java
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

#### 42
[Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/)

给出一组无序的非负整数代表山形图，问雨后能贮水多少

2019.06.05

- 解一

考虑到数组中每一位数可能的情况：成为容器的左隔板/右隔板/底。
如果是底的话，找到左边的最高位，右边的最高位，两者较小的一位就是该容器实际能存储的水位，再减去当前底的高度，就是当前作为底所能存储的水量。
```java
class Solution {
    public int trap(int[] height) {
        int ans = 0;
        for (int i = 1; i < height.length - 1; i++){
            int maxLeft = 0, maxRight = 0;
            for (int j = i; j >= 0; j--)//j = i 可能本身是左隔板
                maxLeft = Math.max(height[j], maxLeft);
            for (int j = i; j < height.length; j++) 
                maxRight = Math.max(height[j], maxRight);
            ans += Math.min(maxLeft, maxRight) - height[i];
        }
        return ans;
    }
}
```

- 解二

双重循环会不会太耗时了？可以对应每一位，分别存储它的左边最高位和右边最高位，而不必在遍历中去求。

```java
class Solution {
    public int trap(int[] height) {
        if(height.length == 0)
            return 0;
        int ans = 0;
        int[] left_max = new int[height.length];int[] right_max = new int[height.length];
        left_max[0] = height[0];right_max[height.length - 1] = height[height.length - 1];
        for (int i = 1; i < height.length; i++) {
            left_max[i] = Math.max(height[i], left_max[i - 1]);
        }
        for (int i = height.length - 2; i >= 0; i--) {
            right_max[i] = Math.max(height[i], right_max[i + 1]);
        }
        for (int i = 1; i < height.length - 1; i++) {
            ans += Math.min(left_max[i], right_max[i]) - height[i];
        }
        return ans;
    }
}
```

- 解三

已经在解二中实现时间复杂度O(n)，但是空间复杂度也为O(n)，还能不能降低？可以，本题事实上是#11一题的引申。根据#11的思路，先从距离最远的两端开始，两端中选出最长的保留，另一端缩短距离再比较。同时再对两端分别设置最大值，不断更新这个最大值，不能更新的时候说明当前数作为容器的底，最大值减去当前数就是能贮水的高度。

```java
class Solution {
    public int trap(int[] height) {
        int ans = 0;
        int left = 0, right = height.length - 1;
        int leftMax = 0, rightMax = 0;
        while (left < right){
            if(leftMax > height[left]){
                ans += leftMax - height[left];
            }else leftMax = height[left];

            if(rightMax > height[right]){
                ans += rightMax - height[right];
            }else rightMax = height[right];

            if(height[left] < height[right]) left++;
            else right--;
        }
        return ans;
    }
}
```

#### 45
[Jump Game II](https://leetcode.com/problems/jump-game-ii/)
给出一组无序的非负整数，每个数字是指在其索引的位置上能往右走的最大步数，问在第一个位置上，最少走几步能走到最末位。（假设一定能走到最末位
关于样例{2,3,1,1,4}的遍历情况如图

 i | curEnd| curFarthest 
:-:|:-:|:-:
0|2|0
1|4|2
2|4|2
3|4|4

2019.05.29
```java
class Solution {
    //事实上，这是一个bfs
    //Input = [2,3,1,1,4]
    //第一层 2 （起始位置数字） 第一层能到达的最远数字是3,1。其中1是最远位置数字
    //第二层 3 1 第二层能到达的最远数字是1,1,4。其中4是最远位置数字
    //一共跳了两次（到达第一层最后一个数字2跳一次，到达第二层最后一个数字3跳一次）
    //Note: You can assume that you can always reach the last index.
    public int jump(int[] nums) {
        int jumps = 0;
        int curEnd = 0;//当前层的最后一个位置
        int curFarthest = 0;//用来记录当前层跳一次能到达的最远位置（也就是下一层的最后一个位置）
        //注意i<=nums.length-2。因为我们最终要落在倒数第二层上,由倒数第二层跳到nums.length-1位置。nums.length-1即最末位4肯定在倒数第一层上
        for (int i = 0; i < nums.length - 1; i++) {
            curFarthest = Math.max(curFarthest, i + nums[i]);//比较本层最远能跳到什么位置
            if (i == curEnd) {//到达这一层的最后一个位置后，该进入下一层了
                jumps++;//跳到了下一层
                curEnd = curFarthest;//下一层的结束位置是上一层所能到达的最远位置
            }
            //如果第k层的curFarthest>=nums.length-1了,那么说明向下跳一层就能完成任务了。
            //因此，在k层的最后一个位置，我们进行jumps++和curEnd=curFarthest操作进入第k+1层之后，再也不会向下跳了:
            //因为i==curEnd不可能被满足了(i<=nums.length-2 而 curEnd>=nums.length-1)

            //下面这个判断不加也行(只要继续i++自然会退出循环), 加了反而更慢(可能是因为if判断耗时比较多)
            // if(curEnd>=nums.length-1) break;
        }
        return jumps;
    }
}
```

#### 123
[Best Time to Buy and Sell Stock III](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/submissions/)
给出一组无序数prices，表示商品在不同日期（索引）时对应的不同价格。可以最多进行两次交易，但每次只能保持一次完整的交易，即买入新的前必须卖出旧的。

遍历每一位，对每一位都思考是否是第一次买进的最低值，此时第一次卖出是否利润最大化，如果在此刻进行第二次交易买入是否价位是当前最低的，目前是否总利润最大

2019.05.30
```Java
class Solution {
    public int maxProfit(int[] prices) {
        int hold1stValue = Integer.MAX_VALUE;
        int Assume1stProfit = 0;
        int hold2ndValue = Integer.MAX_VALUE;
        int OverallProfit = 0;
        for(int currentPrice: prices) {
            hold1stValue = Math.min(hold1stValue, currentPrice);
            Assume1stProfit = Math.max(Assume1stProfit, currentPrice - hold1stValue);
            hold2ndValue = Math.min(hold2ndValue, currentPrice - Assume1stProfit);
            OverallProfit = Math.max(OverallProfit, currentPrice - hold2ndValue);
        }
        return OverallProfit;
    }
}
```

#### 188
[Best Time to Buy and Sell Stock IV](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iv/)

当k超过prices的长度（特别地，超过长度的一半）时，问题就转换为了任意交易次数，对应问题#122。

根据动态规划思想，通用解题思路如下：
1. 确定状态：`DP[i][j]` 表示在前i次交易在j天中选择的最大利润。
2. 确定状态转移方程：
	- 在第j天什么也不做：`dp[i][j] = dp[i][j-1]`
	- 在第j天卖掉手中股票。前提是在前j-1天买了股票。`prices[j] + 目前最小负债`。其中目前最小负债为衡量前次是否有买入状况，即下一种情况。
	- 在第j天买入当前股票。可交易次数-1。表达式为`DP[i - 1][j - 1] - prices[j]`。要使当前利润最大，表达式应为`目前最小负债 =  Math.max(目前最小负债, （进行买入操作）t[i - 1][j - 1] - prices[j]);`注意：目前最小负债在初始时假设买入第一天的股票，即`int 最小负债 =  -prices[0];`

3. 确定边界：k = 0 时为0
4. 最终答案为DP(k, prices.length - 1)

这题还有一个解法是一维数组，待改进，太困了。

2019.05.31

```java 
class Solution {
    public int maxProfit(int k, int[] prices) {
        if (k >= prices.length / 2) return greedy(prices);
        int[][] DP = new int[k + 1][prices.length];
        for (int i = 1; i <= k; i++) {
            int tmpMax =  -prices[0];
            for (int j = 1; j < prices.length; j++) {
                DP[i][j] = Math.max(DP[i][j - 1], prices[j] + tmpMax);
                tmpMax =  Math.max(tmpMax, DP[i - 1][j - 1] - prices[j]);
            }
        }
        return DP[k][prices.length - 1];
    }
    private int greedy(int[] prices) {
        int profit = 0;
        for (int i = 1; i < prices.length; i++)
            if (prices[i] > prices[i - 1]) profit += prices[i] - prices[i - 1];
        return profit;
    }
}
```