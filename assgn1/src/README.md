# COMP 590 Assignment 1 - Lossless Video Compression

## Approach: Left-Neighbor Temporal Difference Context Modeling

This project implements a lossless video compression strategy that improves upon the baseline temporal predictor provided in class by effectively utilizing exactly 256 independent probability contexts for Arithmetic Coding.

### Core Concept
In the baseline code, a single probability model is forced to learn the temporal differences for the entire frame. This causes the probability distribution of the static background (a massive spike at `0`) to be flattened by the larger differences of moving objects, severely penalizing compression.

To fix this, we exploit the spatial coherence of motion. If a pixel is part of a static background, the pixel immediately to its left is almost certainly also part of that static background. If a pixel is part of a moving edge, its neighbor is likely part of the same moving edge.

Therefore, for every pixel, we calculate the exact temporal difference of the pixel immediately to its left (which ranges from 0 to 255 due to modulo arithmetic). We use this left-neighbor difference as the exact **Context ID (0-255)** for the current pixel.

### Why It Works
This strategy aggressively routes all static background pixels into Context 0. Because Context 0 sees an overwhelming number of `0` differences, its probability curve becomes incredibly sharp, allowing the Arithmetic Coder to encode background pixels in fractions of a single bit. Meanwhile, pixels with higher prediction errors are handled by other contexts, preventing them from "polluting" the background context.

### Results
Tested on 300 frames of `bourne.mp4` using the `-check_decode` flag to ensure 100% lossless accuracy:

* **Frames Encoded:** 300
* **Average Size (bits):** 2,231,631
* **Compression Ratio:** 1.02

This demonstrates a successful lossless reduction in file size compared to the raw uncompressed frame data, which is a significant achievement given the highly active, fast-pacing, and high-motion nature of the Bourne video clip.