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

