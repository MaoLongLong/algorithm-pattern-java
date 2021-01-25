## List

大概可以分为两类，用数组实现的 `ArrayList` 和用链表实现的 `LinkedList` 。

```java
List<Integer> list = new ArrayList<>();
List<Integer> list = new LinkedList<>();
```

## Deque

双端队列作为一个**多功能**的集合，能够用它实现栈或队列。下面列举出两组方法，它们在操作失败时会产生不同结果。

<!-- tabs:start -->

### **返回特殊值（null）**

|          | 第一个元素（头部） | 最后一个元素（尾部） |
| :------: | :----------------: | :------------------: |
| **插入** |     offerFirst     |      offerLast       |
| **删除** |     pollFirst      |       pollLast       |
| **查询** |     peekFirst      |       peekLast       |

### **抛出异常**

|          | 第一个元素（头部） | 最后一个元素（尾部） |
| :------: | :----------------: | :------------------: |
| **插入** |      addFirst      |       addLast        |
| **删除** |    removeFirst     |      removeLast      |
| **查询** |      getFirst      |       getLast        |

<!-- tabs:end -->

## Stack

!> 关于 `java.util.Stack`，从源码的注释可以看出，官方已经不推荐使用，可以用 `java.util.Deque` 的实现类代替。

```java
/**
 * The <code>Stack</code> class represents a last-in-first-out
 * (LIFO) stack of objects. It extends class <tt>Vector</tt> with five
 * operations that allow a vector to be treated as a stack. The usual
 * <tt>push</tt> and <tt>pop</tt> operations are provided, as well as a
 * method to <tt>peek</tt> at the top item on the stack, a method to test
 * for whether the stack is <tt>empty</tt>, and a method to <tt>search</tt>
 * the stack for an item and discover how far it is from the top.
 * <p>
 * When a stack is first created, it contains no items.
 *
 * <p>A more complete and consistent set of LIFO stack operations is
 * provided by the {@link Deque} interface and its implementations, which
 * should be used in preference to this class.  For example:
 *
<pre>   {@code
 *   Deque<Integer> stack = new ArrayDeque<Integer>();}</pre>
*
 * @author  Jonathan Payne
 * @since   JDK1.0
 */
public
class Stack<E> extends Vector<E> {
```

用 `LinkedList` 实现栈（因为它实现了 `Deque` 接口），依然是熟悉的 3 种操作：

| Stack 方法 | 等效的 Deque 方法 |
| :--------: | :---------------: |
|    push    |     addFirst      |
|    pop     |    removeFirst    |
|    peek    |     peekFirst     |

代码演示：

```java
int[] nums = new int[]{1, 2, 3};
Deque<Integer> stack = new LinkedList<>();
for (int num : nums) {
    stack.push(num);
}
System.out.println(stack.peek());
while (!stack.isEmpty()) {
    System.out.println(stack.pop());
}
```

?> 用 `ArrayDeque` 实现栈也同理

## Queue

| Queue 方法 | 等效的 Deque 方法 |
| :--------: | :---------------: |
|    add     |      addLast      |
|   offer    |     offerLast     |
|   remove   |    removeFirst    |
|    poll    |     pollFirst     |
|  element   |     getFirst      |
|    peek    |     peekFirst     |

代码演示：

```java
int[] nums = new int[]{1, 2, 3};
Queue<Integer> queue = new LinkedList<>();
for (int num : nums) {
    queue.offer(num);
}
System.out.println(queue.peek());
while (!queue.isEmpty()) {
    System.out.println(queue.poll());
}
```

## Map
