# [1489. Find Critical and Pseudo-Critical Edges in Minimum Spanning Tree (Hard)](https://leetcode.com/problems/find-critical-and-pseudo-critical-edges-in-minimum-spanning-tree/)

<p>Given a weighted undirected connected graph with <code>n</code>&nbsp;vertices numbered from <code>0</code> to <code>n-1</code>,&nbsp;and an array <code>edges</code>&nbsp;where <code>edges[i] = [from<sub>i</sub>, to<sub>i</sub>, weight<sub>i</sub>]</code> represents a bidirectional and weighted edge between nodes&nbsp;<code>from<sub>i</sub></code>&nbsp;and <code>to<sub>i</sub></code>. A minimum spanning tree (MST) is a subset of the edges of the graph that connects all vertices without cycles&nbsp;and with the minimum possible total edge weight.</p>

<p>Find <em>all the critical and pseudo-critical edges in the minimum spanning tree (MST) of the given graph</em>. An MST edge whose deletion from the graph would cause the MST weight to increase is called a&nbsp;<em>critical edge</em>. A <em>pseudo-critical edge</em>, on the other hand, is that which can appear in some MSTs but not all.</p>

<p>Note that you can return the indices of the edges in any order.</p>

<p>&nbsp;</p>
<p><strong>Example 1:</strong></p>

<p><img alt="" src="https://assets.leetcode.com/uploads/2020/06/04/ex1.png" style="width: 259px; height: 262px;"></p>

<pre><strong>Input:</strong> n = 5, edges = [[0,1,1],[1,2,1],[2,3,2],[0,3,2],[0,4,3],[3,4,3],[1,4,6]]
<strong>Output:</strong> [[0,1],[2,3,4,5]]
<strong>Explanation:</strong> The figure above describes the graph.
The following figure shows all the possible MSTs:
<img alt="" src="https://assets.leetcode.com/uploads/2020/06/04/msts.png" style="width: 540px; height: 553px;">
Notice that the two edges 0 and 1 appear in all MSTs, therefore they are critical edges, so we return them in the first list of the output.
The edges 2, 3, 4, and 5 are only part of some MSTs, therefore they are considered pseudo-critical edges. We add them to the second list of the output.
</pre>

<p><strong>Example 2:</strong></p>

<p><img alt="" src="https://assets.leetcode.com/uploads/2020/06/04/ex2.png" style="width: 247px; height: 253px;"></p>

<pre><strong>Input:</strong> n = 4, edges = [[0,1,1],[1,2,1],[2,3,1],[0,3,1]]
<strong>Output:</strong> [[],[0,1,2,3]]
<strong>Explanation:</strong> We can observe that since all 4 edges have equal weight, choosing any 3 edges from the given 4 will yield an MST. Therefore all 4 edges are pseudo-critical.
</pre>

<p>&nbsp;</p>
<p><strong>Constraints:</strong></p>

<ul>
	<li><code>2 &lt;= n &lt;= 100</code></li>
	<li><code>1 &lt;= edges.length &lt;= min(200, n * (n - 1) / 2)</code></li>
	<li><code>edges[i].length == 3</code></li>
	<li><code>0 &lt;= from<sub>i</sub> &lt; to<sub>i</sub> &lt; n</code></li>
	<li><code>1 &lt;= weight<sub>i</sub>&nbsp;&lt;= 1000</code></li>
	<li>All pairs <code>(from<sub>i</sub>, to<sub>i</sub>)</code> are distinct.</li>
</ul>


**Related Topics**:  
[Depth-first Search](https://leetcode.com/tag/depth-first-search/), [Union Find](https://leetcode.com/tag/union-find/)

## Solution 1. Kruskal

	Union-Find Structure: The UnionFind class manages connected components, enabling efficient union and find operations to determine if two nodes are connected.
	
	Kruskal’s Algorithm: The kruskal function calculates the weight of the Minimum Spanning Tree (MST) while optionally including or excluding specific edges to evaluate their impact on connectivity.
	
	Edge Classification:
	
	    All edges are sorted by weight, and the MST cost is computed.
	    Each edge is analyzed:
	        If its removal increases the MST cost, it's marked as a critical edge.
	        If including it does not change the MST cost, it's marked as a pseudo-critical edge.
	
	Final Output: The method returns two lists: one for critical edges and another for pseudo-critical edges, providing a clear classification based on their roles in the MST.

```cpp
// OJ: https://leetcode.com/problems/find-critical-and-pseudo-critical-edges-in-minimum-spanning-tree/
// Author: github.com/lzl124631x
// Time: O(ElogE + E^2)
// Space: O(N)
// Ref: https://youtu.be/GzPUvV85kBI

class UnionFind {
    vector<int> id; // Array to track the parent of each node
    int size; // Number of connected components
public:
    UnionFind(int N) : id(N), size(N) {
        iota(begin(id), end(id), 0); // Initialize the union-find structure
    }

    int find(int x) {
        return id[x] == x ? x : (id[x] = find(id[x])); // Path compression
    }

    bool connect(int x, int y) {
        int p = find(x), q = find(y);
        if (p == q) return false; // Already connected
        id[p] = q; // Union operation
        --size; // Decrease the number of components
        return true;
    }

    int getSize() { return size; } // Return the number of connected components
};

class Solution {
    int kruskal(int n, vector<vector<int>> &E, int include = -1, int exclude = -1) {
        int cost = 0; // Initialize total cost of the MST
        UnionFind uf(n); // Create a new UnionFind for the current MST calculation
        if (include != -1) { // If an edge should be included
            auto &e = E[include];
            uf.connect(e[0], e[1]); // Connect nodes
            cost += e[2]; // Add edge weight to cost
        }
        // Process all edges except the excluded one
        for (int i = 0; i < E.size(); ++i) {
            if (i == include || i == exclude) continue;
            int u = E[i][0], v = E[i][1], w = E[i][2], id = E[i][3];
            if (!uf.connect(u, v)) continue; // Connect nodes if not already connected
            cost += w; // Add edge weight to cost
            if (uf.getSize() == 1) break; // Stop if all nodes are connected
        }
        return uf.getSize() == 1 ? cost : INT_MAX; // Return cost if connected, else INT_MAX
    }

public:
    vector<vector<int>> findCriticalAndPseudoCriticalEdges(int n, vector<vector<int>>& E) {
        for (int i = 0; i < E.size(); ++i) E[i].push_back(i); // Add original index to edges
        sort(begin(E), end(E), [](vector<int> &a, vector<int> &b) { return a[2] < b[2]; }); // Sort edges by weight
        int cost = kruskal(n, E); // Calculate the cost of the MST
        vector<int> c, p; // Vectors for critical and pseudo-critical edges

        // Check each edge to classify as critical or pseudo-critical
        for (int i = 0; i < E.size(); ++i) {
            if (kruskal(n, E, -1, i) > cost) c.push_back(E[i][3]); // Critical edge
            else if (kruskal(n, E, i) == cost) p.push_back(E[i][3]); // Pseudo-critical edge
        }
        return { c, p }; // Return both edge lists
    }
};

```
