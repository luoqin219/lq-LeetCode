# List

### 2.  Add Two Numbers - Medium

You are given two **non-empty** linked lists representing two non-negative integers. The digits are stored in **reverse order** and each of their nodes contain a single digit. Add the two numbers and return it as a linked list.

You may assume the two numbers do not contain any leading zero, except the number 0 itself.

**Example:**

```
Input: (2 -> 4 -> 3) + (5 -> 6 -> 4)
Output: 7 -> 0 -> 8
Explanation: 342 + 465 = 807.
```

#### Approach 1: Elementary Math

**Intuition**

Keep track of the carry using a variable and simulate digits-by-digits sum starting from the head of list, which contains the least-significant digit.

![Illustration of Adding two numbers](https://leetcode.com/problems/add-two-numbers/Figures/2_add_two_numbers.svg)

*Figure 1. Visualization of the addition of two numbers: 342 + 465 = 807342+465=807.
Each node contains a single digit and the digits are stored in reverse order.*

**Algorithm**

Just like how you would sum two numbers on a piece of paper, we begin by summing the least-significant digits, which is the head of l1*l*1 and l2*l*2. Since each digit is in the range of 0…9, summing two digits may "overflow". For example 5 + 7 = 12. In this case, we set the current digit to 2 and bring over the carry = 1 to the next iteration. carry must be either 0 or 1 because the largest possible sum of two digits (including the carry) is 9 + 9 + 1 = 19.

![image-20191110230414607](C:\Users\73995\AppData\Roaming\Typora\typora-user-images\image-20191110230414607.png)

**Implementation**

```java
public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
    ListNode dummyHead = new ListNode(0);
    ListNode p = l1, q = l2, curr = dummyHead;
    int carry = 0;
    while (p != null || q != null) {
        int x = (p != null) ? p.val : 0;
        int y = (q != null) ? q.val : 0;
        int sum = carry + x + y;
        carry = sum / 10;
        curr.next = new ListNode(sum % 10);
        curr = curr.next;
        if (p != null) p = p.next;
        if (q != null) q = q.next;
    }
    if (carry > 0) {
        curr.next = new ListNode(carry);
    }
    return dummyHead.next;
}
```

**Complexity Analysis**

- Time complexity :O*(max(*m*,*n*)). Assume that m and n represents the length of l1 and l2 respectively, the algorithm above iterates at most max(*m*,*n) times.
- Space complexity : O*(max(*m*,*n)). The length of the new list is at most max(m,n) + 1.

**Follow up**

What if the the digits in the linked list are stored in non-reversed order? For example:

(3→4→2)+(4→6→5)=8→0→7



Solution:
We need to add numbers from the most right digit to left digit.
Compared with last question, we can reverse two lists and add them.
And reverse our output is correct answer.



TC: O(n)
SC: O(1)

```java
public ListNode add(ListNode l1, ListNode l2) {
    if (l1 == null) {
        return l2;
    } else if (l2 == null) {
        return l1;
    }
    l1 = reverse(l1);
    l2 = reverse(l2);
    int sum = 0;
    int carry = 0;
    ListNode dummy = new ListNode(0);
    ListNode curr = dummy;
    while (l1 != null || l2 != null) {
        int x = l1 == null ? 0 : l1.val;
        int y = l2 == null ? 0 : l2.val;
        sum = x + y + carry;
        carry = sum / 10;
        curr.next = new ListNode(sum % 10);
        curr = curr.next;
        if (l1 != null) {
           l1 = l1.next;
        }
        if (l2 != null) {
           l2 = l2.next;
        }
    }
    if (carry > 0) {
        curr.next = new ListNode(carry);
    }
    return reverse(dummy.next);
}
private ListNode reverse(ListNode head) {
    if (head == null || head.next == null) {
        return head;
    }
    ListNode prev = null;
    ListNode curr = head;
    while (curr != null) {
        ListNode next = curr.next;
        curr.next = prev;
        prev = curr;
        curr = next;
    }
    return prev;
} 
```



### 141. Linked List Cycle - Easy

Given a linked list, determine if it has a cycle in it.

To represent a cycle in the given linked list, we use an integer `pos` which represents the position (0-indexed) in the linked list where tail connects to. If `pos` is `-1`, then there is no cycle in the linked list.

 

**Example 1:**

```
Input: head = [3,2,0,-4], pos = 1
Output: true
Explanation: There is a cycle in the linked list, where tail connects to the second node.
```

![img](https://assets.leetcode.com/uploads/2018/12/07/circularlinkedlist.png)

**Example 2:**

```
Input: head = [1,2], pos = 0
Output: true
Explanation: There is a cycle in the linked list, where tail connects to the first node.
```

![img](https://assets.leetcode.com/uploads/2018/12/07/circularlinkedlist_test2.png)

**Example 3:**

```
Input: head = [1], pos = -1
Output: false
Explanation: There is no cycle in the linked list.
```

![img](https://assets.leetcode.com/uploads/2018/12/07/circularlinkedlist_test3.png)

**Follow up:**

Can you solve it using *O(1)* (i.e. constant) memory? -> two pointers

#### Approach 1: Hash Table

**Intuition**

To detect if a list is cyclic, we can check whether a node had been visited before. A natural way is to use a hash table.

**Algorithm**

We go through each node one by one and record each node's reference (or memory address) in a hash table. If the current node is `null`, we have reached the end of the list and it must not be cyclic. If current node’s reference is in the hash table, then return true.

```java
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) {
 *         val = x;
 *         next = null;
 *     }
 * }
 */
public class Solution {
    public boolean hasCycle(ListNode head) {
        HashSet<ListNode> set = new HashSet<>();
        
        ListNode curr = head;
        while (curr != null) {
            if (!set.contains(curr)) {
                set.add(curr);
            }
            else {
                return true;
            }
            curr = curr.next;
        }
        
        return false;
    }
}
```

**Complexity analysis**

- Time complexity : O*(*n*). We visit each of the n* elements in the list at most once. Adding a node to the hash table costs only O(1) time.
- Space complexity: O*(*n*). The space depends on the number of elements added to the hash table, which contains at most n* elements.



#### Approach 2: Two Pointers

**Intuition**

Imagine two runners running on a track at different speed. What happens when the track is actually a circle?

**Algorithm**

The space complexity can be reduced to O(1)*O*(1) by considering two pointers at **different speed** - a slow pointer and a fast pointer. The slow pointer moves one step at a time while the fast pointer moves two steps at a time.

If there is no cycle in the list, the fast pointer will eventually reach the end and we can return false in this case.

Now consider a cyclic list and imagine the slow and fast pointers are two runners racing around a circle track. The fast runner will eventually meet the slow runner. Why? Consider this case (we name it case A) - The fast runner is just one step behind the slow runner. In the next iteration, they both increment one and two steps respectively and meet each other.

How about other cases? For example, we have not considered cases where the fast runner is two or three steps behind the slow runner yet. This is simple, because in the next or next's next iteration, this case will be reduced to case A mentioned above.

```java
public boolean hasCycle(ListNode head) {
    if (head == null || head.next == null) {
        return false;
    }
    ListNode slow = head;
    ListNode fast = head.next;
    while (slow != fast) {
        if (fast == null || fast.next == null) {
            return false;
        }
        slow = slow.next;
        fast = fast.next.next;
    }
    return true;
}
```

**Complexity analysis**

- Time complexity : *O*(*n*). Let us denote n*n* as the total number of nodes in the linked list. To analyze its time complexity, we consider the following two cases separately.

  - ***List has no cycle:***
    The fast pointer reaches the end first and the run time depends on the list's length, which is O*(*n).
  - ***List has a cycle:***
    ![image-20191228234326917](C:\Users\73995\AppData\Roaming\Typora\typora-user-images\image-20191228234326917.png)

  Therefore, the worst case time complexity is O*(*N*+*K*), which is O*(*n*).

- Space complexity : O*(1). We only use two nodes (slow and fast) so the space complexity is O*(1).



### 160. Intersection of Two Linked Lists - Easy

Write a program to find the node at which the intersection of two singly linked lists begins.

For example, the following two linked lists:

[![img](https://assets.leetcode.com/uploads/2018/12/13/160_statement.png)](https://assets.leetcode.com/uploads/2018/12/13/160_statement.png)

begin to intersect at node c1.

 

**Example 1:**

[![img](https://assets.leetcode.com/uploads/2020/06/29/160_example_1_1.png)](https://assets.leetcode.com/uploads/2020/06/29/160_example_1_1.png)

```
Input: intersectVal = 8, listA = [4,1,8,4,5], listB = [5,6,1,8,4,5], skipA = 2, skipB = 3
Output: Reference of the node with value = 8
Input Explanation: The intersected node's value is 8 (note that this must not be 0 if the two lists intersect). From the head of A, it reads as [4,1,8,4,5]. From the head of B, it reads as [5,6,1,8,4,5]. There are 2 nodes before the intersected node in A; There are 3 nodes before the intersected node in B.
```

 

**Example 2:**

[![img](https://assets.leetcode.com/uploads/2020/06/29/160_example_2.png)](https://assets.leetcode.com/uploads/2020/06/29/160_example_2.png)

```
Input: intersectVal = 2, listA = [1,9,1,2,4], listB = [3,2,4], skipA = 3, skipB = 1
Output: Reference of the node with value = 2
Input Explanation: The intersected node's value is 2 (note that this must not be 0 if the two lists intersect). From the head of A, it reads as [1,9,1,2,4]. From the head of B, it reads as [3,2,4]. There are 3 nodes before the intersected node in A; There are 1 node before the intersected node in B.
```

 

**Example 3:**

[![img](https://assets.leetcode.com/uploads/2018/12/13/160_example_3.png)](https://assets.leetcode.com/uploads/2018/12/13/160_example_3.png)

```
Input: intersectVal = 0, listA = [2,6,4], listB = [1,5], skipA = 3, skipB = 2
Output: null
Input Explanation: From the head of A, it reads as [2,6,4]. From the head of B, it reads as [1,5]. Since the two lists do not intersect, intersectVal must be 0, while skipA and skipB can be arbitrary values.
Explanation: The two lists do not intersect, so return null.
```

 

**Notes:**

- If the two linked lists have no intersection at all, return `null`.
- The linked lists must retain their original structure after the function returns.
- You may assume there are no cycles anywhere in the entire linked structure.
- Each value on each linked list is in the range `[1, 10^9]`.
- Your code should preferably run in O(n) time and use only O(1) memory.

#### Implementation: Two Pointers

```java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        ListNode cursorA = headA, cursorB = headB;
        while (cursorA != cursorB) {
            if (cursorA == null) {
                cursorA = headB;
            } else {
                cursorA = cursorA.next;
            }
            if (cursorB == null) {
                cursorB = headA;
            } else {
                cursorB = cursorB.next;
            }
        }
        return cursorA;
    }
}
```

**Complexity Analysis**

- Time complexity : *O*(*m*+*n*).
- Space complexity : *O*(1).



### 143. Reorder List - Medium

Given a singly linked list *L*: *L*0→*L*1→…→*Ln*-1→*L*n,
reorder it to: *L*0→*L*n*→*L*1→*L*n*-1→*L*2→*Ln*-2→…

You may **not** modify the values in the list's nodes, only nodes itself may be changed.

**Example 1:**

```
Given 1->2->3->4, reorder it to 1->4->2->3.
```

**Example 2:**

```
Given 1->2->3->4->5, reorder it to 1->5->2->4->3.
```

#### Approach 1: Reverse the Second Part of the List and Merge Two Sorted Lists

**Overview**

- Find a middle node of the linked list. If there are two middle nodes, return the second middle node. Example: for the list `1->2->3->4->5->6`, the middle element is `4`.
- Once a middle node has been found, reverse the second part of the list. Example: convert `1->2->3->4->5->6` into `1->2->3->4` and `6->5->4`.
- Now merge the two sorted lists. Example: merge `1->2->3->4` and `6->5->4` into `1->6->2->5->3->4`.

<img src="https://leetcode.com/problems/reorder-list/Figures/143/overview.png" alt="append" style="zoom: 50%;" />

Now let's check each algorithm part in more detail.

**Find a Middle Node**

Let's use two pointers, `slow` and `fast`. While the slow pointer moves one step forward `slow = slow.next`, the fast pointer moves two steps forward `fast = fast.next.next`, *i.e.* `fast` traverses twice as fast as `slow`. When the fast pointer reaches the end of the list, the slow pointer should be in the middle.

<img src="https://leetcode.com/problems/reorder-list/Figures/143/slow_fast.png" alt="append" style="zoom:50%;" />

<iframe src="https://leetcode.com/playground/GeNJhikz/shared" frameborder="0" width="100%" height="174" name="GeNJhikz" style="box-sizing: border-box; margin: 20px 0px; color: rgba(0, 0, 0, 0.65); font-family: -apple-system, system-ui, &quot;Segoe UI&quot;, &quot;PingFang SC&quot;, &quot;Hiragino Sans GB&quot;, &quot;Microsoft YaHei&quot;, &quot;Helvetica Neue&quot;, Helvetica, Arial, sans-serif, &quot;Apple Color Emoji&quot;, &quot;Segoe UI Emoji&quot;, &quot;Segoe UI Symbol&quot;; font-size: 14px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; background-color: rgb(255, 255, 255); text-decoration-style: initial; text-decoration-color: initial;"></iframe>

**Reverse the Second Part of the List**

Let's traverse the list starting from the middle node `slow` and its virtual predecessor `None`. For each current node, save its neighbours: the previous node `prev` and the next node `tmp = curr.next`.

While you're moving along the list, change the node's next pointer to point to the previous node: `curr.next = prev`, and shift the current node to the right for the next iteration: `prev = curr`, `curr = tmp`.

<img src="https://leetcode.com/problems/reorder-list/Figures/143/reverse2.png" alt="append" style="zoom:50%;" />

<iframe src="https://leetcode.com/playground/Rngn8AQV/shared" frameborder="0" width="100%" height="242" name="Rngn8AQV" style="box-sizing: border-box; margin: 20px 0px; color: rgba(0, 0, 0, 0.65); font-family: -apple-system, system-ui, &quot;Segoe UI&quot;, &quot;PingFang SC&quot;, &quot;Hiragino Sans GB&quot;, &quot;Microsoft YaHei&quot;, &quot;Helvetica Neue&quot;, Helvetica, Arial, sans-serif, &quot;Apple Color Emoji&quot;, &quot;Segoe UI Emoji&quot;, &quot;Segoe UI Symbol&quot;; font-size: 14px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; background-color: rgb(255, 255, 255); text-decoration-style: initial; text-decoration-color: initial;"></iframe>

**Merge Two Sorted Lists**

This algorithm is similar to the one for list reversal.

Let's pick the first node of each list - first and second, and save their successors. While you're traversing the list, set the first node's next pointer to point to the second node, and the second node's next pointer to point to the successor of the first node. For this iteration the job is done, and for the next iteration move to the previously saved nodes' successors.

<img src="https://leetcode.com/problems/reorder-list/Figures/143/first_second.png" alt="append" style="zoom:50%;" />

<iframe src="https://leetcode.com/playground/9t4zjDtN/shared" frameborder="0" width="100%" height="259" name="9t4zjDtN" style="box-sizing: border-box; margin: 20px 0px; color: rgba(0, 0, 0, 0.65); font-family: -apple-system, system-ui, &quot;Segoe UI&quot;, &quot;PingFang SC&quot;, &quot;Hiragino Sans GB&quot;, &quot;Microsoft YaHei&quot;, &quot;Helvetica Neue&quot;, Helvetica, Arial, sans-serif, &quot;Apple Color Emoji&quot;, &quot;Segoe UI Emoji&quot;, &quot;Segoe UI Symbol&quot;; font-size: 14px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; background-color: rgb(255, 255, 255); text-decoration-style: initial; text-decoration-color: initial;"></iframe>

**Implementation**

```java
class Solution {
  public void reorderList(ListNode head) {
    if (head == null) return;

    // find the middle of linked list [Problem 876]
    // in 1->2->3->4->5->6 find 4 
    ListNode slow = head, fast = head;
    while (fast != null && fast.next != null) {
      slow = slow.next;
      fast = fast.next.next;
    }

    // reverse the second part of the list [Problem 206]
    // convert 1->2->3->4->5->6 into 1->2->3->4 and 6->5->4
    // reverse the second half in-place
    ListNode prev = null, curr = slow, tmp;
    while (curr != null) {
      tmp = curr.next;

      curr.next = prev;
      prev = curr;
      curr = tmp;
    }

    // merge two sorted linked lists [Problem 21]
    // merge 1->2->3->4 and 6->5->4 into 1->6->2->5->3->4
    ListNode first = head, second = prev;
    while (second.next != null) {
      tmp = first.next;
      first.next = second;
      first = tmp;

      tmp = second.next;
      second.next = first;
      second = tmp;
    }
  }
}
```

**Complexity Analysis**

- Time complexity: O(*N*). There are three steps here. To identify the middle node takes O(*N*) time. To reverse the second part of the list, one needs *N*/2 operations. The final step, to merge two lists, requires *N*/2 operations as well. In total, that results in O(*N*) time complexity.
- Space complexity: O(1), since we do not allocate any additional data structures.

