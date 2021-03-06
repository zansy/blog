title: 剑指offer刷题记录（20210416 更新/28）

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
遇到树就用递归吧
先判断root是不是null，null的话不存在左右子树直接返回就ok。然后开始交换左右子树，然后如果左子树存在就递归到左子树，如果右子树存在就递归到右子树。
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
    public TreeNode mirrorTree(TreeNode root) {
        if (root != null) {
            TreeNode temp = root.left;
            root.left = root.right;
            root.right = temp;
            if (root.left != null) mirrorTree(root.left);
            if (root.right != null) mirrorTree(root.right);
        }
        return root;
    }
}
```

## 28. 对称的二叉树（easy）
[对称的二叉树](https://leetcode-cn.com/problems/dui-cheng-de-er-cha-shu-lcof/)

请实现一个函数，用来判断一棵二叉树是不是对称的。如果一棵二叉树和它的镜像一样，那么它是对称的。

例如，二叉树 [1,2,2,3,4,4,3] 是对称的。
```
    1
   / \
  2   2
 / \ / \
3  4 4  3
```
但是下面这个 [1,2,2,null,3,null,3] 则不是镜像对称的:
```
    1
   / \
  2   2
   \   \
   3    3
```
示例 1：
```
输入：root = [1,2,2,3,4,4,3]
输出：true
```
示例 2：
```
输入：root = [1,2,2,null,3,null,3]
输出：false
```
2021.04.09

---
这题其实和之前的镜面翻转挺像的。。

总之看到二叉树先考虑递归递归递归！

因为这棵对称二叉树其实不用考虑根结点，那么其实就是看它的左右子树是否是镜面对称的就ok，那么可以新引入一个函数isMirror分析且方便递归。

函数就可以思考返回值处理，如果是完全镜面的情况，那么二者都会走到叶子结点，否则，如果其中一方先走到叶子结点或者结点上的值不相等则都会返回false，最后分别递归对比左树的左子树和右树的右子树以及左树的右子树和右树的左子树即可。
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
    public boolean isSymmetric(TreeNode root) {
        if (root != null) return isMirror(root.left, root.right);
        return true;
    }
    boolean isMirror(TreeNode left, TreeNode right){
        if (left == null && right == null) return true;
        if (left == null && right != null) return false;
        if (left != null && right == null) return false;
        if (left.val != right.val) return false;
        return isMirror(left.left, right.right)&&isMirror(left.right, right.left);
    }
}
```

## 32. I. 从上到下打印二叉树（medium）
[I. 从上到下打印二叉树](https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-lcof/)

从上到下打印出二叉树的每个节点，同一层的节点按照从左到右的顺序打印。

例如:
给定二叉树: [3,9,20,null,null,15,7],
```
    3
   / \
  9  20
    /  \
   15   7
```
返回：
```
[3,9,20,15,7]
```
2021.04.10

---
难得一题不用递归的树题。。只要想到用队列先进先出地保存树的左右子树然后分别取出再保存它们各自的左右子树以后，这题就很好做了。

不要用Java里自带的Queue，用LinkedList就可以了。

然后用循环！最开始还想用回溯啥的。。未免也太麻烦（。

队列先存入根结点，然后循环判断队列是否为空，不为空则进入循环，取出根结点并记录，队列存入其左右子树。

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
    public int[] levelOrder(TreeNode root) {
        if (root != null) {
            LinkedList<Integer> temp = new LinkedList<>();
            LinkedList<TreeNode> queue = new LinkedList<>();
            queue.add(root);
            while (queue.size() != 0){
                TreeNode tempTree = queue.poll();
                temp.add(tempTree.val);
                if (tempTree.left != null) queue.add(tempTree.left);
                if (tempTree.right != null) queue.add(tempTree.right);
            }
            int[] result = new int[temp.size()];
            for (int i = 0; i < temp.size(); i++){
                result[i] = temp.get(i);
            }
            return result;
        }
        return new int[]{};
    }
}
```

## 32. II. 从上到下打印二叉树 II（easy）
[II. 从上到下打印二叉树 II](https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-ii-lcof/)

从上到下按层打印二叉树，同一层的节点按从左到右的顺序打印，每一层打印到一行。

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
2021.04.10

---
>难得一题不用递归的树题。。只要想到用队列先进先出地保存树的左右子树然后分别取出再保存它们各自的左右子树以后，这题就很好做了。

>队列先存入根结点，然后循环判断队列是否为空，不为空则进入循环，取出根结点并记录，队列存入其左右子树。

和前一题差不多，唯一区别就是增加一个for循环，先算出queue的size，然后循环这个树，把每层的保存下来。

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
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> result = new LinkedList<>();
        if (root != null){
            LinkedList<TreeNode> queue = new LinkedList<>();
            queue.add(root);
            while (queue.size() != 0){
                LinkedList<Integer> layer = new LinkedList<>();
                int layers = queue.size();
                for (int i = 0; i < layers; i++){
                    TreeNode temp = queue.poll();
                    layer.add(temp.val);
                    if (temp.left != null) queue.add(temp.left);
                    if (temp.right != null) queue.add(temp.right);
                }
                result.add(layer);
            }
        }
        return result;
    }
}
```

