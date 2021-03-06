# Greedy

### 763. Partition Labels - Medium

A string `S` of lowercase English letters is given. We want to partition this string into as many parts as possible so that each letter appears in at most one part, and return a list of integers representing the size of these parts.

**Example 1:**

```
Input: S = "ababcbacadefegdehijhklij"
Output: [9,7,8]
Explanation:
The partition is "ababcbaca", "defegde", "hijhklij".
This is a partition so that each letter appears in at most one part.
A partition like "ababcbacadefegde", "hijhklij" is incorrect, because it splits S into less parts.
```

**Note:**

- `S` will have length in range `[1, 500]`.
- `S` will consist of lowercase English letters (`'a'` to `'z'`) only.

#### Implementation

```java
class Solution {
    public List<Integer> partitionLabels(String S) {
        // map characters with their LAST indexes
        int[] last = new int[26];
        for (int i = 0; i < S.length(); ++i)
            last[S.charAt(i) - 'a'] = i;
        
        int j = 0; // last index of one character
        int anchor = 0; // start of a new partition
        List<Integer> ans = new ArrayList();
        for (int i = 0; i < S.length(); i++) {
            // expand the last index
            j = Math.max(j, last[S.charAt(i) - 'a']);
            if (i == j) {
                ans.add(i - anchor + 1);
                anchor = i + 1;
            }
        }
        return ans;
    }
}
```

**Complexity Analysis**

- Time Complexity: *O*(*N*), where *N* is the length of *S*.
- Space Complexity: *O(1)* to keep data structure `last` of not more than 26 characters.

