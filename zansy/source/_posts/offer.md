title: 剑指offer刷题记录（20210410 更新/14）

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

