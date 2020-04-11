title: LeetCode 简单题汇总（20200410 更新/45）
author: zansy
tags:
  - 水
categories:
  - 解题观察日记
toc: true
date: 2019-08-27 12:06:00
---
Easy练手感题，分类/题意/思路/代码。
<!--more-->
## 数组 Array
### 基础
#### 26
[Remove Duplicates from Sorted Array](https://leetcode.com/problems/remove-duplicates-from-sorted-array/)

给出一组排好序的数，要求去除数组里重复的数值，返回数组长度。对空间有要求。

---
和#27差不多，设定变量，判定当前位和前一位是否相同，不同则存入，覆盖原数组。

2019.05.04
```Java
class Solution {
    public int removeDuplicates(int[] nums) {
        int flag = 1;
        for(int i =1;i<nums.length;i++){
            if(nums[i] != nums[i-1]) {
                nums[flag] = nums[i];
                flag++;
            }
        }
        return flag;
    }
}
```

#### 27
[Remove Element](https://leetcode.com/problems/remove-element/)

给出一个数组和一个指定的数，要求从数组中删除所有该数并返回数组长度。对空间有要求。

---
那么设定一个变量为数组长度计数，遇到和给定值相同的就跳过去，不同的则覆盖原数组。

2019.05.04
```Java
class Solution {
    public int removeElement(int[] nums, int val) {
        int flag = 0;
        for(int i =0;i<nums.length;i++){
            if(nums[i]!=val){
                nums[flag] = nums[i];
                flag++;
            }
        }
        return flag;
    }
}
```
#### 189
[Rotate Array](https://leetcode.com/problems/rotate-array/)

给出一组数，每次将原数组的最后一位提到数组的第一位来，执行给定的n次

2019.05.16

- 解一 空间复杂度O(1)

存储最后一位数，从后往前分别读取本身的前一位进行覆盖，第一位放入存储的原最后一位数。

```Java
class Solution {
    public void rotate(int[] nums, int k) {
        for(int i = 0;i < k;i++){
            int flag = nums[nums.length-1];
            for(int j = nums.length-1;j > 0;j--){
                nums[j] = nums[j-1];
            }
            nums[0] = flag;
        }
    }
}
```

- 解二 空间复杂度O(n) 

非常基础的用另一个数组作中介存储一次变化后的数组，再用递归完成n次操作

```Java
class Solution {
    public void rotate(int[] nums, int k) {
        if(k == 0){
            return;
        }
        int[] store = new int[nums.length];
        for(int i = 0;i < nums.length-1; i++){
            store[i+1] = nums[i];
        }
        store[0] = nums[nums.length-1];
        for(int i = 0;i < nums.length; i++){
            nums[i] = store[i];
        }
        rotate(nums,k-1);
    }
}
```

- 解三 翻转

之前两种都是很容易想到的死板方法，但事实上根据示例：

Input | k | Output
:-:|:-:|:-:
[1,2,3,4,5,6,7] | 3 | [5,6,7,1,2,3,4]

可以发现本质上k就是需要提到前面的数字位数，即需要把后三位提到前面来。但这个思路涉及到一个问题——k大于数组本身长度的话需要注意。因此有`k%=nums.size();`的前要补充。
这时候就可以扩展开很多解法，比如说“用一个数组保留后k位数组，将前面的数组向后移动k位，再写入前面的数组”等，简单的这里不再赘述。
“原地”的话，可以考虑反转。即先整体反转，[7,6,5,4,3,2,1]，再将数组分为两部分，分为0——k-1（即[7,6,5]）和k——nums.size()-1（即[4,3,2,1]）; 最后两部分分别反转，就能得到正确的结果。 
```Java
class Solution {
    public void rotate(int[] nums, int k) {
        k%=nums.length;
        reverse(nums,0,nums.length-1);
        reverse(nums,0,k-1);
        reverse(nums,k,nums.length-1);
    }
    public void reverse(int[] nums, int start, int end){
        while (start < end){
            int temp = nums[start];
            nums[start] = nums[end];
            nums[end] = temp;
            start++;
            end--;
        }
    }
}
```
- 解四 直接放置在最终的位置上

>We can directly place every number of the array at its required correct position. But if we do that, we will destroy the original element. Thus, we need to store the number being replaced in a temp variable. Then, we can place the replaced number(temp) at its correct position and so on, n times, where n is the length of array. 

每次调换一个元素，后一个元素的调换基于上一个的位置。
1. 记录数组第一个元素 currentNum 和 currentIndex 信息
2. 计算第一位元素进行旋转后的 newIndex
3. 使用 temp 记录数组 newIndex 位置的元素值
4. 调换第一位元素值到数组 newIndex 位置上，此时数组 [1,2,3,1,5,6,7]
5. 此时 currentIndex 和 newIndex 不相等，记录当前要调换的值 currentNum = temp，进行下一次调换，
6. 此时要调换的元素值就是 currentNum，其原来的位置就是 newIndex，然后计算该元素旋转后的新 newIndex 值

以此类推，如果判断 currentIndex = newIndex 说明两个位置的元素相互交换完成，那直接往后推一位

```java
public class Solution {
    public void rotate(int[] nums, int k) {
        k = k % nums.length;
        int count = 0;
        for (int start = 0; count < nums.length; start++) {
            int current = start;
            int prev = nums[start];
            do {
                int next = (current + k) % nums.length;
                int temp = nums[next];
                nums[next] = prev;
                prev = temp;
                current = next;
                count++;
            } while (start != current);
        }
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

#### 118
[Pascal's Triangle](https://leetcode.com/problems/pascals-triangle/)

根据输入的行数，输出一个杨辉三角

2018.05.18

```Java
class Solution {
    public List<List<Integer>> generate(int numRows) {
        List<List<Integer>> allrows = new ArrayList<List<Integer>>();
        ArrayList<Integer> row = new ArrayList<Integer>();
        for(int i=0;i<numRows;i++)
        {
            row.add(0, 1);//每一行中添加行首为1
            for(int j=1;j<row.size()-1;j++)//在第三行时进入循环
                row.set(j, row.get(j)+row.get(j+1));//设置除行首行尾的部份为上一行两位的和
            allrows.add(new ArrayList<Integer>(row));//为了保持原地修改，在结果中添加新的复制行
        }
        return allrows;
    }
}
```

#### 119
[Pascal's Triangle II](https://leetcode.com/problems/pascals-triangle-ii/)

根据输入的行数，输出杨辉三角指定的行数。

---
把#118的改一下就行，注意这里的0行是1而非空白。

2018.05.19

```java
class Solution {
    public List<Integer> getRow(int rowIndex) {
        List<Integer> answer = new ArrayList<Integer>();
        for(int i = 0; i <= rowIndex; i++){
            answer.add(0,1);
            for(int j = 1; j < answer.size() - 1; j++){
                answer.set(j, answer.get(j)+answer.get(j+1));
            }
        }
        return answer;
    }
}
```

#### 169
[Majority Element](https://leetcode.com/problems/majority-element/)

给出一组无序数，要求返回在数组占二分之一以上的一个数。

2018.05.19

- 解一 排序找中位数

既然能在数组中占二分之一以上，那么排好序以后直接返回数组中间的数即可
```Java
class Solution {
    public int majorityElement(int[] nums) {
        for(int i=0; i<nums.length; i++){//表示n次排序过程。
            for(int j=1; j<nums.length-i; j++){
                if(nums[j-1] > nums[j]){//前面的数字大于后面的数字就交换
                    int temp;
                    temp = nums[j-1];
                    nums[j-1] = nums[j];
                    nums[j]=temp;
                }
            }
        }
        return nums[nums.length/2];
    }
}
```
- 解二 空间复杂度常数级 时间复杂度O(n)

既然题目里说肯定存在答案嘛，那就遍历数组，设立一个变量存数组中的数字并计数，遍历时遇到相同则+1，不同则-1，计数到0了，说明该数字并不符合“存在多于二分之一”条件，舍弃，存入当前值继续进行计数操作。
其实这个方法意义不太大。
```Java
public class Solution {
    public int majorityElement(int[] nums) {
        int major = nums[0], count = 1;
        for(int i = 1; i < nums.length; i++){
            if (major == nums[i]) count++;
            else count--;
            if(count == 0){
                count = 1;
                major = nums[i];
            }
        }
        return major;
    }
}
```
#### 217
[Contains Duplicate](https://leetcode.com/problems/contains-duplicate/)
判断一组无序数中是否有重复数字，有则返回true，否则false

---
没什么好说的…桶排也可以；Hash也可以`Set<Integer> set = new HashSet<>(nums.length)`，然后遍历数组添加/判断set.contains就行。这里用了一个自带sort。

2019.05.20
```Java
class Solution {
    public boolean containsDuplicate(int[] nums) {
        Arrays.sort(nums);
        for(int i = 0; i < nums.length-1; i++){
            if(nums[i] == nums[i+1]){
                return true;
            }
        }
        return false;
    }
}
```

#### 219
[Contains Duplicate II](https://leetcode.com/problems/contains-duplicate-ii/)
给出一组无序数和k，问数组中是否存在两个数nums[i] = nums[j]，并且|i-j| <= k

2019.05.20
- 解一

普通思路，遍历数组中除最后一位的每一位数，将它们分别和自己之后的数值比较，有相同的话直接用后数的序号-当前序号，符合条件就返回true，否则false
```Java
class Solution {
    public boolean containsNearbyDuplicate(int[] nums, int k) {
        for(int i = 0; i < nums.length-1; i++){
            for (int j = i + 1; j < nums.length; j++){
                if(nums[i] == nums[j] && j - i <= k ) return true;
            }
        }
        return false;
    }
}
```

- 解二

就算是简单题也要思考：有没有别的方法？时间复杂度是不是有点高？
可以做一个模拟，只遍历一次。假设有一个只能存下k个数的容器，一边向其中存入新的数舍去旧的数，一边看该k-1个数中有没有相等的
```Java
class Solution {
    public boolean containsNearbyDuplicate(int[] nums, int k) {
        Set<Integer> set = new HashSet<Integer>();
        for(int i = 0; i < nums.length; i++){
            if(i > k) set.remove(nums[i-k-1]);
            if(!set.add(nums[i])) return true;
        }
        return false;
    }
}
```

#### 121
[Best Time to Buy and Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/submissions/)
给出一组无序数prices，要求得出prices[j] - prices[i]最大值是多少，且prices[j] > prices[i]，j > i

---

暴力方法不多说，两个for循环一个maxProfit变量，一组组比对即可。

这里只用了一个循环，每次先看最小值可不可以更新，不可以的话看最大值可不可以更新，判断依据是最大利润值获得了更新。

2019.05.23
```Java
class Solution {
    public int maxProfit(int[] prices) {
       int minPrice = Integer.MAX_VALUE;
        int maxProfit = 0;
        for( int i = 0; i < prices.length; i++){
            if(prices[i] < minPrice)
                minPrice = prices[i];
            else if(prices[i] - minPrice > maxProfit)
                maxProfit = prices[i] - minPrice;
        }
        return maxProfit;
    }
}
```

#### 122
[Best Time to Buy and Sell Stock II](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/)

给出一组无序数prices，表示商品在不同日期（索引）时对应的不同价格。可以多次买入卖出，但每次只能保持一次完整的交易，即买入新的前必须卖出旧的。

---

通过价格波动图可以看见，每次在谷底的时候买入，在峰值卖出，对应代码就是只计算上升阶段的高低差值

2019.05.24

```Java
class Solution {
    public int maxProfit(int[] prices) {
        int maxProfit = 0;
        for(int i = 1; i < prices.length; i++){
            if(prices[i] > prices[i - 1])
                maxProfit += prices[i] - prices[i-1];
        }
        return maxProfit;
    }
}
```

## 字符串 String
### 基础
#### 28
[Implement strStr()](https://leetcode.com/problems/implement-strstr/)

字符串匹配

2019.05.25
```Java
class Solution {
    public int strStr(String haystack, String needle) {
        for (int i = 0; ; i++) {
            for (int j = 0; ; j++) {
                if (j == needle.length()) return i;
                if (i + j == haystack.length()) return -1;
                if (needle.charAt(j) != haystack.charAt(i + j)) break;
            }
        }
    }
}
```
#### 14

编写一个函数来查找字符串数组中的最长公共前缀。
如果不存在公共前缀，返回空字符串 ""。

2019.06.10

- 解一

前缀字符串嘛，那就遍历字符串数组中每一位开始比对，从第一位开始，后面对不上的就删除。这种称为横向比对。
```Java
class Solution {
    public String longestCommonPrefix(String[] strs) {
        if (strs.length == 0)return "";
        String prefix = strs[0];
        for (String str : strs){
            while (str.indexOf(prefix) != 0){//第一位不匹配
                prefix = prefix.substring(0, prefix.length() - 1);//舍去最后一位
                if (prefix.isEmpty())return "";//为空则无匹配
            }
        }
        return prefix;
    }
}
```

- 解二

纵向比对就是用charAt先取每个字符串的第一个字母，再取第二个，以此类推，直到对不上了为止，就从第一位到对不上的这一位进行切割。
```Java
class Solution {
    public String longestCommonPrefix(String[] strs) {
        if (strs.length == 0) return "";
        for (int i = 0; i < strs[0].length() ; i++){
            char c = strs[0].charAt(i);
            for (int j = 1; j < strs.length; j ++) {
                if (i == strs[j].length() || strs[j].charAt(i) != c)
                    return strs[0].substring(0, i);
            }
        }
        return strs[0];
    }
}
```

- 解三

二分查找，找到终点。每次切割一半，如果前一半在所有字符串中都存在，则终点扩大范围，否则将前一半再进行二分。

```Java
class Solution {
    public String longestCommonPrefix(String[] strs) {
        if (strs.length == 0) return "";

        int minLength = Integer.MAX_VALUE;
        for (String str : strs){
            minLength = Math.min(minLength, str.length());
        }

        int low = 1; int high = minLength;
        while (low <= high){
            int middle = (low + high) / 2;
            if (isCommonPrefix(strs, middle))
                low = middle + 1;
            else high = middle - 1;
        }
        return strs[0].substring(0, (low + high) / 2);
    }
    private  boolean isCommonPrefix(String[] strs, int length){
        String prefix = strs[0].substring(0, length);
        for (int i = 1; i < strs.length; i++){
            if (!strs[i].startsWith(prefix))return false;
        }
        return true;
    }
}
```
- 解四

还有一种堆排也很有趣，详情可看官方题解：[14. Longest Common Prefix](https://leetcode.com/articles/longest-common-prefix/)

#### 58
[Length of Last Word](https://leetcode.com/problems/length-of-last-word/)

给定一个仅包含大小写字母和空格 ' ' 的字符串，返回其最后一个单词的长度。

如果不存在最后一个单词，请返回 0 。

说明：一个单词是指由字母组成，但不包含任何空格的字符串。
"a" 返回1
" " 返回0
"a " 返回1

---
剪枝处理，剩下纯粹的需要考虑的"a b c"etc，找到最后一个空格出现的位置，把包括空格在内的长度都去除即是所求长度。

2019.06.10
```Java
class Solution {
    public int lengthOfLastWord(String s) {
        return s.trim().length()-s.trim().lastIndexOf(" ")-1;
    }
}
```

#### 387
[First Unique Character in a String](https://leetcode.com/problems/first-unique-character-in-a-string/)

给定一个字符串，找到它的第一个不重复的字符，并返回它的索引。如果不存在，则返回 -1。
HashMap存储每个字母出现的次数，再次遍历输出第一个只出现过一次的字母。

2019.06.10
```Java
class Solution {
    public int firstUniqChar(String s) {
       HashMap<Character,Integer> lettersCount = new HashMap<>();
        for ( int i = 0; i < s.length(); i++){
            lettersCount.put(s.charAt(i),lettersCount.getOrDefault(s.charAt(i),0)+1);
        }
        for ( int i = 0; i < s.length(); i++){
            if (lettersCount.get(s.charAt(i)) == 1) return i;
        }
        return -1; 
    }
}
```
#### 383
[Ransom Note](https://leetcode.com/problems/ransom-note/)

给定一个赎金信 (ransom) 字符串和一个杂志(magazine)字符串，判断第一个字符串ransom能不能由第二个字符串magazines里面的字符构成。如果可以构成，返回 true ；否则返回 false。

(题目说明：为了不暴露赎金信字迹，要从杂志上搜索各个需要的字母，组成单词来表达意思。)

---

仍然是HashMap，存储杂志中的字母计数，再从HashMap中取赎金信所需的字母并-1，如果本身没有的话就置为0再-1。这样一来只要遍历看一次是不是计数小于0，是的话就说明不能构成。

2019.06.10
```Java
class Solution {
    public boolean canConstruct(String ransomNote, String magazine) {
        HashMap<Character,Integer> magazineCount = new HashMap<>();
        for (int i = 0; i < magazine.length(); i++){
            magazineCount.put(magazine.charAt(i), magazineCount.getOrDefault(magazine.charAt(i),0)+1);
        }

        for (int i = 0; i < ransomNote.length(); i++){
            magazineCount.put(ransomNote.charAt(i),magazineCount.getOrDefault(ransomNote.charAt(i),0)-1);
        }
        for (char c : magazineCount.keySet()){
            if (magazineCount.get(c) < 0) return false;
        }
        return true;
    }
}
```
#### 344
[Reverse String](https://leetcode.com/problems/reverse-string/)

编写一个函数，其作用是将输入的字符串反转过来。输入字符串以字符数组 char[] 的形式给出。

不要给另外的数组分配额外的空间，你必须原地修改输入数组、使用 O(1) 的额外空间解决这一问题。

你可以假设数组中的所有字符都是 ASCII 码表中的可打印字符。

---

取中位，把中位两边的分别置换即可。

关于这一题更需要思考的是多种解法：[[JAVA] Simple and Clean with Explanations [6 Solutions]](https://leetcode.com/problems/reverse-string/discuss/80937/JAVA-Simple-and-Clean-with-Explanations-6-Solutions)

2019.06.11
```Java
class Solution {
    public String reverseString(char[] s) {
        for (int i = 0; i < s.length / 2; i++){
            //s[0] == s[length - 1], s[1] = s[length - 1 - 1]
            char temp = s[i];
            s[i] = s[s.length - 1 - i];
            s[s.length - 1 - i] = temp;
        }
        return s.toString();
    }
}
```
#### 345
[Reverse Vowels of a String](https://leetcode.com/problems/reverse-vowels-of-a-string/)
编写一个函数，以字符串作为输入，反转该字符串中的元音字母。

---

惊了翻了一下高票解答程序，思路竟然和我一样…莫名其妙感觉很荣幸
但是高票答案用char数组存储，我用的原字符的charAt，然后以subString字符串切割代替数组转换成字符串的功能。不过高票解答里的String vowels真的有点妙…我很蠢用HashSet存的，学习了！

不过这解答的时空复杂度都不低诶…有没有办法再简化呢？好困先睡

2019.06.11
```Java
class Solution {
    public String reverseVowels(String s) {
        String vowels = "aeiouAEIOU";
        char[] chars = s.toCharArray();
        int start = 0, end = s.length() - 1;
        while (start < end){
            while (!vowels.contains(chars[start]+"") && start < end){
                start++;
            }
            while (!vowels.contains(chars[end]+"") && start < end){
                end--;
            }
            char temp = chars[start];
            chars[start] = chars[end];
            chars[end] = temp;
            start++; end--;
        }
        return new String(chars);
    }
}
```

#### 205
[Isomorphic Strings](https://leetcode.com/problems/isomorphic-strings/)

给定两个字符串 s 和 t，判断它们是否是同构的。

如果 s 中的字符可以被替换得到 t ，那么这两个字符串是同构的。

所有出现的字符都必须用另一个字符替换，同时保留字符的顺序。两个字符不能映射到同一个字符上，但字符可以映射自己本身。

---

很常规的思路…HashMap存储s当前值和t上对应的值，如果发现已经存过了，再进行判断，如果结果不一样直接返回false，否则就揭过。
注意“ab” “aa”的例子，此时需要再判断是否已存在该value。

2019.06.12
```Java
class Solution {
    public boolean isIsomorphic(String s, String t) {
        HashMap<Character,Character> map = new HashMap<>();
        for (int i = 0; i < s.length(); i++){
            if (map.containsKey(s.charAt(i))){
                if(map.get(s.charAt(i)) != t.charAt(i))return false;
            }
            else {
                if(map.containsValue(t.charAt(i))) return false;
                map.put(s.charAt(i), t.charAt(i));
            }
        }
        return true;
    }
}
```
#### 290
[Word Pattern](https://leetcode.com/problems/word-pattern/)
给定一种规律 pattern 和一个字符串 str ，判断 str 是否遵循相同的规律。

这里的 遵循 指完全匹配，例如， pattern 里的每个字母和字符串 str 中的每个非空单词之间存在着双向连接的对应规律。

2019.06.13
- 解一

普通思路…解法和#205差不多，就是对应着两组分别放入HashMap的键值中，如果已有的话，判断一下是不是一致的，一致的话就放过。没有的话，判断一下值是否出现过。这本质上就是看键值对是否唯一。
```Java
class Solution {
    public boolean wordPattern(String pattern, String str) {
        HashMap<Character,String> map = new HashMap<>();
        if (str.split(" ").length != pattern.length()) return false;
        for (int i = 0; i < pattern.length(); i++){
            if(map.containsKey(pattern.charAt(i))){
                if(map.get(pattern.charAt(i)).equals(str.split(" ")[i]) == false) return false;
            }else {
                if (map.containsValue(str.split(" ")[i]))return false;
                map.put(pattern.charAt(i), str.split(" ")[i]);
            }
        }
        return true;
    }
}
```
- 解二

这是看的高票解答代码…惊了 原来HashMap的put方法会返回最后一次出现的索引！
这个答案就是并行查看模式字母和单词，并比较它们最后出现的索引
```Java
class Solution {
    public boolean wordPattern(String pattern, String str) {
        String[] words = str.split(" ");
        if (words.length != pattern.length())
            return false;
        Map index = new HashMap();
        for (Integer i=0; i<words.length; ++i)
            if (index.put(pattern.charAt(i), i) != index.put(words[i], i))
                return false;
        return true;
    }
}
```

#### 242
[Valid Anagram](https://leetcode.com/problems/valid-anagram/)

给定两个字符串 s 和 t ，编写一个函数来判断 t 是否是 s 的字母异位词。

2019.06.13

- 解一

第一反应就是字母排序…将两个字符串转换成字符组，对其中字母进行排序，比对排好序的字符组。

一个补充！Array.equals()可以直接作为返回结果返回！
```Java
class Solution {
    public boolean isAnagram(String s, String t) {
        if (s.length() != t.length()) return false;
        char[] sArray = s.toCharArray();
        char[] tArray = t.toCharArray();
        Arrays.sort(sArray);
        Arrays.sort(tArray);
        for (int i = 0; i < s.length(); i++){
            if(sArray[i] != tArray[i]) return false;
        }
        return true;
    }
}
```
- 解二

用字母表做，先遍历一个字符串，存储每个字母对应出现的次数。在遍历第二个字符串的时候，在字母表中减去出现的次数。最后遍历字母表，如果有不等于0的就说明两个字符串不相同，返回false

（或者在遍历第二个字符串的时候就判断，如果出现的次数减到了-1，说明两者不同，直接返回false就行了
```Java
class Solution {
    public boolean isAnagram(String s, String t) {
        if (s.length() != t.length()) {
            return false;
        }
        int[] counter = new int[26];
        for (int i = 0; i < s.length(); i++) {
            counter[s.charAt(i) - 'a']++;
            counter[t.charAt(i) - 'a']--;
        }
        for (int count : counter) {
            if (count != 0) {
                return false;
            }
        }
        return true;
    }
}
```
#### 38
[Count and Say](https://leetcode.com/problems/count-and-say/)

报数序列是一个整数序列，按照其中的整数的顺序进行报数，得到下一个数。其前五项如下：
```
1.     1
2.     11
3.     21
4.     1211
5.     111221
1 被读作  "one 1"  ("一个一") , 即 11。
11 被读作 "two 1s" ("两个一"）, 即 21。
21 被读作 "one 2",  "one 1" （"一个二" ,  "一个一") , 即 1211。
```

给定一个正整数 n（1 ≤ n ≤ 30），输出报数序列的第 n 项。

注意：整数顺序将表示为一个字符串。

---

这题明明不简单？？？首先要意识到用递归，接着想到遍历前字符串的每一个字符。通过观察可以发现，如果前后有重复的，压缩重复的并在前面插入重复次数；如果没有重复的，在前面插入1。

转换成程序思想就是：遇到相同的就跳过相同的这一段，同时记录下相同的次数，作为前缀，带上当前相同的字符本身，记入结果字符串中。

2019.06.16
```Java
class Solution {
    public String countAndSay(int n) {
        if (n == 1) return "1";
        String prev = countAndSay(n - 1);
        StringBuilder stringBuilder = new StringBuilder();
        int i = 0;
        while (i < prev.length()){
            int same = 0, cur = i;
            while ((cur + same) < prev.length() && prev.charAt(cur + same) == prev.charAt(cur) )
                same++;
            stringBuilder.append(same);
            stringBuilder.append(prev.charAt(cur));
            i += same;
        }
        return stringBuilder.toString();
    }
}
```

#### 168
[Excel Sheet Column Title](https://leetcode.com/problems/excel-sheet-column-title/)
给定一个正整数，返回它在 Excel 表中相对应的列名称。

例如，
```
    1 -> A
    2 -> B
    3 -> C
    ...
    26 -> Z
    27 -> AA
    28 -> AB 
    ...
```
---

找规律题，A是1，AA是26*1+1，AAA是26(26+1)+1，因此每次n需要先--，再进行`/26`操作层层剥开。

子问题相关，很容易想到递归，所以高票答案里有一行代码解决本题的：`return n == 0 ? "" : convertToTitle(--n / 26) + (char)('A' + (n % 26));`，参见[My 1 lines code in Java, C++, and Python](https://leetcode.com/problems/excel-sheet-column-title/discuss/51398/My-1-lines-code-in-Java-C%2B%2B-and-Python)

2019.06.19
```Java
class Solution {
    public String convertToTitle(int n) {
        StringBuilder result = new StringBuilder();
        while (n > 0){
            n--;
            result.insert(0,(char)('A' + n % 26));
            n /= 26;
        }
        return result.toString();
    }
}
```
#### 171
[Excel Sheet Column Number](https://leetcode.com/problems/excel-sheet-column-number/)
给定一个Excel表格中的列名称，返回其相应的列序号。

例如，
```
    A -> 1
    B -> 2
    C -> 3
    ...
    Z -> 26
    AA -> 27
    AB -> 28 
    ...
```
---

看了一下好像基本思路都是一个for循环？其实这题比168简单，应该先写这一题来着…
突破口仍然是观察找规律，A是1，AA是26+1，AAA是26<sup>2</sup>+1。我这里用了Math里的指数函数，但其实也可以result直接自乘，看了一下运行时间差别不大。

2019.06.19

```Java
class Solution {
    public int titleToNumber(String s) {
        int result = 0;
        for (int i = 0; i < s.length(); i++){
            result += Math.pow(26, s.length() - i - 1) * (s.charAt(i) - 'A' + 1);
            //result = result * 26 + (s.charAt(i) - 'A' + 1), i++);
        }
        return result;
    }
}
```

#### 13
[Roman to Integer](https://leetcode.com/problems/roman-to-integer/submissions/)

罗马数字包含以下七种字符: I， V， X， L，C，D 和 M。
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
给定一个罗马数字，将其转换成整数。输入确保在 1 到 3999 的范围内。

----

看了一圈觉得我的答案真是又快又好……其他的大概有用一个数组直接加然后剔除掉特殊情况的。
我这边的思路大概就是按序遍历，基本上每位代表的数字都应该比前一位小的，如果出现特殊情况就说明出现4和9这类需要减的数字了，那就在答案中加上由当前数和它后一位数进行减法操作得到的数字，同时在下一次遍历的时候跳过它的后一位字母。

2019.06.19
```Java
class Solution {
    public int romanToInt(String s) {
        int result = 0;
        HashMap<Character, Integer> map = new HashMap<>();
        map.put('I', 1);
        map.put('V', 5);
        map.put('X', 10);
        map.put('L', 50);
        map.put('C', 100);
        map.put('D', 500);
        map.put('M', 1000);
        for (int i = 0; i < s.length(); i++){
            if(i + 1 < s.length() && map.get(s.charAt(i)) < map.get(s.charAt(i + 1))){
                result += map.get(s.charAt(i + 1)) - map.get(s.charAt(i));
                i++;
            }else {
                result += map.get(s.charAt(i));
            }
        }
        return result;
    }
}
```


## 数学 Math
### 基础
#### 7
[Reverse Integer](https://leetcode.com/problems/reverse-integer/)

长整数翻转。

**Note:**
>Assume we are dealing with an environment which could only store integers within the 32-bit signed integer range: [−2<sup>31</sup>,  2<sup>31</sup> − 1]. For the purpose of this problem, assume that your function returns 0 when the reversed integer overflows.

2019.05.26
```Java
class Solution {
    public int reverse(int x) {
        long result = 0;
        while(x != 0){
            result *= 10;
            result += x % 10;
            x /= 10;
        }
        return (int)result == result ? (int)result : 0;
    }
}
```

#### 66
[Plus One](https://leetcode.com/problems/plus-one/)

给出一组数，其表示一个正整数。要求这个正整数+1后的数仍然以数组的形式展示。

---

从后向前遍历数组，小于9就+1返回，等于9就置为0，不返回等到遍历的前一位+1再返回。
在这一轮中还没有返回结果的，说明是9s，则直接新建一个长度+1的数组前置为1即可。
**Note:**
>The last part of code is only for the case that the whole input array is 9s.
For example : 99999-----> 100000
Any other case would return in the loop.

2019.05.27
```java
class Solution {
    public int[] plusOne(int[] digits) {
        int n = digits.length;
        for(int i=n-1; i>=0; i--) {
            if(digits[i] < 9) {
                digits[i]++;
                return digits;
            }
            digits[i] = 0;
        }
        int[] newNumber = new int [n+1];
        newNumber[0] = 1;
        return newNumber;
    }
}
```
#### 258
[Add Digits](https://leetcode.com/problems/add-digits/)

给定一个非负整数 num，反复将各个位上的数字相加，直到结果为一位数。

2019.06.20

- 解一

普通思路 使用递归不断相加
```Java
class Solution {
    public int addDigits(int num) {
        if (num < 10) return num;
        int sum  = 0;
        while (num > 0){
            sum += num % 10;
            num /= 10;
        }
        return addDigits(sum);
    }
}
```
- 解二

>10^k % 9 = 1; 
a*10^k % 9 = a % 9 

理解了上述基本后，举例说明：
x = 23456; 
x = 2* 10000 + 3 * 1000 + 4 * 100 + 5 * 10 + 6

2 * 10000 % 9 = 2 % 9; 
3 * 1000 % 9 = 3 % 9; 
4 * 100 % 9 = 4 % 9; 
5 * 10 % 9 = 5 % 9; 

 x % 9 = (2 + 3 + 4 + 5 + 6) % 9

所以 23456 % 9 = (2 + 3 + 4 + 5 + 6) % 9

```Java
class Solution {
    public int addDigits(int num) {
        if (num == 0) return 0;
        if (num % 9 == 0) return 9;
        else return num % 9;
    }
}
```
#### 67
[Add Binary](https://leetcode.com/problems/add-binary/)

给定两个二进制字符串，返回他们的和（用二进制表示）。

输入为非空字符串且只包含数字 1 和 0。

2019.06.20
- 解一

普通思路，用StringBuilder进行二进制先补足后进位，分四种情况讨论。在编写这四种情况的时候，可以明显感觉到可以简化，简化部分在解二给出。
```Java
class Solution {
    public String addBinary(String a, String b) {
        StringBuilder aStrB = new StringBuilder(a);
        aStrB.reverse();
        StringBuilder bStrB = new StringBuilder(b);
        bStrB.reverse();
        StringBuilder result = new StringBuilder();
        int aStrBLength = aStrB.length(), bStrBLength = bStrB.length();
        while(aStrBLength > bStrBLength){
            bStrB.append("0");
            aStrBLength--;
        }
        while(bStrBLength > aStrBLength){
            aStrB.append("0");
            bStrBLength--;
        }
        int nextAdd = 0;
        int i = 0;
        for (i = 0; i < aStrB.length(); i++){
            if ((aStrB.charAt(i) - '0') + (bStrB.charAt(i) - '0') + nextAdd == 2){
                nextAdd = 1;
                result.append("0");
            }else if ((aStrB.charAt(i) - '0') + (bStrB.charAt(i) - '0') + nextAdd == 1){
                result.append("1");
                nextAdd = 0;
            }else if ((aStrB.charAt(i) - '0') + (bStrB.charAt(i) - '0') + nextAdd == 3){
                result.append("1");
                nextAdd = 1;
            }else {
                result.append("0");
                nextAdd = 0;
            }
        }
        if (nextAdd == 1)result.append("1");
        return result.reverse().toString();
    }
}
```
- 解二

思路同上，仍然是二进制倒置、从下至上每位添加、进位。
```Java
class Solution {
    public String addBinary(String a, String b) {
        StringBuilder sb = new StringBuilder();
        int i = a.length() - 1, j = b.length() -1, carry = 0;
        while (i >= 0 || j >= 0) {
            int sum = carry;
            if (j >= 0) sum += b.charAt(j--) - '0';
            if (i >= 0) sum += a.charAt(i--) - '0';
            sb.append(sum % 2);
            carry = sum / 2;
        }
        if (carry != 0) sb.append(carry);
        return sb.reverse().toString();
    }
}
```
#### 69
[Sqrt(x)](https://leetcode.com/problems/sqrtx/)

实现 int sqrt(int x) 函数。

计算并返回 x 的平方根，其中 x 是非负整数。

由于返回类型是整数，结果只保留整数的部分，小数部分将被舍去。

2019.06.23
- 解一

最普通思路
```Java
class Solution {
    public int mySqrt(int x) {
        for (int i = 1; i <= x; i++) {
            if (Math.pow(i + 1, 2) > x && Math.pow(i, 2) <= x) return i;
        }
        return 0;
    }
}
```
- 解二

用了Newton证明，在我这个外行眼里差不多就是二分…
[Algorithm using Newton's method](https://en.wikipedia.org/wiki/Integer_square_root#Using_only_integer_division)

![Proof](https://discuss.leetcode.com/assets/uploads/files/1502465638921-95f6f6f0-a1b8-4f19-919d-1a0b53e18aac-image.png)
```Java
class Solution {
    public int mySqrt(int x) {
        long r = x;
        while (r*r > x)
            r = (r + x/r) / 2;
        return (int) r;
    }
}
```
#### 367
[Valid Perfect Square](https://leetcode.com/problems/valid-perfect-square/)

给定一个正整数 num，编写一个函数，如果 num 是一个完全平方数，则返回 True，否则返回 False。

说明：不要使用任何内置的库函数，如  sqrt。

---
直接用了#69的方法，但看了一下好像用一个挺基础的数学概念也能做：[A square number is 1+3+5+7+..., JAVA code](https://leetcode.com/problems/valid-perfect-square/discuss/83874/A-square-number-is-1%2B3%2B5%2B7%2B...-JAVA-code)

2019.06.23
```Java
class Solution {
    public boolean isPerfectSquare(int num) {
        long r = num;
        if (num == 1)return true;
        while (r*r > num) {
            r = (r + num / r) / 2;
            if (r * r == num) return true;
        }
        return false;
    }
}
```
#### 204
[Count Primes](https://leetcode.com/problems/count-primes/)

统计所有小于非负整数 n 的质数的数量。

---
反向思维，先从2开始，设立双重循环，先设置n以内所有的非质数，再在n的范围内看有几个非质数。亮点应该是boolean数组，一次读取。

2019.06.24

```Java
class Solution {
    public int countPrimes(int n) {
        if (n <= 1) return 0;
        boolean[] notPrime = new boolean[n];
        for (int i = 2; i < Math.sqrt(n); i++){
            for (int j = 2; i * j < n; j++){
                notPrime[i * j] = true;
            }
        }
        int result = 0;
        for (int i = 2; i < n; i++) {
            if (!notPrime[i]) result++;
        }
        return result;
    }
}
```
## 动态规划 Dynamic Programming
### 一维
#### 70
[Climbing Stairs](https://leetcode.com/problems/climbing-stairs/)

假设你正在爬楼梯。需要 n 阶你才能到达楼顶。

每次你可以爬 1 或 2 个台阶。你有多少种不同的方法可以爬到楼顶呢？

注意：给定 n 是一个正整数。

2019.07.08

---
这题也是典型的动态规划题，先考虑到的可以有递归处理。
- 递归：
```Java
class Solution {
    public int climbStairs(int n) {
        return climbStairsRecursion(0, n);
    }
    public int climbStairsRecursion(int i, int n) {
        if (i > n) return 0;
        if (i == n) return 1;
        return climbStairsRecursion(i + 1, n) + climbStairsRecursion(i + 2, n);
    }
}
```
但这种一般都容易超时，因此需要再进行简化

- 带有记忆功能的递归

通过加入一个memo数组，把曾经计算过的结果存入数组中
```Java
public class Solution {
    public int climbStairs(int n) {
        int memo[] = new int[n + 1];
        return climb_Stairs(0, n, memo);
    }
    public int climb_Stairs(int i, int n, int memo[]) {
        if (i > n) {
            return 0;
        }
        if (i == n) {
            return 1;
        }
        if (memo[i] > 0) {
            return memo[i];
        }
        memo[i] = climb_Stairs(i + 1, n, memo) + climb_Stairs(i + 2, n, memo);
        return memo[i];
    }
}
```

- 动态规划

因为可以分为子问题解决，并存在最优子结构，因此可用动态规划思路解决。
要抵达第i阶台阶有两种途径：在第i-1的台阶上走一步；在第i-2的台阶上走两步。所以可以得出这样的式子：dp[i] = dp[i - 1] + dp[i - 2];
```Java
class Solution {
    public int climbStairs(int n) {
        if (n == 1) {
            return 1;
        }
        int[] dp = new int[n + 1];
        dp[1] = 1;
        dp[2] = 2;
        for (int i = 3; i <= n; i++) {
            dp[i] = dp[i - 1] + dp[i - 2];
        }
        return dp[n];
    }
}
```

## 链表 Linked List
### 基础
#### 206
[Reverse Linked List](https://leetcode.com/problems/reverse-linked-list/)

反转一个单链表。

示例:
```
输入: 1->2->3->4->5->NULL
输出: 5->4->3->2->1->NULL
```

2020.03.22

---
这道题有两个思路，
一个是设置一个空指针，从第一个节点开始，每个节点暂时指向这个空指针，直到前部分反转成功后再迭代到下一个。
另一个是从最后开始，每次指向前一位

- 迭代

```Java
class Solution {
    public ListNode reverseList(ListNode head) {
        ListNode pre = null;
        ListNode cur = head;
        while (cur != null){
            ListNode next = cur.next;
            cur.next = pre;
            pre = cur;
            cur = next;
        }
        return pre;
    }
}
```

- 递归

```Java
class Solution {
    public ListNode reverseList(ListNode head) {
        if (head == null || head.next == null) return head;
        ListNode cur = reverseList(head.next);
        head.next.next = head;
        head.next = null;
        return cur;
    }
}
```

#### 141
[Linked List Cycle](https://leetcode.com/problems/linked-list-cycle/)

给定一个链表，判断链表中是否有环。

为了表示给定链表中的环，我们使用整数 pos 来表示链表尾连接到链表中的位置（索引从 0 开始）。 如果 pos 是 -1，则在该链表中没有环。

 

示例 1：
```
输入：head = [3,2,0,-4], pos = 1
输出：true
解释：链表中有一个环，其尾部连接到第二个节点。
```

2020.02.25

----
这题的输入pos之类的其实是看不见的，题目就是求问一个链表里怎么判断有环。
一个方法很简单，用个set存储每个结点，并查看是否有在之前存在，如果存在过就是存在环。

- Hash

```Java
public class Solution {
    public boolean hasCycle(ListNode head) {
        Set<ListNode> nodesSeen = new HashSet<>();
        while (head != null) {
            if (nodesSeen.contains(head)) {
                return true;
            } else {
                nodesSeen.add(head);
            }
            head = head.next;
        }
        return false;
    }
}
```

但上个方法的空间复杂度会有点高，还有一种巧思，设置两个快慢指针，在起点一起往前（快结点应比慢结点早一位，这样按理说应该是永远都追不上），如有相遇就是说明链表中存在环。

```Java
public class Solution {
    public boolean hasCycle(ListNode head) {
        if(head == null || head.next == null) return false;
        ListNode quick = head.next;
        ListNode slow = head;
        while(quick != slow){
            if(quick == null || quick.next == null) return false;
            quick = quick.next.next;
            slow = slow.next;
        }
        return true;
    }
}
```

#### 237
[Delete Node in a Linked List](https://leetcode.com/problems/delete-node-in-a-linked-list/)

请编写一个函数，使其可以删除某个链表中给定的（非末尾）节点，你将只被给定要求被删除的节点。

示例 1:

输入: head = [4,5,1,9], node = 5
输出: [4,1,9]
解释: 给定你链表中值为 5 的第二个节点，那么在调用了你的函数之后，该链表应变为 4 -> 1 -> 9.
示例 2:

输入: head = [4,5,1,9], node = 1
输出: [4,5,9]
解释: 给定你链表中值为 1 的第三个节点，那么在调用了你的函数之后，该链表应变为 4 -> 5 -> 9.

2020.03.26

----
准确地来说是删除下一个结点。参数只给了当前结点，链表内也不存在环可以追溯到之前的结点，所以只可能把当前的值改成下一结点的值，当前的next改成下一结点的next。

```Java
class Solution {
    public void deleteNode(ListNode node) {
        node.val = node.next.val;
        node.next = node.next.next;
    }
}
```

#### 83
[Remove Duplicates from Sorted List](https://leetcode.com/problems/remove-duplicates-from-sorted-list/)
给定一个排序链表，删除所有重复的元素，使得每个元素只出现一次。

示例 1:
```
输入: 1->1->2
输出: 1->2
```
示例 2:
```
输入: 1->1->2->3->3
输出: 1->2->3
```
2020.03.28

----
这一题画个图也很好做啦，设置两个指针一前一后，遇到指针值相同的情况就直接跳过指向下一个。
```Java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        if(head == null || head.next == null) return head;
        ListNode cur = head;
        ListNode next = head.next;
        while (next != null){
            if (cur.val == next.val){
                cur.next = next.next;
                next = next.next;
                continue;
            }
            
            cur = cur.next;
            if(next!= null)
                next = next.next;
        
        }
        return head;
    }
}
```

#### 203
[Remove Linked List Elements](https://leetcode.com/problems/remove-linked-list-elements/)

删除链表中等于给定值 val 的所有节点。

示例:
```
输入: 1->2->6->3->4->5->6, val = 6
输出: 1->2->3->4->5
```

2020.03.28

----
这一题也很好做啦基本就是82的代码改了一改。注意设置一个neg指针指向head结点，最终返回也要返回neg.next。
```Java
class Solution {
    public ListNode removeElements(ListNode head, int val) {
        if(head == null) return head;
        ListNode neg = new ListNode(0);
        neg.next = head;
        ListNode cur = neg;
        ListNode next = neg.next;
        while (next != null){
            if (next.val == val){// cur-next-next.next
                cur.next = next.next;
                next = next.next;
                continue;
            }

            cur = cur.next;
            if(next!= null)
                next = next.next;
        }
        return neg.next;
    }
}
```

#### 160
[Intersection of Two Linked Lists](https://leetcode.com/problems/intersection-of-two-linked-lists/)

编写一个程序，找到两个单链表相交的起始节点。

2020.03.30

-----
- 解一 HashSet


HashSet就行啦，先存储下headA里每个结点，到了遍历HeadB的时候，看set里有没有该结点存在。
```Java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        Set<ListNode> set = new HashSet<ListNode>();
        while (headA != null){
            set.add(headA); 
            headA = headA.next;
        }
        while (headB != null){
            if (set.contains(headB)){
                return headB;
            }
            headB = headB.next;
        }
        return null;
    }
}
```

- 解二 双指针

这道题还有另外一个很有意思的解法，设置指针A遍历headA，遍历完了去遍历HeadB；指针B也是，先遍历HeadB，再遍历headA，如果两指针路上有相遇，说明两链表相交。

这其实也可以理解为两个人速度一致， 走过的路程一致。那么肯定会同一个时间点到达终点。如果到达终点的最后一段路两人都走的话，那么这段路上俩人肯定是肩并肩手牵手的。

“若相交，链表A： a+c, 链表B : b+c. a+c+b+c = b+c+a+c 。则会在公共处c起点相遇。若不相交，a +b = b+a 。因此相遇处是NULL。”

```Java
public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
    if (headA == null || headB == null) return null;
    ListNode pA = headA, pB = headB;
    while (pA != pB) {
        pA = pA == null ? headB : pA.next;
        pB = pB == null ? headA : pB.next;
    }
    return pA;
}
```

#### *21
[Merge Two Sorted Lists](https://leetcode.com/problems/merge-two-sorted-lists/)

将两个升序链表合并为一个新的升序链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。 

示例：
```
输入：1->2->4, 1->3->4
输出：1->1->2->3->4->4
```

2020.03.31

-----
- 迭代法

```Java
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        ListNode neg = new ListNode(0);
        ListNode pre = neg;
        while (l1 != null && l2 != null){
            if (l1.val <= l2.val){
                pre.next = l1;
                l1 = l1.next;
            }else {
                pre.next = l2;
                l2 = l2.next;
            }
            pre = pre.next;
        }
        pre.next = l1 == null ? l2 : l1;
        
        return neg.next;
    }
}
```
- 递归法

```Java
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        if (l1 == null) return l2;
        else if (l2 == null) return l1;
        else if (l1.val < l2.val){
            l1.next = mergeTwoLists(l1.next, l2);
            return l1;
        }
        else {
            l2.next = mergeTwoLists(l1, l2.next);
            return l2;
        }
    }
}
```

## 二分查找 Binary Search
### 基础
#### 278 
[First Bad Version](https://leetcode.com/problems/first-bad-version/)
你是产品经理，目前正在带领一个团队开发新的产品。不幸的是，你的产品的最新版本没有通过质量检测。由于每个版本都是基于之前的版本开发的，所以错误的版本之后的所有版本都是错的。

假设你有 n 个版本 [1, 2, ..., n]，你想找出导致之后所有版本出错的第一个错误的版本。

你可以通过调用 bool isBadVersion(version) 接口来判断版本号 version 是否在单元测试中出错。实现一个函数来查找第一个错误的版本。你应该尽量减少对调用 API 的次数。

示例:
```
给定 n = 5，并且 version = 4 是第一个错误的版本。

