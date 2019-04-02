-   [Buffering in Database](#buffering-in-database)
    -   [Cache Replacement](#cache-replacement)
        -   [LRU](#lru)
        -   [LFU](#lfu)
        -   [Hybrid](#hybrid)

Buffering in Database
=====================

Cache Replacement
-----------------

### LRU

### LFU

### Hybrid

-   ARC
    -   feature
        -   balance recency and frequency, empirically universal
            optimization policy
        -   scan-resistent
    -   idea
        -   maintain list of one-hit and list of multi-hit
        -   adopt dynamic slide window of two list
        -   cache directory = cache item \* 2
        -   a hit in list but miss in item will trigger a window slide
            -   compensation for the miss
