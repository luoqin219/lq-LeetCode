# Mixed

### 146. LRU Cache - Medium

Design and implement a data structure for [Least Recently Used (LRU) cache](https://en.wikipedia.org/wiki/Cache_replacement_policies#LRU). It should support the following operations: `get` and `put`.

`get(key)` - Get the value (will always be positive) of the key if the key exists in the cache, otherwise return -1.
`put(key, value)` - Set or insert the value if the key is not already present. When the cache reached its capacity, it should invalidate the least recently used item before inserting a new item.

The cache is initialized with a **positive** capacity.

**Follow up:**
Could you do both operations in **O(1)** time complexity?

#### Approach 1: Ordered dictionary

**Intuition**

We're asked to implement [the structure](https://en.wikipedia.org/wiki/Cache_replacement_policies#LRU) which provides the following operations in \mathcal{O}(1)O(1) time :

- Get the key / Check if the key exists
- Put the key
- Delete the first added key

The first two operations in \mathcal{O}(1)O(1) time are provided by the standard hashmap, and the last one - by linked list.

> There is a structure called *ordered dictionary*, it combines behind both hashmap and linked list. In Python this structure is called [*OrderedDict*](https://docs.python.org/3/library/collections.html#collections.OrderedDict) and in Java [*LinkedHashMap*](https://docs.oracle.com/javase/8/docs/api/java/util/LinkedHashMap.html).

**Implementation**

```java
class LRUCache extends LinkedHashMap<Integer, Integer>{
    private int capacity;
    
    public LRUCache(int capacity) {
        super(capacity, 0.75F, true);
        this.capacity = capacity;
    }

    public int get(int key) {
        return super.getOrDefault(key, -1);
    }

    public void put(int key, int value) {
        super.put(key, value);
    }

    @Override
    protected boolean removeEldestEntry(Map.Entry<Integer, Integer> eldest) {
        return size() > capacity; 
    }
}

/**
 * Your LRUCache object will be instantiated and called as such:
 * LRUCache obj = new LRUCache(capacity);
 * int param_1 = obj.get(key);
 * obj.put(key,value);
 */
```

**Complexity Analysis**

- Time complexity : O(1) both for `put` and `get` since all operations with ordered dictionary : `get/in/set/move_to_end/popitem` (`get/containsKey/put/remove`) are done in a constant time.
- Space complexity : O(capacity) since the space is used only for an ordered dictionary with at most `capacity + 1` elements.

#### Approach 2: Hashmap + DoubleLinkedList

**Intuition**

This Java solution is an extended version of the [the article published on the Discuss forum](https://leetcode.com/problems/lru-cache/discuss/45911/Java-Hashtable-%2B-Double-linked-list-(with-a-touch-of-pseudo-nodes)).

The problem can be solved with a hashmap that keeps track of the keys and its values in the double linked list. That results in O(1) time for `put` and `get` operations and allows to remove the first added node in O(1) time as well.

![compute](https://leetcode.com/problems/lru-cache/Figures/146/structure.png)

One advantage of *double* linked list is that the node can remove itself without other reference. In addition, it takes constant time to add and remove nodes from the head or tail.

One particularity about the double linked list implemented here is that there are *pseudo head* and *pseudo tail* to mark the boundary, so that we don't need to check the `null` node during the update.

![compute](https://leetcode.com/problems/lru-cache/Figures/146/new_node.png)

**Implementation**

```java
class LRUCache {

    private class Node {
        int key;
        int value;
        Node prev;
        Node next;

        public Node(int key, int value) {
            this.key = key;
            this.value = value;
        }
    }

    private int capacity;
    private Map<Integer, Node> map;
    private Node head;
    private Node tail;

    public LRUCache(int capacity) {
        this.capacity = capacity;
        map = new HashMap<Integer, Node>(capacity * 4 / 3 + 1);
        head = new Node(-1, -1);
        tail = new Node(-1, -1);
        head.next = tail;
        tail.prev = head;
    }

    public int get(int key) {
        Node node = map.get(key);
        if (null == node) {
            return -1;
        }
        remove(node);
        addHead(node);
        return node.value;
    }

    public void put(int key, int value) {
        Node node = map.get(key);
        if (null != node) {
            node.value = value;
            remove(node);
            addHead(node);
            return;
        }
        if (map.size() >= capacity) {
            Node last = tail.prev;
            map.remove(last.key);
            remove(last);
        }
        node = new Node(key, value);
        map.put(key, node);
        addHead(node);
    }

    private void addHead(Node node) {
        Node next = head.next;
        node.next = next;
        next.prev = node;
        head.next = node;
        node.prev = head;
    }

    private void remove(Node node) {
        if (null == node) {
            return;
        }
        node.prev.next = node.next;
        node.next.prev = node.prev;
    }
}
/**
 * Your LRUCache object will be instantiated and called as such:
 * LRUCache obj = new LRUCache(capacity);
 * int param_1 = obj.get(key);
 * obj.put(key,value);
 */
```

**Complexity Analysis**

- Time complexity : O(1) both for `put` and `get`.
- Space complexity : O(capacity)since the space is used only for a hashmap and double linked list with at most `capacity + 1` elements.



### 202. Happy Number - Easy

Write an algorithm to determine if a number is "happy".

A happy number is a number defined by the following process: Starting with any positive integer, replace the number by the sum of the squares of its digits, and repeat the process until the number equals 1 (where it will stay), or it loops endlessly in a cycle which does not include 1. Those numbers for which this process ends in 1 are happy numbers.

**Example:** 

```
Input: 19
Output: true
Explanation: 
12 + 92 = 82
82 + 22 = 68
62 + 82 = 100
12 + 02 + 02 = 1
```

#### Approach 1: Detect Cycles with a HashSet

**Intuition**

A good way to get started with a question like this is to make a couple of examples. Let's start with the number 77. The next number will be 4949 (as 7^2 = 4972=49), and then the next after that will be 9797 (as 4^2 + 9^2 = 9742+92=97). We can continually repeat the process of squaring and then adding the digits until we get to 11. Because we got to 11, we know that 77 is a happy number, and the function should return `true`.

![The chain of numbers starting with 7. It has the numbers 7, 49, 97, 130, 10 and 1.](https://leetcode.com/problems/happy-number/Figures/202/image1.png)

As another example, let's start with 116116. By repeatedly applying the squaring and adding process, we eventually get to 5858, and then a bit after that, we get *back to* 5858. Because we are back at a number we've already seen, we know there is a cycle, and therefore it is impossible to ever reach 11. So for 116116, the function should return `false`.

![The chain of numbers starting with 116. It has the numbers 116, 38, 73, 58, and then goes in a circle to 89, 145, 42, 20, 4, 16, 37, and back to 58.](https://leetcode.com/problems/happy-number/Figures/202/image2.png)

Based on our exploration so far, we'd expect continually following links to end in one of three ways.

1. It eventually gets to 11.
2. It eventually gets stuck in a cycle.
3. It keeps going higher and higher, up towards infinity.

That 3rd option sounds really annoying to detect and handle. How would we even know that it is going to continue going up, rather than eventually going back down, possibly to 1?1? Luckily, it turns out we don't need to worry about it. Think carefully about what the largest next number we could get for each number of digits is.

| Digits |    Largest    | Next |
| ------ | :-----------: | ---: |
| 1      |       9       |   81 |
| 2      |      99       |  162 |
| 3      |      999      |  243 |
| 4      |     9999      |  324 |
| 13     | 9999999999999 | 1053 |



For a number with 33 digits, it's impossible for it to ever go larger than 243243. This means it will have to either get stuck in a cycle below 243243 or go down to 11. Numbers with 44 or more digits will always lose a digit at each step until they are down to 33 digits. So we know that *at worst*, the algorithm might cycle around all the numbers under 243243 and then go back to one it's already been to (a cycle) or go to 11. But it won't go on indefinitely, allowing us to rule out the 3rd option.

Even though you don't need to handle the 3rd case in the code, you still need to understand *why* it can never happen, so that you can justify why you didn't handle it.

**Algorithm**

There are 2 parts to the algorithm we'll need to design and code.

1. Given a number n*, what is its *next* number?
2. Follow a chain of numbers and detect if we've entered a cycle.

*Part 1* can be done by using the division and modulus operators to repeatedly take digits off the number until none remain, and then squaring each removed digit and adding them together. Have a careful look at the code for this, "picking digits off one-by-one" is a useful technique you'll use for solving a lot of different problems.

*Part 2* can be done using a **HashSet**. Each time we generate the next number in the chain, we check if it's already in our HashSet.

- If it is *not* in the HashSet, we should add it.
- If it *is* in the HashSet, that means we're in a cycle and so should return `false`.

The reason we use a **HashSet** and *not* a Vector, List, or Array is because we're repeatedly checking whether or not numbers are in it. Checking if a number is in a HashSet takes O*(1) time, whereas for the other data structures it takes O*(*n*) time. Choosing the correct data structures is an essential part of solving these problems.

```java
class Solution {

    private int getNext(int n) {
        int totalSum = 0;
        while (n > 0) {
            int d = n % 10;
            n = n / 10;
            totalSum += d * d;
        }
        return totalSum;
    }

    public boolean isHappy(int n) {
        Set<Integer> seen = new HashSet<>();
        while (n != 1 && !seen.contains(n)) {
            seen.add(n);
            n = getNext(n);
        }
        return n == 1;
    }
}
```

**Complexity Analysis**

Determining the *time complexity* for this problem is challenging for an "easy" level question. If you're new to these problems, have a go at calculating the time complexity for *just* the `getNext(n)` function (don't worry about how many numbers will be in the chain).

- ![image-20191228171954408](C:\Users\73995\AppData\Roaming\Typora\typora-user-images\image-20191228171954408.png)

  Finding the **next** value for a given number has a cost of O(\log n)*O*(log*n*) because we are processing each digit in the number, and the number of digits in a number is given by \log nlog*n*.

  To work out the *total* time complexity, we'll need to think carefully about how many numbers are in the chain, and how big they are.

  We determined above that once a number is below 243243, it is impossible for it to go back up above 243243. Therefore, based on our very shallow analysis we know for *sure* that once a number is below 243243, it is impossible for it to take more than another 243243 steps to terminate. Each of these numbers has at most 3 digits. With a little more analysis, we could replace the 243243 with the length of the longest number chain below 243243, however because the constant doesn't matter anyway, we won't worry about it.

  For an n*n* above 243243, we need to consider the cost of each number in the chain that is above 243243. With a little math, we can show that in the worst case, these costs will be O(\log n) + O(\log \log n) + O(\log \log \log n)...*O*(log*n*)+*O*(loglog*n*)+*O*(logloglog*n*).... Luckily for us, the O(\log n)*O*(log*n*) is the dominating part, and the others are all tiny in comparison (collectively, they add up to less than \log n)log*n*), so we can ignore them.

- ![image-20191228172015771](C:\Users\73995\AppData\Roaming\Typora\typora-user-images\image-20191228172015771.png)

  We can optimize to O*(243⋅3)=*O*(1) easily by only saving numbers in the set that are less than 243243, as we have already shown that for numbers that are higher, it's impossible to get back to them anyway.

It might seem worrying that we're simply dropping such "large" constants. But this is what we do in Big O notation, which is a measure of how long the function will take, as the *size of the input increases*.

Think about what would happen if you had a number with *1 million* digits in it. The first step of the algorithm would process those million digits, and then the next value would be, at most (pretend all the digits are 9), be 81 * 1,000,000 = 81,000,000. In just one step, we've gone from a million digits, down to just 8. The largest possible 8 digit number we could get is 99,9999,99999,9999,999, which then goes down to 81 * 8 = 648. And then from here, the cost will be the same as if we'd started with a 3 digit number. Starting with 2 million digits (a **massively** larger number than one with a 1 million digits) would only take roughly twice as long, as again, the dominant part is summing the squares of the 2 million digits, and the rest is *tiny* in comparison.

#### Approach 2: Floyd's Cycle-Finding Algorithm

**Intuition**

The chain we get by repeatedly calling `getNext(n)` is an *implicit* **LinkedList**. *Implicit* means we don't have actual LinkedNode's and pointers, but the data does still form a LinkedList structure. The starting number is the head "node" of the list, and all the other numbers in the chain are nodes. The next pointer is obtained with our `getNext(n)` function above.

Recognizing that we actually have a LinkedList, it turns out that this question is almost the same as another Leetcode problem, [detecting if a linked list has a cycle](https://leetcode.com/problems/linked-list-cycle). As @Freezen [has pointed out](https://leetcode.com/problems/happy-number/discuss/56917/My-solution-in-C(-O(1)-space-and-no-magic-math-property-involved-)), we can therefore use Floyd's Cycle-Finding Algorithm here. This algorithm is based on 2 runners running around a circular race track, a fast runner and a slow runner. In reference to a famous fable, many people call the slow runner the "tortoise" and the fast runner the "hare".

Regardless of where the tortoise and hare start in the cycle, they are guaranteed to eventually meet. This is because the hare moves one node closer to the tortoise (in their direction of movement) each step.

**Algorithm**

Instead of keeping track of just one value in the chain, we keep track of 2, called the slow runner and the fast runner. At each step of the algorithm, the slow runner goes forward by 1 number in the chain, and the fast runner goes forward by 2 numbers (nested calls to the `getNext(n)` function).

If n *is* a happy number, i.e. there is no cycle, then the fast runner will eventually get to 1 before the slow runner.

If n *is not* a happy number, then eventually the fast runner and the slow runner will be on the same number.

```java
class Solution {

     public int getNext(int n) {
        int totalSum = 0;
        while (n > 0) {
            int d = n % 10;
            n = n / 10;
            totalSum += d * d;
        }
        return totalSum;
    }

    public boolean isHappy(int n) {
        int slowRunner = n;
        int fastRunner = getNext(n);
        while (fastRunner != 1 && slowRunner != fastRunner) {
            slowRunner = getNext(slowRunner);
            fastRunner = getNext(getNext(fastRunner));
        }
        return fastRunner == 1;
    }
}
```

**Complexity Analysis**

- Time complexity : O*(logn*). Builds on the analysis for the previous approach, except this time we need to analyse how much extra work is done by keeping track of two places instead of one, and how many times they'll need to go around the cycle before meeting.

  If there is no cycle, then the fast runner will get to 1, and the slow runner will get halfway to 1. Because there were 2 runners instead of 1, we know that at worst, the cost was O*(2⋅log*n*)=*O*(logn*).

  Like above, we're treating the length of the chain to the cycle as insignificant compared to the cost of calculating the next value for the first n. Therefore, the only thing we need to do is show that the number of times the runners go back over previously seen numbers in the chain is constant.

  Once both pointers are in the cycle (which will take constant time to happen) the fast runner will get one step closer to the slow runner at each cycle. Once the fast runner is one step behind the slow runner, they'll meet on the next step. Imagine there are *k* numbers in the cycle. If they started at k*−1 places apart (which is the furthest apart they can start), then it will take k*−1 steps for the fast runner to reach the slow runner, which again is constant for our purposes. Therefore, the dominating operation is still calculating the next value for the starting n, which is O*(logn*).

- Space complexity : O(1). For this approach, we don't need a HashSet to detect the cycles. The pointers require constant extra space.



### 165. Compare Version Numbers -  Medium

Compare two version numbers *version1* and *version2*.
If `*version1* > *version2*` return `1;` if `*version1* < *version2*` return `-1;`otherwise return `0`.

You may assume that the version strings are non-empty and contain only digits and the `.` character.

The `.` character does not represent a decimal point and is used to separate number sequences.

For instance, `2.5` is not "two and a half" or "half way to version three", it is the fifth second-level revision of the second first-level revision.

You may assume the default revision number for each level of a version number to be `0`. For example, version number `3.4` has a revision number of `3` and `4` for its first and second level revision number. Its third and fourth level revision number are both `0`.

 

**Example 1:**

```
Input: version1 = "0.1", version2 = "1.1"
Output: -1
```

**Example 2:**

```
Input: version1 = "1.0.1", version2 = "1"
Output: 1
```

**Example 3:**

```
Input: version1 = "7.5.2.4", version2 = "7.5.3"
Output: -1
```

**Example 4:**

```
Input: version1 = "1.01", version2 = "1.001"
Output: 0
Explanation: Ignoring leading zeroes, both “01” and “001" represent the same number “1”
```

**Example 5:**

```
Input: version1 = "1.0", version2 = "1.0.0"
Output: 0
Explanation: The first version number does not have a third level revision number, which means its third level revision number is default to "0"
```

 

**Note:**

1. Version strings are composed of numeric strings separated by dots `.` and this numeric strings **may** have leading zeroes.
2. Version strings do not start or end with dots, and they will not be two consecutive dots.

#### Approach 1: Split + Parse, Two Pass

**Intuition**

The first idea is to split both strings by dot character into chunks and then compare the chunks one by one.

![traversal](https://leetcode.com/problems/compare-version-numbers/Figures/165/yoyo3.png)

That works fine if the number of chunks is the same for both versions. If not, we need to pad the shorter string by adding `.0` at the end of the string with less chunks one or several times, so that the number of chunks will be the same.

![traversal](https://leetcode.com/problems/compare-version-numbers/Figures/165/diff3.png)

**Algorithm**

- Split both strings by dot character into two arrays.
- Iterate over the longest array and compare chunks one by one. If one of the arrays is over, virtually add as many zeros as needed to continue the comparison with the longer array.
  - If two chunks are not equal, return 1 or -1.
- If we're here, the versions are equal. Return 0.

**Implementation**

```java
class Solution {
  public int compareVersion(String version1, String version2) {
    String[] nums1 = version1.split("\\.");
    String[] nums2 = version2.split("\\.");
    int n1 = nums1.length, n2 = nums2.length;

    // compare versions
    int i1, i2;
    for (int i = 0; i < Math.max(n1, n2); ++i) {
      i1 = i < n1 ? Integer.parseInt(nums1[i]) : 0;
      i2 = i < n2 ? Integer.parseInt(nums2[i]) : 0;
      if (i1 != i2) {
        return i1 > i2 ? 1 : -1;
      }
    }
    // the versions are equal
    return 0;
  }
}
```

**Complexity Analysis**

- Time complexity : O(*N*+*M*+max(*N*,*M*)), where *N* and *M* are lengths of input strings.
- Space complexity : O(*N*+*M*) to store arrays `nums1` and `nums2`.

