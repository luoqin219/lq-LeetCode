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

- Time complexity : O(1). Queue is implemented as linked list and `add` operation has O(1)*O*(1) time complexity.
- Space complexity : O(1)

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

- Time complexity : O(n). The algorithm dequeues n elements from `q1` and enqueues n - 1 elements to `q2`, where n is the stack size. This gives 2n - 1 operations.
- Space complexity : O(1).

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

- Time complexity : O(n). The algorithm removes n elements from `q1` and inserts n + 1 elements to `q2`, where n is the stack size. This gives 2n + 1 operations. The operations `add` and `remove` in linked lists has O(1) complexity.
- Space complexity : O(1).

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

- Time complexity : O(1).
- Space complexity : O(1).

In both approaches `empty` and `top` operations have the same implementation.

**Empty**

Queue `q1` always contains all stack elements, so the algorithm checks `q1` size to return if the stack is empty.

```java
// Return whether the stack is empty.
public boolean empty() {
    return q1.isEmpty();
}
```

Time complexity : O(1).

Space complexity : O(1).

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

#### Approach #3 (One Queue, push - O(n), pop O(1) )

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

- Time complexity : O(n). The algorithm removes n elements and inserts n + 1 elements to `q1` , where n is the stack size. This gives 2n + 1 operations. The operations `add` and `remove` in linked lists has O(1) complexity.
- Space complexity : O(1).

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

- Time complexity : O(1).
- Space complexity : O(1).

**Empty**

Queue `q1` contains all stack elements, so the algorithm checks if `q1` is empty.

```java
// Return whether the stack is empty.
public boolean empty() {
    return q1.isEmpty();
}
```

Time complexity : O(1).

Space complexity : O(1).

**Top**

The `top` element is always positioned at the front of `q1`. Algorithm return it.

```java
// Get the top element.
public int top() {
    return q1.peek();
}
```

Time complexity : O(1).

Space complexity : O(1).

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



### 484. Find Permutation - Medium

By now, you are given a **secret signature** consisting of character 'D' and 'I'. 'D' represents a decreasing relationship between two numbers, 'I' represents an increasing relationship between two numbers. And our **secret signature** was constructed by a special integer array, which contains uniquely all the different number from 1 to n (n is the length of the secret signature plus 1). For example, the secret signature "DI" can be constructed by array [2,1,3] or [3,1,2], but won't be constructed by array [3,2,4] or [2,1,3,4], which are both illegal constructing special string that can't represent the "DI" **secret signature**.

On the other hand, now your job is to find the lexicographically smallest permutation of [1, 2, ... n] could refer to the given **secret signature** in the input.

**Example 1:**

```
Input: "I"
Output: [1,2]
Explanation: [1,2] is the only legal initial spectial string can construct secret signature "I", where the number 1 and 2 construct an increasing relationship.
```

**Example 2:**

```
Input: "DI"
Output: [2,1,3]
Explanation: Both [2,1,3] and [3,1,2] can construct the secret signature "DI", 
but since we want to find the one with the smallest lexicographical permutation, you need to output [2,1,3]
```

**Note:**

The input string will only contain the character 'D' and 'I'.

The length of input string is a positive integer and will not exceed 10,000

#### Approach 1: Use Stack

Let's revisit the important points of the given problem statement. For a given n, we need to use all the integers in the range (1,n) to generate a lexicographically smallest permutation of these n numbers which satsfies the pattern given in the string s.

Firstly, we note that the lexicographically smallest permutation that can be generated(irrelevant of the given pattern s) using the n integers from (1,n) is [1, 2, 3,.., n][1,2,3,..,*n*](say min). Thus, while generating the required permutation, we can surely say that the permutation generated should be **as close as possible to min**.

Now, we can also note that the number generated will be the smallest possible only if **the digits lying towards the most significant positions are as small as possible** while satisfying the given pattern. Now, to understand how these observations help in providing the solution of the given problem, we'll look at a simple example.

Say, the given pattern s*s* is `"DDIIIID"`. This corresponds to n=8. Thus, the min permutation possible will be `[1, 2, 3, 4, 5, 6, 7, 8]`. Now, to satisfy the first two `"DD"` pattern, we can observe that the best course of action to generate the smallest arrangement will be to rearrange only `1, 2, 3`, because these are the smallest numbers that can be placed at the three most significant positions to generate the smallest arrangement satisfying the given pattern till now, leading to the formation of `[3, 2, 1, 4, 5, 6, 7, 8]` till now. We can note that placing any number larger than 3 at any of these positions will lead to the generation of a lexicographically larger arrangement as discussed above.

We can also note that irrespective of how we rearrange the first three `1, 2, 3`, the relationship of the last number among them with the next number always satisfies the criteria required for satisfying the first `I` in s. Further, note that, the pattern generated till now already satisfies the subpattern `"IIII"` in s. This will always be satisfied since the min number considered originally always satisfies the increasing criteria.

