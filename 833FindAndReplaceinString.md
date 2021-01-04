### 833. Find And Replace in String

To some string S, we will perform some replacement operations that replace groups of letters with new ones (not necessarily the same size).

Each replacement operation has 3 parameters: a starting index i, a source word x and a target word y.  The rule is that if x starts at position i in the original
string S, then we will replace that occurrence of x with y.  If not, we do nothing.

For example, if we have S = "abcd" and we have some replacement operation i = 2, x = "cd", y = "ffff", then because "cd" starts at position 2 in the original string 
S, we will replace it with "ffff".

Using another example on S = "abcd", if we have both the replacement operation i = 0, x = "ab", y = "eee", as well as another replacement operation i = 2, x = "ec", 
y = "ffff", this second operation does nothing because in the original string S[2] = 'c', which doesn't match x[0] = 'e'.

All these operations occur simultaneously.  It's guaranteed that there won't be any overlap in replacement: for example, S = "abc", indexes = [0, 1], sources =
["ab","bc"] is not a valid test case.


Example 1:

Input: S = "abcd", indexes = [0, 2], sources = ["a", "cd"], targets = ["eee", "ffff"]

Output: "eeebffff"

Explanation:

"a" starts at index 0 in S, so it's replaced by "eee".

"cd" starts at index 2 in S, so it's replaced by "ffff".


Example 2:

Input: S = "abcd", indexes = [0, 2], sources = ["ab","ec"], targets = ["eee","ffff"]

Output: "eeecd"

Explanation:

"ab" starts at index 0 in S, so it's replaced by "eee".

"ec" doesn't starts at index 2 in the original S, so we do nothing.
 

Constraints:

0 <= S.length <= 1000

S consists of only lowercase English letters.

0 <= indexes.length <= 100

0 <= indexes[i] < S.length

sources.length == indexes.length

targets.length == indexes.length

1 <= sources[i].length, targets[i].length <= 50

sources[i] and targets[i] consist of only lowercase English letters.

#### My Solutions in Java
```
/*
    Intuitively, we may consider processing S from the right to the left, because changes made on the right does not change the index on the left.  However, this 
    method copies a lot of substrings.
    
    If we process S from the left to the right.  By using a StringBuilder, we only copy each character once. 
    
    The time complexity O(max(n, k*log(k))), and the space complexity is O(m+k), where n is the length of S, k is the size of indexes, and m is the final result 
    size.
    
    We can avoid the sorting operation, which takes O(k*log(k)), by using a HashMap to store {indexes[i], i}.  Then we loop through S, and check if the index is in 
    the HashMap.  If it is in the HashMap, we replace the source with the target.  However, this method may no necessarily improve the performance, because we need 
    to call the Hash function for every index of S.  If the length of S is much larger than the size of indexes, this method could be slower.
*/

class Solution {
    public String findReplaceString(String S, int[] indexes, String[] sources, String[] targets) {
        ArrayList<int[]> sortedIndexes = new ArrayList<>();
        for(int i = 0; i < indexes.length; i++){
            sortedIndexes.add(new int[]{indexes[i], i});
        }
        
        Collections.sort(sortedIndexes, (a, b) -> (a[0]-b[0]));
        
        StringBuilder stringBuilder = new StringBuilder();
        int nextStart = 0;
        for(int[] pair : sortedIndexes){
            String source = sources[pair[1]];
            String target= targets[pair[1]];
            if(source.equals(S.substring(pair[0], pair[0] + source.length()))){
                stringBuilder.append(S.substring(nextStart, pair[0]));
                stringBuilder.append(target);
                nextStart = pair[0] + source.length();
            }
        }
        
        stringBuilder.append(S.substring(nextStart));
        
        return stringBuilder.toString();
    }
}
```

#### My Solution in C++
```
class Solution {
public:
    string findReplaceString(string & S, vector<int>& indexes, vector<string>& sources, vector<string>& targets) {
        if( 0 == indexes.size() ){
            return S;
        }
        
        map<int, int> sorted;
        for(int i = 0, iEnd = indexes.size(); i < iEnd; i++){
            sorted[indexes[i]] = i;
        }
        
        int nextStart = 0;
        string result;
        for(auto p : sorted){
            string & source = sources[p.second];
            string & target = targets[p.second];
            if(0 == source.compare(S.substr(p.first, source.size()))){
                result.append(S.substr(nextStart, p.first - nextStart));
                result.append(target);
                nextStart = p.first + source.size();
            }
        }
        
        result.append(S.substr(nextStart));
        return result;
    }
};
```
