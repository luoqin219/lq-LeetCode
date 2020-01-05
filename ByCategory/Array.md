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