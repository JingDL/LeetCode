### 844. Backspace String Compare

Given two strings S and T, return if they are equal when both are typed into empty text editors. # means a backspace character.

Note that after backspacing an empty text, the text will continue empty.

Example 1:
Input: S = "ab#c", T = "ad#c"
Output: true
Explanation: Both S and T become "ac".

Example 2:
Input: S = "ab##", T = "c#d#"
Output: true
Explanation: Both S and T become "".

Example 3:
Input: S = "a##c", T = "#a#c"
Output: true
Explanation: Both S and T become "c".

Example 4:
Input: S = "a#c", T = "b"
Output: false
Explanation: S becomes "c" while T becomes "b".
Note:

1 <= S.length <= 200
1 <= T.length <= 200
S and T only contain lowercase letters and '#' characters.

Follow up:

Can you solve it in O(N) time and O(1) space?

#### My Solution in C++
```
/**
    If we start to scan a string from left to right, only whwn we reach the right end, we know how the string 
    looks like.  Also we need a stack to store the characters we went through, which make the space 
    complexity O(n).
    
    However, if we start to scan the string from right to left, we don't need a stack, which make the space 
    complexity O(1), and we can stop in the middle if we find two chars in two strings do not match.  For 
    example, we can stop earlier for strings like abcdefgab# and abcdefgbdv##.
    
    time complexity O(n), space complexity O(1)
*/
class Solution {
public:
    bool backspaceCompare(string & S, string & T) {
        int sI = S.length() - 1;  // move from the right to the left
        int tI = T.length() - 1;
        while(sI >= 0 || tI >= 0){
            int backspaceCount = 0;
            while(sI >= 0 && ('#' == S[sI] || backspaceCount > 0)){
                backspaceCount += '#' == S[sI] ? 1 : -1;
                sI--;
            }
            backspaceCount = 0;
            while(tI >= 0 && ('#' == T[tI] || backspaceCount > 0)){
                backspaceCount += '#' == T[tI] ? 1 : -1;
                tI--;
            }
            if(sI >= 0 && tI >= 0 && S[sI] == T[tI]){
                sI--;
                tI--;
            }
            else{
                break;
            }
        }
        return -1 == sI && -1 == tI;
    }
};
```

#### My Solution in Java
```
/**
    If we start to scan a string from left to right, only whwn we reach the right end, we know how the string 
    looks like.  Also we need a stack to store the characters we went through, which make the space 
    complexity O(n).
    
    However, if we start to scan the string from right to left, we don't need a stack, which make the space 
    complexity O(1), and we can stop in the middle if we find two chars in two strings do not match.  For 
    example, we can stop earlier for strings like abcdefgab# and abcdefgbdv##.
    
    time complexity O(n), space complexity O(1)
*/
class Solution {
    public boolean backspaceCompare(String S, String T) {
        if(S == T){
            return true;
        }
        
        if(null == S || null == T){
            return false;
        }
        
        int sI = S.length() - 1;  // move from the right to the left
        int tI = T.length() - 1;
        while(sI >= 0 || tI >= 0){
            int backspaceCount = 0;
            while(sI >= 0 && ('#' == S.charAt(sI) || backspaceCount > 0)){
                backspaceCount += '#' == S.charAt(sI) ? 1 : -1;
                sI--;
            } // this loop will stop on a 'valid' character
            while(tI >= 0 && ('#' == T.charAt(tI) || backspaceCount > 0)){
                backspaceCount += '#' == T.charAt(tI) ? 1 : -1;
                tI--;
            }
            // it is the time to compare two "valid" characters on two Strings
            if(sI >= 0 && tI >= 0 && S.charAt(sI) == T.charAt(tI)){
                sI--;
                tI--;
            }
            else{
                break;
            }
        }
        // if both sI and tI stop on the right place
        return -1 == sI && -1 == tI;
    }
}
```
```
