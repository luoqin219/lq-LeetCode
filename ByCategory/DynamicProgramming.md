# Dynamic Programming Problems

###   53. Maximum Subarray - Easy (???)

Given an integer array `nums`, find the contiguous subarray (containing at least one number) which has the largest sum and return its sum.

**Example:**

```
Input: [-2,1,-3,4,-1,2,1,-5,4],
Output: 6
Explanation: [4,-1,2,1] has the largest sum = 6.
```

**Follow up:**

If you have figured out the O(*n*) solution, try coding another solution using the divide and conquer approach, which is more subtle.

```java
class Solution {
    public int maxSubArray(int[] nums) {
        int sumSoFar = nums[0];
        int sumContinue = nums[0];
        for (int i = 1; i < nums.length; i++){
            sumContinue = Math.max(sumContinue + nums[i], nums[i]);
            sumSoFar = Math.max(sumSoFar, sumContinue);
        }
        
        return sumSoFar;
    }
}
```

this problem was discussed by Jon Bentley (Sep. 1984 Vol. 27 No. 9 Communications of the ACM P885)

the paragraph below was copied from his paper (with a little modifications)



algorithm that operates on arrays: it starts at the left end (element A[1]) and scans through to the right end (element A[n]), keeping track of the maximum sum subvector seen so far. The maximum is initially A[0]. Suppose we've solved the problem for A[1 .. i - 1]; how can we extend that to A[1 .. i]? The maximum
sum in the first I elements is either the maximum sum in the first i - 1 elements (which we'll call MaxSoFar), or it is that of a subvector that ends in position i (which we'll call MaxEndingHere).



MaxEndingHere is either A[i] plus the previous MaxEndingHere, or just A[i], whichever is larger.

### 64.  Minimum Path Sum - Medium

Given a *m* x *n* grid filled with non-negative numbers, find a path from top left to bottom right which *minimizes* the sum of all numbers along its path.

**Note:** You can only move either down or right at any point in time.

**Example:**

```
Input:
[
  [1,3,1],
  [1,5,1],
  [4,2,1]
]
Output: 7
Explanation: Because the path 1→3→1→1→1 minimizes the sum.
```

change the grid to store min sum of path to each index

```java
class Solution {
    public int minPathSum(int[][] grid) {
        int m = grid.length, n = grid[0].length;
        for(int i = 0; i < m; i++){
	        for(int j = 0; j < n; j++){
	            if(i == 0 && j != 0) grid[i][j] += grid[i][j-1];
	            if(i != 0 && j == 0) grid[i][j] += grid[i-1][j];
	            if (i != 0 && j != 0) grid[i][j] += Math.min(grid[i-1][j], grid[i][j-1]);
	        }
        }
        return grid[m-1][n-1];
    }
}
```

if we cannot change the original grid value, then we could use code below

```java
public class Solution {

	private int getDist(int[][] dist, int x, int y) {
		if(x < 0 || y < 0) return Integer.MAX_VALUE;
		return dist[x][y];		
	}

	public int minPathSum(int[][] grid) {
		if(grid == null || grid.length == 0) return 0;
	
		int m = grid.length;
		int n = grid[0].length;
	
		int[][] dist = new int[m][n];
	
		for(int x = 0; x < m; x++){
			for(int y = 0; y < n; y++){
				if(x == 0 && y == 0) dist[0][0] = grid[0][0];
				else dist[x][y] = Math.min(getDist(dist, x-1, y), getDist(dist, x, y-1))  + grid[x][y];				
			}			
		}
		return dist[m-1][n-1];
	}
}
```



###  375. Guess Number Higher or Lower II - Medium

We are playing the Guess Game. The game is as follows:

I pick a number from **1** to **n**. You have to guess which number I picked.

Every time you guess wrong, I'll tell you whether the number I picked is higher or lower.

However, when you guess a particular number x, and you guess wrong, you pay **$x**. You win the game when you guess the number I picked.

**Example:**

```
n = 10, I pick 8.

First round:  You guess 5, I tell you that it's higher. You pay $5.
Second round: You guess 7, I tell you that it's higher. You pay $7.
Third round:  You guess 9, I tell you that it's lower. You pay $9.

Game over. 8 is the number I picked.

You end up paying $5 + $7 + $9 = $21.
```

Given a particular **n ≥ 1**, find out how much money you need to have to guarantee a **win**.



It is actually confusing that the example shown in the problem description is not the best stragety to guess the final target number, and the problem itself is asking for the lowest cost achieved by best guessing strategy.
The example description should be updated.

`---POSSIBLY, it can also add some example about the BEST Strategy---`
The example description should be:

first introducebest strategyto guess:

1. `for one number`, like 1, best strategy is 0$
2. `for two number`, like 3,4, best strategy is 3$, which can be understood in this way: you have two way to guess: a) start by guess 4 is the target, (the worst case is) if wrong, you get charged $4, then immediately you know 3 is the target number, get get charged $0 by guessing that, and finally you get charged $4. b) similarly, if you start by 3, (the worst case is) if wrong, you get charged $3, then you immediately know that 4 is the target number, and get charged $0 for guessing this, and finally you get charged $3. In summary:
   range ---------> best strategy cost
   3, 4 ---------> $3
   5, 6 ---------> $5
   ...
