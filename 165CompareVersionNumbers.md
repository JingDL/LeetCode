### 165. Compare Version Numbers

Given two version numbers, version1 and version2, compare them.

Version numbers consist of one or more revisions joined by a dot '.'. Each revision consists of digits and may contain leading zeros. Every revision contains at least one character. Revisions are 0-indexed from left to right, with the leftmost revision being revision 0, the next revision being revision 1, and so on. For example 2.5.33 and 0.1 are valid version numbers.

To compare version numbers, compare their revisions in left-to-right order. Revisions are compared using their integer value ignoring any leading zeros. This means that revisions 1 and 001 are considered equal. If a version number does not specify a revision at an index, then treat the revision as 0. For example, version 1.0 is less than version 1.1 because their revision 0s are the same, but their revision 1s are 0 and 1 respectively, and 0 < 1.

Return the following:

If version1 < version2, return -1.

If version1 > version2, return 1.

Otherwise, return 0.
 

Example 1:

Input: version1 = "1.01", version2 = "1.001"

Output: 0

Explanation: Ignoring leading zeroes, both "01" and "001" represent the same integer "1".

Example 2:

Input: version1 = "1.0", version2 = "1.0.0"

Output: 0

Explanation: version1 does not specify revision 2, which means it is treated as "0".

Example 3:

Input: version1 = "0.1", version2 = "1.1"

Output: -1

Explanation: version1's revision 0 is "0", while version2's revision 0 is "1". 0 < 1, so version1 < version2.

Example 4:

Input: version1 = "1.0.1", version2 = "1"

Output: 1

Example 5:

Input: version1 = "7.5.2.4", version2 = "7.5.3"

Output: -1
 

Constraints:

1 <= version1.length, version2.length <= 500

version1 and version2 only contain digits and '.'.

version1 and version2 are valid version numbers.

All the given revisions in version1 and version2 can be stored in a 32-bit integer.


#### My Solutions in Java
```
/*
    This algorithm beats 100.00% of other submissions, because it works directly on the raw data.  It does not rely on generic functions such as String.split() and Integer.parseInt(), which use more memory or more time.  
    Even if the revisions in version1 or version2 is not an integer or long, this algorithm still works.  For example, it works for "123456789123456789123456789.1"
    
    Time complexity is O(n), n the max length of version1 and version2.
    Space complexity is O(0).
*/
class Solution {
    static final char DOT = '.';
    public int compareVersion(String version1, String version2) {
        int size1 = version1.length();
        int size2 = version2.length();
        int start1 = 0;
        int end1 = 0;
        int start2 = 0;
        int end2 = 0;
        
        while(end1 < size1 && end2 < size2){
            while(end1 < size1 && version1.charAt(end1) != DOT){
                end1++;
            }
            
            // find start1 right after finding out end1.  It potentially helps us to avoid cache misses by staying with the same string. 
            while(start1 < end1 && version1.charAt(start1) == '0'){
                start1++;
            }
            
            while(end2 < size2 && version2.charAt(end2) != DOT){
                end2++;
            }
            
            while(start2 < end2 && version2.charAt(start2) == '0'){
                start2++;
            }
            
            int subStringSize1 = end1 - start1;
            int subStringSize2 = end2 - start2;
            if(subStringSize1 != subStringSize2){
                return subStringSize1 > subStringSize2? 1 : -1;
            }
            
            while(start1 < end1){
                char char1 = version1.charAt(start1);
                char char2 = version2.charAt(start2);
                if(char1 != char2){
                    return char1 > char2? 1 : -1; 
                }
                start1++;
                start2++;
            }
            
            end1++;
            end2++;
            start1 = end1;
            start2 = end2;
        }
        
        if(end1 == size1 && end2 == size2){
            return 0;
        }
        // we should use end1-size1<0 instead of end1-size1!=0 here.  Because end1 could be greater than size1.  For example version1 = "1", version2 = "1.1"
        if(end1 - size1 < 0){
            for(int i = end1; end1 < size1; end1++){
                char c = version1.charAt(end1);
                if(c != '0' && c != DOT){
                    return 1;
                }
            }
            return 0;
        }
        
        for(int i = end2; end2 < size2; end2++){
            char c = version2.charAt(end2);
            if(c != '0' && c != DOT){
                return -1;
            }
        }
        return 0;
    }
}
```

#### My Solution in C++
```
// time complexity O(n), space complexity O(0)
class Solution {
public:
    int compareVersion(string version1, string version2) {
        int size1 = version1.size();
        int size2 = version2.size();
        int start1 = 0;
        int start2 = 0;
        int end1 = 0;
        int end2 = 0;
        
        const char DOT = '.';
        while(end1 < size1 && end2 < size2){
            while(end1 < size1 && version1[end1] != DOT){
                end1++;
            }
            
            while(start1 < end1 && version1[start1] == '0'){
                start1++;
            }
            
            while(end2 < size2 && version2[end2] != DOT){
                end2++;
            }
            
            while(start2 < end2 && version2[start2] == '0'){
                start2++;
            }
            
            int subStringSize1 = end1 - start1;
            int subStringSize2 = end2 - start2;
            if(subStringSize1 != subStringSize2){
                return subStringSize1 > subStringSize2 ? 1 : -1;
            }
            
            while(start1 < end1){
                char ch1 = version1[start1];
                char ch2 = version2[start2];
                if(ch1 != ch2){
                    return ch1 < ch2 ? -1 : 1;
                }
                start1++;
                start2++;
            }
            
            end1++;
            end2++;
            start1 = end1;
            start2 = end2;
        }
        
        if(end1 == size1 && end2 == size2){
            return 0;
        }
        
        if(end1 < size1){
            while(start1 < size1){
                char ch = version1[start1];
                if(ch != DOT && ch != '0'){
                    return 1;
                }
                start1++;
            }
            return 0;
        }
        
        while(start2 < size2){
            char ch = version2[start2];
            if(ch != DOT && ch != '0'){
                return -1;
            }
            start2++;
        }
        return 0;
    }    
};
```
