# Array

### 350. Intersection of Two Arrays II - Easy

Given two arrays, write a function to compute their intersection.

**Example 1:**

```
Input: nums1 = [1,2,2,1], nums2 = [2,2]
Output: [2,2]
```

**Example 2:**

```
Input: nums1 = [4,9,5], nums2 = [9,4,9,8,4]
Output: [4,9]
```

**Note:**

- Each element in the result should appear as many times as it shows in both arrays.
- The result can be in any order.

**Follow up:**

- What if the given array is already sorted? How would you optimize your algorithm?
- What if *nums1*'s size is small compared to *nums2*'s size? Which algorithm is better?
- What if elements of *nums2* are stored on disk, and the memory is limited such that you cannot load all elements into the memory at once?

#### Implementation - HashMap

```java
class Solution {
    public int[] intersect(int[] nums1, int[] nums2) {
        if (nums1.length > nums2.length) return intersect (nums2, nums1);
        
        Map<Integer, Integer> map = new HashMap<>();
        for (int n: nums1) map.put(n, map.getOrDefault(n, 0) + 1);
        
        int size = 0;
        for (int n: nums2) {
            int count = map.getOrDefault(n, 0);
            if (count > 0) {
                nums1[size++] = n;
                map.put(n, count-1);
            }
        }
        
        return Arrays.copyOfRange(nums1, 0, size);
    }
}
```

**Complexity Analysis**

- Time complexity: O(*n*+*m*), where *n* and *m* are the lengths of the arrays. We iterate through the first, and then through the second array; insert and lookup operations in the hash map take a constant time.
- Space complexity: O(min(*n*,*m*)). We use hash map to store numbers (and their counts) from the smaller array.

#### Approach 2: Sort

**You can recommend this method when the input is sorted, or when the output needs to be sorted.** Here, we sort both arrays (assuming they are not sorted) and use **two pointers** to find common numbers in a single scan.

