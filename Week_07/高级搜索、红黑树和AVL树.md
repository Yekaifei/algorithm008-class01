## **高级搜索**

### 初级搜索

1. 朴素搜索

2. 优化方向： 不重复(fibonacci)、剪枝(生成括号问题)

3. 搜索方向：

   **DFS、BFS 		双向搜索、启发式搜索**

### 剪枝实战题目

#### [22. 括号生成](https://leetcode-cn.com/problems/generate-parentheses/)

```java
class Solution {
    public List<String> generateParenthesis(int n) {
        List<String> list = new ArrayList<>();
        recur(0, 0, n, "", list);
        return list;
    }

    public void recur(int left, int right, int n, String s, List<String> list) {
        if (left == n && right == n) list.add(s);
        if (left < n) recur(left + 1, right, n, s + "(", list);
        if (right < left) recur(left , right + 1, n, s + ")", list);
    }
}
```

#### [51. N皇后](https://leetcode-cn.com/problems/n-queens/)

```java
public class Solution {
    private Set<Integer> col = new HashSet<Integer>();
    private Set<Integer> diag1 = new HashSet<Integer>();
    private Set<Integer> diag2 = new HashSet<Integer>();
    
    public List<List<String>> solveNQueens(int n) {
        List<List<String>> res = new ArrayList<List<String>>();
        dfs(res,new ArrayList<String>(), 0, n);
        return res;
    }
    private void dfs(List<List<String>> res, List<String> list, int row, int n){
        if (row == n){
            res.add(new ArrayList<String>(list));
            return;
        }
        for (int i = 0; i < n; i++){
            if (col.contains(i) || diag1.contains(row + i) || diag2.contains(row - i)) continue;
            
            char[] charArray = new char[n];
            Arrays.fill(charArray, '.');
            charArray[i] = 'Q';
            String rowString = new String(charArray);
            
            list.add(rowString);
            col.add(i);
            diag1.add(row + i);
            diag2.add(row - i);
            
            dfs(res, list, row + 1, n);
            
            list.remove(list.size() - 1);
            col.remove(i);
            diag1.remove(row + i);
            diag2.remove(row - i);
        }
    }
}
```

#### [36. 有效的数独](https://leetcode-cn.com/problems/valid-sudoku/)

```java
class Solution {
    public boolean isValidSudoku(char[][] board) {
        Set seen = new HashSet();
        for (int i=0; i<9; ++i) {
            for (int j=0; j<9; ++j) {
                char number = board[i][j];
                if (number != '.')
                    if (!seen.add(number + " in row " + i) ||
                        !seen.add(number + " in column " + j) ||
                        !seen.add(number + " in block " + i/3 + "-" + j/3))
                        return false;
            }
        }
        return true;
    }
}
```

```java
class Solution {
    private final int l = 9;
    public boolean isValidSudoku(char[][] board) {
        boolean[][] rows = new boolean[l][l];
        boolean[][] cols = new boolean[l][l];
        boolean[][] boxes = new boolean[l][l];
        int n;

        for (int i = 0; i < l; ++i) {
            for (int j = 0; j < l; ++j) {
                if (board[i][j] != '.') {
                    n = board[i][j]-'1';
                    if (rows[i][n] || cols[j][n] || boxes[(i/3) * 3 + j /3][n]) 
                      return false;
                    rows[i][n] = true;
                    cols[j][n] = true;
                    boxes[(i/3) * 3 + j /3][n] = true;
                }
            }
        }
        return true;
    }
}
```



要求行,列,方块种不能有重复的数字, 所以采用boolean二维数组保存
拿boolean[][] rows来举例
row标识对应的 行
col标识出现的 数字
value标识是否出现

#### [37. 解数独](https://leetcode-cn.com/problems/sudoku-solver/)

```java
class Solution {
    public void solveSudoku(char[][] board) {
        if (board == null || board.length != 9 || board[0].length != 9) return;
        boolean[][] row = new boolean[9][9], col = new boolean[9][9], box = new boolean[9][9];
        for (int i = 0; i < 9; i++) {
            for (int j = 0; j < 9; j++) {
                if (board[i][j] == '.') continue;
                int num = board[i][j] - '1', k = (i / 3) * 3 + j / 3;
                row[i][num] = col[j][num] = box[k][num] = true;
            }
        }
        solveSudokuHelper(board, 0, row, col, box);
    }

    boolean solveSudokuHelper(char[][] board, int n, boolean[][] row, boolean[][] col, boolean[][] box) {
        if (n == 81) return true;
        int i = n / 9, j = n % 9;
        if (board[i][j] != '.') return solveSudokuHelper(board, n + 1, row, col, box);

        int k = (i / 3) * 3 + j / 3;
        for (int num = 0; num < 9; num++) {
            if (row[i][num] || col[j][num] || box[k][num]) continue;
            board[i][j] = (char) (num + '1');
            row[i][num] = col[j][num] = box[k][num] = true;
            if (solveSudokuHelper(board, n + 1, row, col, box)) return true;
            row[i][num] = col[j][num] = box[k][num] = false;
        }
        board[i][j] = '.';
        return false;
    }
}
```

