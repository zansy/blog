title: 剑指offer刷题记录（20210409 更新/4）

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
## 3. 数组中重复的数字（easy）
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
## 7. 重建二叉树（medium）
[重建二叉树](https://leetcode-cn.com/problems/zhong-jian-er-cha-shu-lcof/)

输入某二叉树的前序遍历和中序遍历的结果，请重建该二叉树。假设输入的前序遍历和中序遍历的结果中都不含重复的数字。

例如，给出
```
前序遍历 preorder = [3,9,20,15,7]
中序遍历 inorder = [9,3,15,20,7]
```
返回如下的二叉树：
```
    3
   / \
  9  20
    /  \
   15   7
```
2021.04.09

---
用递归做，根据前序遍历第一位是根结点的value，按照人工运算地分出左右子树的前序和中序遍历数组，然后递归地实现他们就好了。
```java
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
    public TreeNode buildTree(int[] preorder, int[] inorder) {
        TreeNode root = null;
        if (preorder.length == 0) return root;
        root = new TreeNode(preorder[0]);

        root.left = root.right = null;
        if (preorder.length == 1) return root;
        int[] leftPreorder = null, leftInorder = null, rightPreorder = null, rightInorder = null;
        for (int i = 0; i < inorder.length; i++){
            if (inorder[i] == preorder[0]) {
                if (i != 0) {
                    leftInorder = Arrays.copyOfRange(inorder, 0, i);
                }else leftInorder = new int[]{};
                if (i + 1 != inorder.length) {
                    rightInorder = Arrays.copyOfRange(inorder, i + 1, inorder.length);
                }else rightInorder = new int[]{};

                if (leftInorder.length != 0){
                    leftPreorder = Arrays.copyOfRange(preorder, 1, leftInorder.length + 1);
                    root.left = buildTree(leftPreorder, leftInorder);
                }else leftPreorder = new int[]{};
                if (rightInorder.length != 0){
                    rightPreorder = Arrays.copyOfRange(preorder, leftInorder.length + 1, preorder.length);
                    root.right = buildTree(rightPreorder, rightInorder);
                }else rightPreorder = new int[]{};
            }
        }
        return root;
    }
}
```
## 26. 树的子结构（medium）
[树的子结构](https://leetcode-cn.com/problems/shu-de-zi-jie-gou-lcof/)

输入两棵二叉树A和B，判断B是不是A的子结构。(约定空树不是任意一个树的子结构)

B是A的子结构， 即 A中有出现和B相同的结构和节点值。

例如:
给定的树 A:
```
     3
    / \
   4   5
  / \
 1   2
```
给定的树 B：
```
   4 
  /
 1
```
返回 true，因为 B 与 A 的一个子树拥有相同的结构和节点值。

示例 1：
```
输入：A = [1,2,3], B = [3,1]
输出：false
```
示例 2：
```
输入：A = [3,4,5,1,2], B = [4,1]
输出：true
```
2021.04.09

---
用递归做。

先从根结点对比，看是否AB树相同，这里涉及到一个新的函数isEqual，同样也是递归，如果B树递归到了叶结点还没有因为终止条件被返回false，就说明之前的结点都是相同的，可以返回true，说明B树和A树相同。否则，如果A树到了叶子结点，而B树没有，则说明B必不是A的子树，返回false。如果都没有到叶子结点，就继续比较左右子树。

这时回到主函数，因为会出现情况一个结点对上了但左右子树没对上，因此需要继续针对A结点的别的子树进行递归找到和B树根结点相同的结点，继续进行比较。
```java
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
    public boolean isSubStructure(TreeNode A, TreeNode B) {
        boolean result = false;
        if (A != null && B != null){
            if (A.val == B.val){
                result = isEqual(A, B);
            }
            if (!result){
                result = isSubStructure(A.left, B);
            }
            if (!result){
                result = isSubStructure(A.right, B);
            }
        }

        return result;
    }
    private boolean isEqual(TreeNode A, TreeNode B) {
        if (B == null) return true;
        if (A == null || A.val != B.val)
            return false;
        return isEqual(A.left, B.left) && isEqual(A.right, B.right);
    }
}
```
## 27. 二叉树的镜像（medium）
[二叉树的镜像](https://leetcode-cn.com/problems/er-cha-shu-de-jing-xiang-lcof/)

请完成一个函数，输入一个二叉树，该函数输出它的镜像。

例如输入：
```
     4
   /   \
  2     7
 / \   / \
1   3 6   9
```
镜像输出：
```
     4
   /   \
  7     2
 / \   / \
9   6 3   1
```
示例 1：
```
输入：root = [4,2,7,1,3,6,9]
输出：[4,7,2,9,6,3,1]
```
2021.04.09

---
用递归做。

先从根结点对比，看是否AB树相同，这里涉及到一个新的函数isEqual，同样也是递归，如果B树递归到了叶结点还没有因为终止条件被返回false，就说明之前的结点都是相同的，可以返回true，说明B树和A树相同。否则，如果A树到了叶子结点，而B树没有，则说明B必不是A的子树，返回false。如果都没有到叶子结点，就继续比较左右子树。

这时回到主函数，因为会出现情况一个结点对上了但左右子树没对上，因此需要继续针对A结点的别的子树进行递归找到和B树根结点相同的结点，继续进行比较。
```java
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
    public boolean isSubStructure(TreeNode A, TreeNode B) {
        boolean result = false;
        if (A != null && B != null){
            if (A.val == B.val){
                result = isEqual(A, B);
            }
            if (!result){
                result = isSubStructure(A.left, B);
            }
            if (!result){
                result = isSubStructure(A.right, B);
            }
        }

        return result;
    }
    private boolean isEqual(TreeNode A, TreeNode B) {
        if (B == null) return true;
        if (A == null || A.val != B.val)
            return false;
        return isEqual(A.left, B.left) && isEqual(A.right, B.right);
    }
}
```