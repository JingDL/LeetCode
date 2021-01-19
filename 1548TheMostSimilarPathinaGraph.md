### 1548. The Most Similar Path in a Graph

We have n cities and m bi-directional roads where roads[i] = [ai, bi] connects city ai with city bi. Each city has a name consisting of exactly 3 upper-case English
letters given in the string array names. Starting at any city x, you can reach any city y where y != x (i.e. the cities and the roads are forming an undirected 
connected graph).

You will be given a string array targetPath. You should find a path in the graph of the same length and with the minimum edit distance to targetPath.

You need to return the order of the nodes in the path with the minimum edit distance, The path should be of the same length of targetPath and should be valid (i.e. 
there should be a direct road between ans[i] and ans[i + 1]). If there are multiple answers return any one of them.

The edit distance is defined as follows:

![Image](https://assets.leetcode.com/uploads/2020/08/08/edit.jpg)



Follow-up: If each node can be visited only once in the path, What should you change in your solution?

 

Example 1:

![Image](https://assets.leetcode.com/uploads/2020/08/08/e1.jpg)


Input: n = 5, roads = [[0,2],[0,3],[1,2],[1,3],[1,4],[2,4]], names = ["ATL","PEK","LAX","DXB","HND"], targetPath = ["ATL","DXB","HND","LAX"]

Output: [0,2,4,2]

Explanation: [0,2,4,2], [0,3,0,2] and [0,3,1,2] are accepted answers.

[0,2,4,2] is equivalent to ["ATL","LAX","HND","LAX"] which has edit distance = 1 with targetPath.

[0,3,0,2] is equivalent to ["ATL","DXB","ATL","LAX"] which has edit distance = 1 with targetPath.

[0,3,1,2] is equivalent to ["ATL","DXB","PEK","LAX"] which has edit distance = 1 with targetPath.

Example 2:

![Image](https://assets.leetcode.com/uploads/2020/08/08/e2.jpg)

Input: n = 4, roads = [[1,0],[2,0],[3,0],[2,1],[3,1],[3,2]], names = ["ATL","PEK","LAX","DXB"], targetPath = ["ABC","DEF","GHI","JKL","MNO","PQR","STU","VWX"]

Output: [0,1,0,1,0,1,0,1]

Explanation: Any path in this graph has edit distance = 8 with targetPath.

Example 3:

![Image](https://assets.leetcode.com/uploads/2020/08/09/e3.jpg)

Input: n = 6, roads = [[0,1],[1,2],[2,3],[3,4],[4,5]], names = ["ATL","PEK","LAX","ATL","DXB","HND"], targetPath = ["ATL","DXB","HND","DXB","ATL","LAX","PEK"]

Output: [3,4,5,4,3,2,1]

Explanation: [3,4,5,4,3,2,1] is the only path with edit distance = 0 with targetPath.

It's equivalent to ["ATL","DXB","HND","DXB","ATL","LAX","PEK"]

#### My Solutions in Java
```
/*
    We may think generate a index array from targetPath, then we compare two cities by using integer comparison instead of string comparison, which is much slower 
    than integer comparison.  However, two cities can have the same name according to Constraints.  So we have to do string comparison.
    
    We use dynamical programming to solve this issue.  Since we need to return path with minimal edit distance, we use nextChoice[targetPath.length][n] to store the
    next best choice.  nextChoise[i][j] is the next choice when the jth city is selected for the ith node in the path.  We use minDistance[targetPath.length][n] to 
    store the minimal edit distance.  minDistance[i][j] is the minimal edit distance from the ith node to the end, when the jth city is selected for the ith node in
    the path.
    
    At the end, we find the start of the result, and use nextChoice to build up the result.
    
    Building the graph takes O(m), where m is the size of roads.  When filling minDistance and nextChoice, we spend O(E+V) for each row, where E is the number of 
    edge, and V is the number of vertex of the graph.  For this question, O(E+V) = O(m+n). Since the name of each city has 3 letters, the string comparison takes 
    O(1) time.  So the time complexity is O(k*(m+n)), where k is the length of targetPath, m is the size of roads, n the number of names.
    
    The graph takes O(m) memory space.  minDistance and nextChoice use O(k*n), where k the length of targetPath, n is the size of names.  Although the values at ith
    row only depend on the values at (i+1)th row, we can not reduce the matrix size from k*n to 2*n, because we need to build the returned path.
*/

class Solution {
    public List<Integer> mostSimilar(int n, int[][] roads, String[] names, String[] targetPath) {
        // build the graph from int[][] roads
        ArrayList<LinkedList<Integer>> graph = new ArrayList<>(n);
        
        for(int i = 0; i < n; i++){
            graph.add(new LinkedList<Integer>());
        }
        
        for(int[] road : roads){
            graph.get(road[0]).add(road[1]);
            graph.get(road[1]).add(road[0]);
        }
        
        /* 
        minDistance[i][j] is the optimal edit distance, when the jth city is selected for the ith node in the path.  int[targetPath.length][n] is better than 
        int[n][targetPath.length], because we process it row by row, which causes fewer cache misses.
        */
        int[][] minDistance = new int[targetPath.length][n];  
        for(int[] distance : minDistance){
            Arrays.fill(distance, targetPath.length+1);  // the edit distance can not be greater than targetPath.length+1
        }
        
        int lastRow = targetPath.length - 1;
        
        /* 
        nextChoice[i][j] is the next city to go for achieving the minimal edit distance, when the jth city is selected for the ith node in the path.
        */
        int[][] nextChoice = new int[lastRow][n];  
        
        for(int i = 0; i < n; i++){
            minDistance[lastRow][i] = targetPath[lastRow].equals(names[i]) ? 0 : 1;
        }
        
        // fill minDistance and nextChoice;
        for(int row = lastRow - 1; row >= 0; row--){
            for(int col = 0; col < n; col++){
                int minEdit = targetPath.length + 1;
                int bestChoice = -1;
                for(int next : graph.get(col)){
                    if ( minDistance[row+1][next] < minEdit ){
                        minEdit = minDistance[row+1][next];
                        bestChoice = next;
                    }
                }
                minDistance[row][col] = minEdit + ( targetPath[row].equals(names[col]) ? 0 : 1 );
                nextChoice[row][col] = bestChoice;
            }
        }
        
        // find the start
        int minEdit = targetPath.length + 1;
        int start = -1;
        for(int col = 0; col < n; col++){
            if(minDistance[0][col] < minEdit){
                minEdit = minDistance[0][col];
                start = col;
            }
        }
        
        // build the result
        List<Integer> result = new ArrayList<>(targetPath.length);
        result.add(start);
        for(int row = 0; row < lastRow; row++){
            start = nextChoice[row][start];
            result.add(start);
        }
        
        return result;
    }
}
```

#### My Solution in C++
```
// refer to the ananlysis in my Java solution

class Solution {
public:
    vector<int> mostSimilar(int n, vector<vector<int>>& roads, vector<string>& names, vector<string>& targetPath) {
        // build graph
        vector<vector<int>> graph(n, vector<int>());
        for(auto road : roads){
            graph[road[0]].push_back(road[1]);
            graph[road[1]].push_back(road[0]);
        }
        
        int pathLength = targetPath.size();
        vector<vector<int>> minDistance(pathLength, vector<int>(n, pathLength+1));
        
        int last = pathLength - 1;
        for(int i = 0; i < n; i++){
            minDistance[last][i] = (0 == targetPath[last].compare(names[i]) ? 0 : 1);  // the edit distance for the last row
        }
        
        vector<vector<int>> nextChoice(last, vector<int>(n));
        
        for(int node = last - 1; node >= 0; node--){
            for(int city = 0; city < n; city++){
                int bestChoice = -1;
                for(int next : graph[city]){
                    if(minDistance[node][city] > minDistance[node+1][next]){
                        minDistance[node][city] = minDistance[node+1][next];
                        nextChoice[node][city] = next;
                    }
                }
                minDistance[node][city] += (0 == targetPath[node].compare(names[city]) ? 0 : 1);
            }
        }
        
        // find start
        int start = -1;
        int distance = pathLength + 1;
        for(int city = 0; city < n; city++){
            if(distance > minDistance[0][city]){
                distance = minDistance[0][city];
                start = city;
            }
        }
        
        // build result;
        vector<int> result(pathLength);
        result[0] = start;
        for(int node = 0; node < last; node++){
            start = nextChoice[node][start];
            result[node+1] = start;
        }
        
        return result;
    }
};
```
