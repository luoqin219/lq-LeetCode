# Backtracking

### 1286. Iterator for Combination - Medium

Design an Iterator class, which has:

- A constructor that takes a string `characters` of **sorted distinct** lowercase English letters and a number `combinationLength` as arguments.
- A function *next()* that returns the next combination of length `combinationLength` in **lexicographical order**.
- A function *hasNext()* that returns `True` if and only if there exists a next combination.

**Example:**

```
CombinationIterator iterator = new CombinationIterator("abc", 2); // creates the iterator.

iterator.next(); // returns "ab"
iterator.hasNext(); // returns true
iterator.next(); // returns "ac"
iterator.hasNext(); // returns true
iterator.next(); // returns "bc"
iterator.hasNext(); // returns false
```

**Constraints:**

- `1 <= combinationLength <= characters.length <= 15`
- There will be at most `10^4` function calls per test.
- It's guaranteed that all calls of the function `next` are valid.

#### Thoughts

The interpretation of this problem depends on the interviewer. There are three possible scenarios:

- You could be asked to implement \mathcal{O}(1)O(1) runtime by precomputing all the combinations.
- Or, you could be asked to save space, to use no precomputation and to implement the `nextCombination` function to generate each new combination from the previous one during the runtime.
- Or, the interviewer could let you choose one of the problems above and then asks you to implement the second one as a follow-up.

#### Approach 1: precompute all combinations using backtracking

```java
class CombinationIterator {
    public Deque <String> combinations = new ArrayDeque <String> ();
    String characters;
    int n, k; // n: length of the string; k: length of the combination

    public void backtrack(int first, StringBuilder curr) {
        // if the combination is done
        if (curr.length() == k) {
            combinations.push(curr.toString());
            // speed up by non-constructing combinations 
            // with more than k elements  
            return;
        }

        for (int i = first; i < n; ++i) {
            // add i into the current combination
            curr.append(characters.charAt(i));
            // use next integers to complete the combination
            backtrack(i + 1, curr);
            // backtrack
            curr.deleteCharAt(curr.length() - 1);
        }
    }

    public CombinationIterator(String characters, int combinationLength) {
        this.n = characters.length();
        this.k = combinationLength;
        this.characters = characters;
        backtrack(0, new StringBuilder());
    }

    public String next() {
        return combinations.removeLast();
    }

    public boolean hasNext() {
        return (!combinations.isEmpty());
    }
}
```

**Complexity Analysis**

- Time Complexity: O(1)
- Space Complexity: <img src="/Users/qinluo/Library/Application Support/typora-user-images/image-20200813101803920.png" alt="image-20200813101803920" style="zoom: 50%;" />

#### Overview: Bitmasking and Algorithm L

In this article, we're going to consider Bitmasking and Algorithm L. These two approaches could be easily used both for the precomputation, and the `nextCombination` function.

