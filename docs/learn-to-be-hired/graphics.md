Graphics
--------

### Rendering

-   Rasterization Pipeline
    -   forward shading: vertex + fragment shading
        -   forward+ shading: vertex -\> tiled Z-buffer -\> light
            clipping + fragment shading
        -   complexity = Geometry \* Light
    -   deferred shading: vertex -\> G-buffer (depth, normal, albedo)
        -\> shading
        -   complexity = Geometry + Light
            -   clipping (could be done with Z-pre-pass in Forward
                Rendering)
            -   dimension mapping
        -   tile-based deferred shading: vertex -\> tiled G-buffer -\>
            light clipping -\> shading
        -   deferred lighting: use lightweight G-buffer to calculate
            light buffer, then forward pass
            -   allow complex material (in forward pass)
        -   features
            -   memory-inefficient
            -   not support alpha
            -   not support hardware AA
    -   inferred lighting
-   Sample and Anti-Aliasing
    -   SSAA: target scaling
    -   MSAA: multiple sample at fragment shading (hardware AA)
        -   color = sample-hit-rate \* vertex color
    -   FXAA: post-process, vision tech
    -   MFAA: inter-frame
    -   TAA: temporal super sampling
        -   projection matrix jittering
    -   DLSS: RNN
-   Physically-based Render
    -   Geometry Intersection
        -   GPU optimization
    -   Monte-Carlo
        -   importance sampling
            -   light sampling
            -   BSDF sampling
            -   multiple importance sampling
        -   bidirectional ray tracing
-   Coordination
    -   two sets of U-V coordinates
    -   tangent / normal space
        -   tangent space rather than model space
        -   texture reuse
        -   compact normal texture (z \> 0)

### Geometry

-   Triangle
-   Collision Detection
    -   8-ary tree
-   Player Clipping

### High-Level Algorithm

-   Probablistic: see Algorithm
-   Generative ALgorithm
    -   NPC AI
    -   Maze Generate
    -   Path-Finding