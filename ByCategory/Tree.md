# Tree

### 104. Maximum Depth of Binary Tree - Easy

Given a binary tree, find its maximum depth.

The maximum depth is the number of nodes along the longest path from the root node down to the farthest leaf node.

**Note:** A leaf is a node with no children.

**Example:**

Given binary tree `[3,9,20,null,null,15,7]`,

```
    3
   / \
  9  20
    /  \
   15   7
```

return its depth = 3.

#### Method 1: Recursion (DFS)

```java
 class Solution {
     public int maxDepth(TreeNode root) {
         if (root == null) return 0;
         else {
             int left_height = maxDepth(root.left);
             int right_height = maxDepth(root.right);
             return Math.max(left_height,right_height) + 1;
         }
     }
}
```

O(N) - N: number of nodes

O(logN)

#### Method 2: BFS

```java
class Solution {
  public int maxDepth(TreeNode root) {
        if (root == null) return 0;
        
        int depth = 0;
        Queue<TreeNode> frontier = new LinkedList<>();
        frontier.add(root);
        
        while (!frontier.isEmpty()) {          
            depth++;
            int fsize = frontier.size();
            for (int i = 0; i < fsize; i++) {
                TreeNode curr = frontier.poll();
               
                if (cur.left != null) frontier.add(curr.left);
                if (cur.right != null) frontier.add(curr.right);
            }
        }
        
        return depth;
  }
}
```

O(N)

O(N)

### 617. Merge Two Binary Trees - Easy

Given two binary trees and imagine that when you put one of them to cover the other, some nodes of the two trees are overlapped while the others are not.

You need to merge them into a new binary tree. The merge rule is that if two nodes overlap, then sum node values up as the new value of the merged node. Otherwise, the NOT null node will be used as the node of new tree.

**Example 1:**

```
Input: 
	Tree 1                     Tree 2                  
          1                         2                             
         / \                       / \                            
        3   2                     1   3                        
       /                           \   \                      
      5                             4   7                  
Output: 
Merged tree:
	     3
	    / \
	   4   5
	  / \   \ 
	 5   4   7
```

**Note:** The merging process must start from the root nodes of both trees.

#### Method 1: Recursion

```java
class Solution {
    public TreeNode mergeTrees(TreeNode t1, TreeNode t2) {
        if (t1 == null) return t2;
        if (t2 == null) return t1;
        
        t1.val += t2.val;
        t1.left = mergeTrees(t1.left, t2.left);
        t1.right = mergeTrees(t1.right, t2.right);
        
        return t1;
    }
}
```

O(M)

O(logM) in average

#### Method 2: Iterative

```java
class Solution {
    public TreeNode mergeTrees(TreeNode t1, TreeNode t2) {
        if (t1 == null) return t2;
        Stack<TreeNode[]> stack = new Stack<>();
        stack.push(new TreeNode[] {t1, t2});
        
        while(!stack.isEmpty()) {
            TreeNode[] t = stack.pop();
            if (t[0] == null || t[1] == null) continue;
            t[0].val += t[1].val;
            if (t[0].left == null) t[0].left = t[1].left;
            else stack.push(new TreeNode[] {t[0].left, t[1].left});
            if (t[0].right == null) t[0].right = t[1].right;
            else stack.push(new TreeNode[] {t[0].right, t[1].right});
        }
        
        return t1;
    }
}
```

O(M)

O(logM) in average



### 100. Same Tree - Easy

Given two binary trees, write a function to check if they are the same or not.

Two binary trees are considered the same if they are structurally identical and the nodes have the same value.

**Example 1:**

```
Input:     1         1
          / \       / \
         2   3     2   3

        [1,2,3],   [1,2,3]

Output: true
```

**Example 2:**

```
Input:     1         1
          /           \
         2             2

        [1,2],     [1,null,2]

Output: false
```

**Example 3:**

```
Input:     1         1
          / \       / \
         2   1     1   2

        [1,2,1],   [1,1,2]

Output: false
```

#### Impementation (Recursion)

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public boolean isSameTree(TreeNode p, TreeNode q) {
        if (p == null && q == null) return true;
        if (p == null || q == null) return false;
        
        if (p.val == q.val)
            return isSameTree(p.left, q.left) && isSameTree(p.right, q.right);
        else
            return false;
    }
}
```

**Complexity Analysis**

- Time complexity : O(*N*), where N is a number of nodes in the tree, since one visits each node exactly once.
- Space complexity : O(log(*N*)) in the best case of completely balanced tree and O(*N*) in the worst case of completely unbalanced tree, to keep a recursion stack.

#### Approach 2: Iteration

**Intuition**

Start from the root and then at each iteration pop the current node out of the deque. Then do the same checks as in the approach 1 :

- `p` and `p` are not `None`,
- `p.val` is equal to `q.val`,

and if checks are OK, push the child nodes.

**Implementation**

```java
class Solution {
  public boolean check(TreeNode p, TreeNode q) {
    // p and q are null
    if (p == null && q == null) return true;
    // one of p and q is null
    if (q == null || p == null) return false;
    if (p.val != q.val) return false;
    return true;
  }

