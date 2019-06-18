title: LeetCode 简单题汇总（20190619 更新/25）
author: zansy
tags:
  - 水
categories:
  - 解题观察日记
toc: true
date: 2019-05-26 12:06:00
---
Easy练手感题，分类/题意/思路/代码。
<!--more-->
## 数组 Array
### 基础
#### 26
[Remove Duplicates from Sorted Array](https://leetcode.com/problems/remove-duplicates-from-sorted-array/)

给出一组排好序的数，要求去除数组里重复的数值，返回数组长度。对空间有要求。

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

根据输入的行数，输出杨辉三角指定的行数。把118的改一下就行，注意这里的0行是1而非空白。

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

2019.06.10

仍然是HashMap，存储杂志中的字母计数，再从HashMap中取赎金信所需的字母并-1，如果本身没有的话就置为0再-1。这样一来只要遍历看一次是不是计数小于0，是的话就说明不能构成。

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

1.     1
2.     11
3.     21
4.     1211
5.     111221
1 被读作  "one 1"  ("一个一") , 即 11。
11 被读作 "two 1s" ("两个一"）, 即 21。
21 被读作 "one 2",  "one 1" （"一个二" ,  "一个一") , 即 1211。

给定一个正整数 n（1 ≤ n ≤ 30），输出报数序列的第 n 项。

注意：整数顺序将表示为一个字符串。

2019.06.16

这题明明不简单？？？首先要意识到用递归，接着想到遍历前字符串的每一个字符。通过观察可以发现，如果前后有重复的，压缩重复的并在前面插入重复次数；如果没有重复的，在前面插入1。

转换成程序思想就是：遇到相同的就跳过相同的这一段，同时记录下相同的次数，作为前缀，带上当前相同的字符本身，记入结果字符串中。

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

    1 -> A
    2 -> B
    3 -> C
    ...
    26 -> Z
    27 -> AA
    28 -> AB 
    ...

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

    A -> 1
    B -> 2
    C -> 3
    ...
    Z -> 26
    AA -> 27
    AB -> 28 
    ...

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