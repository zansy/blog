title: LeetCode 难题汇总（20200406 更新/14）
author: zansy
tags: []
categories:
  - 解题观察日记
toc: true
date: 2019-08-27 11:43:00
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

#### 128
[Longest Consecutive Sequence](https://leetcode.com/problems/longest-consecutive-sequence/)
给出一组无序数，要求返回这组数中存在的连续数组的最长长度。
如{1,2,0,1}，存在连续数组012，返回3

2019.06.06

- 解一

利用`Arrays.sort`函数排序，遍历排好序后的每一位，重复的略过，看和前一位是否是连续数组，是则目前连续数组长度++，直到出现不是的打断，更新目前最长连续数组长度，同时重新开始目前连续数组计数。
```Java
class Solution {
    public int longestConsecutive(int[] nums) {
        if (nums.length == 0) {
            return 0;
        }
        Arrays.sort(nums);
        int longestStreak = 1;
        int currentStreak = 1;
        for (int i = 1; i < nums.length; i++) {
            if (nums[i] != nums[i-1]) {
                if (nums[i] == nums[i-1]+1) {
                    currentStreak += 1;
                }
                else {
                    longestStreak = Math.max(longestStreak, currentStreak);
                    currentStreak = 1;
                }
            }
        }
        return Math.max(longestStreak, currentStreak);
    }
}
```

- 解二（超时）

设立一个查找函数，寻找数组中的特定数字。当遍历数组的每一位时，设立一个while循环，查找当前位的连续数是否在原数组中存在，存在则继续++寻找，当前连续数组长度++，等不存在+1的数则跳出循环，更新最长连续数组长度。

```Java
class Solution {
    private boolean arrayContains(int[] arr, int num) {
        for (int i = 0; i < arr.length; i++) {
            if (arr[i] == num) {
                return true;
            }
        }

        return false;
    }
    public int longestConsecutive(int[] nums) {
        int longestStreak = 0;

        for (int num : nums) {
            int currentNum = num;
            int currentStreak = 1;

            while (arrayContains(nums, currentNum + 1)) {
                currentNum += 1;
                currentStreak += 1;
            }

            longestStreak = Math.max(longestStreak, currentStreak);
        }

        return longestStreak;
    }
}
```

- 解三

解二的方法时间复杂度过高，如果只是查找一个数是否存在的话，最快捷的办法就是用哈希。
HashSet不重复地存储数组中数字，遍历数组中每一位，while循环查找HashSet中该数的连续值是否存在，不存在了就跳出循环，更新最长连续数组长度。
```Java
class Solution {
    public int longestConsecutive(int[] nums) {
        Set<Integer> num_set = new HashSet<Integer>();
        for (int num : nums) {
            num_set.add(num);
        }

        int longestStreak = 0;

        for (int num : num_set) {
            if (!num_set.contains(num-1)) {
                int currentNum = num;
                int currentStreak = 1;

                while (num_set.contains(currentNum+1)) {
                    currentNum += 1;
                    currentStreak += 1;
                }

                longestStreak = Math.max(longestStreak, currentStreak);
            }
        }

        return longestStreak;
    }
}
```
#### 164
[Maximum Gap](https://leetcode.com/problems/maximum-gap/)

给出一组无序数，要求返回这组数排序后的最长的相邻间隔长度。
如{3,6,9,1}，排序后{1,3,6,9}，(3,6)或(6,9)就是最长间隔长度，返回6 - 3 = 3。

2019.06.07
- 解一

最普通思路，排序后比对
```Java
if (nums.length < 2)return 0;
        int maxDiff = 0;
        Arrays.sort(nums);
        for (int i = 1; i < nums.length; i++){
            maxDiff = Math.max(maxDiff, nums[i] - nums[i - 1]);
        }
        return maxDiff;
```

- 解二

这感觉好难想到啊…

主要是这么一个知识点：最大的最近间隔肯定大于 最大值-最小值/总间隔。

想到这个之后，把所有数字分类，以整除该间隔后的数作为桶排序的索引。例如{3,9,21,25,29,37,43,49}，间隔为(49 - 3) / 7 ≈ 7，则其索引分别为{0,0,2,3,3,4,5,6}。相同索引的为一类，同类中的差值都比间隔小，因此最大间隔肯定不存在于其中。

于是找出同类中最大数和最小数，然后遍历这个桶数组，找出相邻类别中，较小类的最大值和较大类的最小值，二者的差值就是最有可能成为最大间隔的数。

```Java
class Solution {
    public int maximumGap(int[] nums) {
        if (nums.length < 2)return 0;
        int min = nums[0], max = nums[0];
        for (int i : nums){
            min = Math.min(min, i);
            max = Math.max(max, i);
        }
        int gap = (int)Math.ceil((double)(max - min) / (nums.length - 1));
        if (gap == 0)return 0;
        int[] bucketMin = new int[nums.length]; int[] bucketMax = new int[nums.length];
        Arrays.fill(bucketMax, Integer.MIN_VALUE); Arrays.fill(bucketMin,Integer.MAX_VALUE);
        for(int i : nums){
            int index = (i - min )/ gap;
            bucketMin[index] = Math.min(bucketMin[index], i);
            bucketMax[index] = Math.max(bucketMax[index], i);
        }
        int maxGap = Integer.MIN_VALUE;
        int previousMax = min;
        for (int i = 0; i < bucketMin.length; i++){
            if(bucketMin[i] != Integer.MAX_VALUE){
                maxGap = Math.max(maxGap, bucketMin[i] - previousMax);
                previousMax = bucketMax[i];
            }
        }
        return maxGap;
    }
}
```

#### 135
[Candy](https://leetcode.com/problems/candy/)

给出一组无序数，视为一群要糖吃的小朋友的等级，要求等级较高的小朋友要比等级较低的身边小朋友有更多的糖，另外每个小朋友都至少要有一颗糖。

2019.06.08

遍历，对每个小朋友，先看后一位的等级数，如果没有自己高但是糖比自己多，就要求有比他们多一颗的糖。再看前一位的等级数，同样的情况的话也要求比他们多一颗糖，因为前位的糖的数量较早沉淀下，因此放在后面能进行准确覆盖更新。

例题：{1,2,87,87,87,2,1}，第一次遍历下来各自得到的糖是1，2，3，1，2，2，1，不准确，索引为5的小朋友少得了糖，因此需要多次遍历确认，直到糖分配数组不再更新为止。

这题解法有很多，推荐看一下题解：[135. Candy](https://leetcode.com/articles/candy/) 和 [力扣评论区](https://leetcode-cn.com/problems/candy/comments/)
```Java
class Solution {
    public int candy(int[] ratings) {
        int[] candies = new int[ratings.length];
        Arrays.fill(candies, 1);
        boolean flag = true;
        int sum = 0;
        while (flag) {
            flag = false;
            for (int i = 0; i < ratings.length; i++) {
                if (i != ratings.length - 1 && ratings[i] > ratings[i + 1] && candies[i] <= candies[i + 1]) {
                    candies[i] = candies[i + 1] + 1;
                    flag = true;
                }
                if (i > 0 && ratings[i] > ratings[i - 1] && candies[i] <= candies[i - 1]) {
                    candies[i] = candies[i - 1] + 1;
                    flag = true;
                }
            }
        }
        for (int candy : candies) {
            sum += candy;
        }
        return sum;
    }
}
```

#### 330
[Patching Array](https://leetcode.com/problems/patching-array/)

给出一组有序正整数nums和一个数n，要求在数组中插入几个数后，数组中的不同数通过相加能够获得1-n中的任意一个数字。问最少插入多少个数才行。

2019.06.09

设置一个miss作为1-n中 目前数组组合相加无法获得的数中的最小值。这说明我们现在已经可以组合[0,miss)了，注意是开区间。

<font color = 'red'>接下去在遍历目前数组的过程中，碰到任意一个num，如果小于等于miss，说明可以通过加入它来组合出[0,miss+num)</font>，例如1，2 和 5，5大于3，无法组合出3+5-1的7；而1，2，3 和 2，2小于等于4，可以通过加入2组合出4+2-1的5。

如果我们没有遇到这样一个数，说明要把它插入到目前数组中。

举例说明，现在有数组{2,3}，要求组合出10以内的所有数字，问最少要插入多少个数。

1. 遍历之前，已知不可组合出任意数，则miss设为1，即能组合的数为0。
2. 遍历到的第一个数为2，2大于miss1，因此added计数+1，把缺失的1加入，miss因此可以加上这个缺失的num成为2。
3. 此时，nums[0] = 2 <= miss, 已有数组序列为{1,2}，miss可以为miss+=nums[0] = 4，组成0~3的任意一个数。
3. nums[1] = 3 <= 4，目前序列{1,2,3}，可组成4+3 = 7，6以内任意一个数字。
4. 6仍然不够，缺失7，需要加入7，miss = miss+7 = 14，即{1,2,3,7}能组合13以内的任何一个数字，此时满足10，退出循环。
```Java
class Solution {
    public int minPatches(int[] nums, int n) {
        long miss = 1;int added = 0;int i = 0;
        while (miss <= n) {
            if (i < nums.length && nums[i] <= miss) {
                miss += nums[i];
                i++;
            } else {
                miss += miss;
                added++;
            }
        }
        return added;
    }
}
```

## 字符串 String
### 基础
#### 87
[Scramble String](https://leetcode.com/problems/scramble-string/)

给定一个字符串 s1，我们可以把它递归地分割成两个非空子字符串，从而将其表示为二叉树。

在扰乱这个字符串的过程中，我们可以挑选任何一个非叶节点，然后交换它的两个子节点。

例如，如果我们挑选非叶节点 "gr" ，交换它的两个子节点，将会产生扰乱字符串 "rgeat" 。

我们将 "rgeat” 称作 "great" 的一个扰乱字符串。

同样地，如果我们继续将其节点 "eat" 和 "at" 进行交换，将会产生另一个新的扰乱字符串 "rgtae" 。

我们将 "rgtae” 称作 "great" 的一个扰乱字符串。

给出两个长度相等的字符串 s1 和 s2，判断 s2 是否是 s1 的扰乱字符串。

2019.06.14
```Java
class Solution {
    public boolean isScramble(String s1, String s2) {
        if(s1.length() != s2.length())return false;
        //pruning
        if (s1.equals(s2)) return true;
        //判断是否不是同字母异构
        int[] count = new int[256];
        for (int i = 0; i < s1.length(); i++){
            count[s1.charAt(i)]++;
            count[s2.charAt(i)]--;
        }
        for (int i = 0; i < s1.length(); i++){
            if(count[s1.charAt(i)] != 0)return false;
        }
        for (int i = 1; i < s1.length(); i++){
            String s1Left = s1.substring(0, i);
            String s2Left = s2.substring(0, i);
            String s1Right = s1.substring(i);
            String s2Right = s2.substring(i);
            //在当前分割处没有交换
            if(isScramble(s1Left, s2Left) && isScramble(s1Right, s2Right))
                return true;
            //在当前分割处左右交换
            s2Left = s2.substring(0, s2.length() - i);
            s2Right = s2.substring(s2.length() - i);
            if(isScramble(s1Left, s2Right) && isScramble(s1Right, s2Left))
                return true;
        }
        return false;
    }
}
```
#### 316
[Remove Duplicate Letters](https://leetcode.com/problems/remove-duplicate-letters/)

给定一个仅包含小写字母的字符串，去除字符串中重复的字母，使得每个字母只出现一次。需保证返回结果的字典序最小（要求不能打乱其他字符的相对位置）。

>输入: "cbacdcbc"
>输出: "acdb"

递归，先常规用数组记录每个字母的重复次数，再遍历字符串，对每一位进行“消除重复”的操作，即舍去重复的每一位，同时把重复的字母计数-1，直到遇到第一位后续无自身重复的时候停下。

将这一位作为字符串的分界点，查看前部分重复的字符中，出现的字典序最小的是什么字母，然后在字符串的后部分中将这一字母清除。

在字符串后部分中重复上述操作，不断找到分界点和其前半段的最小字典序字母，清除再对后部分进行下一步。

举例说明：
例题中字母的重复计数数组为c3b2a1d1，在进行消除重复的遍历过程中，在a部分停下，因为a之后的字符串cdcbc不存在它本身。看a前面的字符串，得到a是前部分最小的，答案的第一位可以定下来了，于是在a为分界点的后部分将最小的a删去。

对消除最小字母的后半部分（以之前的最小字母为分界点）进行再操作，重复计数数组为c3d1b1，在d的部分停下，查看d前最小字母为c。最小的c作为分界点，在后部分cbc中将c清除，同时得到答案的第二位c。

c的后部分只有db，d无重复且最小，得到其本身；b无重复且最小，得到其本身。得到答案acdb

2019.06.18
```Java
class Solution {
    public String removeDuplicateLetters(String s) {
        if (s.length() == 0)return "";
        int[] count = new int[26];
        int smallestPosition = 0;
        for (int i = 0; i < s.length(); i++){
            count[s.charAt(i) - 'a']++;
        }
        for (int i = 0; i < s.length(); i++){
            if (s.charAt(i) < s.charAt(smallestPosition)) smallestPosition = i;//在前置中找出最小的
            if (--count[s.charAt(i) - 'a'] == 0){//消除前置重复，剩余的字符串的第一位在剩余字符串中无重复
                break;
            }
        }
        return s.charAt(smallestPosition) + removeDuplicateLetters(s.substring(smallestPosition + 1)
                .replaceAll("" + s.charAt(smallestPosition), ""));//在后置字符串中清除目前遇到的最小字符串
    }
}
```

#### 273
[Integer to English Words](https://leetcode.com/problems/integer-to-english-words/)
将非负整数转换为其对应的英文表示。可以保证给定输入小于 2<sup>31</sup> - 1 。
```
输入: 1234567891
输出: "One Billion Two Hundred Thirty Four Million Five Hundred Sixty Seven Thousand Eight Hundred Ninety One"
```

---
从末三位开始取，窗口为3依次向前。第一次的三位不需要千数组后位，第二次千后位，第三次百万后位，第四次千万后位

专门设定一个方法，获得这三位的英文表示。

2019.06.19
```Java
private final String[] LESS_THAN_20 = {"", "One", "Two", "Three", "Four", "Five", "Six", "Seven", "Eight", "Nine", "Ten", "Eleven", "Twelve", "Thirteen", "Fourteen", "Fifteen", "Sixteen", "Seventeen", "Eighteen", "Nineteen"};
    private final String[] TENS = {"", "Ten", "Twenty", "Thirty", "Forty", "Fifty", "Sixty", "Seventy", "Eighty", "Ninety"};
    private final String[] THOUSANDS = {"", "Thousand", "Million", "Billion"};
    public String numberToWords(int num) {
        if (num == 0)return "Zero";
        int i = 0; String result = "";
        while (num > 0){
            if(num % 1000 != 0){
                result = extractEndThree(num % 1000) + THOUSANDS[i] +" "+ result;
            }
            num /= 1000;
            i++;
        }
        return result.trim();
    }
    private String extractEndThree(int num){
        if(num == 0) return "";
        else if (num < 20)return LESS_THAN_20[num] + " ";
        else if (num < 100) return TENS[num / 10] + " " + extractEndThree(num % 10);
        else
            return LESS_THAN_20[num / 100] + " Hundred " + extractEndThree(num % 100);
    }
```
## 树 Tree
### 基础
#### 145
[Binary Tree Postorder Traversal](https://leetcode.com/problems/binary-tree-postorder-traversal/)

给定一个二叉树，返回它的 后序 遍历。

2019.06.25
- 解一 递归

后序遍历，用递归的话直接先左子树后右子树最后放入根。
```Java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public List<Integer> postorderTraversal(TreeNode root) {
        List<Integer> postOrder = new LinkedList<Integer>();
        if (root != null) {
            postOrder.addAll(postorderTraversal(root.left));
            postOrder.addAll(postorderTraversal(root.right));
            postOrder.add(root.val);
        }
        return postOrder;
    }
}
```
- 解二 非递归

利用LinkedList中的add(0,root)函数，将读取到的根结点设置到最后。先从左子树读起，如果左子树是叶子结点，就再读右子树，如果右子树是叶子结点就输出。整个队列是把新的添加到最开始的一位。

思路是 后序其实就是先序的颠倒。
```
[3,9,20,null,null,15,7]
    3
   / \
  9  20
    /  \
   15   7
```
例如如上的一棵二叉树，它的后序序列构造过程应该是这样的：
```
3 
 left = null
20 3 
 left = null
7 20 3 
15 7 20 3 
9 15 7 20 3 
```
```Java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public List<Integer> postorderTraversal(TreeNode root) {
        List<Integer> postOrder = new LinkedList<Integer>();
        Stack<TreeNode> stack = new Stack<>();
        if(root == null) return postOrder;
        stack.push(root);
        while (!stack.isEmpty()){
            TreeNode current = stack.pop();
            postOrder.add(0, current.val);
            if (current.left != null)
                stack.push(current.left);
            if (current.right != null)
                stack.push(current.right);
        }
        return postOrder;
    }
}
```
## 二分查找 Binary Search
### 基础
#### 154
[Find Minimum in Rotated Sorted Array II](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array-ii/)

假设按照升序排序的数组在预先未知的某个点上进行了旋转。

( 例如，数组 [0,1,2,4,5,6,7] 可能变为 [4,5,6,7,0,1,2] )。

请找出其中最小的元素。

注意数组中可能存在重复的元素。

示例 1：
```
输入: [1,3,5]
输出: 1
```
示例 2：
```
输入: [2,2,2,0,1]
输出: 0
```
2020.04.06

-----
二分查找，可以参考#153的第二种解法，拿mid元素和跟右边界元素对比，找出旋转的轴点，轴点那就是最小的数。

如果右边元素 > mid元素，就是大概45012这样，说明轴点肯定不在右边，更新右边界=mid；
如果右边界 < mid元素，举例45612，说明轴点会出现在右边，更新左边界=mid+1（注意一定要+1）；
如果右边界 = mid元素，没办法判断轴点出现在哪，就一步步减小右边界。

```Java
class Solution {
    public int findMin(int[] nums) {
        int left = 0, right = nums.length - 1, mid;
        while (left <= right){
            mid = left + (right - left) / 2;
            if (nums[mid] < nums[right]) right = mid;
            else if (nums[mid] > nums[right]) left = mid + 1;
            else right = right - 1;
        }
        return nums[left];
    }
}
```
