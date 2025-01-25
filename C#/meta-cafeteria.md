### [1.1 Cafeteria](https://www.metacareers.com/profile/coding_puzzles?puzzle=203188678289677)

## Approach: Sorting + Greedy

### Problem Understanding
The task is to find the **maximum** number of additional diners that can be seated while maintaining a social distancing rule of **K** empty seats between any two diners, both on the left and right of each diner.

### Intuition
Given:
- **M** diners are already seated.
- **N** seats in total.
- An array **S** representing the positions of the **M** diners.

To maximize the number of additional diners, we need to focus on the **gaps**:
1. **First gap**: The space from the first seat to the first diner.
2. **Intermediate gaps**: The spaces between two consecutive diners.
3. **Last gap**: The space from the last diner to the last seat.

We will use a **greedy approach** by sorting the list of occupied seats and then calculating how many additional diners can fit into each of these gaps, one at a time.

### Sample Visualization:
Consider the following example:
- **K = 2**
- **M = 2**
- **S = [4, 11]**
- **N = 13**

```
        1  2  3  4  5  6  7  8  9  10  11  12  13
                 |                      |
        --------- ---------------------- ----------------
       [First gap]  [Intermediate gaps]    [Last gap]
```

### Formula for Filling Gaps:
1. **First gap** (from seat 1 to the first diner at seat 4):
   - **First gap size** = `4 - 1 = 3` empty seats.
   - To seat a diner, we need `K + 1` seats (K seats for distancing and 1 seat for the diner).
   - We can fit one diner in the first gap:
     - **New diners** = `3 // (2 + 1) = 1`

2. **Intermediate gaps** (between consecutive diners):
   - Calculate the number of empty seats between two diners and adjust by subtracting `K` for the required distancing.
   - For example, if the gap between diner 1 at seat 4 and diner 2 at seat 11 is `7` (i.e., `11 - 4 - 1`), we subtract `K` and use the remaining space:
     - **New diners** = `(7 - 2) // (2 + 1) = 5 // 3 = 1` diner can fit.

3. **Last gap** (from the last diner to the last seat):
   - Similar to the first gap, check how many diners can fit in the remaining space after the last diner.

### Approach:
- **Step 1:** Sort the list of occupied seat positions `S`.
- **Step 2:** Calculate how many new diners can be seated in the **first gap**, **intermediate gaps**, and **last gap** using the formula:
  - For the first and last gaps, use `gap // (K + 1)`.
  - For intermediate gaps, use `(gap - K) // (K + 1)` because `K` seats must remain empty on both sides of a new diner.

```csharp
public int getMaxAdditionalDinersCount(N, K, M, S) {
    // Sort the array S
    Array.Sort(S);
    long new_diners = 0;

    // Find the gap before the first occupied seat
    long first_gap = S[0] - 1;
    // Check if the gap is sufficient to place additional diners
    if (first_gap >= K) {
      new_diners += (long)Math.Floor((double)first_gap / (K + 1));
    }

    // Find the intermediate gaps between the occupied diners
    for(int i = 1; i < M; i++) {
      long gap = S[i] - S[i - 1] - 1;    // Number of empty seats between 2 diners
      if (gap >= K) {
        new_diners += (long)Math.Floor((double)(gap - K) / (K + 1));   // (gap - K) to keep K seats empty on both sides
      }
    }

    // Determine the gap after the last occupied diner
    long last_gap = N - S[M - 1];
    if (last_gap >= K) {
      new_diners += (long)Math.Floor((double)last_gap / (K + 1));
    }
    return new_diners;
  }
}   
```

### Time and Space Complexity:
- **Time Complexity:** O(M log M) – The primary time complexity arises from sorting the list of occupied seats, which takes `O(M log M)`, where `M` is the number of diners.
- **Space Complexity:** O(1) – We only use a fixed amount of extra space for the calculations, so the space complexity is constant.