## 32. III. 从上到下打印二叉树 III（medium）
[III. 从上到下打印二叉树 III](https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-iii-lcof/)

请实现一个函数按照之字形顺序打印二叉树，即第一行按照从左到右的顺序打印，第二层按照从右到左的顺序打印，第三行再按照从左到右的顺序打印，其他行以此类推。

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
  [20,9],
  [15,7]
]
```
2021.04.10

---
>难得一题不用递归的树题。。只要想到用队列先进先出地保存树的左右子树然后分别取出再保存它们各自的左右子树以后，这题就很好做了。

>队列先存入根结点，然后循环判断队列是否为空，不为空则进入循环，取出根结点并记录，队列存入其左右子树。

>(2)和前一题差不多，唯一区别就是增加一个for循环，先算出queue的size，然后循环这个树，把每层的保存下来。

在（2）的基础上再增加一笔，判断result中已经加了的层次是否是偶数，如果是偶数说明当前正在保存的层数是奇数层，因此将结点以addLast函数保存；否则以addFirst函数保存。

不用想得太复杂，要知道所有的题都可以靠观察和用笔画下来以帮助理解和编码，例如第二题中以层数型展示，就应该想到能不能保存层数，正好queue里每次都是当前层的结点，就可以循环queue的当前个数。例如这题要之字型，实际上就是保存结果的方式不一样，一个从尾部推进当前结点，一个从头部推进当前结点。判断奇偶数也可以用观察result中的size判断。

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
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> result = new LinkedList<>();
        if (root != null){
            LinkedList<TreeNode> queue = new LinkedList<>();
            queue.add(root);
            while (queue.size() != 0){
                LinkedList<Integer> layer = new LinkedList<>();
                int layers = queue.size();
                for (int i = 0; i < layers; i++){
                    TreeNode temp = queue.poll();
                    layer.add(temp.val);
                    if (temp.left != null) queue.add(temp.left);
                    if (temp.right != null) queue.add(temp.right);
                }
                result.add(layer);
            }
        }
        return result;
    }
}
```

## 34. 二叉树中和为某一值的路径（medium）
[二叉树中和为某一值的路径](https://leetcode-cn.com/problems/er-cha-shu-zhong-he-wei-mou-yi-zhi-de-lu-jing-lcof/)

输入一棵二叉树和一个整数，打印出二叉树中节点值的和为输入整数的所有路径。从树的根节点开始往下一直到叶节点所经过的节点形成一条路径。

示例:
给定如下二叉树，以及目标和 target = 22，
```
              5
             / \
            4   8
           /   / \
          11  13  4
         /  \    / \
        7    2  5   1
```
返回:
```
[
   [5,4,11,2],
   [5,8,4,5]
]
```
2021.04.10

---
这题感觉有点难。。但也不是不能做，其实根dfs思路差不多，既然要求的路径是从根结点到叶子结点的，就可以深度优先搜索每一种可能的路径，如果结果正好等于要求的数字，则保存并返回。
关于dfs递归函数，首先判断终止情况，一种是失败，当根结点为null的时候返回，否则就先把这个结点加在path上。另一种终止情况是成功，当到达叶子结点的时候，判断一下是否已经达成所需目标数字，是的话就把整条path加到结果集中。然后进行剪枝回溯，回到上一层。

判断完了终止情况就是函数中的主体情况，递归它的左子树和右子树，都递归完了就从path中把自己移出即可。

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public List<List<Integer>> pathSum(TreeNode root, int target) {
        List<List<Integer>> result = new LinkedList<>();
        dfs(root, target, new LinkedList<>(), result);
        return result;
    }
    private void dfs(TreeNode root, int rest,List<Integer> path, List<List<Integer>> result){
        if (root == null) return;
        path.add(root.val);
        if (root.left == null && root.right == null){
            if (rest == root.val) {
                result.add(new LinkedList<>(path));
                path.remove(path.size() - 1);
                return;
            }
        }
        dfs(root.left, rest - root.val, path, result);
        dfs(root.right, rest - root.val, path, result);
        path.remove(path.size() - 1);
    }
}
```

## 37. 序列化二叉树（hard）
[序列化二叉树](https://leetcode-cn.com/problems/xu-lie-hua-er-cha-shu-lcof/)

请实现两个函数，分别用来序列化和反序列化二叉树。

示例: 
```
你可以将以下二叉树：

    1
   / \
  2   3
     / \
    4   5

