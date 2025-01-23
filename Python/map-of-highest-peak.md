[1765. Map of Highest Peak](https://leetcode.com/problems/map-of-highest-peak/description/)

## Approaches
- Approach 1: BFS
- Approach 2: Dynamic Programming

### Approach 1: BFS

**Intution:**
- The problem wants to find the cells represeting the peaks(i.e higher than all their adjacent cells). Our task is to traverse the grid to compute the peak heights and determine the highest peak value.
- A peak is a cell that is strictly higher than all its adjacent cells(up, right, down, left).

**Why BFS?**
- The problem requires us to find the shortest distance from every land cell to the nearest water cell. BFS is ideal for this because it explores the grid level by level, ensuring that the first time a land cell is reached, it is at the shortest distance from a water cell.

**Approach:**
- The idea is that for each land cell, store the shortest distance to the nearest water cell. 
- Initialize a queue, to keep track of adjacent cells, and a visited set, to avoid revisiting of cells.
- Start by adding all water cells(represented as 1) to the queue with a distance of 0. These water cells acts as the BFS sources.
- Mark water cells as visited and set their value in `isWater` to 0(to indicate water).
- BFS traversal:
    - Process each cell in the queue, for each neighboring land cell, if it hasn't been visited, update its value with the current distance(level + 1).
    - Add the land cell to the queue for continuing BFS

```python
import collections
def highestPeak(grid):
    # Initialize queue and visited, add water cells to the queue
    queue = collections.deque()
    visited = set()
    m, n = len(grid), len(grid[0])
    for r in range(m):
        for c in range(n):
            if grid[r][c] == 1:
                queue.append((r,c,0))    # (row, col, distance or level)
                grid[r][c] = 0           # Mark the water cell's height to 0
                visited.add((r, c))      # mark each water cell as visited

    # BFS traversal
    dx = [1,-1,0,0]
    dy = [0,0,-1,1]
    while queue:
        row, col, dist = queue.popleft()
        for i in range(4):
            x = row + dx[i]
            y = col + dy[i]
            # check if (x, y) is valid, not visited, and is a land cell 
            if (0 <= x < m and 0 <= y < n) and (x, y) not in visited and grid[x][y] == 0:
                grid[x][y] = dist + 1
                queue.append((x, y, dist + 1))
                visited.add((x, y))
    
    return grid
```

**Time and Space complexity:**
- Time Complexity: O(m x n), BFS will visit each cell exactly once, for each cell, we processes it and its neighbors
- Space Complexity: 2 * O(m x n), for storing cells to queue and also to visited cells

### Approach 2: Dynamic Programming

**Intution**
- The idea is to that the height of each cell is the smallest distance from any water cell. Once we know the smallest distances of the cell's neighboring cells, then the computing the current cell's height becomes straightforward, it is just the smallest of neighbor's cell plus one. The idea is to use DP to compute this distance efficiently.

**Why DP?**
- Calculating each cell's height depends on the heights of its neighboring cells
- By iterating over the grid in a specific order, we can ensure that all the necessary states are computed before being used.

**Approach:**
- Create a 2D array, `cellHeights`, with the same dimensions as the given grid and initialize each cell with the largest value (i.e., m * n).
- Iterate over `cellHeights` and set the positions of water cells to 0.
- Perform two passes: 
  1. The first pass is from left to right (i.e., from (0, 0) to (m - 1, n - 1)).
  2. The second pass is from right to left and bottom to top (i.e., from (m - 1, n - 1) to (0, 0)).
- In the first pass, for each valid top (i.e., (i - 1, j)) and left (i.e., (i, j - 1)), find the smallest neighbor and update `cellHeights[i][j]` with `smallestNeighbor + 1`.
- In the second pass, move upward or left, to correct any overestimated values from the first pass.
- Finally, return the `cellHeights` array.

```Python
def highestPeak(self, grid):
    ''' DP '''
    # Initialize the cellHeights as the same dimension of the grid
    m, n = len(grid), len(grid[0])
    INF = m * n
    cellHeights = [[INF] * n for _ in range(m)]
    
    # Update cell heights of water cells
    for i in range(m):
        for j in range(n):
            if grid[i][j] == 1:
                cellHeights[i][j] = 0
    
    # First pass: top - left
    for i in range(m):
        for j in range(n):
            smallestHeight = INF
            # Check if the top cell is valid
            if 0 <= i - 1 < m:
                topCellValue = cellHeights[i - 1][j]
                smallestHeight = min(smallestHeight, topCellValue)
            # Check if the left cell is valid
            if 0 <= j - 1 < n:
                leftCellValue = cellHeights[i][j - 1]
                smallestHeight = min(smallestHeight, leftCellValue)
            # Update current cell value
            cellHeights[i][j] = min(cellHeights[i][j], smallestHeight + 1)

    # Second pass: bottom - right
    for i in range(m - 1, -1, -1):
        for j in range(n - 1, -1, -1):
            smallestHeight = INF
            # Check if the bottom cell is valid
            if 0 <= i + 1 < m:
                bottomCellValue = cellHeights[i + 1][j]
                smallestHeight = min(smallestHeight, bottomCellValue)
            # Check if the right cell is valid
            if 0 <= j + 1 < n:
                rightCellValue = cellHeights[i][j + 1]
                smallestHeight = min(smallestHeight, rightCellValue)
            # Update current cell value
            cellHeights[i][j] = min(cellHeights[i][j], smallestHeight + 1)
    
    return cellHeights
```

**Complexities:**
- Time: 2 * O(m x n) ~ O(m x n), for both the passes
- Space: O(m x n), for storing heights of each cell