  public boolean isSameTree(TreeNode p, TreeNode q) {
    if (p == null && q == null) return true;
    if (!check(p, q)) return false;

    // init deques
    ArrayDeque<TreeNode> deqP = new ArrayDeque<TreeNode>();
    ArrayDeque<TreeNode> deqQ = new ArrayDeque<TreeNode>();
    deqP.addLast(p);
    deqQ.addLast(q);

    while (!deqP.isEmpty()) {
      p = deqP.removeFirst();
      q = deqQ.removeFirst();

      if (!check(p, q)) return false;
      if (p != null) {
        // in Java nulls are not allowed in Deque
        if (!check(p.left, q.left)) return false;
        if (p.left != null) {
          deqP.addLast(p.left);
          deqQ.addLast(q.left);
        }
        if (!check(p.right, q.right)) return false;
        if (p.right != null) {
          deqP.addLast(p.right);
          deqQ.addLast(q.right);
        }
      }
    }
    return true;
  }
}
```



### 108. Convert Sorted Array to Binary Search Tree - Easy

Given an array where elements are sorted in ascending order, convert it to a height balanced BST.

For this problem, a height-balanced binary tree is defined as a binary tree in which the depth of the two subtrees of *every* node never differ by more than 1.

**Example:**

```
Given the sorted array: [-10,-3,0,5,9],

One possible answer is: [0,-3,9,-10,null,5], which represents the following height balanced BST:

      0
     / \
   -3   9
   /   /
 -10  5
```

#### How to Traverse the Tree. DFS: Preorder, Inorder, Postorder; BFS.

There are two general strategies to traverse a tree:

- *Depth First Search* (`DFS`)

  In this strategy, we adopt the `depth` as the priority, so that one would start from a root and reach all the way down to certain leaf, and then back to root to reach another branch.

  The DFS strategy can further be distinguished as `preorder`, `inorder`, and `postorder` depending on the relative order among the root node, left node and right node.

- *Breadth First Search* (`BFS`)

  We scan through the tree level by level, following the order of height, from top to bottom. The nodes on higher level would be visited before the ones with lower levels.

On the following figure the nodes are enumerated in the order you visit them, please follow `1-2-3-4-5` to compare different strategies.

![postorder](https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/Figures/108/bfs_dfs.png)



------

#### Construct BST from Inorder Traversal: Why the Solution is *Not* Unique

It's known that [inorder traversal of BST is an array sorted in the ascending order](https://leetcode.com/articles/delete-node-in-a-bst/).

Having sorted array as an input, we could rewrite the problem as *Construct Binary Search Tree from Inorder Traversal*.

> Does this problem have a unique solution, i.e. could inorder traversal be used as a unique identifier to encore/decode BST? The answer is *no*.

Here is the funny thing about BST. Inorder traversal is *not* a unique identifier of BST. At the same time both preorder and postorder traversals *are* unique identifiers of BST. [From these traversals one could restore the inorder one](https://leetcode.com/articles/construct-bst-from-preorder-traversal/): `inorder = sorted(postorder) = sorted(preorder)`, and [inorder + postorder or inorder + preorder are both unique identifiers of whatever binary tree](https://leetcode.com/articles/construct-binary-tree-from-inorder-and-postorder-t/).

So, the problem "sorted array -> BST" has multiple solutions.

![postorder](https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/Figures/108/bst2.png)

Here we have an additional condition: *the tree should be height-balanced*, i.e. the depths of the two subtrees of every node never differ by more than 1.

> Does it make the solution to be unique? Still no.

![postorder](https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/Figures/108/height.png)

Basically, the height-balanced restriction means that at each step one has to pick up the number in the middle as a root. That works fine with arrays containing odd number of elements but there is no predefined choice for arrays with even number of elements.

![postorder](https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/Figures/108/pick.png)

One could choose left middle element, or right middle one, and both choices will lead to *different* height-balanced BSTs. Let's see that in practice: in Approach 1 we will always pick up left middle element, in Approach 2 - right middle one. That will generate *different* BSTs but both solutions will be accepted.



------

#### Approach 1: Inorder Traversal: Always Choose Left Middle Node as a Root

**Algorithm**

![postorder](https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/Figures/108/left.png)

- Implement helper function `helper(left, right)`, which constructs BST from nums elements between indexes `left` and `right`:
  - If left > right, then there is no elements available for that subtree. Return None.
  - Pick left middle element: `p = (left + right) // 2`.
  - Initiate the root: `root = TreeNode(nums[p])`.
  - Compute recursively left and right subtrees: `root.left = helper(left, p - 1)`, `root.right = helper(p + 1, right)`.
- Return `helper(0, len(nums) - 1)`.

**Implementation**

<iframe src="https://leetcode.com/playground/KDzigPDd/shared" frameborder="0" width="100%" height="412" name="KDzigPDd" style="box-sizing: border-box; margin: 20px 0px; color: rgba(0, 0, 0, 0.65); font-family: -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, &quot;PingFang SC&quot;, &quot;Hiragino Sans GB&quot;, &quot;Microsoft YaHei&quot;, &quot;Helvetica Neue&quot;, Helvetica, Arial, sans-serif, &quot;Apple Color Emoji&quot;, &quot;Segoe UI Emoji&quot;, &quot;Segoe UI Symbol&quot;; font-size: 14px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; background-color: rgb(255, 255, 255); text-decoration-style: initial; text-decoration-color: initial;"></iframe>
**Complexity Analysis**

