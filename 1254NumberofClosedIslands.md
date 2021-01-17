### 1254. Number of Closed Islands

Given a 2D grid consists of 0s (land) and 1s (water).  An island is a maximal 4-directionally connected group of 0s and a closed island is an island totally (all left, top, right, bottom) surrounded by 1s.

Return the number of closed islands.

 

Example 1:

![Image](https://assets.leetcode.com/uploads/2019/10/31/sample_3_1610.png)

Input: grid = [[1,1,1,1,1,1,1,0],[1,0,0,0,0,1,1,0],[1,0,1,0,1,1,1,0],[1,0,0,0,0,1,0,1],[1,1,1,1,1,1,1,0]]

Output: 2

Explanation: 

Islands in gray are closed because they are completely surrounded by water (group of 1s).


Example 2:

![Image](https://assets.leetcode.com/uploads/2019/10/31/sample_4_1610.png)

Input: grid = [[0,0,1,0,0],[0,1,0,1,0],[0,1,1,1,0]]

Output: 1


Example 3:

Input: grid = [[1,1,1,1,1,1,1],
               [1,0,0,0,0,0,1],
               [1,0,1,1,1,0,1],
               [1,0,1,0,1,0,1],
               [1,0,1,1,1,0,1],
               [1,0,0,0,0,0,1],
               [1,1,1,1,1,1,1]]

Output: 2
 

Constraints:

1 <= grid.length, grid[0].length <= 100

0 <= grid[i][j] <=1


#### My Solutions in Java
```
/*
    Since the values in grid are either 0 or 1, we can use it to record if we visit it.  This helps us save some memory space.  When we visit a cell in the grid, 
we change it to grid[row][cold] & 0x02.  At the end, we can restore the grid by calling
        grid[row][col] &= 0x01;
    
    In the dfs(), when we detect a cell with value 0 at the edge, we return false.  An island touching the edge will be an invalid closed island.  The closed
Island will get the number of closed islands.
    
    The time complexity is O(n*m), the space complexity is also O(n*m), because the depth of the recursive calls of dfs() can be O(n*m) for the worst case
scenario.  n is the height, and m is the width of the grid.
*/
class Solution {
    private final int[][] DELTA = {{0,1},{0,-1},{1,0},{-1,0}};
    private final int VISITED = 0x02;
    
    private boolean dfs(int[][] grid, int row, int col){
        // if grid[row][col] != 0, it is either 1 (water) or 2 (VISITED)
        if(row < 0 || row >= grid.length || col < 0 || col >= grid[0].length || 0 != grid[row][col] ){
            return true;
        }
        
        boolean result = true;
        if(row == 0 || row == grid.length - 1 || col == 0 || col == grid[0].length - 1){  // grid[row][col] = 0;
            result = false;
        }
        
        grid[row][col] |= VISITED;  // mark it as visited
        
        // we need to visit the nearby cell even if result is false;
        for(int i = 0; i < 4; i++){
            boolean returnedValue = dfs(grid, row + DELTA[i][0], col + DELTA[i][1]);
            result = result && returnedValue;  // result = result && dfs() won't work due to short circuit logic
        }
        
        return result;
    }
    
    public int closedIsland(int[][] grid) {
        int height = 0, width = 0;
        if(grid == null || (height=grid.length) == 0 || (width=grid[0].length) == 0){
            return 0;
        }
        
        int result = 0;
        for(int row = 1, rowEnd = height - 1; row < rowEnd; row++){
            for(int col = 1, colEnd = width - 1; col < colEnd; col++){
                if(grid[row][col] == 0){
                    result += dfs(grid, row, col)? 1: 0;
                }
            }
        }
        
        // restore the grid
        for(int row = 0; row < height; row++){
            for(int col = 0; col < width; col++){
                    grid[row][col] &= 0x01;
            }
        }
        
        return result;
    }
}
```

#### My Solution in C++
```
/*
    The time and space complexity both are O(n*m)
*/
#define VISITED (0x02)
class Solution {
public:
    int closedIsland(vector<vector<int>>& grid) {
        int height = 0;
        int width = 0;
        if(0 == (height = grid.size()) || 0 == (width = grid[0].size())){
            return 0;
        }
        
        int result = 0;
        for(int row = 1, rowEnd = height - 1; row < rowEnd; row++){
            for(int col = 1, colEnd = width - 1; col < colEnd; col++){
               if(0 == grid[row][col]){
                   result += dfs(grid, row, col, height, width) ? 1 : 0;
               } 
            }
        }
        
        // restore grid
        for(int row = 0; row < height; row++){
            for(int col = 0; col < width; col++){
                grid[row][col] &= 0x01;
            }
        }
        
        return result;
    }
private:
    static const int DELTA[4][2];
    bool dfs(vector<vector<int>>& grid, int row, int col, int height, int width){
        // grid[row][col] != 0 --> it is either 1 (water) or 2 (VISITED)
        if(row < 0 || row >= height || col < 0 || col >= width || grid[row][col] != 0){
            return true;   
        }
        
        bool result = true;
        if(0 == row || height - 1 == row || 0 == col || width - 1 == col){
            result = false;
        }
        
        grid[row][col] |= VISITED;
        
        for(int i = 0; i < 4; i++){
            bool returnedValue = dfs(grid, row + DELTA[i][0], col + DELTA[i][1], height, width);
            result =  returnedValue && result;    
        }
        return result;
    }
};

const int Solution::DELTA[4][2] = {{0,1},{0,-1},{1,0},{-1,0}};
```
