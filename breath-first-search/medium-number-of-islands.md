# \[Medium\] Number of Islands / Closed Islands

## [Number of Islands](https://leetcode.com/problems/number-of-islands/)  \(5875/202\)

Given a 2d grid map of `'1'`s \(land\) and `'0'`s \(water\), count the number of islands. An island is surrounded by water and is formed by connecting adjacent lands horizontally or vertically. You may assume all four edges of the grid are all surrounded by water.

## Thought Process

step1.使用visited matrix來記錄是否看過。當然，由於題目的grid是由`'0'`and`'1'`組成，我們可以直接把看過的`'1'`變成`'0'`。這樣等全部看完的時候，整個matrix就都會變成全是'0'的matrix。

step2. 要注意此節點是否已走過or超過邊界。我們可以利用  
\(1\) `0<= x < len(grid) and 0 <= y < len(grid[0])`   
\(2\) `grid[x][y] == '1'`  
\(3\) `visited[x][y] == False`  
這幾個條件，來確保現在看的是valid的節點。

step3. 用DIRECTIONS來移動到相鄰的節點。  
`DIRECTIONS = [(1,0),(0,1),(-1,0),(0,-1)]` or  
`dfs(grid, x+1, y)   
dfs(grid, x, y+1)  
dfs(grid, x-1, y)  
dfs(grid, x, y-1)`

每一次我們call bfs/dfs這些search function時，它會一直search相鄰的所有neighbors，直到所有`neighbor == '1'`的節點都已經看完為止。

### 1. BFS:  O\(m\*n\)/O\(min\(m,n\)\)

推薦用BFS

Time Complexity: O\(m\*n\)  
Space Complexity: O\(min\(m,n\)\)  
space complexity是跟著queue size走的，所以如果grid本身全是'1'的情況下，queue的最大size就是 min\(m,n\)

### 2. DFS: O\(m\*n\)/O\(m\*n\)

用DFS Recursion 版本容易深度太深，造成Stack Overflow。

Time Complexity: O\(m\*n\) we are visiting each element in the 2D array once  
Space complexity: O \(m\*n\) in the case whole grid is filled with ‘1’.

### 3. Union Find: O\(m\*n\)/O\(m\*n\)

## Code

### 1. Iter BFS + boolean matrix: O\(m\*n\) / O\(min\(m,n\)\) \(Recommended\)

Time Complexity: O\(m\*n\) m is the rows, n is the cols  
Space Complexity: O\(min\(m,n\)\) worst-case when the grid is filled with lands, **size of queue will go up to min\(m,n\)**

```python
#     col0,1,2 
# row0 [[x,y,y]]
# row1 [[x    ]]
# row2 [[x    ]]
#
# grid[i][j] = grid[row][col]

def numIslands(self, grid: List[List[str]]) -> int:

    if len(grid) == 0 or len(grid[0]) == 0:
        return 0
    
    rows = len(grid)       # height
    cols = len(grid[0])    # width    
    visited = [ cols*[False] for _ in range(rows) ]
    islands = 0

    
    for i in range(rows):   # 易錯點：先過row0，再過col0
        for j in range(cols):
            # if the node has not been explored and it is an island, 
            # then bfs from there until cannot find anymore.
            if visited[i][j] == False and grid[i][j] == '1':
                self.bfs(grid, i, j, visited)
                islands += 1
    return islands    
                
    
def bfs(self, grid, x, y, visited):
    
    queue = collections.deque()   
    queue.append([(x,y)])    # (x,y)易錯點， 
    DIRECTIONS = [(1,0),(0,1),(-1,0),(0,-1)]
    
    while len(queue) != 0:
        x,y = queue.popleft()  # 易錯點
        for delta_x, delta_y in DIRECTIONS:
            # explore new node in every direction
            next_x = delta_x + x
            next_y = delta_y + y
            
            # within boundary
            if 0 <= next_x < len(grid) and 0 <= next_y < len(grid[0]):
                if visited[next_x][next_y] == False and grid[next_x][next_y] == '1':
                    queue.append((next_x, next_y))  # 易錯點
                    visited[next_x][next_y] = True  # mark as visited
```

### 2. Iter BFS + boolean set : O\(m\*n\)/O\(min\(m,n\)\)

基本還是用Iterative BFS，和上面不同的是，我們把判斷句額外寫成一個function，並且把visited = bolean array -&gt; set\(\)，把boolean array -&gt; set可以減少space complexity。

