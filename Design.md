## 146. LRU Cache

### 1. Using Hashmap + DoubleLinkedList

```java
class LRUCache {
  class Node{
    int key;
    int value;
    Node pre;
    Node next;
    public Node(int key, int value) {
      this.key = key;
      this.value = value;
    }
  }
  private HashMap<Integer, Node> map;
  private int capacity;
  private Node tail;
  private Node head;
  public LRUCache(int capacity) {
    map = new HashMap<>();
    this.capacity = capacity;
    head = null;
    tail = null;
  }
  public int get(int key) {
    if (!map.containsKey(key)) {return -1;}
    Node cur = map.get(key);
    if (cur != tail) {
      if (cur == head) {
        head = head.next;
      } else {
        cur.pre.next = cur.next;
        cur.next.pre = cur.pre;
      }
      tail.next = cur;
      cur.pre = tail;
      cur.next = null;
      tail = cur;
    }
    return tail.value;
  }
  public void put(int key, int value) {
    Node node = map.get(key);
    //如果存在节点
    if (node != null) {
      node.value = value;
      if (node != tail) {
        if (node == head) {
          head = head.next;
        } else {
          node.pre.next = node.next;
          node.next.pre = node.pre;
        }
        tail.next = node;
        node.next = null;
        node.pre = tail;
        tail = node;
      }
    } else {
      //如果不存在，插入新节点
      Node newNode = new Node(key, value);
      if (capacity == 0) {
        Node temp = head;
        head = head.next;
        map.remove(temp.key);
        capacity++;
      } 
      if (head == null) {
        head = newNode;
      } else {
        tail.next = newNode;
        newNode.pre = tail;
        newNode.next = null;
      }
      tail = newNode;
      map.put(key, newNode);
      capacity--;
    }
  }
}
```



## 348. Design Tic - Tac- Toe

### 1. Using Two Arrays and Two variables

```java
class TicTacToe {
    int[] rows;
    int[] cols;
    int diagonal;
    int antiDiagonal;
    private int[][] player2;
    public TicTacToe(int n) {
        rows = new int[n];
        cols = new int[n];
        diagonal = 0;
        antiDiagonal = 0;
    }
    
    public int move(int row, int col, int player) {
        int n = rows.length;
        int cur = (player == 1) ? 1 : -1;
        rows[row] += cur;
        cols[col] += cur;
        if (row == col) {
            diagonal += cur;
        }
        if (row + col == rows.length - 1){
            antiDiagonal += cur;
        }
        if (Math.abs(rows[row]) == n || Math.abs(cols[col]) == n || Math.abs(diagonal) == n || Math.abs(antiDiagonal) == n) {
            return player;
        } else {
            return 0;
        }
    }
}

/**
 * Your TicTacToe object will be instantiated and called as such:
 * TicTacToe obj = new TicTacToe(n);
 * int param_1 = obj.move(row,col,player);
 */
```



## 384. Shuffle an Array

### 1. Brute Force (Using List to Randomize the Array)

```java
class Solution {
  private int[] org;
  private int[] randomArray;
  private Random rand = new Random();
  
  public Solution(int[] nums) {
    org = nums.clone();
    randomArray = nums.clone();
  }
    
  public int[] reset() {
    return org;
  }

  public int[] shuffle() {
    List<Integer> randomList = new ArrayList<>();
    for (int i = 0; i < org.length; i++) {
      randomList.add(org[i]);
    }
    for (int j = 0; j < randomArray.length; j++) {
      int randIndex = rand.nextInt(randomList.size()); // random 0 ~ (size() - 1)
      randomArray[j] = randomList.get(randIndex);
      randomList.remove(randIndex);//Time n
    }
    return randomArray;
  }
}

/**
 * Your Solution object will be instantiated and called as such:
 * Solution obj = new Solution(nums);
 * int[] param_1 = obj.reset();
 * int[] param_2 = obj.shuffle();
 */
```

### 2. Fisher- Yates Algorithm

```java
class Solution {
  private int[] org;
  private int[] randomArray;
  private Random rand = new Random();
  
  public Solution(int[] nums) {
    org = nums.clone();
    randomArray = nums.clone();
  }
    
  public int[] reset() {
    return org;
  }

  public int[] shuffle() {
    for (int i = 0; i < randomArray.length; i++) {
      randChoose(i, randomArray.length);
    }
    return randomArray;
  }
  public void randChoose(int l, int h) {
    int chooseNum = rand.nextInt(h - l) + l;
    swap(l, chooseNum, randomArray);
  }
  public void swap(int i, int j, int[] a) {
    int temp = a[i];
    a[i] = a[j];
    a[j] = temp;
  }
}
```

![image-20220707231420169](/Users/youhao/Library/Application Support/typora-user-images/image-20220707231420169.png)

## 208. Implement Trie (Prefix Tree)

### 1. Using HashMap (Too Slow)

