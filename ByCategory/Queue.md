# Queue

### 622. Design Circular Queue - Medium

Design your implementation of the circular queue. The circular queue is a linear data structure in which the operations are performed based on FIFO (First In First Out) principle and the last position is connected back to the first position to make a circle. It is also called "Ring Buffer".

One of the benefits of the circular queue is that we can make use of the spaces in front of the queue. In a normal queue, once the queue becomes full, we cannot insert the next element even if there is a space in front of the queue. But using the circular queue, we can use the space to store new values.

Your implementation should support following operations:

- `MyCircularQueue(k)`: Constructor, set the size of the queue to be k.
- `Front`: Get the front item from the queue. If the queue is empty, return -1.
- `Rear`: Get the last item from the queue. If the queue is empty, return -1.
- `enQueue(value)`: Insert an element into the circular queue. Return true if the operation is successful.
- `deQueue()`: Delete an element from the circular queue. Return true if the operation is successful.
- `isEmpty()`: Checks whether the circular queue is empty or not.
- `isFull()`: Checks whether the circular queue is full or not.

 

**Example:**

```
MyCircularQueue circularQueue = new MyCircularQueue(3); // set the size to be 3
circularQueue.enQueue(1);  // return true
circularQueue.enQueue(2);  // return true
circularQueue.enQueue(3);  // return true
circularQueue.enQueue(4);  // return false, the queue is full
circularQueue.Rear();  // return 3
circularQueue.isFull();  // return true
circularQueue.deQueue();  // return true
circularQueue.enQueue(4);  // return true
circularQueue.Rear();  // return 4
```

 

**Note:**

- All values will be in the range of [0, 1000].
- The number of operations will be in the range of [1, 1000].
- Please do not use the built-in Queue library.



#### Implementation - Array

```java
class MyCircularQueue {
    int front;
    int rear;
    int[] elements;
    /** Initialize your data structure here. Set the size of the queue to be k. */
    public MyCircularQueue(int k) {
        elements = new int[k];
        front = 0;
        rear = 0;
    }
    
    /** Insert an element into the circular queue. Return true if the operation is successful. */
    public boolean enQueue(int value) {
        if (isFull()) return false;
        int index = front % elements.length;
        elements[index] = value;
        front++;
        return true;
    }
    
    /** Delete an element from the circular queue. Return true if the operation is successful. */
    public boolean deQueue() {
        if (isEmpty()) return false;
        elements[rear++] = -1;
        return true;
    }
    
    /** Get the front item from the queue. */
    public int Front() {
        if (isEmpty()) return -1;
        return elements[rear];
    }
    
    /** Get the last item from the queue. */
    public int Rear() {
        if (isEmpty()) return -1;
        int index = (front-1) % elements.length;
        return elements[index];
    }
    
    /** Checks whether the circular queue is empty or not. */
    public boolean isEmpty() {
        return front == rear;
    }
    
    /** Checks whether the circular queue is full or not. */
    public boolean isFull() {
        return (front - rear) == elements.length;
    }
}
```

Time: O(1)

Space: O(N)

**Improvement: Thread-Safe**

One might be happy with the above implementation, after all it meets all the requirements of the problem.

> As a followup question though, an interviewer might ask one to point out a *potential defect* about the implementation and the solution.

This time, it is not about the space or time complexity, but **concurrency**. Our circular queue is NOT *thread-safe*. One could end up with *corrupting* our data structure in a multi-threaded environment.

For example, here is an execution sequence where we exceed the designed capacity of the queue and overwrite the tail element undesirably.

