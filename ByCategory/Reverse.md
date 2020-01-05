# Reverse

 ### 7. Reverse Integer - Easy

Given a 32-bit signed integer, reverse digits of an integer.

**Example 1:**

```
Input: 123
Output: 321
```

**Example 2:**

```
Input: -123
Output: -321
```

**Example 3:**

```
Input: 120
Output: 21
```

**Note:**
Assume we are dealing with an environment which could only store integers within the 32-bit signed integer range: [−2^31, 2^31 − 1]. For the purpose of this problem, assume that your function returns 0 when the reversed integer overflows.

#### Approach 1: Pop and Push Digits & Check before Overflow

**Intuition**

We can build up the reverse integer one digit at a time. While doing so, we can check beforehand whether or not appending another digit would cause overflow.

**Algorithm**

Reversing an integer can be done similarly to reversing a string.

We want to repeatedly "pop" the last digit off of x*x* and "push" it to the back of the \text{rev}rev. In the end, \text{rev}rev will be the reverse of the x*x*.

To "pop" and "push" digits without the help of some auxiliary stack/array, we can use math.

```cpp
//pop operation:
pop = x % 10;
x /= 10;

//push operation:
temp = rev * 10 + pop;
rev = temp;
```

However, this approach is dangerous, because the statement \text{temp} = \text{rev} \cdot 10 + \text{pop}temp=rev⋅10+pop can cause overflow.

Luckily, it is easy to check beforehand whether or this statement would cause an overflow.

![image-20191109205505718](C:\Users\73995\AppData\Roaming\Typora\typora-user-images\image-20191109205505718.png)

**Implementation**

```java
class Solution {
    public int reverse(int x) {
        int rev = 0;
        while (x != 0) {
            int pop = x % 10;
            x /= 10;
            if (rev > Integer.MAX_VALUE/10 || (rev == Integer.MAX_VALUE / 10 && pop > 7)) return 0;
            if (rev < Integer.MIN_VALUE/10 || (rev == Integer.MIN_VALUE / 10 && pop < -8)) return 0;
            rev = rev * 10 + pop;
        }
        return rev;
    }
}
```

**Other**

```java
class Solution {
    public int reverse(int x) {
        int sign = x < 0 ? -1 : 1;
        x = Math.abs(x);
        int res = 0;
        while (x > 0) {
            int newRes = res * 10 + x % 10;
            if (newRes / 10 != res) return 0;
            res = newRes;
            x /= 10;
        }
        
        return sign * res;
    }
}
```

**How to check if the integer is overflow?**

For your reference, to test if there is overflow for any integer `x` of the form `x = a * 10 + b` where `|b| < 10`, the right way should be comparing `x / 10` (integer division) with `a`. If `x / 10 != a`, there is overflow, otherwise no overflow can happen. The proof is as follows: first note that `x` itself is a signed integer, therefore we have `INT_MIN <= x <= INT_MAX`, which implies `INT_MIN/10 <= x/10 <= INT_MAX/10`. So if we assume `x / 10 == a`, we have `INT_MIN/10 <= a <= INT_MAX/10`. Since `|b| < 10`, then `a * 10 + b` can overflow only if `a = INT_MAX/10` or `a = INT_MIN/10`. For signed 32-bit integers, we have `INT_MAX = 2147483647` and `INT_MIN = -2147483648`. For `a = INT_MAX/10 = 214748364`, overflow will happen only if `b = 8 or 9`. However if this is the case, then `x = a * 10 + b` will be negative and `x / 10` cannot be the same as `a`, contradicting our assumption above. Similarly if `a = INT_MIN/10 = -214748364`, overflow will happen only if `b = -9` but then `x = a * 10 + b` will be positive and again `x / 10` won't be equal to `a`. In summary, `x / 10 != a <==> overflow`.



As for the test condition in the original post, which is equivalent to testing `(x - b) / 10 != a`, it will only cover the cases when `|a| > INT_MAX/10` but not the cases when `|a| = INT_MAX/10`, `b = 8 or 9` for positive `a` and `-9` for negative `a`. The test condition works here because the edge cases mentioned above won't happen due to the fact that the input itself is a signed integer. If the input is something else, say a string (see [String to Integer (atoi)](https://leetcode.com/problems/string-to-integer-atoi/#/description)), the test condition above will fail the edge cases.