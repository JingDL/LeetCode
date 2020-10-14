### 777. Swap Adjacent in LR String

In a string composed of 'L', 'R', and 'X' characters, like "RXXLRXRXL", a move consists of either replacing one occurrence of "XL" with "LX", or replacing one 
occurrence of "RX" with "XR". Given the starting string start and the ending string end, return True if and only if there exists a sequence of moves to transform 
one string to the other.

Example 1:

Input: start = "RXXLRXRXL", end = "XRLXXRRLX"
Output: true
Explanation: We can transform start to end following these steps:
RXXLRXRXL ->
XRXLRXRXL ->
XRLXRXRXL ->
XRLXXRRXL ->
XRLXXRRLX

Example 2:

Input: start = "X", end = "L"
Output: false

Example 3:

Input: start = "LLR", end = "RRL"
Output: false

Example 4:

Input: start = "XL", end = "LX"
Output: true

Example 5:

Input: start = "XLLR", end = "LXLX"
Output: false
 

Constraints:

1 <= start.length <= 104
start.length == end.length
Both start and end will only consist of characters in 'L', 'R', and 'X'.

#### My Solutions in Java
```
/**
    We can observe that a successful transition must satisfy three conditions:
    1) If we strip off all the “X”, the start should be the same as the end, because a “R” can not pass a “L”, and a “L” can not pass a “X”.  For example, we
    cannot convert “RXLX” into “LXRX”
    2) The index of a “L” in start string should be greater than or equal to the index of the corresponding “L” in the end.  We cannot convert “LXRX” into “XLRX”
    although they satisfy condition 1).
    3) The index of a "R" in start should be less than or equal to the index of the corresponding "R" in the end.  We cannot convert “XLXR” into “XLRX” although
    they satisfy condition 1).
    
    The time complexity is O(n), where n is the length of the string.
    The space complexity is O(0).
*/
class Solution {
    public boolean canTransform(String start, String end) {
        // if two string both are null, or are the same string
        if(start == end){
            return true;
        }
        
        
        if(start == null || end == null || start.length() != end.length()){
            return false;
        }

        int sIndex = 0, eIndex = 0;
        int length = start.length();
        while(true){
            while(sIndex < length && start.charAt(sIndex) == 'X'){
                sIndex++;
            }
            
            while(eIndex < length && end.charAt(eIndex) == 'X'){
                eIndex++;
            }
            
            if(sIndex == length && eIndex == length){
                return true;
            }
            
            // if only one index reaches the end of the string
            if(sIndex == length || eIndex == length){
                return false;
            }
            
            if(start.charAt(sIndex) != end.charAt(eIndex)){
                return false; // violate condition 1
            }
            
            char curr = start.charAt(sIndex);
            if(curr != 'L' && curr != 'R'){
                return false;  // wrong character
            }
            
            if(curr == 'L' && sIndex < eIndex){
                return false; // violate condition 2;
            }
            
            if(curr == 'R' && sIndex > eIndex){
                return false; // violate condition 3;
            }
            sIndex++;
            eIndex++;
        }
    }
}
```

#### My Solution in C++
```
/**
    The time complexity is O(n), where n is the length of the string.
    The space complexity is O(0).
*/
class Solution {
public:
    bool canTransform(string & start, string & end) {
        if(start == end){
            return true;
        }
        
        if(start.length() != end.length()){
            return false;
        }
        
        int length = start.length();
        int sIndex = 0, eIndex = 0;
        while(true){
            while(sIndex < length && start[sIndex] == 'X'){
                sIndex++;
            }
            
            while(eIndex < length && end[eIndex] == 'X'){
                eIndex++;
            }
            
            if(sIndex == length && eIndex == length){
                return true;
            }
            
            if(sIndex == length || eIndex == length){
                return false;  // if only one reach the end
            }
            
            if(start[sIndex] != end[eIndex]){
                return false;
            }
            
            char ch = start[sIndex];
            if(ch == 'L' && sIndex < eIndex){
                return false;
            }
            
            if(ch == 'R' && sIndex > eIndex){
                return false;
            }
            sIndex++;
            eIndex++;
        }
    }
};
```
