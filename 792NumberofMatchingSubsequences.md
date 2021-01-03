### 792. Number of Matching Subsequences

Given string S and a dictionary of words words, find the number of words[i] that is a subsequence of S.

Example :

Input: 

S = "abcde"

words = ["a", "bb", "acd", "ace"]

Output: 3

Explanation: There are three words in words that are a subsequence of S: "a", "acd", "ace".

Note:

All words in words and S will only consists of lowercase letters.

The length of S will be in the range of [1, 50000].

The length of words will be in the range of [1, 5000].

The length of words[i] will be in the range of [1, 50].



#### My Solutions in Java
```
/*
    When walking though S, we need to know which words can use the char from S.  To achive this, we define an array of Deque (named as next in the code) to track
which word needs a particular char.  For example, next[0] is for the words with next char being 'a', next[1] is for the words with next char being 'b', .....
    Instead of putting the words into the Deque, we put Pairs into the Deque.  The Pairs are defined as {the index of the word in array words, the index of the
next char in the word}.
    The time complexity is O(n*m), n is the length of S, m is the number of words.  The space complexity is O(m).
*/
class Solution {
    private static final int NUMBERCHAR = 26;
    public int numMatchingSubseq(String S, String[] words) {
        if(S == null || words == null || words.length == 0){
            return 0;
        }
        
        Deque<Pair<Integer, Integer>>[] next = new ArrayDeque[NUMBERCHAR];
        for(int i = 0; i < NUMBERCHAR; i++){
            next[i] = new ArrayDeque<>();
        }
        
        for(int i = 0, iEnd = words.length; i < iEnd; i++){
            // the key of the pair tells which word in words, the value tells which char in the word
            next[words[i].charAt(0) - 'a'].offerLast(new Pair(i, 1));
        }
        
        int result = 0;
        for(int i = 0, iEnd = S.length(); i < iEnd; i++){
            char c = S.charAt(i);
            int count = next[c - 'a'].size();  // The number of words expecting c
            while(count != 0){
                count--;
                Pair pair = next[c - 'a'].pollFirst();
                String currentWord = words[(int)pair.getKey()];  // the word we are processing
                int index = (int)pair.getValue();  // the index of the char we are processing
                if(index < currentWord.length()){ 
                    next[currentWord.charAt(index) - 'a'].offerLast(new Pair(pair.getKey(), index+1));
                }
                else{
                    result++;  // we reached the end of the word
                }
            }
        }
        return result;
    }
}
```

#### My Solution in C++
```
/*
The time complexity is O(n*m), n is the length of S, m is the number of words.  The space complexity is O(m).
*/
class Solution {
public:
    int numMatchingSubseq(string & S, vector<string>& words) {
        if(0 == S.length() || 0 == words.size()){
            return 0;
        }
        
        vector<list<pair<int,int>>> next(26, list<pair<int,int>>());
        for(int i = 0, iEnd = words.size(); i < iEnd; i++){
            next[words[i][0] - 'a'].emplace_back(i, 1);
        }
        
        int result = 0;
        for(int i = 0, iEnd = S.length(); i < iEnd; i++){
            int count = next[S[i] - 'a'].size();
            while(count){
                pair<int, int> & currentPair = next[S[i] - 'a'].front();
                if(currentPair.second < words[currentPair.first].length()){
                    next[words[currentPair.first][currentPair.second] - 'a']
                        .emplace_back(currentPair.first, currentPair.second + 1);
                }
                else{
                    result++;
                }   
                next[S[i] - 'a'].pop_front();
                count--;
            }
        }
        
        return result;
    }
};
```
