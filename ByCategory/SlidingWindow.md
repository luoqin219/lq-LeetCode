### 3. Longest Substring Without Repeating Characters - Medium

Given a string, find the length of the **longest substring** without repeating characters.

**Example 1:**

```
Input: "abcabcbb"
Output: 3 
Explanation: The answer is "abc", with the length of 3. 
```

**Example 2:**

```
Input: "bbbbb"
Output: 1
Explanation: The answer is "b", with the length of 1.
```

**Example 3:**

```
Input: "pwwkew"
Output: 3
Explanation: The answer is "wke", with the length of 3. 
             Note that the answer must be a substring, "pwke" is a subsequence and not a substring.
```

#### Implementation - HashSet and range [left, right]

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        HashSet<Character> set = new HashSet<>();
        int length = 0, left = 0, right = 0;
        while (left < s.length() && right < s.length()) {
            if (!set.contains(s.charAt(right))) {
                set.add(s.charAt(right++));
                length = Math.max(length, right-left);
            } else {
                set.remove(s.charAt(left++));
            }
        }
        return length;
    }
}
```

**Complexity Analysis**

- Time complexity : O(2n) = O(n). In the worst case each character will be visited twice by *i* and *j*.
- Space complexity : O(min(m, n)). We need O(k) space for the sliding window, where k is the size of the `Set`. The size of the Set is upper bounded by the size of the string *n* and the size of the charset/alphabet *m*.

#### Approach 2: Sliding Window Optimized

The above solution requires at most 2n steps. In fact, it could be optimized to require only n steps. Instead of using a set to tell if a character exists or not, we could define a mapping of the characters to its index. Then we can skip the characters immediately when we found a repeated character.

The reason is that if s[j] has a duplicate in the range [i, j) with index j′, we don't need to increase i little by little. We can skip all the elements in the range [i, j'][*i*,*j*′] and let i to be j' + 1 directly.

```java
public class Solution {
    public int lengthOfLongestSubstring(String s) {
        int n = s.length(), ans = 0;
        Map<Character, Integer> map = new HashMap<>(); // current index of character
        // try to extend the range [i, j]
        for (int j = 0, i = 0; j < n; j++) {
            if (map.containsKey(s.charAt(j))) {
                i = Math.max(map.get(s.charAt(j)), i);
            }
            ans = Math.max(ans, j - i + 1);
            map.put(s.charAt(j), j + 1);
        }
        return ans;
    }
}
```

**Complexity Analysis**

- Time complexity: O(*n*log*n*+*m*log*m*), where *n* and *m* are the lengths of the arrays. We sort two arrays independently, and then do a linear scan.
- Space complexity: O(1). We sort the arrays in-place. We ignore the space to store the output as it is not essential to the algorithm itself.

##### Further:

**Java (Assuming ASCII 128)**

The previous implements all have no assumption on the charset of the string `s`.

If we know that the charset is rather small, we can replace the `Map` with an integer array as direct access table.

Commonly used tables are:

- `int[26]` for Letters 'a' - 'z' or 'A' - 'Z'
- `int[128]` for ASCII
- `int[256]` for Extended ASCII

```java
public class Solution {
    public int lengthOfLongestSubstring(String s) {
        int n = s.length(), ans = 0;
        int[] index = new int[128]; // current index of character
        // try to extend the range [i, j]
        for (int j = 0, i = 0; j < n; j++) {
            i = Math.max(index[s.charAt(j)], i);
            ans = Math.max(ans, j - i + 1);
            index[s.charAt(j)] = j + 1;
        }
        return ans;
    }
}
```

**Complexity Analysis**

- Time complexity : O(n). Index j will iterate n*n* times.
- Space complexity (HashMap) : O(min(m, n)). Same as the previous approach.
- Space complexity (Table): O(m). *m* is the size of the charset.

