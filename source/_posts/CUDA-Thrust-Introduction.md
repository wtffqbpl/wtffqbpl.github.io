---
title: CUDA Thrust Introduction
toc: true
date: 2022-11-22 15:14:42
cover: /img/cuda/cudathrust.jpeg
excerpt: The introduction about CUDA Thrust.
tags:
  - CUDA
  - Thrust
---

Thrust is analogous to C++ Standard Template Library(STL). The Thrust algorithms can run on both the host and the device.

Sort, scan, reduce, reduce-by-key
Transform input vector(s)
Eg. vector addition thrust::plus
Or you can apply user defined transformation functors.
interoperate with CUDA code.

If you don't need to do something that is not built in to `Thrust` or that's difficult to do with `Thrust`

```cpp
thrust::device_vector<float> X(3);
float result = thrust::reduce(X.begin(), X.end());
// or
float init = 0.f;
// We can pass in an operator to use or a functor.
result = thrust::reduce(X.begin(), X.end(), init, thrust::maximum<float>());
```

## Some Algorithms

These algorithms are existed in thrust, but do not exist in C++ STL library.

```cpp
thrust::gather();
thrust::gather_if();
thrust::scatter();
thrust::scatter_if();
thrust::sequence();
thrust::stable_partition_copy();
thrust::tabulate();
thrust::transform_if();
```

### thrust::sequence()

The equivalent of algorithm in STL is `std::iota`, which is not a good name. `Thrust` changes this name to `sequence`. 

Some algorithms in `<numeric>` header. There are 12 by-key-algorithms in thrust. These 12 algorithms are super useful. You can think of them as segmented versions of their corresponding algorithms. So if you have an inclusive scan by key. This is exactly like an inclusive scan but instead of doing it on a whole range you are doing it on segments of a range, which can come in handy in many cases.
```cpp
thrust::exclusive_scan_by_key();
thrust::inclusive_scan_by_key();
thrust::merge_by_key();
thrust::reduce_by_key();
thrust::set_difference_by_key();
thrust::set_intersection_by_key();
thrust::set_symmetric_difference_by_key();
thrust::set_union_by_key();
thrust::sort_by_key();
thrust::stable_sort_by_key();
thrust::unique_by_key();
thrust::unique_by_key_copy();
```

There are three examples that shows how to use these algorithms.

## Problem 1
* In C++ STL view.
> What is `std::iota` + `std::transform`?

* In Thrust view:
> What is `thrust::sequence` + `thrust::transform`?

These two algorithms combined to make a single algorithm: `thrust::tabulate`. This is very interesting algorithm. Generate the first odd number. For example, generate first 10 odd numbers.

* In C++
```cpp
auto odds = std::vector<int>(10);

std::iota(odds.begin(), odds.end(), 0);
std::transform(
    odds.begin(),
    odds.end(),
    odds.begin(),
    [](auto e) { return e * 2 + 1; });
```

* In CUDA With Thrust
```cpp
auto odds = std::vector<int>(10);
thrust::tabulate(
    odds.begin(), odds.end(),
    [](auto e) { return e * 2 + 1; });
```
This code is running on the host. And this is very easy to transform this code to device.

```cpp
auto odds = std::device_vector<int>(10);
thrust::tabulate(
    odds.begin(), odds.end(),
    [] __device__ (auto e) { return e * 2 + 1; });
```

## Problem 2
> Copy every other number.

The answer is `thrust::gather()`.

```cpp
auto const deck       = std::vector<int>{13, 2, 14, 3, 6, 7};
auto const gather_map = std::vector<int>{0, 2, 4};
auto       hand       = std::vector<int>(3);

// deals every second card to hand
thrust::gather(
    gather_map.cbegin(), // The first two iterators define the gather map.
    gather_map.cend(),
    deck.cbegin(), // defines source that we are gathering from
    hand.begin()); // defines the target that we are gathering to.
// The final results: hand = {13, 14, 6};
```

## Problem 3 --- MCO
> Maximum Consecutive Ones

Using `thrust::reduce_by_key()` for solve this problem.

```cpp
// 1 1 1 0 0 1 0 1 1 1 1
// For example, calculates the longest contiguous sequence of ones. In this example, the answer is 4.
auto const ones = std::vector<int>{1, 1, 1, 0, 1, 1, 1, 1};
auto sums = std::vector<int>(3);

thrust::reduce_by_key(
    ones.cbegin(),                    // keys input
    ones.cend(),
    ones.cbegin(),                    // values input
    thrust::make_discard_iterator(),  // keys output
    sums.begin());                    // values output

auto const max = *thrust::max_element(
    sums.cbegin(), sums.cend());
```

