# Google Interview Prep

###  482. License Key Formatting - Easy

You are given a license key represented as a string S which consists only alphanumeric character and dashes. The string is separated into N+1 groups by N dashes.

Given a number K, we would want to reformat the strings such that each group contains *exactly* K characters, except for the first group which could be shorter than K, but still must contain at least one character. Furthermore, there must be a dash inserted between two groups and all lowercase letters should be converted to uppercase.

Given a non-empty string S and a number K, format the string according to the rules described above.

**Example 1:**

```
Input: S = "5F3Z-2e-9-w", K = 4

Output: "5F3Z-2E9W"

Explanation: The string S has been split into two parts, each part has 4 characters.
Note that the two extra dashes are not needed and can be removed.
```



**Example 2:**

```
Input: S = "2-5g-3-J", K = 2

Output: "2-5G-3J"

Explanation: The string S has been split into three parts, each part has 2 characters except the first part as it could be shorter as mentioned above.
```



```java
class Solution {
    public String licenseKeyFormatting(String S, int K) {
        StringBuilder sb = new StringBuilder();
        int count = 0;
        // start from the end because the start can only have one character
        for(int i = S.length() - 1; i >= 0 ; i--) {
            if(S.charAt(i) != '-') {
                if(count == K) {
                    sb.append("-");
                    count = 0;
                }
                sb.append(Character.toUpperCase(S.charAt(i)));
                count++;
                }
        }
        // use reverse to have the normal sequence
        return sb.reverse().toString();
    }
}
```

Runtime: 13 ms, faster than 77.68% of Java online submissions for License Key Formatting.

Memory Usage: 36.8 MB, less than 100.00% of Java online submissions for License Key Formatting.

**Complexity Analysis**

Time complexity: O(n)

Space complexity: O(n)



###  15. 3Sum - Medium

Given an array `nums` of *n* integers, are there elements *a*, *b*, *c* in `nums` such that *a* + *b* + *c* = 0? Find all unique triplets in the array which gives the sum of zero.

**Note:**

The solution set must not contain duplicate triplets.

**Example:**

```
Given array nums = [-1, 0, 1, 2, -1, -4],

A solution set is:
[
  [-1, 0, 1],
  [-1, -1, 2]
]
```

 

```java
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        if (nums.length < 3) return Collections.emptyList();
        Arrays.sort(nums);
        for (int i = 0; i < nums.length - 2; i++) {
            if (nums[i] > 0) break;
            // skip the duplicates
            if (i == 0 || (i > 0 && nums[i] != nums[i-1])) {
                int low = i + 1, high = nums.length-1, sum = 0 - nums[i];
                while (low < high) {
                    if (nums[low] + nums[high] == sum) {
                        res.add(Arrays.asList(nums[i], nums[low], nums[high]));
                        // skip duplicates
                        while (low < high && nums[low] == nums[low+1]) low++;
                        while (low < high && nums[high] == nums[high-1]) high--;
                        low++;
                        high--;
                    }
                    else if (nums[low] + nums[high] < sum) low++;
                    else high--;
                }      
            }
        }
        return res;
    }
}
```

Runtime: 26 ms, faster than 97.81% of Java online submissions for 3Sum.

Memory Usage: 45.9 MB, less than 94.70% of Java online submissions for 3Sum.

**Complexity Analysis**

Time complexity: O(n^2) (thanks to two-pointer scan) 		(Sort the array - O(nlogn))

Space complexity: O(1)



###  300. Longest Increasing Subsequence - Medium

Given an unsorted array of integers, find the length of longest increasing subsequence.

**Example:**

```
Input: [10,9,2,5,3,7,101,18]
Output: 4 
Explanation: The longest increasing subsequence is [2,3,7,101], therefore the length is 4. 
```

**Note:**

- There may be more than one LIS combination, it is only necessary for you to return the length.
- Your algorithm should run in O(*n^2*) complexity.

**Follow up:** Could you improve it to O(*n* log *n*) time complexity?



#### O(nlog(n)) Algorithms using DP

```java
// using Arrays binarySearch method
public class Solution {
    public int lengthOfLIS(int[] nums) {            
        int[] dp = new int[nums.length];
        int len = 0;

        for(int x : nums) {
            int i = Arrays.binarySearch(dp, 0, len, x);
            if(i < 0) i = -(i + 1);
            dp[i] = x;
            if(i == len) len++;
        }

        return len;
    }
}
```

