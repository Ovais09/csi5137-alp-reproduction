# CSI5137 — ALP Experiment Reproduction

This repository is for Project 2 in Advanced Topics in Data Management. We will reproduce one experiment from ALP, a paper reviewed in our Project 1 report.

**Current status:** The paper and experiment have been selected. The benchmark has not been built or run yet, and no reproduction results are available.

## 1. Selected paper

**ALP: Adaptive Lossless Floating-Point Compression**

Azim Afroozeh, Leonardo Kuffó, and Peter Boncz. *Proceedings of the ACM on Management of Data*, volume 1, issue 4, article 230, 2023. Presented at SIGMOD 2024.

- [Paper DOI](https://doi.org/10.1145/3626717)
- [Authors' PDF](https://ir.cwi.nl/pub/33334/33334.pdf)
- [Official code repository](https://github.com/cwida/ALP)

ALP compresses floating-point values while preserving their exact original values. We selected it because it was part of our Project 1 review, the authors provide code, and the chosen experiment uses small synthetic inputs.

## 2. Graph to reproduce

Our target is the **lower graph of Figure 5**, discussed under **Kernel Fusion in Section 4.2** and shown on page 11 of the authors' PDF.

The graph compares two decoding methods:

- **Fused decoding:** integer unpacking and reconstruction of floating-point values happen in one routine.
- **Non-fused decoding:** the same steps run as separate routines.

We will reproduce both curves across all **53 bit widths, from 0 to 52**, using vectors of **1,024 values**. The original horizontal axis is vector bit width, and the vertical axis is decoding speed in values per CPU cycle.

The question is whether combining the decoding steps improves performance, and how that benefit changes with bit width. We will compare our measured curves and speed ratios with the published graph, including any cases where fusion does not improve performance.

## 3. Code and experimental setup

We will start from the [authors' experiment artifact at this pinned revision](https://github.com/cwida/ALP/tree/df2f8fcdb61fc995fcb3fec8303cfb80f01a7eda), so the code version used for the reproduction is clear.

The planned tools are **C++ and CMake** for the benchmark and **Python** for analysis and plotting. Our currently available machine is an **Apple M5 Mac with 16 GB of memory**. The original graph used an Intel Ice Lake processor, so we will need to adapt the benchmark to the artifact's ARM or portable routines.

Before collecting measurements, we will:

- Build the relevant decoding routines and check that both methods recover the expected values exactly.
- Investigate the input-buffer issue identified in the supplied benchmark driver and record any correction.
- Verify that the timing method measures what we claim it measures.
- Record the operating system, compiler version, build options, code revision, parameters, and changes.

CPU-cycle measurement on this Mac still needs to be verified. If reliable cycle measurements are unavailable, we will report elapsed-time throughput and relative speedup, and clearly describe the changed measurement as a partial reproduction. We will not estimate CPU cycles from a nominal clock frequency.

## 4. Data

We will use the authors' existing [synthetic inputs in `data/generated`](https://github.com/cwida/ALP/tree/df2f8fcdb61fc995fcb3fec8303cfb80f01a7eda/data/generated):

```text
generated_doubles_bw0.csv
...
generated_doubles_bw52.csv
```

Each file contains 1,024 values for one target bit width. These inputs let us vary bit width while keeping vector length fixed, which isolates the effect studied in the lower graph of Figure 5. The 30 real datasets used elsewhere in the paper are not needed for this target.

We will preserve the original inputs and their ordering, check the file counts and vector lengths, and verify the encoded bit widths before benchmarking. Any preprocessing, replacement data, or changes to the inputs will be documented and justified in the report.
