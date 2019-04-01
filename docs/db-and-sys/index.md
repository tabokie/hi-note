-   [Index Structure used in
    Database](#index-structure-used-in-database)
    -   [B-Tree](#b-tree)
        -   [Overview](#overview)
        -   [Variant](#variant)
            -   [Reduce Tree Height](#reduce-tree-height)
            -   [Concurrency](#concurrency)
        -   [Mics](#mics)
    -   [BitMap](#bitmap)
        -   [Overview](#overview-1)
        -   [Design](#design)
    -   [Trie](#trie)
        -   [Overview](#overview-2)
        -   [Normal Variant](#normal-variant)
    -   [Hash Table](#hash-table)
        -   [Overview](#overview-3)
        -   [Normal Variant](#normal-variant-1)
    -   [SkipList](#skiplist)
        -   [Overview](#overview-4)
    -   [Hybrid Structure](#hybrid-structure)
        -   [Mass Tree](#mass-tree)
        -   [Trie Hashing](#trie-hashing)
        -   [Hash Trie (original)](#hash-trie-original)

Index Structure used in Database
================================

SIMD, GPU, SSD/HDD

B-Tree
------

### Overview

-   merit
    -   balanced and logarithmic height
    -   large node size
-   demerit
    -   binary search
    -   random mutation cause random disk IO
-   usage
    -   clustering or non-clustering index of physical records

### Variant

#### Reduce Tree Height

-   B\*-tree, aka B+-tree
    -   record only stored in leaf nodes
    -   increase the minimal hit height
-   prefix b-tree
    -   reference: TODS-1977-Prefix-B-Trees
    -   idea: find shortest seperator to store in nonleaf nodes,
        lowering the tree height so that page IO is minimized, aka
        **tail compression**
    -   more: prefix compression, aka **head compression**
        -   deduce branch prefix from bounds
        -   remove from key
        -   search and continue
    -   challenge
        -   unfair partition cause poor page utilization
        -   variable-length key cause poor binary search performance
    -   improvement on variable-length binary search
        -   slide search
        -   partition by length
        -   key prefix
            -   reference: SIGMOID-2001-Evolution-of-B-Trees
            -   idea: factoring variable-length key to fixed-length

#### Concurrency

-   B-Link
    -   drawback:
        -   lock
        -   atomic memory
        -   poor locality, sequential insert
-   Bw-Tree
    -   elastic virtual page
        -   central PID Mapper
        -   delta record link prepended to base page
        -   benifit
            -   abstraction layer handle updates
            -   no in-place update, cache friendly
            -   atomic operation on physical data
    -   page consolidation: whaaat?
        -   allocate and populate and CAS, no retry
    -   node split
        -   new page and slide link point to old R
        -   split record on L, update slide link and range, redirect
            subsequent read to R
        -   update parent
    -   node merge
        -   delete record on R, redirect subsequent read to left sibling
            -   how?? no left slide link
        -   merge record on L, redirect partial read to R memory chunk
        -   update parent and recycle R's PID
    -   epoch-based gc
    -   incremental flush with flush-delta-record
-   Palm-Tree
    -   procedure
        -   partition
        -   readonly search
        -   synchronize
        -   resolve hazards
        -   modify (in exclusive mode)
        -   ascend and repeat (if mutation needed)
    -   presort and point-to-point sync
        -   use pre-sort to increase cache utilization
        -   use pre-sort to ensure thread only collide with neighboring
            threads

### Mics

-   Duplicate Handling
    -   list of record
    -   key + list of value
    -   epoch-based key + list of value
    -   prefix compression of value
    -   bitmap index of value
        -   base + bitmap as offset
-   Update Buffering
    -   in-node buffering
        -   redundant space and less fanout
    -   partitioned b-tree
        -   internal leading key to divide record
        -   main partition and recent updates
    -   anti-record and lazy delete

BitMap
------

### Overview

-   tradeoff (space-time)
    -   bit-wise operation VS compression (run-length WAH)
    -   multiple-index for fast query VS update cost and space
    -   update cost VS compression
-   usage
    -   Value-List index
        -   by row: this record has value of k (k-th is set)
        -   by column: the records that has value of k (k-th col)

### Design

-   decomposition of attribute value (0..C-1)
    -   basic: value = position of 1
        -   C bits per record
    -   decomposition: series of base ({10,10...})
        -   reduce bitmap size
        -   need bitwise preprocess to fecth records
-   encoding scheme
    -   equality encoding
    -   range encoding
        -   all bits \>= value is set to 1
        -   highest bit is gauranteed to be 1, not needed
        -   fast for range query, two bitmaps needed for equality query

Trie
----

### Overview

-   merit
    -   insertion
    -   comparison overhead
    -   nack
    -   cache friendly
    -   prefix and range query
-   demerit
    -   child index is memory-consuming
-   optimization
    -   merge node, suffix compact
    -   split char encoding (unicode to binary)
    -   linked list of child pointer
    -   hash table of child pointer

### Normal Variant

-   double array trie (DAT)
    -   state machine
    -   `base[state + input]`, `check[state]`
    -   cedar improvement by indexing free base
        [link](http://www.tkl.iis.u-tokyo.ac.jp/~ynaga/cedar/)
-   suffix trie and suffix tree
    -   trie with single-link compression
-   patricia tree and crit-bit tree
    -   single-link compression: any internal node has at least two
        children
    -   merkle patricia tree: merkle tree with patricia tree
    -   recursive patricia trie
-   ternary search tries
    -   three children: starts with smaller, starts with, starts with
        bigger
-   adaptive radix tree: Node4, Noed16, Node48, Node256
-   fm-index
-   FUDS trie
-   louds trie

Hash Table
----------

### Overview

-   merit
    -   bounded memory cost
-   demerit
    -   hash collision and rehash
    -   not support in-order traverse
    -   poor cache locality
-   hash function
-   hash collision
    -   open addressing
        -   linear probing
            -   cache friendly
        -   double hashing
    -   seperate chaining
        -   linked-list or search tree
    -   two-way chaining
        -   two hash table with double hashing
        -   cuckoo hash
-   dynamic size adjustment
    -   rehash; copy-all
    -   linear hashing
    -   spiral storage
    -   extensible hashing
-   optimization
    -   set-associative: multiple bucket for one slot

### Normal Variant

-   cuckoo hash
    -   calculate two hash slots in two tables to pick
    -   squeeze old if needed, repeat same procedure
    -   rehash when sequeeze too many time
-   perfect hash for static data set
-   rank select: compress sparse hash table
    -   rank: number of valid slot before

SkipList
--------

### Overview

-   merit
    -   no mutation leads to quick insertion
    -   balanced structure
    -   easy to traverse
    -   good performance in persistent storage ?
    -   lockfree-easy
-   challenge: cache miss
    -   unroll list
        -   vertically convert to fixed-len array
        -   restrict tree height to avoid shortcut list resize
        -   horizontally stored in fixed-len chunk

Hybrid Structure
----------------

### Mass Tree

-   overview
    -   trie of B+Tree, each trie node is a B+Tree
    -   each subtree index 8 bytes
    -   border nodes return value or child trie node

### Trie Hashing

-   overview
    -   trie of HashTable, each leaf node is a HashTable
    -   non-leaf node has seperator field { char, rank }, acting as both
        binary seperator and trie seperator

### Hash Trie (original)

-   overview
    -   trie of HashTable, each node is a HashTable
    -   standard trie index, unspawned branch is virtualized as linked
        list of hash slot