```java
class Trie {
    private Map<String, Integer> map;
    public Trie() {
        map = new HashMap<>();
    }
    
    public void insert(String word) {
        map.put(word, map.getOrDefault(word, 0) + 1);
    }
    
    public boolean search(String word) {
        if (map.containsKey(word)) {
            return true;
        } else {
            return false;
        }
    }
    
    public boolean startsWith(String prefix) {
        int lp = prefix.length();
        boolean check = false;
        for (String cur : map.keySet()) {
            int lc = cur.length();
            if (lc >= lp) {
                String curSub = cur.substring(0, lp);
                if (prefix.equals(curSub)) {
                    return true;
                }
            }
        }
        return false;
    }
}

/**
 * Your Trie object will be instantiated and called as such:
 * Trie obj = new Trie();
 * obj.insert(word);
 * boolean param_2 = obj.search(word);
 * boolean param_3 = obj.startsWith(prefix);
 */
```



### 2. Prefix Tree

```java
class Trie {
  class TrieNode {
    TrieNode[] children; // 26 object
    boolean isWord;
    
    public TrieNode() {
      children = new TrieNode[26];
      isWord = false;
    }
  }
  
  private TrieNode root;
  
  public Trie() {
    root = new TrieNode();
  }
  
  public void insert(String word) {
    TrieNode curNode = root;
    for (int i = 0; i < word.length(); i++) {
      int index = word.charAt(i) - 'a';
      if (curNode.children[index] == null) {
        curNode.children[index] = new TrieNode();
      }
      curNode = curNode.children[index];
    }
    curNode.isWord = true;
    // Time O(m) word's length
    // Space O(m)
  }
  
  public boolean search(String word) {
    TrieNode curNode = root;
    for (int i = 0; i < word.length(); i++){
      int index = word.charAt(i) - 'a';
      if (curNode.children[index] == null) {
        return false;
      }
      curNode = curNode.children[index];
    }
    if (curNode.isWord) {return true;}
    else {return false;}
    // Time O(m) word's length
    // Space O(1)
  }
  
  public boolean startsWith(String prefix) {
    TrieNode curNode = root;
    for(int i = 0; i < prefix.length(); i++) {
      int index = prefix.charAt(i) - 'a';
      if (curNode.children[index] == null) {
        return false;
      }
      curNode = curNode.children[index];
    }
    return true;
    // Time O(m) word's length
    // Space O(1)
  }
}
```

![831657680620_.pic](/Users/youhao/Library/Containers/com.tencent.xinWeChat/Data/Library/Application Support/com.tencent.xinWeChat/2.0b4.0.9/5c30e188b46dd764edba02dc2a7d8db0/Message/MessageTemp/9e20f478899dc29eb19741386f9343c8/Image/831657680620_.pic.jpg)





## 380. Insert Delete GetRandom

### 1. Use HashTable and List

```java
class RandomizedSet {
    private HashMap<Integer, Integer> map;
    private List<Integer> res;
    private int index;
    public RandomizedSet() {
        map = new HashMap<>();
        res = new ArrayList<>();
        index = 0;
    }
    
    public boolean insert(int val) {
        if (map.containsKey(val)) {
            return false;
        } else {
            res.add(val);
            map.put(val, res.size() - 1);
            return true;
        }
    }
    
    public boolean remove(int val) {
        if (map.containsKey(val)) {
            int i = map.get(val);
            int lastElement = res.get(res.size() - 1);
            
            // set the specific index's value equals to lastElement
            res.set(i, lastElement);
            // update the lastElement's index
            map.put(lastElement, i);
            // we don't need the lastElement, so we can delete in the map and arrayList
            res.remove(res.size() - 1);
            map.remove(val);
            return true;
        } else {
            return false;
        }
    }
    
    public int getRandom() {
        Random ran = new Random();
        int index = ran.nextInt(res.size());
        return res.get(index);
    }
}

/**
 * Your RandomizedSet object will be instantiated and called as such:
 * RandomizedSet obj = new RandomizedSet();
 * boolean param_1 = obj.insert(val);
 * boolean param_2 = obj.remove(val);
 * int param_3 = obj.getRandom();
 */
```

![911657941530_.pic](/Users/youhao/Library/Containers/com.tencent.xinWeChat/Data/Library/Application Support/com.tencent.xinWeChat/2.0b4.0.9/5c30e188b46dd764edba02dc2a7d8db0/Message/MessageTemp/9e20f478899dc29eb19741386f9343c8/Image/911657941530_.pic.jpg)



## 284. Peeking Iterator

### 1. Saving the next value

```java
// Java Iterator interface reference:
// https://docs.oracle.com/javase/8/docs/api/java/util/Iterator.html

class PeekingIterator implements Iterator<Integer> {
    Iterator<Integer> iter;
    Integer cur;
	public PeekingIterator(Iterator<Integer> iterator) {
	    // initialize any member here.
	    iter = iterator;
        cur = iter.next();
	}
	
    // Returns the next element in the iteration without advancing the iterator.
	public Integer peek() {
        return cur;
	}
	
	// hasNext() and next() should behave the same as in the Iterator interface.
	// Override them if needed.
	@Override
	public Integer next() {
	    Integer temp = cur; 
        if (iter.hasNext()) {
            cur = iter.next();
        } else {
            cur = null;
        }
        return temp;
	}
	
	@Override
	public boolean hasNext() {
	    return (cur != null);
	}
}
```

