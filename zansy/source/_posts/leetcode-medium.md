title: LeetCode 中等题汇总（20200418 更新/60）
author: zansy
tags: []
categories:
  - 解题观察日记
date: 2019-08-27 12:48:00
toc: true
---
Medium思考题，分类/题意/思路/代码。
<!--more-->
## 数组 Array
### 基础
#### 80 Remove Duplicates from Sorted Array II
[Remove Duplicates from Sorted Array II](https://leetcode.com/problems/remove-duplicates-from-sorted-array-ii/submissions/)

给出一组排好序的数，要求去除数组里数，使其中数最多重复两次。返回数组长度。

Given | Modified
:-: | :-:
[1,1,1,2,2,3]|[1,1,2,2,3]
[0,0,1,1,1,1,2,3,3]|[0,0,1,1,2,3,3]
[0,0,0,0,0]|[0,0]

---
说是medium但其实也很水，保持两个数组概念，和#26 #27类似，设定变量，判定当前数组当前位和新数组前两位是否相同，不同则存入。覆盖原数组。

2019.05.16
```java
class Solution {
    public int removeDuplicates(int[] nums) {
        int flag = 2;
        for(int i = 2;i < nums.length;i++) {
            if (nums[i] != nums[flag - 2]) {//关键步骤
                nums[flag++] = nums[i];
            }
        }
        return flag;
    }
}
```

#### 134 Gas Station
[Gas Station](https://leetcode.com/problems/gas-station/)

给出两组数，第一组gas分别表示每个加油站可加的油，第二组cost分别表示目前加油站到达下一加油站所消耗的油量。要求从某一加油站加满油出发，顺时针循环所有的加油站并回到出发的加油站，返回该出发加油站的索引。如果中途油不够无法完成循环，则返回-1。

2019.05.18

- 解一

指定了是顺时针循环的话，难度就降低了很多。先预设结果是不可能，station = -1。遍历每一站，对每一站尝试进行“加油、消耗”操作，如果结果为负，说明在前往下一站的路上油不足，跳出循环看下一站，如果结果为正说明走得通。数组循环的话并不难，将其前面的位数都排到后面来即可，例如：

数组 | 0 | 1 | 2 |  3 | 4
:-:|:-:|:-:|:-:|:-:|:-:|:-:
gas | 1|2|3|4|5
cost| 3| 4| 5| 1| 2|

从index为3（即第四个）加油站出发的话，循环到回到原处就是将两组的前三位复制到末尾。可构思存在这样一个数组：

数组 | 0 | 1 | 2 |  3 | 4|5|6|7
:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:
gas | 1|2|3|4|5|1|2|3
cost| 3| 4| 5| 1| 2|3|4|5

但因为是重复的，所以也不必再设立一个数组增加空间复杂度，只需要简单进行求余算index获得数值即可。
```java
class Solution {
    public int canCompleteCircuit(int[] gas, int[] cost) {
        int station = -1;
        for(int i = 0; i < gas.length; i++){//遍历每一站
            while(gas[i] < cost[i] && i < gas.length-1) i++;
            int sum = 0;
            for(int j = i; j < gas.length + i; j++){//对每一站作预判
                sum += gas[j%gas.length]-cost[j%gas.length];
                if(sum < 0){
                    break;
                }
            }
            if(sum >= 0)station = i;
        }
        return station;
    }
}
```

- 解二

-1的判断其实是很容易的，只要所有加油站油总量比要消耗的要少，就是无法完成循环操作，返回-1。除此以外的情况是一定有满足条件的出发加油站。

既然确定答案肯定存在，就可以大胆预设。由解一可以看到，这样的一个加油站将这个循环路程分为两段，前一段路程一旦出现亏损，即run < 0，说明肯定无法抵达下一站，但是既然预设这样一个出发站肯定存在，那么说明亏损之后肯定存在盈余，才能支撑整个循环油量的收支平衡。这里很容易想到，一段循环路程，如果要出现亏损，势必也是先盈余后亏损。因此，出现亏损的点十分重要，预示着之前的路程应该属于循环路途的末尾段，而该点之后的路程应该属于起先段，所以将该点之后的第一个加油站设为起发站。

但如何能确定这就是唯一的起发站呢？还有一点需要注意，假设了一个起发站后，如果以起发站为界，加了该起发点的油后，如果之后的路程还出现油量亏损到无法去下一站的情况，那么该假设的加油站需要舍弃，无法到达的下一站一定有充足的油量能够驶完全程。
```java
run += (gas[i] - cost[i]);
    if (run < 0){
        start = i + 1;
        run = 0;
    }
```
这一步真的很巧妙，包含了“测试每一个起发点，如果无法去下一个站点即舍弃”的操作。同时还包含了一个解题关键：在确定能完成循环的前提下，目前的路程一旦出现无法去下一站的情况，则下一站一定是起发点，同时重新计算路程油量的收支情况，不行则再次舍弃。
```java
class Solution {
    public int canCompleteCircuit(int[] gas, int[] cost) {
        int rest = 0, run = 0, start = 0;
        for (int i = 0; i < gas.length; ++i){
            run += (gas[i] - cost[i]);
            rest += (gas[i] - cost[i]);
            if (run < 0){
                start = i + 1;
                run = 0;
            }
        }
        return rest < 0 ? -1: start;
    }
}
```

#### 229 Majority Element II
[Majority Element II](https://leetcode.com/problems/majority-element-ii/)

给出两组无序数，返回所有占总数三分之一以上的数

2019.05.19

- 解一

蠢办法，先快排一下，遍历数组中每一位，直接跨越数组三分之一的长度和目标数比较，如果相同则再看之前有没有收入相同的该数，没有则收入。
```java
class Solution {
    public List<Integer> majorityElement(int[] nums) {
        List<Integer> answer = new ArrayList<Integer>();
        quickSort(nums,0,nums.length-1);
        for(int i= 0; i < nums.length-nums.length/3; i++){
            if(nums[i] == nums[i+nums.length/3]){
                if(answer.isEmpty()){
                    answer.add(nums[i]);
                }else if(answer.get(answer.size()-1) != nums[i]){
                    answer.add(nums[i]);
                }

            }
        }
        return answer;
    }
    public static void quickSort(int a[], int low, int hight) {
        int i, j, index;
        if (low > hight) {
            return;
        }
        i = low;
        j = hight;
        index = a[i]; 
        while (i < j) { 
            while (i < j && a[j] >= index)
                j--;
            if (i < j)
                a[i++] = a[j];
            while (i < j && a[i] < index)
                i++;
            if (i < j) 
                a[j--] = a[i];
        }
        a[i] = index;
        quickSort(a, low, i - 1);
        quickSort(a, i + 1, hight);
    }
}
```

- 解二 

摩尔投票算法，相同的三个数可以消除，判断留下的最终两位是否符合存在大于三分之一的条件。算法介绍可参见这篇文章：[Boyer-Moore Voting Algorithm 摩尔投票算法](https://zansy.github.io/2019/05/18/BoyerMooreVotingAlgorithm/)
```java
class Solution {
    public List<Integer> majorityElement(int[] nums) {
        List<Integer> answer = new ArrayList<Integer>();
        int candidate1 = 0,candidate2 = 0,count1 = 0, count2 = 0;
        for(int i= 0; i < nums.length; i++){
            if(nums[i] == candidate1){
                count1++;
            }else if(nums[i] == candidate2){
                count2++;
            }else if(count1 == 0){
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

#### 274 H-Index
[H-Index](https://leetcode.com/problems/h-index/)

涉及一个`h-index`的概念
>H指数的计算基于其研究者的论文数量及其论文被引用的次数。赫希认为：一个人在其所有学术文章中有N篇论文分别被引用了至少N次，他的H指数就是N。如美国耶鲁大学免疫学家理查德·弗来沃发表的900篇文章中，有107篇被引用了107次以上，他的H指数是107。

给出一组长度为n的无序数，表示该科学家的n篇论文对应的被引次数，求该科学家的H指数

2019.05.20

- 解一

很容易想到从高到底快排一次，遍历该降序数组，如果当前值大于等于其序号（从1开始而非0开始）就存入，不停覆盖。例如例题中[3,0,6,1,5]排序后[6,5,3,1,0]，第`1`篇引用次数`6`大于等于`1`，第`2`篇引用次数`5`大于等于`2`，第`3`篇引用次数`3`大于等于`3`，覆盖存入，之后的都不会进入if语句块。最后得到最终结果为3。
```java
class Solution {
    public int hIndex(int[] citations) {
        int answer = 0;
        quickSort(citations,0,citations.length-1);
        for(int i = 0; i < citations.length; i++){
            if(citations[i] >= i+1){
                answer = i+1;
            }
        }
        return answer;
    }
    public void quickSort(int[] nums, int left, int right){
        int i, j, starter;
        if(left > right)return;
        starter = nums[left];
        i = left;
        j = right;
        while (i != j){
            while (nums[j] <= starter && i < j)j--;
            while (nums[i] >= starter && i < j)i++;
            if(i < j){
                int temp = nums[i];
                nums[i] = nums[j];
                nums[j] = temp;
            }
        }
        nums[left] = nums[i];
        nums[i] = starter;

        quickSort(nums, left, i-1);
        quickSort(nums, i+1, right);
    }
}
```
- 解二 桶排序

首先想到的也是排序，桶排序的话长度是多少呢？破除常规思路，想到总共n篇文章，H指数最大为n，最小为0，设置桶排序的序号为答案，序号最长也不过是n，由此设立一个序号为0-n的数组。例如对[4,0,6,1,5]进行桶排序：

0 |1 |2 |3|4 |5
:-:|:-:|:-:|:-:|:-:|:-:
1|1|0|0|1|2

注意这里，大于n的6也被归类于序号为n的计数中，因为大于n的不管多少都不必在意其值了。然后将这个数组从后向前遍历，本质上模拟一个将原引用数组从高到低排序再数数的过程：前2篇文章不大于其序号5，前3篇文章不大于其序号4，类似于对答案再往下对一步：前3篇文章大于等于其序号，则返回当前序号3。
注意这里需要考虑[1,1]等类似情况，因此if语句中不能是count == i。
```java
class Solution {
    public int hIndex(int[] citations) {
        int[] buckets = new int[citations.length+1];
        for(int c : citations) {
            if(c >= citations.length) {
                buckets[citations.length]++;
            } else {
                buckets[c]++;
            }
        }
        int count = 0;
        for(int i = citations.length; i >= 0; i--) {
            count += buckets[i];
            if(count >= i) {
                return i;
            }
        }
        return 0;
    }
}
```

#### 275 H-Index II
[H-Index II](https://leetcode.com/problems/h-index/)

题意如#274，但这次给出的是升序数组，要求时间复杂度对数级。

---
既然是对数级，就用二分查找。
举例[0,1,4,5,6]，数组中每个值和它的h值有这样的对应关系：

数组值 |0 |1 |4|5 |6
:-:|:-:|:-:|:-:|:-:|:-:
需要对应的h值|5|4|3|2|1

在二分查找的过程中，如果某个h值满足条件（即h值小于它对应的值），我们就到前半部分继续查找；如果h值不满足条件（即h值大于它对应的值），我们就到前半部分继续查找。

2019.05.20
```java
class Solution {
    public int hIndex(int[] citations) {
        int start = 0, end = citations.length-1;
        while (start <= end){
            int mid = (end + start)/2;
            if( citations[mid] < citations.length-mid) start = mid + 1;
            else end = mid - 1;
        }
        return citations.length-start;
    }
}
```

#### 220 Contains Duplicate III
[Contains Duplicate III](https://leetcode.com/problems/contains-duplicate-iii/)

给出一组无序数nums，一个k，一个t，要求存在nums[i]和nums[j]，使得二者绝对值小于等于t，同时i与j的绝对值小于等于k。

---
思路如219，设置一个长度为k+1的窗口，例如[1,5,9,1,5,9]，k=2，t=3时，则需要设置一个长度为3的窗口，在窗口中观察是否有这样的两个数字满足绝对值小于等于t的情况。不满足则窗口往后平移一位。159，591，915，159，这样平移。

但是窗口中的数字要怎样方便快捷地判断其绝对值呢？想办法排序，但是桶排的话需要的空间太大了，要怎么样尽可能地压缩空间呢？压缩空间，就是减小排序后差值最小的两个数的距离。这里的思路是把当前数除以t，例如[1,5,9,1,5,9]，除以t = 3得到的号码牌就是[0,1,3,0,1,3]，可见1和5之间的距离被压缩了。

但是尽管相邻，也不代表1和5之间满足绝对值小于等于3的条件，这个时候仍需要参考原数值的值，因此在判断条件后，要将当前数值的号码牌和值本身一起存入一个长度为k的HashMap中。

但这一题小陷阱很多，t是绝对值，肯定不能小于0。那么为了制作号码牌的时候设置除以t+1；数组中存在负数，那么每次先减去一个设定的最小值，即加上一个最大值。

2019.05.21
```java
class Solution {
    public boolean containsNearbyAlmostDuplicate(int[] nums, int k, int t) {
        if (k == 0 || t < 0) return false;
        Map<Long, Long> map = new HashMap<Long, Long>();
        for (int i = 0; i < nums.length; i++) {
            long transferedCurrentNum = (long) nums[i] - Integer.MIN_VALUE;//解决负数问题
            long bucketId = transferedCurrentNum / ((long)t + 1);//本意是该/t，但考虑到t为0的情况，选择除以t+1
            if (map.containsKey(bucketId) ||
                    map.containsKey(bucketId - 1) && transferedCurrentNum - map.get(bucketId - 1) <= t ||
                    map.containsKey(bucketId + 1) && map.get(bucketId + 1) - transferedCurrentNum <= t)
                return true;
            if(map.entrySet().size() >= k){
                long lastBucket = ((long) nums[i-k] - Integer.MIN_VALUE) / (t + 1);
                map.remove(lastBucket);
            }
            map.put(bucketId, transferedCurrentNum);
        }
        return false;
    }
}
```

#### 55 Jump Game
[Jump Game](https://leetcode.com/problems/jump-game/)

给出一组无序的非负整数，每个数字是指在其索引的位置上能往右走的最大步数，问在第一位是否能走到数组最后一位。

---
具体解法看这一篇博文：[从 LeetCode#55 入门动态规划](https://zansy.github.io/2019/05/24/LeetCode-55-DP/)

2019.05.22
```java
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
#### 309 Best Time to Buy and Sell Stock with Cooldown
[Best Time to Buy and Sell Stock with Cooldown](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/)

给出一组无序数prices，表示商品在不同日期（索引）时对应的不同价格。可以进行无限次交易，但每次只能保持一次完整的交易，即买入新的前必须卖出旧的。并且每次交易后的第二天不能交易

---
动态规划好难啊…

2019.06.01

```java
class Solution {
    public int maxProfit(int[] prices) {
        if (prices.length == 0)
            return 0;
        int sold = 0, hold = -prices[0], rest = 0;
        for(int i = 0; i < prices.length; i++){
            int prvSold = sold;
            sold = hold + prices[i];//假设卖出当前股票后的余额
            
            hold = Math.max(hold, rest-prices[i]);//当前持有或是买入
            rest = Math.max(rest, prvSold);//rest 特殊性，因此得比较前一天卖出的余额
        }
        return Math.max(sold, rest);
    }
}
```
#### 11 Container With Most Water
[Container With Most Water](https://leetcode.com/problems/container-with-most-water/)

给出一组无序数height，表示在x轴上不同高度的线。两线可组成容器盛水，底是两线之间的距离，高是较短的一线，求能盛最多水的容器的长宽积。

2019.06.04
- 解一

暴力进行一位一位比较

```java
class Solution {
    public int maxArea(int[] height) {
       int maxArea = 0;
        for(int i = 0; i < height.length - 1; i++){
            for(int j = 1; j < height.length; j++){
                if((j - i)* Math.min(height[i],height[j]) > maxArea)
                    maxArea = (j - i)* Math.min(height[i],height[j]);
            }
        }
        return maxArea; 
    }
}
```

- 解二

思路不算难，先从距离最远的两端开始，两端中选出最长的保留，另一端缩短距离再比较，重复操作，直到间距最小。

```java
class Solution {
    public int maxArea(int[] height) {
       int left = 0, right = height.length - 1;
        int maxArea = Math.min(height[left] , height[right]) * (right);
        while (left < right){
            if(height[left] > height[right])
                right--;
            else left++;
            maxArea = Math.max(maxArea, Math.min(height[left] , height[right]) * (right - left));
        }
        return maxArea;
    }
}
```

#### 334 Increasing Triplet Subsequence
[Increasing Triplet Subsequence](https://leetcode.com/problems/increasing-triplet-subsequence/)

给出一组无序数nums，问其中是否存在三个数，nums[i] < nums[j] < nums[k],同时i < j < k。要求时间复杂度O(n)，空间复杂度O(1)。

---
把整个数组的数分成三个层次，其中两个分界点，一层小于等于small，一层大于small但是小于等于medium，一层大于medium。small、medium没有固定的值，只要能起到分隔作用即可。一旦出现第三个数，返回true。

2019.06.05

```java
class Solution {
    public boolean increasingTriplet(int[] nums) {
        int small = Integer.MAX_VALUE, medium = Integer.MAX_VALUE;
        for(int i = 0; i < nums.length; i++){
            if(nums[i] <= small) small = nums[i];
            else if (nums[i] <= medium) medium = nums[i];
            else return true;
        }
        return false;
    }
}
```
#### 287 Find the Duplicate Number
[Find the Duplicate Number](https://leetcode.com/problems/find-the-duplicate-number/)

给出n+1个无序数，数组中值为1-n，找出重复的数字（必存在也只存在一个）。

约束如下：
1. 不能改变原数组
2. 空间复杂度O(1) 
3. 时间复杂度最多O(n<sup>2</sup>)
4. 重复数字可能不止重复一次

2019.06.07
- 解一

<font color = 'green'>找出无序数组重复数字，最先想到的是Arrays.sort和HashSet。</font>

这里有约束，那就先蠢办法遍历
```Java
class Solution {
    public int findDuplicate(int[] nums) {
       int duplicate = 0;
        for( int i = 0; i < nums.length - 1; i++){
            for (int j = i + 1; j < nums.length; j++){
                if(nums[j] == nums[i]) return nums[i];
            }
        }
        return duplicate; 
    }
}
```

- 解二

用到的是龟兔赛跑算法，具体解法看这里：[Floyd Cycle Detection Algorithm](https://zansy.github.io/2019/06/07/Floyd-Cycle-Detection-Algorithm/)

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

##  字符串 String
### 基础
#### 151 Reverse Words in a String
[Reverse Words in a String](https://leetcode.com/problems/reverse-words-in-a-string/)

给定一个字符串，逐个翻转字符串中的每个单词。

说明：
- 无空格字符构成一个单词。
- 输入字符串可以在前面或者后面包含多余的空格，但是反转后的字符不能包括。
- 如果两个单词间有多余的空格，将反转后单词间的空格减少到只含一个。

---

先剪枝，再用正则替换掉多余的空格，最后split切割。时空复杂度都有点高，去看了一下高票的解答代码[Clean Java two-pointers solution (no trim( ), no split( ), no StringBuilder)
](https://leetcode.com/problems/reverse-words-in-a-string/discuss/47720/Clean-Java-two-pointers-solution-(no-trim(-)-no-split(-)-no-StringBuilder))，用while降低复杂度，先反转整个字符串，再分别反转单词，最后清除开头和中间多余的空格，感觉思路还挺独特的。

2019.06.11

```Java
class Solution {
    public String reverseWords(String s) {
        String sTrim = s.trim(); String result = "";
        sTrim = sTrim.replaceAll(" +"," ");
        String[] words = sTrim.split(" ");
        for (int i = words.length - 1; i > 0; i--){
            result += words[i] + " ";
        }
        result += words[0];
        return result;
    }
}
```

#### 49 Group Anagrams
[Group Anagrams](https://leetcode.com/problems/group-anagrams/)

给定一个字符串数组，将字母异位词组合在一起。字母异位词指字母相同，但排列不同的字符串。

---
思路和242差不多，但这里把排序好后的字符组设为HashMap的键，值为List。如果HashMap中没有见过这个字符串排列好后的字符组，就新建一个ArrayList，然后新增入当前字符串。

最后将HashMap的值返回即可。

还有一种办法，直接以26个字母出现次数数组 组成一个string作为键。具体可以看官方题解：[49. Group Anagrams](https://leetcode.com/articles/group-anagrams/)

2019.06.13
```Java
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        if (strs.length == 0) return new ArrayList<>();
        HashMap<String, List<String>> sortBychars = new HashMap<>();
        for (String str : strs){
            char[] strArray = str.toCharArray();
            Arrays.sort(strArray);
            if (!sortBychars.containsKey(String.valueOf(strArray)))
                sortBychars.put(String.valueOf(strArray), new ArrayList<>());
            sortBychars.get(String.valueOf(strArray)).add(str);
        }
        return new ArrayList<>(sortBychars.values());
    }
}
```

#### 179 Largest Number
[Largest Number](https://leetcode.com/problems/largest-number/)

给定一组非负整数，重新排列它们的顺序使之组成一个最大的整数。

>Input: [3,30,34,5,9]
>Output: "9534330"

---
首先要把输入的整数{3,30,34,5,9}换成字符串，然后首位数字大的排在前面。例如5应该排在34的前面。但这要怎么排呢？这里有一个很巧妙的办法，当5和34进行比较的时候，实质上比较的是534和345，双方各把对方加入末尾，保证位数相同，只比较了高位。

这个方法可以通过重写comparator interface 里面的compare方法实现，然后使用method Arrays.sort(array[], new Comparator<String>());

sort完了就是降序排列。

2019.06.14
```Java
class Solution {
    public String largestNumber(int[] nums) {
        //Get input intergers as strings
        String[] asStrs = new String[nums.length];
        for (int i = 0; i < nums.length; i++)
            asStrs[i] = String.valueOf(nums[i]);

        //Sort strings according to custom comparator
        Arrays.sort(asStrs, new LargerNumberComparator());

        // If after being sorted, the largest number is `0`, the entire number is zero.
        if (asStrs[0].equals("0")) {
            return "0";
        }

        // Build largest number from sorted array.
        String largestNumberStr = new String();
        for (String numAsStr : asStrs) {
            largestNumberStr += numAsStr;
        }

        return largestNumberStr;
    }
    private class LargerNumberComparator implements Comparator<String>{
        @Override
        public int compare(String a, String b){
            String order1 = a + b;
            String order2 = b + a;
            return order2.compareTo(order1);
        }
    }
}
```
#### 6 ZigZag Conversion
[ZigZag Conversion](https://leetcode.com/problems/zigzag-conversion/)

将一个给定字符串根据给定的行数，以从上往下、从左到右进行 Z 字形排列。

比如输入字符串为 "LEETCODEISHIRING"行数为 3 时，输出需要从左往右逐行读取，产生出一个新的字符串，比如："LCIRETOESIIGEDHN"。

---
思路大概就是根据给定的行数，为每一行内容设立一个字符串组，比如说题目中行数为3，就设立3个字符串组。第一步先将字符串组初始化。然后读取原给定字符串的每一个字符，分别读入到3个字符串组中，最开始逐行下移，则行数++，读完一整列后逐行上升，则行数--，最后把这三行内容直接拼接整合起来就可以了。

还有一种思路是根据数学规律：
- Characters in row 0 are located at indexes k(2⋅numRows−2)
- Characters in row numRows−1 are located at indexes k(2⋅numRows−2)+numRows−1
- Characters in inner row i are located at indexes k(2⋅numRows−2)+i and (*k*+1)(2⋅numRows−2)−i.

2019.06.15
- 解一
```Java
class Solution {
    public String convert(String s, int numRows) {
        if (numRows == 1) return s;
        List<StringBuilder> rows = new ArrayList<>();
        for (int i = 0; i < Math.min(numRows, s.length()); i++)
            rows.add(new StringBuilder());
        int curRow = 0;
        boolean goingDown = false;
        for (char c : s.toCharArray()){
            rows.get(curRow).append(c);
            if(curRow == 0 || curRow == numRows - 1)
                goingDown = !goingDown;
            curRow += goingDown ? 1 : -1;
        }
        StringBuilder ret = new StringBuilder();
        for (StringBuilder row : rows)ret.append(row);
        return ret.toString();
    }
}
```
这是官方题解里给出来的代码，思路不变，修改了一下，使可读性更高
```Java
class Solution {
    public String convert(String s, int numRows) {
        if (numRows == 1 || numRows >= s.length()) return s;
        String[] rows = new String[numRows];
        for (int i = 0; i < numRows; i++){
            rows[i] = "";
        }
        int curRow = 0, direction = 1;
        for (char c : s.toCharArray()){
            rows[curRow] += c;
            if(curRow == 0)direction = 1;
            else if (curRow == numRows - 1) direction = -1;
            curRow += direction;
        }
        String result = "";
        for (String row : rows) result += row;
        return result;
    }
}
```

#### 12 Integer to Roman
[Integer to Roman](https://leetcode.com/problems/integer-to-roman/)

罗马数字包含以下七种字符： I， V， X， L，C，D 和 M。
```
字符          数值
I             1
V             5
X             10
L             50
C             100
D             500
M             1000
```
例如， 罗马数字 2 写做 II ，即为两个并列的 1。12 写做 XII ，即为 X + II 。 27 写做  XXVII, 即为 XX + V + II 。

通常情况下，罗马数字中小的数字在大的数字的右边。但也存在特例，例如 4 不写做 IIII，而是 IV。数字 1 在数字 5 的左边，所表示的数等于大数 5 减小数 1 得到的数值 4 。同样地，数字 9 表示为 IX。这个特殊的规则只适用于以下六种情况：

I 可以放在 V (5) 和 X (10) 的左边，来表示 4 和 9。
X 可以放在 L (50) 和 C (100) 的左边，来表示 40 和 90。 
C 可以放在 D (500) 和 M (1000) 的左边，来表示 400 和 900。
给定一个整数，将其转为罗马数字。输入确保在 1 到 3999 的范围内。

2019.06.19
- 解一

仍然是用的HashMap，从最后一位开始向前遍历，每位看一下是不是在特殊情况中存在，不存在的话就再分情况获得当前位数上的结果。

通过insert把每位的结果添加到最终结果中。

这个办法的代码实在是太难看了…
```Java
class Solution {
    public String intToRoman(int num) {
        StringBuilder result = new StringBuilder();
        HashMap<Integer, String> map = new HashMap<>();
        map.put(1, "I");
        map.put(4, "IV");
        map.put(5, "V");
        map.put(9, "IX");
        map.put(10, "X");
        map.put(40, "XL");
        map.put(50, "L");
        map.put(90, "XC");
        map.put(100, "C");
        map.put(400, "CD");
        map.put(500, "D");
        map.put(900, "CM");
        map.put(1000, "M");
        int digits = 0;
        while (num > 0){
            int current = num % 10;
            if(map.containsKey((int)(Math.pow(10, digits) * current))){
                result.insert(0, map.get((int)(Math.pow(10, digits) * current)));
                System.out.println(result);
            }else {
                if(current > 5){
                    String temp = map.get((int)(Math.pow(10, digits) * 5));
                    for (int i = 0; i < current - 5; i++){
                        temp += map.get((int)(Math.pow(10, digits) * 1));
                    }
                    result.insert(0, temp);
                }else{
                    String temp = "";
                    for (int i = 0; i < current; i++){
                        temp += map.get((int)(Math.pow(10, digits) * 1));
                    }
                    result.insert(0, temp);
                }
            }
            digits++;
            num /= 10;
        }
        return result.toString();
    }
}
```
- 解二

设立两个对应的数组，遍历特殊情况values数组，给定的不断减去当前遍历到的数字，同时输出对应的字母。
```Java
class Solution {
    public String intToRoman(int num) {
        int[] values = {1000,900,500,400,100,90,50,40,10,9,5,4,1};
        String[] strs = {"M","CM","D","CD","C","XC","L","XL","X","IX","V","IV","I"};

        StringBuilder sb = new StringBuilder();

        for(int i=0;i<values.length;i++) {
            while(num >= values[i]) {
                num -= values[i];
                sb.append(strs[i]);
            }
        }
        return sb.toString();
    }
}
```

## 数学 Math
### 基础
#### 165 Compare Version Numbers
[Compare Version Numbers](https://leetcode.com/problems/compare-version-numbers/)

两个字符串，比较版本号，前者比后者新返回1，否则返回-1。当相等时返回0。注意1.0和1比较，1.001和1.01比较的情况。

---
思路很简单，字符串以正则拆分，对每一位数字进行二者比较，位数不足的以0补足。比较到不同的时候返回结果，相同的话跳出循环返回0。

2019.05.28
```java
class Solution {
    public int compareVersion(String version1, String version2) {
        String[] version1Number = version1.split("\\.");
        String[] version2Number = version2.split("\\.");
        int longestLength = Math.max(version1Number.length,version2Number.length);

        for(int i = 0; i < longestLength; i++){
            Integer tempV1, tempV2;
            if(i < version1Number.length)
                tempV1 = Integer.parseInt(version1Number[i]);
            else
                tempV1 = 0;
            if(i < version2Number.length)
                tempV2 = Integer.parseInt(version2Number[i]);
            else
                tempV2 = 0;

            int singleNumberCompare =  tempV1.compareTo(tempV2);
            if(singleNumberCompare != 0) return singleNumberCompare;
        }
        return 0;
    }
}
```
#### 8 String to Integer (atoi)
[String to Integer (atoi)](https://leetcode.com/problems/string-to-integer-atoi/)

实现一个 atoi 函数，使其能将字符串转换成整数。

首先，该函数会根据需要丢弃无用的开头空格字符，直到寻找到第一个非空格的字符为止。

当我们寻找到的第一个非空字符为正或者负号时，则将该符号与之后面尽可能多的连续数字组合起来，作为该整数的正负号；假如第一个非空字符是数字，则直接将其与之后连续的数字字符组合起来，形成整数。

该字符串除了有效的整数部分之后也可能会存在多余的字符，这些字符可以被忽略，它们对于函数不应该造成影响。

注意：假如该字符串中的第一个非空格字符不是一个有效整数字符、字符串为空或字符串仅包含空白字符时，则你的函数不需要进行转换。

在任何情况下，若函数不能进行有效的转换时，请返回 0。

假设我们的环境只能存储 32 位大小的有符号整数，那么其数值范围为 [−2<sup>31</sup>,  2<sup>31</sup> − 1]。如果数值超过这个范围，qing返回  INT_MAX (2<sup>31</sup> − 1) 或 INT_MIN (−2<sup>31</sup>) 。
```
输入: "4193 with words"
输出: 4193
解释: 转换截止于数字 '3' ，因为它的下一个字符不为数字。

输入: "words and 987"
输出: 0
解释: 第一个非空字符是 'w', 但它不是数字或正、负号。
     因此无法执行有效的转换。

输入: "-91283472332"
输出: -2147483648
解释: 数字 "-91283472332" 超过 32 位有符号整数范围。 
     因此返回 INT_MIN 

```

---
过滤出字符串中的数字部分，数字包括正负数。注意数字之前只能为空格，否则返回0。超过能表示的最小值或者最大值时返回最小值或最大值。注意要先trim去除前后空格，否则会出现输入`" "`出错的情况。

再用while循环清除掉空格，默认正数，用symbol表示正负号。遍历每个数字，通过*10+下一位进行字符串转换成int型。注意如果当前数比最大值2147483648 / 10 = 214748364 更大或者相等时要进行判断，说明将会溢出，这个时候根据参考正负数的类型返回最大值或最小值。

如果没有溢出，最后只要返回转换后的int型就行。


2019.06.20
```Java
class Solution {
    public int myAtoi(String str) {
        str = str.trim();
        if (str.isEmpty()) return 0;
        int symbol = 1, i = 0;
        while (str.charAt(i) == ' ')i++;
        if (str.charAt(i) == '-'){
            symbol = -1;
            i++;
        }else if (str.charAt(i) == '+'){
            symbol = 1;
            i++;
        }

        int temp = 0;
        while (i < str.length() && str.charAt(i) >= '0' && str.charAt(i) <= '9'){
            if (temp > Integer.MAX_VALUE / 10 || (temp == Integer.MAX_VALUE / 10 && str.charAt(i) - '0' > 7)){
                if (symbol == 1)return Integer.MAX_VALUE;
                else return Integer.MIN_VALUE;
            }
            temp = 10 * temp + (str.charAt(i++) - '0');
        }
        return symbol * temp;
    }
}
```
#### 43 Multiply Strings
[Multiply Strings](https://leetcode.com/problems/multiply-strings/)

给定两个以字符串形式表示的非负整数 num1 和 num2，返回 num1 和 num2 的乘积，它们的乘积也表示为字符串形式。

---
根据实际中的多位数乘法步骤来，从最后一位起，例如123*456，3和6先进行相乘，得到18，但是这个数存在哪儿呢？根据经验我们可以看到，结果得到的数字长度最多比两数长度和再多1，因此设立末位相乘得到的结果存入索引为2+2+1=5的数组中，18存8，进位1，计入索引为4的数组元素中。事实上这一过程就是把过程中得到的数字分别存入数组中，并对元素不断更新相乘。最终得到最后结果。
```Java
class Solution {
    public String multiply(String num1, String num2) {
        int m = num1.length(), n = num2.length();
        int[] process = new int[m + n];

        for(int i = m - 1; i >= 0; i--) {
            for(int j = n - 1; j >= 0; j--) {
                int mul = (num1.charAt(i) - '0') * (num2.charAt(j) - '0');
                int pre = i + j, cur = i + j + 1;
                int sum = mul + process[cur];

                process[pre] += sum / 10;
                process[cur] = (sum) % 10;
            }
        }
        StringBuilder sb = new StringBuilder();
        for(int p : process) if(!(sb.length() == 0 && p == 0)) sb.append(p);
        return sb.length() == 0 ? "0" : sb.toString();
    }
}
```
#### 29 Divide Two Integers
[Divide Two Integers](https://leetcode.com/problems/divide-two-integers/)
给定两个整数，被除数 dividend 和除数 divisor。将两数相除，要求不使用乘法、除法和 mod 运算符。

返回被除数 dividend 除以除数 divisor 得到的商。

---
本质思路是移位，更通俗一点，设立两个数组。

拿100 -3举例，先把二者全化成正数100和3，两个数组分别是
 3, 6,12,24,48,96,192
 1, 2, 4, 8,16,32,64
索引也分别对应。先减去96，96对应32，即3 * 32 = 96，结果中已得到32，但是100-96还有4，4再在第一个数组中找，找到3，对应1，则结果为32+1=33。

但这个方法有一点不足的地方是用了long。不用long的可以看这一篇，通过用递归消除用long：[把正数通通转为求负数](https://leetcode.wang/leetCode-29-Divide-Two-Integers.html)
也可以更好地理解第一个数组的数字设置。

2019.06.23
```Java
class Solution {
    public int divide(int dividend, int divisor) {
        if (dividend == 0)return 0;
        if (dividend == Integer.MIN_VALUE && divisor == -1)return Integer.MAX_VALUE;
        boolean negative = (dividend ^ divisor) < 0;
        long dividendLong = Math.abs((long)dividend); long divisorLong = Math.abs((long)divisor);
        int result = 0;
        for (int i = 31; i >= 0; i--){
            if ((dividendLong >> i) >= divisorLong){//找出足够大的数2^n*divisor
                result += 1 << i; //将结果加上2^n
                dividendLong -= divisorLong << i;//将被除数减去2^n*divisor
            }
        }
        return negative ? -result : result;
    }
}
```
#### 50 Pow(x, n)
[Pow(x, n)](https://leetcode.com/problems/powx-n/)

实现 pow(x, n) ，即计算 x 的 n 次幂函数。

---
递归！看到这种可分解的小问题，就应该要想到递归！主要是用了一点指数的基础公式，把x<sup>n</sup>换成(x<sup>2</sup>)<sup>n/2</sup>，同时再考虑到奇偶指数的问题就行了。注意如果指数是负数，把指数调正，x可以变成1/x。

2019.06.23
```Java
class Solution {
    public double myPow(double x, int n) {
        if (n == 0) return 1;
        if (n == Integer.MIN_VALUE){
            x = x * x;
            n = n / 2;
        }
        if (n < 0){
            n = -n;
            x = 1/x;
        }
        return (n % 2 == 0) ? myPow(x * x, n / 2) : x * myPow(x * x, n / 2);
    }
}
```
#### 365 Water and Jug Problem
[Water and Jug Problem](https://leetcode.com/problems/water-and-jug-problem/)

有两个容量分别为 x升 和 y升 的水壶以及无限多的水。请判断能否通过使用这两个水壶，从而可以得到恰好 z升 的水？

如果可以，最后请用以上水壶中的一或两个来盛放取得的 z升 水。

你允许：

- 装满任意一个水壶
- 清空任意一个水壶
- 从一个水壶向另外一个水壶倒水，直到装满或者倒空

---
纯粹的数学题。有兴趣可以看这个维基百科：[Bézout's identity](https://en.wikipedia.org/wiki/B%C3%A9zout%27s_identity)

两个桶容量分别为xy，要得到z，从描述中可以获得这样一个公式：ax+by=z。同时桶可以倒空装满，说明a和b可以是正数也可以是负数。那么接下去要做的就是找到两数的最大公约数，然后看z是不是这个最大公约数的倍数就行了。

具体证明看维基。

2019.06.24
```Java
class Solution {
    public boolean canMeasureWater(int x, int y, int z) {
        if (x + y < z)return false;
        if (x == z || y == z || x + y == z) return true;
        return z % greatestCommonDivisor(x, y) == 0;
    }
    public int greatestCommonDivisor(int a, int b){
        while (b != 0){
            int temp = b;
            b = a % b;
            a = temp;
        }
        return a;
    }
}
```
## 树 Tree
### 基础
#### 144 Binary Tree Preorder Traversal
[Binary Tree Preorder Traversal](https://leetcode.com/problems/binary-tree-preorder-traversal/)

给定一个二叉树，返回它的 前序 遍历。
```
输入: [1,null,2,3]  
   1
    \
     2
    /
   3 

输出: [1,2,3]
```
2019.06.24

- 解一 递归方法

本科算法课基础内容。先序遍历，先根后左子树右子树，用递归的话思路很清晰。
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
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> pre = new LinkedList<Integer>();
        if (root != null){
            pre.add(root.val);
            pre.addAll(preorderTraversal(root.left));
            pre.addAll(preorderTraversal(root.right));
        }
        return pre;
    }
}
```
- 解二 非递归方法

非递归的话就用栈把暂时不用的右子树先存入。优先读取左子树，同时把它对应的每一个右子树存入先进后出的栈中，这样的话，当左子树为空的时候，栈顶就是离它最近的右子树，依次取出即可。
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
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> list = new LinkedList<Integer>();
        Stack<TreeNode> rights = new Stack<TreeNode>();
        while (root != null){
            list.add(root.val);
            if (root.right != null)rights.push(root.right);
            root = root.left;
            if (root == null && !rights.isEmpty())root = rights.pop();
        }
        return list;
    }
}
```
#### 94 Binary Tree Inorder Traversal
[Binary Tree Inorder Traversal](https://leetcode.com/problems/binary-tree-inorder-traversal/)

给定一个二叉树，返回它的中序 遍历。

2019.06.24
- 解一 递归

思路清晰
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
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> inOrder = new LinkedList<Integer>();
        if (root != null){
            inOrder.addAll(inorderTraversal(root.left));
            inOrder.add(root.val);
            inOrder.addAll(inorderTraversal(root.right));
        }
        return inOrder;
    }
}
```
- 解二

其实和#144的思路差不多，用栈存储左子树，然后一步步往左子树方向走。当走到叶子结点的时候，从栈顶取出左子树的左叶子结点。

判断左节点是否存在且未遍历过，是则进入输出当前节点，出栈。
接着判断右节点是否存在且未遍历过，是则进入。如果栈无元素则退出。
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
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> inOrder = new LinkedList<Integer>();
        Stack<TreeNode> stack = new Stack<TreeNode>();
        TreeNode current = root;
        while (current != null || !stack.isEmpty()){
            while (current != null){
                stack.push(current);
                current = current.left;
            }
            current = stack.pop();
            inOrder.add(current.val);
            current = current.right;
        }
        return inOrder;
    }
}
```
#### 104 Binary Tree Level Order Traversal
[Binary Tree Level Order Traversal](https://leetcode.com/problems/binary-tree-level-order-traversal/)

给定一个二叉树，返回其按层次遍历的节点值。 （即逐层地，从左到右访问所有节点）。

例如:
给定二叉树: [3,9,20,null,null,15,7],
```
    3
   / \
  9  20
    /  \
   15   7
```
返回其层次遍历结果：
```
[
  [3],
  [9,20],
  [15,7]
]
```
2019.06.25

- 解一 DFS

通过递归实现深度优先搜索，一层一层加入结点。
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
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> result = new LinkedList<>();
        levelHelper(result, root, 0);
        return result;
    }
    public void levelHelper(List<List<Integer>> result, TreeNode root, int levelNumber){
        if (root == null) return;
        if (levelNumber >= result.size()){
            result.add(new LinkedList<Integer>());
        }
        result.get(levelNumber).add(root.val);
        levelHelper(result, root.left, levelNumber + 1);
        levelHelper(result, root.right, levelNumber + 1);
    }
}
```
- 解二 BFS

广度优先搜索，一次性把接下去要遍历的左右子树放入到一个LinkedList中，等稍后分别遍历其左右树
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
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> result = new LinkedList<>();
        if (root == null) return result;
        List<TreeNode> currentNode = new LinkedList<>();
        currentNode.add(root);
        while (!currentNode.isEmpty()){
            List<TreeNode> nextNode = new LinkedList<>();
            List<Integer> level = new LinkedList<>();
            for (TreeNode node : currentNode){
                level.add(node.val);
                if (node.left != null) nextNode.add(node.left);
                if (node.right != null) nextNode.add(node.right);
            }
            result.add(level);
            currentNode = nextNode;
        }
        return result;
    }
}
```
## 回溯 Backtracking
### 基础
#### 78 Subsets
[Subsets](https://leetcode.com/problems/subsets/)

给定一组不含重复元素的整数数组 nums，返回该数组所有可能的子集（幂集）。

说明：解集不能包含重复的子集。

2019.06.30
- 解一 回溯

例如输入1，2，3，4先从1这一层开始，通过递归式，tempList依次添加为[1],[1,2],[1,2,3],[1,2,3,4]，同时分别把不同时期的tempList加入结果集中。再一步步回去并移除最后一位，同时观测i还能不能再进行循环：[1,2,3]不能，[1,2]，i = 2，可以进入当前层的循环，添加为[1,2,4]。并继续返回到上一层。
```Java
class Solution {
    public List<List<Integer>> subsets(int[] nums) {
        List<List<Integer>> result = new LinkedList<>();
        Arrays.sort(nums);
        backtracking(result, new LinkedList<>(), nums, 0);
        return result;
    }
    public void backtracking(List<List<Integer>> result, List<Integer> tempList, int[] nums, int start){
        result.add(new ArrayList<>(tempList));
        for (int i = start; i < nums.length; i++){
            tempList.add(nums[i]);
            backtracking(result, tempList, nums, i + 1);
            tempList.remove(tempList.size() - 1);
        }
    }
}
```
- 解二 迭代

继续拿1234举例，遍历1234，将之前的结果暂存到一个列表中，同时将遍历得到的数字插入到之前结果中。
```
null
1
2 1,2
3 1,3 2,3 1,2,3
4 1,4 2,4 1,2,4 3,4 1,3,4 2,3,4 1,2,3,4
```
注意迭代法还可以有另一个思路，这是按照子集元素种类排的，另一个思路可以是按照子集长度排，找出数组长度 1 的所有解，然后再在长度为 1 的所有解上加 1 个数字变成长度为 2 的所有解，同样的直到 n。
```Java
class Solution {
    public List<List<Integer>> subsets(int[] nums) {
        List<List<Integer>> result = new LinkedList<>();
        result.add(new LinkedList<>());
        for (int i = 0; i < nums.length; i++){
            List<List<Integer>> resultTemp = new LinkedList<>();
            for (List<Integer> lastLayer : result){
                List<Integer> lastLayerTemp = new LinkedList<>(lastLayer);
                lastLayerTemp.add(nums[i]);
                resultTemp.add(lastLayerTemp);
            }
            result.addAll(resultTemp);
        }
        return result;
    }
}
```
- 解三 位操作思路（StringBuilder实现）

所有子集中，数组的每个元素都有两个状态：在或者不在，可以用01表示。
```
1 2 3
0 0 0 -> [     ]
0 0 1 -> [    3]
0 1 0 -> [  2  ]   
0 1 1 -> [  2 3]  
1 0 0 -> [1    ]
1 0 1 -> [1   3] 
1 1 0 -> [1 2  ]
1 1 1 -> [1 2 3]
```
由上图可以很明显地感受到，可以引入二进制进行解题。只需要遍历 0 0 0 到 1 1 1，然后判断每个比特位是否是 1，是 1 的话将对应数字加入即可。
```Java
class Solution {
    public List<List<Integer>> subsets(int[] nums) {
        List<List<Integer>> result = new LinkedList<>();
        int forTimes = 1 << nums.length;
        for (int i = 0; i < forTimes; i++ ){
            StringBuilder mirror = new StringBuilder(Integer.toBinaryString(i));
            mirror = mirror.reverse();
            List<Integer> temp = new LinkedList<>();
            for (int j = 0; j < mirror.length(); j++){
                if (mirror.charAt(j) == '1'){
                    temp.add(nums[j]);
                }
            }
            result.add(temp);
        }
        return result;
    }
}
```

- 解四 位操作

其实直接用位操作也可以，思路是一样的，通过移位符直接从最末位看起，但注意这个时候对于输入数组来说，是往后对应的，所以count要++
```Java
class Solution {
    public List<List<Integer>> subsets(int[] nums) {
        List<List<Integer>> ans = new ArrayList<>();
        int ans_nums = 1 << nums.length; //执行 2 的 n 次方
        for (int i = 0; i < ans_nums; i++) {
            List<Integer> tmp = new ArrayList<>();
            int count = 0; //记录当前对应数组的哪一位
            int i_copy = i; //用来移位
            while (i_copy != 0) { 
                if ((i_copy & 1) == 1) { //判断当前位是否是 1
                    tmp.add(nums[count]);
                }
                count++;//对应数组的应该是往后看
                i_copy = i_copy >> 1;//右移一位，从后往前看
            }
            ans.add(tmp);
        }
        return ans;
    }
}
```
#### 90 Subsets II
[Subsets II](https://leetcode.com/problems/subsets-ii/)

给定一个可能包含重复元素的整数数组 nums，返回该数组所有可能的子集（幂集）。

说明：解集不能包含重复的子集。

2019.06.30
- 解一 位操作

```Java
class Solution {
    public List<List<Integer>> subsetsWithDup(int[] nums) {
        Arrays.sort(nums);
        List<List<Integer>> result = new LinkedList<>();
        int forTimes = 1 << nums.length;
        for (int i = 0; i < forTimes; i++ ){
            StringBuilder mirror = new StringBuilder(Integer.toBinaryString(i)).reverse();
            List<Integer> temp = new LinkedList<>();
            for (int j = 0; j < mirror.length(); j++){
                if (mirror.charAt(j) == '1'){
                    temp.add(nums[j]);
                }
            }
            if (!result.contains(temp)) {
                result.add(temp);
            }
        }
        return result;
    }
}
```
- 解二 回溯

```Java
class Solution {
    public List<List<Integer>> subsetsWithDup(int[] nums) {
        List<List<Integer>> list = new ArrayList<>();
        Arrays.sort(nums);
        backtrack(list, new ArrayList<>(), nums, 0);
        return list;
    }

    private void backtrack(List<List<Integer>> list, List<Integer> tempList, int [] nums, int start){
        list.add(new ArrayList<>(tempList));
        for(int i = start; i < nums.length; i++){
            if(i > start && nums[i] == nums[i-1]) continue; // skip duplicates
            tempList.add(nums[i]);
            backtrack(list, tempList, nums, i + 1);
            tempList.remove(tempList.size() - 1);
        }
    }
}
```
#### 🌟77 Combinations
[Combinations](https://leetcode.com/problems/combinations/)

给定两个整数 n 和 k，返回 1 ... n 中所有可能的 k 个数的组合。

2019.07.01

- 解一 回溯

经典回溯，注意总结回溯法的基本框架。这道题非常值得再扩展，有回溯就有迭代，还可以递归，有递归还可以用动态规划，记得补充总结。
```Java
class Solution {
    public List<List<Integer>> combine(int n, int k) {
        List<List<Integer>> result = new LinkedList<>();
        backTracking(n, k, 1, result, new LinkedList<>());
        return result;
    }
    public void backTracking(int n, int k, int starter, List<List<Integer>> result, List<Integer> temp){
        if (k == 0){
            result.add(new LinkedList<>(temp));
            return;
        }
        for (int i = starter; i <= n; i++){
            temp.add(i);
            backTracking(n, k - 1, i + 1, result, temp);
            temp.remove(temp.size() - 1);
        }
    }
}
```
#### 39 Combination Sum
[Combination Sum](https://leetcode.com/problems/combination-sum/)

给定一个无重复元素的数组 candidates 和一个目标数 target ，找出 candidates 中所有可以使数字和为 target 的组合。

candidates 中的数字可以无限制重复被选取。

说明：所有数字（包括 target）都是正整数；解集不能包含重复的组合。 

2019.07.01
- 解一 回溯
```Java
class Solution {
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        Arrays.sort(candidates);
        List<List<Integer>> result = new ArrayList<>();
        backTracking(candidates, target, 0, result, new ArrayList<>());
        return result;
    }
    public void backTracking(int[] candidates, int target, int starter, List<List<Integer>> result, List<Integer> temp){
        if (target == 0){
            result.add(new ArrayList<>(temp));
            return;
        }
        if (target > 0){
            for (int i = starter; i < candidates.length; i++){
                temp.add(candidates[i]);
                backTracking(candidates, target - candidates[i], i, result, temp);
                temp.remove(temp.size() - 1);
            }
        }
    }
}
```
#### 40 Combination Sum II
[Combination Sum II](https://leetcode.com/problems/combination-sum-ii/)

给定一个数组 candidates 和一个目标数 target ，找出 candidates 中所有可以使数字和为 target 的组合。

candidates 中的每个数字在每个组合中只能使用一次。

说明：所有数字（包括目标数）都是正整数；解集不能包含重复的组合。 

2019.07.01
- 解一 回溯
```Java
class Solution {
    public List<List<Integer>> combinationSum2(int[] candidates, int target) {
        Arrays.sort(candidates);
        List<List<Integer>> result = new ArrayList<>();
        backTracking(candidates, target, 0, result, new ArrayList<>());
        return result;
    }
    public void backTracking(int[] candidates, int target, int starter, List<List<Integer>> result, List<Integer> temp){
        if (target == 0 ){
            result.add(new ArrayList<>(temp));
            return;
        }
        if (target > 0){
            for (int i = starter; i < candidates.length; i++){
                if (i > starter && candidates[i] == candidates[i - 1])continue;
                temp.add(candidates[i]);
                backTracking(candidates, target - candidates[i], i + 1, result, temp);
                temp.remove(temp.size() - 1);
            }
        }
    }
}
```

#### 216 Combination Sum III
[Combination Sum III](https://leetcode.com/problems/combination-sum-iii/)

找出所有相加之和为 n 的 k 个数的组合。组合中只允许含有 1 - 9 的正整数，并且每种组合中不存在重复的数字。

说明：所有数字都是正整数；解集不能包含重复的组合。 

2019.07.01
- 解一 回溯

拿回溯的结构去套真的好轻松哦…
```Java
class Solution {
    public List<List<Integer>> combinationSum3(int k, int n) {
        List<List<Integer>> result = new ArrayList<>();
        backTracking(k, n, 1, result, new ArrayList<>());
        return result;
    }
    public void backTracking(int k, int n, int starter, List<List<Integer>> result, List<Integer> temp){
        if (k == 0 && n == 0){
            result.add(new ArrayList<>(temp));
            return;
        }
        for (int i = starter; i <= 9; i++){
            temp.add(i);
            backTracking(k - 1, n - i, i + 1, result, temp);
            temp.remove(temp.size() - 1);
        }
    }
}
```

#### 🌟377 Combination Sum IV
[Combination Sum IV](https://leetcode.com/problems/combination-sum-iv/)

给定一个由正整数组成且不存在重复数字的数组，找出和为给定目标正整数的组合的个数。

2019.07.01
由回溯法发现会超时，想到有没有办法用数组存下中间过程呢。通过动态规划存储过程中结果。
```Java
class Solution {
    private int[] dp;
    public int combinationSum4(int[] nums, int target) {
        dp = new int[target + 1];
        Arrays.fill(dp, -1);
        dp[0] = 1;
        return helper(nums, target);
    }
    private int helper(int[] nums, int target){
        if (dp[target] != -1){
            return dp[target];
        }
        int result = 0;
        for (int i = 0; i < nums.length; i++){
            if (target >= nums[i]){
                result += helper(nums, target - nums[i]);
            }
        }
        dp[target] = result;
        return result;
    }
}
```
#### 46 Permutations
[Permutations](https://leetcode.com/problems/permutations/)

给定一个没有重复数字的序列，返回其所有可能的全排列。

2019.07.02
- 解一 回溯
```Java
class Solution {
    public List<List<Integer>> permute(int[] nums) {
        List<List<Integer>> result = new ArrayList<>();
        backTracking(nums, new ArrayList<>(), result);
        return result;
    }
    public void backTracking(int nums[], List<Integer> temp, List<List<Integer>> result){
        if (temp.size() == nums.length) result.add(new ArrayList<>(temp));
        for (int i = 0; i < nums.length; i++){
            if (temp.contains(nums[i])) continue;
            temp.add(nums[i]);
            backTracking(nums, temp, result);
            temp.remove(temp.size() - 1);
        }
    }
}
```

#### 47 Permutations II
[Permutations II](https://leetcode.com/problems/permutations-ii/)
给定一个可包含重复数字的序列，返回所有不重复的全排列。

2019.07.02
- 解一 回溯

比起上一题不重复的，主要加入了一个boolean数组判断该数是否已经使用过
```Java
class Solution {
    public List<List<Integer>> permuteUnique(int[] nums) {
        List<List<Integer>> result = new ArrayList<>();
        Arrays.sort(nums);
        backTracking(result, new ArrayList<>(), nums, new boolean[nums.length]);
        return result;
    }
    public void backTracking(List<List<Integer>> result, List<Integer> temp, int[] nums, boolean[] used){
        if (temp.size() == nums.length) result.add(new ArrayList<>(temp));
        for (int i = 0; i < nums.length; i++){
            if (used[i] || i > 0 && nums[i] == nums[i - 1] && !used[i - 1]) continue;
            temp.add(nums[i]);
            used[i] = true;
            backTracking(result, temp, nums, used);
            temp.remove(temp.size() - 1);
            used[i] = false;
        }
    }
}
```

#### 31 Next Permutation
[Next Permutation](https://leetcode.com/problems/next-permutation/)

实现获取下一个排列的函数，算法需要将给定数字序列重新排列成字典序中下一个更大的排列。

如果不存在下一个更大的排列，则将数字重新排列成最小的排列（即升序排列）。

必须原地修改，只允许使用额外常数空间。

以下是一些例子，输入位于左侧列，其相应输出位于右侧列。
```
1,2,3 → 1,3,2
3,2,1 → 1,2,3
1,1,5 → 1,5,1
```

2019.07.02

---
![Single Pass Approach](https://leetcode.com/media/original_images/31_Next_Permutation.gif)
```Java
class Solution {
    public void nextPermutation(int[] nums) {
        int theFirstDecreasing = nums.length - 2;
        while (theFirstDecreasing >= 0 && nums[theFirstDecreasing + 1] <= nums[theFirstDecreasing]){
            theFirstDecreasing--;
        }
        if (theFirstDecreasing >= 0){
            int swapIndex = nums.length - 1;
            while (swapIndex >= 0 && nums[swapIndex] <= nums[theFirstDecreasing]) swapIndex--;
            swap(nums, theFirstDecreasing, swapIndex);
        }
        reverse(nums, theFirstDecreasing + 1);
    }
    private void reverse(int[] nums, int start) {
        int i = start, j = nums.length - 1;
        while (i < j) {
            swap(nums, i, j);
            i++;
            j--;
        }
    }
    private void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```
#### 60 Permutation Sequence
[Permutation Sequence](https://leetcode.com/problems/permutation-sequence/)
给出集合 [1,2,3,…,n]，其所有元素共有 n! 种排列。

按大小顺序列出所有排列情况，并一一标记，当 n = 3 时, 所有排列如下：
```
"123"
"132"
"213"
"231"
"312"
"321"
```
给定 n 和 k，返回第 k 个排列。

说明：给定 n 的范围是 [1, 9]; 给定 k 的范围是[1,  n!]。

2019.07.05

---
拿n = 4, k = 14举例，把4的结果分为
```
1（234）
2（134）
3（124）
4（123）
```
根据4!可知结果一共有24个，按首位不同分为4大类，每类3!为6个。
因为从0开始算起，所以14-1=13.
13/6=2 余1。因为从0算起，所以2直接对应数组2 1234，为3。确定首位后确定第二位：1/2!=1/2=0 余1 所以第二位直接对应数组124第0位，为1。确定第三位：1/1!=1/1=1，余0，所以第三位直接对应数组24第1位，为4。剩下2，答案为3142。 
```Java
class Solution {
    public String getPermutation(int n, int k) {
        List<Integer> numbers = new ArrayList<>();
        int[] factorial = new int[n+1];
        StringBuilder result = new StringBuilder();

        int sum = 1;
        factorial[0] = 1;
        for (int i = 1; i <= n; i++){
            sum *= i;
            factorial[i] = sum;
        }

        for (int i = 1; i <= n; i++){
            numbers.add(i);
        }

        k--;

        for (int i = 1; i <= n; i++){
            int index = k / factorial[n - i];
            result.append(String.valueOf(numbers.get(index)));
            numbers.remove(index);
            k = k - index * factorial[n - i];
        }
        return String.valueOf(result);
    }
}
```

## 动态规划 Dynamic Programming
### 一维
#### 62 Unique Paths
[Unique Paths](https://leetcode.com/problems/unique-paths/)
一个机器人位于一个 m x n 网格的左上角 （起始点在下图中标记为“Start” ）。

机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为“Finish”）。

问总共有多少条不同的路径？

说明：m 和 n 的值均不超过 100。

2019.08.08

- 解一：二维动态规划

这题其实不难理解，机器人只有向下或者向右移动的可能，所以到达每一格的可能路数这一结果，只有可能由它的上一格和左边格相加得到。

接下去就很简单了，既然是地图格子，就列一个二维数组。将二维数组的边界格子都列为1，因为只有一种可能到达其本身，右边界为一直向右走，只有这一条路，同理下边界。

以m7n3为例，得到初始二维格：
```
1 1 1 
1 0 0 
1 0 0 
1 0 0 
1 0 0 
1 0 0 
1 0 0 
```
每一格的结果数只有可能由它的上一格和左边格相加得到，即得到最终地图格：
```
1 1 1 
1 2 3 
1 3 6 
1 4 10 
1 5 15 
1 6 21 
1 7 28 
```
因此可以轻松得到一个m7n3范围的地图中，要达到右下角的地点可以有28种走法。
```Java
class Solution {
    public int uniquePaths(int m, int n) {
        int[][] map = new int[m][n];
        for (int i = 0; i < m; i++){
            map[i][0] = 1;
        }
        for (int i = 0; i < n; i++){
            map[0][i] = 1;
        }
        for (int i = 1; i < m; i++){
            for (int j = 1; j < n; j++){
                map[i][j] = map[i - 1][j] + map[i][j - 1];
            }
        }
        return map[m - 1][n - 1];
    }
}
```
- 解二：一维数组

由解法一还可以再进行化简，我们其实只需要最后一个，所以每次只更新保留最后一行。
```Java
class Solution {
    public int uniquePaths(int m, int n) {
        int[] dp = new int[n];
        for (int i = 0; i < n; i++) {
            dp[i] = 1;
        }
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                dp[j] += dp[j - 1];
            }
        }
        return dp[n - 1];
    }
}
```
#### 63 Unique Paths II
[Unique Paths II](https://leetcode.com/problems/unique-paths-ii/)

一个机器人位于一个 m x n 网格的左上角 （起始点在下图中标记为“Start” ）。

机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为“Finish”）。

现在考虑网格中有障碍物。那么从左上角到右下角将会有多少条不同的路径？

网格中的障碍物和空位置分别用 1 和 0 来表示。

说明：m 和 n 的值均不超过 100。

示例 1:
```
输入:
[
  [0,0,0],
  [0,1,0],
  [0,0,0]
]
输出: 2
解释:
3x3 网格的正中间有一个障碍物。
从左上角到右下角一共有 2 条不同的路径：
1. 向右 -> 向右 -> 向下 -> 向下
2. 向下 -> 向下 -> 向右 -> 向右
```
2019.08.24

---

举例：
```
0 0 0 
0 1 0 
0 0 0 
```
直接在原图上面改，先把第一行和第一列分别计算出能走的条数，如例中，第一行和第一列都无阻碍，则可以全部记为1，即到每一格只有一条路走。
```
1 1 1 
1 1 0 
1 0 0 
```
最后可以一行行由上方元素和左方元素相加获得，为1的是障碍物，没有路可以到达，因此改为0。最右下角的结果就是到达右下角可走的路的条数。
```
1 1 1 
1 0 1 
1 1 2 
```

```Java
class Solution {
    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        int R = obstacleGrid.length;
        int C = obstacleGrid[0].length;

        if (obstacleGrid[0][0] == 1) {
            return 0;
        }
        obstacleGrid[0][0] = 1;

        for (int i = 1; i < R; i++) {
            if (obstacleGrid[i][0] == 0 && obstacleGrid[i - 1][0] == 1)
                obstacleGrid[i][0] = 1;
            else obstacleGrid[i][0] = 0;
        }

        for (int i = 1; i < C; i++) {
            if (obstacleGrid[0][i] == 0 && obstacleGrid[0][i - 1] == 1)
                obstacleGrid[0][i] = 1;
            else obstacleGrid[0][i] = 0;
        }

        for (int i = 1; i < R; i++) {
            for (int j = 1; j < C; j++) {
                if (obstacleGrid[i][j] == 0) {
                    obstacleGrid[i][j] = obstacleGrid[i - 1][j] + obstacleGrid[i][j - 1];
                } else {
                    obstacleGrid[i][j] = 0;
                }
            }
        }
        return obstacleGrid[R - 1][C - 1];
    }
}
```

#### 120 Triangle
[Triangle](https://leetcode.com/problems/triangle/)

给定一个三角形，找出自顶向下的最小路径和。每一步只能移动到下一行中相邻的结点上。

例如，给定三角形：
```
[
     [2],
    [3,4],
   [6,5,7],
  [4,1,8,3]
]
```
自顶向下的最小路径和为 11（即，2 + 3 + 5 + 1 = 11）。

说明：

如果你可以只使用 O(n) 的额外空间（n 为三角形的总行数）来解决这个问题，那么你的算法会很加分。

2019.08.27

----
动态规划思路，自底向上找最短路径。在实际操作中，可以从倒数第二行开始，把这一行上每一个数字都改为当前数字和下一行（倒数第一行）对应的相邻数相加取最小值。以题中数据举例，全部完成后应该是这样的：
```
2
3 4
7 6 10 
4 1 8 3 
```
由此依次向上一层递推，得到最终结果为
```
11 
9 10 
7 6 10 
4 1 8 3 
```
```Java
class Solution {
    public int minimumTotal(List<List<Integer>> triangle) {
        if (triangle.size() == 0) return 0;

        for (int i = triangle.size() - 2; i >= 0; i--){
            for (int j = 0; j <= i; j++){
                List<Integer> nextRow = triangle.get(i + 1);
                //和下一行数字相加
                int sum = Math.min(nextRow.get(j), nextRow.get(j + 1)) + triangle.get(i).get(j);
                triangle.get(i).set(j, sum);
            }
        }

        return triangle.get(0).get(0);
    }
}
```

#### 279 Perfect Squares
[Perfect Squares](https://leetcode.com/problems/perfect-squares/)

给定正整数 n，找到若干个完全平方数（比如 1, 4, 9, 16, ...）使得它们的和等于 n。你需要让组成和的完全平方数的个数最少。

示例 1:
```
输入: n = 12
输出: 3 
解释: 12 = 4 + 4 + 4.
```
示例 2:
```
输入: n = 13
输出: 2
解释: 13 = 4 + 9.
```
2019.12.09

----
动态规划思路，自底向上推出通用公式如下：
```
dp[0] = 0 
dp[1] = dp[0]+1 = 1
dp[2] = dp[1]+1 = 2
dp[3] = dp[2]+1 = 3
dp[4] = Min{ dp[4-1*1]+1, dp[4-2*2]+1 } 
      = Min{ dp[3]+1, dp[0]+1 } 
      = 1				
dp[5] = Min{ dp[5-1*1]+1, dp[5-2*2]+1 } 
      = Min{ dp[4]+1, dp[1]+1 } 
      = 2
						.
						.
						.
dp[13] = Min{ dp[13-1*1]+1, dp[13-2*2]+1, dp[13-3*3]+1 } 
       = Min{ dp[12]+1, dp[9]+1, dp[4]+1 } 
       = 2
						.
						.
						.
dp[n] = Min{ dp[n - i*i] + 1 },  n - i*i >=0 && i >= 1
```
对每一个给定的数，比较所有它减去平方数的数，例如13，比较dp[12]、dp[9]、dp[4]，比较它们加上本身被减去的平方数后的最小值即是能组成和的最小平方数。
```Java
class Solution {
    public int numSquares(int n) {
        int[] dp = new int[n+1];
        Arrays.fill(dp, Integer.MAX_VALUE);
        dp[0] = 0;
        for (int i = 1; i <= n; i++){
            int min = Integer.MAX_VALUE;
            int j = 1;
            while (i - j * j >= 0){
                min = Math.min(min, dp[i - j * j] + 1);
                j++;
            }
            dp[i] = min;
        }
        return dp[n];
    }
}
```

#### 139 Word Break
[Word Break](https://leetcode.com/problems/word-break/)

给定一个非空字符串 s 和一个包含非空单词列表的字典 wordDict，判定 s 是否可以被空格拆分为一个或多个在字典中出现的单词。

说明：

拆分时可以重复使用字典中的单词。
你可以假设字典中没有重复的单词。

示例 1：
```
输入: s = "leetcode", wordDict = ["leet", "code"]
输出: true
解释: 返回 true 因为 "leetcode" 可以被拆分成 "leet code"。
```
示例 2：
```
输入: s = "applepenapple", wordDict = ["apple", "pen"]
输出: true
解释: 返回 true 因为 "applepenapple" 可以被拆分成 "apple pen apple"。
     注意你可以重复使用字典中的单词。
```
示例 3：
```
输入: s = "catsandog", wordDict = ["cats", "dog", "sand", "and", "cat"]
输出: false
```
2020.02.27

----
根据wordDict依序生成不同字符串，同时与目标字符串进行匹配。如果生成的字符串可以和目标字符串匹配上了但是长度不足，则进入该支线。

如示例3中，首先生成cats，cats和目标字符串全部吻合，则开始深度优先搜索，依序测试catscats和catsdog等，在catsand吻合，进入下一级，catsandcats等等。
首先判断长度，长度超出则直接返回false进行同级其他字词匹配；否则逐单词进行比对，如果全部吻合且长度也相等，返回true。
```Java
class Solution {
    public boolean wordBreak(String s, List<String> wordDict) {
        return wordBreakHelper(s, wordDict, "", new HashMap<String, Boolean>());
    }

    private boolean wordBreakHelper(String s, List<String> wordDict, String temp, HashMap<String, Boolean> hashMap) {
        if (temp.length() > s.length()) {
            return false;
        }

        if (hashMap.containsKey(temp)){
            return hashMap.get(temp);
        }
        for (int i = 0; i < temp.length(); i++) {
            if (s.charAt(i) != temp.charAt(i))
                return false;
        }

        if (s.length() == temp.length()) return true;

        for (int i = 0; i < wordDict.size(); i++) {
            if (wordBreakHelper(s, wordDict, temp + wordDict.get(i), hashMap)){
                hashMap.put(temp, true);
                return true;
            }
        }

        hashMap.put(temp, false);
        return false;
    }
}
```

#### 375 Guess Number Higher or Lower II
[Guess Number Higher or Lower II](https://leetcode.com/problems/guess-number-higher-or-lower-ii/)

我们正在玩一个猜数游戏，游戏规则如下：

我从 1 到 n 之间选择一个数字，你来猜我选了哪个数字。

每次你猜错了，我都会告诉你，我选的数字比你的大了或者小了。

然而，当你猜了数字 x 并且猜错了的时候，你需要支付金额为 x 的现金。直到你猜到我选的数字，你才算赢得了这个游戏。

示例:
```
n = 10, 我选择了8.

第一轮: 你猜我选择的数字是5，我会告诉你，我的数字更大一些，然后你需要支付5块。
第二轮: 你猜是7，我告诉你，我的数字更大一些，你支付7块。
第三轮: 你猜是9，我告诉你，我的数字更小一些，你支付9块。

游戏结束。8 就是我选的数字。

你最终要支付 5 + 7 + 9 = 21 块钱。
```
给定 n ≥ 1，计算你至少需要拥有多少现金才能确保你能赢得这个游戏。

2020.02.29

----

给定一个数字n，对方会从1-n选择什么数字作为最终答案是并不清楚的，同时这个也不重要。题意的要求就是，要算每次都猜错的情况下的总体最小开销。

假设i是第一次选错的数字，那么cost(1, n) = i + max( cost(1, i − 1), cost(i + 1, n))。在这一步我们把大问题分解成了小问题。对于左右两段，我们分别考虑在段内选择一个数，并重复上面的过程来求得最小开销。

这一步就是初步暴力的做法，可以得到代码如下：
```Java
public class Solution {
    public int calculate(int low, int high) {
        if (low >= high)
            return 0;
        int minres = Integer.MAX_VALUE;
        for (int i = low; i <= high; i++) {
            int res = i + Math.max(calculate(i + 1, high), calculate(low, i - 1));
            minres = Math.min(res, minres);
        }

        return minres;
    }
    public int getMoneyAmount(int n) {
        return calculate(1, n);
    }
}
```
但这一步超时了。我们想一下是不是可以用数组来存储之前已经算好的结果，这样的话就不需要每次都重复计算。

因为1-n每次都分成不同长度的小段（并不是二分法猜数字耗费最少），所以求1-n之间每次猜错后最终的最小花销，可以细分成求low-high之间每次猜错后最终的最小花销。

例如给出n=5，要求出每次都猜错的最小花费，先猜2被告知比2大，再猜4被告知比4大或者小，这样最少需要6可以猜出对方心中想的数字。

要储存不同范围内每次都猜错的最小花费，可以设置一个二维数组，当n=5时，这个二维数组dp可以设置为
\ |1 |2 |3|4 |5
:-:|:-:|:-:|:-:|:-:|:-:
1|0||||
2|-|0|||
3|-|-|0||
4|-|-|-|0|
5|-|-|-|-|0

其中dp[m][n]存储的就是在m-n范围内每次都猜错的最小花费，所以当m = n时，dp[m][n] = 0;

要规律地填满这个二维数组，先按照范围长度依次递增的顺序进行填充。

例如范围长度为1的时候，依次算1-2，2-3，3-4，4-5
很顺利可得二维数组为
\ |1 |2 |3|4 |5
:-:|:-:|:-:|:-:|:-:|:-:
1|0|1|||
2|-|0|2||
3|-|-|0|3|
4|-|-|-|0|4
5|-|-|-|-|0

当范围长度为2的时候，依次算1-3，2-4，3-5
由于之前的1-2，2-3，3-4，4-5已有答案，例如求dp[1][3]时，先1+max(dp[1][0], dp[2][3]) = 3;再2+max(dp[1][1], dp[3][3]) = 2;得出2最小。
依此推论，则很顺利可得二维数组为
\ |1 |2 |3|4 |5
:-:|:-:|:-:|:-:|:-:|:-:
1|0|1|2|4|6
2|-|0|2|3|6
3|-|-|0|3|4
4|-|-|-|0|4
5|-|-|-|-|0

自顶向上得出dp[1][n]结果。
```Java
public static int getMoneyAmount(int n) {
        int[][] dp = new int[n + 1][n + 1];
        for (int len = 1; len < n; len++){
            for (int start = 1; start <= n - len; start++) {
                int minRes = Integer.MAX_VALUE;
                for (int piv = start; piv < start + len; piv++) {
                    int res = piv + Math.max(dp[start][piv - 1], dp[piv + 1][start + len]);
                    minRes = Math.min(res, minRes);
                }
                dp[start][start + len] = minRes;
            }
        }
        return dp[1][n];
    }
```

## 链表 Linked List
### 基础
#### 24 Swap Nodes in Pairs
[Swap Nodes in Pairs](https://leetcode.com/problems/swap-nodes-in-pairs/)

给定一个链表，两两交换其中相邻的节点，并返回交换后的链表。

你不能只是单纯的改变节点内部的值，而是需要实际的进行节点交换。

示例:
```
给定 1->2->3->4, 你应该返回 2->1->4->3.
```
2020.03.25

---

- 递归

```Java
class Solution {
    public ListNode swapPairs(ListNode head) {
        if (head == null || head.next == null) return head;

        ListNode next = head.next;
        head.next = swapPairs(next.next);
        next.next = head;
        
        return next;
    }
}
```
递归主要注意三个点：
    1. 递归终止条件
    2. 递归内部操作
    3. 递归返回值

在这个题目里面，假设有head->next->next.next点，最终要达到next->head->next.next效果，可以确定终止条件是当head或者next为空，同时也可以确定返回值，新链表的head是next，那么返回值就是next，以作为下一个递归的参数。

内部操作也很好懂，把head的下一位链接到next.next，具体这里到底是哪个值其实并不需要在意，只需要连接到第三位就行了，不用细思第三位上到底是什么。这里就可以用递归，swapPairs(next.next)，返回的是第三部分链表结点们的头结点。最后把next的下一位指向head，就完成了链表的转换。记住递归就是不断做重复的操作。

- 非递归

```Java
class Solution {
    public ListNode swapPairs(ListNode head) {
        ListNode neg = new ListNode(0);
        neg.next = head;
        ListNode pre = neg;

        while (head != null && head.next != null){
            //pre->1st->2nd->2nd.next
            ListNode first = head;
            ListNode second = head.next;

            pre.next = second;
            first.next = second.next;
            second.next = first;

            pre = head;
            head = head.next;
        }
        return neg.next;
    }
}
```
非递归的话就是设置pre->1st->2nd三个指针，内部操作完以后再往后顺一位就行了，while循环外要变动的只有两个指针，一个pre一个head，所以只要他俩往后顺一位就行了。neg用来记录头结点。

#### 328 Odd Even Linked List
[Odd Even Linked List](https://leetcode.com/problems/odd-even-linked-list/)

给定一个单链表，把所有的奇数节点和偶数节点分别排在一起。请注意，这里的奇数节点和偶数节点指的是节点编号的奇偶性，而不是节点的值的奇偶性。

请尝试使用原地算法完成。你的算法的空间复杂度应为 O(1)，时间复杂度应为 O(nodes)，nodes 为节点总数。

示例 1:
```
输入: 1->2->3->4->5->NULL
输出: 1->3->5->2->4->NULL
```
示例 2:
```
输入: 2->1->3->5->6->4->7->NULL 
输出: 2->3->6->7->1->5->4->NULL
```
说明:

应当保持奇数节点和偶数节点的相对顺序。
链表的第一个节点视为奇数节点，第二个节点视为偶数节点，以此类推。

2020.03.25

----
这题思路很简单，就是拆成两个奇偶链表，因为需要把偶链表的头结点链接到奇链表的尾结点上，双端操作就需要每个链表分别设置头指针和尾指针。

遇到链表题一定要画图，也注意分清楚哪些指针是需要变动的

```Java
class Solution {
    public ListNode oddEvenList(ListNode head) {
        if(head == null) return null;
        ListNode oddTail = head;
        ListNode evenHead = head.next;
        ListNode evenTail = evenHead;

        while (evenTail != null && evenTail.next != null){
            oddTail.next = evenTail.next;
            oddTail = oddTail.next;
            evenTail.next = oddTail.next;
            evenTail = evenTail.next;
        }

        oddTail.next = evenHead;
        return head;
    }
}
```
#### 92 Reverse Linked List II
[Reverse Linked List II](https://leetcode.com/problems/reverse-linked-list-ii/)

反转从位置 m 到 n 的链表。请使用一趟扫描完成反转。

说明:
1 ≤ m ≤ n ≤ 链表长度。

示例:
```
输入: 1->2->3->4->5->NULL, m = 2, n = 4
输出: 1->4->3->2->5->NULL
```
2020.03.26

----

- 迭代

不难做，注意遇到链表题要画图画图画图。
然后为了防止出现从1开始反转的情况，设置一个neg结点在头指针前。因为要有衔接嘛所以设置一个linkTail作为m结点前一位的结点，linkHead作为m结点本身。只有pre和head两个结点在动（其实为了谨慎一点可以把head设置成为current指针）。pre和head每次往后移一位，进入m-n的范围了，head直接回头指向pre就行了。最后把这段反转链表的头尾衔接上，此时头尾衔接指针应该分别为 linkTail, linkHead; head, head.next。
```Java
class Solution {
    public ListNode reverseBetween(ListNode head, int m, int n) {
        if (m == n) return head;
        
        ListNode neg = new ListNode(0);
        neg.next = head;
        int count = 0;
        ListNode linkTail = null;
        ListNode linkHead = null;
        ListNode pre = neg;

        while (head != null){
            count ++;
            if (count == m){
                linkTail = pre;
                linkHead = head;
            }

            if (count > m && count < n){
                ListNode temp = head.next;
                head.next = pre;
                pre = head;
                head = temp;
                continue;
            }

            if (count == n){
                linkHead.next = head.next;
                head.next = pre;
                linkTail.next = head;
                break;
            }

            head = head.next;
            pre = pre.next;
        }


        return neg.next;
    }
}
```
#### 19 Remove Nth Node From End of List
[Remove Nth Node From End of List](https://leetcode.com/problems/remove-nth-node-from-end-of-list/)

给定一个链表，删除链表的倒数第 n 个节点，并且返回链表的头结点。

示例：
```
给定一个链表: 1->2->3->4->5, 和 n = 2.

当删除了倒数第二个节点后，链表变为 1->2->3->5.
```
说明：

给定的 n 保证是有效的。

进阶：

你能尝试使用一趟扫描实现吗？

2020.03.26

----
先遍历一遍得到全长，再通过全长和倒数数字得到顺数的位置。

```Java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode cur = head;
        int overall = 0;

        while (cur != null){
            cur = cur.next;
            overall++;

        }
        int target = overall - n + 1;

        ListNode neg = new ListNode(0);
        neg.next = head;
        cur = neg;
        while (target > 1){
            target--;
            cur = cur.next;
        }
        cur.next = cur.next.next;

        return neg.next;
    }
}
```
这题还有一个解法很有意思，但是先不写了：
```
想象一下，两个人进行 100m 赛跑，假设他们的速度相同。开始的时候，第一个人就在第二个人前边 10m ，这样当第一个人跑到终点的时候，第二个人相距第一个人依旧是 10m ，也就是离终点 10m。

对比于链表，我们设定两个指针，先让第一个指针遍历 n 步，然后再让它俩同时开始遍历，这样的话，当第一个指针到头的时候，第二个指针就离第一个指针有 n 的距离，所以第二个指针的位置就刚好是倒数第 n 个结点。
```

#### 82 Remove Duplicates from Sorted List II
[Remove Duplicates from Sorted List II](https://leetcode.com/problems/remove-duplicates-from-sorted-list-ii/)

给定一个排序链表，删除所有含有重复数字的节点，只保留原始链表中 没有重复出现 的数字。

示例 1:
```
输入: 1->2->3->3->4->4->5
输出: 1->2->5
```
示例 2:
```
输入: 1->1->1->2->3
输出: 2->3
```
2020.03.28

-----
这题画了图以后还是很好做的……设置了三个指针pre cur next，先记录下重复值，一次性消除该重复值的结点们。


```Java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        if(head == null) return head;
        ListNode neg = new ListNode(0);
        neg.next = head;
        ListNode pre = neg;
        ListNode cur = neg.next;
        ListNode next = neg.next.next;
        int dupNum = -1;
        while (next != null){
            if (cur.val == next.val){
                dupNum = cur.val;
                while (next.val == dupNum) {
                    next = next.next;
                    if(next == null) break;
                }
                pre.next = next;
                cur = next;
                if(next != null)
                    next = next.next;
                continue;
            }
            pre = pre.next;
            cur = cur.next;
            if (next != null){
                next = next.next;
            }
        }
        return neg.next;
    }
}
```

#### 2 Add Two Numbers
[Add Two Numbers](https://leetcode.com/problems/add-two-numbers/)

给出两个 非空 的链表用来表示两个非负的整数。其中，它们各自的位数是按照 逆序 的方式存储的，并且它们的每个节点只能存储 一位 数字。

如果，我们将这两个数相加起来，则会返回一个新的链表来表示它们的和。

您可以假设除了数字 0 之外，这两个数都不会以 0 开头。

示例：
```
输入：(2 -> 4 -> 3) + (5 -> 6 -> 4)
输出：7 -> 0 -> 8
原因：342 + 465 = 807
```
2020.03.29

-----
这题就用常规思路算就行了，新建一个链表，对l1l2每个相加进位，用个carry。也没什么坑。
```Java
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode neg = new ListNode(0);
        ListNode num1 = l1, num2 = l2, cur = neg;
        int carry = 0;
        while (num1 != null || num2 != null){
            int x = 0, y = 0;
            if (num1 != null) x = num1.val;
            if (num2 != null) y = num2.val;

            int sum = carry + x + y;
            carry = sum / 10;
            cur.next = new ListNode(sum % 10);
            
            cur = cur.next;
            if (num1 != null) num1 = num1.next;
            if (num2 != null) num2 = num2.next;
        }
        if (carry > 0) cur.next = new ListNode(carry);
        
        return neg.next;
    }
}
```

## 二分查找 Binary Search
### 基础
#### 33 Search in Rotated Sorted Array
[Search in Rotated Sorted Array](https://leetcode.com/problems/search-in-rotated-sorted-array/)

假设按照升序排序的数组在预先未知的某个点上进行了旋转。

( 例如，数组 [0,1,2,4,5,6,7] 可能变为 [4,5,6,7,0,1,2] )。

搜索一个给定的目标值，如果数组中存在这个目标值，则返回它的索引，否则返回 -1 。

你可以假设数组中不存在重复的元素。

你的算法时间复杂度必须是 O(log n) 级别。

示例 1:
```
输入: nums = [4,5,6,7,0,1,2], target = 0
输出: 4
```
示例 2:
```
输入: nums = [4,5,6,7,0,1,2], target = 3
输出: -1
```
2020.04.03

-------

```Java
class Solution {
    public int search(int[] nums, int target) {
        int left = 0, right = nums.length - 1, mid;
        while (left <= right){
            mid = left + (right - left) / 2;
            if (nums[mid] == target) return mid;
            else if (nums[left] <= nums[mid]){//左边有序
                if (target >= nums[left] && target <= nums[mid]){//target在左边
                    right = mid - 1;
                }else {
                    left = mid + 1;
                }
            }else {//右边有序
                if (target >= nums[mid] && target <= nums[right]){//target在右边
                    left = mid + 1;
                }else {
                    right = mid - 1;
                }
            }
        }
        return -1;
    }
}
```

#### 81 Search in Rotated Sorted Array II
[Search in Rotated Sorted Array II](https://leetcode.com/problems/search-in-rotated-sorted-array-ii/)

假设按照升序排序的数组在预先未知的某个点上进行了旋转。

( 例如，数组 [0,0,1,2,2,5,6] 可能变为 [2,5,6,0,0,1,2] )。

编写一个函数来判断给定的目标值是否存在于数组中。若存在返回 true，否则返回 false。

示例 1:
```
输入: nums = [2,5,6,0,0,1,2], target = 0
输出: true
```
示例 2:
```
输入: nums = [2,5,6,0,0,1,2], target = 3
输出: false
```

2020.04.05

-----
注意[1,3,1,1,1]中寻找3这种没办法确定哪边是有序的例子，用left++去掉一个重复元素即可

```Java
class Solution {
    public boolean search(int[] nums, int target) {
        int left = 0, right = nums.length - 1, mid;
        while (left <= right){
            mid = (left + right) / 2;
            if (nums[mid] == target) return true;
            else if (nums[left] == nums[mid]){
                left ++;
                continue;
            } else if (nums[left] < nums[mid]){
                if (nums[left] <= target && target < nums[mid]){
                    right = mid - 1;
                }else left = mid + 1;
            }else {
                if (nums[mid] < target && target <= nums[right]){
                    left = mid + 1;
                }else right = mid - 1;
            }
        }
        return false;
    }
}
```

#### 153 Find Minimum in Rotated Sorted Array
[Find Minimum in Rotated Sorted Array](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/)

假设按照升序排序的数组在预先未知的某个点上进行了旋转。

( 例如，数组 [0,1,2,4,5,6,7] 可能变为 [4,5,6,7,0,1,2] )。

请找出其中最小的元素。

你可以假设数组中不存在重复元素。

示例 1:
```
输入: [3,4,5,1,2]
输出: 1
```
示例 2:
```
输入: [4,5,6,7,0,1,2]
输出: 0
```
2020.04.06

-----
- 解一

一个解法是找出轴点，先判断有序，如果左边全是升序，那结点一定存在于右边；如果右边有序，那结点一定存在于左边。

但是这个解法需要考虑边界，一个是[1]的情况，一个是[1,2]的情况。

```Java
class Solution {
    public int findMin(int[] nums) {
        int left = 0, right = nums.length - 1, mid;
        if (nums[right] > nums[0] || nums.length == 1) {
          return nums[left];
        }
        while (left <= right){
            mid = (left + right) / 2;
            if (nums[mid] > nums[mid + 1]) return nums[mid + 1];
            if (nums[mid] < nums[mid - 1]) return nums[mid];
            if (nums[left] < nums[mid]){//左边有序
                left = mid;
            }else {
                right = mid;
            }
        }
        return nums[left];
    }
}
```
- 解二

另一种解法不需要判断边界值

```Java
class Solution {
    public int findMin(int[] nums) {
        int left = 0, right = nums.length - 1, mid;
        while (left <= right){
            mid = (left + right) / 2;
            if (nums[mid] > nums[nums.length - 1]) left = mid + 1;
            else right = mid - 1;
        }
        return nums[left];
    }
}
```

#### 162 Find Peak Element
[Find Peak Element](https://leetcode.com/problems/find-peak-element/)

峰值元素是指其值大于左右相邻值的元素。

给定一个输入数组 nums，其中 nums[i] ≠ nums[i+1]，找到峰值元素并返回其索引。

数组可能包含多个峰值，在这种情况下，返回任何一个峰值所在位置即可。

你可以假设 nums[-1] = nums[n] = -∞。

示例 1:
```
输入: nums = [1,2,3,1]
输出: 2
解释: 3 是峰值元素，你的函数应该返回其索引 2。
```
示例 2:
```
输入: nums = [1,2,1,3,5,6,4]
输出: 1 或 5 
解释: 你的函数可以返回索引 1，其峰值元素为 2；
     或者返回索引 5， 其峰值元素为 6。
```

2020.04.07

-----
找峰值，通过判断升序降序来判断峰值在左边还是右边。while循环条件那边要注意一下。
```Java
class Solution {
    public int findPeakElement(int[] nums) {
        int left = 0, right = nums.length - 1, mid;
        while (left < right){
            mid = left + (right - left) / 2;
            if (nums[mid] < nums[mid+1]) left = mid + 1;
            else right = mid;
        }
        return left;
    }
}
```

#### *34 Find First and Last Position of Element in Sorted Array

[Find First and Last Position of Element in Sorted Array](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/)

给定一个按照升序排列的整数数组 nums，和一个目标值 target。找出给定目标值在数组中的开始位置和结束位置。

你的算法时间复杂度必须是 O(log n) 级别。

如果数组中不存在目标值，返回 [-1, -1]。

示例 1:
```
输入: nums = [5,7,7,8,8,10], target = 8
输出: [3,4]
```

示例 2:
```
输入: nums = [5,7,7,8,8,10], target = 6
输出: [-1,-1]
```

2020.04.08

-----

```Java
class Solution {
    private int extremeInsertionIndex(int[] nums, int target, boolean left) {
        int lo = 0;
        int hi = nums.length;

        while (lo < hi) {
            int mid = (lo + hi) / 2;
            if (nums[mid] > target || (left && target == nums[mid])) {
                hi = mid;
            }
            else {
                lo = mid+1;
            }
        }

        return lo;
    }

    public int[] searchRange(int[] nums, int target) {
        int[] targetRange = {-1, -1};

        int leftIdx = extremeInsertionIndex(nums, target, true);

        // assert that `leftIdx` is within the array bounds and that `target`
        // is actually in `nums`.
        if (leftIdx == nums.length || nums[leftIdx] != target) {
            return targetRange;
        }

        targetRange[0] = leftIdx;
        targetRange[1] = extremeInsertionIndex(nums, target, false)-1;

        return targetRange;
    }
}
```

## 矩阵 Matrix
### 基础
#### 48 Rotate Image
[Rotate Image](https://leetcode.com/problems/rotate-image/)

给定一个 n × n 的二维矩阵表示一个图像。

将图像顺时针旋转 90 度。

说明：

你必须在原地旋转图像，这意味着你需要直接修改输入的二维矩阵。请不要使用另一个矩阵来旋转图像。

示例 1:
```
给定 matrix = 
[
  [1,2,3],
  [4,5,6],
  [7,8,9]
],

原地旋转输入矩阵，使其变为:
[
  [7,4,1],
  [8,5,2],
  [9,6,3]
]
```
示例 2:
```
给定 matrix =
[
  [ 5, 1, 9,11],
  [ 2, 4, 8,10],
  [13, 3, 6, 7],
  [15,14,12,16]
], 

原地旋转输入矩阵，使其变为:
[
  [15,13, 2, 5],
  [14, 3, 4, 1],
  [12, 6, 8, 9],
  [16, 7,10,11]
]
```

2020.04.11

-----

矩阵翻转题。观察规律是每四个点旋转，如
```
[1,2,3]
[4,5,6]
[7,8,9]
```
最先是角上四个点1，3，7，9旋转，最后2，4，8，6旋转。有点难写就换了思路，观察可得先把例中矩阵置换为
```
1 4 7 
2 5 8 
3 6 9 
```
注意双重循环的条件应该是j = i开始。
置换完毕后再每行中置换。

```Java
class Solution {
    public void rotate(int[][] matrix) {
        int n = matrix.length;
        for (int i = 0; i < n; i++){
            for (int j = i; j < n; j++){
                int temp = matrix[i][j];
                matrix[i][j] = matrix[j][i];
                matrix[j][i] = temp;
            }
        }
        for (int i = 0; i < n; i++){
            for (int j = 0; j < n / 2; j++){
                int temp = matrix[i][j];
                matrix[i][j] = matrix[i][n - 1 - j];
                matrix[i][n - 1 - j] = temp;
            }
        }
    }
}
```

#### 54 Spiral Matrix
[Spiral Matrix](https://leetcode.com/problems/spiral-matrix/)

2020.04.12

------

这题太绝了，我推荐我自己学十遍！主要就是设置边界，如果边界有重合那就说明已经遍历完成了

```Java
class Solution {
    public List<Integer> spiralOrder(int[][] matrix) {
        List<Integer> result = new LinkedList<Integer>();
        if (matrix.length == 0) return result;
        int up = 0, down = matrix.length - 1, left = 0, right = matrix[0].length - 1;
        while (true){
            for (int i = left; i <= right; i++) result.add(matrix[up][i]);//从左移到右
            if (up++ >= down)break; //重新定义上边界，若上边界大于下边界，则遍历完成
            for (int i = up; i <= down; i++)result.add(matrix[i][right]);//从上移到下
            if (right-- <= left) break;//重新定义右边界，若右边界和左边界重合，则遍历完成
            for (int i = right; i >= left; i--) result.add(matrix[down][i]);//从右移到左
            if (down-- <= up)break;
            for (int i = down; i >= up; i--)result.add(matrix[i][left]);
            if (left++ >= right)break;
        }
        return result;
    }
}
```

#### 59 Spiral Matrix II
[Spiral Matrix II](https://leetcode.com/problems/spiral-matrix-ii/)

给定一个正整数 n，生成一个包含 1 到 n2 所有元素，且元素按顺时针顺序螺旋排列的正方形矩阵。

示例:
```
输入: 3
输出:
[
 [ 1, 2, 3 ],
 [ 8, 9, 4 ],
 [ 7, 6, 5 ]
]
```

2020.04.13

-------
边界值设置这个办法实在是太好用了……

```Java
class Solution {
    public int[][] generateMatrix(int n) {
        int[][] result = new int[n][n];
        int left = 0, right = n-1, up = 0, down = n-1;
        int number = 1;

        while (true){
            for (int i = left; i <= right; i++){
                result[up][i] = number;
                number++;
            }
            if (up++ > down) break;

            for (int i = up; i <= down; i++){
                result[i][right] = number;
                number++;
            }
            if (right-- < left) break;
            
            for (int i = right; i >= left; i--){
                result[down][i] = number;
                number++;
            }
            if (down-- < up) break;
            
            for (int i = down; i >= up; i--){
                result[i][left] = number;
                number++;
            }
            if (left++ > right) break;
        }

        return result;
    }
}
```

#### 73 Set Matrix Zeroes
[Set Matrix Zeroes](https://leetcode.com/problems/set-matrix-zeroes/)

给定一个 m x n 的矩阵，如果一个元素为 0，则将其所在行和列的所有元素都设为 0。请使用原地算法。

示例 1:
```
输入: 
[
  [1,1,1],
  [1,0,1],
  [1,1,1]
]
输出: 
[
  [1,0,1],
  [0,0,0],
  [1,0,1]
]
```
示例 2:
```
输入: 
[
  [0,1,2,0],
  [3,4,5,2],
  [1,3,1,5]
]
输出: 
[
  [0,0,0,0],
  [0,4,5,0],
  [0,3,1,0]
]
```
2020.04.14

-----
第一反应用hashSet！一次AC啦。用HashSet存下0的横竖坐标轴，最后再遍历一次，把记录下的xy轴上的行列都置为0。

但是这个办法要求额外空间，还有另一种办法可以原地置换，就是遇到有0的，把对应的行列第一个值分别记为0，这样在第二次遍历数组的时候，遇到行列第一个值是0的 就可以把整行或者整列清为0。
```Java
class Solution {
    public void setZeroes(int[][] matrix) {
        HashSet<Integer> x = new HashSet<Integer>();
        HashSet<Integer> y = new HashSet<Integer>();

        for (int i = 0; i < matrix.length; i++){
            for (int j = 0; j < matrix[0].length; j++){
                if (matrix[i][j] == 0){
                    x.add(i);y.add(j);
                }
            }
        }

        for (int i = 0; i < matrix.length; i++){
            for (int j = 0; j < matrix[0].length; j++){
                if (x.contains(i) || y.contains(j))
                    matrix[i][j] = 0;
            }
        }
    }
}
```

#### 378 Kth Smallest Element in a Sorted Matrix
[Kth Smallest Element in a Sorted Matrix](https://leetcode.com/problems/kth-smallest-element-in-a-sorted-matrix/)

给定一个 n x n 矩阵，其中每行和每列元素均按升序排序，找到矩阵中第k小的元素。
请注意，它是排序后的第 k 小元素，而不是第 k 个不同的元素。

示例:
```
matrix = [
   [ 1,  5,  9],
   [10, 11, 13],
   [12, 13, 15]
],
k = 8,

返回 13。
```

2020.04.16

-----
- 解一 优先队列
用优先队列做，正常优先队列是小顶堆，注意要重写比较方法变为大顶堆。如果队列长度大于k，就把最大的poll出去。最终返回堆顶。

```Java
class Solution {
    public int kthSmallest(int[][] matrix, int k) {
        PriorityQueue<Integer> queue = new PriorityQueue<>(new Comparator<Integer>() {
            public int compare(Integer o1, Integer o2) {
                return o2 - o1;
            }
        });
        
        for (int i = 0; i < matrix.length; i++){
            for (int j = 0; j < matrix[0].length; j++){
                queue.add(matrix[i][j]);
                if (queue.size() > k) queue.poll();
            }
        }
        return queue.peek();
    }
}
```

- 解二 二分查找

这题可以通过二分查找做，化作在有序数列中找出一个分界点，使得分界点的左边正好有k个数。当low >= high时返回low，就是第k小的元素。
两个端点：low是数组最左上角的数字，high是数组最右下角的数字。从数组的每一行开始判断，找出整个数组中少于两极端中端数mid的计数有多少。再根据比较k继续二分查找。
```Java
class Solution {
    public int kthSmallest(int[][] matrix, int k) {
        int low = matrix[0][0], high = matrix[matrix.length - 1][matrix[0].length - 1];
        while (low < high){
            int mid = low + (high - low) / 2;
            int count = 0, j = matrix[0].length - 1;
            for (int i = 0; i < matrix.length; i++){
                while (j >= 0 && matrix[i][j] > mid) j--;
                count += (j + 1);
            }
            if (count < k) low = mid + 1;
            else high = mid;
        }
        return low;
    }
}
```

#### 74 Search a 2D Matrix
[Search a 2D Matrix](https://leetcode.com/problems/search-a-2d-matrix/)

编写一个高效的算法来判断 m x n 矩阵中，是否存在一个目标值。该矩阵具有如下特性：

每行中的整数从左到右按升序排列。
每行的第一个整数大于前一行的最后一个整数。
示例 1:
```
输入:
matrix = [
  [1,   3,  5,  7],
  [10, 11, 16, 20],
  [23, 30, 34, 50]
]
target = 3
输出: true
```
示例 2:
```
输入:
matrix = [
  [1,   3,  5,  7],
  [10, 11, 16, 20],
  [23, 30, 34, 50]
]
target = 13
输出: false
```

2020.04.17

-----
数组的每一行首尾衔接上可以看作有序数列，那么问题可以简化为二分查找在有序数列中找到目标值。low是数组左上角数字的序号，high是数组右下角数字的序号。
```Java
class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        if (matrix.length == 0) return false;
        int low = 0, high = matrix[0].length * matrix.length - 1;//0 11
        while (low <= high){
            int mid = low + (high - low) / 2;//5
            int x = mid / matrix[0].length, y = mid % matrix[0].length;
            if (matrix[x][y] == target) return true;
            else if (matrix[x][y] < target) low = mid + 1;
            else high = mid - 1;
        }
        return false;
    }
}
```

#### 240 Search a 2D Matrix II
[Search a 2D Matrix II](https://leetcode.com/problems/search-a-2d-matrix-ii/)

编写一个高效的算法来搜索 m x n 矩阵 matrix 中的一个目标值 target。该矩阵具有以下特性：

每行的元素从左到右升序排列。
每列的元素从上到下升序排列。
示例:

现有矩阵 matrix 如下：
```
[
  [1,   4,  7, 11, 15],
  [2,   5,  8, 12, 19],
  [3,   6,  9, 16, 22],
  [10, 13, 14, 17, 24],
  [18, 21, 23, 26, 30]
]
```
给定 target = 5，返回 true。

给定 target = 20，返回 false。

2020.04.18

-----
既然是有序，可以用二分，就是先二分查找第一列第一行，都没有的话就递推到第二列第二行，直到最后一行最后一列也被查找过。
```Java
class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        if (matrix == null || matrix.length == 0){
            return false;
        }

        int shorterDim = Math.min(matrix.length, matrix[0].length);
        for (int i = 0; i < shorterDim; i++){
            boolean verticalFound = binarySearch(matrix, target, i, true);
            boolean horizontalFound = binarySearch(matrix, target, i, false);
            if (verticalFound || horizontalFound){
                return true;
            }
        }
        return false;
    }
    private boolean binarySearch(int[][] matrix, int target, int start, boolean vertical){
        int lo = start;
        int hi;
        if (vertical){
            hi = matrix[0].length - 1;
        }else hi = matrix.length - 1;

        while (hi >= lo){
            int mid = (lo + hi)/2;
            if (vertical){//searching a column
                if (matrix[start][mid] < target){
                    lo = mid + 1;
                }else if (matrix[start][mid] > target){
                    hi = mid - 1;
                }else return true;
            }else{//search a row
                if (matrix[mid][start] < target){
                    lo = mid + 1;
                }else if (matrix[mid][start] > target){
                    hi = mid - 1;
                }else return true;
            }
        }
        return false;
    }
}
```