# Tree

### 104. Maximum Depth of Binary Tree

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