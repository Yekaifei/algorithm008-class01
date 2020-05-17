学习笔记

## 广度优先搜索（BFS）

广度优先搜索（Breadth-First-Search），我们平常都把简称为 BFS。直观地讲，它其实就是一种“地毯式”层层推进的搜索策略，即先查找离起始顶点最近的，然后是次近的，依次往外搜索。

广度优先搜索需要借助队列来实现，遍历得到的路径就是，起始顶点到终止顶点的最短路径。

```java
//使用Queue实现BFS
public void BFSWithQueue(TreeNode root) {
    Queue<TreeNode> queue = new LinkedList<>();
    if (root != null)
        queue.add(root);
    while (!queue.isEmpty()) {
        TreeNode treeNode = queue.poll();
 
        //在这里处理遍历到的TreeNode节点
 
        if (treeNode.left != null)
            queue.add(treeNode.left);
        if (treeNode.right != null)
            queue.add(treeNode.right);
    }
}
```



## 深度优先搜索（DFS）

深度优先搜索用的是回溯思想，非常适合用递归实现。换种说法，深度优先搜索是借助栈来实现的。在执行效率方面，深度优先和广度优先搜索的时间复杂度都是 O(E)，空间复杂度是 O(V)。

```java
//DFS递归实现
public void DFSWithRecursion(TreeNode root) {
    if (root == null)
        return;
 
    //在这里处理遍历到的TreeNode节点
    if (root.left != null)
        DFSWithRecursion(root.left);
    if (root.right != null)
        DFSWithRecursion(root.right);
}
```