![Sort Illustration](https://leetcode.com/problems/intersection-of-two-arrays-ii/Figures/350/350_approach2-v2.png)

**Algorithm**

1. Sort `nums1` and `nums2`.
2. Initialize `i`, `j` and `k` with zero.
3. Move indices `i` along `nums1`, and `j` through `nums2`:
   - Increment `i` if `nums1[i]` is smaller.
   - Increment `j` if `nums2[j]` is smaller.
   - If numbers are the same, copy the number into `nums1[k]`, and increment `i`, `j` and `k`.
4. Return first `k` elements of `nums1`.

<iframe src="https://leetcode.com/playground/ih9uynPf/shared" frameborder="0" width="100%" height="327" name="ih9uynPf" style="box-sizing: border-box; margin: 20px 0px; color: rgba(0, 0, 0, 0.65); font-family: -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, &quot;PingFang SC&quot;, &quot;Hiragino Sans GB&quot;, &quot;Microsoft YaHei&quot;, &quot;Helvetica Neue&quot;, Helvetica, Arial, sans-serif, &quot;Apple Color Emoji&quot;, &quot;Segoe UI Emoji&quot;, &quot;Segoe UI Symbol&quot;; font-size: 14px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; background-color: rgb(255, 255, 255); text-decoration-style: initial; text-decoration-color: initial;"></iframe>

**Complexity Analysis**

- Time complexity: O(*n*log*n*+*m*log*m*), where *n* and *m* are the lengths of the arrays. We sort two arrays independently, and then do a linear scan.
- Space complexity: O(1). We sort the arrays in-place. We ignore the space to store the output as it is not essential to the algorithm itself.

------

#### Follow-up Questions

1. What if the given array is already sorted? How would you optimize your algorithm?
   - We can use either [Approach 2](https://leetcode.com/problems/intersection-of-two-arrays-ii/solution/#approach-2-sort) or [Approach 3](https://leetcode.com/problems/intersection-of-two-arrays-ii/solution/#approach-3-built-in-intersection), dropping the sort of course. It will give us linear time and constant memory complexity.
2. What if *nums1's* size is small compared to *nums2's* size? Which algorithm is better?
   - [Approach 1](https://leetcode.com/problems/intersection-of-two-arrays-ii/solution/#approach-1-hash-map) is a good choice here as we use a hash map for the smaller array.
3. What if elements of *nums2* are stored on disk, and the memory is limited such that you cannot load all elements into the memory at once?
   - If `nums1` fits into the memory, we can use [Approach 1](https://leetcode.com/problems/intersection-of-two-arrays-ii/solution/#approach-1-hash-map) to collect counts for `nums1` into a hash map. Then, we can sequentially load and process `nums2`.
   - If neither of the arrays fit into the memory, we can apply some partial processing strategies:
     - Split the numeric range into subranges that fits into the memory. Modify [Approach 1](https://leetcode.com/problems/intersection-of-two-arrays-ii/solution/#approach-1-hash-map) to collect counts only within a given subrange, and call the method multiple times (for each subrange).
     - Use an external sort for both arrays. Modify [Approach 2](https://leetcode.com/problems/intersection-of-two-arrays-ii/solution/#approach-2-sort) to load and process arrays sequentially.

### 1365. How Many Numbers Are Smaller Than the Current Number - Easy

Given the array `nums`, for each `nums[i]` find out how many numbers in the array are smaller than it. That is, for each `nums[i]` you have to count the number of valid `j's` such that `j != i` **and** `nums[j] < nums[i]`.

Return the answer in an array.

**Example 1:**

```
Input: nums = [8,1,2,2,3]
Output: [4,0,1,1,3]
Explanation: 
For nums[0]=8 there exist four smaller numbers than it (1, 2, 2 and 3). 
For nums[1]=1 does not exist any smaller number than it.
For nums[2]=2 there exist one smaller number than it (1). 
For nums[3]=2 there exist one smaller number than it (1). 
For nums[4]=3 there exist three smaller numbers than it (1, 2 and 2).
```

**Example 2:**

```
Input: nums = [6,5,4,8]
Output: [2,1,0,3]
```

**Example 3:**

```
Input: nums = [7,7,7,7]
Output: [0,0,0,0]
```

 

**Constraints:**

- `2 <= nums.length <= 500`
- `0 <= nums[i] <= 100`

#### Implementation - Brute-force

```java
class Solution {
    public int[] smallerNumbersThanCurrent(int[] nums) {
        // brute-force method
        int[] res = new int[nums.length];
        for (int i = 0; i < nums.length; i++) {
             int count = 0;
             for (int m: nums) {
                 if (m < nums[i]) count++;
             }
             res[i] = count;
         }
         return res;
    }
}
```

**Complexity Analysis**

- Space: O(N)

- Time: O(N^2)


#### Implementation - Buck

```java
class Solution {
    public int[] smallerNumbersThanCurrent(int[] nums) {
        // buck method
        int[] buck = new int[101];
        // calculate the frequence of each number
        for (int num: nums) buck[num]++;
        // iterate to count num of smaller ones
        for (int i = 1; i < 101; i++) {
            buck[i] += buck[i-1];
        }
        // record the result
        int res[] = new int[nums.length];
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] == 0) res[i] = 0;
            else res[i] = buck[nums[i] - 1];
        }
        
        return res;
    }
}
```

**Complexity Analysis**

- Space: O(N)

- Time: O(N)



### 435. Non-overlapping Intervals - Medium

Given a collection of intervals, find the minimum number of intervals you need to remove to make the rest of the intervals non-overlapping.

**Example 1:**

```
Input: [[1,2],[2,3],[3,4],[1,3]]
Output: 1
Explanation: [1,3] can be removed and the rest of intervals are non-overlapping.
```

**Example 2:**

```
Input: [[1,2],[1,2],[1,2]]
Output: 2
Explanation: You need to remove two [1,2] to make the rest of intervals non-overlapping.
```

**Example 3:**

```
Input: [[1,2],[2,3]]
Output: 0
Explanation: You don't need to remove any of the intervals since they're already non-overlapping.
```

**Note:**

1. You may assume the interval's end point is always bigger than its start point.
2. Intervals like [1,2] and [2,3] have borders "touching" but they don't overlap each other.

#### Approcah 1: Greedy approach based on END point

**Algorithm**

The Greedy approach just discussed was based on choosing intervals greedily based on the starting points. But in this approach, we go for choosing points greedily based on the end points. For this, firstly we sort the given intervals based on the end points. Then, we traverse over the sorted intervals. While traversing, if there is no overlapping between the previous interval and the current interval, we need not remove any interval. But, if an overlap exists between the previous interval and the current interval, we always drop the current interval.

To explain how it works, again we consider every possible arrangement of the intervals.

![Non_overlapping](https://leetcode.com/problems/non-overlapping-intervals/Figures/435_NonOverlapping_greedy3.JPG)

**Case 1:**

> The two intervals currently considered are non-overlapping:

In this case, we need not remove any interval and for the next iteration the current interval becomes the previous interval.

**Case 2:**

> The two intervals currently considered are overlapping and the starting point of the later interval falls before the starting point of the previous interval:

In this case, as shown in the figure below, it is obvious that the later interval completely subsumes the previous interval. Hence, it is advantageous to remove the later interval so that we can get more range available to accommodate future intervals. Thus, previous interval remains unchanged and the current interval is updated.

**Case 3:**

> The two intervals currently considered are overlapping and the starting point of the later interval falls before the starting point of the previous interval:

In this case, the only opposition to remove the current interval arises because it seems that more intervals could be accommodated by removing the previous interval in the range marked by A. But that won't be possible as can be visualized with a case similar to Case 3a and 3b shown below in approach 2. But, if we remove the current interval, we can save the range B to accommodate further intervals. Thus, previous interval remains unchanged and the current interval is updated.

**Implementation**

```java
class Solution {
    public int eraseOverlapIntervals(int[][] intervals) {
        if (intervals.length == 0) return 0;
        Arrays.sort(intervals, (int[] a, int[] b) -> a[1] - b[1]);
        int end = intervals[0][1];
        int count = 1;
        for (int i = 1; i < intervals.length; i++) {
          if (intervals[i][0] >= end) {
            end = intervals[i][1];
            count++;
          }
        }
        return intervals.length - count;
    }
}
```

**Complexity Analysis**

- Time complexity : *O*(*n*log(*n*)). Sorting takes *O*(*n*log(*n*)) time.
- Space complexity : *O*(1). No extra space is used.

#### Approach 2: Greedy approach based on START point

**Algorithm**

**if two intervals are overlapping, we want to remove the interval that has the longer end point -- the longer interval will always overlap with more or the same number of future intervals compared to the shorter one**

If we sort the given intervals based on starting points, the greedy approach works very well. While considering the intervals in the ascending order of starting points, we make use of a pointer prev pointer to keep track of the interval just included in the final list. While traversing, we can encounter 3 possibilities as shown in the figure:

![Non_overlapping](https://leetcode.com/problems/non-overlapping-intervals/Figures/435_NonOverlapping_greedy1.JPG)

**Case 1:**

> The two intervals currently considered are non-overlapping:

In this case, we need not remove any interval and we can continue by simply assigning the prev*p**r**e**v* pointer to the later interval and the count of intervals removed remains unchanged.

**Case 2:**

> The two intervals currently considered are overlapping and the end point of the later interval falls before the end point of the previous interval:

In this case, we can simply take the later interval. The choice is obvious since choosing an interval of smaller width will lead to more available space labelled as A*A* and B*B*, in which more intervals can be accommodated. Hence, the prev*p**r**e**v* pointer is updated to current interval and the count of intervals removed is incremented by 1.

**Case 3:**

> The two intervals currently considered are overlapping and the end point of the later interval falls after the end point of the previous interval:

In this case, we can work in a greedy manner and directly remove the later interval. To understand why this greedy approach works, we need to see the figure below, which includes all the subcases possible. It is clear from the figures that we choosing interval 1 always leads to a better solution in the future. Thus, the prev*p**r**e**v* pointer remains unchanged and the count of intervals removed is incremented by 1.

![Non_overlapping](https://leetcode.com/problems/non-overlapping-intervals/Figures/435_NonOverlapping_greedy2.JPG)

**Implementation**

```java
class Solution {
    public int eraseOverlapIntervals(int[][] intervals) {
        if (intervals.length == 0) return 0;
        Arrays.sort(intervals, (int[] a, int[] b) -> a[0] - b[0]);
        int end = intervals[0][1], prev = 0, count = 0;
        for (int i = 1; i < intervals.length; i++) {
          if (intervals[prev][1] > intervals[i][0]) {
            if (intervals[prev][1] > intervals[i][1]) {
              prev = i;
            }
            count++;
          } else {
            prev = i;
          }
        }
        return count;
    }
}
```

**Complexity Analysis**

- Time complexity : *O*(*n*log(*n*)). Sorting takes *O*(*n*log(*n*)) time.
- Space complexity : O(1). No extra space is used.

