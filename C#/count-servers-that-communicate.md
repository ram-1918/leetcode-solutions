[1267.Count Servers That Communicate](https://leetcode.com/problems/count-servers-that-communicate/description/)

## Approaches
- Counting
- Server Grouping

### Approach 1: Counting

**Intution**
- The problem wants us to find the number of servers that can communicate with any other server in its row or a column. The idea is pretty straightforward to maintain 2 arrays to keep track of number of servers in each row and column. Later, check if the row and column has more than one servers, then consider this as a communicable server.

**Approach**
- Initialize two arrays, say `row_counts` of size len(grid), `col_counts` of size len(grid[0]), and `communicable_servers`(total servers communicable).
- First pass: Iterate over the grid, when a server is encountered, `grid[row][col]` is 1, increment count for the `row` on `col_counts` and increment count for the `col` on `row_counts`.
- Second pass: Again, iterate over the grid, when a server is encountered, check if the total servers in the current row or column are greater than 1, if yes, then increment the `communicable_servers` count.

**Complexities**
- Time Complexity: 2 * O(m * n) ~ O(m * n), for traversing over the grid twice
- Space Complexity: O(m + n), for storing row and col counts.

```csharp
public int CountServers(int[][] grid) {
    int m = grid.Length, n = grid[0].Length;
    int[] serversPerRow = new int[m];
    int[] serversPerCol = new int[n];
    int totalServers = 0;

    // Iterate over the grid to calculate servers per row and col
    for (int r = 0; r < m; r++) {
        for (int c = 0; c < n; c++) {
            if(grid[r][c] == 1) {
                serversPerRow[r]++;
                serversPerCol[c]++;
            }
        }
    }

    // Check if any servers communicate
    for (int r = 0; r < m; r++) {
        for (int c = 0; c < n; c++) {
            if(grid[r][c] == 1 && serversPerRow[r] > 1 && serverPerCol[c] > 1) {
                totalServers++;
            }
        }
    }

    return totalServers;
}
```

---

### Approach 2: Server Grouping

**Intuition**
- This approach efficiently tracks servers in rows and columns by remembering the last seen server in each column for each row. This avoids revisiting servers and reduces redundant checks.

**Approach**
- We maintain three variables:
  - `row_counts`: Tracks the number of servers in each column.
  - `total_communicable_servers`: Counts the total number of servers that can communicate with at least one other server.
  - `last_seen_server_in_col`: Stores the column index of the last server in the current row for each column.

**Steps**

1. **First Pass:**
   - Iterate through the grid row by row and column by column:
     - For each row, initialize `total_servers` to 0 (this counts the servers in the current row).
     - When a server is encountered:
       - Increment the `row_counts` for that column (indicating a server exists in this column).
       - Increment `total_servers` (count the servers in the row).
       - Update `last_seen_server_in_col` for the current row with the column index where the server was found.
     - After finishing the row, check if it has more than one server:
       - If yes, increment `total_communicable_servers` by `total_servers` (since all servers in the row communicate with each other).
       - Reset `last_seen_server_in_col` for this row to `-1` (indicating no need for further checks for this row).

2. **Second Pass:**
   - Iterate through the grid again:
     - For each row, check if `last_seen_server_in_col[row]` is not `-1` (indicating that there’s a server in the current row that might communicate with others).
     - If a server exists in that row and column, check the count of servers in the column using `row_counts[last_seen_server_in_col[row]]`.
     - If there are more than one server in that column, increment `total_communicable_servers` by 1 (because the server in the current row can communicate with others in the column).

**Final Result:**
- Return `total_communicable_servers`, which now contains the count of all servers that can communicate with at least one other server.

**Benefits of This Approach:**
- By keeping track of `last_seen_server_in_col`, we ensure we don’t recheck servers and reduce unnecessary operations.
- The two-pass approach efficiently identifies and counts communicable servers.

**Complexities**
- Time Complexity:
- Space Complexity:

```csharp
public int CountServers(int[][] grid) {
    int m = grid.Length, n = grid[0].Length;
    int[] serverPerColumn = new int[n];   // total servers in each column
    int[] lastSeenServerInCol =  new int[m];   // last seen server in each row, handles one server per row case
    int totalServers = 0;

    // Interate over the grid
    for(int r = 0; r < m; r++) {
        int totalServersInRow = 0;
        for(int c = 0; c < n; c++) {
            if (grid[r][c] == 1) {
                serverPerColumn[c]++;
                totalServersInRow++;
                lastSeenServerInCol[r] = c;
            }
        }
        if (totalServersInRow > 1) {
            totalServers += totalServersInRow;
            lastSeenServerInCol[r] = -1;    // reset, because there are servers more than one which can communicate with each other
        }
    }

    // Iterate through the length of the grid
    for (int r = 0; r < m; r++) {
        int col = lastSeenServerInCol[r];    // Last seen col in the row 'r'
        // if col != -1, then it means there is one server in the row
        // The below condition checks, if that column has more than one server to communicate
        if (col != -1 && serverPerColumn[col] > 1) {
            totalServers++;
        }
    }

    return totalServers;

}
```