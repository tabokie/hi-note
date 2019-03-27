-   [Database](#database)
    -   [Data Structure](#data-structure)
        -   [Index Structure](#index-structure)
            -   [Trie](#trie)
            -   [Hash Table](#hash-table)
-   [Distributed System](#distributed-system)

Database
========

Data Structure
--------------

### Index Structure

SIMD, GPU, SSD/HDD

#### Trie

-   Overview
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

#### Hash Table

-   Overview
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
-   cuckoo hash
    -   calculate two hash slots in two tables to pick
    -   squeeze old if needed, repeat same procedure
    -   rehash when sequeeze too many time
-   perfect hash for static data set
-   rank select: compress sparse hash table
    -   rank: number of valid slot before

Distributed System
==================
