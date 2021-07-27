# \[Medium\] Number of Islands

[Number of Islands](https://leetcode.com/problems/number-of-islands/)  
5875/202  
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

### 3. Union Find

## Code

### 1. BFS + boolean matrix: O\(m\*n\) / O\(min\(m,n\)\) \(Recommended\)

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
    
    rows = len(grid)
    cols = len(grid[0])     
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
    queue.append((x,y))    # (x,y)易錯點
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

#### 2. BFS + boolean set : O\(m\*n\)/O\(min\(m,n\)\)

needs to revisit

```python
def numIslands(self, grid: List[List[str]]) -> int:
    if len(grid) == 0 or len(grid[0]) == 0:
        return 0
    
    visited = set()
    islands = 0
    cols = len(grid[0])
    rows = len(grid)
    
    for i in range(cols):
        for j in range(rows):
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
            if not self.valid_node(next_x,next_y) and grid[next_x][next_y] == '0':
                continue
            # if all conditions satisfy from above, 
            # then mark the node as true and append to queue.
            queue.append((next_x, next_y))
            visited.append((next_x, next_y))
            
def valid_node(self,x,y):
    if x > len(grid[0]) or y > len(grid):
        return False
    if visited[x][y] == True:
        return False
    return grid[x][y]
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

### 4. Recursive DFS: 

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
            if grid[i][j] == '1':
                self.dfs(grid,i,j)
                islands += 1
    return islands

def dfs(self, grid, x, y):
    if self.is_valid(grid,x,y):
        return 
    # mark as visited
    grid[x][y] = '0'
    
    self.dfs(grid, x+1, y)
    self.dfs(grid, x, y+1)
    self.dfs(grid, x-1, y)
    self.dfs(grid, x, y-1)    
    
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

