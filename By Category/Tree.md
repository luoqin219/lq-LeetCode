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