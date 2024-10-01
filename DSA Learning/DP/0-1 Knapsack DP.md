

### Brute Force (Top-down approach):
- **Explanation**: 
  - In the brute force method, we look at each item and decide whether to include it in the knapsack or not. 
  - We subtract the current item's weight from the remaining capacity and explore both choices: adding the item or skipping it.
  - If adding the item leads to the same remaining capacity for a later item, we end up recalculating the same state. So, this approach becomes inefficient due to the repetitive nature of recalculating already-explored paths.
  - This approach has a time complexity of \(O(2^n)\) since each item has two possible outcomes (include or exclude), resulting in an exponential number of possibilities.


``` java
    // Brute force Solution
    // Time: O(2^n), Space: O(n)
    // Where n is the number of items.
    public static int dfs(List<Integer> profit, List<Integer> weight, int capacity) {
        return dfsHelper(0, profit, weight, capacity);
    }

    public static int dfsHelper(int i, List<Integer> profit, List<Integer> weight, int capacity) {
        if (i == profit.size()) {
            return 0;
        }

        // Skip item i
        int maxProfit = dfsHelper(i + 1, profit, weight, capacity);

        // Include item i
        int newCap = capacity - weight.get(i);
        if (newCap >= 0) {
            int p = profit.get(i) + dfsHelper(i + 1, profit, weight, newCap);
            // Compute the max
            maxProfit = Math.max(maxProfit, p);
        }
        return maxProfit;
    }

```
### Memoization (Top-down with caching):

- **Explanation**: 
  - In this approach, we create a 2D grid (`m*n`), where `m` represents the number of items, and `n` represents the knapsack's capacity. This grid helps us store intermediate results.
  - Each time we subtract the item's weight from the remaining capacity, we check if the result brings us to a previously computed state (i.e., the same item index with the same remaining capacity). If it does, we use the stored result rather than recomputing it.
  - By storing these results in a cache, we avoid redundant calculations, making the algorithm much faster compared to brute force.
	```java
	// Memoization Solution
    // Time: O(n * m), Space: O(n * m)
    // Where n is the number of items & m is the capacity.
    public static int memoization(List<Integer> profit, List<Integer> weight, int capacity) {
        // A 2d array, with N rows and M + 1 columns, init with -1's
        int N = profit.size(), M = capacity;
        List<Integer[]> cache = new ArrayList<>();
        for (int row = 0; row < N; row++) {
            cache.add(row, new Integer[M + 1]);
            Arrays.fill(cache.get(row), -1);
        }

        return memoHelper(0, profit, weight, capacity, cache);
    }

    public static int memoHelper(int i, List<Integer> profit, List<Integer> weight, 
        int capacity, List<Integer[]> cache) {
        if (i == profit.size()) {
            return 0;
        }
        if (cache.get(i)[capacity] != -1) {
            return cache.get(i)[capacity];
        }

        // Skip item i
        cache.get(i)[capacity] = memoHelper(i + 1, profit, weight, capacity, cache);

        // Include item i
        int newCap = capacity - weight.get(i);
        if (newCap >= 0) {
            int p = profit.get(i) + memoHelper(i + 1, profit, weight, newCap, cache);
            // Compute the max
            cache.get(i)[capacity] = Math.max(cache.get(i)[capacity], p);  
        }
        return cache.get(i)[capacity];
    } 
	```


### Dynamic Programming (Bottom-up approach):
- **Explanation**: 
  - Dynamic programming (DP) solves the problem iteratively instead of using recursion. 
  - Instead of subtracting the capacity from the total repeatedly as in brute force, we incrementally build a DP table (grid `m*n`) that tracks the maximum profit achievable for each item at different capacities.
  - First, we initialize the table to represent different capacities and fill it with profits for each item as we go along. We only add an item if its weight fits within the current capacity.
  - For instance, if we have weights `[5, 2, 3, 1]` and profits `[4, 4, 7, 1]`, we go row by row, checking if the current item fits in the knapsack. If it does, we check whether adding it results in more profit than skipping it.
  - The final value at the bottom-right corner of the grid (`dp[n-1][m]`) gives us the maximum possible profit for the given knapsack capacity.

