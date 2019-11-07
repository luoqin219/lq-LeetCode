# Google Interview Prep

###  482. License Key Formatting 

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

###  15. 3Sum

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

Space complexity: O(n)