序列化为 "[1,2,3,null,null,4,5]"
```
2021.04.10

---
这题其实感觉主要是字符串处理？然后比较麻烦的是反序列。

通过观察可以看出，序列化其实就是之前的从上到下打印二叉树中的每个结点。继续用那个方法做就ok了，唯一的区别是如果treeNode是null的时候，例如左右子树不存在，那就可以用stringBuilder的append方法写上null，完了之后做一下字符串处理就可以。

反序列化有点麻烦，需要把数组中每个数字读出来，第一个数字是根结点，就先把建立起的根结点放入队列。遇到null的时候就跳过这一string数组的值。例如题中正常建立完根结点1和它的左右子树2，3后，2，3又存入队列中，首先弹出的是2，但是之后连续的两个都是null，那就再弹出3，建立它的左右子树。

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
public class Codec {

    // Encodes a tree to a single string.
    public String serialize(TreeNode root) {
        if(root == null) return "[]";
        StringBuilder result = new StringBuilder("[");
        LinkedList<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        while (queue.size() != 0){
            TreeNode treeNode = queue.poll();
            if (treeNode != null){
                result.append(treeNode.val+",");
                queue.add(treeNode.left);
                queue.add(treeNode.right);
            }else result.append("null,");
        }
        result.deleteCharAt(result.length() - 1);
        result.append("]");
        return result.toString();
    }

    // Decodes your encoded data to tree.
    public TreeNode deserialize(String data) {
        if (data.equals("[]")) return null;
        String[] values = data.substring(1, data.length() - 1).split(",");
        TreeNode treeNode = new TreeNode(Integer.parseInt(values[0]));
        LinkedList<TreeNode> queue = new LinkedList<>();
        queue.add(treeNode);
        int i = 1;
        while (queue.size() != 0){
            TreeNode node = queue.poll();
            if (!values[i].equals("null")){
                node.left = new TreeNode(Integer.parseInt(values[i]));
                queue.add(node.left);
            }
            i++;
            if (!values[i].equals("null")){
                node.right = new TreeNode(Integer.parseInt(values[i]));
                queue.add(node.right);
            }
            i++;
        }
        return treeNode;
    }
}

// Your Codec object will be instantiated and called as such:
// Codec codec = new Codec();
// codec.deserialize(codec.serialize(root));
```

## 54. 二叉搜索树的第k大节点（easy）
[二叉搜索树的第k大节点](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-di-kda-jie-dian-lcof/)

给定一棵二叉搜索树，请找出其中第k大的节点。

示例 1:
```
输入: root = [3,1,4,null,2], k = 1
   3
  / \
 1   4
  \
   2
输出: 4
```
示例 2:
```
输入: root = [5,3,6,2,4,null,null,1], k = 3
       5
      / \
     3   6
    / \
   2   4
  /
 1
输出: 4
```
2021.04.10

---
自己动手画一画，想到先右子树再根再左子树就很好做了。设立一个函数遍历，我最开始是用一个list存下来遍历的每个结点的值，因为是二叉搜索树，所以其实存下来就是一个递减序列，那么第k大就直接返回list的第k个数字就ok。

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
    public int kthLargest(TreeNode root, int k) {
        LinkedList<Integer> re = gothrough(root, new LinkedList<>());
        return re.get(k - 1);
    }
    LinkedList<Integer> gothrough(TreeNode root, LinkedList<Integer> result){
        if (root == null) return result;
        gothrough(root.right, result);
        result.add(root.val);
        gothrough(root.left, result);
        return result;
    }
}
```

后来看题解还能直接k--，但我当时在函数中把k的值一块传过去减减了，就导致总是失败，所以其实把k和result设为全局就ok。

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
    int result,k;
    public int kthLargest(TreeNode root, int k) {
        this.k = k;
        gothrough(root);
        return result;
    }
    void gothrough(TreeNode root){
        if (root == null) return;
        gothrough(root.right);
        k--;
        if (k == 0) result = root.val;
        gothrough(root.left);
    }
}
```

## 55. I. 二叉树的深度（easy）
[I. 二叉树的深度](https://leetcode-cn.com/problems/er-cha-shu-de-shen-du-lcof/)

输入一棵二叉树的根节点，求该树的深度。从根节点到叶节点依次经过的节点（含根、叶节点）形成树的一条路径，最长路径的长度为树的深度。

例如：

给定二叉树 [3,9,20,null,null,15,7]，
```
    3
   / \
  9  20
    /  \
   15   7
```
返回它的最大深度 3 。

2021.04.10

---
这题其实就是分层打印二叉树的分支题，只要额外加个result就ok，每到新的一层就++。

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
    public int maxDepth(TreeNode root) {
        int result = 0;
        LinkedList<TreeNode> queue = new LinkedList<>();
        if (root != null){
            queue.add(root);
            while (queue.size() != 0){
                int layerNodeNum = queue.size();
                for (int i = 0; i < layerNodeNum; i++){
                    TreeNode temp = queue.poll();
                    if (temp.left != null)queue.add(temp.left);
                    if (temp.right != null)queue.add(temp.right);
                }
                result++;
            }
        }
        return result;
    }
}
```

## 55. II. 平衡二叉树（easy）
[II. 平衡二叉树](https://leetcode-cn.com/problems/ping-heng-er-cha-shu-lcof/)

输入一棵二叉树的根节点，判断该树是不是平衡二叉树。如果某二叉树中任意节点的左右子树的深度相差不超过1，那么它就是一棵平衡二叉树。

示例 1:
```
给定二叉树 [3,9,20,null,null,15,7]

    3
   / \
  9  20
    /  \
   15   7
返回 true 。
```
示例 2:
```
给定二叉树 [1,2,2,3,3,null,null,4,4]

       1
      / \
     2   2
    / \
   3   3
  / \
 4   4
