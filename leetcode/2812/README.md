# [2812. Find the Safest Path in a Grid (Medium)](https://leetcode.com/problems/find-the-safest-path-in-a-grid)

<p>You are given a <strong>0-indexed</strong> 2D matrix <code>grid</code> of size <code>n x n</code>, where <code>(r, c)</code> represents:</p>

<ul>
	<li>A cell containing a thief if <code>grid[r][c] = 1</code></li>
	<li>An empty cell if <code>grid[r][c] = 0</code></li>
</ul>

<p>You are initially positioned at cell <code>(0, 0)</code>. In one move, you can move to any adjacent cell in the grid, including cells containing thieves.</p>

<p>The <strong>safeness factor</strong> of a path on the grid is defined as the <strong>minimum</strong> manhattan distance from any cell in the path to any thief in the grid.</p>

<p>Return <em>the <strong>maximum safeness factor</strong> of all paths leading to cell </em><code>(n - 1, n - 1)</code><em>.</em></p>

<p>An <strong>adjacent</strong> cell of cell <code>(r, c)</code>, is one of the cells <code>(r, c + 1)</code>, <code>(r, c - 1)</code>, <code>(r + 1, c)</code> and <code>(r - 1, c)</code> if it exists.</p>

<p>The <strong>Manhattan distance</strong> between two cells <code>(a, b)</code> and <code>(x, y)</code> is equal to <code>|a - x| + |b - y|</code>, where <code>|val|</code> denotes the absolute value of val.</p>

<p>&nbsp;</p>
<p><strong class="example">Example 1:</strong></p>
<img alt="" src="https://assets.leetcode.com/uploads/2023/07/02/example1.png" style="width: 362px; height: 242px;" />
<pre>
<strong>Input:</strong> grid = [[1,0,0],[0,0,0],[0,0,1]]
<strong>Output:</strong> 0
<strong>Explanation:</strong> All paths from (0, 0) to (n - 1, n - 1) go through the thieves in cells (0, 0) and (n - 1, n - 1).
</pre>

<p><strong class="example">Example 2:</strong></p>
<img alt="" src="https://assets.leetcode.com/uploads/2023/07/02/example2.png" style="width: 362px; height: 242px;" />
<pre>
<strong>Input:</strong> grid = [[0,0,1],[0,0,0],[0,0,0]]
<strong>Output:</strong> 2
<strong>Explanation:</strong> The path depicted in the picture above has a safeness factor of 2 since:
- The closest cell of the path to the thief at cell (0, 2) is cell (0, 0). The distance between them is | 0 - 0 | + | 0 - 2 | = 2.
It can be shown that there are no other paths with a higher safeness factor.
</pre>

<p><strong class="example">Example 3:</strong></p>
<img alt="" src="https://assets.leetcode.com/uploads/2023/07/02/example3.png" style="width: 362px; height: 242px;" />
<pre>
<strong>Input:</strong> grid = [[0,0,0,1],[0,0,0,0],[0,0,0,0],[1,0,0,0]]
<strong>Output:</strong> 2
<strong>Explanation:</strong> The path depicted in the picture above has a safeness factor of 2 since:
- The closest cell of the path to the thief at cell (0, 3) is cell (1, 2). The distance between them is | 0 - 1 | + | 3 - 2 | = 2.
- The closest cell of the path to the thief at cell (3, 0) is cell (3, 2). The distance between them is | 3 - 3 | + | 0 - 2 | = 2.
It can be shown that there are no other paths with a higher safeness factor.
</pre>

<p>&nbsp;</p>
<p><strong>Constraints:</strong></p>

<ul>
	<li><code>1 &lt;= grid.length == n &lt;= 400</code></li>
	<li><code>grid[i].length == n</code></li>
	<li><code>grid[i][j]</code> is either <code>0</code> or <code>1</code>.</li>
	<li>There is at least one thief in the <code>grid</code>.</li>
</ul>


