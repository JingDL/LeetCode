### 1277. Count Square Submatrices with All Ones

Given a m * n matrix of ones and zeros, return how many square submatrices have all ones.

Example 1:

Input: matrix =
[
  [0,1,1,1],
  [1,1,1,1],
  [0,1,1,1]
]

Output: 15

Explanation:

There are 10 squares of side 1.

There are 4 squares of side 2.

There is  1 square of side 3.

Total number of squares = 10 + 4 + 1 = 15.

Example 2:

Input: matrix = 
[
  [1,0,1],
  [1,1,0],
  [1,1,0]
]

Output: 7

Explanation: 

There are 6 squares of side 1.  

There is 1 square of side 2. 

Total number of squares = 6 + 1 = 7.
 
Constraints:

1 <= arr.length <= 300

1 <= arr[0].length <= 300

0 <= arr[i][j] <= 1



#### My Solutions in Java
```
/*
    This question is similar to Question 221. Maximal Square (https://leetcode.com/problems/maximal-square/)
    The space complexity is O(1) since I work on the existing matrix, the time complexity is O(n*m), where n is the height, m is the width of the matrix.
*/
class Solution {
    public int countSquares(int[][] matrix) {
        int height = 0, width = 0;
        if(matrix == null || (height=matrix.length) == 0 || (width=matrix[0].length) == 0){
            return 0;
        }
        int result = 0;
        for(int row = 0; row < height; row++){
            for(int col = 0; col < width; col++){
                if(matrix[row][col] == 1 && row >= 1 && col >= 1){
                    matrix[row][col] = Math.min(Math.min(matrix[row][col-1], matrix[row-1][col]), matrix[row-1][col-1]) + 1;
                }
                result += matrix[row][col];
            }
        }
        
        // restore the matrix
        for(int row = 0; row < height; row++){
            for(int col = 0; col < width; col++){
                matrix[row][col] = matrix[row][col] > 1 ? 1 : matrix[row][col];
            }
        }
        return result;
    }
}
```


#### My Solution in C++
```
class Solution {
public:
    int countSquares(vector<vector<int>>& matrix) {
        int height = 0, width = 0;
        if((height=matrix.size()) == 0 || (width=matrix[0].size()) == 0){
            return 0;
        }
        int result = 0;
        
        for(int row = 0; row < height; row++){
            for(int col = 0; col < width; col++){
                if(matrix[row][col] == 1 && row > 0 && col > 0){
                    matrix[row][col] = min(min(matrix[row-1][col-1], matrix[row-1][col]), matrix[row][col-1]) + 1;
                };
                result += matrix[row][col];
            }
        }
        
        for(int row = 0; row < height; row++){
            for(int col = 0; col < width; col++){
                matrix[row][col] = matrix[row][col] > 1 ? 1: matrix[row][col];
            }
        }
        return result;
    }
};
```
