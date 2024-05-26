# LEETCODE-DP-552
To understand how the `checkRecord` method works, let's break down the solution step by step. This method aims to count the number of valid attendance records of length `n` that do not contain more than one 'A' (absent) and do not have more than two consecutive 'L's (late).

Here's the breakdown:

1. **Initial Setup**:
    - `kMod` is the modulus value used to prevent overflow and keep the results within manageable limits.
    - `dp` is a 2D array where `dp[i][j]` represents the number of valid sequences of a certain length with `i` 'A's and ending with `j` consecutive 'L's.
    - `dp[0][0] = 1` initializes the base case: a sequence of length 0 with no 'A' and no 'L'.

2. **Main Loop**:
    - The loop runs `n` times, each iteration corresponding to extending the sequences by one more character.
    - `prev` is a deep copy of `dp` before it is updated, preserving the state from the previous iteration.

3. **Updates**:
    - For each possible sequence state, we consider adding 'P' (present), 'L' (late), or 'A' (absent) to the sequences and update `dp` accordingly.
    - We update `dp` for sequences with 0 'A's and with 1 'A' by considering:
      - Adding 'P' to sequences ending in 0, 1, or 2 'L's.
      - Adding 'L' to sequences ending in 0 or 1 'L'.
      - Adding 'A' or 'P' to sequences with exactly 1 'A'.

4. **Result Calculation**:
    - After the loop, the result is the sum of all valid sequences of length `n` in `dp`.

Here is a clearer look at how the transitions are calculated:

- **Append 'P'**:
  - To `dp[0][0]`: Add all sequences ending with 0, 1, or 2 'L's (prev[0][0] + prev[0][1] + prev[0][2]).
  - To `dp[1][0]`: Add all sequences with 0 or 1 'A' ending with 0, 1, or 2 'L's.

- **Append 'L'**:
  - To `dp[0][1]`: Add sequences ending in exactly 0 'L' (prev[0][0]).
  - To `dp[0][2]`: Add sequences ending in exactly 1 'L' (prev[0][1]).
  - To `dp[1][1]`: Add sequences with exactly 1 'A' ending in 0 'L' (prev[1][0]).
  - To `dp[1][2]`: Add sequences with exactly 1 'A' ending in 1 'L' (prev[1][1]).

- **Append 'A'**:
  - To `dp[1][0]`: Add all sequences with 0 'A' ending in 0, 1, or 2 'L's (prev[0][0] + prev[0][1] + prev[0][2]).

Here's the revised code with comments:

```java
class Solution {
  public int checkRecord(int n) {
    final int kMod = 1_000_000_007;
    // dp[i][j] := the length so far with i A's and the last letters are j L's
    long[][] dp = new long[2][3];
    dp[0][0] = 1; // Initialize base case

    while (n-- > 0) {
      long[][] prev = Arrays.stream(dp)
                          .map((long[] A) -> A.clone())
                          .toArray((int length) -> new long[length][]);

      // Append a P
      dp[0][0] = (prev[0][0] + prev[0][1] + prev[0][2]) % kMod;

      // Append an L
      dp[0][1] = prev[0][0];
      dp[0][2] = prev[0][1];

      // Append an A or append a P
      dp[1][0] = (prev[0][0] + prev[0][1] + prev[0][2] + prev[1][0] + prev[1][1] + prev[1][2]) % kMod;

      // Append an L
      dp[1][1] = prev[1][0];
      dp[1][2] = prev[1][1];
    }

    // Sum all valid sequences of length n
    return (int) ((dp[0][0] + dp[0][1] + dp[0][2] + dp[1][0] + dp[1][1] + dp[1][2]) % kMod);
  }
}
```

This code efficiently computes the number of valid attendance records of length `n` by dynamically updating the count of valid sequences as described above.