**Companies**:
[IMC](https://leetcode.com/company/imc), [Google](https://leetcode.com/company/google)

**Related Topics**:  
[Array](https://leetcode.com/tag/array), [Binary Search](https://leetcode.com/tag/binary-search), [Breadth-First Search](https://leetcode.com/tag/breadth-first-search), [Union Find](https://leetcode.com/tag/union-find), [Matrix](https://leetcode.com/tag/matrix)

**Similar Questions**:
* [Path With Minimum Effort (Medium)](https://leetcode.com/problems/path-with-minimum-effort)

**Hints**:
* Consider using both BFS and binary search together.
* Launch a BFS starting from all the cells containing thieves to calculate d[x][y] which is the smallest Manhattan distance from (x, y) to the nearest grid that contains thieves.
* To check if the bottom-right cell of the grid can be reached **through a path of safeness factor v**, eliminate all cells (x, y) such that grid[x][y]  < v. if (0, 0) and (n - 1, n - 1) are still connected, there exists a path between (0, 0) and (n - 1, n - 1) of safeness factor v.
* Binary search over the final safeness factor v.

## Solution 1.

1. BFS to calculate the distance to thieves for each node.
2. Dijkstra to calculate the maximum safeness factor.

The solution calculates the maximum safeness factor for a path from the top-left to the bottom-right corner of a grid containing bombs. It first uses a Breadth-First Search (BFS) to determine the distance from each bomb to all 
other cells in the grid, populating the dist array. Then, it employs a priority queue (max-heap) to implement a Dijkstra-like algorithm, maximizing the safeness factor along the path. Starting from the top-left cell, 
it explores neighboring cells, updating their safeness factors based on the minimum distance to the nearest bomb. If the bottom-right corner is reached, the maximum safeness factor is returned; otherwise, -1 is returned if no valid path exists.

```cpp
// OJ: https://leetcode.com/problems/find-the-safest-path-in-a-grid
// Author: github.com/lzl124631x
// Time: O(MNlog(MN))
// Space: O(MN)
class Solution {
public:
    int maximumSafenessFactor(vector<vector<int>>& A) {
        int N = A.size(), dirs[4][2] = {{0,1},{0,-1},{1,0},{-1,0}};
        vector<vector<int>> dist(N, vector<int>(N, INT_MAX)), factor(N, vector<int>(N, -1));
        queue<pair<int, int>> q;

        // Initialize distances for cells with bombs
        for (int i = 0; i < N; ++i) {
            for (int j = 0; j < N; ++j) {
                if (A[i][j] == 0) continue; // Skip empty cells
                q.emplace(i, j);
                dist[i][j] = 0; // Bomb distance is 0
            }
        }

        // BFS to calculate the distance from bombs
        while (q.size()) {
            int cnt = q.size();
            while (cnt--) {
                auto [x, y] = q.front();
                q.pop();
                for (auto &[dx, dy] : dirs) {
                    int a = x + dx, b = y + dy;
                    // Update distances for unvisited cells
                    if (a < 0 || a >= N || b < 0 || b >= N || dist[a][b] != INT_MAX) continue;
                    dist[a][b] = 1 + dist[x][y];
                    q.emplace(a, b);
                }
            }
        }

        typedef array<int, 3> Node; // factor, x, y
        priority_queue<Node> pq; // Max-heap for safeness factors
        factor[0][0] = dist[0][0]; // Initialize factor for starting cell
        pq.push({factor[0][0], 0, 0});

        // Dijkstra-like approach to find the maximum safeness factor path
        while (pq.size()) {
            auto [f, x, y] = pq.top();
            if (x == N - 1 && y == N - 1) return f; // Return if target reached
            pq.pop();
            for (auto &[dx, dy] : dirs) {
                int a = x + dx, b = y + dy;
                if (a < 0 || a >= N || b < 0 || b >= N || factor[a][b] != -1) continue;
                factor[a][b] = min(dist[a][b], factor[x][y]); // Update safeness factor
                pq.push({factor[a][b], a, b});
            }
        }
        return -1; // Return -1 if no path found
    }
};

```

## Solution 2.

1. BFS to calculate the distance to thieves for each node.
2. Binary Search `limit` within `[0, maximumDistance]` to see if there exists a path each cell of which has a distance `>= limit`. The maximum limit is the answer.

The algorithm calculates the distance of each cell to the nearest bomb using BFS. It then employs binary search to determine the maximum safeness factor, checking if a path exists from the top-left 
to the bottom-right corner where each cell's distance to a bomb meets a certain threshold. The process continues until the maximum valid safeness factor is found, which is returned as the result.

```cpp
// OJ: https://leetcode.com/problems/find-the-safest-path-in-a-grid
// Author: github.com/lzl124631x
// Time: O(MNlog(M + N))
// Space: O(MN)
class Solution {
public:
    int maximumSafenessFactor(vector<vector<int>>& A) {
        int N = A.size(), dirs[4][2] = {{0,1},{0,-1},{1,0},{-1,0}}, maxDist = 0;
        vector<vector<int>> dist(N, vector<int>(N, INT_MAX));
        queue<pair<int, int>> q;

        // Initialize distances for cells with bombs
        for (int i = 0; i < N; ++i) {
            for (int j = 0; j < N; ++j) {
                if (A[i][j] == 0) continue; // Skip empty cells
                q.emplace(i, j);
                dist[i][j] = 0; // Bomb distance is 0
            }
        }

        // BFS to calculate the distance from bombs
        while (q.size()) {
            int cnt = q.size();
            while (cnt--) {
                auto [x, y] = q.front();
                q.pop();
                for (auto &[dx, dy] : dirs) {
                    int a = x + dx, b = y + dy;
                    // Update distances for unvisited cells
                    if (a < 0 || a >= N || b < 0 || b >= N || dist[a][b] != INT_MAX) continue;
                    dist[a][b] = 1 + dist[x][y];
                    maxDist = max(maxDist, dist[a][b]); // Track the maximum distance
                    q.emplace(a, b);
                }
            }
        }

        // Binary search to find the maximum safeness factor
        int L = 0, R = maxDist;
        vector<vector<bool>> seen;
        auto valid = [&](int limit) { // Check if a path exists with distance >= limit
            seen.assign(N, vector<bool>(N));
            function<bool(int, int)> dfs = [&](int x, int y) -> bool {
                if (seen[x][y]) return false; // Avoid cycles
                seen[x][y] = true;
                if (dist[x][y] < limit) return false; // Check distance
                if (x == N - 1 && y == N - 1) return true; // Reached target
                for (auto &[dx, dy] : dirs) {
                    int a = x + dx, b = y + dy;
                    if (a < 0 || a >= N || b < 0 || b >= N) continue;
                    if (dfs(a, b)) return true; // Explore neighbors
                }
                return false;
            };
            return dfs(0, 0); // Start DFS from the top-left corner
        };

        // Perform binary search to find the highest valid safeness factor
        while (L <= R) {
            int M = (L + R) / 2;
            if (valid(M)) L = M + 1; // Move right if valid
            else R = M - 1; // Move left if not valid
        }
        return R; // Return the maximum safeness factor found
    }
};

```
