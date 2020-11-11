### 1240. Tiling a Rectangle with the Fewest Squares

Given a rectangle of size n x m, find the minimum number of integer-sided squares that tile the rectangle.

 

Example 1:


![Image](https://assets.leetcode.com/uploads/2019/10/17/sample_11_1592.png)

Input: n = 2, m = 3
Output: 3

Explanation: 3 squares are necessary to cover the rectangle.
2 (squares of 1x1)
1 (square of 2x2)

Example 2:


![Image](https://assets.leetcode.com/uploads/2019/10/17/sample_22_1592.png)

Input: n = 5, m = 8
Output: 5

Example 3:


![Image](https://assets.leetcode.com/uploads/2019/10/17/sample_33_1592.png)

Input: n = 11, m = 13
Output: 6
 

Constraints:

1 <= n <= 13
1 <= m <= 13

#### My Solutions in Java
```
/*
     Intuitively, we may think using Greedy Algorithm.  We can take squares as large as possible, so the remaining area is as smaller as possible.
     
    However, Greedy Algorithm does not work for this question.  For example, for a 10*12 rectangle, Greedy Algorithm gives us one 10*10 square and 5 2*2 squares, 
    totally 6 squares.  But the right answer should be 2 6*6 squares and 3 4*4 squares, totally 5 squares.
    
    Inspired by others' solutions , especially for converting a partially filled rectangle into a single integer, which can be used as a HashMap key.
    
    I use a HashMap stepsToReach to short-circuit the algorithm, which largely improves the performance.  For the time complexity analysis, it is difficult to 
    calculate the impact of the HashMap.  There are totally n*m 1*1 squares, so the recursion depth could be n*m although we never reach n*m due to the HashMap.  
    For each layer of the recursion, we can have n (n<m) different ways to get a square.  The time complexity should be n^(n*m).  This is a very rough estimation 
    about the upper bound.
    
    The space complexity is O(n*m), which is the depth of the recursion.
*/
class Solution {
    int result;
    Map<Long, Integer> stepsToReach;
    public int tilingRectangle(int n, int m) {
        if(n == m){
            return 1;
        }
        result = Integer.MAX_VALUE;
        stepsToReach = new HashMap<>();
        
        if( n < m){
            backtracking(n, m, new int[n], 0);
        }
        else{
            backtracking(m, n, new int[m], 0);
        }
        return result;
    }
    
    private void backtracking(final int n, final int m, int[] height, int step){
        // It is not better than the answer we already have
        if(step >= result){
            return;
        }
        
        int minHeight = m;
        int start = 0;
        for(int i = 0; i < n; i++){
            if( height[i] < minHeight ){
                minHeight = height[i];
                start = i;
            } 
        }
        if(minHeight == m){
            result = step;
            // System.out.println("L41 -- " + step);
            return;  // the rectangle is already tiled
        }
        
        long key = convertToKey(n, m, height);
        int savedStep = stepsToReach.getOrDefault(key, Integer.MAX_VALUE);
        if(savedStep <= step){
            return; // can not find a better answer going further
        }
        stepsToReach.put(key, step);
        
        int end = start;
        // end+1 is included in the square.  Suppose m - minHeight = 3, start = 0, end should be 2
        while(end + 1 < n && height[end+1] == minHeight && end + 1 - start < m - minHeight){
            end++;
        }
        //System.out.println("L56 " + step + " " + start + " " + end + " " + minHeight);
        
        for(int i = end; i >= start; i--){
            int additionalHeight = i + 1 - start;
            for(int j = start; j <= i; j++){
                height[j] = minHeight + additionalHeight;
            }
            backtracking(n, m, height, step+1);
            
            // restore to the original value, so we can process next iteration;
            height[i] = minHeight;
            // The above statement is better than following code, because height[0...i-1] will be overwritten on line 62.
            /*for(int j = start; j <= i; j++){
                height[j] = minHeight;
            }*/
        }
    }
    
    // every rectangle state maps to an unique key
    private long convertToKey(int n, int m, int[] height){
        long key = 0;
        int multiplier = 1;  
        for(int i = 0; i < n; i++){
            key += height[i] * multiplier;
            multiplier += m + 1;  // m+1 instead of m, because height is in [1, m]
        }
        return key;
    }
}
```

#### My Solution in C++
```
/* please refer my Java solution for complexity analysis*/
class Solution {
public:
    int tilingRectangle(int n, int m) {
        if(n == m){
            return 1;
        }
        
        result = INT_MAX;
        unordered_map<long, int> stepsToReach;
        if(n > m){
            vector<int> height(m, 0);
            backTracking(m, n, height, stepsToReach, 0);
        }
        else{
            vector<int> height(n, 0);
            backTracking(n, m, height, stepsToReach, 0);
        }
        return result;
    }
    
private:
    int result;
    
    
    void backTracking(int n, int m, vector<int> & height, unordered_map<long, int> & stepsToReach, int step){
        if(step > result){
            return;
        }
        
        int minHeight = INT_MAX;
        int start = -1;
        for(int i = 0; i < n; i++){
            if(minHeight > height[i]){
                minHeight = height[i];
                start = i;
            }
        }
        
        if(minHeight == m){  // The entire area is tiled
            result = step;
            return;
        }
        
        long key = convertToKey(n, m, height);
        if(stepsToReach.find(key) != stepsToReach.end()){
            if(stepsToReach[key] <= step){
                return;
            }
        }
        stepsToReach[key] = step;
        
        int end = start;
        while(end+1 < n && height[end+1] == minHeight && end + 1 - start < m - minHeight){
            end++;
        }
        
        // we should try large squares first, so i descrease from end to start.
        for(int i = end; i >= start; i--){
            int additionalHeight = i + 1 - start;
            for(int j = start; j <= i; j++){
                height[j] = minHeight + additionalHeight;
            }
            backTracking(n, m, height, stepsToReach, step+1);
            //restore height.  We only need to change back height[i], since height[start...i-1] will be overwritten in the next iteration.
            height[i] = minHeight;   
        }
    }
    
    long convertToKey(int n, int m, vector<int> & height){
        long multiplier = 1;
        long key = 0;
        for(int i = 0; i < n; i++){
            key += (height[i] == 0 ? 0 : height[i] * multiplier);
            multiplier *= (m+1);
        }
        return key;
    }
};
```
