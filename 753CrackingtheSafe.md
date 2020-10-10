### 753. Cracking the Safe

There is a box protected by a password. The password is a sequence of n digits where each digit can be one of the first k digits 0, 1, ..., k-1.

While entering a password, the last n digits entered will automatically be matched against the correct password.

For example, assuming the correct password is "345", if you type "012345", the box will open because the correct password matches the suffix of the entered password.

Return any password of minimum length that is guaranteed to open the box at some point of entering it.

Example 1:

Input: n = 1, k = 2
Output: "01"
Note: "10" will be accepted too.

Example 2:

Input: n = 2, k = 2
Output: "00110"
Note: "01100", "10011", "11001" will be accepted too.
 
Note:

n will be in the range [1, 4].
k will be in the range [1, 10].
k^n will be at most 4096.

#### My Solutions in Java
```
/**
    I am trying to solve this question as a programmer, not as a mathmatician.  It assumes that a solution starting with 00000... is the shortest solution.
    Time complexity is O(k^k^n), since the result length is k^n, and there are k possibilities for each character in the result.  For the worest case scenerio, 
    the code tries every possibility.
    Space complexity is O((k^n)*n)), since there are k^n passwrods, and each contains n characters.  The hashset has O(k^n) buckets.
*/
class Solution {
    public String crackSafe(int n, int k) {
        if(n < 1 || k < 1){
            return "";
        }
        
        // each character in the password has n possibilities. The count is k^n.
        int count = (int)Math.pow(k, n);
        
        // set the capacity of the StringBuilder for better performance.
        StringBuilder result = new StringBuilder(count + n - 1);
        for(int i = 0; i < n; i++){
            result.append('0');
        }
        
        /* set the initial capacity to avoid unnecessary rehashing. 0.75 is the optimal loadfactor.  performance consideration.
        */
        Set<String> visited = new HashSet<String>((int)((float)count/0.75));
        visited.add(result.toString());
        if(backtracking(result, visited, count-1, k, n)){
            return result.toString();
        }
        return "";

    }
    private boolean backtracking(StringBuilder result, Set<String> visited, int count, int k, int n){
        if(count == 0){
            return true;  // we have tried all the possibilities
        }
        
        String seed = result.substring(result.length() - n + 1);
        // System.out.println("L35 " + seed);
        for(int i = 0; i < k; i++){
            String nextPassword = seed + i;
            if(visited.contains(nextPassword)){
                continue;
            }
            visited.add(nextPassword);
            result.append(i);
            if(backtracking(result, visited, count-1, k, n)){
                return true;
            }
            // reverse the change, then try next i
            result.setLength(result.length()-1);
            visited.remove(nextPassword);
            // System.out.println("rollback " + nextPassword);
        }
        return false;
    }
}
```

#### My Solution in C++
```
/**
   Time complexity is O(k^k^n), since the result length is k^n, and there are k possibilities for each character in the result.  For the worest case scenerio,
   the code tries every possibility.
   Space complexity is O((k^n)*n)), since there are k^n passwrods, and each contains n characters.  The hashset has O(k^n) buckets.
*/
class Solution {
public:
    string crackSafe(int n, int k) {
        if(n < 1 || k < 1){
            return "";
        }
        
        int count = pow(k, n);
        
        string result;
        for(int i = 0; i < n; i++){
            // in C++, string is like a vector
            result.push_back('0');
        }
        //cout << "L18 " << result << endl;
        
        unordered_set<string> visited;
        visited.insert(result);
        return backtracking(result, visited, count-1, k, n) ? result : "";
    }
private:
    bool backtracking(string & result, unordered_set<string> & visited, int count, const int k, const int n){
        if(0 == count){
            return true;
        }
        
        string nextPassword = result.substr(result.size() - n + 1, string::npos);
        for(int i = 0; i < k; i++){
            char ch = '0' + i;
            nextPassword.push_back(ch);
            // cout << "L35 " << nextPassword << endl;
            if(visited.find(nextPassword) != visited.end()){
                nextPassword.resize(n-1);
                continue;
            }
            visited.insert(nextPassword);
            result.push_back(ch);
            if(backtracking(result, visited, count-1, k, n)){
                return true;
            }
            // roll back
            result.resize(result.size()-1);
            visited.erase(nextPassword);
            nextPassword.resize(n-1);
        }
        return false;
    }

};
```
