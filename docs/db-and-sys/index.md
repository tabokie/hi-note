-   [Index Structure used in
    Database](#index-structure-used-in-database)
    -   [B-Tree](#b-tree)
        -   [Overview](#overview)
        -   [Variant](#variant)
            -   [Reduce Tree Height](#reduce-tree-height)
            -   [Handle Duplicate as Index](#handle-duplicate-as-index)
            -   [Concurrency](#concurrency)
    -   [Trie](#trie)
        -   [Overview](#overview-1)
        -   [Normal Variant](#normal-variant)
    -   [Hash Table](#hash-table)
        -   [Overview](#overview-2)
        -   [Normal Variant](#normal-variant-1)

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

#### Handle Duplicate as Index

#### Concurrency

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
    -   rehash； copy-all
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