返回 false 。
```
2021.04.10

---
这题凭什么算简单啊，我觉得挺难的呀。。至少也得是个medium吧，一般人就算是画图也想不到是自底向上呀。

这题判断二叉树是否平衡，可以分解为它的左右子树之间的高度差是否没有超过1。既然要算高度，那当然要写一个函数算一个树的高度了。这个函数是个递归函数，因为求一个树的高度得求它的左右子树的高度。函数先判断返回值，如果为null的时候是个空树，没有高度，返回0。否则就是它的左右子树的最大值+1（它本身的层）。到了这一步一个计算高度的函数已经完成了。
但是如果在计算的过程中，发现了左右子树相差比较大，那其实它自己当前的高度是多少已经不重要了，因为这一题问的是是否平衡二叉树，而不是具体问树的高度。因此，如果在计算高度的过程中，发现自己就是个不平衡二叉树，那就返回-1。

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
    public boolean isBalanced(TreeNode root) {
        if (root == null)return true;
        if (treeDepth(root) != -1)return true;
        else return false;
    }
    private int treeDepth(TreeNode root){
        if (root == null) return 0;
        int left = treeDepth(root.left);
        int right = treeDepth(root.right);
        int depth = 0;
        
        if (left != -1 && right != -1) depth = Math.max(left, right) + 1;
        else depth = -1;
        
        if (Math.abs(left - right) > 1) depth =  -1;
        return depth;
    }
}
```

## 68. 二叉搜索树的最近公共祖先（easy）
[二叉搜索树的最近公共祖先](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-zui-jin-gong-gong-zu-xian-lcof/)

给定一个二叉搜索树, 找到该树中两个指定节点的最近公共祖先。

百度百科中最近公共祖先的定义为：“对于有根树 T 的两个结点 p、q，最近公共祖先表示为一个结点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（一个节点也可以是它自己的祖先）。”

例如，给定如下二叉搜索树:  root = [6,2,8,0,4,7,9,null,null,3,5]

![pic](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/14/binarysearchtree_improved.png)

示例 1:
```
输入: root = [6,2,8,0,4,7,9,null,null,3,5], p = 2, q = 8
输出: 6 
解释: 节点 2 和节点 8 的最近公共祖先是 6。
```
示例 2:
```
输入: root = [6,2,8,0,4,7,9,null,null,3,5], p = 2, q = 4
输出: 2
解释: 节点 2 和节点 4 的最近公共祖先是 2, 因为根据定义最近公共祖先节点可以为节点本身。
```
2021.04.10

---
无语死啦这题我也觉得不能算简单。。。这难度和那些按层打印二叉树的显然不一样啊。

既然题目里给出的是二叉搜索树，首先要想到的是二叉搜索树的特点：每个结点上的左子树小于它本身，右子树大于它本身。

通过观察很容易就能发现，如果一个结点处在给出的两个结点值的中间，说明这个结点就是他们的最近公共祖先。

想到这后面就很好写了，只要判断当前结点是否是两个给出结点的中间就行了，是的话就返回，不是的话就转成它的左或者右子树继续判断。

注意要return要return！就算是转到左右子树也要return！

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
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root != null) {
            if (root.val >= Math.min(p.val, q.val) && root.val <= Math.max(p.val, q.val)) return root;
            else if (root.val > Math.max(p.val, q.val)) return lowestCommonAncestor(root.left, p, q);
            else if (root.val < Math.min(p.val, q.val)) return lowestCommonAncestor(root.right, p, q);
        }
        return root;
    }
}
```

## 68. II. 二叉树的最近公共祖先（easy）
[II. 二叉树的最近公共祖先](https://leetcode-cn.com/problems/er-cha-shu-de-zui-jin-gong-gong-zu-xian-lcof/)

定一个二叉树, 找到该树中两个指定节点的最近公共祖先。

百度百科中最近公共祖先的定义为：“对于有根树 T 的两个结点 p、q，最近公共祖先表示为一个结点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（一个节点也可以是它自己的祖先）。”

例如，给定如下二叉树:  root = [3,5,1,6,2,0,8,null,null,7,4]

![pic](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/15/binarytree.png)

示例 1:
```
输入: root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 1
输出: 3
解释: 节点 5 和节点 1 的最近公共祖先是节点 3。
```
示例 2:
```
输入: root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 4
输出: 5
解释: 节点 5 和节点 4 的最近公共祖先是节点 5。因为根据定义最近公共祖先节点可以为节点本身。
```
2021.04.10

---
这题也一点儿不简单。。如果有针对父节点的指针的话就是一道求两个链表的公共结点的题，可以用两个指针同时走两个链表的长度，相交的时候就是公共结点。

但这里没有针对父结点的指针，先考虑递归。
递归函数想三个要点：
1. 结束条件。

当root为null的时候结束，返回null。
当左子树和右子树分别找到给出的两个结点，这样当前root结点肯定肯定是最小公共结点（可以画一画）。

2. 递归主体处理。

设置left和right两个递归，判断left和right中是否存在这样两个给出的结点。

3. 返回值

针对left和right有多种情况，如果left找到了p或者right找到了q，那就直接返回这个结点表示找到了。

如果一方没找到，那就返回另一方。

如果都没找到，就返回null。

----
另外还可以用hashmap做，遍历每个结点并存储下它的父节点，然后用一个set存储两个结点之一的一个结点的所有父系结点。最后遍历另一个结点的父系结点，如果有存在于set中就直接返回。
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
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null) return null;
        if (root == p || root == q) return root;
        TreeNode ifLeftExists = lowestCommonAncestor(root.left, p, q);
        TreeNode ifRightExists = lowestCommonAncestor(root.right, p, q);
        if (ifLeftExists == null && ifRightExists != null)
            return ifRightExists;
        if (ifRightExists == null && ifLeftExists != null)
            return ifLeftExists;
        if (ifLeftExists != null && ifRightExists != null) 
            return root;
        return null;
    }
}
```

