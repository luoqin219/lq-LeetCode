# Math

### 342. Power of Four - Easy

Given an integer (**signed 32 bits**), write a function to check whether it is a power of 4.

**Example 1:**

```
Input: 16
Output: true
```

**Example 2:**

```
Input: 5
Output: false
```

**Follow up**: Could you solve it without loops/recursion?

#### Approch 1: Math

![image-20200804143956952](/Users/qinluo/Library/Application Support/typora-user-images/image-20200804143956952.png)

#### Implementation

```java
class Solution {
    public boolean isPowerOfFour(int num) {
        return (num > 0) && (Math.log(num) / Math.log(2) % 2 == 0);
    }
}
```

**Complexity Analysis**

- Time complexity : O(1).
- Space complexity : O(1).

#### Approach 2: Bit Manipulation

![image-20200804144358133](/Users/qinluo/Library/Application Support/typora-user-images/image-20200804144358133.png)

<img src="/Users/qinluo/Library/Application Support/typora-user-images/image-20200804144414962.png" alt="image-20200804144414962" style="zoom:50%;" />

<img src="/Users/qinluo/Library/Application Support/typora-user-images/image-20200804144505688.png" alt="image-20200804144505688" style="zoom:50%;" />

#### Implementation

```java
class Solution {
  public boolean isPowerOfFour(int num) {
    return (num > 0) && ((num & (num - 1)) == 0) && ((num & 0xaaaaaaaa) == 0);
  }
}
```

*For checking if a number is power of two (num & (num - 1)), please refer to https://leetcode.com/articles/power-of-two/