```python
def numIslands(self, grid: List[List[str]]) -> int:
    if len(grid) == 0 or len(grid[0]) == 0:
        return 0
    
    visited = set()
    islands = 0
    cols = len(grid[0])   # width
    rows = len(grid)      # height
    
    for i in range(rows):
        for j in range(cols):
            if grid[i][j] == '1' and (i,j) not in visited:
                self.bfs(self, grid, i, j, visited)
                islands += 1 
    return islands 

def bfs(self, grid, x, y, visited):
    queue = deque([(x,y)])
    DIRECTIONS = [(1,0),(0,1),(-1,0),(0,-1)]
    while len(queue) != 0:
        x,y = queue.popleft()
        for delta_x, delta_y in DIRECTIONS:
            next_x = x + delta_x
            next_y = y + delta_y
            if not self.valid_node(next_x,next_y):
                continue
            # if all conditions satisfy from above, 
            # then mark the node as true and append to queue.
            queue.append((next_x, next_y))
            visited.append((next_x, next_y))
            
def valid_node(self, grid, x, y, visited):
    if x < 0 or x >= len(grid) or y < 0 or y >= len(grid[0]):
    # or like this 
    # if not (0 <= x < len(grid) and 0 <= y < len(grid[0])):
        return False
    if (x, y) in visited:
        return False
    return grid[x][y] == '1'
```

### 3. Iterative DFS + boolean matrix: O\(m\*n\)/O\(m\*n\) \(Not Recommended\)

Time Complexity: O\(m\*n\) m is the rows, n is the cols  
Space Complexity: O\(m\*n\) worst-case when the grid is filled with lands, **DFS will go m\*n deep**

DFS雖然可以Accepted，但DFS容易深度比較深，會導致Stack Overflow。

```python
def numIslands(self, grid: List[List[str]]) -> int:
    
    if len(grid) == 0 or len(grid[0]) == 0:
        return 0
    
    islands = 0
    rows = len(grid)
    cols = len(grid[0])
    visited = [ cols*[False] for _ in range(rows)]
        
    for i in range(rows):
        for j in range(cols):
            if visited[i][j] == False and grid[i][j] == '1':
                self.dfs(grid, i, j, visited)
                islands += 1 
    return islands
    
def dfs(self, grid, x, y, visited):
    
    stack = []
    stack.append((x,y))
    DIRECTIONS = [(1,0), (0,1), (-1,0), (0,-1)]
    
    while stack:
        x,y = stack.pop()           
        # neighbor in neighbors
        for d_x, d_y in DIRECTIONS:
            next_x = d_x + x
            next_y = d_y + y
            if 0 <= next_x < len(grid) and 0 <= next_y < len(grid[0]):
                if visited[next_x][next_y] == False and grid[next_x][next_y] == '1':
                    stack.append((next_x, next_y))
                    visited[next_x][next_y] = True
```

### 4. Recursive DFS: O\(m\*n\)/O\(m\*n\)

把Recusion + DIRECTIONS 結合，讓code本身自己走。   
跟Iterative DFS比，少了visited matrix來記走過路徑。

```python
# 此解法省略了 visited matrix，直接mark as '0'
def numIslands(self, grid: List[List[str]]) -> int:
    if len(grid) == 0 or len(grid[0]) == 0:
        return 0
    
    rows = len(grid) 
    cols = len(grid[0])
    islands = 0
    
    for i in range(rows):
        for j in range(cols):
            if grid[i][j] == '1':  #這裡已經不需要visited，直接修改grid
                self.dfs(grid,i,j)
                islands += 1
    return islands

def dfs(self, grid, x, y):
    # check boundary, should not go beyond
    if self.is_valid(grid,x,y):
        return 
# method 2
#    # alternative way of checking boundary
#    if not (0 <= x < len(grid) and 0 <= y < len(grid[0]) and grid[x][y] == '1'):
#       return    
        
    # mark (x,y) as visited
    grid[x][y] = '0'    
    self.dfs(grid, x+1, y)
    self.dfs(grid, x, y+1)
    self.dfs(grid, x-1, y)
    self.dfs(grid, x, y-1)    

# methd 1, separate method to check   
def is_valid(self, grid, x, y):
    # -X--|[0---len(grid)]--X-|--X--
    # -X--|[0-----len(grid[0])]--X--
    if not (0 <= x < len(grid) and 0 <= y < len(grid[0])): #易錯點：用 and
        return False
    if grid[x][y] == '0':
        return False
    return True
    
```

#### 類似Matrix題型

1. Word Search \(Use DFS\)

### 待整理

#### 1. BFS

#### 2. DFS 

