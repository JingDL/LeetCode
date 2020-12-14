### 995. Minimum Number of K Consecutive Bit Flips

In an array A containing only 0s and 1s, a K-bit flip consists of choosing a (contiguous) subarray of length K and simultaneously changing every 0 in the subarray 
to 1, and every 1 in the subarray to 0.

Return the minimum number of K-bit flips required so that there is no 0 in the array.  If it is not possible, return -1.

 

Example 1:

Input: A = [0,1,0], K = 1

Output: 2

Explanation: Flip A[0], then flip A[2].

Example 2:

Input: A = [1,1,0], K = 2

Output: -1

Explanation: No matter how we flip subarrays of size 2, we can't make the array become [1,1,1].

Example 3:

Input: A = [0,0,0,1,0,1,1,0], K = 3

Output: 3

Explanation:

Flip A[0],A[1],A[2]: A becomes [1,1,1,1,0,1,1,0]

Flip A[4],A[5],A[6]: A becomes [1,1,1,1,1,0,0,0]

Flip A[5],A[6],A[7]: A becomes [1,1,1,1,1,1,1,1]
 

Note:

1 <= A.length <= 30000

1 <= K <= A.length


#### My Solutions in Java
```
/*
Suppose A[0] = 0, the only way to flip it is to flip A[0], … A[K-1].  In other words, for every leftmost 0 at i, we need to flip A[i],…A[i+K-1].  We process A[i]
in this way from the left to the right of A, if i+K > the size of A, we cannot convert every 0 into 1.

When we process A from the left to the right, the flips happened on A[i-K+1]…A[i-1] can affect A[i].  The flips happened before A[i-K+1] should have no effect on
A[i].  We can use a FIFO queue to track the flips.  The FIFO queue records the index of A where a flip happened.  If the the first item of the queue equals (i - k
-1), we remove the first item, and the size of the queue is the number of flips that can affect A[I].

If the size of the queue is an even number, and A[i] (the original value of A[I]) is 0,  we must flip A[I] because it become 0 after all the flips.  If the size of
queue is an odd number, and A[i] is 1, we also need to flip A[I], since A[i] became 0 after all the flips.  We put i into the queue for the coming items, and
update the number of needed flips.

The time complexity is O(n), where n is the size of A, and the space complexity is O(K) for the worst case scenario (e.g. 0101010101)
*/
class Solution {
    public int minKBitFlips(int[] A, int K) {
        if(A == null){
            return 0;
        }
        
        Deque<Integer> queue = new ArrayDeque<>(K);
        int result = 0;
        for(int i = 0; i < A.length; i++){
            if(i >= K && !queue.isEmpty() && i - K == queue.getFirst() ){
                queue.removeFirst();
            }
            
            /*
            We never change A[i], so A[i] is the original value.  If A[i] is 1, and there are odd number of flips in queue, or A[i] is 0, and there are even number 
            of flips in queue, we need to flip A[i]
            */
            if(A[i] == (queue.size() & 0x01) ){
                // To flip A[i], we need to flip A[i+K-1], which is beyong the range of A
                if(i + K > A.length){  
                    return -1;
                }
                result++;
                queue.addLast(i);
            }   
        }
        return result;
        
    }
}
```

```
/*
	This is an enhancement for the above solution.  It uses O(1) memeory.
  Instead of using a queue, we A to record the flips.  Since 0 <= A[i] <= 1, we can use (A[i] | 0x02) to record a flip.  if A[i-K] > 1, it means we fliped A[i-K].
  We use variable flips for the number of flips that can affect A[i].
  At the end, we perform A[i] = A[i] & 0x01 to restore A.
*/
class Solution {
    public int minKBitFlips(int[] A, int K) {
        if(A == null){
            return 0;
        }
        
        int result = 0;
        int flips = 0;
        for(int i = 0; i < A.length; i++){
            if(i >= K && A[i - K] > 1 ){
                flips--;
            }
            
            if((flips & 0x01) == A[i] ){
                // To flip A[i], we need to flip A[i+K-1], which is beyong the range of A
                if(i + K > A.length){  
                    result = -1;
                    break;
                }
                flips++;
                result++;
                A[i] |= 0x02;  // record the flip
            }   
        }
        
        // restore A
        for(int i = 0; i < A.length; i++){
            A[i] &= 0x01;
        }
        return result;
        
    }
}
```

#### My Solution in C++
```
// read my description in my Java solution
class Solution {
public:
    int minKBitFlips(vector<int>& A, int K) {
        list<int> flips;
        int result = 0;
        int ASize = A.size();
        for(int i = 0; i < ASize; i++){
            if(i >= K && !flips.empty() && i - K == flips.front()){
                flips.pop_front();
            }
            
            if(A[i] == (flips.size() & 0x01)){
                if(i + K > ASize){
                    return -1;
                }
                flips.push_back(i);
                result++;
            }
        }
        return result;
    }
};

// The enhancement for this solution is very similar to enhanced Java solution.
```
