### 72. Edit Distance

Given two words word1 and word2, find the minimum number of operations required to convert word1 to word2.

You have the following 3 operations permitted on a word:

Insert a character
Delete a character
Replace a character
Example 1:

Input: word1 = "horse", word2 = "ros"
Output: 3
Explanation: 
horse -> rorse (replace 'h' with 'r')
rorse -> rose (remove 'r')
rose -> ros (remove 'e')
Example 2:

Input: word1 = "intention", word2 = "execution"
Output: 5
Explanation: 
intention -> inention (remove 't')
inention -> enention (replace 'i' with 'e')
enention -> exention (replace 'n' with 'x')
exention -> exection (replace 'n' with 'c')
exection -> execution (insert 'u')



#### My Solutions in Java

```
/** A typical DP problem.
    time and space complexity O(n*m), where n is the length of word1, m is the length of word2
*/
class Solution {
    public int minDistance(String word1, String word2) {
        if(word1 == null || word2 == null){
            return -1;  // error
        }
        int sourceLength = word1.length();
        int destinationLength = word2.length();
        
        // +1 is for empty set
        int[][] distance = new int[destinationLength+1][sourceLength+1];
        
        for(int i = 0; i <= sourceLength; i++){
            // the number of deletion to convert word1.substring(0, i+1) into an empty string
            distance[0][i] = i;
        }
        
        for(int i = 0; i <= destinationLength; i++){
            // the number of insertion to convert an empty string into word2.substring(0, i+1)
            distance[i][0] = i;
        }
        
        for(int i = 1; i <= destinationLength; i++){
            for(int j = 1; j <= sourceLength; j++){
                if(word1.charAt(j-1) == word2.charAt(i-1)){
                    /*
                    distance[i-1][j-1] <= distance[i-1][j] + 1.  Otherwise we can convert word1.substring(0, j) into word2.substring(0, i-1), and then remove last letter.  The number of operation is lower than distance[i-1][j-1].  distance[i-1][j-1] is not the minimal.
                    For the same reason, distance[i-1][j-1] <= distance[i][j-1] + 1;
                    */
                    distance[i][j] = distance[i-1][j-1];
                }
                else{
                    /* distance[i-1][j-1] + 1 -- replace a character
                       distance[i-1][j] + 1 -- insert a character
                       distance[i][j-1] + 1 -- delete a character
                    */
                    distance[i][j] = Math.min(distance[i-1][j-1], Math.min(distance[i-1][j], distance[i][j-1])) + 1;
                }
                // System.out.println("L46 " + i + " " + j + " " + distance[i][j]);
            }
        }
        return distance[destinationLength][sourceLength];
        
    }
}
```

```
/** 
    Usually, people use a two dimension maxtrix to track the number of edit distance.  Since
    the values on (i+1)th row only depends the values on (i)th and (i+1)th rows, I am going to 
    use one array to solve it.
    time complexity is O(n*m), and space complexity is O(n), where n is the length of word1, m is the length of word2.
*/
class Solution {
    public int minDistance(String word1, String word2) {
        if(word1 == null || word2 == null){
            return -1;  // error
        }
        int sourceLength = word1.length();
        int destinationLength = word2.length();
        
        int[] dp = new int[sourceLength+1];
        
        for(int i = 0; i <= sourceLength; i++){
            // the number of deletion to convert word1.substring(0, i+1) into an empty string
            dp[i] = i;
        }
        
        // previous1 is the old value of dp[j-1], previous2 is the old value of dp[j] 
        for(int i = 1; i <= destinationLength; i++){
            int previous1 = dp[0];
            for(int j = 1; j <= sourceLength; j++){
                int previous2 = dp[j];
                if(word1.charAt(j-1) == word2.charAt(i-1)){
                    dp[j] = previous1;
                }
                else{
                    dp[j] = Math.min(previous1, Math.min(previous2, dp[j-1])) + 1;
                }
                previous1 = previous2;
            }
            // we need to update dp[0]
            dp[0] = i;
        }
        return dp[sourceLength];
        
    }
}
```



#### My Solution in C++
```
/* time and space complexity O(n*m), where n is the length of word1, m is the length of word2
   see the explanation in the above Java solution 
*/
class Solution {
public:
    int minDistance(string & word1, string & word2) {
        int length1 = word1.length();
        int length2 = word2.length();
        
        vector<vector<int>> dp(length2+1, vector<int>(length1+1, 0));
        
        for(int col = 0; col <= length1; col++){
            dp[0][col] = col;
        }
        
        for(int row = 0; row <= length2; row++){
            dp[row][0] = row;
        }
        
        for(int row = 1; row <= length2; row++){
            for(int col = 1; col <= length1; col++){
                if(word1[col-1] == word2[row-1]){
                    dp[row][col] = dp[row-1][col-1];
                }
                else{
                    dp[row][col] = min(dp[row-1][col-1], min(dp[row-1][col], dp[row][col-1]))+1;
                }
            }
        }
        return dp[length2][length1];
    }
};
```

```
/** 
    Usually, people use a two dimension maxtrix to track the number of edit distance.  Since
    the values on (i+1)th row only depends the values on (i)th and (i+1)th rows, I am going to 
    use one array to solve it.
    time complexity is O(n*m), and space complexity is O(n), where n is the length of word1, m is the length of word2.
*/
class Solution {
public:
    int minDistance(string & word1, string & word2) {
        int length1 = word1.length();
        int length2 = word2.length();
        
        vector<int> dp(length1+1, 0);
        
        for(int col = 0; col <= length1; col++){
            dp[col] = col;
        }
        
        // previous1 is the old value of dp[col-1], previous2 is the old value of dp[col]
        for(int row = 1; row <= length2; row++){
            int previous1 = dp[0];
            for(int col = 1; col <= length1; col++){
                int previous2 = dp[col];
                if(word1[col-1] == word2[row-1]){
                    dp[col] = previous1;
                }
                else{
                    dp[col] = min(previous1, min(previous2, dp[col-1]))+1;
                }
                previous1 = previous2;
            }
            dp[0] = row;
        }
        return dp[length1];
    }
};
```
