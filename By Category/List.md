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

