# Sort

### 148. Sort List - Medium

Sort a linked list in *O*(*n* log *n*) time using constant space complexity.

**Example 1:**

```
Input: 4->2->1->3
Output: 1->2->3->4
```

**Example 2:**

```
Input: -1->5->3->4->0
Output: -1->0->3->4->5
```

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode sortList(ListNode head) {
        if (head == null || head.next == null) return head;
        ListNode pre = null, slow = head, fast = head;
        while (fast != null && fast.next != null) {
            pre = slow; // control the first linklist's tail
            slow = slow.next;
            fast = fast.next.next;
        }
        pre.next = null; // closing up
        // using the returned node as the header
        return merge(sortList(head), sortList(slow));
        }

    private ListNode merge(ListNode head1, ListNode head2) {
        ListNode newHead = new ListNode(0);
        ListNode tail = newHead;
        while (head1 != null || head2 != null) {
            if (head2 == null || (head1 != null && head1.val <= head2.val)) {
                tail.next = head1;
                head1 = head1.next;
            } else {
                tail.next = head2;
                head2 = head2.next;
            }
            tail = tail.next;
        }
        return newHead.next;
    }
}
```

 利用归并的思想，递归地将当前链表分为两段，然后merge，分两段的方法是使用 fast-slow  法，用两个指针，一个每次走两步，一个走一步，知道快的走到了末尾，然后慢的所在位置就是中间位置，这样就分成了两段。merge时，把两段头部节点值比较，用一个 p 指向较小的，且记录第一个节点，然后  两段的头一步一步向后走，p也一直向后走，总是指向较小节点，直至其中一个头为NULL，处理剩下的元素。最后返回记录的头即可。 

![image-20191107174240710](C:\Users\73995\AppData\Roaming\Typora\typora-user-images\image-20191107174240710.png)



### 56. Merge Intervals - Medium 

Given a collection of intervals, merge all overlapping intervals.

**Example 1:**

```
Input: [[1,3],[2,6],[8,10],[15,18]]
Output: [[1,6],[8,10],[15,18]]
Explanation: Since intervals [1,3] and [2,6] overlaps, merge them into [1,6].
```

**Example 2:**

```
Input: [[1,4],[4,5]]
Output: [[1,5]]
Explanation: Intervals [1,4] and [4,5] are considered overlapping.
```

```java
class Solution {
    public int[][] merge(int[][] intervals) {
        if (intervals.length <= 1)
			return intervals;

		// Sort by ascending starting point
		Arrays.sort(intervals, (a, b) -> a[0] - b[0]);
        List<int[]> ret = new ArrayList<>();
        int[] prev = null;
        for (int[] inter : intervals) {
            //if prev is null or curr.start > prev.end, add the interval
            if (prev == null || inter[0] > prev[1]) {
                ret.add(inter);
                prev = inter;
            } else if (inter[1] > prev[1]) {
                // curr.end > prev.end, modify the element already in list
                prev[1] = inter[1];
            }
        }
        return ret.toArray(new int[ret.size()][2]);
    }
}
```

O(NlogN)

O(N)