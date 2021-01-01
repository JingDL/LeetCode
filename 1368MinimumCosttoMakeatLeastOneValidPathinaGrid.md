### 1368. Minimum Cost to Make at Least One Valid Path in a Grid

Given a m x n grid. Each cell of the grid has a sign pointing to the next cell you should visit if you are currently in this cell. The sign of grid[i][j] can be:

1 which means go to the cell to the right. (i.e go from grid[i][j] to grid[i][j + 1])

2 which means go to the cell to the left. (i.e go from grid[i][j] to grid[i][j - 1])

3 which means go to the lower cell. (i.e go from grid[i][j] to grid[i + 1][j])

4 which means go to the upper cell. (i.e go from grid[i][j] to grid[i - 1][j])

Notice that there could be some invalid signs on the cells of the grid which points outside the grid.

You will initially start at the upper left cell (0,0). A valid path in the grid is a path which starts from the upper left cell (0,0) and ends at the bottom-right cell (m - 1, n - 1) following the signs on the grid. The valid path doesn't have to be the shortest.

You can modify the sign on a cell with cost = 1. You can modify the sign on a cell one time only.

Return the minimum cost to make the grid have at least one valid path.

 

Example 1:

![Image](https://assets.leetcode.com/uploads/2020/02/13/grid1.png)

Input: grid = [[1,1,1,1],[2,2,2,2],[1,1,1,1],[2,2,2,2]]

Output: 3

Explanation: You will start at point (0, 0).
The path to (3, 3) is as follows. (0, 0) --> (0, 1) --> (0, 2) --> (0, 3) change the arrow to down with cost = 1 --> (1, 3) --> (1, 2) --> (1, 1) --> (1, 0) change the arrow to down with cost = 1 --> (2, 0) --> (2, 1) --> (2, 2) --> (2, 3) change the arrow to down with cost = 1 --> (3, 3)
The total cost = 3.

Example 2:

![Image](https://assets.leetcode.com/uploads/2020/02/13/grid2.png)

Input: grid = [[1,1,3],[3,2,2],[1,1,4]]

Output: 0

Explanation: You can follow the path from (0, 0) to (2, 2).

Example 3:

![Image](https://assets.leetcode.com/uploads/2020/02/13/grid3.png)

Input: grid = [[1,2],[4,3]]

Output: 1

Example 4:

Input: grid = [[2,2,2],[2,2,2]]

Output: 3

Example 5:

Input: grid = [[4]]

Output: 0
 

Constraints:

m == grid.length

n == grid[i].length

1 <= m, n <= 100

#### My Solutions in Java
```
/*
    Just like Dijkstra's algorithm, we first find the nodes with cost of 0, then nodes with cost of 1, ...  
We use a FIFO queue to track the nodes we visited.  Because the cost monotonically increases, the FIFO queue
is incrementally sorted items of the nodes' costs.  
    To ensure we don't resist the nodes we visited in the past, we compare the current cost with the node's
recorded cost.  At beginning, we assign every node with cost of Integer.MAX_VALUE. Only when the current cost
is lower than the recorded cost, we visit that node.  This helps us avoid infinite loops.  For example, if
[0,0] points to [0,1], and [0,1] points to [0,0]. For this case, we should only put [0,0] to our FIFO queue
once.
    By following the above scheme, we put a node into our FIFO queue only once.  Therefore, the time
complexity and space complexity both are O(m*n), where m is the height of grid, and n is the width of grid.
*/
class Solution {
    static final int DIR[][] = {{0,0}, {0, 1},{0, -1},{1, 0},{-1, 0}};  // changes for values 1, 2, 3, and 4
    public int minCost(int[][] grid) {
        int height = 0, width = 0;
        if(grid == null || (height = grid.length) == 0 || (width = grid[0].length) == 0){
            return 0;
        }
        
        int expense = 0;
        Deque<int[]> queue = new ArrayDeque<>();
        int[][] costs = new int[height][width];
        for(int row = 0; row < height; row++){
            Arrays.fill(costs[row], Integer.MAX_VALUE);
        }
        getReachable(grid, costs, queue, 0, 0, expense);
        
        
        while(!queue.isEmpty()){
            if(costs[height-1][width-1] != Integer.MAX_VALUE){
                break;
            }
            expense++;
            int size = queue.size();
            while(size != 0){
                int[] current = queue.pollFirst();
                for(int i = 1; i < 5; i++){
                    getReachable(grid, costs, queue, current[0] + DIR[i][0], current[1] + DIR[i][1], expense);
                }
                size--;
            }
        }
        return expense;
    }
    
    private void getReachable(int[][] grid, int[][] costs, Deque<int[]> queue, int row, int col, int expense){
        if(row < 0 || row >= grid.length || col < 0 || col >= grid[0].length || expense >= costs[row][col]){
            return;
        }
        
        costs[row][col] = expense;
        queue.offerLast(new int[]{row, col});
        getReachable(grid, costs, queue, row + DIR[grid[row][col]][0], col + DIR[grid[row][col]][1], expense);
    }
}
```

#### My Solution in C++
```
class Solution {
public:
    int minCost(vector<vector<int>>& grid) {
        int height = 0, width = 0;
        if((height = grid.size()) == 0 || (width = grid[0].size()) == 0){
            return 0;
        }
        
        vector<vector<int>> costs(height, vector<int>(width, INT_MAX));
        list<pair<int, int>> queue;
        int cost = 0;
        if(isLastReached(grid, costs, queue, 0, 0, cost, height, width)){
            return 0;
        }
        
        while(!queue.empty()){
            cost++;
            int size = queue.size();
            while(size){
                size--;
                pair<int,int> & current = queue.front();
                for(int i = 1; i < 5; i++){
                    if(isLastReached(grid, costs, queue, current.first+DIR[i][0], 
                                     current.second+DIR[i][1], cost, height, width)){
                        return cost;
                    }
                }
                queue.pop_front();
            }
        }
        return cost;
    }
private:
    static const int DIR[5][2];
    bool isLastReached(vector<vector<int>>& grid, vector<vector<int>>& costs, list<pair<int,int>>& queue, int row, int col, int cost, int height, int width){
        if(row == height - 1 && col == width - 1){
            costs[row][col] = cost;
            return true;
        }
        
        if(row < 0 || row >= height || col < 0 || col >= width || cost >= costs[row][col]){
            return false;
        }
        
        queue.emplace_back(row, col);
        costs[row][col] = cost;
        return isLastReached(grid, costs, queue, row+DIR[grid[row][col]][0], col+DIR[grid[row][col]][1], cost, height, width);
    }
};

const int Solution::DIR[5][2] = {{0,0},{0,1},{0,-1},{1,0},{-1,0}};
```
