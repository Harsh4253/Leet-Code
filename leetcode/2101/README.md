# [2101. Detonate the Maximum Bombs (Medium)](https://leetcode.com/problems/detonate-the-maximum-bombs/)

<p>You are given a list of bombs. The <strong>range</strong> of a bomb is defined as the area where its effect can be felt. This area is in the shape of a <strong>circle</strong> with the center as the location of the bomb.</p>

<p>The bombs are represented by a <strong>0-indexed</strong> 2D integer array <code>bombs</code> where <code>bombs[i] = [x<sub>i</sub>, y<sub>i</sub>, r<sub>i</sub>]</code>. <code>x<sub>i</sub></code> and <code>y<sub>i</sub></code> denote the X-coordinate and Y-coordinate of the location of the <code>i<sup>th</sup></code> bomb, whereas <code>r<sub>i</sub></code> denotes the <strong>radius</strong> of its range.</p>

<p>You may choose to detonate a <strong>single</strong> bomb. When a bomb is detonated, it will detonate <strong>all bombs</strong> that lie in its range. These bombs will further detonate the bombs that lie in their ranges.</p>

<p>Given the list of <code>bombs</code>, return <em>the <strong>maximum</strong> number of bombs that can be detonated if you are allowed to detonate <strong>only one</strong> bomb</em>.</p>

<p>&nbsp;</p>
<p><strong>Example 1:</strong></p>
<img alt="" src="https://assets.leetcode.com/uploads/2021/11/06/desmos-eg-3.png" style="width: 300px; height: 300px;">
<pre><strong>Input:</strong> bombs = [[2,1,3],[6,1,4]]
<strong>Output:</strong> 2
<strong>Explanation:</strong>
The above figure shows the positions and ranges of the 2 bombs.
If we detonate the left bomb, the right bomb will not be affected.
But if we detonate the right bomb, both bombs will be detonated.
So the maximum bombs that can be detonated is max(1, 2) = 2.
</pre>

<p><strong>Example 2:</strong></p>
<img alt="" src="https://assets.leetcode.com/uploads/2021/11/06/desmos-eg-2.png" style="width: 300px; height: 300px;">
<pre><strong>Input:</strong> bombs = [[1,1,5],[10,10,5]]
<strong>Output:</strong> 1
<strong>Explanation:
</strong>Detonating either bomb will not detonate the other bomb, so the maximum number of bombs that can be detonated is 1.
</pre>

<p><strong>Example 3:</strong></p>
<img alt="" src="https://assets.leetcode.com/uploads/2021/11/07/desmos-eg1.png" style="width: 300px; height: 300px;">
<pre><strong>Input:</strong> bombs = [[1,2,3],[2,3,1],[3,4,2],[4,5,3],[5,6,4]]
<strong>Output:</strong> 5
<strong>Explanation:</strong>
The best bomb to detonate is bomb 0 because:
- Bomb 0 detonates bombs 1 and 2. The red circle denotes the range of bomb 0.
- Bomb 2 detonates bomb 3. The blue circle denotes the range of bomb 2.
- Bomb 3 detonates bomb 4. The green circle denotes the range of bomb 3.
Thus all 5 bombs are detonated.
</pre>

<p>&nbsp;</p>
<p><strong>Constraints:</strong></p>

<ul>
	<li><code>1 &lt;= bombs.length&nbsp;&lt;= 100</code></li>
	<li><code>bombs[i].length == 3</code></li>
	<li><code>1 &lt;= x<sub>i</sub>, y<sub>i</sub>, r<sub>i</sub> &lt;= 10<sup>5</sup></code></li>
</ul>


**Similar Questions**:
* [Minesweeper (Medium)](https://leetcode.com/problems/minesweeper/)
* [Number of Provinces (Medium)](https://leetcode.com/problems/number-of-provinces/)
* [Max Area of Island (Medium)](https://leetcode.com/problems/max-area-of-island/)
* [Rotting Oranges (Medium)](https://leetcode.com/problems/rotting-oranges/)

## Solution 1. DFS

Time complexity: In the worst case, all `N` nodes are inter-connected. So for each node we need to enumerate its `N` neighbors. So the DFS has `O(N^2)` time complexity, and we need to repeat the DFS `N` times. Thus the overall time complexity is `O(N^3)`.

Graph Construction: It builds a directed graph where each bomb is a node, and a directed edge exists from bomb ii to bomb jj if bomb ii can cover bomb jj based on their positions and radii.
Coverage Check: The cover function determines if one bomb can cover another by checking if the distance between them is within the explosion radius of the first bomb.
DFS for Detonations: The algorithm iterates through each bomb, performing a depth-first search (DFS) to count how many bombs can be detonated starting from that bomb.
Maximum Count: It keeps track of the maximum number of detonated bombs across all starting points and returns that value as the result.

```cpp
// OJ: https://leetcode.com/problems/detonate-the-maximum-bombs/
// Author: github.com/lzl124631x
// Time: O(N^3)
// Space: O(N^2)

class Solution {
public:
    int maximumDetonation(vector<vector<int>>& A) {
        int N = A.size();
        vector<vector<int>> G(N); // Adjacency list for bomb connections
        
        // Build the graph based on coverage
        for (int i = 0; i < N; ++i) {
            for (int j = i + 1; j < N; ++j) {
                if (cover(A, i, j)) G[i].push_back(j); // i covers j
                if (cover(A, j, i)) G[j].push_back(i); // j covers i
            }
        }

        vector<bool> seen(N);
        int ans = 0;
        
        // Calculate maximum detonation starting from each bomb
        for (int i = 0; i < N; ++i) {
            seen.assign(N, false); // Reset seen for each starting bomb
            ans = max(ans, dfs(i, G, seen)); // Update maximum detonated bombs
        }
        return ans;
    }

private:
    // Check if bomb i covers bomb j
    static bool cover(const vector<vector<int>>& A, int i, int j) {
        return pow((long)A[i][0] - A[j][0], 2) + pow((long)A[i][1] - A[j][1], 2) <= pow((long)A[i][2], 2);
    }

    // Depth-first search to count detonated bombs
    int dfs(int u, vector<vector<int>>& G, vector<bool>& seen) {
        seen[u] = true; // Mark current bomb as seen
        int ans = 1; // Count the current bomb
        for (int v : G[u]) {
            if (seen[v]) continue; // Skip already seen bombs
            ans += dfs(v, G, seen); // Recur for connected bombs
        }
        return ans;
    }
};


```