## 4. 二维数组中的查找（medium）
[二维数组中的查找](https://leetcode-cn.com/problems/er-wei-shu-zu-zhong-de-cha-zhao-lcof/)

在一个 n * m 的二维数组中，每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个高效的函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。

示例:
```
现有矩阵 matrix 如下：
[
  [1,   4,  7, 11, 15],
  [2,   5,  8, 12, 19],
  [3,   6,  9, 16, 22],
  [10, 13, 14, 17, 24],
  [18, 21, 23, 26, 30]
]
给定 target = 5，返回 true。
给定 target = 20，返回 false。
```
2021.04.10

---
做完了树再做这些简直就像在切菜。。

说是medium但其实只要按照数组的规律去找就很方便。通过这个数组，可以根据二叉查找想到，每次比较最右上角的那个树，如果target比它大就下沉一行寻找，否则就往左获得新的比较数，直到找出那个数为止。

但这里要注意while的条件，不是找出这个树，而是数组越界的时候退出。证明数组找完了都没有，返回false就ok。
```java
class Solution {
    public boolean findNumberIn2DArray(int[][] matrix, int target) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) return false;
        int rows = matrix.length, cols = matrix[0].length, startRow = 0, startCol = cols - 1;
        while (startCol >= 0 && startRow < rows){
            if (matrix[startRow][startCol] == target) return true;
            else if (matrix[startRow][startCol] > target) startCol--;
            else startRow++;
        }
        return false;
    }
}
```

## 5. 替换空格（easy）
[替换空格](https://leetcode-cn.com/problems/ti-huan-kong-ge-lcof/)

请实现一个函数，把字符串 s 中的每个空格替换成"%20"。

示例 1：
```
输入：s = "We are happy."
输出："We%20are%20happy."
```
2021.04.10

---
这道题用库函数replaceAll是真的没意思，剑指Offer上为C语言提供了一种方法，先把字符串中所有的空格统计出来，那么更改后的字符串长度就应该在原先的基础上再加空格数*2。

然后用两个指针，一个指针1指向扩展后的字符串最后一位，另一个指针2指向原先字符串的最后一位，指针1复制指针2的内容，如果指针2是空格的话，指针1自行修改为%20，并往前跳三步。

两个指针在他们相遇的时候停止，这说明前面已经没有要替换的空格了。
```C++
class Solution {
public:
    string replaceSpace(string s) {
        int count = 0, len = s.size();
        // 统计空格数量
        for (char c : s) {
            if (c == ' ') count++;
        }
        // 修改 s 长度
        s.resize(len + 2 * count);
        // 倒序遍历修改
        for(int i = len - 1, j = s.size() - 1; i < j; i--, j--) {
            if (s[i] != ' ')
                s[j] = s[i];
            else {
                s[j - 2] = '%';
                s[j - 1] = '2';
                s[j] = '0';
                j -= 2;
            }
        }
        return s;
    }
};
```

## 6. 从尾到头打印链表（easy）
[从尾到头打印链表](https://leetcode-cn.com/problems/cong-wei-dao-tou-da-yin-lian-biao-lcof/)

输入一个链表的头节点，从尾到头反过来返回每个节点的值（用数组返回）。

示例 1：
```
输入：head = [1,3,2]
输出：[2,3,1]
```
2021.04.10

---
一个是用栈，一个是用递归。
```Java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public int[] reversePrint(ListNode head) {
        LinkedList<Integer> stack = new LinkedList<>();
        while (head != null){
            stack.push(head.val);
            head = head.next;
        }
        int[] result = new int[stack.size()];
        for (int i = 0; i < result.length; i++){
            result[i] = stack.pop();
        }
        return result;
    }
}
```

## 9. 用两个栈实现队列（easy）
[用两个栈实现队列](https://leetcode-cn.com/problems/yong-liang-ge-zhan-shi-xian-dui-lie-lcof/)

用两个栈实现一个队列。队列的声明如下，请实现它的两个函数 appendTail 和 deleteHead ，分别完成在队列尾部插入整数和在队列头部删除整数的功能。(若队列中没有元素，deleteHead 操作返回 -1 )

示例 1：
```
输入：
["CQueue","appendTail","deleteHead","deleteHead"]
[[],[3],[],[]]
输出：[null,null,3,-1]
```
示例 2：
```
输入：
["CQueue","deleteHead","appendTail","appendTail","deleteHead","deleteHead"]
[[],[],[5],[2],[],[]]
输出：[null,-1,null,null,5,2]
```
2021.04.11

---
这题读懂了题意以后会好做很多，输入的第一组的每一个元素对应第二组的每一格元素，可以看到append的时候不需要输出，delete的时候如果存在元素则返回该元素，否则返回-1。

用两个栈模拟队列，栈的特点是先进后出，队列的特点是先进先出，这个时候一定要画图，画了图以后通过观察和设计，需要掌握一个关键点：两个栈，一个栈A用于添加操作，另一个栈B用于删除操作。

举例，当需要删除的时候，首先看栈B是否为空，如果为空，那就把栈A中添加的`[abc`添加到栈B成为`abc]`，此时再弹出栈B最顶上的元素就可以实现a的先进先出。
之后对于bc的取，只要关注当栈B不为空直接返回栈B最顶上元素即可。
特殊情况，如果在弹出a后又加入了d，但此时d并不需要被先推出，因此，仍要等到栈B为空了再由栈A将d送入栈B中再推出。
```Java
class CQueue {
    LinkedList<Integer> addStack, deleteStack;
    public CQueue() {
        addStack =  new LinkedList<>();
        deleteStack = new LinkedList<>();
    }
    
    public void appendTail(int value) {
        addStack.push(value);
    }
    
    public int deleteHead() {
        if (deleteStack.size() != 0) {
            return deleteStack.pop();
        }
        else if (addStack.size() != 0){
            while (addStack.size() != 0){
                deleteStack.push(addStack.pop());
            }
            return deleteStack.pop();
        }
        else return -1;
    }
}

/**
 * Your CQueue object will be instantiated and called as such:
 * CQueue obj = new CQueue();
 * obj.appendTail(value);
 * int param_2 = obj.deleteHead();
 */
```

## 10. I. 斐波那契数列（easy）
[I. 斐波那契数列](https://leetcode-cn.com/problems/fei-bo-na-qi-shu-lie-lcof/)

写一个函数，输入 n ，求斐波那契（Fibonacci）数列的第 n 项（即 F(N)）。斐波那契数列的定义如下：

F(0) = 0,   F(1) = 1
F(N) = F(N - 1) + F(N - 2), 其中 N > 1.
斐波那契数列由 0 和 1 开始，之后的斐波那契数就是由之前的两数相加而得出。

答案需要取模 1e9+7（1000000007），如计算初始结果为：1000000008，请返回 1。

示例 1：
```
输入：n = 2
输出：1
```
示例 2：
```
输入：n = 5
输出：5
```
2021.04.11

---
基础的斐波那契数列，可以用递归，也可以用动态规划一维数组。
另外看到一个有意思的解法，不用数组 只用ab两个数代表所求数字的前两位，另外再来个sum数算作它俩的和，然后不停往下挪舍去a中的数字，a变成原来的b，b变成原来的sum，继续求接下去的sum。
```Java
class Solution {
    public int fib(int n) {
        if (n > 0){
            int[] dic = new int[n + 1];
            dic[0] = 0; dic[1] = 1;
            for (int i = 2; i < dic.length; i++){
                dic[i] = dic[i - 1] + dic[i - 2];
                if (dic[i] > 1000000007) dic[i] %= 1000000007;
            }
            return dic[n];
        }
        return 0;
    }
}
```
## 10. II. 青蛙跳台阶问题（easy）
[II. 青蛙跳台阶问题](https://leetcode-cn.com/problems/qing-wa-tiao-tai-jie-wen-ti-lcof/)

一只青蛙一次可以跳上1级台阶，也可以跳上2级台阶。求该青蛙跳上一个 n 级的台阶总共有多少种跳法。

答案需要取模 1e9+7（1000000007），如计算初始结果为：1000000008，请返回 1。

示例 1：
```
输入：n = 2
输出：2
```
示例 2：
```
输入：n = 7
输出：21
```
示例 3：
```
输入：n = 0
输出：1
```
2021.04.11

---
因为其实和上一题一模一样，就用了上面提到的比较有趣的解法。

剑指offer里还提到另一个变形题，如果一只青蛙可以跳1级2级3级4级...n级，请问跳n级台阶有几种可能。这个根据数学归纳法可得是2^(n-1)。

```Java
class Solution {
    public int numWays(int n) {
        int a = 0, b = 1, sum = 0;
        while (n-- >= 0){
            sum = (a + b)%1000000007;
            a = b;
            b = sum;
        }
        return a;
    }
}
```

## 11. 旋转数组的最小数字（easy）
[旋转数组的最小数字](https://leetcode-cn.com/problems/xuan-zhuan-shu-zu-de-zui-xiao-shu-zi-lcof/)

把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。输入一个递增排序的数组的一个旋转，输出旋转数组的最小元素。例如，数组 [3,4,5,1,2] 为 [1,2,3,4,5] 的一个旋转，该数组的最小值为1。 

示例 1：
```
输入：[3,4,5,1,2]
输出：1
```
示例 2：
```
输入：[2,2,2,0,1]
输出：0
```
2021.04.13

---
无语，一点也不简单，遇到这种轴点题需要判断轴点存在于左边还是右边。如果右边有序就说明在左边，如果右边无序就说明在右边，否则的话就一步步缩小右边界的范围就ok，直到右边界和左边界相等，返回左边界。
注意while停止的情况是左边界<=右边界，别忘啦

```Java
class Solution {
    public int minArray(int[] numbers) {
        int start = 0, end = numbers.length - 1;
        while (start <= end){
            int mid = (end - start) / 2 + start;
            if (numbers[mid] > numbers[end]) start = mid + 1;
            else if (numbers[mid] < numbers[end])end = mid;
            else end = end - 1;
        }
        return numbers[start];
    }
}
```

## 12. 矩阵中的路径（medium）
[矩阵中的路径](https://leetcode-cn.com/problems/ju-zhen-zhong-de-lu-jing-lcof/)

给定一个 m x n 二维字符网格 board 和一个字符串单词 word 。如果 word 存在于网格中，返回 true ；否则，返回 false 。

单词必须按照字母顺序，通过相邻的单元格内的字母构成，其中“相邻”单元格是那些水平相邻或垂直相邻的单元格。同一个单元格内的字母不允许被重复使用。

例如，在下面的 3×4 的矩阵中包含单词 "ABCCED"（单词中的字母已标出）。

示例 1：
```
输入：board = [
    ["A","B","C","E"],
    ["S","F","C","S"],
    ["A","D","E","E"]
], word = "ABCCED"
输出：true
```
示例 2：
```
输入：board = [
    ["a","b"],
    ["c","d"]
], word = "abcd"
输出：false
```
2021.04.14

---
这题以前做过，回溯剪枝
要把握好回溯算法的边界值、空值、基础错误值和回溯主体，最最重要别忘了回溯的时候把痕迹都还原

```Java
class Solution {
    public boolean exist(char[][] board, String word) {
        if (board.length > 0){
            boolean[][] visited = new boolean[board.length][board[0].length];
            for (int i = 0; i < board.length; i++){
                for (int j = 0; j < board[0].length; j++){
                    if (existRecursive(board, i, j, word, 0, visited)) return true;
                }
            }
        }
        return false;
    }
    private boolean existRecursive(char[][] board, int row, int col, String word, int index, boolean[][] visited){
        if (row < 0 || row >= board.length || col < 0 || col >= board[0].length) return false;
        if (visited[row][col] == true || board[row][col] != word.charAt(index))return false;
        if (index == word.length() - 1)return true;
        visited[row][col] = true;
        if (existRecursive(board, row - 1, col, word, index + 1, visited) == true) return true;
        if (existRecursive(board, row + 1, col, word, index + 1, visited) == true) return true;
        if (existRecursive(board, row, col + 1, word, index + 1, visited) == true) return true;
        if (existRecursive(board, row, col - 1, word, index + 1, visited) == true) return true;
        visited[row][col] = false;
        return false;
    }
}
```

## 13. 机器人的运动范围（medium）
[机器人的运动范围](https://leetcode-cn.com/problems/ji-qi-ren-de-yun-dong-fan-wei-lcof/)

地上有一个m行n列的方格，从坐标 [0,0] 到坐标 [m-1,n-1] 。一个机器人从坐标 [0, 0] 的格子开始移动，它每次可以向左、右、上、下移动一格（不能移动到方格外），也不能进入行坐标和列坐标的数位之和大于k的格子。例如，当k为18时，机器人能够进入方格 [35, 37] ，因为3+5+3+7=18。但它不能进入方格 [35, 38]，因为3+5+3+8=19。请问该机器人能够到达多少个格子？

示例 1：
```
输入：m = 2, n = 3, k = 1
输出：3
```
示例 2：
```
输入：m = 3, n = 1, k = 0
输出：1
```
2021.04.15

---
回溯剪枝

```Java
class Solution {
    public int movingCount(int m, int n, int k) {
        boolean[][] visited = new boolean[m][n];
        return dfs(m, n, k, 0, 0, visited);
    }
    private static boolean canBeCalculate(int a, int b, int k){
        int sum = 0;
        while (a > 0){
            sum += a % 10;
            a /= 10;
        }
        while (b > 0){
            sum += b % 10;
            b /= 10;
        }
        if (sum > k) return false;
        return true;
    }
    private static int dfs(int a, int b, int k, int col, int row, boolean[][] visited){
        if (row >= a || col >= b || visited[row][col] || !canBeCalculate(row, col, k)) return 0;
        visited[row][col] = true;
        int sum = 1 + dfs(a, b, k, col + 1, row, visited) + dfs(a, b, k, col, row + 1,visited);
        return sum;
    }
}
```

## 14. I. 剪绳子（medium）
[I. 剪绳子](https://leetcode-cn.com/problems/jian-sheng-zi-lcof/)

给你一根长度为 n 的绳子，请把绳子剪成整数长度的 m 段（m、n都是整数，n>1并且m>1），每段绳子的长度记为 k[0],k[1]...k[m-1] 。请问 k[0]*k[1]*...*k[m-1] 可能的最大乘积是多少？例如，当绳子的长度是8时，我们把它剪成长度分别为2、3、3的三段，此时得到的最大乘积是18。

示例 1：
```
输入: 2
输出: 1
解释: 2 = 1 + 1, 1 × 1 = 1
```
示例 2:
```
输入: 10
输出: 36
解释: 10 = 3 + 3 + 4, 3 × 3 × 4 = 36
```
2021.04.15

---
比较难。动态规划的重点就是当前的目标结果可以通过之前的最优结果推的，用这个思路去想问题。

```Java
class Solution {
    public int cuttingRope(int n) {
        int[] dp = new int[n + 1];
        dp[2] = 1;
        for (int i = 3; i <= n; i++){
            int maxValue = 0;
            for (int j = 2; j < i; j++){
                int value = Math.max(j * (i - j), j * dp[i - j]);
                if (value > maxValue) maxValue = value;
            }
            dp[i] = maxValue;
        }
        return dp[n];
    }
}
```

## 14. II. 剪绳子 II（medium）
[II. 剪绳子 II](https://leetcode-cn.com/problems/jian-sheng-zi-ii-lcof/)

给你一根长度为 n 的绳子，请把绳子剪成整数长度的 m 段（m、n都是整数，n>1并且m>1），每段绳子的长度记为 k[0],k[1]...k[m - 1] 。请问 k[0]*k[1]*...*k[m - 1] 可能的最大乘积是多少？例如，当绳子的长度是8时，我们把它剪成长度分别为2、3、3的三段，此时得到的最大乘积是18。

答案需要取模 1e9+7（1000000007），如计算初始结果为：1000000008，请返回 1。

示例 1：
```
输入: 2
输出: 1
解释: 2 = 1 + 1, 1 × 1 = 1
```
示例 2:
```
输入: 10
输出: 36
解释: 10 = 3 + 3 + 4, 3 × 3 × 4 = 36
```
2021.04.16

---
贪心算法，每次剪3的时候乘积最大。因为大数会溢出，每次乘剪下来的3的时候都会，因此每次都要取余数。
最后还剩下1，2，3，4；4的话最优解答案也是4，因此不必管，都乘以剩下的本身就ok。

```Java
class Solution {
    public int cuttingRope(int n) {
        if (n <= 3) return n - 1;
        long result = 1;
        while (n > 4){
            result = result * 3 % 1000000007;
            n -= 3;
        }
        return (int)(result * n % 1000000007);
    }
}
```

## 15. 二进制中1的个数（easy）
[二进制中1的个数](https://leetcode-cn.com/problems/er-jin-zhi-zhong-1de-ge-shu-lcof/)

请实现一个函数，输入一个整数（以二进制串形式），输出该数二进制表示中 1 的个数。例如，把 9 表示成二进制是 1001，有 2 位是 1。因此，如果输入 9，则该函数输出 2。

示例 1：
```
输入：00000000000000000000000000001011
输出：3
解释：输入的二进制串 00000000000000000000000000001011 中，共有三位为 '1'。
```
示例 2：
```
输入：00000000000000000000000010000000
输出：1
解释：输入的二进制串 00000000000000000000000010000000 中，共有一位为 '1'。
```
示例 3：
```
输入：11111111111111111111111111111101
输出：31
解释：输入的二进制串 11111111111111111111111111111101 中，共有 31 位为 '1'。
```
2021.04.16

---
位运算，主要要了解`&`这个符号，如果n的最后一位是1，那么n&1等于1，否则为0；
另外`>>>`这个符号能使二进制数右移一位，n >>>= 1就是n的二进制每次去掉最后1位。

```Java
public class Solution {
    // you need to treat n as an unsigned value
    public int hammingWeight(int n) {
        int result = 0;
        while (n != 0){
            if ((n & 1) == 1)result++;
            n >>>= 1;
        }
        return result;
    }
}
```

## 16. 数值的整数次方（medium）
[数值的整数次方](https://leetcode-cn.com/problems/shu-zhi-de-zheng-shu-ci-fang-lcof/)

实现 pow(x, n) ，即计算 x 的 n 次幂函数（即，xn）。不得使用库函数，同时不需要考虑大数问题。

示例 1：
```
输入：x = 2.00000, n = 10
输出：1024.00000
```
示例 2：
```
输入：x = 2.10000, n = 3
输出：9.26100
```
示例 3：
```
输入：x = 2.00000, n = -2
输出：0.25000
解释：2-2 = 1/22 = 1/4 = 0.25
```
2021.04.16

---
考虑两个输入的数字的不同的可能性，指数n为0的时候应该返回1.0，base数x为0的时候应该返回0。
另外还要考虑到指数n为负数的情况，这个时候就是1.0/result；
注意指数既然要取反的话，要注意用long，因为当n = -2147483648, n = −n, 会越界。

```Java
class Solution {
    public double myPow(double x, int n) {
        long nLong = (long)n;
        if (n == 0) return 1.0;
        if (x == 0) return 0.0;
        if (n < 0){
            return 1.0 / core(x, -nLong);
        }else return core(x, nLong);

    }
    private double core(double x, long n){
        if (n == 1) return x;
        if (n % 2 == 0) return core(x*x, n / 2);
        else return core(x*x, n/2) * x;
    }
}
```