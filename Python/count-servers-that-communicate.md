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

```python
def countServers(grid):
    m, n = len(grid), len(grid[0])
    row_counts = [0] * m
    col_counts = [0] * n
    for i in range(m):
        for j in range(n):
            if grid[i][j]:
                row_counts[j] += 1
                col_counts[i] += 1
    communicable_servers = 0
    for i in range(m):
        for j in range(n):
            if grid[i][j]:
                if row_counts[j] > 1 or col_counts[i] > 1:
                    communicable_servers += 1
    return communicable_servers
```

**Complexities**
- Time Complexity: 2 * O(m * n) ~ O(m * n), for traversing over the grid twice
- Space Complexity: O(m + n), for storing row and col counts.


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

---

**Benefits of This Approach:**
- By keeping track of `last_seen_server_in_col`, we ensure we don’t recheck servers and reduce unnecessary operations.
- The two-pass approach efficiently identifies and counts communicable servers.

**Complexities**
- Time Complexity:
- Space Complexity:

```python
def countServers(grid):
    '''Server Grouping'''
    m, n = len(grid), len(grid[0])
    row_counts = [0] * n    # contains total servers in each column
    communicable_servers = 0
    # to keep track of a server in a row can communicate,
    # with a server in another row in the SAME COLUMN
    last_seen_server_in_col = [-1] * m

    # Iterate and calculate row_counts
    for r in range(m):
        total_servers_in_row = 0
        for c in range(n):
            if grid[r][c]:
                row_counts[c] += 1
                total_servers_in_row += 1
                last_seen_server_in_col[r] = c
        
        # if the total servers in the current row is > 1, then
        # last_seen_server_in_col[r] can be reset to -1
        # this is because, all servers in the current row are already communicable
        # This helps avoid further processing(reconsidering the same server)
        if total_servers_in_row > 1:
            communicable_servers += total_servers_in_row
            last_seen_server_in_col[r] = -1

    # Iterate over the counts and check if servers can communicate
    for r in range(m):
        col = last_seen_server_in_col[r]
        # if col == -1, then that means the servers in the row are already counted
        if col != -1 and row_counts[col] > 1:
            communicable_servers += 1

    return communicable_servers
```