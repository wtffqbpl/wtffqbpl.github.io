---
title: CUB Architecture Overview
toc: true
date: 2022-12-12 23:37:08
cover:
excerpt:
tags:
  - CUDA
  - CUB
  - Thrust
---

# Introduction

Thrust --- Host-side interface, no kernels.
CUB --- CUDA Unbound (C_uda U_n_B_und)
software reuse in CUDA Kernels.

Compile-time binding with template, typing high performance. So stiching these kernels together, you can get high performance. And finally, because CUB doesn't dictate things like thread block size or shared memory usage, it allows you to experiment with these values as you optimize or auto-tune your code. And one thing you'll find if you get into optimizing CUDA code is that this kind of auto-tuning where you experiment with or even automatically sweep the parameters of your code, things like the block size and the shared memory size. That's a really powerful tool for extracting the maximum amount of performance from your code without investing a lot of personal effort.

CUB is a generic, configurable C++ template library for high performance CUDA primitives. It targets each layer of the CUDA programming model.

* reusable software components are:
  * Parallel primitives:
    * Warp-wide "collective" primitives
    * Block-wide "collective" primitives
    * Device-wide primitives
  * Utilities:
    * Fancy iterators
    * Thread and thread block I/O
    * PTX intrinsics
    * Device, kernel, and storage management

CUB is CUDA C++ specific, it allows the programmers to intefere CUDA specific details (the number of threads, cudaStream_t parameters etc.). So the primitives can be configured for a specific GPU architecture.

# Iterators

## cub::ArgIndexInputIterator<InputIterator, OffsetT, OutputValueT>

```cpp
template <
    typename InputIteratorT,
    typename OffsetT = ptrdiff_t,
    typename OutputValueT = cub::detail::value_t<InputIteratorT>>
class cub::ArgIndexInputIterator<InputIteratorT, OffsetT, OutputValueT> {
  
};
```




# References

- [CUB Introduction](https://indico.truba.gov.tr/event/84/attachments/173/379/Day%202-%20Session_3.pdf)
- [Introducing CUDA UnBound (CUB)](https://www.microway.com/hpc-tech-tips/introducing-cuda-unbound-cub/)