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



### 76. Minimum Window Substring - Hard

Given a string S and a string T, find the minimum window in S which will contain all the characters in T in complexity O(n).

**Example:**

```
Input: S = "ADOBECODEBANC", T = "ABC"
Output: "BANC"
```

**Note:**

- If there is no such window in S that covers all characters in T, return the empty string `""`.
- If there is such window, you are guaranteed that there will always be only one unique minimum window in S.

**Intuition**

The question asks us to return the minimum window from the string S*S* which has all the characters of the string T*T*. Let us call a window `desirable` if it has all the characters from T*T*.

We can use a simple sliding window approach to solve this problem.

In any sliding window based problem we have two pointers. One right*r**i**g**h**t* pointer whose job is to expand the current window and then we have the left*l**e**f**t* pointer whose job is to contract a given window. At any point in time only one of these pointers move and the other one remains fixed.

The solution is pretty intuitive. We keep expanding the window by moving the right pointer. When the window has all the desired characters, we contract (if possible) and save the smallest window till now.

The answer is the smallest desirable window.

For eg. `S = "ABAACBAB" T = "ABC"`. Then our answer window is `"ACB"` and shown below is one of the possible desirable windows.

<img src="https://leetcode.com/problems/minimum-window-substring/Figures/76/76_Minimum_Window_Substring_1.png" alt="img" style="zoom:50%;" />



**Algorithm**

1. We start with two pointers, left and right initially pointing to the first element of the string S.
2. We use the right pointer to expand the window until we get a desirable window i.e. a window that contains all of the characters of T.
3. Once we have a window with all the characters, we can move the left pointer ahead one by one. If the window is still a desirable one we keep on updating the minimum window size.
4. If the window is not desirable any more, we repeat step 2 onwards.

<img src="https://leetcode.com/problems/minimum-window-substring/Figures/76/76_Minimum_Window_Substring_2.png" alt="img" style="zoom:50%;" />

The above steps are repeated until we have looked at all the windows. The smallest window is returned.

<img src="https://leetcode.com/problems/minimum-window-substring/Figures/76/76_Minimum_Window_Substring_3.png" alt="img" style="zoom:50%;" />

#### Implementation - two HashMaps and range [left, right]

```java
class Solution {
  public String minWindow(String s, String t) {

      if (s.length() == 0 || t.length() == 0) {
          return "";
      }

      // Dictionary which keeps a count of all the unique characters in t.
      Map<Character, Integer> dictT = new HashMap<Character, Integer>();
      for (int i = 0; i < t.length(); i++) {
          dictT.put(t.charAt(i), dictT.getOrDefault(t.charAt(i), 0) + 1);
      }

      // Number of unique characters in t, which need to be present in the desired window.
      int required = dictT.size();

      // Left and Right pointer
      int l = 0, r = 0;

      // formed is used to keep track of how many unique characters in t
      // are present in the current window in its desired frequency.
      // e.g. if t is "AABC" then the window must have two A's, one B and one C.
      // Thus formed would be = 3 when all these conditions are met.
      int formed = 0;

      // Dictionary which keeps a count of all the unique characters in the current window.
      Map<Character, Integer> windowCounts = new HashMap<Character, Integer>();

      // ans list of the form (window length, left, right)
      int[] ans = {-1, 0, 0};

      while (r < s.length()) {
          // Add one character from the right to the window
          char c = s.charAt(r);
          int count = windowCounts.getOrDefault(c, 0);
          windowCounts.put(c, count + 1);

          // If the frequency of the current character added equals to the
          // desired count in t then increment the formed count by 1.
          if (dictT.containsKey(c) && windowCounts.get(c).intValue() == dictT.get(c).intValue()) {
              formed++;
          }

          // Try and contract the window till the point where it ceases to be 'desirable'.
          while (l <= r && formed == required) {
              c = s.charAt(l);
              // Save the smallest window until now.
              if (ans[0] == -1 || r - l + 1 < ans[0]) {
                  ans[0] = r - l + 1;
                  ans[1] = l;
                  ans[2] = r;
              }

              // The character at the position pointed by the
              // `Left` pointer is no longer a part of the window.
              windowCounts.put(c, windowCounts.get(c) - 1);
              if (dictT.containsKey(c) && windowCounts.get(c).intValue() < dictT.get(c).intValue()) {
                  formed--;
              }

              // Move the left pointer ahead, this would help to look for a new window.
              l++;
          }

          // Keep expanding the window once we are done contracting.
          r++;   
      }

      return ans[0] == -1 ? "" : s.substring(ans[1], ans[2] + 1);
  }
}
```

