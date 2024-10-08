# [909. Snakes and Ladders (Medium)](https://leetcode.com/problems/snakes-and-ladders/)

<p>You are given an <code>n x n</code> integer matrix <code>board</code> where the cells are labeled from <code>1</code> to <code>n<sup>2</sup></code> in a <a href="https://en.wikipedia.org/wiki/Boustrophedon" target="_blank"><strong>Boustrophedon style</strong></a> starting from the bottom left of the board (i.e. <code>board[n - 1][0]</code>) and alternating direction each row.</p>

<p>You start on square <code>1</code> of the board. In each move, starting from square <code>curr</code>, do the following:</p>

<ul>
	<li>Choose a destination square <code>next</code> with a label in the range <code>[curr + 1, min(curr + 6, n<sup>2</sup>)]</code>.

	<ul>
		<li>This choice simulates the result of a standard <strong>6-sided die roll</strong>: i.e., there are always at most 6 destinations, regardless of the size of the board.</li>
	</ul>
	</li>
	<li>If <code>next</code> has a snake or ladder, you <strong>must</strong> move to the destination of that snake or ladder. Otherwise, you move to <code>next</code>.</li>
	<li>The game ends when you reach the square <code>n<sup>2</sup></code>.</li>
</ul>

<p>A board square on row <code>r</code> and column <code>c</code> has a snake or ladder if <code>board[r][c] != -1</code>. The destination of that snake or ladder is <code>board[r][c]</code>. Squares <code>1</code> and <code>n<sup>2</sup></code> do not have a snake or ladder.</p>

<p>Note that you only take a snake or ladder at most once per move. If the destination to a snake or ladder is the start of another snake or ladder, you do <strong>not</strong> follow the subsequent&nbsp;snake or ladder.</p>

<ul>
	<li>For example, suppose the board is <code>[[-1,4],[-1,3]]</code>, and on the first move, your destination square is <code>2</code>. You follow the ladder to square <code>3</code>, but do <strong>not</strong> follow the subsequent ladder to <code>4</code>.</li>
</ul>

<p>Return <em>the least number of moves required to reach the square </em><code>n<sup>2</sup></code><em>. If it is not possible to reach the square, return </em><code>-1</code>.</p>

<p>&nbsp;</p>
<p><strong>Example 1:</strong></p>
<img alt="" src="https://assets.leetcode.com/uploads/2018/09/23/snakes.png" style="width: 500px; height: 394px;">
<pre><strong>Input:</strong> board = [[-1,-1,-1,-1,-1,-1],[-1,-1,-1,-1,-1,-1],[-1,-1,-1,-1,-1,-1],[-1,35,-1,-1,13,-1],[-1,-1,-1,-1,-1,-1],[-1,15,-1,-1,-1,-1]]
<strong>Output:</strong> 4
<strong>Explanation:</strong> 
In the beginning, you start at square 1 (at row 5, column 0).
You decide to move to square 2 and must take the ladder to square 15.
You then decide to move to square 17 and must take the snake to square 13.
You then decide to move to square 14 and must take the ladder to square 35.
You then decide to move to square 36, ending the game.
This is the lowest possible number of moves to reach the last square, so return 4.
</pre>

<p><strong>Example 2:</strong></p>

<pre><strong>Input:</strong> board = [[-1,-1],[-1,3]]
<strong>Output:</strong> 1
</pre>

<p>&nbsp;</p>
<p><strong>Constraints:</strong></p>

<ul>
	<li><code>n == board.length == board[i].length</code></li>
	<li><code>2 &lt;= n &lt;= 20</code></li>
	<li><code>grid[i][j]</code> is either <code>-1</code> or in the range <code>[1, n<sup>2</sup>]</code>.</li>
	<li>The squares labeled <code>1</code> and <code>n<sup>2</sup></code> do not have any ladders or snakes.</li>
</ul>


**Companies**:  
[Amazon](https://leetcode.com/company/amazon), [Goldman Sachs](https://leetcode.com/company/goldman-sachs), [Uber](https://leetcode.com/company/uber), [Google](https://leetcode.com/company/google), [Adobe](https://leetcode.com/company/adobe)

**Related Topics**:  
[Array](https://leetcode.com/tag/array/), [Breadth-First Search](https://leetcode.com/tag/breadth-first-search/), [Matrix](https://leetcode.com/tag/matrix/)

## Solution 1. BFS

	Initialization: A queue is initialized with the starting cell (1), and a boolean vector tracks visited cells.
	BFS Loop: For each position, it explores the next 1 to 6 possible moves (dice rolls).
	Coordinate Mapping: The board is converted from a 1D representation to a 2D grid to account for the zigzag layout.
	Ladders and Snakes: It checks for ladders or snakes at each position, adjusting the target cell accordingly.
	End Condition: If the end cell (N*N) is reached, the number of steps taken is returned; otherwise, the search continues until all options are exhausted. If unreachable, it returns -1.

```cpp
// OJ: https://leetcode.com/problems/snakes-and-ladders/
// Author: github.com/lzl124631x
// Time: O(N^2)
// Space: O(N^2)

// OJ: https://leetcode.com/problems/snakes-and-ladders/
// Author: github.com/lzl124631x
// Time: O(N^2)
// Space: O(N^2)

class Solution {
public:
    int snakesAndLadders(vector<vector<int>>& A) {
        int N = A.size(), step = 0; // Size of the board and current step count
        vector<bool> seen(N * N + 1); // Track visited cells
        seen[1] = true; // Mark the starting cell as visited
        queue<int> q{{1}}; // Initialize the queue with the starting position

        while (q.size()) {
            int cnt = q.size(); // Number of positions to explore at this step
            while (cnt--) {
                int u = q.front(); // Current position
                if (u == N * N) return step; // Reached the end
                q.pop();
                // Explore possible moves (1 to 6)
                for (int v = u + 1; v <= min(N * N, u + 6); ++v) {
                    int x = (v - 1) / N, y = (v - 1) % N; // Convert to 2D coordinates
                    if (x % 2) y = N - 1 - y; // Adjust for zigzag pattern
                    x = N - 1 - x; // Flip the x-coordinate for board orientation
                    int next = A[x][y] == -1 ? v : A[x][y]; // Check for snakes or ladders
                    if (seen[next]) continue; // Skip if already visited
                    seen[next] = true; // Mark the next position as visited
                    q.push(next); // Add the next position to the queue
                }
            }
            ++step; // Increment the step count after exploring all positions
        }
        return -1; // Return -1 if the end cannot be reached
    }
};

```
