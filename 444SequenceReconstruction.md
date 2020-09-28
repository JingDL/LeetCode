### 444. Sequence Reconstruction

Check whether the original sequence org can be uniquely reconstructed from the sequences in seqs. The org sequence is a permutation of the integers from 1 to n, 
with 1 ≤ n ≤ 104. Reconstruction means building a shortest common supersequence of the sequences in seqs (i.e., a shortest sequence so that all sequences in seqs 
are subsequences of it). Determine whether there is only one sequence that can be reconstructed from seqs and it is the org sequence.

Example 1:

Input: org = [1,2,3], seqs = [[1,2],[1,3]]
Output: false
Explanation: [1,2,3] is not the only one sequence that can be reconstructed, because [1,3,2] is also a valid sequence that can be reconstructed.

Example 2:

Input: org = [1,2,3], seqs = [[1,2]]
Output: false
Explanation: The reconstructed sequence can only be [1,2].

Example 3:

Input: org = [1,2,3], seqs = [[1,2],[1,3],[2,3]]
Output: true
Explanation: The sequences [1,2], [1,3], and [2,3] can uniquely reconstruct the original sequence [1,2,3].

Example 4:

Input: org = [4,1,5,2,6,3], seqs = [[5,2,6,3],[4,1,5,2]]
Output: true
 

Constraints:
1 <= n <= 10^4
org is a permutation of {1,2,...,n}.
1 <= segs[i].length <= 10^5
seqs[i][j] fits in a 32-bit signed integer.

#### My Solutions in Java
```
/* 
    It took me a while to fully understand the question.
    
    This method does not use conventional topological sorting.  I use inDegree to figure out
    the next number (if inDegree == 0).  inDegree use O(org.size()) memory.  But this does not
    mean it use more memory than topological sorting, because topological sorting uses recursion,
    which puts function call frames in memory
    
    assuming seqs contains n List, and the max length of the Lists is m.
    The time and space complexity is O(n*m);
*/
class Solution {
    public boolean sequenceReconstruction(int[] org, List<List<Integer>> seqs) {
        if(org == null || seqs == null || seqs.size() == 0){
            return false;
        }
        
        /*
            We cannot use ArrayList<HashSet<Integer>> here.  The same order l->m may appear 
            in seqs[x] and seqs[y].  If we use ArrayList<HashSet<Integer>>, we will record l->m
            once in graph, but inDegre[m] > 1.
        */
        ArrayList<ArrayList<Integer>> graph = new ArrayList<>(org.length+1);
        ArrayList<Integer> inDegree = new ArrayList<>(org.length+1);

        for(int i = 0; i <= org.length; i++){
            graph.add(new ArrayList<Integer>());
            inDegree.add(0);
        }
        
        for(List<Integer> seq : seqs){
            Integer prev = null;
            for(Integer curr : seq){
                if(curr < 1 || curr > org.length){
                    return false;
                }
                if(prev != null){
                    graph.get(prev).add(curr);
                    inDegree.set(curr, inDegree.get(curr)+1);
                }
                prev = curr;
            }
        }
        
        Deque<Integer> queue = new ArrayDeque<>();
        for(int i = 1; i <= org.length; i++){
            if(inDegree.get(i) == 0){
                if(queue.size() != 0){  // we can have two starting points
                    return false;
                }
                queue.add(i);
            }
        }
        
        int index = 0;
        while(!queue.isEmpty()){
            int curr = queue.poll();
            if(curr != org[index++]){
                return false;  // value is not right
            }
            for(int next : graph.get(curr)){
                inDegree.set(next, inDegree.get(next)-1);
                if(inDegree.get(next) == 0){
                    if(queue.size() != 0 ){
                        return false; // not unique
                    } 
                    queue.add(next);
                }
            }
        }
        return index == org.length;  // we must reach the end of org
        
    }
}
```

```
/*
    Assuming that seqs contain n Lists, and the max length of the Lists is m.
    The time complexity is O(n*m), the space complexity is O(org.length)
*/
class Solution {
    public boolean sequenceReconstruction(int[] org, List<List<Integer>> seqs) {
        if(org == null || seqs == null || seqs.size() == 0){
            return false;
        }
        
        int[] order = new int[org.length+1];
        for(int i = 0; i < org.length; i++){
            order[org[i]] = i;
        }
        boolean[] filled = new boolean[org.length+1];
        
        for(List<Integer> seq : seqs){
            int prev = -1;
            for(int curr : seq){
                if(curr < 1 || curr > org.length){
                    return false;  // out of range
                }
                if(prev == -1){
                    prev = curr;
                    continue;
                }
                
                if(order[prev] >= order[curr]){
                    return false;  // cyclic graph, not unique reconstruction
                }
                
                if(order[prev] + 1 == order[curr]){ 
                    filled[prev] = true;
                }
                prev = curr;
            }
        }
        
        // filled[org[i]] should be true except the last one
        for(int i = 0; i < org.length - 1; i++){
            if(filled[org[i]] == false){
                return false;
            }
        }
        
        return true;
    }
}
```

#### My Solution in C++
```
class Solution {
public:
    bool sequenceReconstruction(vector<int>& org, vector<vector<int>>& seqs) {
        if(!seqs.size()){
            return false;
        }
        
        int orgSize = org.size();
        vector<vector<int>> graph(orgSize+1, vector<int>());
        vector<int> inDegree(orgSize+1, 0);
        
        for(auto seq : seqs){
            int prev = -1;
            for(auto curr : seq){
                if(curr < 1 || curr > orgSize){
                    return false;
                }
                if(-1 == prev){
                    prev = curr;
                    continue;
                }
                graph[prev].push_back(curr);
                inDegree[curr]++;
                prev = curr;
            }
        }
        
        list<int> queue;
        for(int i = 1; i <= orgSize; i++){
            if(inDegree[i] == 0){
                if(queue.size() > 0){
                    return false;  // we should have only one starting point, unique construction
                }
                queue.push_back(i);
            }
        }
        
        int index = 0;
        while(!queue.empty()){
            int curr = queue.front();
            if(curr != org[index]){
                return false;
            }
            index++;
            queue.pop_front();
            for(int next : graph[curr]){
                inDegree[next]--;
                if(inDegree[next] == 0){
                    if(queue.size() > 0){
                        return false;
                    }
                    queue.push_back(next);
                }
            }
        }
        
        return index == org.size();
    }
};
```

```
class Solution {
public:
    bool sequenceReconstruction(vector<int>& org, vector<vector<int>>& seqs) {
        if(!seqs.size()){
            return false;
        }
        
        int orgSize = org.size();
        int order[orgSize+1];
        bool filled[orgSize];
        
        for(int i = 0; i < orgSize; i++){
            order[org[i]] = i;
            filled[i] = false;
        }
        
        for(auto seq : seqs){
            int prev = -1;
            for(auto next : seq){
                if(next < 1 || next > orgSize){
                    return false;
                }
                if(-1 == prev){
                    prev = next;
                    continue;
                }
                if(order[prev] >= order[next]){
                    return false;
                }
                if(order[prev] + 1 == order[next]){
                    filled[order[prev]] = true;
                }
                prev = next;
            }
        }
        
        // the last one is not filled
        for(int i = 0; i < orgSize -1; i++){
            if(!filled[i]){
                return false;
            }
        }
        return true;
    }
};
```