Now, when we find the last `"D"` in the pattern s, we again need to make rearrangements in the last two positions only and we need to use only the numbers `7, 8` in such rearrangements at those positions. This is because, again, we would like to keep the larger number towards the least significant possible as much as possible to generate the lexicographically smallest arrangement. Thus, to satisfy the last `"D"` the best arrangement of the last two numbers is `8, 7` leading to the generation of `[3, 2, 1, 4, 5, 6, 8, 7]` as the required output.

Based on the above example, we can summarize that, to generate the required arrangement, we can **start off with the min number that can be formed for the given n. Then, to satisfy the given pattern s, we need to reverse only those subsections of the min array which have a `D` in the pattern at their corresponding positions**.

To perform these operations, we need not necessarily create the min array first, because it simply consists of numbers from 1 to n in ascending order.

To perform the operations discussed above, we can make use of a stack. We can start considering the numbers i from 1 to n. For every current number, whenver we find a `D` in the pattern s, we just push that number onto the stack. This is done so that, later on, when we find the next `I`, we can pop off these numbers from the stack leading to the formation of a reversed (descending) subpattern of those numbers corrresponding to the `D`'s in s(as discussed above).

When we encounter an `I` in the pattern, as discussed above, we push the current number as well onto the stack and then pop-off all the numbers on the stack and append these numbers to the resultant pattern formed till now.

Now, we could reach the end of the pattern s with a trail of `D`'s at the end. In this case, we won't find an ending `I` to pop-off the numbers on the stack. Thus, at the end, we push the value n on the stack and then pop all the values on the stack and append them to the resultant pattern formed till now. Now, if the second last character in s was an `I`, n will be appended at the end of the resultant arrangement correctly. If the second last character was a `D`, the reversed pattern appended at the end of the resultant arrangement will be reversed including the last number n. In both the cases, the resultant arrangement turns out to be correct.

#### Implementation

```java
public class Solution {
    public int[] findPermutation(String s) {
        int[] res = new int[s.length() + 1];
        Stack < Integer > stack = new Stack < > ();
        int j = 0;
        for (int i = 1; i <= s.length(); i++) {
            if (s.charAt(i - 1) == 'I') {
                stack.push(i);
                while (!stack.isEmpty())
                    res[j++] = stack.pop();
            } else
                stack.push(i);
        }
        stack.push(s.length() + 1);
        while (!stack.isEmpty())
            res[j++] = stack.pop();
        return res;
    }
}
```

**Complexity Analysis**

- Time complexity : O(n). n numbers will be pushed and popped from the stack. Here, n refers to the number of elements in the resultant arrangement.
- Space complexity : O(n). The stack can grow upto a depth of n*n* in the worst case.

#### Approach 2: Reverse the Subarray

**Algorithm**

In order to reverse the subsections of the min array, as discussed in the last approach, we can also start by initializing the resultant arrangement res with the min array i.e. by filling with elements (1,n) in ascending order. Then, while traversing the pattern s, we can keep a track of the starting and ending indices in res corresponding to the `D`'s in the pattern s*s*, and reverse the portions of the sub-arrays in res corresponding to these indices. The reasoning behind this remains the same as discussed in the last approach.

**Implementation**

```java
public class Solution {
    public int[] findPermutation(String s) {
        int[] res = new int[s.length() + 1];
        for (int i = 0; i < res.length; i++)
            res[i] = i + 1;
        int i = 1;
        while (i <= s.length()) {
            int j = i;
            while (i <= s.length() && s.charAt(i - 1) == 'D')
                i++;
            reverse(res, j - 1, i);
            i++;
        }
        return res;
    }
    public void reverse(int[] a, int start, int end) {
        for (int i = 0; i < (end - start) / 2; i++) {
            int temp = a[i + start];
            a[i + start] = a[end - i - 1];
            a[end - i - 1] = temp;
        }
    }
}
```

**Complexity Analysis**

- Time complexity : O(n). The resultant array of size n is traversed atmost three times, in the worst case e.g. "DDDDDD"
- Space complexity : O(1). Constant extra space is used.

#### Approach 3: Two Pointers

<img src="/Users/qinluo/Library/Application Support/typora-user-images/image-20200815140521683.png" alt="image-20200815140521683" style="zoom: 50%;" />

**Implementation**

```java
public class Solution {
    public int[] findPermutation(String s) {
        int[] res = new int[s.length() + 1];
        res[0]=1;
        int i = 1;
        while (i <= s.length()) {
            res[i]=i+1;
            int j = i;
            if(s.charAt(i-1)=='D')
            {
                while (i <= s.length() && s.charAt(i - 1) == 'D')
                    i++;
                for (int k = j - 1, c = i; k <= i - 1; k++, c--) {
                    res[k] = c;
                }
            }
            else
                i++;
        }
        return res;
    }
}
```

**Complexity Analysis**

- Time complexity : O(n). The resultant array of size n is traversed atmost two times, in the worst case e.g. "DDDDDD"
- Space complexity : O(1). Constant extra space is used.