```java
// implement binary search on our own
class Solution {
    public int lengthOfLIS(int[] nums) {
        if(nums == null || nums.length == 0) return 0;
        if(nums.length == 1) return 1;
        int[] dp = new int[nums.length];
        Arrays.fill(dp,Integer.MAX_VALUE);
        for(int i = 0;i< dp.length;i++){
            int pos = binarySearchPosition(dp,nums[i],i);
            dp[pos] = nums[i];
        }
        for(int i = dp.length - 1;i >= 0;i--){
            if(dp[i] != Integer.MAX_VALUE) return i + 1;
        }
        return 0;
    }
    
    private int binarySearchPosition(int[] dp,int target,int hi){
        int low = 0;
        while(low <= hi){
            int mid = low + (hi - low)/2;
            if(target == dp[mid]) return mid;
            else if(target < dp[mid]) hi = mid - 1;
            else if(target > dp[mid]) low = mid + 1;
        }
        return low; // E.g. nums = {1,3,5}, target = 4. Then low = 2, hi = 1 at the end, which make sense for this problem to find the first item to cover.
    }
}
```

Video:  https://www.youtube.com/watch?time_continue=722&v=S9oUiVYEq7E 

Binary Search: https://blog.csdn.net/cxhply/article/details/49423501



### 1007. Minimum Domino Rotations For Equal Row - Medium

In a row of dominoes, `A[i]` and `B[i]` represent the top and bottom halves of the `i`-th domino. (A domino is a tile with two numbers from 1 to 6 - one on each half of the tile.)

We may rotate the `i`-th domino, so that `A[i]` and `B[i]` swap values.

Return the minimum number of rotations so that all the values in `A` are the same, or all the values in `B` are the same.

If it cannot be done, return `-1`.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2019/03/08/domino.png)

```
Input: A = [2,1,2,4,2,2], B = [5,2,6,2,3,2]
Output: 2
Explanation: 
The first figure represents the dominoes as given by A and B: before we do any rotations.
If we rotate the second and fourth dominoes, we can make every value in the top row equal to 2, as indicated by the second figure.
```

**Example 2:**

```
Input: A = [3,5,1,2,3], B = [3,6,3,3,4]
Output: -1
Explanation: 
In this case, it is not possible to rotate the dominoes to make one row of values equal.
```

 

**Note:**

1. `1 <= A[i], B[i] <= 6`
2. `2 <= A.length == B.length <= 20000`



Greedy Algorithm

Let's pick up an arbitrary `i`th domino element in the configuration. The element has two sides, `A[i]` is an upper side and `B[i]` is a lower side.

