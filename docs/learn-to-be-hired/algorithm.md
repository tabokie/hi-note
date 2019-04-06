-   [Algorithm](#algorithm)
    -   [Strategy](#strategy)
    -   [Basic Data Structure](#basic-data-structure)
    -   [Basic Algorithm](#basic-algorithm)
    -   [Advanced Algorithm](#advanced-algorithm)
        -   [Data Structure](#data-structure)
        -   [Dynamic Programming](#dynamic-programming)
    -   [Probablistic and Randomness](#probablistic-and-randomness)
    -   [BigData and Online Algorithm](#bigdata-and-online-algorithm)
    -   [Corner Case](#corner-case)
    -   [Bugs](#bugs)
    -   [Optimization](#optimization)
    -   [Test Case](#test-case)
    -   [Misc Insights](#misc-insights)

Algorithm
---------

### Strategy

-   practice
    -   with speed and amount
    -   whiteboard: plan-ahead, testcase design
    -   verbal: abstracting idea
    -   mock interview

### Coding

#### C++

-   basic structure

```c++
// list
std::vector<int> v(count, 0);
v.assign(count, 0);
// stack
std::stack<int> s;
s.push(0);
s.pop();
// fifo
std::queue<int> q;
q.push(0);
q.pop();

```

-   linked list

```c++
std::list<int> l;
l.push_back(0);l.pop_back();
l.push_front(0);l.pop_front();
l.front();l.back();
l.remove(val);
l.insert(index, val);
```

-   hash map

```c++
std::unordered_map<string, int> map;
map.insert(make_pair<string, int>("a", 0));
map["a"]  = 0;
::iterator ret = map.find("a");
return ret->first;
```

-   tree

```c++
// no duplicate
// rbtree
map<key, value> tree;
map.begin();
map.rbegin();
map.find();
// heap
priority_queue<pair, container<pair>, greater<pair>> queue; // cmp = leaf cmp root
queue.push(pair);
queue.pop();
queue.top();
```

-   algorithm

```c++
next_permutation(begin(), end());
prev_permutation(begin(), end());
sort(begin(), end(), func);
unique(begin(), end()); // make unique
lower_bound(begin(), end(), value); // value as lower_bound, value <= first
```

#### Python

-   list

```python
l = [1,2,3]
l[0::-1] # l.reverse # inplace reverse
l.append(value) # pop(index = -1)
l.insert(0,first)
l.remove(value)
l.count(value)
l.sort(key=None, reverse=False)
if 1 in l:
    return True
```

-   heap

```python
import * from heapq;
data = [1,2]
heapify(data)
heappush(data, 1)
print heappop(data)
```

-   map

```python
mp = {'b', 2}
mp['a'] = 2
del mp['a']
mp.clear()
mp.get('a', default=None)
list(mp.keys()) # values
```

-   set

```python
st = {value} # not {}
st = set((value0, value1)) # set(), set(value)
st.add(value)
st.update({value0, value1})
st.remove(x) # safe: st.discard(x)
```

-   misc

```python
map(function, [1,2,3])
map(lambda x,y: x+y, list_1, list_2)
list(filter(lambda x: True if x>0 else False, range(100)))
```

### Basic Data Structure

-   Linked List
    -   intersection
    -   cycle
        -   marker: use data field or pointer field
        -   slow pointer and fast pointer
        -   stack / recursive
    -   sort linked list
        -   quick sort: partition into two list
        -   merge sort: no need for auxiliary array
-   Hash Table
    -   [collision](http://www.ruanyifeng.com/blog/2018/09/hash-collision-and-birthday-attack.html)

### Basic Algorithm

-   Permutation and Subset O(2\^n)
    -   recursive DFS

    ``` {.python}
    def subset1(self,subset,res,index,nums): 
        if index == len(nums):
            res.append(subset[:]) 
            return 
        subset.append(nums[index]) 
        self.subset1(subset,res,index+1,nums) # 这种情况就是用第idx个数字
        subset.pop(-1) 
        self.subset1(subset,res,index+1,nums) # 这种情况就是不用idx
    ```

    -   divide and conquer

    ``` {.python}
    def subset2(self,subset,res,index,nums): 
        res.append(subset[:]) 
        for i in range(index,len(nums)): 
            subset.append(nums) 
            self.subset2(subset,res,i+1,nums) 
            subset.pop(-1)
    ```

    -   iterative BFS

    ``` {.python}
    def subset3(self,res,nums): 
        stack = [] 
        stack.append([]) 
        while stack: 
            temp = stack.pop()[:]
            res.append(temp) 
            for i in range(len(nums)): 
                if not temp or temp[-1] < nums: 
                    #相当于没有用temp[-1]到 第i-1个数字
                    subset = temp[:] 
                    temp.append(nums) 
                    stack.append(subset) 
        return res 
    ```

-   QuickSort and Partition O(nlogn)
    -   K-th Element in an Array
-   MergeSort and Merge
    -   K-merge
    -   interval-merge
-   Dual Pointer
    -   merge-sort parallel pointer
    -   quick-sort opposite pointer
    -   scan-line 扫描线 p-p
    -   单调栈 p-p
    -   linked-list intersection !!
-   Divide-and-Conquer and Traverse
    -   divide-and-conquer: f = \|\| f(a) + f(b)
    -   traverse: g(res), f(res), res
-   Binary Search
-   BFS
    -   basic form

    ``` {.python}
    def BFS(self, graph, start)
        ans = []
        q = []
        visited = {}

        q.append(start)
        visited[start] = True  

        while q:
            temp = q.pop(0)
            ans.append(temp)
            for n in temp.neighbors:
                if n not in visited:
                    q.append(n)
                    visited[n] = True
        return ans
    ```

    -   level-by-level

    ``` {.python}
    def BFS(self, graph, start)
        ans = []
        q = []
        visited = {}

        q.append(start)
        visited[start] = True  

        while q:
            size = len(q)
            for i in range(size):
                temp = q.pop(0)
                ans.append(temp)
                for n in temp.neighbors:
                    if n not in visited:
                        q.append(n)
                        visited[n] = True
        return ans
    ```

    -   topological sort

    ``` {.python}
    def topSortBFS(self, graph):
        indegree = {}
        ans = []
        for g in graph:
            for n in g.neighbors:
                if n not in indegree:
                    indegree[n] = 1
                else:
                    indegree[n] += 1

        q = []
        for g in graph:
            if g not in indegree:
                q.append(g)

        while q:
            temp = q.pop(0)
            ans.append(temp)
            for n in temp.neighbors:
                indegree[n] -= 1
                if indegree[n] == 0:
                    q.append(n)
        return ans
    ```

### Advanced Algorithm

#### Data Structure

-   Indexing
    -   prefix
    -   diff
    -   length
-   Tree
    -   Morris Traversal: T(n) S(1)
        -   use leaf pointer as precursor storage
        -   inorder:
            -   find leftMax
            -   if pointed to `cur`, restore
            -   else point to `cur` and descend
    -   Height-balanced Tree
-   Use Case
    -   LRU / LFU
        -   random sampling
            -   see [`Redis`](./infra.md#redis)
        -   doubly-linked list
        -   lru-k
            -   history queue and priority queue
        -   2Q
            -   fifo (first access) and lru (multiple access)
            -   remove fifo if fifo is full, else remove lru tail
        -   LIRS
        -   Oracle LRU [blog](https://www.cnblogs.com/cyjb/archive/2012/11/16/LruCache.html)
            -   hot queue and cold queue
                -   put: add to hot front if empty, evict then add to cold front if full
                -   get: maintain reference count
                -   evict: traverse from cold tail, reset and move to hot front if multiple access (hot end became cold)
            -   use circular queue to implement
        -   segmented hash-table and linked-list
            -   Java ConcurrentHashMap
        -   timestamped hash-table
            -   lazy eviction: possibility of OOM
            -   batch eviction: halt the put

#### Graph

-   Minimal Spanning Tree
    -   traverse edge by length, add if no circle
    -   union-set
-   Shortest Path
    -   Dijkstra
        -   link to nearest point
        -   update all neighbors
    -   Bellman-Ford
        -   update n-1 times
-   Topological Sort
    -   use in-degree = 0 to select first node

#### Dynamic Programming

-   Overview
    -   Dynamic Programming = State + State-Transition
    -   Dimension = discrete amount
    -   Augmented Dimension = dual pivot / set
    -   State Transition = Divide-and-conquer / Topology
-   Property
    -   `min`, `max`
    -   `set`

### Probablistic and Randomness

-   Shuffle
    -   random pick from remainder
    -   (online) switch new-comer with insider
    -   (online pick-k) keep first K, for i \> K:
        `x=random(1,i); if x < K: arr[x] = new`
-   Sample
    -   starting point: integer uniform generator
        -   xn+1 = (a \* xn + b) % m

        <!-- -->

            linear congruential method: 线性同余法
            complete cycle (output 0..m before repeat):
            1. b,m inter-prime
            2. m prime factors' product % (a-1) == 0
            3. if M%4==0 then (a-1)%4==0
            4. a,b,x0 < m
            5. a,b > 0

    -   Uniform Sample
        -   rejection sampling
            -   uniform sample in domain area
            -   repeat until accepted
        -   inverse sampling
            -   intersect Cumulative Distribution Function
            -   inverse function
        -   e.g. Circle
    -   Scaler
        -   composing: `rand25 = rand5 * 5 + rand5`
        -   module: `rand21 = reject(rand25)`
        -   rejection: `rand3 = rand21 % 7`

### BigData and Online Algorithm

-   Partition Data
    -   natural order
        -   need lut
    -   hash
        -   balancing
    -   logical order
        -   locality but also need lut
-   Repetition
    -   HashSet with partitioned hash
    -   BitSet
    -   Bloom Filter
    -   Trie
-   Top-K
    -   numeric
        -   Heap
    -   occurance
        -   visit by hash then top-k-value
        -   N Hash Table, Freq = min(value from all tables) \>= accurate
            Freq
        -   Trie
-   Statistic
    -   Median
        -   two-heap
-   Union and Intersection <TODO> - hash on small set, traverse big set
-   Data Container
    -   fifo
-   Sort
    -   mergesort

### Corner Case

-   `uint32_t`
-   decimal carry

### Bugs

-   grammar
    -   `>>`
    -   `a + (b) ? x : y`
-   structure
    -   for linked-list, use branch
-   memory
    -   iterating while modifying
    -   modifying while referencing
        -   `back = v.back(); v.push_back(0);`

### Optimization

-   `unordered_map`
-   modular space

### Test Case

-   integer sequence
    -   all zero
    -   min amount of set
    -   negative
    -   overflow

### High-Level Insights

-   generic optimization approach
    -   HashMap: space for O(n) time
    -   In-Place: O(1) space
-   generic solution approach
    -   reinterpret your target
        -   e.g.
            -   等差数列 = `2 * b = a + c`
            -   sequence sum of 0 = two same prefix sum
-   divide-and-conquer
-   bucket / modular space
    -   solve sub-problem in limited scope
-   dual pivot
-   dynamic programming
    -   use naive recursive as fallback solution
-   inverse problem / sieve method
    -   e.g.
        -   prime number

### Unsorted

-   classics:
    -   min in interval
        -   segment and two pass
        -   monotonic queue with fixed length
    -   lru, lfu
    -   query interval
    -   interval intersection
        -   scan line
            -   by 1 step
            -   by interval end point
        -   with interval queryer
-   in-place sort
-   stable sort with O(nlogn): binary search tree
    -   (preserve relative order)
    -   bubble sort (n2)
    -   insert sort (n2)
    -   merge sort
    -   radix sort
-   k-th or median of fixed-size array
    -   partition of quicksort
    -   two heap in logN and 1
    -   binary search tree in both logN
-   big data median with unknown size
    -   accurate implementation
        -   two heap with unlimited size
            -   filter in opposite heap then pop and insert
            -   swap if confict the partition rule
            -   `priority_queue<int, vector<int>, std::smaller<int>>`: big heap
        -   heaps of heap
        -   binary tree, VAL tree
-   bitmask
    -   Extract lowest set bit: `s & (-s)`
    -   Extract lowest unset bit: `~s & (s + 1)`
-   optimize under parallel workloads
    -   分流
    -   维护中间状态一致性 以获得无锁并发