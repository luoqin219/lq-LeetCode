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

**Complexity Analysis**

<img src="/Users/qinluo/Library/Application Support/typora-user-images/image-20200813120538020.png" alt="image-20200813120538020" style="zoom:50%;" />



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



### 967. Numbers With Same Consecutive Differences - Medium

Return all **non-negative** integers of length `N` such that the absolute difference between every two consecutive digits is `K`.

Note that **every** number in the answer **must not** have leading zeros **except** for the number `0` itself. For example, `01` has one leading zero and is invalid, but `0` is valid.

You may return the answer in any order.

**Example 1:**

```
Input: N = 3, K = 7
Output: [181,292,707,818,929]
Explanation: Note that 070 is not a valid number, because it has leading zeroes.
```

**Example 2:**

```
Input: N = 2, K = 1
Output: [10,12,21,23,32,34,43,45,54,56,65,67,76,78,87,89,98]
```

**Note:**

1. `1 <= N <= 9`
2. `0 <= K <= 9`

#### Approach 1: Backtracking

**Implementation**

```java
List<Integer> result = new ArrayList<>();
public int[] numsSameConsecDiff(int N, int K) {
    if (N == 1) // edge case, when N == 1 we should start answer sequence from 0
        result.add(0);
    
    // start forming sequences starting from 1 to 9
    for (int i = 1; i < 10; i++) {
        StringBuilder sb = new StringBuilder();
        sb.append(i);
        backtrack(N-1, K, sb);
    }
    
    // transform our result to array that matches the expected output 
    int[] arr = new int[result.size()];
    for (int i = 0; i < arr.length; i++)
        arr[i] = result.get(i);
    return arr;
}

void backtrack(int N, int K, StringBuilder sb) {
    if (N == 0){ // come to the limit of numbers with valid sequence
        result.add(Integer.valueOf(sb.toString()));
        return;
    }
    
    int prev = sb.charAt(sb.length()-1) -'0';// check abs diff curr - previous == K
    for (int curr = 0; curr <10; curr++) {
        if(Math.abs(curr - prev) == K){
            sb.append(curr);
            backtrack(N-1, K, sb);
            sb.setLength(sb.length()-1);
        }
    }
}
```



#### Approach 2: DFS (Depth-First Search)

**Intuition**