![image-20220721224644146](/Users/youhao/Library/Application Support/typora-user-images/image-20220721224644146.png)



## 341. Flatten Nested List Iterator

### 1. Recursive Solution

```java
/**
 * // This is the interface that allows for creating nested lists.
 * // You should not implement it, or speculate about its implementation
 * public interface NestedInteger {
 *
 *     // @return true if this NestedInteger holds a single integer, rather than a nested list.
 *     public boolean isInteger();
 *
 *     // @return the single integer that this NestedInteger holds, if it holds a single integer
 *     // Return null if this NestedInteger holds a nested list
 *     public Integer getInteger();
 *
 *     // @return the nested list that this NestedInteger holds, if it holds a nested list
 *     // Return empty list if this NestedInteger holds a single integer
 *     public List<NestedInteger> getList();
 * }
 */
public class NestedIterator implements Iterator<Integer> {
    private List<Integer> res;
    private int length;
    private int index;
    public NestedIterator(List<NestedInteger> nestedList) {
        res = new ArrayList<>();
        flatting(nestedList);
        length = res.size();
        index = 0;
    }
    
    public void flatting(List<NestedInteger> nestedList) {
        for (NestedInteger nestedInteger : nestedList){
            if (nestedInteger.isInteger()) {
            res.add(nestedInteger.getInteger());
            } else {
            flatting(nestedInteger.getList());
            }
        }
    }

    @Override
    public Integer next() {
        int temp = res.get(index);
        index++;
        return temp;
    }


    @Override
    public boolean hasNext() {
        if (index == length) {
            return false;
        } else {
            return true;
        }
    }
}



/**
 * Your NestedIterator object will be instantiated and called as such:
 * NestedIterator i = new NestedIterator(nestedList);
 * while (i.hasNext()) v[f()] = i.next();
 */
```

Time O(N + L): 

For each list within the nested list, there will be one call to flattenList(...). The loop within flattenList(...) will then iterate nn times, where nn is the number of integers within that list. Across all calls to flattenList(...), there will be a total of NN loop iterations. Therefore, the time complexity is the number of lists plus the number of integers, giving us O(N + L)O(N+L).

Space :

The less obvious auxiliary space is the space used by the `flattenList(...)` function. Recall that recursive functions need to keep track of where they're up to by putting stack frames on the runtime stack. Therefore, we need to determine what the maximum number of stack frames there could be at a time is. Each time we encounter a nested list, we call `flattenList(...)` and a stack frame is added. Each time we finish processing a nested list, `flattenList(...)` returns and a stack frame is removed. Therefore, the maximum number of stack frames on the runtime stack is the maximum nesting depth, D*D*.

Because these two operations happen one-after-the-other, and either could be the largest, we add their time complexities together giving a final result of O(N + D)*O*(*N*+*D*).

### 2. Stack

```java
/**
 * // This is the interface that allows for creating nested lists.
 * // You should not implement it, or speculate about its implementation
 * public interface NestedInteger {
 *
 *     // @return true if this NestedInteger holds a single integer, rather than a nested list.
 *     public boolean isInteger();
 *
 *     // @return the single integer that this NestedInteger holds, if it holds a single integer
 *     // Return null if this NestedInteger holds a nested list
 *     public Integer getInteger();
 *
 *     // @return the nested list that this NestedInteger holds, if it holds a nested list
 *     // Return empty list if this NestedInteger holds a single integer
 *     public List<NestedInteger> getList();
 * }
 */
public class NestedIterator implements Iterator<Integer> {
    private Stack<NestedInteger> stack;
    public NestedIterator(List<NestedInteger> nestedList) {
        stack = new Stack<>();
        flatting(nestedList);
    }
    
    public void flatting(List<NestedInteger> nestedList) {
        for (int i = nestedList.size() - 1; i >= 0; i--) {
            stack.push(nestedList.get(i));
        }
    }

    @Override
    public Integer next() {
        if (!hasNext()) {
            return null;
        }
        return stack.pop().getInteger();
    }


    @Override
    public boolean hasNext() {
        while (!stack.isEmpty()) {
            if (stack.peek().isInteger()) {
                return true;
            }
            flatting(stack.pop().getList());
        }
        return false;
    }
}

/**
 * Your NestedIterator object will be instantiated and called as such:
 * NestedIterator i = new NestedIterator(nestedList);
 * while (i.hasNext()) v[f()] = i.next();
 */
```



## 1603. Design Parking System

### 1.Counting

```java
class ParkingSystem {
    private int[] park;
    public ParkingSystem(int big, int medium, int small) {
        park = new int[3];
        park[0] = big;
        park[1] = medium;
        park[2] = small;
    }
    
    public boolean addCar(int carType) {
        if (park[carType - 1] > 0) {
            park[carType - 1]--;
            return true;
        } else {
            return false;
        }
    }
}

/**
 * Your ParkingSystem object will be instantiated and called as such:
 * ParkingSystem obj = new ParkingSystem(big, medium, small);
 * boolean param_1 = obj.addCar(carType);
 */
```

