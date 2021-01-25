## 简介

栈的特点是后入先出，根据这个特点可以临时保存一些数据，之后用到依次再弹出来，常用于 DFS 深度搜索。队列一般常用于 BFS 广度搜索，类似一层一层的搜索。

## Stack & DFS

### 155. 最小栈

[题目链接](https://leetcode-cn.com/problems/min-stack/)

> 设计一个支持 `push` ，`pop` ，`top` 操作，并能在常数时间内检索到最小元素的栈。

利用辅助栈，存放最小值。

```java
class MinStack {

    private Deque<Integer> stack = new ArrayDeque<>();
    private Deque<Integer> minStack = new ArrayDeque<>();

    public MinStack() {
    }

    public void push(int x) {
        int min = getMin();
        minStack.push(x < min ? x : min);
        stack.push(x);
    }

    public void pop() {
        minStack.pop();
        stack.pop();
    }

    public int top() {
        return stack.peek();
    }

    public int getMin() {
        if (minStack.isEmpty()) {
            return Integer.MAX_VALUE;
        }
        return minStack.peek();
    }
}
```

### 150. 逆波兰表达式求值

[题目链接](https://leetcode-cn.com/problems/evaluate-reverse-polish-notation/)

> 根据 [逆波兰表示法](https://baike.baidu.com/item/%E9%80%86%E6%B3%A2%E5%85%B0%E5%BC%8F/128437)，求表达式的值。

先是用栈的解法提交了一次，发现**慢的离谱**，因为严格的栈不能访问栈顶之下的元素，所以这题用数组模拟栈会快得多。

- 数组解法

```java
class Solution {
    public int evalRPN(String[] tokens) {
        int[] a = new int[tokens.length / 2 + 1];
        int len = 0;
        for (String t : tokens) {
            if ("+".equals(t)) {
                a[len - 2] += a[--len];
            } else if ("-".equals(t)) {
                a[len - 2] -= a[--len];
            } else if ("*".equals(t)) {
                a[len - 2] *= a[--len];
            } else if ("/".equals(t)) {
                a[len - 2] /= a[--len];
            } else {
                a[len++] = Integer.parseInt(t);
            }
        }
        return a[0];
    }
}
```

- 栈解法

```java
class Solution {
    public int evalRPN(String[] tokens) {
        Deque<Integer> stack = new ArrayDeque<>();
        for (String token : tokens) {
            try {
                int num = Integer.parseInt(token);
                stack.push(num);
            } catch (NumberFormatException e) {
                int b = stack.pop();
                int a = stack.pop();
                if ("+".equals(token)) {
                    stack.push(a + b);
                } else if ("-".equals(token)) {
                    stack.push(a - b);
                } else if ("*".equals(token)) {
                    stack.push(a * b);
                } else {
                    stack.push(a / b);
                }
            }
        }
        return stack.peek();
    }
}
```

### 394. 字符串解码

[题目链接](https://leetcode-cn.com/problems/decode-string/)

> 给定一个经过编码的字符串，返回它解码后的字符串。
> 编码规则为: `k[encoded_string]`，表示其中方括号内部的 `encoded_string` 正好重复 `k` 次。注意 `k` 保证为正整数。
> 你可以认为输入字符串总是有效的；输入字符串中没有额外的空格，且输入的方括号总是符合格式要求的。
> 此外，你可以认为原始数据不包含数字，所有的数字只表示重复的次数 `k` ，例如不会出现像 `3a` 或 `2[4]` 的输入。

通过栈进行辅助操作。

```java
class Solution {

    private int index = 0;

    public String decodeString(String s) {
        LinkedList<String> stack = new LinkedList<>();
        while (index < s.length()) {
            char c = s.charAt(index);
            if (Character.isDigit(c)) {
                stack.push(getNum(s));
            } else if (Character.isLetter(c) || c == '[') {
                ++index;
                stack.push(String.valueOf(c));
            } else {
                ++index;
                LinkedList<String> strs = new LinkedList<>();
                while (!"[".equals(stack.peek())) {
                    strs.addFirst(stack.pop());
                }
                stack.pop();
                String sub = getStr(strs);
                int count = Integer.parseInt(stack.pop());
                StringBuilder sb = new StringBuilder();
                for (int i = 0; i < count; i++) {
                    sb.append(sub);
                }
                stack.push(sb.toString());
            }
        }
        Collections.reverse(stack);
        return getStr(stack);
    }

    public String getNum(String s) {
        StringBuilder sb = new StringBuilder();
        while (Character.isDigit(s.charAt(index))) {
            sb.append(s.charAt(index++));
        }
        return sb.toString();
    }

    public String getStr(List<String> strs) {
        StringBuilder sb = new StringBuilder();
        for (String str : strs) {
            sb.append(str);
        }
        return sb.toString();
    }
}
```

另外，再附上递归的解法。（参考排行榜上 0ms 的代码写的，这思路太强了）

递归最重要的技巧就是，**别死磕细节**。从大体上看，这题只有可能出两种字符串：`123[......]` 或者 `abc`，直接用递归分别处理就好。

```java
class Solution {

    private String s;
    private int i = 0;

    public String decodeString(String s) {
        this.s = s;
        return helper();
    }

    public String helper() {
        StringBuilder sb = new StringBuilder();
        while (i < s.length() && s.charAt(i) != ']') {
            char c = s.charAt(i);
            if (Character.isDigit(c)) {
                int cnt = c - '0';
                while (s.charAt(++i) != '[') {
                    cnt = cnt * 10 + s.charAt(i) - '0';
                }
                ++i;  // skip [
                String sub = helper();
                for (int j = 0; j < cnt; j++) {
                    sb.append(sub);
                }
                ++i;  // skip ]
            } else {
                sb.append(c);
                ++i;
            }
        }
        return sb.toString();
    }
}
```

### 94. 二叉树的中序遍历

[题目链接](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)

> 给定一个二叉树的根节点  `root` ，返回它的  **中序**  遍历。

为什么又来一遍中序遍历呢，因为太经典了。

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

### 133. 克隆图

[题目链接](https://leetcode-cn.com/problems/clone-graph/)

> 给你无向  **[连通](https://baike.baidu.com/item/%E8%BF%9E%E9%80%9A%E5%9B%BE/6460995?fr=aladdin) **图中一个节点的引用，请你返回该图的  [**深拷贝**](https://baike.baidu.com/item/%E6%B7%B1%E6%8B%B7%E8%B4%9D/22785317?fr=aladdin)（克隆）。

思路：为了避免死循环，用 map 记录已经克隆的节点，和对应的新节点。

```java
class Solution {

    private Map<Node, Node> map = new HashMap<>();

    public Node cloneGraph(Node node) {
        if (node == null) {
            return null;
        }
        if (map.containsKey(node)) {
            return map.get(node);
        }
        Node newNode = new Node(node.val, new ArrayList<>());
        map.put(node, newNode);
        for (Node neighbor : node.neighbors) {
            newNode.neighbors.add(cloneGraph(neighbor));
        }
        return newNode;
    }
}
```

### 200. 岛屿数量

[题目链接](https://leetcode-cn.com/problems/number-of-islands/)

> 给你一个由 `'1'`（陆地）和 `'0'`（水）组成的的二维网格，请你计算网格中岛屿的数量。
> 岛屿总是被水包围，并且每座岛屿只能由水平方向和/或竖直方向上相邻的陆地连接形成。
> 此外，你可以假设该网格的四条边均被水包围。

思路：遇到岛屿就把它的所有土地“销毁”（标为 `'0'`），保证不会重复遇到同一个岛屿。

```java
class Solution {

    public int numIslands(char[][] grid) {
        int count = 0;
        for (int i = 0; i < grid.length; ++i) {
            for (int j = 0; j < grid[0].length; ++j) {
                if (grid[i][j] == '1') {
                    dfs(grid, i, j);
                    ++count;
                }
            }
        }
        return count;
    }

    private void dfs(char[][] grid, int i, int j) {
        if (i < 0 || j < 0 || i >= grid.length
                || j >= grid[0].length || grid[i][j] == '0') {
            return;
        }
        grid[i][j] = '0';
        dfs(grid, i - 1, j);
        dfs(grid, i + 1, j);
        dfs(grid, i, j - 1);
        dfs(grid, i, j + 1);
    }
}
```

### 84. 柱状图中的最大矩形

[题目链接](https://leetcode-cn.com/problems/largest-rectangle-in-histogram/)

> 给定 _n_ 个非负整数，用来表示柱状图中各个柱子的高度。每个柱子彼此相邻，且宽度为 1 。
> 求在该柱状图中，能够勾勒出来的矩形的最大面积。

思路： 通过单调栈找出各个柱体的左右边界。

```java
class Solution {
    public int largestRectangleArea(int[] heights) {
        int[] temp = new int[heights.length + 2];
        // [0, ..., 0] 前后补0，以简化操作
        System.arraycopy(heights, 0, temp, 1, heights.length);
        Deque<Integer> stack = new ArrayDeque<>();
        int area = 0;
        for (int i = 0; i < temp.length; ++i) {
            while (!stack.isEmpty() && temp[i] < temp[stack.peek()]) {
                int h = temp[stack.pop()];
                // 栈顶和i，分别是上一个出栈元素的左边界和右边界
                area = Math.max(area, (i - stack.peek() - 1) * h);
            }
            stack.push(i);
        }
        return area;
    }
}
```

## Queue & BFS

### 232. 用栈实现队列

[题目链接](https://leetcode-cn.com/problems/implement-queue-using-stacks/)

> 请你仅使用两个栈实现先入先出队列。队列应当支持一般队列的支持的所有操作（push、pop、peek、empty）

思路：用两个栈，第二个栈辅助获取队头。

```java
class MyQueue {

    private final Deque<Integer> s1;
    private final Deque<Integer> s2;

    /** Initialize your data structure here. */
    public MyQueue() {
        s1 = new ArrayDeque<>();
        s2 = new ArrayDeque<>();
    }

    /** Push element x to the back of queue. */
    public void push(int x) {
        s1.push(x);
    }

    /** Removes the element from in front of queue and returns that element. */
    public int pop() {
        if (s2.isEmpty()) {
            while (!s1.isEmpty()) {
                s2.push(s1.pop());
            }
        }
        return s2.pop();
    }

    /** Get the front element. */
    public int peek() {
        if (s2.isEmpty()) {
            while (!s1.isEmpty()) {
                s2.push(s1.pop());
            }
        }
        return s2.peek();
    }

    /** Returns whether the queue is empty. */
    public boolean empty() {
        return s1.isEmpty() && s2.isEmpty();
    }
}

/**
 * Your MyQueue object will be instantiated and called as such:
 * MyQueue obj = new MyQueue();
 * obj.push(x);
 * int param_2 = obj.pop();
 * int param_3 = obj.peek();
 * boolean param_4 = obj.empty();
 */
```

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

### 542. 01 矩阵

[题目链接](https://leetcode-cn.com/problems/01-matrix/)

> 给定一个由 0 和 1 组成的矩阵，找出每个元素到最近的 0 的距离。
> 两个相邻元素间的距离为 1 。

思路：虽然题目描述的是每个元素到最近的 0 的距离。但是反过来思考，把每个 0 都当作起点，题目就变成了，起点到达每个点的最近距离，实现起来要方便的多。

```java
class Solution {

    static int[][] dir = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};

    public int[][] updateMatrix(int[][] matrix) {
        Queue<int []> queue = new ArrayDeque<>();
        int n = matrix.length, m = matrix[0].length;
        boolean[][] vis = new boolean[n][m];
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < m; ++j) {
                if (matrix[i][j] == 0) {
                    queue.offer(new int[]{i, j});
                    vis[i][j] = true;
                }
            }
        }
        while (!queue.isEmpty()) {
            int[] p = queue.poll();
            int i = p[0], j = p[1];
            for (int k = 0; k < 4; ++k) {
                int ni = i + dir[k][0], nj = j + dir[k][1];
                if (ni >= 0 && nj >= 0 && ni < n && nj < m && !vis[ni][nj]) {
                    matrix[ni][nj] = matrix[i][j] + 1;
                    queue.offer(new int[]{ni, nj});
                    vis[ni][nj] = true;
                }
            }
        }
        return matrix;
    }
}
```

## 总结

- 熟悉栈的使用场景

  - 后入先出，保存临时值

  - 利用栈 DFS 深度搜索

- 熟悉队列的使用场景
  - 利用队列 BFS 广度搜索
