### 149. Max Points on a Line

Given n points on a 2D plane, find the maximum number of points that lie on the same straight line.

Example 1:

Input: [[1,1],[2,2],[3,3]]

Output: 3


Example 2:

Input: [[1,1],[3,2],[5,3],[4,1],[2,3],[1,4]]

Output: 4

#### My Solutions in Java
```
/*
    Any straight line can be expressed as
        y = k * x + b
    A pair of [k, b] determines a unique line.
    
    Intuitively, we may think use double k and b.  But this will fail for test case [[0,0],[94911151,94911150],[94911152,94911151]] due to the precision limitation 
    of double variables.  We have to find a different way to represent k.
    
    We can use [dx, dy] to represents slope k, where dx = points[i][0] - points[i][0], dy = points[i][1] - points[j][1].  We need to find the GCD of dx and dy, and 
    convert dx = dx/gcd, dy = dy/gcd.  So all slopes with same values (e.g [dx, dy] and [2*dx, 2*dy]) will be converted to the same values.
    
    We select ith point, and calculate the slope between ith point with other points.  Since these straight lines all go through ith point, the lines with same 
    slopes will have the same intercept on x-axis (b).  We don't need to calculate b.

    The time complexity is O(n^2), where n is the size of points.  The space complexity is O(n), since the HashMap can contain O(n) items. 
*/

class Solution {
    public int maxPoints(int[][] points) {
        if(null == points){
            return 0;
        }
        
        if(points.length < 3){
            return points.length;
        }
        
        HashMap<Long, Integer> map = new HashMap<>(); 
        
        int result = 0;
        // We don't to loop beyond points.length - 1 -result, since that won't increase result.
        for(int i = 0; i < points.length - result - 1; i++){
            int maxCount = 0;
            int samePoint = 0;  // for points overlapping
            map.clear();
            for(int j = i + 1; j < points.length; j++){
                int dx = points[i][0] - points[j][0];
                int dy = points[i][1] - points[j][1];
                if(0 == dx && 0 == dy){
                    samePoint++;
                    continue;
                }
                int gcd = getGcd(dy, dx);
                dx /= gcd;  // gcd will never become 0
                dy /= gcd;
                if(dx < 0){
                    dx = -dx;
                    dy = -dy;
                }
                long slopeHashCode = ((long)dx << 32) ^ dy;
                int slopeCount = map.getOrDefault(slopeHashCode, 0) + 1;
                map.put(slopeHashCode, slopeCount);
                maxCount = Math.max(maxCount, slopeCount);
                //System.out.println("L34 " + dy + " " + dx + " " + kslopeHashCode+ " " + slopeCount);
            }  
            result = Math.max(result, maxCount + samePoint + 1);  // +1 is for ith point
        }
        return result;
    }
    
    private int getGcd(int a, int b){
        while(0 != a){
            int t = a;
            a = b % a;
            b = t;
        }
        return b;
    }
}
```



#### My Solution in C++
```
// The time complexity is O(n^2), where n is the size of points.  The space complexity is O(n), since the HashMap can contain O(n) items.

class Solution {
public:
    int maxPoints(vector<vector<int>>& points) {
        int size = points.size();
        if(points.size() < 3){
            return size;
        }
        
        int result = 0;
        
        unordered_map<long, int> map;
        
        for(int i = 0; i < size - 1 - result; i++){
            int maxCount = 0;
            int samePoint = 0;
            map.clear();
            for(int j = i + 1; j < size; j++){
                int dx = points[j][0] - points[i][0];
                int dy = points[j][1] - points[i][1];
                if(!dx && !dy){
                    samePoint++;
                    continue;
                }
                
                int gcd = getGcd(dx, dy);
                dx /= gcd;
                dy /= gcd;
                if(dx < 0){
                    dx = -dx;
                    dy = -dy;
                }
                long slopeHash = (((long)dx << 32) ^ (long)dy);
                int slopeCount = map[slopeHash] + 1;
                map[slopeHash] = slopeCount;
                // cout << "L35 " << dx << " " << dy << " " << slopeHash << " " << slopeCount << endl;
                maxCount = max(maxCount, slopeCount);
            }
            result = max(result, maxCount + samePoint + 1);
        }
        
        return result;   
    }
private:
    int getGcd(int a, int b){
        while(0 != a){
            int t = a;
            a = b % a;
            b = t;
        }
        return b;
    }
};
```
