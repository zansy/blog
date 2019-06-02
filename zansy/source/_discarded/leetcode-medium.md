title: LeetCode 中等题汇总（20190522 更新/8）
author: zansy
tags: []
categories:
  - 解题观察日记
toc: true
date: 2019-05-16 00:17:00
---
Medium思考题，分类/题意/思路/代码。
<!--more-->
## 数组
### 基础
#### 80
[Remove Duplicates from Sorted Array II](https://leetcode.com/problems/remove-duplicates-from-sorted-array-ii/submissions/)

给出一组排好序的数，要求去除数组里数，使其中数最多重复两次。返回数组长度。

Given | Modified
:-: | :-:
[1,1,1,2,2,3]|[1,1,2,2,3]
[0,0,1,1,1,1,2,3,3]|[0,0,1,1,2,3,3]
[0,0,0,0,0]|[0,0]

说是medium但其实也很水，保持两个数组概念，和#26 #27类似，设定变量，判定当前数组当前位和新数组前两位是否相同，不同则存入。覆盖原数组。

2019.05.16
```
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


#### 299
[Bulls and Cows](https://leetcode.com/problems/bulls-and-cows/)

给出两串数字字符，找出字符中数值相同且位置对应的数字个数记为A，找出字符中数值相同但位置不同的数字个数记为B，输出记录。注意1123和0111等有重复数字的，第二串字符中第三位第四位的1只与第一串字符的第一位1符合B记录条件，因此B=1。11123和00111，B=2。

2019.05.18

- 解一

简单。字符串转换数组，在转换的过程中就可以开始记录位置相同数值相同的数字个数了，获得A，最终得到去除这些数的两个数组。

之后求B，遍历第一个数组的时候，每一位都和第二个数组的全部数比对，遇到数值相同的即将其转换成999防止后续再被比对上，记录B并退出比较循环，保证B值确实是一对一的对应结果。
```java
class Solution {
    public String getHint(String secret, String guess) {
        int[] secretInt = new int[secret.length()];
        int[] guessInt = new int[guess.length()];
        int unbulls = 0;
        for(int i = 0; i < secret.length(); i++){
            if(secret.charAt(i)!=guess.charAt(i)){
                secretInt[unbulls] = Integer.parseInt(secret.substring(i,i+1));
                guessInt[unbulls] = Integer.parseInt(guess.substring(i,i+1));
                unbulls++;
            }
        }
        int cows = 0;
        for(int i = 0; i < unbulls; i++){
            for(int j = 0; j < unbulls; j++){
                if(secretInt[i] == guessInt[j]){
                    cows++;
                    guessInt[j] = 999;
                    break;
                }
            }
        }
        return secret.length() - unbulls + "A" + cows + "B";
    }
}
```

- 解二

求A值的部份好像不能再简化了。

解一的空间复杂度稍高，额外要求了两个数组，有没有可能在记录A值的时候同时记录B值呢？

由桶排序想到可以设立一个0-9的数组bucket，第一个字符串中每个出现的数字用正整数记录在bucket中，第二个字符串中每个出现的数字用负整数记录在bucket中。

但在此之前检测一下，如果第二个字符串中出现的数字在bucket中的记录是反常的正整数，可以说明第二个字符串中出现的数字已经在第一个字符串中出现过了。同理可推反常的负整数也是如此。针对这两种不同的情况分别记录B值。
```java
class Solution {
    public String getHint(String secret, String guess) {
        int unbulls = 0;
        int cows = 0;
        int[] bucket = new int[10];
        for(int i = 0; i < secret.length(); i++){
            if(secret.charAt(i) != guess.charAt(i)){
                unbulls++;
                if(bucket[guess.charAt(i)-'0'] > 0) cows++;
                if(bucket[secret.charAt(i)-'0'] < 0) cows++;
                bucket[secret.charAt(i)-'0']++;
                bucket[guess.charAt(i)-'0']--;
            }
        }
        return secret.length() - unbulls + "A" + cows + "B";
    }
}
```

#### 134
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
```
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
```
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

#### 229
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

#### 274
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

#### 275
[H-Index II](https://leetcode.com/problems/h-index/)

题意如#274，但这次给出的是升序数组，要求时间复杂度对数级。

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

#### 220
[Contains Duplicate III](https://leetcode.com/problems/contains-duplicate-iii/)

给出一组无序数nums，一个k，一个t，要求存在nums[i]和nums[j]，使得二者绝对值小于等于t，同时i与j的绝对值小于等于k。

思路如219，设置一个长度为k+1的窗口，例如[1,5,9,1,5,9]，k=2，t=3时，则需要设置一个长度为3的窗口，在窗口中观察是否有这样的两个数字满足绝对值小于等于t的情况。不满足则窗口往后平移一位。159，591，915，159，这样平移。

但是窗口中的数字要怎样方便快捷地判断其绝对值呢？想办法排序，但是桶排的话需要的空间太大了，要怎么样尽可能地压缩空间呢？压缩空间，就是减小排序后差值最小的两个数的距离。这里的思路是把当前数除以t，例如[1,5,9,1,5,9]，除以t = 3得到的号码牌就是[0,1,3,0,1,3]，可见1和5之间的距离被压缩了。

但是尽管相邻，也不代表1和5之间满足绝对值小于等于3的条件，这个时候仍需要参考原数值的值，因此在判断条件后，要将当前数值的号码牌和值本身一起存入一个长度为k的HashMap中。

但这一题小陷阱很多，t是绝对值，肯定不能小于0。那么为了制作号码牌的时候设置除以t+1；数组中存在负数，那么每次先减去一个设定的最小值，即加上一个最大值。

2019.05.21
```
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

#### 55
[Jump Game](https://leetcode.com/problems/jump-game/)

给出一组无序的非负整数，每个数字是指在其索引的位置上能往右走的最大步数，问在第一位是否能走到数组最后一位。

具体解法看这一篇博文：[待补]()

2019.05.22
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