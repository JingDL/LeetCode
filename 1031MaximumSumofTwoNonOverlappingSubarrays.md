### 1031. Maximum Sum of Two Non-Overlapping Subarrays

Given an array A of non-negative integers, return the maximum sum of elements in two non-overlapping (contiguous) subarrays, which have lengths L and M.  (For 
clarification, the L-length subarray could occur before or after the M-length subarray.)

Formally, return the largest V for which V = (A[i] + A[i+1] + ... + A[i+L-1]) + (A[j] + A[j+1] + ... + A[j+M-1]) and either:

0 <= i < i + L - 1 < j < j + M - 1 < A.length, or
0 <= j < j + M - 1 < i < i + L - 1 < A.length.
 

Example 1:

Input: A = [0,6,5,2,2,5,1,9,4], L = 1, M = 2
Output: 20
Explanation: One choice of subarrays is [9] with length 1, and [6,5] with length 2.

Example 2:
Input: A = [3,8,1,3,2,1,8,9,0], L = 3, M = 2
Output: 29
Explanation: One choice of subarrays is [3,8,1] with length 3, and [8,9] with length 2.

Example 3:
Input: A = [2,1,5,6,0,9,5,0,3,8], L = 4, M = 3
Output: 31
Explanation: One choice of subarrays is [5,6,0,9] with length 4, and [3,8] with length 3.
 
Note:

L >= 1,
M >= 1,
L + M <= A.length <= 1000,
0 <= A[i] <= 1000




#### My Solution in C++
```
/**
    Since L-length subarray could occur on the left or the right, we create a function 
    maxSumTwoNoOverlapFixedOrder() to calculate the sum when one array is alway on the left of
    the other.  Then we can the function twice with different order of input:
    maxSumTwoNoOverlapFixedOrder(A, L, M) and
    maxSumTwoNoOverlapFixedOrder(A, M, L).
    
    When scan through array, we use leftSum to store the sum of current left subarray, and 
    rightSum to store the sum of current right subarray.  LeftSumMax is the max value of all
    the subarrays.
    leftSumMax = max(leftSumMax, leftSum);
    result = max(result, leftSumMax + rightSum);
    
    time complexity O(n), space compexity O(1)
  */
class Solution {
public:
    int maxSumTwoNoOverlap(vector<int>& A, int L, int M) {
        if(A.size() < L + M){
            return 0;
        }
        return max(maxSumTwoNoOverlapFixedOrder(A, L, M),
                   maxSumTwoNoOverlapFixedOrder(A, M, L));  // reverse the order of subarrays.
    }
private:
    int maxSumTwoNoOverlapFixedOrder(vector<int>& A, int leftLength, int rightLength) {
        int leftSum = 0, rightSum = 0;
        int i = 0;
        while(i < leftLength){
            leftSum += A[i];
            i++;
        }
        int totalLength = leftLength + rightLength;
        while(i < totalLength){
            rightSum += A[i];
            i++;
        }
        int result = leftSum + rightSum;
        
        int ASize = A.size();
        int leftSumMax = leftSum;
        while(i < ASize){
            leftSum += A[i-rightLength] - A[i-totalLength];
            rightSum += A[i] - A[i-rightLength];
            leftSumMax = max(leftSumMax, leftSum);
            result = max(result, leftSumMax + rightSum);
            i++;
        }
        return result;
    }
};
```



#### My Solution in Java
```
/*  
    Since L-length subarray could occur on the left or the right, we create a function 
    maxSumTwoNoOverlapFixedOrder() to calculate the sum when one array is alway on the left of
    the other.  Then we can the function twic with different order of input:
    maxSumTwoNoOverlapFixedOrder(A, L, M) and
    maxSumTwoNoOverlapFixedOrder(A, M, L).
    
    When scan through array, we use leftSum to store the sum of current left subarray, and 
    rightSum to store the sum of currecnt right subarray.  LeftSumMax is the max value of all
    the subarrays.
    leftSumMax = Math.max(leftSumMax, leftSum);
    result = Math.max(result, leftSumMax + rightSum);
    
    time complexity O(n), space complexity O(1), n is the length of A
*/
class Solution {
    private int maxSumTwoNoOverlapFixedOrder(int[] A, int leftLength, int rightLength) {
        int leftSum = 0, rightSum = 0;
        int i = 0;
        while(i < leftLength){
            leftSum += A[i];
            i++;
        }
        int totalLength = leftLength + rightLength;
        while(i < totalLength){
            rightSum += A[i];
            i++;
        }
        int result = leftSum + rightSum;
        
        int leftSumMax = leftSum;
        while(i < A.length){
            leftSum += A[i-rightLength] - A[i-totalLength];
            rightSum += A[i] - A[i-rightLength];
            leftSumMax = Math.max(leftSumMax, leftSum);
            result = Math.max(result, leftSumMax + rightSum);
            i++;
        }
        return result;
    }
    
    public int maxSumTwoNoOverlap(int[] A, int L, int M) {
        if(A == null || A.length < L + M){
            return 0;
        }
        return Math.max( maxSumTwoNoOverlapFixedOrder(A, L, M),
            maxSumTwoNoOverlapFixedOrder(A, M, L) );
    }
}
```
