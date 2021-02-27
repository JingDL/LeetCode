308. Range Sum Query 2D - Mutable  

Given a 2D matrix matrix, find the sum of the elements inside the rectangle defined by its upper left corner (row1, col1) and lower right corner (row2, col2).  

Range Sum Query 2D  
The above rectangle (with the red border) is defined by (row1, col1) = (2, 1) and (row2, col2) = (4, 3), which contains sum = 8.  

Example:  
Given matrix = [
  [3, 0, 1, 4, 2],  
  [5, 6, 3, 2, 1],  
  [1, 2, 0, 1, 5],  
  [4, 1, 0, 1, 7],  
  [1, 0, 3, 0, 5]  
]  

sumRegion(2, 1, 4, 3) -> 8  
update(3, 2, 2)  
sumRegion(2, 1, 4, 3) -> 10  
  
Note:    
The matrix is only modifiable by the update function.  
You may assume the number of calls to update and sumRegion function is distributed evenly.  
You may assume that row1 ≤ row2 and col1 ≤ col2.  
  
#### My Solutions in Java  
```
/*
Inspired by https://www.geeksforgeeks.org/binary-indexed-tree-or-fenwick-tree-2/
My solution outperforms 100% of other solutions.

Time complexity of NumMatrix() is O(width*height), the time complexity of update() is O(log(width)*log(height)), the time complexity of sumRegion(int    row1, int col1, int row2, int col2) is O(log(row2)*log(col2)).  

The sapce complexity is O(width*height). 
*/
class NumMatrix {
    int[][] fenwickTree;  // assuming int does not cause overflow.
    int[][] original;
    int height;
    int width;

    public NumMatrix(int[][] matrix) {
        if(matrix == null || (height = matrix.length) == 0 || (width = matrix[0].length) == 0){
            return;
        }      
        
        original = matrix;
        
        fenwickTree = new int[height+1][width+1];
        
        // fill the fenwickTree with time complexity of O(height*width)
        for(int row = 1; row <= height; row++){
            for(int col = 1; col <= width; col++){
                fenwickTree[row][col] = matrix[row-1][col-1];
                /*System.out.println(
                        "L24 fenwickTree[" + row + "][" + col + "]=" + fenwickTree[row][col] + 
                        ", " +  (matrix[row-1][col-1]));*/
            }
        }
        
        for(int row = 1; row <= height; row++){
            for(int col = 1; col <= width; col++){
                int parent = col + (col & -col);   // col & -col give the rightmost bit that is 1.
                if(parent <= width){
                    fenwickTree[row][parent] += fenwickTree[row][col];
                    /*System.out.println(
                        "L30 fenwickTree[" + row + "][" + parent + "]=" + fenwickTree[row][parent] + 
                        ", " +  (fenwickTree[row][col]));*/
                }
            }
        }
        
        for(int col = 1; col <= width; col++){
            for(int row = 1; row <= height; row++){
                int parent = row + (row & -row);
                if(parent <= height){
                    fenwickTree[parent][col] += fenwickTree[row][col];
                }
            }
        }
    }
    
    public void update(int row, int col, int val) {
        if(fenwickTree == null || row < 0 || row >= height || col < 0 || col >= width){
            return;
        }
        
        int delta = val - original[row][col];
        original[row][col] = val;
        
        // O(log(height)*log(width))
        for(int parentRow = row + 1; parentRow <= height; parentRow += (parentRow & -parentRow)){
            for(int parentCol = col + 1; parentCol <= width; parentCol += (parentCol & -parentCol)){
                fenwickTree[parentRow][parentCol] += delta;
            }
        }    
    }
    
    public int sumRegion(int row1, int col1, int row2, int col2) {
        if(fenwickTree == null || row1 < 0 || col1 < 0 || row2 >= height || col2 >= width){
            return 0;
        }
        return sumRegion(row2, col2) - sumRegion(row2, col1-1) - sumRegion(row1-1, col2) + sumRegion(row1-1, col1-1);
    }
    
    private int sumRegion(int row, int col){
        int result = 0;
        for(int childRow = row + 1; childRow > 0; childRow -= (childRow & -childRow)){
            for(int childCol = col + 1; childCol > 0; childCol -= (childCol & -childCol)){
                result += fenwickTree[childRow][childCol];
            }
        }
        return result;
    }
}

/**
 * Your NumMatrix object will be instantiated and called as such:
 * NumMatrix obj = new NumMatrix(matrix);
 * obj.update(row,col,val);
 * int param_2 = obj.sumRegion(row1,col1,row2,col2);
 */
```
  
#### My Solution in C++
```
class NumMatrix {
public:
    NumMatrix(vector<vector<int>>& matrix) {
        height = 0;
        width = 0;
        if((height=matrix.size()) == 0 || (width=matrix[0].size()) == 0){
            height = 0;
            return;
        }
        
        fenwickTree.resize(height+1);
        for(auto & oneRow : fenwickTree){
            oneRow.resize(width+1);
        }
        
        for(int row = 1; row <= height; row++){
            for(int col = 1; col <= width; col++){
                fenwickTree[row][col] = matrix[row-1][col-1];
            }
        };
        
        for(int row = 1; row <= height; row++){
            for(int col = 1; col <= width; col++){
                int parentCol = col + (col & -col);
                if(parentCol <= width){
                    fenwickTree[row][parentCol] += fenwickTree[row][col]; 
                }
            }
        }
        
        for(int col = 1; col <= width; col++){
            for(int row = 1; row <= height; row++){
                int parentRow = row + (row & -row);
                if(parentRow <= height){
                    fenwickTree[parentRow][col] += fenwickTree[row][col];
                }
            }
        }
        
        original = &matrix;
    }
    
    void update(int row, int col, int val) {
        if(0 == height || row < 0 || row >= height || col < 0 || col >= width){
            return;
        }
        
        int delta = val - (*original)[row][col];
        (*original)[row][col] = val;
        
        for(int parentRow = row + 1; parentRow <= height; parentRow += (parentRow & -parentRow)){
            for(int parentCol = col + 1; parentCol <= width; parentCol += (parentCol & -parentCol)){
                fenwickTree[parentRow][parentCol] += delta;
            }
        }
    }
    
    int sumRegion(int row1, int col1, int row2, int col2) {
        if(0 == height || row1 < 0 || col1 < 0 || row2 >= height || col2 >= width){
            return 0;
        }
        return sumRegion(row2, col2) - sumRegion(row1-1, col2)
            - sumRegion(row2, col1-1) + sumRegion(row1-1, col1-1);         
    }
private:
    int sumRegion(int row, int col){
        int result = 0;
        for(int childRow = row + 1; childRow > 0; childRow -= (childRow & -childRow)){
            for(int childCol = col + 1; childCol > 0; childCol -= (childCol & -childCol)){
                result += fenwickTree[childRow][childCol];
            }
        }
        return result;
    }
    vector<vector<int>> fenwickTree;
    vector<vector<int>> * original;
    int height;
    int width;
};
```