- Time complexity: O(*N*) since we visit each node exactly once.

- Space complexity: O(*N*). O(*N*) to keep the output, and O(log*N*) for the recursion stack.

  

------

#### Approach 2: Inorder Traversal: Always Choose Right Middle Node as a Root

**Algorithm**

![postorder](https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/Figures/108/right.png)

- Implement helper function `helper(left, right)`, which constructs BST from nums elements between indexes `left` and `right`:
  - If left > right, then there is no elements available for that subtree. Return None.
  - Pick right middle element:
    - `p = (left + right) // 2`.
    - If `left + right` is odd, add 1 to p-index.
  - Initiate the root: `root = TreeNode(nums[p])`.
  - Compute recursively left and right subtrees: `root.left = helper(left, p - 1)`, `root.right = helper(p + 1, right)`.
- Return `helper(0, len(nums) - 1)`.

**Implementation**

<iframe src="https://leetcode.com/playground/4iXwruvN/shared" frameborder="0" width="100%" height="429" name="4iXwruvN" style="box-sizing: border-box; margin: 20px 0px; color: rgba(0, 0, 0, 0.65); font-family: -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, &quot;PingFang SC&quot;, &quot;Hiragino Sans GB&quot;, &quot;Microsoft YaHei&quot;, &quot;Helvetica Neue&quot;, Helvetica, Arial, sans-serif, &quot;Apple Color Emoji&quot;, &quot;Segoe UI Emoji&quot;, &quot;Segoe UI Symbol&quot;; font-size: 14px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; background-color: rgb(255, 255, 255); text-decoration-style: initial; text-decoration-color: initial;"></iframe>
**Complexity Analysis**

- Time complexity: O(*N*) since we visit each node exactly once.

- Space complexity: O(*N*). O(*N*) to keep the output, and \mathcal{O}(\log N)O(log*N*) for the recursion stack.

  

------

#### Approach 3: Inorder Traversal: Choose Random Middle Node as a Root

This one is for fun. Instead of predefined choice we will pick randomly left or right middle node at each step. Each run will result in different solution and they all will be accepted.

![postorder](https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/Figures/108/random.png)

**Algorithm**

- Implement helper function `helper(left, right)`, which constructs BST from nums elements between indexes `left` and `right`:
  - If left > right, then there is no elements available for that subtree. Return None.
  - Pick random middle element:
    - `p = (left + right) // 2`.
    - If `left + right` is odd, add randomly 0 or 1 to p-index.
  - Initiate the root: `root = TreeNode(nums[p])`.
  - Compute recursively left and right subtrees: `root.left = helper(left, p - 1)`, `root.right = helper(p + 1, right)`.
- Return `helper(0, len(nums) - 1)`.

**Implementation**

<iframe src="https://leetcode.com/playground/sKR7fp7k/shared" frameborder="0" width="100%" height="446" name="sKR7fp7k" style="box-sizing: border-box; margin: 20px 0px; color: rgba(0, 0, 0, 0.65); font-family: -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, &quot;PingFang SC&quot;, &quot;Hiragino Sans GB&quot;, &quot;Microsoft YaHei&quot;, &quot;Helvetica Neue&quot;, Helvetica, Arial, sans-serif, &quot;Apple Color Emoji&quot;, &quot;Segoe UI Emoji&quot;, &quot;Segoe UI Symbol&quot;; font-size: 14px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; background-color: rgb(255, 255, 255); text-decoration-style: initial; text-decoration-color: initial;"></iframe>
**Complexity Analysis**

- Time complexity: O(*N*) since we visit each node exactly once.
- Space complexity: O(*N*). O(*N*) to keep the output, and O(log*N*) for the recursion stack.

*<u>**It should be universally accepted to use `int mid = lo + (hi - lo) / 2;` for overflow protection, or that bit-shifting one**</u>*



### 110. Balanced Binary Tree

Given a binary tree, determine if it is height-balanced.

For this problem, a height-balanced binary tree is defined as:

> a binary tree in which the left and right subtrees of *every* node differ in height by no more than 1.

 

**Example 1:**

Given the following tree `[3,9,20,null,null,15,7]`:

```
    3
   / \
  9  20
    /  \
   15   7
```

Return true.

**Example 2:**

Given the following tree `[1,2,2,3,3,null,null,4,4]`:

```
       1
      / \
     2   2
    / \
   3   3
  / \
 4   4
```

Return false.

![image-20200102141531600](C:\Users\73995\AppData\Roaming\Typora\typora-user-images\image-20200102141531600.png)

