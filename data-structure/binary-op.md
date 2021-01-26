## 常见二进制操作

### 与

都为 1 才为 1

```
0 & 0 = 0
0 & 1 = 1 & 0 = 0
1 & 1 = 1
```

### 或

都为 0 才为 0

```
0 | 0 = 0
0 | 1 = 1 | 0 = 1 | 1 = 1
```

### 非

相反

```
~0 = 1
~1 = 0
```

### 异或

不同才为 1

```
0 ^ 0 = 1 ^ 1 = 0
0 ^ 1 = 1 ^ 0 = 1
```

### 常用公式

异或同一个数两次，值不变

```
a ^ b ^ b = a
```

交换两个数

```
a = a ^ b
b = a ^ b
a = a ^ b
```

移除最后一个 1

```
a & (a - 1)
```

获得最后一个 1

```
a & (-a)
```

## 常见题目

### 136. 只出现一次的数字

[题目链接](https://leetcode-cn.com/problems/single-number/)

> 给定一个**非空**整数数组，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。

套公式 `a ^ b ^ b = a` 。

```java
class Solution {
    public int singleNumber(int[] nums) {
        int ans = 0;
        for (int num : nums) {
            ans ^= num;
        }
        return ans;
    }
}
```

### 137. 只出现一次的数字 II

[题目链接](https://leetcode-cn.com/problems/single-number-ii/)

> 给定一个**非空**整数数组，除了某个元素只出现一次以外，其余每个元素均出现了三次。找出那个只出现了一次的元素。

官方题解的思路给我看傻了，我承认没看懂哈哈 :joy: ，这里用了用最笨、最简单的方法统计每一位上 1 的个数。

```java
class Solution {
    public int singleNumber(int[] nums) {
        int ans = 0;
        for (int i = 0; i < 32; ++i) {
            int sum = 0;
            for (int num : nums) {
                sum += (num >> i) & 1;
            }
            ans ^= (sum % 3) << i;
        }
        return ans;
    }
}
```

### 260. 只出现一次的数字 III

[题目链接](https://leetcode-cn.com/problems/single-number-iii/)

> 给定一个整数数组 nums，其中恰好有两个元素只出现一次，其余所有元素均出现两次。 找出只出现一次的那两个元素。

如果按照第一题的思路，会发现异或一轮之后，结果为需要的两个答案异或（`mask = ans1 ^ ans2`），剩下的问题就是怎么把它们拆开。

思考两个数异或运算的方法，只有不同的位才为 1，也就是我们可以在 `mask` 中随便找一个数值为 1 的位（肯定是找最后一个 1 简单）。按照该位为 1 或是为 0，把数组拆分成两部分，`ans1` 和 `ans2` 必定在不同的部分。

最后，问题就变回了第一题的模型。

```java
class Solution {
    public int[] singleNumber(int[] nums) {
        int mask = 0;
        for (int num : nums) {
            mask ^= num;
        }
        mask &= -mask;
        int[] ans = new int[2];
        for (int num : nums) {
            if ((num & mask) == 0) {
                ans[0] ^= num;
            } else {
                ans[1] ^= num;
            }
        }
        return ans;
    }
}
```

### 191. 位 1 的个数

[题目链接](https://leetcode-cn.com/problems/number-of-1-bits/)

> 编写一个函数，输入是一个无符号整数（以二进制串的形式），返回其二进制表达式中数字位数为 '1' 的个数（也被称为[汉明重量](https://baike.baidu.com/item/%E6%B1%89%E6%98%8E%E9%87%8D%E9%87%8F)）。

套公式，移除最后一个 1，`n &= (n - 1)` 。

```java
public class Solution {
    // you need to treat n as an unsigned value
    public int hammingWeight(int n) {
        int count = 0;
        while (n != 0) {
            n &= (n - 1);
            ++count;
        }
        return count;
    }
}
```

### 338. 比特位计数

[题目链接](https://leetcode-cn.com/problems/counting-bits/)

> 给定一个非负整数 **num**。对于 **0 ≤ i ≤ num** 范围中的每个数字 i ，计算其二进制数中的 1 的数目并将它们作为数组返回。

比较简单的思路就是直接用上题的代码，一个个计算 1 的数目。

```java
class Solution {
    public int[] countBits(int num) {
        int[] ans = new int[num + 1];
        for (int i = 1; i <= num; ++i) {
            ans[i] = hammingWeight(i);
        }
        return ans;
    }

    public int hammingWeight(int n) {
        int count = 0;
        while (n != 0) {
            n &= (n - 1);
            ++count;
        }
        return count;
    }
}
```

动态规划（推荐）

不难推出状态转移方程：`dp[当前] = dp[移除最后一个 1] + 1` 。

```java
class Solution {
    public int[] countBits(int num) {
        int[] dp = new int[num + 1];
        for (int i = 1; i <= num; ++i) {
            dp[i] = dp[i & (i - 1)] + 1;
        }
        return dp;
    }
}
```

### 190. 颠倒二进制位

[题目链接](https://leetcode-cn.com/problems/reverse-bits/)

> 颠倒给定的 32 位无符号整数的二进制位。

方法一，简单模拟。

```java
public class Solution {
    // you need treat n as an unsigned value
    public int reverseBits(int n) {
        int ans = 0;
        for (int i = 0; i < 32; ++i) {
            ans <<= 1;
            ans |= n & 1;
            n >>= 1;
        }
        return ans;
    }
}
```

方法二，类似 java 中 `Integer.reverse` 的实现，分支思想，先反转左右 16 位，再分别反转两个 16 位中的 8 位，以此类推，直到 1 位。（“反转” 其实就是通过掩码和位移来实现）

```java
public class Solution {
    // you need treat n as an unsigned value
    public int reverseBits(int n) {
        n = (n << 16) | (n >>> 16);
        n = (n & 0x00ff00ff) << 8 | (n >>> 8) & 0x00ff00ff;
        n = (n & 0x0f0f0f0f) << 4 | (n >>> 4) & 0x0f0f0f0f;
        n = (n & 0x33333333) << 2 | (n >>> 2) & 0x33333333;
        n = (n & 0x55555555) << 1 | (n >>> 1) & 0x55555555;
        return n;
    }
}
```

### 201. 数字范围按位与

[题目链接](https://leetcode-cn.com/problems/bitwise-and-of-numbers-range/)

> 给定范围 `[m, n]`，其中 `0 <= m <= n <= 2147483647`，返回此范围内所有数字的按位与（包含 m, n 两端点）。

毫无疑问，如果直接从 m 一直与到 n 是会超时的，这题的本质其实是求最长公共前缀。

方法一

```java
class Solution {
    public int rangeBitwiseAnd(int m, int n) {
        int i = 0;
        while (m != n) {
            m >>= 1;
            n >>= 1;
            ++i;
        }
        return m << i;
    }
}
```

方法二

```java
class Solution {
    public int rangeBitwiseAnd(int m, int n) {
        while (m < n) {
            n &= (n - 1);
        }
        return n;
    }
}
```