![pic](https://leetcode.com/problems/design-circular-queue/Figures/622/622_concurrency.png)

The above scenario is called race conditions as described in the problem of [Print in Order](https://leetcode.com/problems/print-in-order/solution/). One can find more [concurrency problems](https://leetcode.com/problemset/concurrency/) to practice on LeetCode.

There are several ways to mitigate the above concurrency problem. Take the function `enQueue(int value)` as an example, we show how we can make the function thread-safe in the following implementation:

<iframe src="https://leetcode.com/playground/BUYoD5Gc/shared" frameborder="0" width="100%" height="500" name="BUYoD5Gc" style="box-sizing: border-box; margin: 20px 0px; color: rgba(0, 0, 0, 0.65); font-family: -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, &quot;PingFang SC&quot;, &quot;Hiragino Sans GB&quot;, &quot;Microsoft YaHei&quot;, &quot;Helvetica Neue&quot;, Helvetica, Arial, sans-serif, &quot;Apple Color Emoji&quot;, &quot;Segoe UI Emoji&quot;, &quot;Segoe UI Symbol&quot;; font-size: 14px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; background-color: rgb(255, 255, 255); text-decoration-style: initial; text-decoration-color: initial;"></iframe>
With the protection of locks, we now feel more confident to apply our circular queue in critical scenarios.

The above improvement does not alter the time and space complexity of the original data structure, though overall the thread-safe measures do incur some extra costs.

#### Approach 2: Singly-Linked List

**Intuition**

Similar with Array, the *Linked List* is another common building block for more advanced data structures.

> Different than a fixed size Array, a linked list could be more memory efficient, since it does not pre-allocate memory for unused capacity.

With a singly-linked list, we could design a circular queue with the same time and space complexity as the approach with Array. **But we could gain some memory efficiency, since we don't need to pre-allocate the memory upfront.**

In the following graph, we show how the operations of `enQueue()` and `deQueue()` can be done via singly-linked list.

![pic](https://leetcode.com/problems/design-circular-queue/Figures/622/622_queue_linked_list.png)

**Algorithm**

Here we give a list of attributes in our circular queue data structure and the thoughts behind each attribute.

- `capacity`: the maximum number of elements that the circular queue will hold.
- `head`: the reference to the head element in the queue.
- `count`: the current length of the queue. This is a critical attribute that helps us to do the boundary check in each method.
- `tail`: the reference to the tail element in the queue. Unlike the Array approach, we need to explicitly keep the reference to the tail element. Without this attribute, it would take us O(*N*) time complexity to locate the tail element from the head element.

<iframe src="https://leetcode.com/playground/MDN9TRSH/shared" frameborder="0" width="100%" height="500" name="MDN9TRSH" style="box-sizing: border-box; margin: 20px 0px; color: rgba(0, 0, 0, 0.65); font-family: -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, &quot;PingFang SC&quot;, &quot;Hiragino Sans GB&quot;, &quot;Microsoft YaHei&quot;, &quot;Helvetica Neue&quot;, Helvetica, Arial, sans-serif, &quot;Apple Color Emoji&quot;, &quot;Segoe UI Emoji&quot;, &quot;Segoe UI Symbol&quot;; font-size: 14px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; background-color: rgb(255, 255, 255); text-decoration-style: initial; text-decoration-color: initial;"></iframe>
**Complexity**

- Time complexity: O(1) for each method in our circular queue.
- Space Complexity: The upper bound of the memory consumption for our circular queue would be O(*N*), same as the Array approach. However, it should be more memory efficient as we discussed in the intuition section.

### 232. Implement Queue using Stacks - Easy

Implement the following operations of a queue using stacks.

- push(x) -- Push element x to the back of queue.
- pop() -- Removes the element from in front of queue.
- peek() -- Get the front element.
- empty() -- Return whether the queue is empty.

**Example:**

```
MyQueue queue = new MyQueue();

queue.push(1);
queue.push(2);  
queue.peek();  // returns 1
queue.pop();   // returns 1
queue.empty(); // returns false
```

**Notes:**

- You must use *only* standard operations of a stack -- which means only `push to top`, `peek/pop from top`, `size`, and `is empty` operations are valid.
- Depending on your language, stack may not be supported natively. You may simulate a stack by using a list or deque (double-ended queue), as long as you use only standard operations of a stack.
- You may assume that all operations are valid (for example, no pop or peek operations will be called on an empty queue).

#### Approach #1 (Two Stacks) Push - O(n)*O*(*n*) per operation, Pop - O(1)*O*(1) per operation.

**Algorithm**

**Push**

A queue is FIFO (first-in-first-out) but a stack is LIFO (last-in-first-out). This means the newest element must be pushed to the bottom of the stack. To do so we first transfer all `s1` elements to auxiliary stack `s2`. Then the newly arrived element is pushed on top of `s2` and all its elements are popped and pushed to `s1`.

![Push an element in queue](https://leetcode.com/media/original_images/232_queue_using_stacksBPush.png)

*Figure 1. Push an element in queue*

**Java**

```java
private int front;

public void push(int x) {
    if (s1.empty())
        front = x;
    while (!s1.isEmpty())
        s2.push(s1.pop());
    s2.push(x);
    while (!s2.isEmpty())
        s1.push(s2.pop());
}
```

**Complexity Analysis**

- Time complexity : O(n)*O*(*n*).

Each element, with the exception of the newly arrived, is pushed and popped twice. The last inserted element is popped and pushed once. Therefore this gives 4 n + 24*n*+2 operations where n*n* is the queue size. The `push` and `pop` operations have O(1)*O*(1) time complexity.

- Space complexity : O(n)*O*(*n*). We need additional memory to store the queue elements

**Pop**

The algorithm pops an element from the stack `s1`, because `s1` stores always on its top the first inserted element in the queue. The front element of the queue is kept as `front`.

![Pop an element from queue](https://leetcode.com/media/original_images/232_queue_using_stacksBPop.png)

*Figure 2. Pop an element from queue*

**Java**

```java
// Removes the element from the front of queue.
public void pop() {
    s1.pop();
    if (!s1.empty())
        front = s1.peek();
}
```

**Complexity Analysis**

- Time complexity : O(1)*O*(1).
- Space complexity : O(1)*O*(1).

**Empty**

Stack `s1` contains all stack elements, so the algorithm checks `s1` size to return if the queue is empty.

```java
// Return whether the queue is empty.
public boolean empty() {
    return s1.isEmpty();
}
```

Time complexity : O(1)*O*(1).

Space complexity : O(1)*O*(1).

**Peek**

The `front` element is kept in constant memory and is modified when we push or pop an element.

```java
// Get the front element.
public int peek() {
  return front;
}
```

Time complexity : O(1)*O*(1). The `front` element has been calculated in advance and only returned in `peek` operation.

Space complexity : O(1)*O*(1).

------

#### Approach #2 (Two Stacks) Push - O(1)*O*(1) per operation, Pop - Amortized O(1)*O*(1) per operation.

**Algorithm**

**Push**

The newly arrived element is always added on top of stack `s1` and the first element is kept as `front` queue element

![Push an element in queue](https://leetcode.com/media/original_images/232_queue_using_stacksAPush.png)

*Figure 3. Push an element in queue*

**Java**

```java
private Stack<Integer> s1 = new Stack<>();
private Stack<Integer> s2 = new Stack<>();

// Push element x to the back of queue.
public void push(int x) {
    if (s1.empty())
        front = x;
    s1.push(x);
}
```

**Complexity Analysis**

- Time complexity : O(1).

Appending an element to a stack is an O(1) operation.

- Space complexity : O*(*n). We need additional memory to store the queue elements

**Pop**

We have to remove element in front of the queue. This is the first inserted element in the stack `s1` and it is positioned at the bottom of the stack because of stack's `LIFO (last in - first out)` policy. To remove the bottom element from `s1`, we have to pop all elements from `s1` and to push them on to an additional stack `s2`, which helps us to store the elements of `s1` in reversed order. This way the bottom element of `s1` will be positioned on top of `s2` and we can simply pop it from stack `s2`. Once `s2` is empty, the algorithm transfer data from `s1` to `s2` again.

![Pop an element from stack](https://leetcode.com/media/original_images/232_queue_using_stacksAPop.png)

*Figure 4. Pop an element from stack*

**Java**

```java
// Removes the element from in front of queue.
public void pop() {
    if (s2.isEmpty()) {
        while (!s1.isEmpty())
            s2.push(s1.pop());
    }
    s2.pop();    
}
```

**Complexity Analysis**

- Time complexity: Amortized O(1), Worst-case O(n).

In the worst case scenario when stack `s2` is empty, the algorithm pops n elements from stack s1 and pushes n elements to `s2`, where n is the queue size. This gives 2n2*n* operations, which is O(n). But when stack `s2` is not empty the algorithm has O(1) time complexity. So what does it mean by Amortized O(1)? Please see the next section on Amortized Analysis for more information.

- Space complexity : O(1).

**Amortized Analysis**

Amortized analysis gives the average performance (over time) of each operation in the worst case. The basic idea is that a worst case operation can alter the state in such a way that the worst case cannot occur again for a long time, thus amortizing its cost.

Consider this example where we start with an empty queue with the following sequence of operations applied:

push_1, push_2, \ldots, push_n, pop_1,pop_2 \ldots, pop_n*p**u**s**h*1,*p**u**s**h*2,…,*p**u**s**h**n*,*p**o**p*1,*p**o**p*2…,*p**o**p**n*

The worst case time complexity of a single pop operation is O(n)*O*(*n*). Since we have n*n* pop operations, using the worst-case per operation analysis gives us a total of O(n^2)*O*(*n*2) time.

However, in a sequence of operations the worst case does not occur often in each operation - some operations may be cheap, some may be expensive. Therefore, a traditional worst-case per operation analysis can give overly pessimistic bound. For example, in a dynamic array only some inserts take a linear time, though others - a constant time.

In the example above, the number of times pop operation can be called is limited by the number of push operations before it. Although a single pop operation could be expensive, it is expensive only once per `n` times (queue size), when `s2` is empty and there is a need for data transfer between `s1` and `s2`. Hence the total time complexity of the sequence is : `n` (for push operations) + `2*n` (for first pop operation) + `n - 1` ( for pop operations) which is O(2*n)*O*(2∗*n*).This gives O(2n/2n)*O*(2*n*/2*n*) = O(1)*O*(1) average time per operation.

**Empty**

Both stacks `s1` and `s2` contain all stack elements, so the algorithm checks `s1` and `s2` size to return if the queue is empty.

```java
// Return whether the queue is empty.
public boolean empty() {
    return s1.isEmpty() && s2.isEmpty();
}
```

Time complexity : O(1)*O*(1).

Space complexity : O(1)*O*(1).

**Peek**

The `front` element is kept in constant memory and is modified when we push an element. When `s2` is not empty, front element is positioned on the top of `s2`

```java
// Get the front element.
public int peek() {
    if (!s2.isEmpty()) {
            return s2.peek();
    }
    return front;
}
```

Time complexity : O(1)*O*(1).

The `front` element was either previously calculated or returned as a top element of stack `s2`. Therefore complexity is O(1)*O*(1)

Space complexity : O(1)*O*(1).

#### Implementation - Using Two Stacks (Approach 2)

```java
class MyQueue {
    private Stack<Integer> stack1, stack2;
    private int front;

    /** Initialize your data structure here. */
    public MyQueue() {
        stack1 = new Stack<>();
        stack2 = new Stack<>(); 
    }
    
    /** Push element x to the back of queue. */
    public void push(int x) {
        if (stack1.isEmpty()) front = x;
        stack1.push(x);
    }
    
    /** Removes the element from in front of queue and returns that element. */
    public int pop() {
        if (stack2.isEmpty())
            while (!stack1.isEmpty()) stack2.push(stack1.pop());
        return stack2.pop();
    }
    
    /** Get the front element. */
    public int peek() {
        if (!stack2.isEmpty()) return stack2.peek();
        return front;
    }
    
    /** Returns whether the queue is empty. */
    public boolean empty() {
        return stack1.isEmpty() && stack2.isEmpty();
    }
}
```

#### **Practical Significance**

It's a common way to implement a queue in functional programming languages with purely functional (immutable, but sharing structure) lists (e.g. Clojure, Haskell, Erlang...):

- use a pair of lists to represent a queue where elements are in FIFO order in the first list and in LIFO order in the second list
- enqueue to the queue by prepending to the second list
- dequeue from the queue by taking the first element of the first list
- if the first list is empty: reverse the second list and replace the first list with it, and replace the second list with an empty list

(all operations return the new queue object in addition to any possible return values)

The point is that adding (removing) an element to (from) the front of a purely functional list is O(1) and the reverse operation which is O(n) is amortised over all the dequeues, so it's close to O(1), thereby giving you a ~O(1) queue implementation with immutable data structures.

<u>***Lock-free***</u>: The application for this implementation is to separate read & write of a queue in multi-processing. One of the stack is for read, and another is for write. They only interfere each other when the former one is full or latter is empty. When there's only one thread doing the read/write operation to the stack, there will always one stack empty. However, in a multi-thread application, if we have only one queue, for thread-safety, either read or write will lock the whole queue. In the two stack implementation, as long as the second stack is not empty, push operation will not lock the stack for pop.