调用 isBadVersion(3) -> false
调用 isBadVersion(5) -> true
调用 isBadVersion(4) -> true

所以，4 是第一个错误的版本。 
```
2020.04.01

-------

二叉查找

```Java
public class Solution extends VersionControl {
    public int firstBadVersion(int n) {
        int left = 1;
        int right = n;
        while (left < right){
            int mid = left + (right - left) / 2;
            if (isBadVersion(mid)){
                right = mid;
            }else left = mid + 1;
        }
        return left;
    }
}
```

#### 35
[Search Insert Position](https://leetcode.com/problems/search-insert-position/)
给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。

你可以假设数组中无重复元素。

示例 1:
```
输入: [1,3,5,6], 5
输出: 2
```
示例 2:
```
输入: [1,3,5,6], 2
输出: 1
```
示例 3:
```
输入: [1,3,5,6], 7
输出: 4
```
示例 4:
```
输入: [1,3,5,6], 0
输出: 0
```
2020.04.02

-----
```Java
class Solution {
    public int searchInsert(int[] nums, int target) {
        int left = 0, right = nums.length - 1, mid;
        while (left <= right){
            mid = left + (right - left) / 2;
            if (nums[mid] > target){
                right = mid - 1;
            }else if (nums[mid] < target){
                left = mid + 1;
            }else return mid;
        }
        return left;
    }
}
```

#### 374
[Guess Number Higher or Lower](https://leetcode.com/problems/guess-number-higher-or-lower/)

我们正在玩一个猜数字游戏。 游戏规则如下：
我从 1 到 n 选择一个数字。 你需要猜我选择了哪个数字。
每次你猜错了，我会告诉你这个数字是大了还是小了。
你调用一个预先定义好的接口 guess(int num)，它会返回 3 个可能的结果（-1，1 或 0）：
```
-1 : 我的数字比较小
 1 : 我的数字比较大
 0 : 恭喜！你猜对了！
 ```
示例 :
```
输入: n = 10, pick = 6
输出: 6
```
2020.04.08

-----
很基础的二分查找
```Java
public class Solution extends GuessGame {
    public int guessNumber(int n) {
        int left = 0, right = n;
        while (left <= right){
            int mid = left + (right - left)/2;
            if (guess(mid) == 0) return mid;
            else if (guess(mid) == -1) right = mid - 1;
            else left = mid + 1;
        }
        return -1;
    }
}
```

#### 349
[Intersection of Two Arrays](https://leetcode.com/problems/intersection-of-two-arrays/)

给定两个数组，编写一个函数来计算它们的交集。

示例 1:
```
输入: nums1 = [1,2,2,1], nums2 = [2,2]
输出: [2]
```

示例 2:
```
输入: nums1 = [4,9,5], nums2 = [9,4,9,8,4]
输出: [9,4]
```

说明:
```
输出结果中的每个元素一定是唯一的。
我们可以不考虑输出结果的顺序。
```

2020.04.10

----

和二分查找没啥关系

- 解一
用HashSet的办法，转换两个数组，迭代某一个数组，检查是否有值存在于另一个数组中。

```Java
class Solution {
    public int[] intersection(int[] nums1, int[] nums2) {
       HashSet<Integer> set1 = new HashSet<Integer>();
        for (Integer n : nums1) set1.add(n);
        HashSet<Integer> set2 = new HashSet<Integer>();
        for (Integer n : nums2) set2.add(n);
        
        int[] output = new int[set1.size()];
        int index = 0;
        for (Integer s : set1)
            if (set2.contains(s)) 
                output[index++] = s;
            
        return Arrays.copyOf(output, index); 
    }
}
```

- 解二

Java有内置函数retainAll，直接看代码学用法就行
```Java
class Solution {
    public int[] intersection(int[] nums1, int[] nums2) {
       HashSet<Integer> set1 = new HashSet<Integer>();
        for (Integer n : nums1) set1.add(n);
        HashSet<Integer> set2 = new HashSet<Integer>();
        for (Integer n : nums2) set2.add(n);

        set1.retainAll(set2);
        int[] output = new int[set1.size()];
        int index = 0;
        for (Integer s : set1)
            if (set2.contains(s))
                output[index++] = s;

        return output;
    }
}
```

#### 350
[Intersection of Two Arrays II](https://leetcode.com/problems/intersection-of-two-arrays-ii/)

给定两个数组，编写一个函数来计算它们的交集。

示例 1:
```
输入: nums1 = [1,2,2,1], nums2 = [2,2]
输出: [2,2]
```
示例 2:
```
输入: nums1 = [4,9,5], nums2 = [9,4,9,8,4]
输出: [4,9]
```
说明：
```
输出结果中每个元素出现的次数，应与元素在两个数组中出现的次数一致。
我们可以不考虑输出结果的顺序。
```
2020.04.10

-----
用Hashmap。
举例num1:49544，num2:94984
设立一个hashmap m存储较短数组中数字的出现次数，如num1就是4-3 9-1 5-1。在遍历另一数组的时候，遇到相同数字就在m中-1，并且将该数字计入输出数组中，如果减为0就不计。
如遍历num2时第一个数字：4-3 9-0 5-1，第二个数字：4-2 9-0 5-1，第三个数字：4-2 9-0 5-1……如此。

```Java
class Solution {
    public int[] intersect(int[] nums1, int[] nums2) {
        if (nums1.length > nums2.length) return intersect(nums2, nums1);

        HashMap<Integer,Integer> m = new HashMap<Integer, Integer>();
        for (int n : nums1) m.put(n, m.getOrDefault(n, 0) + 1);

        int k = 0;
        for (int n : nums2){
            int count = m.getOrDefault(n,0);
            if (count > 0){
                nums1[k++] = n;
                m.put(n, count - 1);
            }
        }
        return Arrays.copyOfRange(nums1, 0, k);
    }
}
```