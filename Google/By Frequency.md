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
        return sb.reverse().toString();
    }
}
```

Runtime: 13 ms, faster than 77.68% of Java online submissions for License Key Formatting.

Memory Usage: 36.8 MB, less than 100.00% of Java online submissions for License Key Formatting.

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
- Your algorithm should run in O(*n2*) complexity.

**Follow up:** Could you improve it to O(*n* log *n*) time complexity?



#### O(nlog(n)) Algorithms using DP

```java
// using Arrays
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

