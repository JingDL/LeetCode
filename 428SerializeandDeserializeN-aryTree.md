### 428. Serialize and Deserialize N-ary Tree

Serialization is the process of converting a data structure or object into a sequence of bits so that it can be stored in a file or memory buffer, or transmitted 
across a network connection link to be reconstructed later in the same or another computer environment.

Design an algorithm to serialize and deserialize an N-ary tree. An N-ary tree is a rooted tree in which each node has no more than N children. There is no 
restriction on how your serialization/deserialization algorithm should work. You just need to ensure that an N-ary tree can be serialized to a string and this 
string can be deserialized to the original tree structure.

For example, you may serialize the following 3-ary tree

![Image](https://assets.leetcode.com/uploads/2018/10/12/narytreeexample.png)


as [1 [3[5 6] 2 4]]. Note that this is just an example, you do not necessarily need to follow this format.

Or you can follow LeetCode's level order traversal serialization format, where each group of children is separated by the null value.

![Image](https://assets.leetcode.com/uploads/2019/11/08/sample_4_964.png)


For example, the above tree may be serialized as [1,null,2,3,4,5,null,null,6,7,null,8,null,9,10,null,null,11,null,12,null,13,null,null,14].

You do not necessarily need to follow the above suggested formats, there are many more different formats that work so please be creative and come up with different 
approaches yourself.

 

Constraints:

The height of the n-ary tree is less than or equal to 1000
The total number of nodes is between [0, 10^4]
Do not use class member/global/static variables to store states. Your encode and decode algorithms should be stateless.

#### My Solutions in Java
```
/*
Certainly, I can use level traversal to serialize/deserialize the N-tree.  But it will use a lot of memory, because I need to put all the nodes in one level 
into a FIFO queue.  If the N-tree is a balance tree, the queue size is O(n), where n is the number of nodes of the tree.  

Level traversal is a good option if the N-tree is just a linked list.

If I use pre-order traversal, the depth of recursion call is the height of the tree.  If the N-tree is balanced, the height is O(lg(n)).

The time complexity and space complexity both are O(n).

// Definition for a Node.
class Node {
    public int val;
    public List<Node> children;

    public Node() {}

    public Node(int _val) {
        val = _val;
    }

    public Node(int _val, List<Node> _children) {
        val = _val;
        children = _children;
    }
};
*/

class Codec {
    static final String SPLITTER = " ";
    // Encodes a tree to a single string.
    public String serialize(Node root) {
        if(root == null){
            return "";
        }
        
        StringBuilder stringBuilder = new StringBuilder();
        serialize(root, stringBuilder);
        return stringBuilder.toString();
    }
    
    private void serialize(Node root, StringBuilder stringBuilder){
        if(root == null){
            return;
        }
        
        stringBuilder.append(String.valueOf(root.val))
            .append(SPLITTER)
            .append(root.children.size())
            .append(SPLITTER);
        
        for(Node next : root.children){
            serialize(next, stringBuilder);
        }
        return;
    }
	
    // Decodes your encoded data to tree.
    public Node deserialize(String data) {
        if(data == null || data.length() == 0){
            return null;
        }
        
        String dataArray[] = data.split(SPLITTER);
        Node root = new Node();
        deserialize(root, dataArray, 0);
        return root;
    }
    
    /*
        The returned value is the index for the processed node.  nextIndex+1 points to value for next node.  
        If you think it is hard to understand the nextIndex, you can use a class variable to trace the next string to be processed.
    */
    private int deserialize(Node node, String dataArray[], int index){
        if(index == dataArray.length){
            return index;
        }
        node.val = Integer.parseInt(dataArray[index]);
        int childrenSize = Integer.parseInt(dataArray[index + 1]);
        node.children = new ArrayList<Node>(childrenSize);
        int nextIndex = index + 2;
        for(int i = 0; i < childrenSize; i++){
            Node child = new Node();
            nextIndex = deserialize(child, dataArray, nextIndex);
            node.children.add(child);
        }
        return nextIndex;
    }
}

// Your Codec object will be instantiated and called as such:
// Codec codec = new Codec();
// codec.deserialize(codec.serialize(root));
```

#### My Solution in C++
```
/*
// Definition for a Node.
class Node {
public:
    int val;
    vector<Node*> children;

    Node() {}

    Node(int _val) {
        val = _val;
    }

    Node(int _val, vector<Node*> _children) {
        val = _val;
        children = _children;
    }
};
*/

class Codec {
public:
    // Encodes a tree to a single string.
    string serialize(Node* root) {
        ostringstream result;
        serialize(root, result);
        return result.str();
    }
	
    // Decodes your encoded data to tree.
    Node* deserialize(string data) {
        istringstream dataStream(data);
        return deserialize(dataStream);
    }
private:
    const string SEPARATOR = " ";
    void serialize(Node * root, ostringstream & result){
        if(root == nullptr){
            return;
        }

        result << root->val << SEPARATOR << root->children.size() << SEPARATOR;
        for(Node * child : root->children){
            serialize(child, result);
        }
    }
    
    Node * deserialize(istringstream & dataStream){
        dataStream.peek();
        if(dataStream.eof()){
            return nullptr;
        }
        int val, childrenSize;
        dataStream >> val >> childrenSize;
        //cout << val << SEPARATOR << childrenSize << endl;
        Node * node = new Node(val);
        node->children.reserve(childrenSize);  // performance consideration.  This line reduces runtime from 112 ms (18.49%) to 48 ms (98.84%)
        for(int i = 0; i < childrenSize; i++){
            Node * child = deserialize(dataStream);
            node->children.push_back(child);
        }
        return node;
    }
    
};

// Your Codec object will be instantiated and called as such:
// Codec codec;
// codec.deserialize(codec.serialize(root));
```