![bla](https://leetcode.com/problems/minimum-domino-rotations-for-equal-row/Figures/1007/config.png)

There could be three possible situations here

1 . One could make all elements of `A` row or `B` row to be the same and equal to `A[i]` value. For example, if one picks up the `0`th element, it's possible to make all elements of `A` row to be equal to `2`.

![bla](https://leetcode.com/problems/minimum-domino-rotations-for-equal-row/Figures/1007/s1.png)

2 . One could make all elements of `A` row or `B` row to be the same and equal to `B[i]` value. For example, if one picks up the `1`th element, it's possible to make all elements of `B` row to be equal to `2`.

![bla](https://leetcode.com/problems/minimum-domino-rotations-for-equal-row/Figures/1007/s2.png)

3 . It's impossible to make all elements of `A` row or `B` row to have the same `A[i]` or `B[i]` value.

![bla](https://leetcode.com/problems/minimum-domino-rotations-for-equal-row/Figures/1007/s3.png)

> The third situation means that it's impossible to make all elements in `A` row or `B` row to be equal.

Yes, the only one domino element was checked here, and still it's enough because the rotation is the only allowed operation here.

**Algorithm**

- Pick up the first element. It has two sides: `A[0]` and `B[0]`.
- Check if one could make all elements in `A` row or `B` row to be equal to `A[0]`. If yes, return the minimum number of rotations needed.
- Check if one could make all elements in `A` row or `B` row to be equal to `B[0]`. If yes, return the minimum number of rotations needed.
- Otherwise return `-1`.

```java
class Solution {
     /*
    Return min number of rotations 
    if one could make all elements in A or B equal to x.
    Else return -1.
    */
    public int check(int x, int[] A, int[] B, int n) {
        // how many rotations should be done
        // to have all elements in A equal to x
        // and to have all elements in B equal to x
        int rotations_a = 0, rotations_b = 0;
        for (int i = 0; i < n; i++) {
            // rotations coudn't be done
            if (A[i] != x && B[i] != x) return -1;
            // A[i] != x and B[i] == x, swap
            else if (A[i] != x) rotations_a++;
            // A[i] == x and B[i] != x, swap    
            else if (B[i] != x) rotations_b++;
        }
        // min number of rotations to have all
        // elements equal to x in A or B
        return Math.min(rotations_a, rotations_b);
    } 

    public int minDominoRotations(int[] A, int[] B) {
        int n = A.length;
        int rotations = check(A[0], A, B, n);
        // If one could make all elements in A or B equal to A[0]
        if (rotations != -1) return rotations;
        // If one could make all elements in A or B equal to B[0]
        else return check(B[0], A, B, n);
    }
}
```



###  410. Split Array Largest Sum - Hard 

Given an array which consists of non-negative integers and an integer *m*, you can split the array into *m* non-empty continuous subarrays. Write an algorithm to minimize the largest sum among these *m* subarrays.

**Note:**
If *n* is the length of array, assume the following constraints are satisfied:

- 1 ≤ *n* ≤ 1000
- 1 ≤ *m* ≤ min(50, *n*)



**Examples:**

```
Input:
nums = [7,2,5,10,8]
m = 2

Output:
18

Explanation:
There are four ways to split nums into two subarrays.
The best way is to split it into [7,2,5] and [10,8],
where the largest sum among the two subarrays is only 18.
```

#### Approach #1 Brute Force [Time Limit Exceeded]

**Intuition**

Check all possible splitting plans to find the minimum largest value for subarrays.

**Algorithm**

We can use depth-first search to generate all possible splitting plan. For each element in the array, we can choose to append it to the previous subarray or start a new subarray starting with that element (if the number of subarrays does not exceed `m`). The sum of the current subarray can be updated at the same time.

```java
class Solution {
    private int ans;
    private int n, m;
    private void dfs(int[] nums, int i, int cntSubarrays, int curSum, int curMax) {
        if (i == n && cntSubarrays == m) {
            ans = Math.min(ans, curMax);
            return;
        }
        if (i == n) {
            return;
        }
        if (i > 0) {
            dfs(nums, i + 1, cntSubarrays, curSum + nums[i], Math.max(curMax, curSum + nums[i]));
        }
        if (cntSubarrays < m) {
            dfs(nums, i + 1, cntSubarrays + 1, nums[i], Math.max(curMax, nums[i]));
        }
    }
    public int splitArray(int[] nums, int M) {
        ans = Integer.MAX_VALUE;
        n = nums.length;
        m = M;
        dfs(nums, 0, 0, 0, 0);
        return ans;
    }
}
```

**Complexity Analysis**

- Time complexity : O(n^m). To split `n` elements into `m` parts, we can have binom{n - 1}{m - 1} different solutions. This is equivalent to n ^ m.
- Space complexity : O(n). We only need the space to store the array.

#### Approach #2 Dynamic Programming [Accepted]

**Intuition**

The problem satisfies the non-aftereffect property. We can try to use dynamic programming to solve it.

The non-aftereffect property means, once the state of a certain stage is determined, it is not affected by the state in the future. In this problem, if we get the largest subarray sum for splitting `nums[0..i]` into `j` parts, this value will not be affected by how we split the remaining part of `nums`.

To know more about non-aftereffect property, this link may be helpful : http://www.programering.com/a/MDOzUzMwATM.html

**Algorithm**

Let's define `f[i][j]` to be the minimum largest subarray sum for splitting `nums[0..i]` into `j` parts.

Consider the `j`th subarray. We can split the array from a smaller index `k` to `i` to form it. Thus `f[i][j]` can be derived from `max(f[k][j - 1], nums[k + 1] + ... + nums[i])`. For all valid index `k`, `f[i][j]` should choose the minimum value of the above formula.

The final answer should be `f[n][m]`, where `n` is the size of the array.

For corner situations, all the invalid `f[i][j]` should be assigned with `INFINITY`, and `f[0][0]` should be initialized with `0`.

**Implementation**

```java
class Solution {
    public int splitArray(int[] nums, int m) {
        int n = nums.length;
        int[][] f = new int[n + 1][m + 1];
        int[] sub = new int[n + 1];
        for (int i = 0; i <= n; i++) {
            for (int j = 0; j <= m; j++) {
                // initialize as corner situation
                f[i][j] = Integer.MAX_VALUE;
            }
        }
        for (int i = 0; i < n; i++) {
            sub[i + 1] = sub[i] + nums[i];
        }
        // corner case
        f[0][0] = 0;
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= m; j++) {
                for (int k = 0; k < i; k++) {
                    // equation of state 
                    f[i][j] = Math.min(f[i][j], Math.max(f[k][j - 1], sub[i] - sub[k]));
                }
            }
        }
        return f[n][m];        
    }
}
```

**状态转移方程**： `f[i][j]` = `max(f[k][j - 1], nums[k + 1] + ... + nums[i])`

**Complexity Analysis**

- Time complexity : O(n^2 * m). The total number of states is O(n * m). To compute each state `f[i][j]`, we need to go through the whole array to find the optimum `k`. This requires another O(n) loop. So the total time complexity is O(n ^ 2 * m).
- Space complexity : O(n * m). The space complexity is equivalent to the number of states, which is O(n * m).

#### Approach #3 Binary Search + Greedy [Accepted]

**Intuition**

We can easily find a property for the answer:

> If we can find a splitting method that ensures the maximum largest subarray sum will not exceed a value `x`, then we can also find a splitting method that ensures the maximum largest subarray sum will not exceed any value `y` that is greater than `x`.

Lets define this property as `F(x)` for the value `x`. `F(x)` is true means we can find a splitting method that ensures the maximum largest subarray sum will not exceed `x`.

From the discussion above, we can find out that for `x` ranging from `-INFINITY` to `INFINITY`, `F(x)` will start with false, then from a specific value `x0`, `F(x)` will turn to true and stay true forever.

Obviously, the specific value `x0` is our answer.

**Algorithm**

We can use Binary search to find the value `x0`. Keeping a value `mid = (left + right) / 2`. If `F(mid)` is false, then we will search the range `[mid + 1, right]`; If `F(mid)` is true, then we will search `[left, mid - 1]`.

For a given `x`, we can get the answer of `F(x)` using a greedy approach. Using an accumulator `sum` to store the sum of the current processing subarray and a counter `cnt` to count the number of existing subarrays. We will process the elements in the array one by one. For each element `num`, if `sum + num <= x`, it means we can add `num` to the current subarray without exceeding the limit. Otherwise, we need to make a cut here, start a new subarray with the current element `num`. This leads to an increment in the number of subarrays.

After we have finished the whole process, we need to compare the value `cnt` to the size limit of subarrays `m`. If `cnt <= m`, it means we can find a splitting method that ensures the maximum largest subarray sum will not exceed `x`. Otherwise, `F(x)` should be false.

**Implementation**

```java
class Solution {
    public int splitArray(int[] nums, int m) {
        long l = 0;
        long r = 0;        
        int n = nums.length;
        for (int i = 0; i < n; i++) {
            // get the sum of the whole array
            r += nums[i];
            if (l < nums[i]) {
                // get the max single value
                l = nums[i];
            }
        }
        long ans = r;
        while (l <= r) {
            // halve to get the middle value
            long mid = (l + r) >> 1;
            long sum = 0;
            int cnt = 1; // number of subarrays
            for (int i = 0; i < n; i++) {
                if (sum + nums[i] > mid) {
                    cnt++;
                    sum = nums[i];
                } else {
                    sum += nums[i];
                }
            }
            if (cnt <= m) {
                ans = Math.min(ans, mid);
                r = mid - 1;
            } else {
                l = mid + 1;
            }
        }
        return (int)ans;      
    }
}
```

- Set the search range between min=(largest single value) and max=(sum of all values).
  The min starts there because we're looking for the sum of the largest group in the final set of groups. And no matter what groups you create, the largest value has to be in it, so the largest group can't be smaller than that. (This assumes no negative numbers.)

- 

  Calculate the midpoint between min and max. This midpoint is the group size we're going to try out to see how well it performs.

- 

  Split the nums list into groups such that no group has a value larger than the chosen midpoint.
  Note that we may end up with too many or too few groups. That's fine.

- 

  Compare the number of groups we created against the target m. If we created too many groups, we know the final answer must be between mid+1 and max. That's because we need fewer groups and the way to achieve fewer groups is to increase the allowed maximum sum in each group.

- 

  On the other hand, if the number of groups is too small, we know the final answer is between min and mid-1 because we need to increase the number of groups which means the target sum is something smaller than the one we used. This is actually also a possible answer assuming m is valid because you can always take any group and split it up to make more groups, so the mid value you targeted is at worst, higher than the real value.

- 

  On the third hand, if the number of groups is just right, we have a possible answer, so remember that answer. However, we should keep searching just in case there is a better answer. We're ultimately looking for smaller maximum sums, so the potentially better answer is between min and mid-1.

- 

  Repeat the process until there is nothing else to search. Then use the minimum value we found during the above process.

**Complexity Analysis**

- Time complexity : O(n * log(sum of array)). The binary search costs O(log(sum of array)), where `sum of array` is the sum of elements in `nums`. For each computation of `F(x)`, the time complexity is O(n) since we only need to go through the whole array.
- Space complexity : O(n). Same as the Brute Force approach. We only need the space to store the array.



### 222. Count Complete Tree Nodes - Medium

Given a **complete** binary tree, count the number of nodes.

**Note:**

**Definition of a complete binary tree from [Wikipedia](http://en.wikipedia.org/wiki/Binary_tree#Types_of_binary_trees):**
In a complete binary tree every level, except possibly the last, is completely filled, and all nodes in the last level are as far left as possible. It can have between 1 and 2h nodes inclusive at the last level h.

**Example:**

```
Input: 
    1
   / \
  2   3
 / \  /
4  5 6

Output: 6
```

#### Approach 1: Linear Time (Recursive)

**Intuition**

This problem is quite popular at Google during the last year. The naive solution here is a linear time one-liner which counts nodes recursively one by one.

**Implementation**

```java
class Solution {
  public int countNodes(TreeNode root) {
    return root != null ? 1 + countNodes(root.right) + countNodes(root.left) : 0;
  }
}
```

**Complexity Analysis**

- Time complexity : O(*N*).
- Space complexity : O(*d*)=O(log*N*) to keep the recursion stack, where d is a tree depth.

#### Approach 2: Binary search

**Intuition**

Approach 1 doesn't profit from the fact that the tree is a complete one.

> In a complete binary tree every level, except possibly the last, is completely filled, and all nodes in the last level are as far left as possible.

That means that complete tree has 2^k nodes in the kth level if the kth level is not the last one. The last level may be not filled completely, and hence in the last level the number of nodes could vary from 1 to 2^d, where d is a tree depth.

![fig](https://leetcode.com/problems/count-complete-tree-nodes/Figures/222/tree.png)

Now one could compute the number of nodes in all levels but the last one: ![image-20191109160234298](C:\Users\73995\AppData\Roaming\Typora\typora-user-images\image-20191109160234298.png) That reduces the problem to the simple check of how many nodes the tree has in the last level.

![fic](https://leetcode.com/problems/count-complete-tree-nodes/Figures/222/level.png)

Now there are two questions:

1. How many nodes in the last level have to be checked?
2. What is the best time performance for such a check?

Let's start from the first question. It's a complete tree, and hence all nodes in the last level are *as far left as possible*. That means that instead of checking the existence of all 2^d2*d* possible leafs, one could use binary search and check ![image-20191109162750685](C:\Users\73995\AppData\Roaming\Typora\typora-user-images\image-20191109162750685.png) leafs only.

![pic](https://leetcode.com/problems/count-complete-tree-nodes/Figures/222/exist.png)

Let's move to the second question, and enumerate potential nodes in the last level from 0 to 2^d - 12*d*−1. How to check if the node number idx exists? Let's use binary search again to reconstruct the sequence of moves from root to idx node. For example, idx = 4. idx is in the second half of nodes `0,1,2,3,4,5,6,7` and hence the first move is to the right. Then idx is in the first half of nodes `4,5,6,7` and hence the second move is to the left. The idx is in the first half of nodes `4,5` and hence the next move is to the left. The time complexity for one check is O(*d*).

![pif](https://leetcode.com/problems/count-complete-tree-nodes/Figures/222/check.png)

1 and 2 together result in O(*d*) checks, each check at a price of O(*d*). That means that the overall time complexity would be O(d^2).

**Algorithm**

- Return 0 if the tree is empty.
- Compute the tree depth `d`.
- Return 1 if `d == 0`.
- The number of nodes in all levels but the last one is 2^d -1. The number of nodes in the last level could vary from 1 to 2^d. Enumerate potential nodes from 0 to 2^d - 1 and perform the binary search by the node index to check how many nodes are in the last level. Use the function `exists(idx, d, root)` to check if the node with index idx exists.
- Use binary search to implement `exists(idx, d, root)` as well.
- Return 2^d - 1 + the number of nodes in the last level.

**Implementation**

```java
class Solution {
  // Return tree depth in O(d) time.
  public int computeDepth(TreeNode node) {
    int d = 0;
    while (node.left != null) {
      node = node.left;
      ++d;
    }
    return d;
  }

  // Last level nodes are enumerated from 0 to 2**d - 1 (left -> right).
  // Return True if last level node idx exists. 
  // Binary search with O(d) complexity.
  public boolean exists(int idx, int d, TreeNode node) {
    int left = 0, right = (int)Math.pow(2, d) - 1;
    int pivot;
    for(int i = 0; i < d; ++i) {
      pivot = left + (right - left) / 2;
      if (idx <= pivot) {
        node = node.left;
        right = pivot;
      }
      else {
        node = node.right;
        left = pivot + 1;
      }
    }
    return node != null;
  }

  public int countNodes(TreeNode root) {
    // if the tree is empty
    if (root == null) return 0;

    int d = computeDepth(root);
    // if the tree contains 1 node
    if (d == 0) return 1;

    // Last level nodes are enumerated from 0 to 2**d - 1 (left -> right).
    // Perform binary search to check how many nodes exist.
    int left = 1, right = (int)Math.pow(2, d) - 1;
    int pivot;
    while (left <= right) {
      pivot = left + (right - left) / 2;
      if (exists(pivot, d, root)) left = pivot + 1;
      else right = pivot - 1;
    }

    // The tree contains 2**d - 1 nodes on the first (d - 1) levels
    // and left nodes on the last level.
    return (int)Math.pow(2, d) - 1 + left;
  }
}
```

#### Approach 2 Revised:  Sum of most left nodes before last level +  countLastLevel (binary search)

**Intuition**

Basically my solution contains 2 steps.
(1) Firstly, we need to find the height of the binary tree and count the nodes above the last level.
(2) Then we should find a way to count the nodes on the last level.



Here I used a kind of binary search. We define the "midNode" of the last level as a node following the path "root->left->right->right->...->last level".



If midNode is null, then it means we should count the nodes on the last level in the left subtree.

If midNode is not null, then we add half of the last level nodes to our result and then count the nodes on the last level in the right subtree.



Of course I used some stop condition to make the code more efficient, e.g. when a tree has height 1, it means it only has 3 cases: 1. has right son; 2. only has left son; 3. has no son.

**Implementation**

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public int countNodes(TreeNode root) {
	    if (root == null) return 0;
	    if (root.left == null) return 1;
	    int height = 0;
        int nodesSum = 0;
	    TreeNode curr = root;
        while(curr.left != null) {
    	    nodesSum += (1 << height);
    	    height++;
    	    curr = curr.left;
        }
        return nodesSum + countLastLevel(root, height);
}

    private int countLastLevel(TreeNode root, int height) {
	    if(height == 1) 
		    if (root.right != null) return 2;
		    else if (root.left != null) return 1;
		    else return 0;
	    TreeNode midNode = root.left;
	    int currHeight = 1;
	    while(currHeight < height) {
		    currHeight++;
		    midNode = midNode.right;
	    }
	    if (midNode == null) return countLastLevel(root.left, height-1);
	    else return (1 << (height-1)) + countLastLevel(root.right, height-1);
    }
}
```

The number of nodes in the last level vary from `[1,2^d-1]`, binary search costs `log(2^d-1)=d`.
The traverse to find midNode costs `O(d)` time.
The whole time complexity is `O(log(2^d-1)*d)=O(d^2)`=`O((logN)^2)` 



### 1057. Campus Bikes - Medium

On a campus represented as a 2D grid, there are `N` workers and `M` bikes, with `N <= M`. Each worker and bike is a 2D coordinate on this grid.

Our goal is to assign a bike to each worker. Among the available bikes and workers, we choose the (worker, bike) pair with the shortest Manhattan distance between each other, and assign the bike to that worker. (If there are multiple (worker, bike) pairs with the same shortest Manhattan distance, we choose the pair with the smallest worker index; if there are multiple ways to do that, we choose the pair with the smallest bike index). We repeat this process until there are no available workers.

The Manhattan distance between two points `p1` and `p2` is `Manhattan(p1, p2) = |p1.x - p2.x| + |p1.y - p2.y|`.

Return a vector `ans` of length `N`, where `ans[i]` is the index (0-indexed) of the bike that the `i`-th worker is assigned to.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2019/03/06/1261_example_1_v2.png)

```
Input: workers = [[0,0],[2,1]], bikes = [[1,2],[3,3]]
Output: [1,0]
Explanation: 
Worker 1 grabs Bike 0 as they are closest (without ties), and Worker 0 is assigned Bike 1. So the output is [1, 0].
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2019/03/06/1261_example_2_v2.png)

```
Input: workers = [[0,0],[1,1],[2,0]], bikes = [[1,0],[2,2],[2,1]]
Output: [0,2,1]
Explanation: 
Worker 0 grabs Bike 0 at first. Worker 1 and Worker 2 share the same distance to Bike 2, thus Worker 1 is assigned to Bike 2, and Worker 2 will take Bike 1. So the output is [0,2,1].
```

 

**Note:**

1. `0 <= workers[i][j], bikes[i][j] < 1000`
2. All worker and bike locations are distinct.
3. `1 <= workers.length <= bikes.length <= 1000`

**Algorithm**

We are able to solve this question using a greedy approach.



1. initiate a priority queue of bike and worker pairs. The heap order should be `Distance ASC, WorkerIndex ASC, Bike ASC`
2. Loop through all workers and bikes, calculate their distance, and then throw it to the queue.
3. Initiate a set to keep track of the bikes that have been assigned.
4. initiate a result array and fill it with `-1`. (unassigned)
5. poll every possible pair from the priority queue and check if the person already got his bike or the bike has been assigned.
6. early exist on every people got their bike.

#### Approach 1: Priority Queue

**Implementation**

```java
class Solution {
   public int[] assignBikes(int[][] workers, int[][] bikes) {
        int n = workers.length;
        // order by Distance ASC, WorkerIndex ASC, BikeIndex ASC
        PriorityQueue<int[]> q = new PriorityQueue<int[]>((a, b) -> {
            int comp = Integer.compare(a[0], b[0]);
            if (comp == 0) {
                if (a[1] == b[1]) {
                    return Integer.compare(a[2], b[2]);
                }
                return Integer.compare(a[1], b[1]);
            }
            
            return comp;
        });
            
        // loop through every possible pairs of bikes and people,
        // calculate their distance, and then throw it to the pq.
        for (int i = 0; i < workers.length; i++) {  
            int[] worker = workers[i];
            for (int j = 0; j < bikes.length; j++) {
                int[] bike = bikes[j];
                int dist = Math.abs(bike[0] - worker[0]) + Math.abs(bike[1] - worker[1]);
                q.add(new int[]{dist, i, j}); 
            }
        }
        
        // init the result array with state of 'unvisited'.
        int[] res = new int[n];
        Arrays.fill(res, -1);
        
        // assign the bikes.
        Set<Integer> bikeAssigned = new HashSet<>();
        while (bikeAssigned.size() < n) {
            int[] workerAndBikePair = q.poll();
            if (res[workerAndBikePair[1]] == -1 
                && !bikeAssigned.contains(workerAndBikePair[2])) {   
                res[workerAndBikePair[1]] = workerAndBikePair[2];
                bikeAssigned.add(workerAndBikePair[2]);
            }
        }
        
        return res;
    }
    
}
```

**Complexity Analysis**

- Time complexity : O(N * M+N * logN). (not sure)
- Space complexity : O(*N*^2 * M).

#### Approach 2: Counting Sort

**Algorithm**

 Need to calculate distance between each worker and bike, then sort them asc and iterate in a greedy manner. Because we're starting from the shortest distance we can fill the resulting array as we go. It will work with priority queue, but slow due to sorting.

Catch is the limit of possible distances. It's just 2000, so we can use counting sort.

Complexity is O(m * n) time needed to fill the array of distances. Same for space - need to store every distance, plus array or seen bikes but this is minor compare to distance array. 

**Implementation**

```java
 public int[] assignBikes(int[][] workers, int[][] bikes) {
    //this array will hold pairs sorted by distance - index in array is distance and list of ints
    //is the list of pairs of worker-bike that have this distance
    ArrayList<int[]>[] dist = new ArrayList[2001];
    int wlen = workers.length;
    int blen = bikes.length;
    //this will hold the resutl array
    int[] res = new int[wlen];
    //this is for us to check if bike has been used
    boolean[] seenBikes = new boolean[blen];
    //fill result array with -1 so that we can identify worker that hasn't been processed
    Arrays.fill(res, -1);

    //fill array of distances with pairs of bike-worker. Our "for" loops start with bike and then
    //with worker. Because of this the condition of the problem has been satisfied - worker with smaller index 
	//will be always first, then bikes
    for (int w = 0; w < wlen; w++) {
      for (int b = 0; b < blen; b++) {
        int d = Math.abs(workers[w][0] - bikes[b][0]) + Math.abs(workers[w][1] - bikes[b][1]);
        if (dist[d] == null) {
          dist[d] = new ArrayList();
        }
        dist[d].add(new int[] {b, w});
      }
    }

    //this counts how many workers has been assigned so far
    int c = 0;

    for (int d = 0; d <= 2000; d++) {
      if (c == blen)
        break;
      //if we haven't met any pair with this distance
      if (dist[d] == null) continue;
      for (int[] pair : dist[d]) {
        //check if worker hasn't been assigned yet and if bike hasn't been used
        if (res[pair[1]] == -1 && !seenBikes[pair[0]]) {
          res[pair[1]] = pair[0];
          seenBikes[pair[0]] = true;
          //increment count of workers with bikes and check if need to break from loop
          c++;
          if (c == blen)
            break;
        }
      }
    }
    return res;
  }
```



### 1170. Compare Strings by Frequency of the Smallest Character - Easy

Let's define a function `f(s)` over a non-empty string `s`, which calculates the frequency of the smallest character in `s`. For example, if `s = "dcce"` then `f(s) = 2` because the smallest character is `"c"` and its frequency is 2.

Now, given string arrays `queries` and `words`, return an integer array `answer`, where each `answer[i]` is the number of words such that `f(queries[i])` < `f(W)`, where `W` is a word in `words`.

 

**Example 1:**

```
Input: queries = ["cbd"], words = ["zaaaz"]
Output: [1]
Explanation: On the first query we have f("cbd") = 1, f("zaaaz") = 3 so f("cbd") < f("zaaaz").
```

**Example 2:**

```
Input: queries = ["bbb","cc"], words = ["a","aa","aaa","aaaa"]
Output: [1,2]
Explanation: On the first query only f("bbb") < f("aaaa"). On the second query both f("aaa") and f("aaaa") are both > f("cc").
```

 

**Constraints:**

- `1 <= queries.length <= 2000`
- `1 <= words.length <= 2000`
- `1 <= queries[i].length, words[i].length <= 10`
- `queries[i][j]`, `words[i][j]` are English lowercase letters.



My solution:

```java
class Solution {
    public int[] numSmallerByFrequency(String[] queries, String[] words) {
        if (queries.length == 0 || words.length == 0) return null;
        int[] res = new int[queries.length];
        for (int i = 0; i < queries.length; i++) {
            int count = 0;
            String s = queries[i];
            for (int j = 0; j < words.length; j++) {
                if (countMinChar(s) < countMinChar(words[j])) count++;
            }
            res[i] = count;
        }
        return res;
    }
    public int countMinChar(String s) {
        if (s.length() == 0) return 0;
        char[] arr = s.toCharArray();
        Arrays.sort(arr);
        int count = 0;
        for (int i = 0; i < arr.length; i++) {
            if (arr[i] != arr[0]) break;
            else count++;
        }
        return count;
    }
}
```

Analysis of Complexity:

- Time: O(N * M)
- Space: O(N)

Runtime: 912 ms, faster than 5.01% of Java online submissions for Compare Strings by Frequency of the Smallest Character.

Memory Usage: 39.1 MB, less than 100.00% of Java online submissions for Compare Strings by Frequency of the Smallest Character.

#### Approach: Binary Search

```java
public int[] numSmallerByFrequency(String[] queries, String[] words) {
        int[] q = new int[queries.length], w = new int[words.length];
        for (int i = 0; i < q.length; i++) {
            q[i] = jisuan(queries[i]);
        }
        for (int i = 0; i < w.length; i++) {
            w[i] = jisuan(words[i]);
        }
        Arrays.sort(w);
        int[] ans = new int[q.length];
        for (int i = 0; i < q.length; i++) {
            int l = 0, r = w.length - 1;
            while (l <= r) {
                int mid = (l + r) / 2;
                if (w[mid] <= q[i]) {
                    l = mid + 1;
                }
                else {
                    r = mid - 1;
                }
            }
            ans[i] = w.length - l;
        }
        return ans;
    }
    
    private int jisuan(String str) {
        int[] arr = new int[26];
        for (char ch : str.toCharArray())
            arr[ch - 'a']++;
        for (int i = 0; i < 26; i++) {
            if (arr[i] != 0)
                return arr[i];
        }
        return 0;
    }
```



### 278.  First Bad Version - Easy

You are a product manager and currently leading a team to develop a new product. Unfortunately, the latest version of your product fails the quality check. Since each version is developed based on the previous version, all the versions after a bad version are also bad.

Suppose you have `n` versions `[1, 2, ..., n]` and you want to find out the first bad one, which causes all the following ones to be bad.

You are given an API `bool isBadVersion(version)` which will return whether `version` is bad. Implement a function to find the first bad version. You should minimize the number of calls to the API.

**Example:**

```
Given n = 5, and version = 4 is the first bad version.

call isBadVersion(3) -> false
call isBadVersion(5) -> true
call isBadVersion(4) -> true

Then 4 is the first bad version. 
```

```java
/* The isBadVersion API is defined in the parent class VersionControl.
      boolean isBadVersion(int version); */

public class Solution extends VersionControl {
    public int firstBadVersion(int n) {
        int start = 1, end = n;
        while (start < end) {
            int mid = start + (end-start) / 2;
            if (!isBadVersion(mid)) start = mid + 1;
            else end = mid;            
        }        
     return start;
    }
}
```

