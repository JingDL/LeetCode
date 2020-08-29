### 50. Pow(x, n)

Implement pow(x, n), which calculates x raised to the power n (i.e. xn).

 

Example 1:

Input: x = 2.00000, n = 10
Output: 1024.00000
Example 2:

Input: x = 2.10000, n = 3
Output: 9.26100
Example 3:

Input: x = 2.00000, n = -2
Output: 0.25000
Explanation: 2-2 = 1/22 = 1/4 = 0.25
 

Constraints:

-100.0 < x < 100.0
-231 <= n <= 231-1
-104 <= xn <= 104










#### My Solution in C++
```
/* 
    iteration
    time complexity O(lg(n)), space complexity O(1)
    consider 5^9 = 5^(8+1) = (5^8)*(5^1) = (5^8)^1*(5^4)^0*(5^2)^0*(5^1)^1
*/
class Solution {
public:
    double myPow(double x, int n) {
        if(0 == n){
            return 1.0;
        }
        if(x == 0.0){
            return n > 0? x : DBL_MAX;  // or std::numeric_limits<double>::max();
        }
        long longN = (long)n;
        if(n < 0){
            x = 1.0/x;
            longN = -longN;
        }
        double result = 1;
        long bitMask = 0x01;
        while(bitMask <= longN){
            if(bitMask & longN){
                result *= x;
            }
            x = x * x;
            bitMask <<= 1;
        }
        return result;
    }
};

/*  recursion
    time complexity O(lg(n)), space complexity O(lg(n))
    consider 5^9 = 5^(8+1) = (5^8)*(5^1) = (5^8)^1*(5^4)^0*(5^2)^0*(5^1)^1
*/
class Solution {
public:
    double myPow(double x, int n) {
        if(0 == n){
            return 1.0;
        }
        if(x == 0.0){
            return n > 0? x : DBL_MAX;  // or std::numeric_limits<double>::max();
        }
        long longN = (long)n;
        if(n < 0){
            x = 1.0/x;
            longN = -longN;
        }
        return myPowAux(x, longN);
    }
private:
    double myPowAux(double x, long n){
    if( 0 == n){
        return 1.0;
    }
    return (n & 0x01)? x*myPowAux(x*x, n>>1) : myPowAux(x*x, n>>1);
}
};
```

#### My Solution in Java
```
/*  
    iteration
    time complexity O(lg(n)), space complexity O(1)
    consider 5^9 = 5^(8+1) = (5^8)*(5^1) = (5^8)^1*(5^4)^0*(5^2)^0*(5^1)^1
*/
class Solution {
    public double myPow(double x, int n) {
        if(0 == n){
            return 1.0;
        }
        if(0.0 == x){
            return n > 0 ? 0.0: Double.MAX_VALUE;
        }
        double result = 1.0;
        long longN = (long)n;
        if(n < 0){
            longN = -longN;  // if we use int, -(Integer.MIN_VALUE) will overflow.
            x = 1.0 / x;
        }
        long bitMask = 0x01;
        while(bitMask <= longN){
            if((bitMask & longN) != 0){
                result *= x;
            }
            x = x * x;
            bitMask <<= 1;
        }
        return result;
    }
}

/*  
    recursion
    time complexity O(lg(n)), space complexity O(lg(n))
    consider 5^9 = 5^(8+1) = (5^8)*(5^1) = (5^8)^1*(5^4)^0*(5^2)^0*(5^1)^1
*/
class Solution {
    public double myPow(double x, int n) {
        if(0 == n){
            return 1.0;
        }
        if(0.0 == x){
            return n > 0 ? 0.0: Double.MAX_VALUE;
        }
        double result = 1.0;
        long longN = (long)n;
        if(n < 0){
            longN = -longN;  // if we use int, -(Integer.MIN_VALUE) will overflow.
            x = 1.0 / x;
        }
        return myPowAux(x, longN);
    }
private double myPowAux(double x, long n){
    if( 0 == n){
        return 1;
    }
    return (n & 0x01) == 0x01 ? x * myPowAux(x*x, n>>1) : myPowAux(x*x, n>>1);
}
}
```