### 双向BFS

#### [127. 单词接龙](https://leetcode-cn.com/problems/word-ladder/)

```java
class Solution {
    public int ladderLength(String beginWord, String endWord, List<String> wordList) {
        HashSet<String> start = new HashSet<>();
        start.add(beginWord);
        HashSet<String> end = new HashSet<>();
        end.add(endWord);
        HashSet<String> words = new HashSet<>(wordList); 
        if (!words.contains(endWord)) {
            return 0;
        }

        return deBfs(start,end,words,2);
    }
    private int deBfs(HashSet<String> start, HashSet<String> end, HashSet<String> words, int depth) {
        
        if (start.size() > end.size()) {
            return deBfs(end, start, words, depth);
        }
        words.removeAll(start);
        HashSet<String> next = new HashSet<>();
        for (String str : start) {
            char[] chars = str.toCharArray();
            for (int i = 0; i < chars.length; i++) {
                char temp = chars[i];
                for (char j = 'a'; j <= 'z'; j++) {
                    chars[i] = j;
                    String word = new String(chars);
                    if (words.contains(word)) {
                        if (end.contains(word)) {
                            return depth;
                        } 
                        next.add(word);
                    }
                }
                chars[i] = temp;
            }
        }
        if (start.isEmpty()) {
            return 0;
        } 
        return deBfs(next, end, words, depth + 1);

    }
}
```

#### [433. 最小基因变化](https://leetcode-cn.com/problems/minimum-genetic-mutation/)

```java
class Solution {
    public int minMutation(String start, String end, String[] bank) {
        if (start == end) return 0;
        Set<String> bankSet = new HashSet<>();
        for (String s : bank) bankSet.add(s);
        char[] charSet = new char[]{'A', 'C', 'G', 'T'};
        Set<String> visited = new HashSet<>();
        Queue<String> queue = new LinkedList<>();
        queue.add(start);
        visited.add(start);
        int count = 0;
        while (!queue.isEmpty()) {
            int size = queue.size();
            while (size-- > 0) {
                String currString = queue.poll();
                if (currString.equals(end)) return count;
                char[] currChar = currString.toCharArray();
                for (int i = 0; i < currChar.length; ++i) {
                    char old = currChar[i];
                    for (char c : charSet) {
                        currChar[i] = c;
                        String tmp = new String(currChar);
                        if (bankSet.contains(tmp) && !visited.contains(tmp)) {
                            queue.add(tmp);
                            visited.add(tmp);
                        }
                    }
                    currChar[i] = old;
                }
            }
            count++;
        }
        return -1;
    }
}
```

```java
// 自己实现的，但是错了
class Solution {
    char[] char_store;
    public int minMutation(String start, String end, String[] bank) {
        Set<String> startSet = new HashSet<>();
        startSet.add(start);
        Set<String> endSet = new HashSet<>();
        endSet.add(end);
        Set<String> banks = new HashSet<>();
        for (String s : bank) banks.add(s);
        if (!banks.contains(end)) return -1;
        char_store = new char[]{'A', 'C', 'G', 'T'};
        return deBFS(startSet, endSet, banks, 2);
    }

    private int deBFS(HashSet<String> startSet, HashSet<String> endSet,HashSet<String> banks, int depth) {
        if (startSet.size() > endSet.size()) deBFS(endSet, startSet, banks, depth);
        banks.removeAll(startSet);
        HashSet<String> next = new HashSet<>();
        for (String str : startSet) {
            char[] chars = str.toCharArray();
            for (int i = 0; i < chars.length; ++i) {
                char temp = chars[i];
                for (int j = 0; j < char_store.length; ++j) {
                    chars[i] = char_store[j];
                    String word = new String(chars);
                    if (banks.contains(word)) {
                        if (endSet.contains(word)) return depth;
                        next.add(word);
                    }
                    char[i] = temp;
                }
            }
        }
        if (start.isEmpty()) return 0;
        return deBFS(next, endSet, banks, depth + 1);
    }
}
```

```java
class Solution {
    public int minMutation(String start, String end, String[] bank) {
        recurse(start, end, bank, 0, new HashSet<String>());
        return count == Integer.MAX_VALUE ? -1 : count;
    }
    int count = Integer.MAX_VALUE;
    private void recurse(String start, String end, String[] bank, int soFar, Set<String> visited) {
        if(start.intern() == end.intern()) {
            count = Math.min(count, soFar);
        }
        
        for(String e : bank) {
            int diff = 0;
            for(int i = 0; i < e.length(); i++) {
                if(start.charAt(i) != e.charAt(i)) {
                    diff++;
                    if(diff > 1) break;
                }
            }
            if(diff == 1 && !visited.contains(e)) {
                visited.add(e);
                recurse(e, end, bank, soFar+1, visited);
                visited.remove(e);
            }
        }
    }
}
```

