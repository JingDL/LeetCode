### 695. Max Area of Island

Given a non-empty 2D array grid of 0's and 1's, an island is a group of 1's (representing land) connected 4-directionally (horizontal or vertical.) You may assume all four edges of the grid are surrounded by water.

Find the maximum area of an island in the given 2D array. (If there is no island, the maximum area is 0.)

Example 1:

[[0,0,1,0,0,0,0,1,0,0,0,0,0],

 [0,0,0,0,0,0,0,1,1,1,0,0,0],

 [0,1,1,0,1,0,0,0,0,0,0,0,0],
 
 [0,1,0,0,1,1,0,0,1,0,1,0,0],
 
 [0,1,0,0,1,1,0,0,1,1,1,0,0],
 
 [0,0,0,0,0,0,0,0,0,0,1,0,0],
 
 [0,0,0,0,0,0,0,1,1,1,0,0,0],
 
 [0,0,0,0,0,0,0,1,1,0,0,0,0]]

Given the above grid, return 6. Note the answer is not 11, because the island must be connected 4-directionally.

Example 2:

[[0,0,0,0,0,0,0,0]]

Given the above grid, return 0.

Note: The length of each dimension in the given grid does not exceed 50.



#### My Solutions in Java
```
/*
    We use grid to track if we visited the cell.  Whenever we visit a cell with value of 1, we change it to -1, which means it is visited.  At the end, we restore the grid.
    Time complexity is O(n*m), and space complexity is O(n*m), where n is the height of grid, and m is the width of grid.  Although we don't need another 2D array to track visit status, the depth of recursion function dfs() could be n*m for the worst case scenario -- every cell is 1.
*/
class Solution {
    final static int[][] DELTA = {{1, 0}, {0, 1}, {-1, 0}, {0, -1}};
    public int maxAreaOfIsland(int[][] grid) {
        if(grid == null || grid.length == 0 || grid[0].length == 0){
            return 0;
        }
        
        int result = 0;
        for(int row = 0; row < grid.length; row++){
            for(int col = 0; col < grid[0].length; col++){
                if(grid[row][col] == 1){
                    result = Math.max(dfs(grid, row, col), result);
                }
            }
        }
        restore(grid);  // change -1 back to 1
        return result;
    }
    
    private int dfs(int[][] grid, int row, int col){
        if(row < 0 || row >= grid.length || col < 0 || col >= grid[0].length || grid[row][col] <= 0){
            return 0;
        }
        
        grid[row][col] = -1;  // -1 menas vistied
        int result = 1;
        for(int[] delta : DELTA){
            result += dfs(grid, row + delta[0], col + delta[1]);
        }
        return result;
    }
    
    private void restore(int[][] grid){
        for(int row = 0; row < grid.length; row++){
            for(int col = 0; col < grid[0].length; col++){
                grid[row][col] = grid[row][col] < 0 ? 1 : 0;
            }
        }
    }
}
```


#### My Solution in C++
```
/*
    Time complexity and space complexity both are O(n*m), where n is the height of grid, m is the width of grid
*/
class Solution {
public:
    int maxAreaOfIsland(vector<vector<int>>& grid) {
        int height, width;
        if((height = grid.size()) == 0 || (width = grid[0].size()) == 0){
            return 0;
        }
        
        int result = 0;
        for(int row = 0; row < height; row++){
            for(int col = 0; col < width; col++){
                if(grid[row][col] == 1){
                    result = max(result, dfs(grid, row, col, height, width));
                }
            }
        }
        
        // restore the grid
        for(int row = 0; row < height; row++){
            for(int col = 0; col < width; col++){
                grid[row][col] = grid[row][col] < 0 ? 1 : 0;
            }
        }
        return result;
    }
private:
    static const int DELTA[4][2];
    
    int dfs(vector<vector<int>>& grid, int row, int col, int height, int width){
        grid[row][col] = -1; // mark it as visited;
        int result = 1;
        for(auto & delta : DELTA){
            int newRow = row + delta[0];
            int newCol = col + delta[1];
            if(newRow >= 0 && newRow < height && newCol >= 0 && newCol < width && grid[newRow][newCol] == 1){
            result += dfs(grid, newRow, newCol, height, width);               
            }
        }
        return result;
    }
};

const int Solution::DELTA[4][2] = {{1,0},{0,1},{-1,0},{0,-1}};
```