**Complexity Analysis**

- Time Complexity: O(|S| + |T|)where |S| and |T| represent the lengths of strings S and T. In the worst case we might end up visiting every element of string S*S* twice, once by left pointer and once by right pointer. |T|represents the length of string T.
- Space Complexity: O(|S| + |T|). |S| when the window size is equal to the entire string S*S*. |T|when T has all unique characters.

#### Approach 2: Sliding Window Optimized

**Intuition**

A small improvement to the above approach can reduce the time complexity of the algorithm to O(2*|filtered\_S| + |S| + |T|), where *filtered\_S* is the string formed from S by removing all the elements not present in T.

This complexity reduction is evident when |filtered\_S| <<< |S|.

This kind of scenario might happen when length of string T is way too small than the length of string S and string S consists of numerous characters which are not present in T.

**Algorithm**

We create a list called filtered\_S which has all the characters from string S along with their indices in S, but these characters should be present in T.

```
  S = "ABCDDDDDDEEAFFBC" T = "ABC"
  filtered_S = [(0, 'A'), (1, 'B'), (2, 'C'), (11, 'A'), (14, 'B'), (15, 'C')]
  Here (0, 'A') means in string S character A is at index 0.
```

We can now follow our sliding window approach on the smaller string filtered\_S.

```java
class Solution {
    public String minWindow(String s, String t) {

        if (s.length() == 0 || t.length() == 0) {
            return "";
        }

        Map<Character, Integer> dictT = new HashMap<Character, Integer>();

        for (int i = 0; i < t.length(); i++) {
            int count = dictT.getOrDefault(t.charAt(i), 0);
            dictT.put(t.charAt(i), count + 1);
        }

        int required = dictT.size();

        // Filter all the characters from s into a new list along with their index.
        // The filtering criteria is that the character should be present in t.
        List<Pair<Integer, Character>> filteredS = new ArrayList<Pair<Integer, Character>>();
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if (dictT.containsKey(c)) {
                filteredS.add(new Pair<Integer, Character>(i, c));
            }
        }

        int l = 0, r = 0, formed = 0;
        Map<Character, Integer> windowCounts = new HashMap<Character, Integer>();  
        int[] ans = {-1, 0, 0};

        // Look for the characters only in the filtered list instead of entire s.
        // This helps to reduce our search.
        // Hence, we follow the sliding window approach on as small list.
        while (r < filteredS.size()) {
            char c = filteredS.get(r).getValue();
            int count = windowCounts.getOrDefault(c, 0);
            windowCounts.put(c, count + 1);

            if (dictT.containsKey(c) && windowCounts.get(c).intValue() == dictT.get(c).intValue()) {
                formed++;
            }

            // Try and contract the window till the point where it ceases to be 'desirable'.
            while (l <= r && formed == required) {
                c = filteredS.get(l).getValue();

                // Save the smallest window until now.
                int end = filteredS.get(r).getKey();
                int start = filteredS.get(l).getKey();
                if (ans[0] == -1 || end - start + 1 < ans[0]) {
                    ans[0] = end - start + 1;
                    ans[1] = start;
                    ans[2] = end;
                }

                windowCounts.put(c, windowCounts.get(c) - 1);
                if (dictT.containsKey(c) && windowCounts.get(c).intValue() < dictT.get(c).intValue()) {
                    formed--;
                }
                l++;
            }
            r++;   
        }
        return ans[0] == -1 ? "" : s.substring(ans[1], ans[2] + 1);
    }
}
```

**Complexity Analysis**

- Time Complexity : O(|S| + |T|) where |S| and |T| represent the lengths of strings S*S* and T*T*. The complexity is same as the previous approach. But in certain cases where |filtered\_S| <<< |S|, the complexity would reduce because the number of iterations would be 2*|filtered\_S| + |S| + |T|.
- Space Complexity : O(|S| + |T|).