![pic](https://leetcode.com/problems/iterator-for-combination/Figures/1286/overall4.png)

**Bitmasking**

It's more simple to generate numbers than combinations. So let us generate numbers, and then use their binary representations, bitmasks.

The idea is that we map each bitmask of length n to a combination. Each bit is mapped to a character, the lowest bit to the last character, the highest bit - to the first character.

![pic](https://leetcode.com/problems/iterator-for-combination/Figures/1286/lowest_bit22.png)

The character `characters[i]` is present in the combination if bit at the `n - 1 - i`*th* position is set.

![pic](https://leetcode.com/problems/iterator-for-combination/Figures/1286/present22.png)

<img src="/Users/qinluo/Library/Application Support/typora-user-images/image-20200813113526262.png" alt="image-20200813113526262" style="zoom:50%;" />

![pic](https://leetcode.com/problems/iterator-for-combination/Figures/1286/bitmasks2.png)

In this article, we're going to keep bitmasking approach as simple as possible, having O(2^*N*⋅*N*) time complexity for the precomputation case.

**Algorithm L by D. E. Knuth**

![pic](https://leetcode.com/problems/iterator-for-combination/Figures/1286/algorithm_l2.png)

Algorithm L is an efficient BFS approach to generate lexicographic (*i.e.* binary sorted) combinations. It works by generating the combinations of indexes.

<img src="/Users/qinluo/Library/Application Support/typora-user-images/image-20200813114026735.png" alt="image-20200813114026735" style="zoom:67%;" />

#### Apporach 2: Bit masking

#### Implementation 1: Bitmasking: Precomputation

**Algorithm**

- Generate all possible binary bitmasks of length n*: from 0 to 2^n - 1*.
- Use bitmasks with *k* set bits to generate combinations with *k* elements. If the `n - 1 - j`*th* bit set in the bitmask, it indicates the presence of the character `characters[j]` in the combination, and vise versa.

> Bit manipulation trick. To test if the `i`*th* bit is set in the bitmask, check the following: `bitmask & (1 << i) != 0`. It shifts the first 1-bit i positions to the left and then uses logical AND operation to eliminate all bits from bitmask but `i`*th*. Hence, the result is nonzero only if `i`*th* bit is set in the bitmask.

- Now you have all combinations precomputed. Pop them out one by one at each request.

![pic](https://leetcode.com/problems/iterator-for-combination/Figures/1286/bitmasking2.png)

```java
class CombinationIterator {
    public Deque <String> combinations = new ArrayDeque<String> ();

    public CombinationIterator(String characters, int combinationLength) {
        int n = characters.length();
        int k = combinationLength;

        // generate bitmasks from 0..00 to 1..11  
        for (int bitmask = 0; bitmask < 1 << n; bitmask++) {
            // use bitmasks with k 1-bits
            if (Integer.bitCount(bitmask) == k) {
                // convert bitmask into combination
                // 111 --> "abc", 000 --> ""
                // 110 --> "ab", 101 --> "ac", 011 --> "bc"
                StringBuilder curr = new StringBuilder();
                for (int j = 0; j < n; j++) {
                    if ((bitmask & (1 << n - j - 1)) != 0) {
                        curr.append(characters.charAt(j));
                    }
                }
                combinations.push(curr.toString()); // push the element to the top (different from using add())
            }
        }
    }

    public String next() {
        return combinations.pop();
    }

    public boolean hasNext() {
        return (!combinations.isEmpty());
    }
}
```

**Complexity Analysis**

<img src="/Users/qinluo/Library/Application Support/typora-user-images/image-20200813114712531.png" alt="image-20200813114712531" style="zoom:50%;" />

#### Implementation 2: Bitmasking: Next Combination

During precomputation, we've generated combinations in the *descending* order. That was done to pop them out later in the *ascending* order easily.

> For the runtime generation at each `next()` call, the strategy should be changed: the combinations should be generated directly in the *ascending* order.

**Algorithm**

- <img src="/Users/qinluo/Library/Application Support/typora-user-images/image-20200813115151929.png" alt="image-20200813115151929" style="zoom:50%;" />
- At each step, generate a combination out of the current bitmask. If the `n - 1 - j`*th* bit set in the bitmask, that means the presence of the character `characters[j]` in the combination, and vise versa.
- Generate the next bitmask. Decrease bitmask gradually till you meet a bitmask with exactly *k* set bits.

![pic](https://leetcode.com/problems/iterator-for-combination/Figures/1286/approach222.png)

```java
class CombinationIterator {
    int bitmask, n, k;
    String chars;

    public CombinationIterator(String characters, int combinationLength) {
        n = characters.length();
        k = combinationLength;
        chars = characters;

        // generate first bitmask 1(k)0(n - k)
        bitmask = (1 << n) - (1 << n - k);
    }

    public String next() {
        // convert bitmask into combination
        // 111 --> "abc", 000 --> ""
        // 110 --> "ab", 101 --> "ac", 011 --> "bc"
        StringBuilder curr = new StringBuilder();
        for (int j = 0; j < n; j++) {
            if ((bitmask & (1 << n - j - 1)) != 0) {
                curr.append(chars.charAt(j));
            }
        }

        // generate next bitmask
        bitmask--;
        while (bitmask > 0 && Integer.bitCount(bitmask) != k) {
            bitmask--;
        }

        return curr.toString();
    }

    public boolean hasNext() {
        return bitmask > 0;
    }
}
```

**Complexity Analysis**

<img src="/Users/qinluo/Library/Application Support/typora-user-images/image-20200813120108370.png" alt="image-20200813120108370" style="zoom:50%;" />



#### Apporach 3: Algorithm L by D. E. Knuth: Lexicographic Combinations

#### Implementation 1: Precomputation

**Algorithm**

The algorithm is quite straightforward:

- Initialize `nums` to be a list of integers from 00 to k*k*. Add n*n* as the last element. It serves as a sentinel. Set the pointer at the beginning of the list `j = 0`.
- While `j < k`:
  - Convert the first *k* elements (*i.e.* all elements but the sentinel) from `nums` into the combination to save.
  - Find the first number in `nums` such that `nums[j + 1] != nums[j] + 1` and increase it by one `nums[j] += 1` to move to the next combination.
- Now you have all combinations precomputed. Pop them out one by one at each request.

```java
class CombinationIterator {
    public Deque <String> combinations = new ArrayDeque <String>();

    public CombinationIterator(String characters, int combinationLength) {
        int n = characters.length(), k = combinationLength;

        // init the first combination
        int[] nums = new int[k + 1];
        for (int i = 0; i < k; ++i) {
            nums[i] = i;
        }
        nums[k] = n;

        int j = 0;
        while (j < k) {
            // add current combination
            StringBuilder sb = new StringBuilder();
            for (int i = k - 1; i > -1; i--) {
                sb.append(characters.charAt(n - 1 - nums[i]));
            }
            combinations.push(sb.toString());
            // Generate next combination.
            // Find the first j such that nums[j] + 1 != nums[j + 1].
            // Increase nums[j] by one.
            j = 0;
            while ((j < k) && (nums[j + 1] == nums[j] + 1)) {
                nums[j] = j;
                j++;
            }
            nums[j]++;
        }
    }

    public String next() {
        return combinations.pop();
    }

    public boolean hasNext() {
        return (!combinations.isEmpty());
    }
}
```



#### Implementation 2: Next Combination

> For the runtime generation, the strategy should be changed: the combinations will be generated directly in the *ascending* order.

![pic](https://leetcode.com/problems/iterator-for-combination/Figures/1286/l_reversed2.png)

**Algorithm**

- Initialize `nums` as a list of integers from 0 to *k*.
- At each step:
  - Convert nums into the combination to save.
  - Generate next combination:
    - Set the pointer at the end of the list `j = k - 1`.
    - Find the first j such that `nums[j] == n - k + j` and increase `nums[j]` by one `nums[j] += 1`.
    - Set `nums[i] = nums[j] + i - j` for every `i` in range `(j + 1, k)` to move to the next combination.

```java
class CombinationIterator {
    int[] nums;
    boolean has_next;
    int n, k;
    String chars;

    public CombinationIterator(String characters, int combinationLength) {
        n = characters.length();
        k = combinationLength;
        chars = characters;

        // init the first combination
        has_next = true;
        nums = new int[k];
        for (int i = 0; i < k; ++i) {
            nums[i] = i;
        }
    }

    public String next() {
        StringBuilder curr = new StringBuilder();
        for (int j: nums) {
            curr.append(chars.charAt(j));
        }

        // Generate next combination.
        // Find the first j such that nums[j] != n - k + j.
        // Increase nums[j] by one.
        int j = k - 1;
        while (j >= 0 && nums[j] == n - k + j) {
            j--;
        }

        if (j >= 0) {
            nums[j]++;
            for (int i = j + 1; i < k; i++) {
                nums[i] = nums[j] + i - j;
            }
        } else {
            has_next = false;
        }

        return curr.toString();
    }

    public boolean hasNext() {
        return has_next;
    }
}
```

**Complexity Analysis**

- Time Complexity: O(*k*) both for `init()` and `next()` functions. The algorithm generates a new combination from the previous one in O(*k*) time.
- Space Complexity: O(*k*) to keep the current combination of length *k*.