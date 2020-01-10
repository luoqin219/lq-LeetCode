# Heap

### 347. Top K Frequent Elements - Medium

Given a non-empty array of integers, return the ***k\*** most frequent elements.

**Example 1:**

```
Input: nums = [1,1,1,2,2,3], k = 2
Output: [1,2]
```

**Example 2:**

```
Input: nums = [1], k = 1
Output: [1]
```

**Note:**

- You may assume *k* is always valid, 1 ≤ *k* ≤ number of unique elements.
- Your algorithm's time complexity **must be** better than O(*n* log *n*), where *n* is the array's size.



#### Implementation - HashMap & Min Heap

```java
class Solution {
    public List<Integer> topKFrequent(int[] nums, int k) {
        // pair elements and its frequency O(N)
        HashMap<Integer, Integer> map = new HashMap<>();
        for (int a: nums) map.put(a, map.getOrDefault(a, 0) + 1);
        // construct a min heap
        PriorityQueue<Integer> heap = new PriorityQueue<>((a, b) -> map.get(a) - map.get(b));
        // add top-k elements in acending order and pop them into the result list O(Nlog(k))
        for (int b: map.keySet()) {
            heap.add(b);
            if (heap.size() > k) heap.poll();
        }

        List<Integer> res = new LinkedList<>();
        while (!heap.isEmpty()) res.add(heap.poll());
        Collections.reverse(res);
        
        return res;
    }
}
```

**Complexity Analysis**

- Time complexity : O(*N*log(*k*)). The overall complexity of the algorithm is O(*N*+*N*log(*k*))=O(*N*log(*k*)).
- Space complexity : O(*N*) to store the hash map.



### 215. Kth Largest Element in an Array - Medium

Find the **k**th largest element in an unsorted array. Note that it is the kth largest element in the sorted order, not the kth distinct element.

**Example 1:**

```
Input: [3,2,1,5,6,4] and k = 2
Output: 5
```

**Example 2:**

```
Input: [3,2,3,1,2,4,5,5,6] and k = 4
Output: 4
```

**Note:**
You may assume k is always valid, 1 ≤ k ≤ array's length.

#### Implementation - Min Heap

```java
class Solution {
    public int findKthLargest(int[] nums, int k) {
        Queue<Integer> minHeap = new PriorityQueue<>((a, b) -> a - b);
        for (int num: nums) {
            minHeap.add(num);
            if (minHeap.size() > k) minHeap.poll();
        }
        
        return minHeap.peek();
    }
}
```

O(klog(N))

O(k)

#### Alternative Approach - Quick Select

This [textbook algorthm](https://en.wikipedia.org/wiki/Quickselect) has \mathcal{O}(N)O(*N*) average time complexity. Like quicksort, it was developed by Tony Hoare, and is also known as *Hoare's selection algorithm*.

The approach is basically the same as for quicksort. For simplicity let's notice that **`k`th largest element is the same as `N - k`th smallest element**, hence one could implement `k`th smallest algorithm for this problem.

First one chooses a pivot, and defines its position in a sorted array in a linear time. This could be done with the help of *partition algorithm*.

> To implement partition one moves along an array, compares each element with a pivot, and moves all elements smaller than pivot to the left of the pivot.

As an output we have an array where pivot is on its perfect position in the ascending sorted array, all elements on the left of the pivot are smaller than pivot, and all elements on the right of the pivot are larger or equal to pivot.

Hence the array is now split into two parts. If that would be a quicksort algorithm, one would proceed recursively to use quicksort for the both parts that would result in O(*N*log*N*) time complexity. **Here there is no need to deal with both parts since now one knows in which part to search for `N - k`th smallest element, and that reduces average time complexity to O(*N*).**

Finally the overall algorithm is quite straightforward :

- Choose a random pivot.
- Use a **partition algorithm** to place the pivot into its perfect position `pos` in the sorted array, move smaller elements to the left of pivot, and larger or equal ones - to the right.
- Compare `pos` and `N - k` to choose the side of array to proceed recursively.

> ! Please notice that this algorithm works well even for arrays with duplicates.

![quickselect](https://leetcode.com/problems/kth-largest-element-in-an-array/Figures/215/215_quickselect.png)

<iframe src="https://leetcode.com/playground/f2LZDmjX/shared" frameborder="0" width="100%" height="500" name="f2LZDmjX" style="box-sizing: border-box; margin: 20px 0px; color: rgba(0, 0, 0, 0.65); font-family: -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, &quot;PingFang SC&quot;, &quot;Hiragino Sans GB&quot;, &quot;Microsoft YaHei&quot;, &quot;Helvetica Neue&quot;, Helvetica, Arial, sans-serif, &quot;Apple Color Emoji&quot;, &quot;Segoe UI Emoji&quot;, &quot;Segoe UI Symbol&quot;; font-size: 14px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; background-color: rgb(255, 255, 255); text-decoration-style: initial; text-decoration-color: initial;"></iframe>

- Time complexity : O(*N*) in the average case, O(*N*^2) in the worst case.
- Space complexity : O(1).