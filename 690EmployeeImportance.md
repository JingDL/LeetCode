### 690. Employee Importance  

You are given a data structure of employee information, which includes the employee's unique id, their importance value and their direct subordinates' id.  

For example, employee 1 is the leader of employee 2, and employee 2 is the leader of employee 3. They have importance value 15, 10 and 5, respectively. Then 
employee 1 has a data structure like [1, 15, [2]], and employee 2 has [2, 10, [3]], and employee 3 has [3, 5, []]. Note that although employee 3 is also a 
subordinate of employee 1, the relationship is not direct.

Now given the employee information of a company, and an employee id, you need to return the total importance value of this employee and all their subordinates.

Example 1:

Input: [[1, 5, [2, 3]], [2, 3, []], [3, 3, []]], 1  
Output: 11  
Explanation:  
Employee 1 has importance value 5, and he has two direct subordinates: employee 2 and employee 3. They both have importance value 3. So the total importance value 
of employee 1 is 5 + 3 + 3 = 11.
 

Note:

One employee has at most one direct leader and may have several subordinates.  
The maximum number of employees won't exceed 2000.

#### My Solutions in Java
```
/*
We can solve this problem by recursion or iteration (dfs or bfs).  The time complexity for both of them is O(n),   where 
n is the number of Employees.  The space complexity of recursion depends on the depth of the organization  tree, the 
space complexity of iteration depends on the width of the organization tree.  

For recursion, the software put a function call frame in the stack, and a function call frame contains returned values,  
parameters, local variables, and one pointer pointing the caller function call frame.  It uses a lot of memory in the 
stack.  Iteration puts one reference into the FIFO queue.  For one node in the organization tree, iteration method uses 
fewer space than recursion method.  

The FIFO queue used in iteration method puts data in the heap, while the function call frames are in teh stack.  Personally, 
I prefer Iteration to recursion.  For the test, iteration uses 40.5MB, while recursion uses 54.6MB in Java.  Iteration uses 
14.1MB, while recursion uses 100.3MB in C++.

The time and space complexity is O(n), since the hashMap uses O(n) space.

// Definition for Employee.
class Employee {
    public int id;
    public int importance;
    public List<Integer> subordinates;
};
*/

class Solution {
    public int getImportance(List<Employee> employees, int id) {
        if(null == employees){
            return 0;
        }
        
        HashMap<Integer, Employee> map = new HashMap<>();
        for(Employee employee : employees){
            map.put(employee.id, employee);
        }
        
        Queue<Employee> queue = new ArrayDeque<>();
        Employee current = map.get(id);
        if(null == current){
            return 0;
        }
      
        queue.add(current);  
        int result = 0;
        while(!queue.isEmpty()){
            current = queue.poll();
            result += current.importance;
            for(int subId : current.subordinates){
                Employee next = map.get(subId);
                if(null == next){
                    continue;
                }
                queue.add(next);
            }
        }
        
        return result;
        
    }
}
```
  
```
class Solution {
    public int getImportance(List<Employee> employees, int id) {
        if(null == employees){
            return 0;
        }
        
        HashMap<Integer, Employee> map = new HashMap<>();
        for(Employee employee : employees){
            map.put(employee.id, employee);
        }
        
        return getImportance(map, id);
    }
    
    private int getImportance(HashMap<Integer, Employee> map, int id){
        Employee current = map.get(id);
        if(null == current){
            return 0;
        }
        
        int result = current.importance;
        for(int subId : current.subordinates){
            result += getImportance(map, subId);
        }
        
        return result;
    }
}
```

#### My Solution in C++
```
/*
// Definition for Employee.
class Employee {
public:
    int id;
    int importance;
    vector<int> subordinates;
};
*/

class Solution {
public:
    int getImportance(vector<Employee*> & employees, int id) {
        if(!employees.size()){
            return 0;
        }
        
        unordered_map<int, Employee*> hashMap;
        for(auto pEmployee : employees){
            hashMap[pEmployee->id] = pEmployee;
        }
        
        list<Employee*> queue;
        auto it = hashMap.find(id);
        if(hashMap.end() == it){
            return 0;
        }
        
        queue.push_back(it->second);
        
        int result = 0;
        while(!queue.empty()){
            Employee* current = queue.front();
            queue.pop_front();
            result += current->importance;
            for(int subId : current->subordinates){
                // if subId does not exist, at() or [] will throw an out-of-range exception.
                if(hashMap.end() == (it = hashMap.find(subId))){  
                    continue;
                }
                queue.push_back(it->second);
            }
        }
        return result;
    }
};
```
  
```
class Solution {
public:
    int getImportance(vector<Employee*> & employees, int id) {
        if(!employees.size()){
            return 0;
        }
        
        unordered_map<int, Employee*> hashMap;
        for(auto pEmployee : employees){
            hashMap[pEmployee->id] = pEmployee;
        }
        
        return getImportance(hashMap, id);      
    }
    
private:
    int getImportance(unordered_map<int, Employee*> hashMap, int id){
        if(hashMap.end() == hashMap.find(id)){
            return 0;
        }
        
        Employee* current = hashMap[id];
        int result = current->importance;
        
        for(int subId : current->subordinates){
            result += getImportance(hashMap, subId);
        }
        return result;
    }
};
```