![pic](https://leetcode.com/problems/balanced-binary-tree/Figures/110/110-unbalanced-wheight-highlighted.png)

> The balanced subtree definition hints at the fact that we should treat each subtree as a subproblem. The question is: in which order should we solve the subproblems?

------

#### Approach 1: Top-down recursion

**Algorithm**

![image-20200102141616350](C:\Users\73995\AppData\Roaming\Typora\typora-user-images\image-20200102141616350.png)

```
isBalanced(root):
    if (root == NULL):
        return true
    if (abs(height(root.left) - height(root.right)) > 1):
        return false
    else:
        return isBalanced(root.left) && isBalanced(root.right)
```

<iframe src="https://leetcode.com/playground/BDAF9qQX/shared" frameborder="0" width="100%" height="463" name="BDAF9qQX" style="box-sizing: border-box; margin: 20px 0px; color: rgba(0, 0, 0, 0.65); font-family: -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, &quot;PingFang SC&quot;, &quot;Hiragino Sans GB&quot;, &quot;Microsoft YaHei&quot;, &quot;Helvetica Neue&quot;, Helvetica, Arial, sans-serif, &quot;Apple Color Emoji&quot;, &quot;Segoe UI Emoji&quot;, &quot;Segoe UI Symbol&quot;; font-size: 14px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; background-color: rgb(255, 255, 255); text-decoration-style: initial; text-decoration-color: initial;"></iframe>
**Complexity Analysis**

![image-20200101143517644](C:\Users\73995\AppData\Roaming\Typora\typora-user-images\image-20200101143517644.png)

![image-20200101143549402](C:\Users\73995\AppData\Roaming\Typora\typora-user-images\image-20200101143549402.png)

**Fun fact**: f*(*n*)=*f*(*n*−1)+*f*(*n−2)+1 is known as a [Fibonacci meanders](http://oeis.org/wiki/User:Peter_Luschny/FibonacciMeanders) sequence.



------

#### Approach 2: Bottom-up recursion

**Intuition**

In approach 1, we perform redundant calculations when computing height. In each call to height, we require that the subtree's heights also be computed. Therefore, when working top down we will compute the height of a subtree once for every parent. We can remove the redundancy by first recursing on the children of the current node and then using their computed height to determine whether the current node is balanced.

**Algorithm**

We will use the same height defined in the first approach. The bottom-up approach is a reverse of the logic of the top-down approach since we *first* check if the child subtrees are balanced *before* comparing their heights. The algorithm is as follows:

> Check if the child subtrees are balanced. If they are, use their heights to determine if the current subtree is balanced as well as to calculate the current subtree's height.

<iframe src="https://leetcode.com/playground/NpoCAWjW/shared" frameborder="0" width="100%" height="500" name="NpoCAWjW" style="box-sizing: border-box; margin: 20px 0px; color: rgba(0, 0, 0, 0.65); font-family: -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, &quot;PingFang SC&quot;, &quot;Hiragino Sans GB&quot;, &quot;Microsoft YaHei&quot;, &quot;Helvetica Neue&quot;, Helvetica, Arial, sans-serif, &quot;Apple Color Emoji&quot;, &quot;Segoe UI Emoji&quot;, &quot;Segoe UI Symbol&quot;; font-size: 14px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; background-color: rgb(255, 255, 255); text-decoration-style: initial; text-decoration-color: initial;"></iframe>
**Complexity Analysis**

- Time complexity : O(*n*)

  For every subtree, we compute its height in constant time as well as compare the height of its children.

- Space complexity : O(*n*). The recursion stack may go up to O(*n*) if the tree is unbalanced.



### 669. Trim A Binary Search Tree - Easy

Given a binary search tree and the lowest and highest boundaries as `L` and `R`, trim the tree so that all its elements lies in `[L, R]` (R >= L). You might need to change the root of the tree, so the result should return the new root of the trimmed binary search tree.

**Example 1:**

```
Input: 
    1
   / \
  0   2

  L = 1
  R = 2

Output: 
    1
      \
       2
```

**Example 2:**

```
Input: 
    3
   / \
  0   4
   \
    2
   /
  1

  L = 1
  R = 3

Output: 
      3
     / 
   2   
  /
 1
```

#### Implementation - Recursion

Given the property of a **binary search tree** (the value should be in accordance with inoder traversal), we can use recursion to solve this problem.

```java
class Solution {
    public TreeNode trimBST(TreeNode root, int L, int R) {
        if (root == null) return null;
        if (root.val < L) return trimBST(root.right, L, R);
        if (root.val > R) return trimBST(root.left, L , R);
        
        root.left = trimBST(root.left, L, R);
        root.right = trimBST(root.right, L, R);
        
        return root;
    }
}
```

**Complexity Analysis**

- Time Complexity: O*(*N*), where N* is the total number of nodes in the given tree. We visit each node at most once.
- Space Complexity: O*(*N). Even though we don't explicitly use any additional memory, the call stack of our recursion could be as large as the number of nodes in the worst case.

#### Approach 2 - Iteration

```java
class Solution {
    public TreeNode trimBST(TreeNode root, int L, int R) {
        if (root == null) return root;
        //Find a valid root which is used to return.
        while (root != null && (root.val < L || root.val > R)) {
  			if (root.val < L) root = root.right;
		    else if (root.val > R) root = root.left;
		}
        TreeNode dummy = root;
        // Remove the invalid nodes from left subtree.
        while (dummy != null) {
            while (dummy.left != null && dummy.left.val < L) {
                dummy.left = dummy.left.right; // If the left child is smaller than L, then we just keep the right subtree of it. 
            }
            dummy = dummy.left;
        }
        dummy = root;
        // Remove the invalid nodes from right subtree
        while (dummy != null) {
            while (dummy.right != null && dummy.right.val > R) {
                dummy.right = dummy.right.left; // If the right child is biggrt than R, then we just keep the left subtree of it. 
            }
            dummy = dummy.right;
        }
        return root;
    }
}
```

O(N): we visit all nodes once.

O(N): in the worst case, we could use dummy node to indicate all nodes.



### 572. Subtree of Another Tree - Easy

Given two non-empty binary trees **s** and **t**, check whether tree **t** has exactly the same structure and node values with a subtree of **s**. A subtree of **s** is a tree consists of a node in **s** and all of this node's descendants. The tree **s** could also be considered as a subtree of itself.

**Example 1:**
Given tree s:

```
     3
    / \
   4   5
  / \
 1   2
```

Given tree t:

```
   4 
  / \
 1   2
```

Return **true**, because t has the same structure and node values with a subtree of s.



**Example 2:**
Given tree s:

```
     3
    / \
   4   5
  / \
 1   2
    /
   0
```

Given tree t:

```
   4
  / \
 1   2
```

Return **false**.



#### Implementation - Recursion

```java
class Solution {
    public boolean isSubtree(TreeNode s, TreeNode t) {
        if (s != null && t == null) return true;
        if (s == null && t != null) return false;
        return equals(s, t) || isSubtree(s.left, t) || isSubtree(s.right, t);
    }
    
    public boolean equals(TreeNode s, TreeNode t) {
        if (s == null && t == null) return true;
        if (s == null || t == null) return false;
        return s.val == t.val && equals(s.left, t.left) && equals(s.right, t.right);
    }
}
```

- Time complexity : O*(*m*∗*n) in worst case(skewed tree). In the worst case, the equals method is called for each node. The equals method will be O(m) w/ m being the size of the smaller tree. You are calling this n times w/ n being the size of the larger tree, so O(m)*O(n) = O(m*n)
- Space complexity : O*(*n*) in worst case. The depth of the recursion tree can go upto n*. n refers to the number of nodes in s.

#### Approach 2 - Using Preorder Traversal

**Algorithm**

![image-20200111225219427](C:\Users\73995\AppData\Roaming\Typora\typora-user-images\image-20200111225219427.png)

![Preorder_null](https://leetcode.com/problems/subtree-of-another-tree/Figures/572_Subtree_1.PNG)

![Preorder_lnull_rnull](https://leetcode.com/problems/subtree-of-another-tree/Figures/572_Subtree_2.PNG)

<iframe src="https://leetcode.com/playground/cagXWqSv/shared" frameborder="0" name="cagXWqSv" width="100%" height="513" style="box-sizing: border-box; margin: 20px 0px; color: rgba(0, 0, 0, 0.65); font-family: -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, &quot;PingFang SC&quot;, &quot;Hiragino Sans GB&quot;, &quot;Microsoft YaHei&quot;, &quot;Helvetica Neue&quot;, Helvetica, Arial, sans-serif, &quot;Apple Color Emoji&quot;, &quot;Segoe UI Emoji&quot;, &quot;Segoe UI Symbol&quot;; font-size: 14px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; background-color: rgb(255, 255, 255); text-decoration-style: initial; text-decoration-color: initial;"></iframe>

**Complexity Analysis**

![image-20200111225826600](C:\Users\73995\AppData\Roaming\Typora\typora-user-images\image-20200111225826600.png)

String Concatenation is O(n^2)

**Approach 2 Follow-up: Optimized Preorder Traversal Solution**

```java
class Solution {
    public boolean isSubtree(TreeNode s, TreeNode t) {
        return serialize(s).contains(serialize(t)); // Java use naive solution that cause O(M*N). Replace with KMP algorithm to ensure O(M+N)
    }

    private String serialize(TreeNode root) {
        StringBuilder builder = new StringBuilder();
        serialize(root, builder);
        return builder.toString();
    }
    
    private void serialize(TreeNode root, StringBuilder builder) {
        if (root == null) {
            builder.append(",#");
        } else {
            builder.append("," + root.val);
            serialize(root.left, builder);
            serialize(root.right, builder);
        }
    }
}
```

Complexity:

- Time: O(M+N), M is the number of nodes of tree `s`, N is the number of nodes of tree `t`
- Space: O(M+N)





### 987. Vertical Order Traversal of a Binary Tree - Medium (?)

Given a binary tree, return the *vertical order* traversal of its nodes values.

For each node at position `(X, Y)`, its left and right children respectively will be at positions `(X-1, Y-1)` and `(X+1, Y-1)`.

Running a vertical line from `X = -infinity` to `X = +infinity`, whenever the vertical line touches some nodes, we report the values of the nodes in order from top to bottom (decreasing `Y` coordinates).

If two nodes have the same position, then the value of the node that is reported first is the value that is smaller.

Return an list of non-empty reports in order of `X` coordinate. Every report will have a list of values of nodes.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2019/01/31/1236_example_1.PNG)

```
Input: [3,9,20,null,null,15,7]
Output: [[9],[3,15],[20],[7]]
Explanation: 
Without loss of generality, we can assume the root node is at position (0, 0):
Then, the node with value 9 occurs at position (-1, -1);
The nodes with values 3 and 15 occur at positions (0, 0) and (0, -2);
The node with value 20 occurs at position (1, -1);
The node with value 7 occurs at position (2, -2).
```

**Example 2:**

**![img](https://assets.leetcode.com/uploads/2019/01/31/tree2.png)**

```
Input: [1,2,3,4,5,6,7]
Output: [[4],[2],[1,5,6],[3],[7]]
Explanation: 
The node with value 5 and the node with value 6 have the same position according to the given scheme.
However, in the report "[1,5,6]", the node value of 5 comes first since 5 is smaller than 6.
```

 

**Note:**

1. The tree will have between 1 and `1000` nodes.
2. Each node's value will be between `0` and `1000`.

#### Key Points

In this problem, we are asked to return the **vertical** order of a binary tree, which implies three sub-orders (denoted as `<column, row, value>`) as follows:

- **column-wise order**

  First, we look at a binary tree *horizontally*. Each node would be aligned to a specific `column`, based on its relative *offset* to the root node of the tree.

  Let us assume that the root node has a column index of `0`, then its left child node would have a column index of `-1`, and its right child node would have a column index of `+1`, and so on.

- **row-wise order**

  Secondly, we look at the binary tree *vertically*. Each node would be assigned to a specific `row`, based on its *level* (*i.e.* the vertical distance to the root node).

  Let us assume that the root node has a row index of `0`, then both its child nodes would have the row index of `1`. Note that the convention we adopt here is slightly different from the one in the problem description where the row index of a parent node is larger than the one of its child nodes. This, though, would not jeopardize our solution. On the contrary, it would help us to simplify the solution, as one will see later.

- **value-wise order**

  Finally, given the definitions of the above two sub-orders, there could be a case where two different nodes have the same `<column, row>` index. As a result, to resolve the draw situation, as stated in the problem description, the node that has a smaller `value` should come first.

> Given the above definitions, we can now formulate the problem as a task to **sort** the nodes based on the 3-dimensional coordinates (*i.e.* `<column, row, value>`) that we defined above.
>
> 

#### Approach 1: BFS/DFS with Global Sorting

The idea to solve the problem would be as intuitive as **building** a list where each element in the list corresponds to the 3-dimensional coordinates of each node in the tree, and then **sorting** the list based on the coordinates.

**Algorithm**

Based on the above intuition, we could implement the solution in 3 simple steps:

- **Step 1)**: we traverse the input tree either by BFS or DFS, in order to generate a list that contains the 3-dimensional coordinates (*i.e.* `<column, row, value>`) of each node.
  - Note that, we assign a higher `row` index value to a node's child node. This convention is at odds with the denotation given in the problem description. This is done *intentionally*, in order to keep the ordering of all coordinates consistent, *i.e.* a lower value in any specific coordinate represents a higher order. As a result, a sorting operation in ascending order would work for each coordinate consistently.
- **Step 2)**: Once we generate the desired list, we then *sort* the list.
- **Step 3)**: From the *sorted* list, we then extract the results, and group them by the `column` index.

In the following, we give some sample implementations with both the BFS traversal and the DFS traversal.

#### Implementation 1: BFS

```java
class Triplet<F, S, T> {
    public F first;
    public S second;
    public T third;

    public Triplet(F first, S second, T third) {
        this.first = first;
        this.second = second;
        this.third = third;
    }
}

class Solution {
    // record the coordinate <column, row, node.val>
    List<Triplet<Integer, Integer, Integer>> nodeList = new ArrayList<>();

    // iterate using bfs to give each node a coordinate
    private void BFS(TreeNode root) {
        Queue<Triplet<TreeNode, Integer, Integer>> queue = new ArrayDeque();
        int row = 0, column = 0;
        queue.offer(new Triplet(root, row, column));

        while (!queue.isEmpty()) {
            Triplet<TreeNode, Integer, Integer> triplet = queue.poll();
            root = triplet.first;
            row = triplet.second;
            column = triplet.third;

            if (root != null) {
                this.nodeList.add(new Triplet(column, row, root.val));
                queue.offer(new Triplet(root.left, row + 1, column - 1));
                queue.offer(new Triplet(root.right, row + 1, column + 1));
            }
        }
    }

    public List<List<Integer>> verticalTraversal(TreeNode root) {

        List<List<Integer>> output = new ArrayList();
        if (root == null) {
            return output;
        }

        // step 1). BFS traversal
        BFS(root);

        // step 2). sort the global list by <column, row, value>
        Collections.sort(this.nodeList, new Comparator<Triplet<Integer, Integer, Integer>>() {
            @Override
            public int compare(Triplet<Integer, Integer, Integer> t1,
                    Triplet<Integer, Integer, Integer> t2) {
                if (t1.first.equals(t2.first))
                    if (t1.second.equals(t2.second))
                        return t1.third - t2.third;
                    else
                        return t1.second - t2.second;
                else
                    return t1.first - t2.first;
            }
        });

        // step 3). extract the values, partitioned by the column index.
        List<Integer> currColumn = new ArrayList();
        Integer currColumnIndex = this.nodeList.get(0).first;

        for (Triplet<Integer, Integer, Integer> triplet : this.nodeList) {
            Integer column = triplet.first, value = triplet.third;
            if (column == currColumnIndex) {
                currColumn.add(value);
            } else {
                output.add(currColumn);
                currColumnIndex = column;
                currColumn = new ArrayList();
                currColumn.add(value);
            }
        }
        output.add(currColumn);

        return output;
    }
}
```

#### Implementation 2: DFS

```java
class Triplet<F, S, T> {
    public final F first;
    public final S second;
    public final T third;

    public Triplet(F first, S second, T third) {
        this.first = first;
        this.second = second;
        this.third = third;
    }
}

class Solution {
    List<Triplet<Integer, Integer, Integer>> nodeList = new ArrayList<>();

    private void DFS(TreeNode node, Integer row, Integer column) {
        if (node == null)
            return;
        nodeList.add(new Triplet(column, row, node.val));
        // preorder DFS traversal
        this.DFS(node.left, row + 1, column - 1);
        this.DFS(node.right, row + 1, column + 1);
    }

    public List<List<Integer>> verticalTraversal(TreeNode root) {
        List<List<Integer>> output = new ArrayList();
        if (root == null) {
            return output;
        }

        // step 1). DFS traversal
        DFS(root, 0, 0);

        // step 2). sort the list by <column, row, value>
        Collections.sort(this.nodeList, new Comparator<Triplet<Integer, Integer, Integer>>() {
            @Override
            public int compare(Triplet<Integer, Integer, Integer> t1,
                    Triplet<Integer, Integer, Integer> t2) {
                if (t1.first.equals(t2.first))
                    if (t1.second.equals(t2.second))
                        return t1.third - t2.third;
                    else
                        return t1.second - t2.second;
                else
                    return t1.first - t2.first;
            }
        });

        // step 3). extract the values, grouped by the column index.
        List<Integer> currColumn = new ArrayList();
        Integer currColumnIndex = this.nodeList.get(0).first;

        for (Triplet<Integer, Integer, Integer> triplet : this.nodeList) {
            Integer column = triplet.first, value = triplet.third;
            if (column == currColumnIndex) {
                currColumn.add(value);
            } else {
                output.add(currColumn);
                currColumnIndex = column;
                currColumn = new ArrayList();
                currColumn.add(value);
            }
        }
        output.add(currColumn);

        return output;
    }
}
```

**Complexity Analysis**

Let *N* be the number of nodes in the input tree.

- Time Complexity: O(*N*log*N*), which applies to both the BFS and DFS approaches.
  - In the first step of the algorithm, we traverse the input tree with either BFS or DFS, which would take O(*N*) time.
  - Secondly, we sort the obtained list of coordinates which contains *N* elements. The sorting operation would take O(*N*log*N*) time.
  - Finally, we extract the results from the sorted list, which would take another O(*N*) time.
  - To summarize, the overall time complexity of the algorithm would be O(*N*log*N*), which is dominated by the sorting operation in the second step.
- Space Complexity: O(*N*). Again this applies to both the BFS and DFS approaches.
  - In the first step of the algorithm, we build a list that contains the coordinates of all the nodes. Hence, we need O(*N*) space for this list.
  - Additionally, for the BFS approach, we used a queue data structure to maintain the order of visits. At any given moment, the queue contains no more than **two levels** of nodes in the tree. The maximal number of nodes at one level is upper N/2, which is the number of the leaf nodes in a balanced binary tree. As a result, the space needed for the queue would be O(*N/2*⋅2)=O(*N*).
  - Although we don't need the queue data structure for the DFS approach, the recursion in the DFS approach incurs some additional memory consumption on the function call stack. In the worst case, the input tree might be completely imbalanced, *e.g.* each node has only the left child node. In this case, the recursion would occur up to *N* times, which in turn would consume O(*N*) space in the function call stack.
  - To summarize, the space complexity for the BFS approach would be O(*N*)+O(*N*)=O(*N*). And the same applies to the DFS approach.



### 437. Path Sum 3 - Medium

You are given a binary tree in which each node contains an integer value.

Find the number of paths that sum to a given value.

The path does not need to start or end at the root or a leaf, but it must go downwards (traveling only from parent nodes to child nodes).

The tree has no more than 1,000 nodes and the values are in the range -1,000,000 to 1,000,000.

**Example:**

```
root = [10,5,-3,3,2,null,11,3,-2,null,1], sum = 8

      10
     /  \
    5   -3
   / \    \
  3   2   11
 / \   \
3  -2   1

Return 3. The paths that sum to 8 are:

1.  5 -> 3
2.  5 -> 2 -> 1
3. -3 -> 11
```

#### Approach 1: Prefix Sum

**Definition**: *Prefix sum* is a sum of the current value with all previous elements starting from the beginning of the structure.

It could be defined for 1D arrays (sum the current value with all the previous integers),

![append](https://leetcode.com/problems/path-sum-iii/Figures/437/prefix_qd.png) *Figure 1. Prefix sum for 1D array.*

for 2D arrays (sum of the current value with the integers above or on the left)

![append](https://leetcode.com/problems/path-sum-iii/Figures/437/2d_prefix.png) *Figure 2. Prefix sum for 2D array.*

or for the binary trees (sum the values of the current node
and all parent nodes),

![append](https://leetcode.com/problems/path-sum-iii/Figures/437/tree2.png) *Figure 3. Prefix sum for the binary tree.*





------

#### Prefix Sum: How to Use: Number of Continuous Subarrays that Sum to Target

You might want to use the prefix sum technique for the problems like "Find a number of *continuous* subarrays/submatrices/tree paths that sum to target".

Before going to the current problem with the tree, let's check the idea on a simpler example [Find a number of continuous subarrays that sum to target](https://leetcode.com/problems/subarray-sum-equals-k/).

- Use a variable to track the current prefix sum and a hashmap "prefix sum -> how many times was it seen so far".

![append](https://leetcode.com/problems/path-sum-iii/Figures/437/array1.png) *Figure 4. Find a number of continuous subarrays that sum to target.*

- Parse the input structure and count the requested subarrays/submatrices/tree paths along the way with the help of that hashmap. How to count?

There could be two situations. In situation 1, the subarray with the target sum starts from the beginning of the array. That means that the current prefix sum is equal to the target sum, and we increase the counter by 1.

![append](https://leetcode.com/problems/path-sum-iii/Figures/437/situation11.png) *Figure 5. Situation 1: The subarray starts from the beginning of the array.*

In situation 2, the subarray with the target sum starts somewhere in the middle. That means we should add to the counter the number of times we have seen the prefix sum `curr_sum - target` so far: `count += h[curr_sum - target]`.

The logic is simple: the current prefix sum is `curr_sum`, and some elements before the prefix sum was `curr_sum - target`. All the elements in between sum up to `curr_sum - (curr_sum - target) = target`.

![append](https://leetcode.com/problems/path-sum-iii/Figures/437/situation24.png) *Figure 6. Situation 2: The subarray starts somewhere in the middle.*

**Algorithm**

- Let's initialize tree paths counter `count = 0`, and the hashmap `h` "prefix sum -> how many times was it seen so far".

- One could parse the tree using [recursive preorder traversal](https://leetcode.com/articles/sum-root-to-leaf-numbers/): node -> left -> right: `preorder(node: TreeNode, curr_sum: int) -> None`. This function takes two arguments: a tree node and a prefix sum before that node. To start the recursion chain, one should call `preorder(root, 0)`.

  - The first thing is to update the current prefix sum by adding the value of the current node: `curr_sum += node.val`.

  - Now one could update the counter. One should consider two situations.

    In situation 1, the tree path with the target sum starts from the root. That means the current prefix sum is equal to the target sum `curr_sum == k`, so one should increase the counter by 1: `count += 1`.

    In situation 2, the tree path with the target sum starts somewhere downwards. That means we should add to the counter the number of times we have seen the prefix sum `curr_sum - target` so far: `count += h[curr_sum - target]`.

    The logic is simple: the current prefix sum is `curr_sum`, and several elements before the prefix sum was `curr_sum - target`. All the elements in between sum up to `curr_sum - (curr_sum - target) = target`.

  - Now it's time to update the hashmap: `h[curr_sum] += 1`.

  - Let's parse left and right subtrees: `preorder(node.left, curr_sum)`, `preorder(node.right, curr_sum)`.

  - Now the current subtree is processed. It's time to remove the current prefix sum from the hashmap, in order not to blend the parallel subtrees: `h[curr_sum] -= 1`.

- Now the preorder traversal is done, and the counter is updated. Return it.

![append](https://leetcode.com/problems/path-sum-iii/Figures/437/one_vs_two.png) *Figure 7. Situation 1 vs situation 2.*

#### Implementation

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    int count = 0;
    int k;
    HashMap<Integer, Integer> h = new HashMap();
    
    public void preorder(TreeNode node, int currSum) {
        if (node == null) return;
        
        // current prefix sum
        currSum += node.val;

        // here is the sum we're looking for
        if (currSum == k) count++;
        
        // number of times the curr_sum − k has occured already, 
        // determines the number of times a path with sum k 
        // has occured upto the current node
        count += h.getOrDefault(currSum - k, 0);
        
        // add the current sum into hashmap
        // to use it during the child nodes processing
        h.put(currSum, h.getOrDefault(currSum, 0) + 1);

        // process left subtree
        preorder(node.left, currSum);
        // process right subtree
        preorder(node.right, currSum);

        // remove the current sum from the hashmap
        // in order not to use it during 
        // the parallel subtree processing
        h.put(currSum, h.get(currSum) - 1);
    }    
            
    public int pathSum(TreeNode root, int sum) {
        k = sum;
        preorder(root, 0);
        return count;
    }
}
```

**Complexity Analysis**

- Time complexity: O(*N*), where *N* is a number of nodes. During preorder traversal, each node is visited once.
- Space complexity: up to O(*N*) to keep the hashmap of prefix sums, where *N* is a number of nodes.

