# Sort

### 148. Sort List - Medium (MergeSort)

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

 利用归并的思想，递归地将当前链表分为两段，然后merge，分两段的方法是使用 fast-slow  法，用两个指针，一个每次走两步，一个走一步。当快的走到了末尾，慢的所在位置就是中间位置，这样就分成了两段。merge时，把两段头部节点值比较，用一个 p 指向较小的，且记录第一个节点，然后 两段的头一步一步向后走，p也一直向后走，总是指向较小节点，直至其中一个头为NULL，处理剩下的元素。最后返回记录的头即可。 



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

		// sort by ascending starting point
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

**Complexity Analysis**

- Time: O(NlogN)

- Space: O(N)




### 147. Insertion Sort List - Medium (Insertion Sort)

Sort a linked list using insertion sort.

![img](https://upload.wikimedia.org/wikipedia/commons/0/0f/Insertion-sort-example-300px.gif)
A graphical example of insertion sort. The partial sorted list (black) initially contains only the first element in the list.
With each iteration one element (red) is removed from the input data and inserted in-place into the sorted list

**Algorithm of Insertion Sort:**

1. Insertion sort iterates, consuming one input element each repetition, and growing a sorted output list.
2. At each iteration, insertion sort removes one element from the input data, finds the location it belongs within the sorted list, and inserts it there.
3. It repeats until no input elements remain.



#### Implementation - dummy head

```java
class Solution {
   public ListNode insertionSortList(ListNode head) {
        ListNode dummy = new ListNode(0);
        ListNode prev = dummy;
        ListNode curr = head;
        while (curr != null) {
            ListNode temp = curr.next;
        
            /* Before insert, the prev is at the last node of the sorted list.
            Only when the last node's value is larger than the current inserting node 
            should we move the temp back to the head*/
            if (prev.val >= curr.val) prev = dummy;
            // needn't insert, just move forward
            while (prev.next != null && prev.next.val < curr.val) {
                prev = prev.next;
            }
            // insert
            curr.next = prev.next;
            prev.next = curr;
            curr = temp;
        }
        return dummy.next;
    }
}
```



### 253. Meeting Rooms II - Medium (Priority Queue)

Given an array of meeting time intervals consisting of start and end times `[[s1,e1],[s2,e2],...]` (si < ei), find the minimum number of conference rooms required.

**Example 1:**

```
Input: [[0, 30],[5, 10],[15, 20]]
Output: 2
```

**Example 2:**

```
Input: [[7,10],[2,4]]
Output: 1
```

**NOTE:** input types have been changed on April 15, 2019. Please reset to default code definition to get new method signature.

#### Implementation - Priority Queue(Min Heap)

```java
class Solution {
    public int minMeetingRooms(int[][] intervals) {
        // check corner cases
        if (intervals == null || intervals.length == 0) return 0;
        // min-heap
        PriorityQueue<Integer> minHeap = new PriorityQueue<>(intervals.length, (Integer a, Integer b) -> a - b);
        Arrays.sort(intervals, (int[] a, int[] b) -> a[0] - b[0]);
        minHeap.add(intervals[0][1]);
        for (int i = 1; i < intervals.length; i++) {
            // occupy the free room
            if (intervals[i][0] >= minHeap.peek()) minHeap.poll(); 
            // add the newest end time
            minHeap.add(intervals[i][1]);
        }
        return minHeap.size();
    }
}
```

**Complexity Analysis**

- Time Complexity: O(NlogN).

  - There are two major portions that take up time here. One is `sorting` of the array that takes O(Nlog N) considering that the array consists of N*N* elements.
  - Then we have the `min-heap`. In the worst case, all *N* meetings will collide with each other. In any case we have N*N* add operations on the heap. In the worst case we will have *N* extract-min operations as well. Overall complexity being (NlogN) since extract-min operation on a heap takes O(logN).

- Space Complexity: *O*(*N*) because we construct the `min-heap` and that can contain N*N* elements in the worst case as described above in the time complexity section. Hence, the space complexity is O(N).

#### Approach 1: Priority Queues

We can't really process the given meetings in any random order. The most basic way of processing the meetings is in increasing order of their `start times` and this is the order we will follow. After all if you're an IT guy, you should allocate a room to the meeting that is scheduled for 9 a.m. in the morning before you worry about the 5 p.m. meeting, right?

Let's do a dry run of an example problem with sample meeting times and see what our algorithm should be able to do efficiently.

We will consider the following meeting times for our example `(1, 10), (2, 7), (3, 19), (8, 12), (10, 20), (11, 30)`. The first part of the tuple is the start time for the meeting and the second value represents the ending time. We are considering the meetings in a sorted order of their start times. The first diagram depicts the first three meetings where each of them requires a new room because of collisions.

![img](https://leetcode.com/problems/meeting-rooms-ii/Figures/253/253_Meeting_Rooms_II_Diag_1.png)

The next 3 meetings start to occupy some of the existing rooms. However, the last one requires a new room altogether and overall we have to use 4 different rooms to accommodate all the meetings.

![img](https://leetcode.com/problems/meeting-rooms-ii/Figures/253/253_Meeting_Rooms_II_Diag_2.png)

Sorting part is easy, but for every meeting how do we find out efficiently if a room is available or not? At any point in time we have multiple rooms that can be occupied and we don't really care which room is free as long as we find one when required for a new meeting.

A naive way to check if a room is available or not is to iterate on all the rooms and see if one is available when we have a new meeting at hand.

> However, we can do better than this by making use of Priority Queues or the Min-Heap data structure.

Instead of manually iterating on every room that's been allocated and checking if the room is available or not, we can keep all the rooms in a min heap where the key for the min heap would be the ending time of meeting.

So, every time we want to check if **any** room is free or not, simply check the topmost element of the min heap as that would be the room that would get free the earliest out of all the other rooms currently occupied.

If the room we extracted from the top of the min heap isn't free, then `no other room is`. So, we can save time here and simply allocate a new room.

Let us look at the algorithm before moving onto the implementation.

**Algorithm**

1. Sort the given meetings by their `start time`.
2. Initialize a new `min-heap` and add the first meeting's ending time to the heap. We simply need to keep track of the ending times as that tells us when a meeting room will get free.
3. For every meeting room check if the minimum element of the heap i.e. the room at the top of the heap is free or not.
4. If the room is free, then we extract the topmost element and add it back with the ending time of the current meeting we are processing.
5. If not, then we allocate a new room and add it to the heap.
6. After processing all the meetings, the size of the heap will tell us the number of rooms allocated. This will be the minimum number of rooms needed to accommodate all the meetings.

------

#### Approach 2: Chronological Ordering

**Intuition**

The meeting timings given to us define a chronological order of events throughout the day. We are given the start and end timings for the meetings which can help us define this ordering.

Arranging the meetings according to their start times helps us know the natural order of meetings throughout the day. However, simply knowing when a meeting starts doesn't tell us much about its duration.

We also need the meetings sorted by their ending times because an ending event essentially tells us that there must have been a corresponding starting event and more importantly, an ending event tell us that a previously occupied room has now become free.

A meeting is defined by its start and end times. However, for this specific algorithm, we need to treat the start and end times `individually`. This might not make sense right away because a meeting is defined by its start and end times. If we separate the two and treat them individually, then the identity of a meeting goes away. This is fine because:

> When we encounter an ending event, that means that some meeting that started earlier has ended now. We are not really concerned with which meeting has ended. All we need is that **some** meeting ended thus making a room available.

Let us consider the same example as we did in the last approach. We have the following meetings to be scheduled: `(1, 10), (2, 7), (3, 19), (8, 12), (10, 20), (11, 30)`. As before, the first diagram show us that the first three meetings are colliding with each other and they have to be allocated separate rooms.

![img](https://leetcode.com/problems/meeting-rooms-ii/Figures/253/253_Meeting_Rooms_II_Diag_3.png)

The next two diagrams process the remaining meetings and we see that we can now reuse some of the existing meeting rooms. The final result is the same, we need 4 different meeting rooms to process all the meetings. That's the best we can do here.

![img](https://leetcode.com/problems/meeting-rooms-ii/Figures/253/253_Meeting_Rooms_II_Diag_4.png)

![img](https://leetcode.com/problems/meeting-rooms-ii/Figures/253/253_Meeting_Rooms_II_Diag_5.png)

**Algorithm**

1. Separate out the start times and the end times in their separate arrays.
2. Sort the start times and the end times separately. Note that this will mess up the original correspondence of start times and end times. They will be treated individually now.
3. We consider two pointers: `s_ptr` and `e_ptr` which refer to start pointer and end pointer. The start pointer simply iterates over all the meetings and the end pointer helps us track if a meeting has ended and if we can reuse a room.
4. When considering a specific meeting pointed to by `s_ptr`, we check if this start timing is greater than the meeting pointed to by `e_ptr`. If this is the case then that would mean some meeting has ended by the time the meeting at `s_ptr` had to start. So we can reuse one of the rooms. Otherwise, we have to allocate a new room.
5. If a meeting has indeed ended i.e. if `start[s_ptr] >= end[e_ptr]`, then we increment `e_ptr`.
6. Repeat this process until `s_ptr` processes all of the meetings.

Let us not look at the implementation for this algorithm.

```java
class Solution {
    public int minMeetingRooms(int[][] intervals) {
    // check corner case
    if (intervals.length == 0) {
      return 0;
    }

    Integer[] start = new Integer[intervals.length];
    Integer[] end = new Integer[intervals.length];

    for (int i = 0; i < intervals.length; i++) {
      start[i] = intervals[i][0];
      end[i] = intervals[i][1];
    }

    // sort the intervals by end time
    Arrays.sort(
        end,
        new Comparator<Integer>() {
          public int compare(Integer a, Integer b) {
            return a - b;
          }
        });

    // sort the intervals by start time
    Arrays.sort(
        start,
        new Comparator<Integer>() {
          public int compare(Integer a, Integer b) {
            return a - b;
          }
        });

    int startPointer = 0, endPointer = 0;
	// keep track of maximum number of rooms used.
    int usedRooms = 0;

    while (startPointer < intervals.length) {
      // if there is a meeting that has ended by the time the meeting at `start_pointer` starts
      if (start[startPointer] >= end[endPointer]) {
        usedRooms -= 1;
        endPointer += 1;
      }

      // do this irrespective of whether a room frees up or not.
      // if a room got free, then this used_rooms += 1 wouldn't have any effect. used_rooms would
      // remain the same in that case. If no room was free, then this would increase used_rooms
      usedRooms += 1;
      startPointer += 1;

    }

    return usedRooms;
  }
}
```

**Complexity Analysis**

- Time Complexity: O*(*N*log*N) because all we are doing is sorting the two arrays for `start timings` and `end timings` individually and each of them would contain N elements considering there are N intervals.
- Space Complexity: O*(*N) because we create two separate arrays of size N, one for recording the start times and one for the end times.



### 937. Reorder Data in Log Files - Easy

You have an array of `logs`. Each log is a space delimited string of words.

For each log, the first word in each log is an alphanumeric *identifier*. Then, either:

- Each word after the identifier will consist only of lowercase letters, or;
- Each word after the identifier will consist only of digits.

We will call these two varieties of logs *letter-logs* and *digit-logs*. It is guaranteed that each log has at least one word after its identifier.

Reorder the logs so that all of the letter-logs come before any digit-log. The letter-logs are ordered lexicographically ignoring identifier, with the identifier used in case of ties. The digit-logs should be put in their original order.

Return the final order of the logs.

**Example 1:**

```
Input: logs = ["dig1 8 1 5 1","let1 art can","dig2 3 6","let2 own kit dig","let3 art zero"]
Output: ["let1 art can","let3 art zero","let2 own kit dig","dig1 8 1 5 1","dig2 3 6"]
```

**Constraints:**

1. `0 <= logs.length <= 100`
2. `3 <= logs[i].length <= 100`
3. `logs[i]` is guaranteed to have an identifier, and a word after the identifier.



#### Implementation - Custom Order

```java
class Solution {
    public String[] reorderLogFiles(String[] logs) {
        Arrays.sort(logs, (log1, log2) -> {
            String[] split1 = log1.split(" ", 2);
            String[] split2 = log2.split(" ", 2);
            boolean isDigit1 = Character.isDigit(split1[1].charAt(0));
            boolean isDigit2 = Character.isDigit(split2[1].charAt(0));
            if (!isDigit1 && !isDigit2) {
                int cmp = split1[1].compareTo(split2[1]);
                if (cmp != 0) return cmp;
                return split1[0].compareTo(split2[0]);
            }
            return isDigit1 ? (isDigit2 ? 0 : 1) : -1;
        });
        return logs;
    }
}
```

**Complexity Analysis**

- Time Complexity: O*(AlogA)*, where A is the total content of `logs`.
- Space Complexity: O*(A)*.



### 274. H-Index - Medium

Given an array of citations (each citation is a non-negative integer) of a researcher, write a function to compute the researcher's h-index.

According to the [definition of h-index on Wikipedia](https://en.wikipedia.org/wiki/H-index): "A scientist has index *h* if *h* of his/her *N* papers have **at least** *h* citations each, and the other *N − h* papers have **no more than** *h* citations each."

**Example:**

```
Input: citations = [3,0,6,1,5]
Output: 3 
Explanation: [3,0,6,1,5] means the researcher has 5 papers in total and each of them had 
             received 3, 0, 6, 1, 5 citations respectively. 
             Since the researcher has 3 papers with at least 3 citations each and the remaining 
             two with no more than 3 citations each, her h-index is 3.
```

**Note:** If there are several possible values for *h*, the maximum one is taken as the h-index.

#### Implementation: Built-in sorting method

```java
class Solution {
    public int hIndex(int[] citations) {
        Arrays.sort(citations);
        int h = 0;
        for (int i = 0; i < citations.length; i++) {
            int count = citations.length - i;
            if (count <= citations[i]) {
                h = Math.max(count, h);
            }
        }
        return h;
    }
}
```

**Complexity Analysis**

- Time complexity: O(NlogN)
- Space complexity: O(1)

#### Approach 2: Counting sort with slight change

**Intuition**

Comparison sorting algorithm has a lower bound of O(n\log n)*O*(*n*log*n*). To achieve better performance, we need non-comparison based sorting algorithms.

**Algorithm**

From [Approach #1](https://leetcode.com/problems/h-index/solution/#approach-1-sorting-accepted), we sort the citations to find the h-index. However, it is well known that comparison sorting algorithms such as `heapsort`, `mergesort` and `quicksort` have a lower bound of O(n*log*n). The most commonly used non-comparison sorting is `counting sort`.

> Counting sort operates by counting the number of objects that have each distinct key value, and using arithmetic on those tallies to determine the positions of each key value in the output sequence. Its running time is linear in the number of items and the difference between the maximum and minimum keys, so it is only suitable for direct use in situations where the variation in keys is not significantly greater than the number of items.
>
> ---by Wikipedia

However, in our problem, the keys are the citations of each paper which can be much larger than the number of papers n*n*. It seems that we cannot use `counting sort`. The trick here is the following observation:

> Any citation larger than n*n* can be replaced by n*n* and the h*h*-index will not change after the replacement

The reason is that h*h*-index is upper bounded by total number of papers n*n*, i.e.

*h*≤*n*

In the diagram, replacing citations greater than *n* with *n* is equivalent to cutting off the area where *y*>*n*.

<img src="https://leetcode.com/problems/h-index/Figures/274_H_index_2.svg" alt="h-index cut off" style="zoom: 25%;" />

*Figure 2. cutting off the area with citations more than n*

Apparently, cutting that area off will not change the largest **square** and the *h*-index.

After we have the counts, we can get a sorted citations by traversing the counts array. And the rest is the same as [Approach #1](https://leetcode.com/problems/h-index/solution/#approach-1-sorting-accepted).

But we can do even better. The idea is that we don't even need to get sorted citations. We can find the h*h*-index by using the paper counts directly.

To explain this, let's look at the following example:

\mathrm{citations} = [1, 3, 2, 3, 100]citations=[1,3,2,3,100]

The counting results are:

|  *k*  |  0   |  1   |  2   |  3   |  4   |  5   |
| :---: | :--: | :--: | :--: | :--: | :--: | :--: |
| count |  0   |  1   |  1   |  2   |  0   |  1   |
|  s_k  |  5   |  5   |  4   |  3   |  1   |  1   |

The value s_k is defined as "the sum of all counts with citation ≥ *k*" or "the number of papers having, at least, *k* citations". By definition of the h-index, the largest *k* with k *≤* s_k is our answer.

After replacing 100 with n=5, we have citations = [1, 3, 2, 3, 5]. Now, we count the number of papers for each citation number 0 to 5. The counts are [0, 1, 1, 2, 0, 1][0,1,1,2,0,1]. The first *k* from right to left (5 down to 0) that have k*≤*s is the *h*-index 3.

Since we can calculate s_k on the fly when traverse the count array, we only need one pass through the count array which only costs O*(*n) time.

**Implementation**

```java
public class Solution {
    public int hIndex(int[] citations) {
        int n = citations.length;
        int[] papers = new int[n + 1];
        // counting papers for each citation number
        for (int c: citations)
            papers[Math.min(n, c)]++;
        // finding the h-index
        int k = n;
        for (int s = papers[n]; k > s; s += papers[k])
            k--;
        return k;
    }
}
```

**Complexity Analysis**

- Time complexity : O*(*n). There are two steps. The counting part is O*(n*) since we traverse the `citations` array once and only once. The second part of finding the h*h*-index is also O(n)*O*(*n*) since we traverse the `papers` array at most once. Thus, the entire algorithm is O(n)
- Space complexity : *O*(*n*). We use O*(*n) auxiliary space to store the counts.

