## 二叉树遍历

- 前序遍历：先访问根节点，再前序遍历左子树，再前序遍历右子树
- 中序遍历：先中序遍历左子树，再访问根节点，再中序遍历右子树
- 后序遍历：先后序遍历左子树，再后序遍历右子树，再访问根节点

### 144. 二叉树的前序遍历

[题目链接](https://leetcode-cn.com/problems/binary-tree-preorder-traversal/)

递归

```java
class Solution {
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> list = new ArrayList<>();
        preOrder(root, list);
        return list;
    }

    public void preOrder(TreeNode root, List<Integer> list) {
        if (root == null) {
            return;
        }
        list.add(root.val);
        preOrder(root.left, list);
        preOrder(root.right, list);
    }
}
```

非递归

```java
class Solution {
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> list = new ArrayList<>();
        if (root == null) {
            return list;
        }
        Deque<TreeNode> stack = new LinkedList<>();
        stack.push(root);
        while (!stack.isEmpty()) {
            TreeNode node = stack.pop();
            list.add(node.val);
            if (node.right != null) {
                stack.push(node.right);
            }
            if (node.left != null) {
                stack.push(node.left);
            }
        }
        return list;
    }
}
```

### 94. 二叉树的中序遍历

[题目链接](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)

递归

```java
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> list = new ArrayList<>();
        inOrder(root, list);
        return list;
    }

    public void inOrder(TreeNode root, List<Integer> list) {
        if (root == null) {
            return;
        }
        inOrder(root.left, list);
        list.add(root.val);
        inOrder(root.right, list);
    }
}
```

非递归

```java
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> list = new ArrayList<>();
        if (root == null) {
            return list;
        }
        Deque<TreeNode> stack = new LinkedList<>();
        TreeNode node = root;
        while (!stack.isEmpty() || node != null) {
            if (node != null) {
                stack.push(node);
                node = node.left;
            } else {
                node = stack.pop();
                list.add(node.val);
                node = node.right;
            }
        }
        return list;
    }
}
```

### 145. 二叉树的后序遍历

[题目链接](https://leetcode-cn.com/problems/binary-tree-postorder-traversal/)

递归

```java
class Solution {
    public List<Integer> postorderTraversal(TreeNode root) {
        List<Integer> list = new ArrayList<>();
        postOrder(root, list);
        return list;
    }

    public void postOrder(TreeNode root, List<Integer> list) {
        if (root == null) {
            return;
        }
        postOrder(root.left, list);
        postOrder(root.right, list);
        list.add(root.val);
    }
}
```

非递归，和前序遍历很像，前序是 “中，左，右”，反一下左右，变成 “中，右，左” 再逆序就变成 “左，右，中”。（这里用 `LinkedList` 的 `addFirst` 实现逆序）。

```java
class Solution {
    public List<Integer> postorderTraversal(TreeNode root) {
        LinkedList<Integer> list = new LinkedList<>();
        if (root == null) {
            return list;
        }
        Deque<TreeNode> stack = new LinkedList<>();
        stack.push(root);
        while (!stack.isEmpty()) {
            TreeNode node = stack.pop();
            list.addFirst(node.val);
            if (node.left != null) {
                stack.push(node.left);
            }
            if (node.right != null) {
                stack.push(node.right);
            }
        }
        return list;
    }
}
```

另外一种迭代遍历的思路（类似中序那种）

```java
class Solution {
    public List<Integer> postorderTraversal(TreeNode root) {
        List<Integer> list = new ArrayList<>();
        if (root == null) {
            return list;
        }
        Deque<TreeNode> stack = new LinkedList<>();
        TreeNode node = root, prev = null;
        while (!stack.isEmpty() || node != null) {
            if (node != null) {
                stack.push(node);
                node = node.left;
            } else {
                node = stack.peek();
                if (node.right == null || node.right == prev) {
                    list.add(node.val);
                    stack.pop();
                    prev = node;
                    node = null;
                } else {
                    node = node.right;
                }
            }
        }
        return list;
    }
}
```

## 分治思想的应用

### 104. 二叉树的最大深度

> 给定一个二叉树，找出其最大深度。
> 二叉树的深度为根节点到最远叶子节点的最长路径上的节点数。

[题目链接](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)

```java
class Solution {
    public int maxDepth(TreeNode root) {
        return root == null ? 0
                : Math.max(maxDepth(root.left), maxDepth(root.right)) + 1;
    }
}
```

### 110. 平衡二叉树

> 给定一个二叉树，判断它是否是高度平衡的二叉树。
> 本题中，一棵高度平衡二叉树定义为：
> 一个二叉树每个节点 的左右两个子树的高度差的绝对值不超过 1 。

[题目链接](https://leetcode-cn.com/problems/balanced-binary-tree/)

自顶向下，时间复杂度 $O(n^2)$ 。

```java
class Solution {
    public boolean isBalanced(TreeNode root) {
        if (root == null) {
            return true;
        }
        return Math.abs(maxDepth(root.left) - maxDepth(root.right)) <= 1
                && isBalanced(root.left) && isBalanced(root.right);
    }

    public int maxDepth(TreeNode root) {
        return root == null ? 0
                : Math.max(maxDepth(root.left), maxDepth(root.right)) + 1;
    }
}
```

自底向上，时间复杂度 $O(n)$ 。

```java
class Solution {
    public boolean isBalanced(TreeNode root) {
        return maxDepth(root) >= 0;
    }

    public int maxDepth(TreeNode root) {
        if (root == null) {
            return 0;
        }
        int leftDepth = maxDepth(root.left);
        int rightDepth = maxDepth(root.right);
        if (leftDepth == -1 || rightDepth == -1 || Math.abs(leftDepth - rightDepth) > 1) {
            return -1;
        }
        return Math.max(leftDepth, rightDepth) + 1;
    }
}
```

### 124. 二叉树中的最大路径和

[题目链接](https://leetcode-cn.com/problems/binary-tree-maximum-path-sum/)

> 给定一个非空二叉树，返回其最大路径和。
> 本题中，路径被定义为一条从树中任意节点出发，沿父节点-子节点连接，达到任意节点的**序列**。该路径至少包含一个节点，且不一定经过根节点。

实现不难，难在题目意思有点难理解。**父节点可以即走左边又走右边，但是从父节点延伸下来的路径，不能走入左子树又掉头走右子树**。

```java
class Solution {
    private int sum = Integer.MIN_VALUE;

    public int maxPathSum(TreeNode root) {
        dfs(root);
        return sum;
    }

    public int dfs(TreeNode root) {
        if (root == null) {
            return 0;
        }
        int left = Math.max(dfs(root.left), 0);
        int right = Math.max(dfs(root.right), 0);
        sum = Math.max(sum, root.val + left + right);
        return root.val + Math.max(left, right);
    }
}
```

### 236. 二叉树的最近公共祖先

[题目链接](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)

> 给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。

经典的 LCA 问题，无奈水平有限，就会递归，什么 RMQ，倍增，树剖，Trajan 都一窍不通。

```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null || root == p || root == q) {
            return root;
        }
        TreeNode left = lowestCommonAncestor(root.left, p, q);
        TreeNode right = lowestCommonAncestor(root.right, p, q);
        if (left == null) {
            return right;
        }
        if (right == null) {
            return left;
        }
        return root;
    }
}
```

## BFS 广度优先搜索的应用

### 102. 二叉树的层序遍历

[题目链接](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)

> 给你一个二叉树，请你返回其按 **层序遍历** 得到的节点值。 （即逐层地，从左到右访问所有节点）。

```java
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> res = new ArrayList<>();
        if (root == null) {
            return res;
        }
        Queue<TreeNode> queue = new ArrayDeque<>();
        queue.offer(root);
        while (!queue.isEmpty()) {
            List<Integer> list = new ArrayList<>();
            int len = queue.size();
            for (int i = 0; i < len; i++) {
                TreeNode node = queue.poll();
                list.add(node.val);
                if (node.left != null) {
                    queue.offer(node.left);
                }
                if (node.right != null) {
                    queue.offer(node.right);
                }
            }
            res.add(list);
        }
        return res;
    }
}
```

### 107. 二叉树的层序遍历 II

[题目链接](https://leetcode-cn.com/problems/binary-tree-level-order-traversal-ii/)

> 给定一个二叉树，返回其节点值自底向上的层序遍历。 （即按从叶子节点所在层到根节点所在的层，逐层从左向右遍历）

和上一题几乎没区别，就是利用 `LinkedList` 的 `addFirst` 实现了逆序。

```java
class Solution {
    public List<List<Integer>> levelOrderBottom(TreeNode root) {
        LinkedList<List<Integer>> res = new LinkedList<>();
        if (root == null) {
            return res;
        }
        Queue<TreeNode> queue = new ArrayDeque<>();
        queue.offer(root);
        while (!queue.isEmpty()) {
            List<Integer> list = new ArrayList<>();
            int len = queue.size();
            for (int i = 0; i < len; i++) {
                TreeNode node = queue.poll();
                list.add(node.val);
                if (node.left != null) {
                    queue.offer(node.left);
                }
                if (node.right != null) {
                    queue.offer(node.right);
                }
            }
            res.addFirst(list);
        }
        return res;
    }
}
```

### 103. 二叉树的锯齿形层序遍历

[题目链接](https://leetcode-cn.com/problems/binary-tree-zigzag-level-order-traversal/)

> 给定一个二叉树，返回其节点值的锯齿形层序遍历。（即先从左往右，再从右往左进行下一层遍历，以此类推，层与层之间交替进行）。

用一个变量 `rev` 表示当前是否需要逆序，最后同样用 `LinkedList` 实现逆序。

```java
class Solution {
    public List<List<Integer>> zigzagLevelOrder(TreeNode root) {
        List<List<Integer>> res = new ArrayList<>();
        if (root == null) {
            return res;
        }
        Queue<TreeNode> queue = new ArrayDeque<>();
        queue.offer(root);
        boolean rev = false;
        while (!queue.isEmpty()) {
            LinkedList<Integer> list = new LinkedList<>();
            int len = queue.size();
            for (int i = 0; i < len; i++) {
                TreeNode node = queue.poll();
                if (rev) {
                    list.addFirst(node.val);
                } else {
                    list.addLast(node.val);
                }
                if (node.left != null) {
                    queue.offer(node.left);
                }
                if (node.right != null) {
                    queue.offer(node.right);
                }
            }
            res.add(list);
            rev = !rev;
        }
        return res;
    }
}
```

## 二叉搜索树的应用

### 98. 验证二叉搜索树

[题目链接](https://leetcode-cn.com/problems/validate-binary-search-tree/)

> 给定一个二叉树，判断其是否是一个有效的二叉搜索树。

首先很容易想到，二叉搜索树的中序遍历是顺序的，可以按照这个特性来验证。

```java
class Solution {
    public boolean isValidBST(TreeNode root) {
        if (root == null) {
            return true;
        }
        Deque<TreeNode> stack = new LinkedList<>();
        TreeNode node = root;
        Integer prev = null;
        while (!stack.isEmpty() || node != null) {
            if (node != null) {
                stack.push(node);
                node = node.left;
            } else {
                node = stack.pop();
                if (prev != null && node.val <= prev) {
                    return false;
                }
                prev = node.val;
                node = node.right;
            }
        }
        return true;
    }
}
```

另外还可以用**分治**的思想，一个二叉搜索树，它的左子树和右子树也一定是二叉搜索树。

```java
class Solution {
    public boolean isValidBST(TreeNode root) {
        return helper(root, null, null);
    }

    public boolean helper(TreeNode root, Integer min, Integer max) {
        if (root == null) {
            return true;
        }
        if (min != null && root.val <= min || max != null && root.val >= max) {
            return false;
        }
        return helper(root.left, min, root.val) && helper(root.right, root.val, max);
    }
}
```

### 701. 二叉搜索树中的插入操作

[题目链接](https://leetcode-cn.com/problems/insert-into-a-binary-search-tree/)

> 给定二叉搜索树（BST）的根节点和要插入树中的值，将值插入二叉搜索树。 返回插入后二叉搜索树的根节点。 输入数据 保证 ，新值和原始二叉搜索树中的任意节点值都不同。

直接按照二叉搜索树性质进行模拟

```java
class Solution {
    public TreeNode insertIntoBST(TreeNode root, int val) {
        TreeNode newNode = new TreeNode(val);
        if (root == null) {
            return newNode;
        }
        TreeNode cur = root;
        while (cur != null) {
            if (val < cur.val) {
                if (cur.left == null) {
                    cur.left = newNode;
                    break;
                } else {
                    cur = cur.left;
                }
            } else {
                if (cur.right == null) {
                    cur.right = newNode;
                    break;
                } else {
                    cur = cur.right;
                }
            }
        }
        return root;
    }
}
```

另外，附上递归解法

```java
class Solution {
    public TreeNode insertIntoBST(TreeNode root, int val) {
        if (root == null) {
            return new TreeNode(val);
        }
        if (val < root.val) {
            root.left = insertIntoBST(root.left, val);
        } else {
            root.right = insertIntoBST(root.right, val);
        }
        return root;
    }
}
```

## 总结

重要知识点：

- 前、中、后序遍历的递归和非递归
- DFS、分治
- BFS
