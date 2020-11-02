### 1477. Find Two Non-overlapping Sub-arrays Each With Target Sum

Given an array of integers arr and an integer target.

You have to find two non-overlapping sub-arrays of arr each with sum equal target. There can be multiple answers so you have to find an answer where the sum of 
the lengths of the two sub-arrays is minimum.

Return the minimum sum of the lengths of the two required sub-arrays, or return -1 if you cannot find such two sub-arrays.

 

Example 1:

Input: arr = [3,2,2,4,3], target = 3
Output: 2
Explanation: Only two sub-arrays have sum = 3 ([3] and [3]). The sum of their lengths is 2.

Example 2:

Input: arr = [7,3,4,7], target = 7
Output: 2
Explanation: Although we have three non-overlapping sub-arrays of sum = 7 ([7], [3,4] and [7]), but we will choose the first and third sub-arrays as the sum of their lengths is 2.

Example 3:

Input: arr = [4,3,2,6,2,3,4], target = 6
Output: -1
Explanation: We have only one sub-array of sum = 6.

Example 4:

Input: arr = [5,5,4,4,5], target = 3
Output: -1
Explanation: We cannot find a sub-array of sum = 3.

Example 5:

Input: arr = [3,1,1,1,5,1,2,1], target = 3
Output: 3
Explanation: Note that sub-arrays [1,2] and [2,1] cannot be an answer because they overlap.
 

Constraints:

1 <= arr.length <= 10^5
1 <= arr[i] <= 1000
1 <= target <= 10^8


#### My Solutions in Java
```
/*
    Because of constraint 1 <= arr[i] <= 1000, we can use a sliding window instead of a hashmap.  When the sum in the sliding window < target, we move the right 
    side of the window to increase the sum.  When the sum > target, we move the left side to decrease the sum.  A sliding window uses O(0) memory, while a hashmap 
    use O(n) memory, where n is the size of arr.
    If arr[i] < 0, sliding window does not work, because we cannot always increase/decrease the sum by moving the right/left side of the sliding window.
    Time complexity is O(n).  The space complexity is O(n), because we need array leftLength to record the shortest length on the left.
    
    We can develop an algorithm, which only uses O(0) space. But we need loop through arr from left to right, then from right to left.  For example, arr = 
    [2,1,3,3,2,3,1], target = 6.  If we loop from left to right, we get result 6 ([2, 1, 3] and [2, 3, 1]).  If we loop from right to left, we get the right result 
    5 ([2, 3, 1] and [3, 3]).  This means we can save space by doubling the execution time.  
    Usually, we care more about performance(time) than memory, so I am not going to develop that algorithm here.
*/
class Solution {
    public int minSumOfLengths(int[] arr, int target) {
        int arrSize;
        if(arr == null || (arrSize = arr.length) < 2 ){
            return -1;
        }
        
        int leftLength[] = new int[arrSize];  // the shortest length on the left
        Arrays.fill(leftLength, Integer.MAX_VALUE);
            
        int result = Integer.MAX_VALUE;
        int start = 0;  // the left side of the sliding window
        int sum = 0;  // sum of the sliding window
        int shortest = Integer.MAX_VALUE;
        for(int end = 0; end < arrSize; end++){ // end is the right side of the sliding window
            sum += arr[end];
            while(sum > target){
                sum -= arr[start];
                start++;
            }
            
            if(sum != target){
                leftLength[end] = shortest;
                continue;
            }
            
            int currentLength = end - start + 1;
            if(start > 1 && leftLength[start-1] != Integer.MAX_VALUE){
                result = Math.min(result, leftLength[start-1] + currentLength);
            }
            
            shortest = Math.min(shortest, currentLength);
            leftLength[end] = shortest;
            
            sum -= arr[start];
            start++;
        }
        return result == Integer.MAX_VALUE ? -1 : result;
    }
}
```


#### My Solution in C++
```
// Time complexity and space complexity both are O(n), where n is the length of arr
class Solution {
public:
    int minSumOfLengths(vector<int>& arr, int target) {
        int size = arr.size();
        if(size < 2){
            return -1;
        }
        
        vector<int> shortestLeft(size, INT_MAX);  // shortest lwngth before current window
        int shortestSofar = INT_MAX;
        int sum = 0; // the sum of items in the sliding window
        int result = INT_MAX;
        for(int start = 0, end = 0; end < size; end++){  // start -- the left side of the sliding window.  end -- the right side of the sliding window
            sum += arr[end];
            while(sum > target){
                sum -= arr[start];
                start++;
            }
            
            if(sum != target){
                shortestLeft[end] = shortestSofar;
                continue;
            }
            
            int windowLength = end - start + 1;
            if(start > 0 && shortestLeft[start-1] != INT_MAX){
                result = min(result, shortestLeft[start-1] + windowLength);
            }
            
            shortestSofar = min(shortestSofar, windowLength);
            shortestLeft[end] = shortestSofar;
            
            sum -= arr[start];
            start++;
        }
        return result != INT_MAX ? result : -1;
    }
};
```
