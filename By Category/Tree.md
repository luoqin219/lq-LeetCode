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