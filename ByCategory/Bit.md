# Bit Manipulation

### 67. Add Binary

Given two binary strings, return their sum (also a binary string).

The input strings are both **non-empty** and contains only characters `1` or `0`.

**Example 1:**

```
Input: a = "11", b = "1"
Output: "100"
```

**Example 2:**

```
Input: a = "1010", b = "1011"
Output: "10101"
```

**Constraints:**

- Each string consists only of `'0'` or `'1'` characters.
- `1 <= a.length, b.length <= 10^4`
- Each string is either `"0"` or doesn't contain any leading zero.

#### Approach 1: Use Built-in Method

**Algorithm**

- Convert a and b into integers.
- Compute the sum.
- Convert the sum back into binary form.

```java
class Solution {
  public String addBinary(String a, String b) {
    return Integer.toBinaryString(Integer.parseInt(a, 2) + Integer.parseInt(b, 2));
  }
}
```

**Complexity Analysis**

Time: O(N+M)

*Drawbacks*:

1.  In Java this approach is limited by the length of the input strings a and b. Once the string is long enough, the result of conversion into integers will not fit into Integer, Long or BigInteger:
   - 33 1-bits - and b doesn't fit into Integer.
   - 65 1-bits - and b doesn't fit into Long.
   - [500000001](https://docs.oracle.com/javase/8/docs/api/java/math/BigInteger.html) 1-bits - and b doesn't fit into BigInteger.
2.  This method has quite low performance in the case of large input numbers.

#### Approach 2: Bit -by-bit Computation

**Algorithm**

That's a good old classical algorithm, and there is no conversion from binary string to decimal and back here. Let's consider the numbers bit by bit starting from the lowest one and compute the carry this bit will add.

Start from carry = 0. If number a has 1-bit in this lowest bit, add 1 to the carry. The same for number b: if number b has 1-bit in the lowest bit, add 1 to the carry. At this point the carry for the lowest bit could be equal to (00)_2(00)2, (01)_2(01)2, or (10)_2(10)2.

Now append the lowest bit of the carry to the answer, and move the highest bit of the carry to the next order bit.

Repeat the same steps again, and again, till all bits in a and b are used up. If there is still nonzero carry to add, add it. Now reverse the answer string and the job is done.

#### Implementation

```java
class Solution {
  public String addBinary(String a, String b) {
    int n = a.length(), m = b.length();
    if (n < m) return addBinary(b, a);
    int L = Math.max(n, m);

    StringBuilder sb = new StringBuilder();
    int carry = 0, j = m - 1;
    for(int i = L - 1; i > -1; i--) {
      if (a.charAt(i) == '1') carry++;
      if (j > -1 && b.charAt(j--) == '1') carry++;

      if (carry % 2 == 1) sb.append('1');
      else sb.append('0');

      carry /= 2;
    }
    if (carry == 1) sb.append('1');
    sb.reverse();

    return sb.toString();
  }
}
```

**Complexity Analysis**

- Time complexity: O(max(*N*,*M*)), where N and M are lengths of the input strings a and b.
- Space complexity: O(max(*N*,*M*)) to keep the answer.

#### Apporach 3: Bit Manipulation

When interviewer provides you two numbers and asks to sum them up without using addition operation, we have to use bit manipulation.

Here XOR is a key as well, because it's a sum of two binaries without taking carry into account.

![fig](https://leetcode.com/problems/add-binary/Figures/67/xor4.png)

To find current carry is quite easy as well, it's AND of two input numbers, shifted one bit to the left.

![fig](https://leetcode.com/problems/add-binary/Figures/67/carry2.png)

Now the problem is reduced: one has to find the sum of answer without carry and carry. It's the same problem - to sum two numbers, and hence one could solve it in **a loop with the condition statement "while carry is not equal to zero"**.

**Algorithm**

- Convert a and b into integers x and y, x will be used to keep an answer, and y for the carry.
- While carry is nonzero: `y != 0`:
  - Current answer without carry is XOR of x and y: `answer = x^y`.
  - Current carry is left-shifted AND of x and y: `carry = (x & y) << 1`.
  - Job is done, prepare the next loop: `x = answer`, `y = carry`.
- Return x in the binary form.

#### Implementation

```java
import java.math.BigInteger;
class Solution {
  public String addBinary(String a, String b) {
    BigInteger x = new BigInteger(a, 2);
    BigInteger y = new BigInteger(b, 2);
    BigInteger zero = new BigInteger("0", 2);
    BigInteger carry, answer;
    while (y.compareTo(zero) != 0) {
      answer = x.xor(y);
      carry = x.and(y).shiftLeft(1);
      x = answer;
      y = carry;
    }
    return x.toString(2);
  }
}
```

**Performance Discussion**

Here we deal with input numbers which are greater than 2^{100}2100. That forces to use slow [BigInteger](https://docs.oracle.com/javase/8/docs/api/java/math/BigInteger.html) in Java, and hence the performance gain will be present for the Python solution only. Provided here Java solution could make its best with Integers or Longs, but not with BigIntegers.

**Complexity Analysis**

- Time complexity : \mathcal{O}(N + M)O(*N*+*M*), where N*N* and M*M* are lengths of the input strings a and b.
- Space complexity : \mathcal{O}(\max(N, M))O(max(*N*,*M*)) to keep the answer.



### 421. Maximum XOR of Two Numbers in an Array - Medium

Given a **non-empty** array of numbers, a0, a1, a2, … , an-1, where 0 ≤ ai < 231.

Find the maximum result of ai XOR aj, where 0 ≤ *i*, *j* < *n*.

Could you do this in O(*n*) runtime?

**Example:**

```
Input: [3, 10, 5, 25, 2, 8]

Output: 28

Explanation: The maximum result is 5 ^ 25 = 28.
```

#### Approach 1: Bitwise Prefixes in HashSet

- Let's start from rewriting all numbers `[3, 10, 5, 25, 2, 8]` in binary from

  3=(00011)2

  10=(01010)2

  5=(00101)2

  25=(11001)2

  2=(00010)2

  8=(01000)2

  To simplify the work with prefixes, better to use the same number of bits L*L* for all the numbers. It's enough to take *L* equal to the length of the max number in the binary representation.

  **Algorithm**

  - Compute the number of bits L to be used. It's a length of max number in binary representation.
  - Initiate `max_xor = 0`.
  - Loop from i = L - 1 down to i = 0 (from the leftmost bit L - 1 to the rightmost bit 0):
    - Left shift the `max_xor` to free the next bit.
    - Initiate variable `curr_xor = max_xor | 1` by setting 1 in the rightmost bit of `max_xor`. Now let's check if `curr_xor` could be done using available prefixes.
    - Compute all possible prefixes of length L - i by iterating over `nums`.
      - Put in the hashset `prefixes` the prefix of the current number of the length L - i: `num >> i`.
    - Iterate over all prefixes and check if `curr_xor` could be done using two of them: `p1^p2 == curr_xor`. Using self-inverse property of XOR `p1^p2^p2 = p1`, one could rewrite it as `p1 == curr_xor^p2` and simply check for each `p` if `curr_xor^p` is in prefixes. If so, set `max_xor` to be equal to `curr_xor`, i.e. set 1-bit in the rightmost bit. Otherwise, let `max_xor` keep 0-bit in the rightmost bit.
  - Return `max_xor`.

#### Implementation

```java
class Solution {
  public int findMaximumXOR(int[] nums) {
    int maxNum = nums[0];
    for (int num: nums) maxNum = Math.max(maxNum, num);
    // length of max number in a binary representation
    int L = (Integer.toBinaryString(maxNum)).length();

    int maxXor = 0, currXor;
    Set<Integer> prefixes = new HashSet<>();
    for (int i = L - 1; i > -1; --i) {
      // go to the next bit by the left shift
      maxXor <<= 1;
      // set 1 in the smallest bit
      currXor = maxXor | 1;
      prefixes.clear();
      // compute all possible prefixes 
      // of length (L - i) in binary representation
      for (int num: nums) prefixes.add(num >> i);
      // Update maxXor, if two of these prefixes could result in currXor.
      // Check if p1^p2 == currXor, i.e. p1 == currXor^p2.
      for (int p: prefixes) {
        if (prefixes.contains(currXor^p)) {
          maxXor = currXor;
          break;
        }
      }
    }
    return maxXor;
  }
}
```

**Complexity Analysis**

<img src="/Users/qinluo/Library/Application Support/typora-user-images/image-20200805160939400.png" alt="image-20200805160939400" style="zoom:150%;" />

#### Appoach 2: Bitwise Trie

Note that the appoach 1 which used HashSet structure does not provide the functionality to cut off some paths which don't lead to the solution.

For example, after two steps of max XOR computation (11***)2, it's quite obvious that 25 should be paired with 00 prefix, i.e. with 2, 3, or 5.

The standard way is to use [Bitwise Trie](https://en.wikipedia.org/wiki/Trie#Bitwise_tries). It's a special type of [Trie](https://leetcode.com/articles/word-search-ii/), which is used to store binary prefixes in an efficient way. There are plenty of real-life examples of bitwise trie usage, for example, [in GCC](https://gcc.gnu.org/onlinedocs/libstdc++/ext/pb_ds/trie_based_containers.html).

Let's start with Bitwise Trie for the array `[3, 10, 5, 25, 2]`

3=(00011)2

10=(01010)2

5=(00101)2

25=(11001)2

2=(00010)2

![fig](https://leetcode.com/problems/maximum-xor-of-two-numbers-in-an-array/Figures/421/trie.png)

Each root -> leaf path in Bitwise Trie represents a binary form of a number in nums, for example, 0 -> 0 -> 0 -> 1 -> 1 is 3. As before, the same number of bits L is used for all numbers, and L*=1+[log2*M*]*, where M is a maximum number in nums. The depth of Bitwise Trie is equal to *L* as well, and all leafs are on the same level.

Bitwise Trie is a perfect way to see how different the binary forms of numbers are, for example, 3 and 2 share 4 bits of 5. The construction of Bitwise Trie is pretty straightforward, it's basically nested hashmaps. At each step one has to verify, if the child node to add (0 or 1) is already present. If yes, just go one step down. If not, add it into the Trie and then go one step down.

<iframe src="https://leetcode.com/playground/Wa5wWTzi/shared" frameborder="0" width="100%" height="276" name="Wa5wWTzi" style="box-sizing: border-box; margin: 20px 0px; color: rgba(0, 0, 0, 0.65); font-family: -apple-system, system-ui, &quot;Segoe UI&quot;, &quot;PingFang SC&quot;, &quot;Hiragino Sans GB&quot;, &quot;Microsoft YaHei&quot;, &quot;Helvetica Neue&quot;, Helvetica, Arial, sans-serif, &quot;Apple Color Emoji&quot;, &quot;Segoe UI Emoji&quot;, &quot;Segoe UI Symbol&quot;; font-size: 14px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; background-color: rgb(255, 255, 255); text-decoration-style: initial; text-decoration-color: initial;"></iframe>

**Maximum XOR of a Given Number with All Numbers in Trie**

Now the Trie is constructed, so let's find the maximum XOR of a given number with all numbers that have been already inserted into Bitwise Trie.

**To maximize XOR, the strategy is to choose the opposite bit at each step whenever it's possible.** Step by step for 25 as a given number:

![fig](https://leetcode.com/problems/maximum-xor-of-two-numbers-in-an-array/Figures/421/max_xor.png)

The implementation is also pretty simple:

- Try to go down to the opposite bit at each step if it's possible. Add 1-bit at the end of current XOR.
- If not, just go down to the same bit. Add 0-bit at the end of current XOR.

<iframe src="https://leetcode.com/playground/Xc7uoNBd/shared" frameborder="0" width="100%" height="310" name="Xc7uoNBd" style="box-sizing: border-box; margin: 20px 0px; color: rgba(0, 0, 0, 0.65); font-family: -apple-system, system-ui, &quot;Segoe UI&quot;, &quot;PingFang SC&quot;, &quot;Hiragino Sans GB&quot;, &quot;Microsoft YaHei&quot;, &quot;Helvetica Neue&quot;, Helvetica, Arial, sans-serif, &quot;Apple Color Emoji&quot;, &quot;Segoe UI Emoji&quot;, &quot;Segoe UI Symbol&quot;; font-size: 14px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; background-color: rgb(255, 255, 255); text-decoration-style: initial; text-decoration-color: initial;"></iframe>

**Algorithm**

To summarize, now one could

- Insert a number into Bitwise Trie.
- Find maximum XOR of a given number with all numbers that have been inserted so far.

That's all one needs to solve the initial problem:

- Convert all numbers to the binary form.
- Add the numbers into Trie one by one and compute the maximum XOR of a number to add with all previously inserted. Update maximum XOR at each step.
- Return `max_xor`.

#### Implementation

```java
class TrieNode {
  HashMap<Character, TrieNode> children = new HashMap<Character, TrieNode>();
  public TrieNode() {}
}

class Solution {
  public int findMaximumXOR(int[] nums) {
    // Compute length L of max number in a binary representation
    int maxNum = nums[0];
    for(int num : nums) maxNum = Math.max(maxNum, num);
    int L = (Integer.toBinaryString(maxNum)).length();

    // zero left-padding to ensure L bits for each number
    int n = nums.length, bitmask = 1 << L;
    String[] strNums = new String[n];
    for(int i = 0; i < n; ++i) {
        // String substring(int beginIndex)
      strNums[i] = Integer.toBinaryString(bitmask | nums[i]).substring(1);
    }

    TrieNode trie = new TrieNode();
    int maxXor = 0;
    for (String num: strNums) {
      TrieNode node = trie, xorNode = trie;
      int currXor = 0;
      for (Character bit : num.toCharArray()) {
        // insert new number in trie  
        if (node.children.containsKey(bit)) {
          node = node.children.get(bit);
        } else {
          TrieNode newNode = new TrieNode();
          node.children.put(bit, newNode);
          node = newNode;
        }

        // compute max xor of that new number 
        // with all previously inserted
        Character toggledBit = bit == '1' ? '0' : '1';
        if (xorNode.children.containsKey(toggledBit)) {
          currXor = (currXor << 1) | 1;
          xorNode = xorNode.children.get(toggledBit);
        } else {
          currXor = currXor << 1;
          xorNode = xorNode.children.get(bit);
        }
      }
      maxXor = Math.max(maxXor, currXor);
    }

    return maxXor;
  }
}
```

**Complexity Analysis**

- Time complexity : O(*N*). It takes O(*L*) to insert a number in Trie, and O(*L*) to find the max XOR of the given number with all already inserted ones. *L=1+[log2*M*] is defined by the maximum number in the array and could be considered as a constant here. Hence the overall time complexity is O(*N).
- Space complexity : O(1), since one needs at maximum O(*2^L*)=O(*M*) space to keep Trie, and L and M could be considered as constants here because of input limitations.

