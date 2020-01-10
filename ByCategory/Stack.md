# Stack

 ### 20. Valid Parentheses 

Given a string containing just the characters `'('`, `')'`, `'{'`, `'}'`, `'['` and `']'`, determine if the input string is valid.

An input string is valid if:

1. Open brackets must be closed by the same type of brackets.
2. Open brackets must be closed in the correct order.

Note that an empty string is also considered valid.

**Example 1:**

```
Input: "()"
Output: true
```

**Example 2:**

```
Input: "()[]{}"
Output: true
```

**Example 3:**

```
Input: "(]"
Output: false
```

**Example 4:**

```
Input: "([)]"
Output: false
```

**Example 5:**

```
Input: "{[]}"
Output: true
```

**Intuition**

Imagine you are writing a small compiler for your college project and one of the tasks (or say sub-tasks) for the compiler would be to detect if the parenthesis are in place or not.

The algorithm we will look at in this article can be then used to process all the parenthesis in the program your compiler is compiling and checking if all the parenthesis are in place. This makes checking if a given string of parenthesis is valid or not, an important programming problem.

The expressions that we will deal with in this problem can consist of three different type of parenthesis:

- `()`,
- `{}` and
- `[]`

Before looking at how we can check if a given expression consisting of these parenthesis is valid or not, let us look at a simpler version of the problem that consists of just one type of parenthesis. So, the expressions we can encounter in this simplified version of the problem are e.g.

```
(((((()))))) -- VALID

()()()()     -- VALID

(((((((()    -- INVALID

((()(())))   -- VALID
```

Let's look at a simple algorithm to deal with this problem.

1. We process the expression one bracket at a time starting from the left.
2. Suppose we encounter an opening bracket i.e. `(`, it may or may not be an invalid expression because there can be a matching ending bracket somewhere in the remaining part of the expression. Here, we simply increment the counter keeping track of left parenthesis till now. `left += 1`
3. If we encounter a closing bracket, this has two meanings:
   1. One, there was no matching opening bracket for this closing bracket and in that case we have an invalid expression. This is the case when `left == 0` i.e. when there are no unmatched left brackets available.
   2. We had `some unmatched` opening bracket available to match this closing bracket. This is the case when `left > 0` i.e. we have unmatched left brackets available.
4. If we encounter a closing bracket i.e. `)` when `left == 0`, then we have an invalid expression on our hands. Else, we decrement `left` thus reducing the number of unmatched left parenthesis available.
5. Continue processing the string until all parenthesis have been processed.
6. If in the end we still have unmatched left parenthesis available, this implies an invalid expression.

The reason we discussed this particular algorithm here is because the approach for the original problem derives its inspiration from this very solution. Have a look at the following dry run of the algorithm we discussed to have a better understanding.



If we try and follow the same approach for our original problem, then it simply won't work. The reason a simple counter based approach works above is because all the parenthesis are of the same type. So when we encounter a closing bracket, we simply assume a corresponding opening matching bracket to be available i.e. if `left > 0`.

But, in our problem, if we encounter say `]`, we don't really know if there is a corresponding opening `[` available or not. You could say:

> Why not maintain a separate counter for the different types of parenthesis?

This doesn't work because the relative placement of the parenthesis also matters here. e.g.:

```
[{]
```

If we simply keep counters here, then as soon as we encounter the closing square bracket, we would know there is an unmatched opening square bracket available as well. But, the **closest unmatched opening bracket available is a curly bracket and not a square bracket** and hence the counting approach breaks here.

#### Approach: Stacks

An interesting property about a valid parenthesis expression is that a sub-expression of a valid expression should also be a valid expression. (Not every sub-expression) e.g.

