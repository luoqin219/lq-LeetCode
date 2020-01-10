# Divide and Conquer

### 218. The Skyline Problem - Hard

A city's skyline is the outer contour of the silhouette formed by all the buildings in that city when viewed from a distance. Now suppose you are **given the locations and height of all the buildings** as shown on a cityscape photo (Figure A), write a program to **output the skyline** formed by these buildings collectively (Figure B).

[<img src="https://assets.leetcode.com/uploads/2018/10/22/skyline1.png" alt="Buildings" style="zoom: 33%;" /> ](https://leetcode.com/static/images/problemset/skyline1.jpg)[<img src="https://assets.leetcode.com/uploads/2018/10/22/skyline2.png" alt="Skyline Contour" style="zoom: 33%;" />](https://leetcode.com/static/images/problemset/skyline2.jpg)

The geometric information of each building is represented by a triplet of integers `[Li, Ri, Hi]`, where `Li` and `Ri` are the x coordinates of the left and right edge of the ith building, respectively, and `Hi` is its height. It is guaranteed that `0 ≤ Li, Ri ≤ INT_MAX`, `0 < Hi ≤ INT_MAX`, and `Ri - Li > 0`. You may assume all buildings are perfect rectangles grounded on an absolutely flat surface at height 0.

For instance, the dimensions of all buildings in Figure A are recorded as: `[ [2 9 10], [3 7 15], [5 12 12], [15 20 10], [19 24 8] ] `.

The output is a list of "**key points**" (red dots in Figure B) in the format of `[ [x1,y1], [x2, y2], [x3, y3], ... ]` that uniquely defines a skyline. **A key point is the left endpoint of a horizontal line segment**. Note that the last key point, where the rightmost building ends, is merely used to mark the termination of the skyline, and always has zero height. Also, the ground in between any two adjacent buildings should be considered part of the skyline contour.

For instance, the skyline in Figure B should be represented as:`[ [2 10], [3 15], [7 12], [12 0], [15 10], [20 8], [24, 0] ]`.

**Notes:**

- The number of buildings in any input list is guaranteed to be in the range `[0, 10000]`.
- The input list is already sorted in ascending order by the left x position `Li`.
- The output list must be sorted by the x position.
- There must be no consecutive horizontal lines of equal height in the output skyline. For instance, `[...[2 3], [4 5], [7 5], [11 5], [12 7]...]` is not acceptable; the three lines of height 5 should be merged into one in the final output as such: `[...[2 3], [4 5], [12 7], ...]`

#### Solution

#### Approach 1: Divide and Conquer

**Solution template**

The problem is a classical example of **divide and conquer** approach, and typically implemented exactly the same way as **merge sort** algorithm.

Let's follow here a solution template for divide and conquer problems :

- Define the base case(s).
- Split the problem into subproblems and solve them recursively.
- Merge the subproblems solutions into the problem solution.

**Algorithm**

getSkyline for `n` buildings :

- If `n == 0` : return an empty list.
- If `n == 1` : return the skyline for one building (it's straightforward).
- `leftSkyline` = getSkyline for the first n/2 buildings.
- `rightSkyline` = getSkyline for the last n/2 buildings.
- Merge `leftSkyline` and `rightSkyline`.

Now let's discuss each step in more details.

**Base cases**

The first base case is an empty `buildings` list. Then the skyline is an empty list, too.

The second base case is the only one building in the list, when the skyline construction is quite straightforward.

![bla](https://leetcode.com/problems/the-skyline-problem/Figures/218/base5.png)

**How to split the problem**

The idea is the same as for merge sort : at each step split the list exactly in two parts : from `0` to `n/2` and from `n/2` to `n`, and then construct the skylines recursively for each part.

![bla](https://leetcode.com/problems/the-skyline-problem/Figures/218/split5.png)

**How to merge two skylines**

The algorithm for merge function is quite straightforward and based on the same merge sort logic : the height of an output skyline is always a maximum between the left and right skylines.

![bla](https://leetcode.com/problems/the-skyline-problem/Figures/218/merge5.png)

Let's use here two pointers `pR` and `pL` to track the current element index in both skylines, and three integers `leftY`, `rightY`, and `currY` to track the current height for the `left` skyline, `right` skyline and the merged skyline.

mergeSkylines (left, right) :

- currY = leftY = rightY = 0
- While we're in the region where both skylines are present (`pR < nR` and `pL < nL`) :
  - Pick up the element with the smallest `x` coordinate. If it's an element from the left skyline, move `pL` and update `leftY`. If it's an element from the right skyline, move `pR` and update `rightY`.
  - Compute the largest height at the current point : `maxY = max(leftY, rightY)`.
  - Update an output skyline by `(x, maxY)` point, if `maxY` is not equal to `currY`.
- While there are still elements in the left skyline (`pL < nL`), process them following the same logic as above.
- While there are still elements in the right skyline (`pR < nR`), process them following the same logic as above.
- Return output skyline.

> Here are three usecases to illustrate the merge algorithm execution

![bla](https://leetcode.com/problems/the-skyline-problem/Figures/218/merge3.png)

![bla](https://leetcode.com/problems/the-skyline-problem/Figures/218/merge4.png)

![bla](https://leetcode.com/problems/the-skyline-problem/Figures/218/merge7.png)

**Implementation**

<iframe src="https://leetcode.com/playground/KffyCwen/shared" frameborder="0" width="100%" height="500" name="KffyCwen" style="box-sizing: border-box; margin: 20px 0px; color: rgba(0, 0, 0, 0.65); font-family: -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, &quot;PingFang SC&quot;, &quot;Hiragino Sans GB&quot;, &quot;Microsoft YaHei&quot;, &quot;Helvetica Neue&quot;, Helvetica, Arial, sans-serif, &quot;Apple Color Emoji&quot;, &quot;Segoe UI Emoji&quot;, &quot;Segoe UI Symbol&quot;; font-size: 14px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; background-color: rgb(255, 255, 255); text-decoration-style: initial; text-decoration-color: initial;"></iframe>



- Time complexity : O(*N*log*N*), where *N* is number of buildings. The problem is an example of [Master Theorem case II](https://en.wikipedia.org/wiki/Master_theorem_(analysis_of_algorithms)#Case_2_example) : T*(*N*)=2*T*(2*N*)+2*N*, that results in O(*N*log*N) time complexity.
- Space complexity : O(*N*) to keep the output.