If one is not familiar with the concepts of DFS and BFS, we have an Explore card called [Queue & Stack](https://leetcode.com/explore/learn/card/queue-stack/) where we cover the [DFS traversal](https://leetcode.com/explore/learn/card/queue-stack/232/practical-application-stack/) as well as the [BFS traversal](https://leetcode.com/explore/learn/card/queue-stack/231/practical-application-queue/).

In this section, we will start from the DFS strategy, which arguably is more intuitive for this problem.

As we stated in the overview section, we could build a valid digit combination *digit by digit* or (node by node in terms of tree).

For a number consisting of `N` digits, we start from the highest digit and walk through to the lowest digit. At each step, we might have several candidates that are eligible to be explored.

With the DFS strategy, we prioritize the *depth* over the *breadth*, *i.e.* we pick one of the candidates and continue the exploration before moving on to the other candidates that are of the same level.

**Algorithm**

Intuitively we could implement the DFS algorithm with recursion. Here we define a recursive function `DFS(N, num)` (in Python) whose goal is to come up the combinations for the remaining `N` digits, starting from the current `num`. Note that, the signature of the function is slightly different in our Java implementation. Yet, the semantics of the function remains the same.

![DFS example](https://leetcode.com/problems/numbers-with-same-consecutive-differences/Figures/967/967_dfs_example.png)

For instance, in the previous examples, where `N=3` and `K=2`, and there is a moment we would invoke `DFS(1, 13)` which is to add another digit to the existing number `13` so that the final number meets the requirements. If the DFS function works properly, we should have the numbers of `135` and `131` as results after the invocation.

We could implement the recursive function in the following steps:

- As a base case, when `N=0` *i.e.* no more remaining digits to complete, we could return the current `num` as the result.
- Otherwise, there are still some remaining digits to be added to the current number, *e.g.* `13`. There are two potential cases to explore, based on the last digit of the current number which we denote as `tail_digit`.
  - Adding the difference `K` to the last digit, *i.e.* `tail_digit + K`.
  - Deducting the difference `K` from the last digit, *i.e.* `tail_digit - K`.
- If the result of either above case falls into the valid digit range (*i.e.* [0, 9][0,9]), we then continue the exploration by invoking the function itself.

Once we implement the `DFS(N, num)` function, we then simply call this function over the scope of [1, 9][1,9], *i.e.* the valid digits for the highest position.

**Note**: If we are asked to return numbers of a single digit (i.e. `N=1`), then regardless of `K`, all digits are valid, including zero. We treat this as a special case in the code, since in our implementation of DFS function, we will never return zero as the result.

```java
class Solution {

    public int[] numsSameConsecDiff(int N, int K) {
        if (N == 1)
            return new int[] {0, 1, 2, 3, 4, 5, 6, 7, 8, 9};

        List<Integer> results = new ArrayList<Integer>();
        for (int num = 1; num < 10; ++num)
            this.DFS(N - 1, num, K, results);

        // convert the ArrayList to int[]
        return results.stream().mapToInt(i->i).toArray();
    }

    protected void DFS(int N, int num, int K, List<Integer> results) {
        if (N == 0) {
            results.add(num);
            return;
        }
        List<Integer> nextDigits = new ArrayList<>();

        Integer tailDigit = num % 10;
        nextDigits.add(tailDigit + K);
        if (K != 0)
            nextDigits.add(tailDigit - K);
        for (Integer nextDigit : nextDigits) {
            if (0 <= nextDigit && nextDigit < 10) {
                Integer newNum = num * 10 + nextDigit;
                this.DFS(N - 1, newNum, K, results);
            }
        }
    }
}
```

**Complexity Analysis**

Let *N* be the number of digits for a valid combination, and *K* be the difference between digits.

<img src="/Users/qinluo/Library/Application Support/typora-user-images/image-20200818154328788.png" alt="image-20200818154328788" style="zoom:50%;" />



#### Approach 3: BFS (Breadth-First Search)

**Intuition**

It might be more intuitive to come up a DFS solution as we presented before. However, it is also viable to solve this problem with *BFS* (Breadth-First Search) traversal strategy.

> Rather than building the solution one by one, we could do it *batch by batch*, *i.e.* level by level.

Each level contains the numbers that are of the same amount of digits. Also, each level corresponds to the solutions with a specific number of digits.

![BFS](https://leetcode.com/problems/numbers-with-same-consecutive-differences/Figures/967/967_BFS.png)

For example, given `N=3` and `K=7`, at the first level, we would have potentially 9 candidates (*i.e.* `[1, 2, 3, 4, 5, 7, 8, 9]`). When we move on to the second level, the candidates are reduced down to `[18, 29, 70, 81, 92]`. Finally, at the last level, we would have the solutions as `[181, 292, 707, 818, 929]`.

**Algorithm**

Here are a few steps to implement the BFS algorithm for this problem.

- We could implement the algorithm with nested two-levels loops, where the outer loop iterates through levels and the inner loop handles the elements within each level.
- We could use a list data structure to keep the numbers for a single level, *i.e.* here we name the variable as `queue`.
- For each number in the queue, we could apply the same logics as in the DFS approach, except the last step, rather than making a recursive call for the next number we simply append the number to the queue for the next level.

```java
class Solution {

    public int[] numsSameConsecDiff(int N, int K) {

        if (N == 1)
            return new int[] {0, 1, 2, 3, 4, 5, 6, 7, 8, 9};

        List<Integer> queue = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9);
        for(int level = 1; level < N; level++) {
            ArrayList<Integer> nextQueue = new ArrayList<>();
            // iterate through each number within the level
            for (Integer num : queue) {
                Integer tailDigit = num % 10;

                ArrayList<Integer> nextDigits = new ArrayList<>();
                nextDigits.add(tailDigit + K);
                if (K != 0)
                    nextDigits.add(tailDigit - K);
                for (Integer nextDigit : nextDigits) {
                    if (0 <= nextDigit && nextDigit < 10) {
                        Integer newNum = num * 10 + nextDigit;
                        nextQueue.add(newNum);
                    }
                }
            }
            // prepare for the next level
            queue = nextQueue;
        }

        return queue.stream().mapToInt(i->i).toArray();
    }
}
```

**Complexity Analysis**

Let *N* be the number of digits for a valid combination, and *K* be the difference between digits.

![image-20200818154727444](/Users/qinluo/Library/Application Support/typora-user-images/image-20200818154727444.png)