```python
def numIslands(self, grid: List[List[str]]) -> int:
    if len(grid) == 0 or len(grid[0]) == 0:
        return 0
    
    cols = len(grid[0])
    rows = len(grid)
    visited = [cols*[False] for _ in range(rows)]
    islands = 0
    
    for i in range(rows):
        for j in range(cols):
            if visited[i][j] == False and grid[i][j] == '1':
                self.dfs(grid, x, y, visited)
                islands += 1
    return islands
    
def dfs(self, grid, x, y, visited):
    
    visited[x][y] = True    # 易錯點：要先mark原點visited，否則會無限recursion下去
    DIRECTIONS = [(1,0),(0,1),(-1,0),(0,-1)]
    for delta_x, delta_y in DIRECTIONS:
        next_x = x + delta_x
        next_y = y + delta_y
        if 0 <= next_x < len(grid) and 0 <= next_y < len(grid[0]):
            if visited[next_x][next_y] == False and grid[next_x][next_y] == '1':
                self.dfs(grid, next_x, next_y, visited)
    
```

## [\(follow up\) 1254 Number of Closed Islands   \(1128/29\)](https://leetcode.com/problems/number-of-closed-islands/)

Given a 2D `grid` consists of `0s` \(land\) and `1s` \(water\).  An _island_ is a maximal 4-directionally connected group of `0s` and a _closed island_ is an island **totally** \(all left, top, right, bottom\) surrounded by `1s.`

Return the number of _closed islands_.



![](../.gitbook/assets/image%20%28111%29.png)

```text
Input: grid = [[1,1,1,1,1,1,1,0],[1,0,0,0,0,1,1,0],[1,0,1,0,1,1,1,0],[1,0,0,0,0,1,0,1],[1,1,1,1,1,1,1,0]]
Output: 2
Explanation: 
Islands in gray are closed because they are completely surrounded by 
water (group of 1s).
```

![](../.gitbook/assets/image%20%28110%29.png)

```text
Input: grid = [[0,0,1,0,0],[0,1,0,1,0],[0,1,1,1,0]]
Output: 1
```

### 1. Recursive DFS + visited boolean array: O\(m\*n\) / O\(m\*n\) 

Time Complexity: O\(m\*n\) for dfs search entire matrix  
Space Complexity: O\(m\*n\) for visited boolean array

> 思路：找到一個島`grid[i][j] == 0`時，看  
> \(1\)是否visited過: visiteed\[i\]\[j\] == False --&gt; GOOD  
> \(2\)是否為closed island: DFS closed == True --&gt; GOOD

{% tabs %}
{% tab title="Python" %}
```python
def closedIsland(self, grid: List[List[int]]) -> int:
    width = len(grid[0])
    height = len(grid)
    visited = [[False] * width for _ in range(height)]
    ans = 0
    
    for i in range(height):
        for j in range(width):
            # 0 -> island found, 
            # then we need to check surrounding
            if grid[i][j] == 0 and visited[i][j] == False and self.is_closed(grid, i, j, visited) == True:
                ans += 1
    return ans
    
    
def is_closed(self, grid, x, y, visited):

    # boundary & recursion exit
    if not (0 <= x < len(grid) and 0 <= y < len(grid[0])):
        return False
    # water found, move on to the next direction 
    if grid[x][y] == 1 or visited[x][y]:
        return True
    
    # check up, down, left, right  
    visited[x][y] = True
    up    = self.is_closed(grid, x+1, y, visited) #up
    right = self.is_closed(grid, x, y+1, visited) #right
    down  = self.is_closed(grid, x-1, y, visited) #down
    left  = self.is_closed(grid, x, y-1, visited) #left
    
    # all dirctions must be satisfied then we can proceed as closed island.
    return up and right and down and left 
```
{% endtab %}

{% tab title="Java" %}
```java
public int closedIsland(int[][] grid) {
    if (grid == null || grid.length == 0) return 0;
    boolean[][] seen = new boolean[grid.length][grid[0].length];
    int result = 0;
    for (int i = 0; i < grid.length; ++i) {
        for (int j = 0; j < grid[0].length; ++j) {
            if (grid[i][j] == 0 && !seen[i][j] && isClosed(grid, seen, i, j)) {
                result++;
            }
        }
    }
    return result;
}

private boolean isClosed(int[][] grid, boolean[][] seen, int row, int col) {
    if (row < 0 || grid.length <= row || col < 0 || grid[0].length <= col) return false;
    if (grid[row][col] == 1 || seen[row][col]) return true;
    seen[row][col] = true;
    
    boolean up = isClosed(grid, seen, row - 1, col); 
    boolean right = isClosed(grid, seen, row, col + 1);
    boolean down = isClosed(grid, seen, row + 1, col); 
    boolean left = isClosed(grid, seen, row, col - 1);
    return up && right && down && left;
}
```
{% endtab %}
{% endtabs %}



