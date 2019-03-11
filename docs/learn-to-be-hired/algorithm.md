Algorithm
---------

### Strategy

-   practice
    -   with speed and amount
    -   whiteboard: plan-ahead, testcase design
    -   verbal: abstracting idea
    -   mock interview

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
-   Union and Intersection
-   Data Container
    -   fifo

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
