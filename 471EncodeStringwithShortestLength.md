### 471. Encode String with Shortest Length

Given a non-empty string, encode the string such that its encoded length is the shortest.

The encoding rule is: k[encoded_string], where the encoded_string inside the square brackets is being repeated exactly k times.

Note:

k will be a positive integer.
If an encoding process does not make the string shorter, then do not encode it. If there are several solutions, return any of them.
 

Example 1:

Input: s = "aaa"
Output: "aaa"
Explanation: There is no way to encode it such that it is shorter than the input string, so we do not encode it.

Example 2:

Input: s = "aaaaa"
Output: "5[a]"
Explanation: "5[a]" is shorter than "aaaaa" by 1 character.

Example 3:

Input: s = "aaaaaaaaaa"
Output: "10[a]"
Explanation: "a9[a]" or "9[a]a" are also valid solutions, both of them have the same length = 5, which is the same as "10[a]".

Example 4:

Input: s = "aabcaabcd"
Output: "2[aabc]d"
Explanation: "aabc" occurs twice, so one answer can be "2[aabc]d".

Example 5:

Input: s = "abbbabbbcabbbabbbc"
Output: "2[2[abbb]c]"
Explanation: "abbbabbbc" occurs twice, but "abbbabbbc" can also be encoded to "2[abbb]c", so one answer can be "2[2[abbb]c]".
 

Constraints:

1 <= s.length <= 150
s consists of only lowercase English letters.


#### My Solutions in Java
```
/*
Assuming the length of string s is n, the time complexity is O(n^3*m), where m is the number of factors of n.
The space complexity is O(n^3). dp[][] has n^2 strings, with the max length to be n.
*/
class Solution {
    public String encode(String s) {
        int sLength;
        if(s == null || (sLength = s.length()) <= 4 ){
            return s;
        }
        
        String dp[][] = new String[sLength][sLength];
        
        for(int len = 1; len <= sLength; len++){
            for(int start = 0, startLast = sLength - len; start <= startLast; start++){
                int end = start + len;
                String subString = s.substring(start, end);
                dp[start][end-1] = subString;
                if(len <= 4){
                    continue;
                }
                
                /*
                substring can be replaced by the concantenation of two substrings. For example, aaaaabb can be represented by 5[a] + bb.
                */
                int minCut = 0;
                int minLength = subString.length();
                for(int cut = start, cutLast = end - 1; cut < cutLast; cut++){
                    int current = dp[start][cut].length() + dp[cut+1][end-1].length();
                    if( current < minLength ){
                        minLength = current;
                        minCut = cut;
                    }
                }
                if(minLength < subString.length()){
                    dp[start][end-1] = dp[start][minCut] + dp[minCut+1][end-1];
                }
                
                // represent a strng by multiple of a short substring, e.g. aaaaaa can be replaced by 6[a]            
                for(int cut = start, cutLast = start + len/2; cut < cutLast; cut++){
                    int m = getMultipe(start, cut, len, subString);
                    if(m == 0){
                        continue;
                    }
                    String replacement = m + "[" + dp[start][cut] +"]";
                    // System.out.println("L44 " + start + " " + cut + " " + dp[start][end-1] + " " + m + " " + dp[start][cut]);
                    if(replacement.length() < dp[start][end-1].length()){
                        dp[start][end-1] = replacement;
                    }
                    /* 
                    we don't need to go further. For example, for aaaaaa, after we find 6[a], we don't need to try 3[aa], 2[aaa].  However for abcabc, we will stop after finding 2[abc].
                    */
                    break;
                }
            }
        }
        return dp[0][sLength-1];
    }
    
    private int getMultipe(int start, int cut, int len, String subString){
        int patternLength = cut - start + 1;
        if(len % (patternLength) != 0){
            return 0;
        }
        // suppose Len has m factors, we can only reach here m times.
        
        for(int i = 0, iEnd = len - patternLength; i < iEnd; i++){
            if(subString.charAt(i) != subString.charAt(i+patternLength)){
                return 0;
            }
        }
        
        return len/patternLength;
    }
}
```

#### My Solution in C++
```
class Solution {
public:
    string encode(string s) {
        int sLength = s.size();
        if(sLength < 5){
            return s;
        }
        
        vector<vector<string>> dp(sLength, vector<string>(sLength));
        
        for(int len = 1; len <= sLength; len++){
            for(int start = 0, startLast = sLength - len; start <= startLast; start++){
                int end = start + len;
                string subString = s.substr(start, len);
                dp[start][end-1] = subString;
                if(len < 5){
                    continue;
                }
                
                // replace a string with two substrings
                int sizeMin = len;
                int cutMin = start;
                for(int cut = start, cutLast = end - 1; cut < cutLast; cut++){
                    int current = dp[start][cut].size() + dp[cut+1][end-1].size();
                    if(current < sizeMin){
                        sizeMin = current;
                        cutMin = cut;
                    }
                }
                if(sizeMin < len){
                    dp[start][end-1] = dp[start][cutMin] + dp[cutMin+1][end-1];
                }
                
                // replact a string with multiple of subtring. e.g. aaaaa = 5[a]
                for(int cut = start, cutLast = start + (len >> 1); cut < cutLast; cut++){
                    int m = getMultiple(start, cut, len, subString);
                    if(!m){
                        continue;
                    }
                    
                    string replacement = to_string(m) + "[" + dp[start][cut] + "]";
                    if(replacement.size() < dp[start][end-1].size()){
                        dp[start][end-1] = replacement;
                    }
                    // we don't need to loop further, aaaaaa can be written as 6[a], 3[aa], 2[aaa], but 6[a] is the right one.
                    break;  
                }
            }
        }
        return dp[0][sLength-1];
    }
private:
    int getMultiple(int start, int cut, int len, string & subString){
        int patternLength = cut - start + 1;
        if(len % patternLength != 0){
            return 0;
        }
        
        for(int i = 0, iLast = len - patternLength; i < iLast; i++){
            if(subString[i] != subString[i+patternLength]){
                return 0;
            }
        }
        return len/patternLength;
    }
};
```