``` java
	

    // Dynamic Programming Solution
    // Time: O(n * m), Space: O(n * m)
    // Where n is the number of items & m is the capacity.
    public static int dp(List<Integer> profit, List<Integer> weight, int capacity) {
        int N = profit.size(), M = capacity;
        List<Integer[]> dp = new ArrayList<>();
        for (int row = 0; row < N; row++) {
            dp.add(row, new Integer[M + 1]);
            Arrays.fill(dp.get(row), 0);
        }

        // Fill the first column and row to reduce edge cases
        for (int i = 0; i < N; i++) {
            dp.get(i)[0] = 0;
        }
        for (int c = 0; c <= M; c++) {
            if (weight.get(0) <= c) {
                dp.get(0)[c] = profit.get(0);
            } 
        }

        for (int i = 1; i < N; i++) {
            for (int c = 1; c <= M; c++) {
                int skip = dp.get(i-1)[c];
                int include = 0;
                if (c - weight.get(i) >= 0) {
                    include = profit.get(i) + dp.get(i-1)[c - weight.get(i)];
                }
                dp.get(i)[c] = Math.max(include, skip);
            }
        }
        return dp.get(N-1)[M];
    }


    // Memory optimized Dynamic Programming Solution
    // Time: O(n * m), Space: O(m)
    public static int optimizedDp(List<Integer> profit, List<Integer> weight, int capacity) {
        int N = profit.size(), M = capacity;
        Integer[] dp = new Integer[M+1];
        Arrays.fill(dp, 0);

        // Fill the first row to reduce edge cases
        for (int c = 0; c <= M; c++) {
            if (weight.get(0) <= c) {
                dp[c] = profit.get(0);
            } 
        }

        for (int i = 1; i < N; i++) {
            Integer[] curRow = new Integer[M+1];
            Arrays.fill(curRow, 0);
            for (int c = 1; c <= M; c++) {
                int skip = dp[c];
                int include = 0;
                if (c - weight.get(i) >= 0) {
                    include = profit.get(i) + dp[c - weight.get(i)];
                }
                curRow[c] =  Math.max(include, skip);
            }
            dp = curRow;
        }
        return dp[M];
    }
}
```
---

### Code Breakdown:

1. **Brute Force**:
   - In this method, the helper function (`dfsHelper`) recursively explores all possible combinations of including and excluding each item. The capacity is reduced as we decide to include items, and we calculate profits for all such combinations.

2. **Memoization**:
   - We use a 2D array (`cache`) to store previously calculated results. Whenever we revisit the same item index and capacity, we retrieve the precomputed result from the cache, which saves time by avoiding redundant calculations.

3. **Dynamic Programming**:
   - Here, we create a DP table (`dp`) where each cell stores the maximum profit for a given item and knapsack capacity. For each item, we either add it to the knapsack (if its weight fits) or skip it. The table is filled row by row, and the final cell in the bottom-right corner gives the maximum achievable profit.

4. **Optimized DP**:
   - Instead of maintaining a full 2D table, the memory-optimized DP version only keeps track of the previous and current rows, reducing the space complexity to \(O(m)\) while still computing the same result.

---

### Refined Notes (in My own words):

- **Brute Force**: In brute force, we reduce the knapsack’s total capacity by subtracting the current item's weight and recursively deciding whether to include or omit the item. Each time we make a choice, we repeat the same process for the remaining items. If the remaining capacity and items are the same as a previous state, we recompute the same thing multiple times.

- **Memoization**: By using a grid (`m*n`) where `m` is the number of items and `n` is the knapsack capacity, we avoid redundant calculations. If we subtract an item’s weight and find ourselves in a previously visited state, we can retrieve the result from our cache instead of recalculating. This method saves time by storing intermediate results.

- **Dynamic Programming**: Unlike brute force, we don't keep subtracting the total capacity. Instead, we initialize a DP table where we progressively add item weights and compare profits for each capacity. For example, with profits `[4, 4, 7, 1]` and weights `[5, 2, 3, 1]`, we check if adding the current item increases the profit more than skipping it. The table ultimately holds the maximum profit in its last cell.
