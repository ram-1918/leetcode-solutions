### [1.2 Director of Photography](https://www.metacareers.com/profile/coding_puzzles?puzzle=870874083549040)

## Approach: Precomputation

### Problem Understanding
We are given a string `C` of length `N`, where each character represents one of the following types:
- `P`: Photographer
- `A`: Actor
- `B`: Backdrop
- `.`: Empty space

We need to count the number of valid "artistic photographs" that can be taken according to the following rules:
1. The **actor** (`A`) must always be between the **photographer** (`P`) and the **backdrop** (`B`).
2. The distance between the **photographer** and the **actor** must be between **X** and **Y** (inclusive).
3. The distance between the **actor** and the **backdrop** must also be between **X** and **Y** (inclusive).

### Intuition
To solve this, we can **precompute the positions** of the photographer (`P`), actor (`A`), and backdrop (`B`). Then, for each valid combination of `P`, `A`, and `B`, we can check if the two conditions hold:
1. The actor is always between the photographer and the backdrop. This can be satisfied by ensuring the following two patterns:
   - **P A B**: Photographer, Actor, and Backdrop in that order.
   - **B A P**: Backdrop, Actor, and Photographer in that order.
2. The distances between the characters (photographer-actor and actor-backdrop) should lie within the given limits `[X, Y]`.

### Approach:
1. **Precompute Positions**: 
   - Find the indices where each type (`P`, `A`, `B`) appears and store them in separate lists (`P`, `A`, `B`).
2. **Iterate Over All Possible Combinations**: 
   - For each `P`, iterate over all `A`s and for each `A`, iterate over all `B`s to form valid combinations (`P A B` or `B A P`).
3. **Check Validity**:
   - Ensure that the actor is positioned between the photographer and the backdrop (i.e., `i < j < k` or `k < j < i`).
   - Check that the distances between the photographer and the actor, and the actor and the backdrop, lie within the range `[X, Y]`.

4. **Count Valid Combinations**:
   - For each valid combination, increment the count.

### Python Code:

```python
def getArtisticPhotographCount(N, C, X, Y):
    # Precompute positions of P, A, and B
    p = [i for i in range(N) if C[i] == 'P']
    a = [i for i in range(N) if C[i] == 'A']
    b = [i for i in range(N) if C[i] == 'B']
    
    total = 0
    
    # Iterate over each P, A, and B to form valid combinations
    for i in p:
        for j in a:
            for k in b:
                # Check if the actor is between the photographer and backdrop
                if i < j < k or k < j < i:
                    # Check if the distances are within the limits [X, Y]
                    if X <= abs(i - j) <= Y and X <= abs(j - k) <= Y:
                        total += 1
    
    return total
```

### Explanation of Code:
1. **Precompute Positions**:
   - We create lists `p`, `a`, and `b` that store the indices of `P`, `A`, and `B` in the string `C` respectively.
   
2. **Nested Loops**:
   - We iterate over each photographer (`i` in `p`), actor (`j` in `a`), and backdrop (`k` in `b`) to check all possible combinations.

3. **Conditions**:
   - **Actor between Photographer and Backdrop**: We ensure that the actor (`A`) is between the photographer (`P`) and the backdrop (`B`), which can happen in two ways: `P A B` or `B A P`.
   - **Distance Constraints**: We check that the distance between the photographer and the actor, and the distance between the actor and the backdrop, are both within the range `[X, Y]`.

4. **Counting Valid Photographs**:
   - If both conditions are satisfied, we increment the `total` count.

### Time and Space Complexity:
- **Time Complexity: O(N + (N * 3))**:
   - **O(N)**: We first iterate over the string once to find positions of `P`, `A`, and `B`.
   - **O(N³)**: We then iterate over each combination of `P`, `A`, and `B` (i.e., three nested loops), which gives a time complexity of `O(N³)` in the worst case.
   
- **Space Complexity: O(3 * N)**:
   - We store the positions of `P`, `A`, and `B` in three separate lists, each of size `N` in the worst case.