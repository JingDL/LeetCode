### 715. Range Module
A Range Module is a module that tracks ranges of numbers. Your task is to design and implement the following interfaces in an efficient manner.

addRange(int left, int right) Adds the half-open interval [left, right), tracking every real number in that interval. Adding an interval that partially overlaps with currently tracked numbers should add any numbers in the interval [left, right) that are not already tracked.

queryRange(int left, int right) Returns true if and only if every real number in the interval [left, right) is currently being tracked.

removeRange(int left, int right) Stops tracking every real number currently being tracked in the interval [left, right).

Example 1:

addRange(10, 20): null

removeRange(14, 16): null

queryRange(10, 14): true (Every number in [10, 14) is being tracked)

queryRange(13, 15): false (Numbers like 14, 14.03, 14.17 in [13, 15) are not being tracked)

queryRange(16, 17): true (The number 16 in [16, 17) is still being tracked, despite the remove operation)

Note:

A half open interval [left, right) denotes all real numbers left <= x < right.

0 < left < right < 10^9 in all calls to addRange, queryRange, removeRange.

The total number of calls to addRange in a single test case is at most 1000.

The total number of calls to queryRange in a single test case is at most 5000.

The total number of calls to removeRange in a single test case is at most 1000.


#### My Solutions in Java
```
/* 
The space complexity is O(n), which n is the max size of intervals stored in the TreeMap.  TreeMap uses red-black balanced binary searching tree.  It takes O(log(n)) to find one item.
*/
class RangeModule {

    public RangeModule() {
        sortedIntervals = new TreeMap<Integer, Integer>();
    }
    
    // time complexity O(m*log(n)), m is the number of overlapping intervals, n is the size of sortedIntervals.
    public void addRange(int left, int right) {
        /* It is faster to read entire Map.Entry than getting key first, then getting value by using the key.  The former calls hash function once, but the latter
        calls hash function twice. */
        Map.Entry<Integer, Integer> leftFloor = sortedIntervals.floorEntry(left);  
        int start = left;
        if(leftFloor != null){
            start = leftFloor.getValue() >= left ? leftFloor.getKey() : left;
        }
        
       Map.Entry<Integer, Integer> rightFloor = sortedIntervals.floorEntry(right);
        int end = right;
        if(rightFloor != null){
            end = rightFloor.getValue() > end ? rightFloor.getValue() : right;
        }
         
        sortedIntervals.put(start, end);
        
         // remove overlapping intervals.  left -- exclusive, right -- inclusive
        sortedIntervals.subMap(left, false, right, true).clear();
    }
    
    // time complexity is O(log(n))
    public boolean queryRange(int left, int right) {
        Map.Entry<Integer, Integer> floor = sortedIntervals.floorEntry(left);
        if(floor == null){
            return false;
        }
        return floor.getValue() >= right;
    }
    
    // time complexity is O(m*log(n))
    public void removeRange(int left, int right) {
        // We must read the original sortedIntervals before making any change
        Map.Entry<Integer, Integer> leftFloor = sortedIntervals.floorEntry(left);
        Map.Entry<Integer, Integer> rightFloor = sortedIntervals.floorEntry(right);
        
        if(rightFloor != null && rightFloor.getValue() > right){
            sortedIntervals.put(right, rightFloor.getValue());
        }
        
        if(leftFloor != null && leftFloor.getValue() > left){
            sortedIntervals.put(leftFloor.getKey(), left);
        }
        
        sortedIntervals.subMap(left, true, right, false).clear();
    }
    
    private TreeMap<Integer, Integer> sortedIntervals;
}

/**
 * Your RangeModule object will be instantiated and called as such:
 * RangeModule obj = new RangeModule();
 * obj.addRange(left,right);
 * boolean param_2 = obj.queryRange(left,right);
 * obj.removeRange(left,right);
 */
```


#### My Solution in C++
```
/*
   In C++, lower_bound() and upper_bound() return an iterator, so we can loop through the map by using the iterator.  The time complexity of addRange() and removeRange() are O(max(log(n), m)), the time complexity of queryRanger() is O(log(n)), where n is the number of items in sortedIntervals, m is the number of overlapping ranges.  The space complexity is O(n).
    The definition of lower_bound() and upper_bound() is very different from floorEntry() or ceilingEntry() in Java.
    For aaabbbbbccc, lower_bound(b) returns an iterator pointing the first b, upper_bound(b) returns an iterator pointing to the first c.  This is why it is called "bound".  It gives us the range of b [ lower_bound(b), upper_bound(b) ).
*/
class RangeModule {
public:
    RangeModule() {
    }

    void addRange(int left, int right) {
        //cout << "L11 addRange " << left << " " << right << endl;
        if(sortedIntervals.size() == 0){
            sortedIntervals[left] = right;
            //display();
            return;
        }
        
        auto leftBound = sortedIntervals.upper_bound(left);
        auto current = leftBound;
        if(current != sortedIntervals.begin()){
            current--;
            if(current->second >= left){
                left = min(current->first, left);
                right = max(current->second, right);
                sortedIntervals.erase(current);
            }
        }
        
        current = leftBound;
        auto ed = sortedIntervals.end();
        while(current != ed && current->first <= right){  //should be <= instead of <. Merge them if right == current->first
           auto toBeErased = current;
           right = max(current->second, right);
           current++;
           sortedIntervals.erase(toBeErased);
        }
        
        sortedIntervals[left] = right;
        // display();
    }
    
    bool queryRange(int left, int right) {
        // cout << "L46 queryRange " << left << " " << right << " ";
        if(left >= right || sortedIntervals.size() < 1){
            // cout << "false" << endl;
            return false;
        }
        
        auto leftBound = sortedIntervals.upper_bound(left);
        if(leftBound == sortedIntervals.begin()){
            // cout << "false" << endl;
            return false;
        }
        
        leftBound--;
        // return leftBound->first <= left && leftBound->second >= right;
        // cout << (leftBound->second >= right ? "true" : "false") << endl;
        return leftBound->second >= right;
    }
    
    void removeRange(int left, int right) {
        // cout << "L61 removeRange " << left << " " << right << endl;
        if(sortedIntervals.size() < 1 || left >= right){
            // cout << "doing nothing" << endl;
            return;
        }
        
        // **** process from right to left
        auto rightBound = sortedIntervals.upper_bound(right);
        auto current = rightBound;
        // if current == sortedIntervals.begin(), [left, right) does not overlap with any existing range.  We do not remove anything.
        if(current != sortedIntervals.begin()){  
            current--;
            if(current->second > right){
                //only add a new range, and keep all the existing ranges for processing left
                sortedIntervals[right] = current->second;  
            }
            
            while(current != sortedIntervals.begin() && current->first > left){
                auto prev = current;
                current--;
                sortedIntervals.erase(prev);
            }
            
            if(current->first < left){
                current->second = min(left, current->second);
            }
            else{
                sortedIntervals.erase(current);
            }
        }
         
        // display();
    }
private:
    map<int, int> sortedIntervals;
    void display(){
        for(auto interval : sortedIntervals){
            cout << " [" << interval.first << "=" << interval.second << "]";
        }
        cout << endl;
    }
};
```