3. `for three number`, the best strategy is guess the middle number first, and (worst case is) if wrong, you get charged that middle number money, and then you immediately know what target number is by using "lower" or "higher" response, so in summary:
   range ---------> best strategy cost
   3, 4, 5 ---------> $4
   7, 8, 9 ---------> $8
   ...
4. `for more numbers`, it can simply be reduced them into smaller ranges, and here is why DP solution make more sense in solving this.
   suppose the range is [start, end]
   the strategy here is to iterate through all number possible and select it as the starting point, say for any k between start and end, the worst cost for this is: k+DP( start, k-1 ) + DP(k+1, end ), and the goal is minimize the cost, so you need the minimum one among all those k between start and end

```java
class Solution {
    int[][] DP;
    
    public int findSolution(int begin, int end){
        if(begin >= end) return 0;
        if(DP[begin][end] != 0){
            return DP[begin][end];
        }
        DP[begin][end] = Integer.MAX_VALUE;
        for(int i = begin; i <= end; i++){
            int left = findSolution(begin,i-1);
            int right = findSolution(i+1,end);
            int temp = Math.max(left,right) + i;
            DP[begin][end] = Math.min(temp,DP[begin][end]);
        }
        return DP[begin][end];
    }
    public int getMoneyAmount(int n) {
        DP = new int[n+1][n+1];
        return findSolution(1,n);
    }
}
```

![image-20191111213933557](C:\Users\73995\AppData\Roaming\Typora\typora-user-images\image-20191111213933557.png)



### 70. Climbing Stairs - Easy

You are climbing a stair case. It takes *n* steps to reach to the top.

Each time you can either climb 1 or 2 steps. In how many distinct ways can you climb to the top?

**Example 1:**

```
Input: 2
Output: 2
Explanation: There are two ways to climb to the top.
1. 1 step + 1 step
2. 2 steps
```

**Example 2:**

```
Input: 3
Output: 3
Explanation: There are three ways to climb to the top.
1. 1 step + 1 step + 1 step
2. 1 step + 2 steps
3. 2 steps + 1 step
```

 

**Constraints:**

- `1 <= n <= 45`



#### Implementation

**Algorithm**

As we can see this problem can be broken into subproblems, and it contains the optimal substructure property i.e. its optimal solution can be constructed efficiently from optimal solutions of its subproblems, we can use dynamic programming to solve this problem.

![image-20200628233818428](/Users/qinluo/Library/Application Support/typora-user-images/image-20200628233818428.png)

<iframe src="https://leetcode.com/playground/bJT3YiVD/shared" frameborder="0" width="100%" height="293" name="bJT3YiVD" style="box-sizing: border-box; margin: 20px 0px; color: rgba(0, 0, 0, 0.65); font-family: -apple-system, system-ui, &quot;Segoe UI&quot;, &quot;PingFang SC&quot;, &quot;Hiragino Sans GB&quot;, &quot;Microsoft YaHei&quot;, &quot;Helvetica Neue&quot;, Helvetica, Arial, sans-serif, &quot;Apple Color Emoji&quot;, &quot;Segoe UI Emoji&quot;, &quot;Segoe UI Symbol&quot;; font-size: 14px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; background-color: rgb(255, 255, 255); text-decoration-style: initial; text-decoration-color: initial;"></iframe>

**Complexity Analysis**

- Time complexity : O*(*n*). Single loop upto n*.
- Space complexity : O*(*n*). dp* array of size n* is used.



### 198. House Robber - Easy

You are a professional robber planning to rob houses along a street. Each house has a certain amount of money stashed, the only constraint stopping you from robbing each of them is that adjacent houses have security system connected and **it will automatically contact the police if two adjacent houses were broken into on the same night**.

Given a list of non-negative integers representing the amount of money of each house, determine the maximum amount of money you can rob tonight **without alerting the police**.

 

**Example 1:**

```
Input: nums = [1,2,3,1]
Output: 4
Explanation: Rob house 1 (money = 1) and then rob house 3 (money = 3).
             Total amount you can rob = 1 + 3 = 4.
```

**Example 2:**

```
Input: nums = [2,7,9,3,1]
Output: 12
Explanation: Rob house 1 (money = 2), rob house 3 (money = 9) and rob house 5 (money = 1).
             Total amount you can rob = 2 + 9 + 1 = 12.
```

#### Approach

**Formula Derivation**

- Let f(k) = the *largest* amount that you can rob from the first k houses
- Let Ai =  the amount at the ith house

Then, according to the problem:

- k = 1, f(1) = A1;
- k = 2, f(2) = Max(A1, A2);
- k = 3, f(3) = Max(f(2), f(1) + A3);
- ...

Generally, the formula would be: ***f(k) = Max(f(k-1), f(k-2) + Ak)***

#### Implementation

```java
class Solution {
    public int rob(int[] nums) {
        // k = -1, k = 0
        int prevMax = 0, currMax = 0;
        for (int x: nums) {
            int temp = currMax;
            currMax = Math.max(prevMax + x, currMax);
            prevMax = temp;
        }
        return currMax;
    }
}
```

**Complexity analysis**

- Time complexity : *O*(*n*). Assume that *n* is the number of houses, the time complexity is *O*(*n*).
- Space complexity : *O(1)*.