## 启发式搜索

就是将BFS中的队列改为优先级队列，最重要的是估价函数的定义。

```java
class Solution {
    private static class Node implements Comparable<Node> {
    private int x;
    private int y;
    
    // g(node) is the cost of the path from the start node to node
    private int g;
    // f(node) = g(node) + h(node)
    private int f;
    
    private Node(int x, int y, int g, int h) {
        this.x = x;
        this.y = y;
        this.g = g;
        this.f = g + h;
    }
    
    @Override
    public int compareTo(Node node) {
        return this.f - node.f;
    }
}

    // eight directions sorted in clockwise order
    private static int[][] DIRECTIONS = new int[][]{{0, -1}, {1, -1}, {1, 0}, {1, 1}, {0, 1}, {-1, 1}, {-1, 0}, {-1, -1}};

    public int shortestPathBinaryMatrix(int[][] grid) {
        int m = grid.length, n = grid[0].length;
        int endX = m - 1, endY = n - 1;
        
        if (grid[endX][endY] == 1) return -1; // early exit
        
        // Initialize the open list -> which node we expand next?
        PriorityQueue<Node> pq = new PriorityQueue<Node>();
        
        // Initialize the closed list -> which nodes we've already visited? What is the minimum g from start node to this?
        int[] closed = new int[m * n];
        Arrays.fill(closed, Integer.MAX_VALUE);
        
        // put the starting node on the open list
        pq.add(new Node(0, 0, 1, Math.max(m, n)));
        
        // while the open list is not empty
        while (!pq.isEmpty()) {
            
            // retrive the node with the least f on the open list, call it "node"
            Node node = pq.remove();
            
            int x = node.x;
            int y = node.y;
            
            // skip disallowed area
            if (x < 0 || x >= m || y < 0 || y >= n || grid[x][y] == 1) continue;
            
            // if node is the goal, stop search
            if (x == endX && y == endY) return node.g;
            
            // if a node with the same position is in the closed list
            // which has a lower or equals g than this, skip this expansion
            if (closed[x * m + y] <= node.g) continue;
            
            // push node on the closed list
            closed[x * m + y] = node.g;
            
            // generate 8 successors to node
            for (int[] dir : DIRECTIONS) {
                // for each successor
                // successor.g = node.g + distance between successor and node (equals to 1)
                // successor.h = estimate distance from successor to goal
                int g = node.g + 1;
                
                // h(node) is a heuristic function that 
                // estimates the cost of the cheapest path from node to the goal
                
                // Here we use **Diagonal Distance** as heuristic function, 
                // because we can and only can move in eight directions
                int h = Math.max(Math.abs(endX - x), Math.abs(endY - y));
                
                // push the successor on the open list
                pq.add(new Node(x + dir[0], y + dir[1], g, h));
            }
        }
        
        return -1;
    }
}
```

## AVL树和红黑树的实现和特性

1. 发明者 G. M. Adelson-Velsky 和 Evgenii Landis
2. Balance Factor(平衡因子): 是它的左子树的高度减去它的右子树的高度(有时相反)。 balance factor = {-1, 0, 1}
3. 通过旋转操作来进行平衡(四种)

4. https://en.wikipedia.org/wiki/Self- balancing_binary_search_tree

### AVL总结

1. 平衡二叉搜索树
2. 每个结点存 balance factor = {-1, 0, 1}
3. 四种旋转操作

**不足：结点需要存储额外信息、且调整次数频繁**。

## Red-black Tree

红黑树是一种近似平衡的二叉搜索树(Binary Search Tree)，它能够确保任何一 个结点的左右子树的高度差小于两倍。具体来说，红黑树是满足如下条件的二叉搜 索树:

- 每个结点要么是红色，要么是黑色

- 根节点是黑色

  每个叶节点(NIL节点，空节点)是黑色的。

- 不能有相邻接的两个红色节点

- 从任一节点到其每个叶子的所有路径都包含相同数目的黑色节点。

 

**关键性质**

**从根到叶子的最长的可能路径不多于最短的可能路径的两倍长。**



### 对比

• AVL trees provide **faster lookups** than Red Black Trees because they are **more strictly balanced.**

- Red Black Trees provide **faster insertion and removal** operations than AVL trees as fewer rotations are done due to relatively relaxed balancing.
- AVL trees store balance **factors or heights** with each node, thus requires storage for an integer per node whereas Red Black Tree requires only 1 bit of information per node.
- Red Black Trees are used in most of the **language libraries** **like map, multimap, multisetin C++** whereas AVL trees are used in **databases** where faster retrievals are required.

