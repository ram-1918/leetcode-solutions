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

```csharp
// BFS with Built-in methods
public class Solution {
    public int[][] highestPeak(int[][] grid) {
        // Initialze a queue and a visited arrays
        int m = grid.Length, n = grid[0].Length;
        Queue<Tuple<int, int, int>> queue = new Queue<Tuple<int, int, int>>();
        bool[,] visited = new bool[m, n];

        // Iterate and add water cells into the queue and visited array, update its height to 0
        for(int i = 0; i < m; i++) {
            for(int j = 0; j < n; j++) {
                if(grid[i][j] == 1) {
                    grid[i][j] = 0;
                    queue.Enqueue(new Tuple<int,int,int>(i, j, 0));
                    visited[i, j] = true;
                }
            }
        }

        // BFS traversal
        int[] dx = {-1, 1, 0, 0};
        int[] dy = {0, 0, -1, 1};

        while(queue.Count > 0) {
            var item = queue.Dequeu();
            int row = item.Item1, col = item.Item2, dist = item.Item3;

            for(int i = 0; i < 4; i++) {
                int x = row + dx[i];
                int y = col + dy[i];
                
                if(0 <= x < m && 0 <= y < n && grid[x][y] == 0 && !visited[x, y]) {
                    grid[x][y] = dist + 1;
                    queue.Enqueue(new Tuple<int,int,int>(x, y, dist + 1))
                    visited[x, y] = true;
                }
            }
        }

        return grid;
    }
}
```
```csharp
// BFS without Queue and Tuple classes
public class Solution {
    public int[][] HighestPeak(int[][] grid) {
        int m = grid.Length, n = grid[0].Length;
        
        // Directions for BFS traversal
        int[] dx = {1, -1, 0, 0};
        int[] dy = {0, 0, -1, 1};
        
        // 1st Pass: Set all water cells to height 0 and prepare a queue with them
        // Mark all land cells as 'unreachable' (e.g., -1)
        for (int r = 0; r < m; r++) {
            for (int c = 0; c < n; c++) {
                if (grid[r][c] == 1) {
                    grid[r][c] = 0; // Water cells' height is 0
                } else {
                    grid[r][c] = -1; // Land cells are initially unreachable
                }
            }
        }

        // Perform BFS using the grid directly to update land cell heights
        // We can treat all water cells as sources and start BFS from them
        for (int r = 0; r < m; r++) {
            for (int c = 0; c < n; c++) {
                if (grid[r][c] == 0) {
                    // For each water cell, use a simple DFS/BFS to fill in the distances
                    // We use an auxiliary array to track cells to be visited next
                    for (int i = 0; i < 4; i++) {
                        int x = r + dx[i];
                        int y = c + dy[i];
                        if (x >= 0 && x < m && y >= 0 && y < n && grid[x][y] == -1) {
                            grid[x][y] = grid[r][c] + 1;  // Update height
                        }
                    }
                }
            }
        }

        return grid;
    }
}
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

```csharp
public class Solution {
    public int[][] HighestPeak(int[][] grid) {
        int m = grid.Length, n = grid[0].Length;

        // Create a DP array to store the height values
        int[][] dp = new int[m][];
        for (int i = 0; i < m; i++) {
            dp[i] = new int[n];
            for (int j = 0; j < n; j++) {
                dp[i][j] = int.MaxValue; // Initialize all land cells with a large number (unreachable state)
            }
        }

        // 1st pass: Update heights from top-left to bottom-right
        for (int r = 0; r < m; r++) {
            for (int c = 0; c < n; c++) {
                if (grid[r][c] == 1) {
                    dp[r][c] = 0; // Water cells are set to height 0
                } else {
                    if (r > 0) {
                        dp[r][c] = Math.Min(dp[r][c], dp[r - 1][c] + 1); // From top
                    }
                    if (c > 0) {
                        dp[r][c] = Math.Min(dp[r][c], dp[r][c - 1] + 1); // From left
                    }
                }
            }
        }

        // 2nd pass: Update heights from bottom-right to top-left
        for (int r = m - 1; r >= 0; r--) {
            for (int c = n - 1; c >= 0; c--) {
                if (r < m - 1) {
                    dp[r][c] = Math.Min(dp[r][c], dp[r + 1][c] + 1); // From bottom
                }
                if (c < n - 1) {
                    dp[r][c] = Math.Min(dp[r][c], dp[r][c + 1] + 1); // From right
                }
            }
        }

        return dp;
    }
}
```

**Complexities:**
- Time: 2 * O(m x n) ~ O(m x n), for both the passes
- Space: O(m x n), for storing heights of each cell