![img](https://leetcode.com/problems/valid-parentheses/Figures/20/20-Valid-Parentheses-Recursive-Property.png)

Also, if you look at the above structure carefully, the color coded cells mark the opening and closing pairs of parenthesis. The entire expression is valid, but sub portions of it are also valid in themselves. This lends a sort of a recursive structure to the problem. For e.g. Consider the expression enclosed within the two green parenthesis in the diagram above. The opening bracket at index `1` and the corresponding closing bracket at index `6`.

> What if whenever we encounter a matching pair of parenthesis in the expression, we simply remove it from the expression?

Let's have a look at this idea below where remove the smaller expressions one at a time from the overall expression and since this is a valid expression, we would be left with an empty string in the end.

> The stack data structure can come in handy here in representing this recursive structure of the problem. We can't really process this from the inside out because we don't have an idea about the overall structure. But, the stack can help us process this recursively i.e. from outside to inwards.

Let us have a look at the algorithm for this problem using stacks as the intermediate data structure.

**Algorithm**

1. Initialize a stack S.
2. Process each bracket of the expression one at a time.
3. If we encounter an opening bracket, we simply push its corresponding closing bracket onto the stack. 
4. If we encounter a closing bracket, then we check the element on top of the stack. If the element at the top of the stack is a same one, then we pop it off the stack and continue processing. Else, this implies an invalid expression.
5. In the end, if we are left with a stack still having elements, then this implies an invalid expression.

**Implementation**

```java
class Solution {
    public boolean isValid(String s) {
        Stack<Character> stack = new Stack<Character>();
	    for (char c : s.toCharArray()) {
		    if (c == '(')
			    stack.push(')');
		    else if (c == '{')
			    stack.push('}');
		    else if (c == '[')
			    stack.push(']');
		    else if (stack.isEmpty() || stack.pop() != c)
			    return false;
	    }
	    return stack.isEmpty();
    }
}
```



### 225. Implement Stack using Queues - Easy (Using Two Queues)

Implement the following operations of a stack using queues.

- push(x) -- Push element x onto stack.
- pop() -- Removes the element on top of the stack.
- top() -- Get the top element.
- empty() -- Return whether the stack is empty.

**Example:**

```
MyStack stack = new MyStack();

stack.push(1);
stack.push(2);  
stack.top();   // returns 2
stack.pop();   // returns 2
stack.empty(); // returns false
```

**Notes:**

- You must use *only* standard operations of a queue -- which means only `push to back`, `peek/pop from front`, `size`, and `is empty` operations are valid.
- Depending on your language, queue may not be supported natively. You may simulate a queue by using a list or deque (double-ended queue), as long as you use only standard operations of a queue.
- You may assume that all operations are valid (for example, no pop or top operations will be called on an empty stack).

#### Approach #1 (Two Queues, push - O(1)*O*(1), pop O(n)*O*(*n*) )

**Intuition**

Stack is **LIFO** (last in - first out) data structure, in which elements are added and removed from the same end, called `top`. In general stack is implemented using array or linked list, but in the current article we will review a different approach for implementing stack using queues. In contrast queue is **FIFO** (first in - first out) data structure, in which elements are added only from the one side - `rear` and removed from the other - `front`. In order to implement stack using queues, we need to maintain two queues `q1` and `q2`. Also we will keep top stack element in a constant memory.

**Algorithm**

**Push**

The new element is always added to the rear of queue `q1` and it is kept as `top` stack element

![Push an element in stack](https://leetcode.com/media/original_images/225_stack_using_queues_pushA.png)

*Figure 1. Push an element in stack*

**Java**

```java
private Queue<Integer> q1 = new LinkedList<>();
private Queue<Integer> q2 = new LinkedList<>();
private int top;

// Push element x onto stack.
public void push(int x) {
    q1.add(x);
    top = x;
}
```

**Complexity Analysis**

- Time complexity : O(1)*O*(1). Queue is implemented as linked list and `add` operation has O(1)*O*(1) time complexity.
- Space complexity : O(1)*O*(1)

**Pop**

We need to remove the element from the top of the stack. This is the last inserted element in `q1`. Because queue is FIFO (first in - first out) data structure, the last inserted element could be removed only after all elements, except it, have been removed. For this reason we need to maintain additional queue `q2`, which will serve as a temporary storage to enqueue the removed elements from q1. The last inserted element in `q2` is kept as top. Then the algorithm removes the last element in `q1`. We swap `q1` with `q2` to avoid copying all elements from `q2` to `q1`.

![Pop an element from stack](https://leetcode.com/media/original_images/225_stack_using_queues_popA.png)

*Figure 2. Pop an element from stack*

**Java**

```java
// Removes the element on top of the stack.
public void pop() {
    while (q1.size() > 1) {
        top = q1.remove();
        q2.add(top);
    }
    q1.remove();
    Queue<Integer> temp = q1;
    q1 = q2;
    q2 = temp;
}
```

**Complexity Analysis**

- Time complexity : O(n)*O*(*n*). The algorithm dequeues n elements from `q1` and enqueues n - 1*n*−1 elements to `q2`, where n*n* is the stack size. This gives 2n - 12*n*−1 operations.
- Space complexity : O(1)*O*(1).

------

#### Approach #2 (Two Queues, push - O(n)*O*(*n*), pop O(1)*O*(1) )

**Algorithm**

**Push**

The algorithm inserts each new element to queue `q2` and keep it as the `top` element. In case queue `q1` is not empty (there are elements in the stack), we remove all elements from `q1` and add them to `q2`. In this way the new inserted element (`top` element in the stack) will be always positioned at the front of `q2`. We swap `q1` with `q2` to avoid copying all elements from `q2` to `q1`.

![Push an element in stack](https://leetcode.com/media/original_images/225_stack_using_queues_pushB.png)

*Figure 3. Push an element in stack*

**Java**

```java
public void push(int x) {
    q2.add(x);
    top = x;
    while (!q1.isEmpty()) {                
        q2.add(q1.remove());
    }
    Queue<Integer> temp = q1;
    q1 = q2;
    q2 = temp;
}
```

**Complexity Analysis**

- Time complexity : O(n)*O*(*n*). The algorithm removes n elements from `q1` and inserts n + 1*n*+1 elements to `q2`, where n is the stack size. This gives 2n + 12*n*+1 operations. The operations `add` and `remove` in linked lists has O(1)*O*(1) complexity.
- Space complexity : O(1)*O*(1).

**Pop**

The algorithm dequeues an element from queue `q1` and keeps front element of `q1` as `top`.

![Pop an element from stack](https://leetcode.com/media/original_images/225_stack_using_queues_popB.png)

*Figure 4. Pop an element from stack*

**Java**

```java
// Removes the element on top of the stack.
public void pop() {
    q1.remove();
    if (!q1.isEmpty()) {
    	top = q1.peek();
    }
}
```

**Complexity Analysis**

- Time complexity : O(1)*O*(1).
- Space complexity : O(1)*O*(1).

In both approaches `empty` and `top` operations have the same implementation.

**Empty**

Queue `q1` always contains all stack elements, so the algorithm checks `q1` size to return if the stack is empty.

```java
// Return whether the stack is empty.
public boolean empty() {
    return q1.isEmpty();
}
```

Time complexity : O(1)*O*(1).

Space complexity : O(1)*O*(1).

**Top**

The `top` element is kept in constant memory and is modified each time when we push or pop an element.

```java
// Get the top element.
public int top() {
    return top;
}
```

Time complexity : O(1)*O*(1). The `top` element has been calculated in advance and only returned in `top` operation.

Space complexity : O(1)*O*(1).

------

#### Approach #3 (One Queue, push - O(n)*O*(*n*), pop O(1)*O*(1) )

The mentioned above two approaches have one weakness, they use two queues. This could be optimized as we use only one queue, instead of two.

**Algorithm**

**Push**

When we push an element into a queue, it will be stored at back of the queue due to queue's properties. But we need to implement a stack, where last inserted element should be in the front of the queue, not at the back. To achieve this we can invert the order of queue elements when pushing a new element.

![Push an element in stack](https://leetcode.com/media/original_images/225_stack_using_queues_pushC.png)

*Figure 5. Push an element in stack*

**Java**

```java
private LinkedList<Integer> q1 = new LinkedList<>();

// Push element x onto stack.
public void push(int x) {
    q1.add(x);
    int sz = q1.size();
    while (sz > 1) {
        q1.add(q1.remove());
        sz--;
    }
}
```

**Complexity Analysis**

- Time complexity : O(n)*O*(*n*). The algorithm removes n elements and inserts n + 1*n*+1 elements to `q1` , where n is the stack size. This gives 2n + 12*n*+1 operations. The operations `add` and `remove` in linked lists has O(1)*O*(1) complexity.
- Space complexity : O(1)*O*(1).

**Pop**

The last inserted element is always stored at the front of `q1` and we can pop it for constant time.

**Java**

```java
// Removes the element on top of the stack.
public void pop() {
    q1.remove();
}
```

**Complexity Analysis**

- Time complexity : O(1)*O*(1).
- Space complexity : O(1)*O*(1).

**Empty**

Queue `q1` contains all stack elements, so the algorithm checks if `q1` is empty.

```java
// Return whether the stack is empty.
public boolean empty() {
    return q1.isEmpty();
}
```

Time complexity : O(1)*O*(1).

Space complexity : O(1)*O*(1).

**Top**

The `top` element is always positioned at the front of `q1`. Algorithm return it.

```java
// Get the top element.
public int top() {
    return q1.peek();
}
```

Time complexity : O(1)*O*(1).

Space complexity : O(1)*O*(1).

#### Implementation - Approach 1

```java
class MyStack {
    Queue<Integer> q1,q2;
    int top;
    /** Initialize your data structure here. */
    public MyStack() {
        q1 = new LinkedList<>();
        q2 = new LinkedList<>();
    }
    
    /** Push element x onto stack. */
    public void push(int x) {
        q1.offer(x);
        top = x;
    }
    
    /** Removes the element on top of the stack and returns that element. */
    public int pop() {
        while (q1.size() > 1) {
            top = q1.poll();
            q2.offer(top);
        }
        int pop = q1.poll();
        Queue<Integer> temp = q1;
        q1 = q2;
        q2 = temp;
        
        return pop;
    }
    
    /** Get the top element. */
    public int top() {
        return top;
    }
    
    /** Returns whether the stack is empty. */
    public boolean empty() {
        return q1.isEmpty() && q2.isEmpty();
    }
}
```



### 155. Min Stack - Easy

Design a stack that supports push, pop, top, and retrieving the minimum element in constant time.

- push(x) -- Push element x onto stack.
- pop() -- Removes the element on top of the stack.
- top() -- Get the top element.
- getMin() -- Retrieve the minimum element in the stack.

**Example:**

```
MinStack minStack = new MinStack();
minStack.push(-2);
minStack.push(0);
minStack.push(-3);
minStack.getMin();   --> Returns -3.
minStack.pop();
minStack.top();      --> Returns 0.
minStack.getMin();   --> Returns -2.
```



#### Implementation - Using One Stack

```java
class MinStack {
    Stack<Integer> stack = new Stack<>();
    int min = Integer.MAX_VALUE;

    public void push(int x) {
        if (x <= min) {
            stack.push(min);
            min = x;
        }
        stack.push(x);
    }

    public void pop() {
        int peek = stack.pop();
        if (peek == min){
            min = stack.pop();
        }
    }

    public int top() {
        return stack.peek();
    }

    public int getMin() {
        return min;
    }
}
```

#### Implementation2 - Using LinkedList

```java
class MinStack {
    Node head;
    class Node{
        int value;
        int min;
        Node next;
        
        Node(int x, int min){
            this.value = x;
            this.min = min;
            next = null;
        }
    }
    
    public void push(int x) {
        if(null == head){
            head = new Node(x,x);
        }else{
            Node n = new Node(x, Math.min(x,head.min));
            n.next = head;
            head = n;
        }
    }

    public void pop() {
        if(head != null)
            head = head.next;
    }

    public int top() {
        if(head != null)
            return head.value;
        return Integer.MIN_VALUE;
    }

    public int getMin() {
        if(null != head)
            return head.min;
        return Integer.MIN_VALUE;
    }
}
```

