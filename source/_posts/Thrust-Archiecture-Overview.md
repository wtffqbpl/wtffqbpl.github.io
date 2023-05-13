---
title: Thrust Archiecture Overview
toc: true
date: 2022-12-06 23:16:26
cover: /img/cuda/cudathrust.jpeg
excerpt: The CUDA Architecture Analysis
tags:
  - CUDA
  - Thrust
  - CUB
---

# Introduction

TODO

Thrust is not CUDA specific, it does not allow the programmers to interfere CUDA specific details (the number of threads, cudaStream_t parameters etc.). Because of that, the primitives can not be configured for a specific GPU architecture.

## Overview
TODO

## Architecture

TODO

# APIs

TODO

## Containers

TODO

## Algorithms

TODO

### Copying

#### Gathering

[Reference Page](https://nvidia.github.io/thrust/api/groups/group__gathering.html)

##### thrust::gather

```cpp
template <typename DerivedPolicy,
  typename InputIterator,
  typename RandomAccessIterator,
  typename OutputIterator>
__host__ __device__ OutputIterator
thrust::gather(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator map_first,
  InputIterator map_last,
  RandomAccessIterator input_first,
  OutputIterator result);

template <typename InputIterator,
  typename RandomAccessIterator,
  typename OutputIterator>
OutputIterator
thrust::gather(InputIterator map_first,
  InputIterator map_last,
  RandomAccessIterator input_first,
  OutputIterator result);
```

##### thrust::gather_if

```cpp
template <typename DerivedPolicy,
  typename InputIterator,
  typename RandomAccessIterator,
  typename OutputIterator>
__host__ __device__ OutputIterator
thrust::gather(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator map_first,
  InputIterator map_last,
  RandomAccessIterator input_first,
  OutputIterator result);

template <typename InputIterator,
  typename RandomAccessIterator,
  typename OutputIterator>
OutputIterator
thrust::gather(InputIterator map_first,
  InputIterator map_last,
  RandomAccessIterator input_first,
  OutputIterator result);

template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename RandomAccessIterator,
  typename OutputIterator>
__host__ __device__ OutputIterator
thrust::gather_if(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 map_first,
  InputIterator1 map_last,
  InputIterator2 stencil,
  RandomAccessIterator input_first,
  OutputIterator result);

template <typename InputIterator1,
  typename InputIterator2,
  typename RandomAccessIterator,
  typename OutputIterator>
OutputIterator
thrust::gather_if(InputIterator1 map_first,
  InputIterator1 map_last,
  InputIterator2 stencil,
  RandomAccessIterator input_first,
  OutputIterator result);

template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename RandomAccessIterator,
  typename OutputIterator,
  typename Predicate>
__host__ __device__ OutputIterator
thrust::gather_if(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 map_first,
  InputIterator1 map_last,
  InputIterator2 stencil,
  RandomAccessIterator input_first,
  OutputIterator result,
  Predicate pred);

template <typename InputIterator1,
  typename InputIterator2,
  typename RandomAccessIterator,
  typename OutputIterator,
  typename Predicate>
OutputIterator
thrust::gather_if(InputIterator1 map_first,
  InputIterator1 map_last,
  InputIterator2 stencil,
  RandomAccessIterator input_first,
  OutputIterator result,
  Predicate pred);
```

#### Scattering

[Reference Page](https://nvidia.github.io/thrust/api/groups/group__scattering.html)

##### thrust::scatter

`thrust::scatter` copies elements from a source range into an output array according to a map. For each iterator `i` in the range `[first, last)`, the value `*i` is assigned to `output[*(map + (i - first))]`. The output iterator must permit random access. If the same index appears more than once in the range `[map, map + (last - first))`, the result is undefined.

```cpp
template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename RandomAccessIterator>
__host__ __device__ void
thrust::scatter(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 first,
  InputIterator1 last,
  InputIterator2 map,
  RandomAccessIterator result);

template <typename InputIterator1,
  typename InputIterator2,
  typename RandomAccessIterator>
void
thrust::scatter(InputIterator1 first,
  InputIterator1 last,
  InputIterator2 map,
  RandomAccessIterator result);
```

The following code demonstrates how to use `thrust::scatter` to reorder a range using the `thrust::device` execution policy for parallelization:
```cpp
#include <thrust/scatter.h>
#include <thrust/device_vector.h>
#include <thrust/execution_policy.h>

// mark even indices with a 1; odd indices with a o
int values[10] = {1, 0, 1, 0, 1, 0, 1, 0, 1, 0};
thrust::device_vector<int> d_values(values, values + 10);

// scatter all even indices into the first half of the
// range, and odd indices vice versa.
int map[10] = {0, 5, 1, 6, 2, 7, 3, 8, 4, 9};
thrust::device_vector<int> d_map(map, map + 10);

thrust::device_vector<int> d_output(10);
thrust::scatter(thrust::device,
                d_values.begin(), d_values.end(),
                d_map.begin(), d_output.begin());
// d_output is now {1, 1, 1, 1, 1, 0, 0, 0, 0, 0};
```

##### thrust::scatter_if

```cpp
template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename InputIterator3,
  typename RandomAccessIterator>
__host__ __device__ void
thrust::scatter_if(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 first,
  InputIterator1 last,
  InputIterator2 map,
  InputIterator3 stencil,
  RandomAccessIterator output);

template <typename InputIterator1,
  typename InputIterator2,
  typename InputIterator3,
  typename RandomAccessIterator>
void
thrust::scatter_if(InputIterator1 first,
  InputIterator1 last,
  InputIterator2 map,
  InputIterator3 stencil,
  RandomAccessIterator output);

template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename InputIterator3,
  typename RandomAccessIterator,
  typename Predicate>
__host__ __device__ void
thrust::scatter_if(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 first,
  InputIterator1 last,
  InputIterator2 map,
  InputIterator3 stencil,
  RandomAccessIterator output,
  Predicate pred);

template <typename InputIterator1,
  typename InputIterator2,
  typename InputIterator3,
  typename RandomAccessIterator,
  typename Predicate>
void
thrust::scatter_if(InputIterator1 first,
  InputIterator1 last,
  InputIterator2 map,
  InputIterator3 stencil,
  RandomAccessIterator output,
  Predicate pred);
```

#### Copying

[Reference Page](https://nvidia.github.io/thrust/api/groups/group__copying.html)

##### thrust::copy

```cpp
template <typename DerivedPolicy,
  typename InputIterator,
  typename OutputIterator>
__host__ __device__ OutputIterator
thrust::copy(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator first,
  InputIterator last,
  OutputIterator result);

template <typename InputIterator,
  typename OutputIterator>
OutputIterator
thrust::copy(InputIterator first,
  InputIterator last,
  OutputIterator result);
```

##### thrust::copy_n

```cpp
template <typename DerivedPolicy,
  typename InputIterator,
  typename Size,
  typename OutputIterator>
__host__ __device__ OutputIterator
thrust::copy_n(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator first,
  Size n,
  OutputIterator result);

template <typename InputIterator,
  typename Size,
  typename OutputIterator>
OutputIterator
thrust::copy_n(InputIterator first,
  Size n,
  OutputIterator result);
```

##### thrust::swap_ranges

`thrust::swap_ranges` swaps each of the elements in the range `[first1, last1)` with the corresponding element in the range `[first2, first2 + (last1 - first1))`. That is, for each integer `n` such that `0 <= n < (last1 - first1)`, it swaps `*(first1 + n)` and `*(first2 + n)`. The return value is `first2 + (lsat1 - first1)`.

```cpp
template <typename DerivedPolicy,
  typename ForwardIterator1,
  typename ForwardIterator2>
__host__ __device__ ForwardIterator2
thrust::swap_ranges(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator1 first1,
  ForwardIterator1 last1,
  ForwardIterator2 first2);

template <typename ForwardIterator1,
  typename ForwardIterator2>
ForwardIterator2
thrust::swap_ranges(ForwardIterator1 first1,
  ForwardIterator1 last1,
  ForwardIterator2 first2);
```

The following code snippet demonstrates how to use `swap_ranges` to swap the contents of two `thrust::device_vectors` using the `thrust::device` execution policy for parallelization:

```cpp
#include <thrust/swap.h>
#include <thrust/device_vector.h>
#include <thrust/execution_policy.h>

thrust::device_vector<int> v1(2), v2(2);
v1[0] = 1;
v1[1] = 2;
v2[0] = 3;
v2[1] = 4;

thrust::swap_ranges(thrust::device, v1.begin(), v1.end(), v2.begin());

// The result is:
// v1[0] = 3, v1[1] = 4
// v2[0] = 1, v2[1] = 2
```

##### thrust::uninitialized_copy

```cpp
template <typename DerivedPolicy,
  typename InputIterator,
  typename ForwardIterator>
__host__ __device__ ForwardIterator
thrust::uninitialized_copy(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator first,
  InputIterator last,
  ForwardIterator result);

template <typename InputIterator,
  typename ForwardIterator>
ForwardIterator
thrust::uninitialized_copy(InputIterator first,
  InputIterator last,
  ForwardIterator result);
```

##### thrust::uninitialized_copy_n

```cpp
template <typename DerivedPolicy,
  typename InputIterator,
  typename Size,
  typename ForwardIterator>
__host__ __device__ ForwardIterator
thrust::uninitialized_copy_n(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator first,
  Size n,
  ForwardIterator result);

template <typename InputIterator,
  typename Size,
  typename ForwardIterator>
ForwardIterator
thrust::uninitialized_copy_n(InputIterator first,
  Size n,
  ForwardIterator result);
```

### Merging

[Reference Page](https://nvidia.github.io/thrust/api/groups/group__merging.html)

#### thrust::merge

`thrust::merge` combines two sorted ranges `[first1, last1)` and `[first2, last2)` into a single sorted range. That is, it copies from `[first1, last1)` and `[first2, last2)` into `[result, result + (last1 - first1) + (last2 - first2))` such taht the resulting range is in ascending order. `merge` is stable, meaning both that the relative order of elements within each input range is preserved, and that for equivalent elements in both input ranges the element from the first range precedes the element from the second. The return value is `result + (last1 - first1) + (last2 - first2)`.

```cpp
template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator>
__host__ __device__ OutputIterator
thrust::merge(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  InputIterator2 last2,
  OutputIterator result);

template <typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator>
OutputIterator
thrust::merge(InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  InputIterator2 last2,
  OutputIterator result);

template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator,
  typename StrictWeakCompare>
__host__ __device__ OutputIterator
thrust::merge(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  InputIterator2 last2,
  OutputIterator result,
  StrictWeakCompare comp);

template <typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator,
  typename StrictWeakCompare>
OutputIterator
thrust::merge(InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  InputIterator2 last2,
  OutputIterator result,
  StrictWeakCompare comp);
```

The following code snippet demonstrates how to use `merge` to compute the merger of two sorted sets of integers using the `thrust::host` execution policy for parallelization:
```cpp
#include <thrust/merge.h>
#include <thrust/execution_policy.h>

// ...
int A1[6] = {1, 3, 5, 7, 9, 11};
int A2[7] = {1, 1, 2, 3, 5, 8, 13};

int result[13];
int *result_end = thrust::merge(thrust::host,
                                A1, A1 + 6,
                                A2, A2 + 7,
                                result);
// result = {1, 1, 1, 2, 3, 3, 5, 5, 7, 8, 9, 11, 13};
```

#### thrust::merge_by_key

```cpp
template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename InputIterator3,
  typename InputIterator4,
  typename OutputIterator1,
  typename OutputIterator2>
__host__ __device__ thrust::pair< OutputIterator1, OutputIterator2 >
thrust::merge_by_key(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 keys_first1,
  InputIterator1 keys_last1,
  InputIterator2 keys_first2,
  InputIterator2 keys_last2,
  InputIterator3 values_first1,
  InputIterator4 values_first2,
  OutputIterator1 keys_result,
  OutputIterator2 values_result);

template <typename InputIterator1,
  typename InputIterator2,
  typename InputIterator3,
  typename InputIterator4,
  typename OutputIterator1,
  typename OutputIterator2>
thrust::pair< OutputIterator1, OutputIterator2 >
thrust::merge_by_key(InputIterator1 keys_first1,
  InputIterator1 keys_last1,
  InputIterator2 keys_first2,
  InputIterator2 keys_last2,
  InputIterator3 values_first1,
  InputIterator4 values_first2,
  OutputIterator1 keys_result,
  OutputIterator2 values_result);

template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename InputIterator3,
  typename InputIterator4,
  typename OutputIterator1,
  typename OutputIterator2,
  typename Compare>
__host__ __device__ thrust::pair< OutputIterator1, OutputIterator2 >
thrust::merge_by_key(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 keys_first1,
  InputIterator1 keys_last1,
  InputIterator2 keys_first2,
  InputIterator2 keys_last2,
  InputIterator3 values_first1,
  InputIterator4 values_first2,
  OutputIterator1 keys_result,
  OutputIterator2 values_result,
  Compare comp);

template <typename InputIterator1,
  typename InputIterator2,
  typename InputIterator3,
  typename InputIterator4,
  typename OutputIterator1,
  typename OutputIterator2,
  typename StrictWeakCompare>
thrust::pair< OutputIterator1, OutputIterator2 >
thrust::merge_by_key(InputIterator1 keys_first1,
  InputIterator1 keys_last1,
  InputIterator2 keys_first2,
  InputIterator2 keys_last2,
  InputIterator3 values_first1,
  InputIterator4 values_first2,
  OutputIterator1 keys_result,
  OutputIterator2 values_result,
  StrictWeakCompare comp);
```

### Prefix Sums

#### Segmented Prefix Sums

[Reference Page](https://nvidia.github.io/thrust/api/groups/group__segmentedprefixsums.html)

##### thrust::inclusive_scan_by_key

```cpp
template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator>
__host__ __device__ OutputIterator
thrust::inclusive_scan_by_key(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  OutputIterator result);

template <typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator>
OutputIterator
thrust::inclusive_scan_by_key(InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  OutputIterator result);

template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator,
  typename BinaryPredicate>
__host__ __device__ OutputIterator
thrust::inclusive_scan_by_key(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  OutputIterator result,
  BinaryPredicate binary_pred);

template <typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator,
  typename BinaryPredicate>
OutputIterator
thrust::inclusive_scan_by_key(InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  OutputIterator result,
  BinaryPredicate binary_pred);

template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator,
  typename BinaryPredicate,
  typename AssociativeOperator>
__host__ __device__ OutputIterator
thrust::inclusive_scan_by_key(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  OutputIterator result,
  BinaryPredicate binary_pred,
  AssociativeOperator binary_op);

template <typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator,
  typename BinaryPredicate,
  typename AssociativeOperator>
OutputIterator
thrust::inclusive_scan_by_key(InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  OutputIterator result,
  BinaryPredicate binary_pred,
  AssociativeOperator binary_op);
```

##### thrust::exclusive_scan_by_key

```cpp
template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator>
__host__ __device__ OutputIterator
thrust::exclusive_scan_by_key(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  OutputIterator result);

template <typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator>
OutputIterator
thrust::exclusive_scan_by_key(InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  OutputIterator result);

template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator,
  typename T>
__host__ __device__ OutputIterator
thrust::exclusive_scan_by_key(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  OutputIterator result,
  T init);

template <typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator,
  typename T>
OutputIterator
thrust::exclusive_scan_by_key(InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  OutputIterator result,
  T init);

template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator,
  typename T,
  typename BinaryPredicate>
__host__ __device__ OutputIterator
thrust::exclusive_scan_by_key(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  OutputIterator result,
  T init,
  BinaryPredicate binary_pred);

template <typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator,
  typename T,
  typename BinaryPredicate>
OutputIterator
thrust::exclusive_scan_by_key(InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  OutputIterator result,
  T init,
  BinaryPredicate binary_pred);

template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator,
  typename T,
  typename BinaryPredicate,
  typename AssociativeOperator>
__host__ __device__ OutputIterator
thrust::exclusive_scan_by_key(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  OutputIterator result,
  T init,
  BinaryPredicate binary_pred,
  AssociativeOperator binary_op);

template <typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator,
  typename T,
  typename BinaryPredicate,
  typename AssociativeOperator>
OutputIterator
thrust::exclusive_scan_by_key(InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  OutputIterator result,
  T init,
  BinaryPredicate binary_pred,
  AssociativeOperator binary_op);
```

#### Transformed Prefix Sums

[Reference Page](https://nvidia.github.io/thrust/api/groups/group__transformed__prefixsums.html)

##### thrust::transform_inclusive_scan

```cpp
template <typename DerivedPolicy,
  typename InputIterator,
  typename OutputIterator,
  typename UnaryFunction,
  typename AssociativeOperator>
__host__ __device__ OutputIterator
thrust::transform_inclusive_scan(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator first,
  InputIterator last,
  OutputIterator result,
  UnaryFunction unary_op,
  AssociativeOperator binary_op);

template <typename InputIterator,
  typename OutputIterator,
  typename UnaryFunction,
  typename AssociativeOperator>
OutputIterator
thrust::transform_inclusive_scan(InputIterator first,
  InputIterator last,
  OutputIterator result,
  UnaryFunction unary_op,
  AssociativeOperator binary_op);
```

##### trust::transform_exclusive_scan

```cpp
template <typename DerivedPolicy,
  typename InputIterator,
  typename OutputIterator,
  typename UnaryFunction,
  typename T,
  typename AssociativeOperator>
__host__ __device__ OutputIterator
thrust::transform_exclusive_scan(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator first,
  InputIterator last,
  OutputIterator result,
  UnaryFunction unary_op,
  T init,
  AssociativeOperator binary_op);

template <typename InputIterator,
  typename OutputIterator,
  typename UnaryFunction,
  typename T,
  typename AssociativeOperator>
OutputIterator
thrust::transform_exclusive_scan(InputIterator first,
  InputIterator last,
  OutputIterator result,
  UnaryFunction unary_op,
  T init,
  AssociativeOperator binary_op);
```

#### Group Prefix Sums

[Reference Page](https://nvidia.github.io/thrust/api/groups/group__prefixsums.html)

##### thrust::inclusive_scan

`inclusive_scan` computes an inclusive prefix sum operation. The term 'inclusive' means each result includes the corresponding input operand in the partial sum. More precisely, `*first` is assigned to `*result` and the sum of `*first` and `*(first ~ 1)` is assigned to `*(result + 1)`, and so on.
`thrust::inclusive_scan` is similar to `std::partial_sum` in the STL. The primary difference between the two functions is that `std::parital_sum` guarantees a serial summation order, while `thrust::inclusive_scan` requires associativity of the binary operation to parallelize the prefix sum.
Results are not deterministic for pseudo-associative operators (e.g. addition of floating-point types). Results for pseudo-associative operators may vary from run to run.
The algorithm's execution is parallelized as determined by `exec`.

```cpp
template <typename DerivedPolicy,
  typename InputIterator,
  typename OutputIterator>
__host__ __device__ OutputIterator
thrust::inclusive_scan(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator first,
  InputIterator last,
  OutputIterator result);

template <typename InputIterator,
  typename OutputIterator>
OutputIterator
thrust::inclusive_scan(InputIterator first,
  InputIterator last,
  OutputIterator result);

template <typename DerivedPolicy,
  typename InputIterator,
  typename OutputIterator,
  typename AssociativeOperator>
__host__ __device__ OutputIterator
thrust::inclusive_scan(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator first,
  InputIterator last,
  OutputIterator result,
  AssociativeOperator binary_op);

template <typename InputIterator,
  typename OutputIterator,
  typename AssociativeOperator>
OutputIterator
thrust::inclusive_scan(InputIterator first,
  InputIterator last,
  OutputIterator result,
  AssociativeOperator binary_op);
```

The following code snippet demonstrates how to use `inclusive_scan` to compute an in-place p

##### thrust::exclusive_scan

```cpp
template <typename DerivedPolicy,
  typename InputIterator,
  typename OutputIterator>
__host__ __device__ OutputIterator
thrust::exclusive_scan(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator first,
  InputIterator last,
  OutputIterator result);

template <typename InputIterator,
  typename OutputIterator>
OutputIterator
thrust::exclusive_scan(InputIterator first,
  InputIterator last,
  OutputIterator result);

template <typename DerivedPolicy,
  typename InputIterator,
  typename OutputIterator,
  typename T>
__host__ __device__ OutputIterator
thrust::exclusive_scan(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator first,
  InputIterator last,
  OutputIterator result,
  T init);

template <typename InputIterator,
  typename OutputIterator,
  typename T>
OutputIterator
thrust::exclusive_scan(InputIterator first,
  InputIterator last,
  OutputIterator result,
  T init);

template <typename DerivedPolicy,
  typename InputIterator,
  typename OutputIterator,
  typename T,
  typename AssociativeOperator>
__host__ __device__ OutputIterator
thrust::exclusive_scan(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator first,
  InputIterator last,
  OutputIterator result,
  T init,
  AssociativeOperator binary_op);

template <typename InputIterator,
  typename OutputIterator,
  typename T,
  typename AssociativeOperator>
OutputIterator
thrust::exclusive_scan(InputIterator first,
  InputIterator last,
  OutputIterator result,
  T init,
  AssociativeOperator binary_op);
```

### Reductions

#### Comparisons

[Reference Page](https://nvidia.github.io/thrust/api/groups/group__comparisons.html)


##### thrust::equal

```cpp
template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2>
__host__ __device__ bool
thrust::equal(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2);

template <typename InputIterator1,
  typename InputIterator2>
bool
thrust::equal(InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2);

template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename BinaryPredicate>
__host__ __device__ bool
thrust::equal(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  BinaryPredicate binary_pred);

template <typename InputIterator1,
  typename InputIterator2,
  typename BinaryPredicate>
bool
thrust::equal(InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  BinaryPredicate binary_pred);
```

#### Counting

[Reference Page](https://nvidia.github.io/thrust/api/groups/group__counting.html)

##### thrust::count

```cpp
template <typename DerivedPolicy,
  typename InputIterator,
  typename EqualityComparable>
__host__ __device__ thrust::iterator_traits< InputIterator >::difference_type
thrust::count(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator first,
  InputIterator last,
  const EqualityComparable & value);

template <typename InputIterator,
  typename EqualityComparable>
thrust::iterator_traits< InputIterator >::difference_type
thrust::count(InputIterator first,
  InputIterator last,
  const EqualityComparable & value);
```

##### thrust::count_if

```cpp
template <typename DerivedPolicy,
  typename InputIterator,
  typename Predicate>
__host__ __device__ thrust::iterator_traits< InputIterator >::difference_type
thrust::count_if(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator first,
  InputIterator last,
  Predicate pred);

template <typename InputIterator,
  typename Predicate>
thrust::iterator_traits< InputIterator >::difference_type
thrust::count_if(InputIterator first,
  InputIterator last,
  Predicate pred);
```

#### Extrema

[Reference Page](https://nvidia.github.io/thrust/api/groups/group__extrema.html)

##### thrust::min_element

```cpp
template <typename DerivedPolicy,
  typename ForwardIterator>
__host__ __device__ ForwardIterator
thrust::min_element(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last);

template <typename ForwardIterator>
ForwardIterator
thrust::min_element(ForwardIterator first,
  ForwardIterator last);

template <typename DerivedPolicy,
  typename ForwardIterator,
  typename BinaryPredicate>
__host__ __device__ ForwardIterator
thrust::min_element(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last,
  BinaryPredicate comp);

template <typename ForwardIterator,
  typename BinaryPredicate>
ForwardIterator
thrust::min_element(ForwardIterator first,
  ForwardIterator last,
  BinaryPredicate comp);
```

##### thrust::max_element

```cpp
template <typename DerivedPolicy,
  typename ForwardIterator>
__host__ __device__ ForwardIterator
thrust::max_element(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last);

template <typename ForwardIterator>
ForwardIterator
thrust::max_element(ForwardIterator first,
  ForwardIterator last);

template <typename DerivedPolicy,
  typename ForwardIterator,
  typename BinaryPredicate>
__host__ __device__ ForwardIterator
thrust::max_element(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last,
  BinaryPredicate comp);

template <typename ForwardIterator,
  typename BinaryPredicate>
ForwardIterator
thrust::max_element(ForwardIterator first,
  ForwardIterator last,
  BinaryPredicate comp);

```

##### thrust::minmax_element

```cpp
template <typename DerivedPolicy,
  typename ForwardIterator>
__host__ __device__ thrust::pair< ForwardIterator, ForwardIterator >
thrust::minmax_element(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last);

template <typename ForwardIterator>
thrust::pair< ForwardIterator, ForwardIterator >
thrust::minmax_element(ForwardIterator first,
  ForwardIterator last);

template <typename DerivedPolicy,
  typename ForwardIterator,
  typename BinaryPredicate>
__host__ __device__ thrust::pair< ForwardIterator, ForwardIterator >
thrust::minmax_element(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last,
  BinaryPredicate comp);

template <typename ForwardIterator,
  typename BinaryPredicate>
thrust::pair< ForwardIterator, ForwardIterator >
thrust::minmax_element(ForwardIterator first,
  ForwardIterator last,
  BinaryPredicate comp);
```

#### Logical

[Reference Page](https://nvidia.github.io/thrust/api/groups/group__logical.html)

##### thrust::all_of

```cpp
template <typename DerivedPolicy,
  typename InputIterator,
  typename Predicate>
__host__ __device__ bool
thrust::all_of(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator first,
  InputIterator last,
  Predicate pred);

template <typename InputIterator,
  typename Predicate>
bool
thrust::all_of(InputIterator first,
  InputIterator last,
  Predicate pred);
```

##### thrust::any_of

```cpp
template <typename DerivedPolicy,
  typename InputIterator,
  typename Predicate>
__host__ __device__ bool
thrust::any_of(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator first,
  InputIterator last,
  Predicate pred);

template <typename InputIterator,
  typename Predicate>
bool
thrust::any_of(InputIterator first,
  InputIterator last,
  Predicate pred);
```

##### thrust::none_of

```cpp
template <typename DerivedPolicy,
  typename InputIterator,
  typename Predicate>
__host__ __device__ bool
thrust::none_of(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator first,
  InputIterator last,
  Predicate pred);

template <typename InputIterator,
  typename Predicate>
bool
thrust::none_of(InputIterator first,
  InputIterator last,
  Predicate pred);
```

#### Predicates

[Reference Page](https://nvidia.github.io/thrust/api/groups/group__predicates.html)

##### thrust::is_partitioned

`thrust::is_partitioned` returns `true` if the given range is partitioned with respect to a predicate, and `false` otherwise. 是否所有满足predicates的元素都出现在不满足 predicates的元素前面。
Specifically, `thrust::is_partitioned` returns `true` if `[first, last)` is empty of if `[first, last)` is partitioned by `pred`, i.e. if all elements that satisfy `pred` appear before those that do not.

```cpp
template <typename DerivedPolicy,
  typename InputIterator,
  typename Predicate>
__host__ __device__ bool
thrust::is_partitioned(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator first,
  InputIterator last,
  Predicate pred);

template <typename InputIterator,
  typename Predicate>
bool
thrust::is_partitioned(InputIterator first,
  InputIterator last,
  Predicate pred);
```

The following code demonstrates how to use `thrust::is_partitioned`:
```cpp
#include <thrust/partition.h>
#include <thrust/execution_policy.h>

struct is_even {
  __host__ __device__
  bool operator()(const int &x) { return (x % 2) == 0; }
};
// ...
int A[] = {2, 4, 6, 8, 19, 1, 3, 5, 7, 9};
int B[] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
thrust::is_partitioned(thrust::host, A, A + 10, is_even()); // returns true
thrust::is_partitioned(thrust::host, B, B + 10, is_even()); // returns false
```

##### thrust::is_sorted

`thrust::is_sorted` returns `true` if the range `[first, last)` is sorted in ascending order, and `false` otherwise.

```cpp
template <typename DerivedPolicy,
  typename ForwardIterator>
__host__ __device__ bool
thrust::is_sorted(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last);

template <typename ForwardIterator>
bool
thrust::is_sorted(ForwardIterator first,
  ForwardIterator last);

template <typename DerivedPolicy,
  typename ForwardIterator,
  typename Compare>
__host__ __device__ bool
thrust::is_sorted(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last,
  Compare comp);

template <typename ForwardIterator,
  typename Compare>
bool
thrust::is_sorted(ForwardIterator first,
  ForwardIterator last,
  Compare comp);
```

The following code demonstrates how to use `thrust::is_sorted`:
```cpp
#include <thrust/sort.h>
#include <thrust/device_vector.h>
#include <thrust/sort.h>
#include <thrust/execution_policy.h>

thrust::device_vector<int> v(6);
v[0] = 1;
v[1] = 4;
v[2] = 2;
v[3] = 8;
v[4] = 5;
v[5] = 7;

bool result = thrust::is_sorted(thrust::device, v.begin(), v.end());
// result == false;

thrust::sort(v.begin(), v.end());
result = thrust::is_sorted(thrust::device, v.begin(), v.end());
// result = true;
```

##### thrust::is_sorted_until

```cpp
template <typename DerivedPolicy,
  typename ForwardIterator>
__host__ __device__ ForwardIterator
thrust::is_sorted_until(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last);

template <typename ForwardIterator>
ForwardIterator
thrust::is_sorted_until(ForwardIterator first,
  ForwardIterator last);

template <typename DerivedPolicy,
  typename ForwardIterator,
  typename Compare>
__host__ __device__ ForwardIterator
thrust::is_sorted_until(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last,
  Compare comp);

template <typename ForwardIterator,
  typename Compare>
ForwardIterator
thrust::is_sorted_until(ForwardIterator first,
  ForwardIterator last,
  Compare comp);
```

#### Transformed Reductions

[Reference Page](https://nvidia.github.io/thrust/api/groups/group__transformed__reductions.html)

##### thrust::inner_product

```cpp
template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename OutputType>
__host__ __device__ OutputType
thrust::inner_product(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  OutputType init);

template <typename InputIterator1,
  typename InputIterator2,
  typename OutputType>
OutputType
thrust::inner_product(InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  OutputType init);

template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename OutputType,
  typename BinaryFunction1,
  typename BinaryFunction2>
__host__ __device__ OutputType
thrust::inner_product(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  OutputType init,
  BinaryFunction1 binary_op1,
  BinaryFunction2 binary_op2);

template <typename InputIterator1,
  typename InputIterator2,
  typename OutputType,
  typename BinaryFunction1,
  typename BinaryFunction2>
OutputType
thrust::inner_product(InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  OutputType init,
  BinaryFunction1 binary_op1,
  BinaryFunction2 binary_op2);
```

##### thrust::transform_reduce

```cpp
template <typename DerivedPolicy,
  typename InputIterator,
  typename UnaryFunction,
  typename OutputType,
  typename BinaryFunction>
__host__ __device__ OutputType
thrust::transform_reduce(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator first,
  InputIterator last,
  UnaryFunction unary_op,
  OutputType init,
  BinaryFunction binary_op);

template <typename InputIterator,
  typename UnaryFunction,
  typename OutputType,
  typename BinaryFunction>
OutputType
thrust::transform_reduce(InputIterator first,
  InputIterator last,
  UnaryFunction unary_op,
  OutputType init,
  BinaryFunction binary_op);
```

#### Reductions

[Reference Page](https://nvidia.github.io/thrust/api/groups/group__reductions.html)

##### thrust::reduce

```cpp
template <typename DerivedPolicy,
  typename InputIterator>
__host__ __device__ thrust::iterator_traits< InputIterator >::value_type
thrust::reduce(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator first,
  InputIterator last);

template <typename InputIterator>
thrust::iterator_traits< InputIterator >::value_type
thrust::reduce(InputIterator first,
  InputIterator last);

template <typename DerivedPolicy,
  typename InputIterator,
  typename T>
__host__ __device__ T
thrust::reduce(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator first,
  InputIterator last,
  T init);

template <typename InputIterator,
  typename T>
T
thrust::reduce(InputIterator first,
  InputIterator last,
  T init);

template <typename DerivedPolicy,
  typename InputIterator,
  typename T,
  typename BinaryFunction>
__host__ __device__ T
thrust::reduce(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator first,
  InputIterator last,
  T init,
  BinaryFunction binary_op);

template <typename InputIterator,
  typename T,
  typename BinaryFunction>
T
thrust::reduce(InputIterator first,
  InputIterator last,
  T init,
  BinaryFunction binary_op);
```

##### thrust::reduce_by_key

```cpp
template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator1,
  typename OutputIterator2>
__host__ __device__ thrust::pair< OutputIterator1, OutputIterator2 >
thrust::reduce_by_key(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 keys_first,
  InputIterator1 keys_last,
  InputIterator2 values_first,
  OutputIterator1 keys_output,
  OutputIterator2 values_output);

template <typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator1,
  typename OutputIterator2>
thrust::pair< OutputIterator1, OutputIterator2 >
thrust::reduce_by_key(InputIterator1 keys_first,
  InputIterator1 keys_last,
  InputIterator2 values_first,
  OutputIterator1 keys_output,
  OutputIterator2 values_output);

template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator1,
  typename OutputIterator2,
  typename BinaryPredicate>
__host__ __device__ thrust::pair< OutputIterator1, OutputIterator2 >
thrust::reduce_by_key(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 keys_first,
  InputIterator1 keys_last,
  InputIterator2 values_first,
  OutputIterator1 keys_output,
  OutputIterator2 values_output,
  BinaryPredicate binary_pred);

template <typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator1,
  typename OutputIterator2,
  typename BinaryPredicate>
thrust::pair< OutputIterator1, OutputIterator2 >
thrust::reduce_by_key(InputIterator1 keys_first,
  InputIterator1 keys_last,
  InputIterator2 values_first,
  OutputIterator1 keys_output,
  OutputIterator2 values_output,
  BinaryPredicate binary_pred);

template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator1,
  typename OutputIterator2,
  typename BinaryPredicate,
  typename BinaryFunction>
__host__ __device__ thrust::pair< OutputIterator1, OutputIterator2 >
thrust::reduce_by_key(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 keys_first,
  InputIterator1 keys_last,
  InputIterator2 values_first,
  OutputIterator1 keys_output,
  OutputIterator2 values_output,
  BinaryPredicate binary_pred,
  BinaryFunction binary_op);

template <typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator1,
  typename OutputIterator2,
  typename BinaryPredicate,
  typename BinaryFunction>
thrust::pair< OutputIterator1, OutputIterator2 >
thrust::reduce_by_key(InputIterator1 keys_first,
  InputIterator1 keys_last,
  InputIterator2 values_first,
  OutputIterator1 keys_output,
  OutputIterator2 values_output,
  BinaryPredicate binary_pred,
  BinaryFunction binary_op);
```

### Reordering

#### Partitioning

[Reference Page](https://nvidia.github.io/thrust/api/groups/group__partitioning.html)

##### thrust::partition
根据 Predicate 的结果来重新排列元素(true 元素在前，其余元素在后面).
`thrust::partition` reorders the elements `[first, last)` based on the function object `pred` applied to a stencil range `[stencil, stencil + (last - first))`, such that all the elements whose corresponding stencil element satisfies `pred` precede all the elements whose corresponding stencil element failed 

**Preconditions**: The ranges `[first, last)` and `[stencil, tencil + (last - first))` shall not overlap.

**Returns**: An iterator referring to the first element of the second partition, that is, the sequence of the elements whose stencil elements do not satisfy `pred`.
```cpp
template <typename DerivedPolicy,
  typename ForwardIterator,
  typename Predicate>
__host__ __device__ ForwardIterator
thrust::partition(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last,
  Predicate pred);

template <typename ForwardIterator,
  typename Predicate>
ForwardIterator
thrust::partition(ForwardIterator first,
  ForwardIterator last,
  Predicate pred);

template <typename DerivedPolicy,
  typename ForwardIterator,
  typename InputIterator,
  typename Predicate>
__host__ __device__ ForwardIterator
thrust::partition(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last,
  InputIterator stencil,
  Predicate pred);

template <typename ForwardIterator,
  typename InputIterator,
  typename Predicate>
ForwardIterator
thrust::partition(ForwardIterator first,
  ForwardIterator last,
  InputIterator stencil,
  Predicate pred);
```

##### thrust::partition_copy
`thrust::partition_copy` differs from `partition` only in that the reordered sequence is written to difference output sequences, rather than in place.

```cpp
template <typename DerivedPolicy,
  typename InputIterator,
  typename OutputIterator1,
  typename OutputIterator2,
  typename Predicate>
__host__ __device__ thrust::pair< OutputIterator1, OutputIterator2 >
thrust::partition_copy(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator first,
  InputIterator last,
  OutputIterator1 out_true,
  OutputIterator2 out_false,
  Predicate pred);

template <typename InputIterator,
  typename OutputIterator1,
  typename OutputIterator2,
  typename Predicate>
thrust::pair< OutputIterator1, OutputIterator2 >
thrust::partition_copy(InputIterator first,
  InputIterator last,
  OutputIterator1 out_true,
  OutputIterator2 out_false,
  Predicate pred);

template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator1,
  typename OutputIterator2,
  typename Predicate>
__host__ __device__ thrust::pair< OutputIterator1, OutputIterator2 >
thrust::partition_copy(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 first,
  InputIterator1 last,
  InputIterator2 stencil,
  OutputIterator1 out_true,
  OutputIterator2 out_false,
  Predicate pred);

template <typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator1,
  typename OutputIterator2,
  typename Predicate>
thrust::pair< OutputIterator1, OutputIterator2 >
thrust::partition_copy(InputIterator1 first,
  InputIterator1 last,
  InputIterator2 stencil,
  OutputIterator1 out_true,
  OutputIterator2 out_false,
  Predicate pred);
```

##### thrust::stable_partition

```cpp
template <typename DerivedPolicy,
  typename ForwardIterator,
  typename Predicate>
__host__ __device__ ForwardIterator
thrust::stable_partition(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last,
  Predicate pred);

template <typename ForwardIterator,
  typename Predicate>
ForwardIterator
thrust::stable_partition(ForwardIterator first,
  ForwardIterator last,
  Predicate pred);

template <typename DerivedPolicy,
  typename ForwardIterator,
  typename InputIterator,
  typename Predicate>
__host__ __device__ ForwardIterator
thrust::stable_partition(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last,
  InputIterator stencil,
  Predicate pred);

template <typename ForwardIterator,
  typename InputIterator,
  typename Predicate>
ForwardIterator
thrust::stable_partition(ForwardIterator first,
  ForwardIterator last,
  InputIterator stencil,
  Predicate pred);
```

##### thrust::stable_partition_copy

```cpp
template <typename DerivedPolicy,
  typename InputIterator,
  typename OutputIterator1,
  typename OutputIterator2,
  typename Predicate>
__host__ __device__ thrust::pair< OutputIterator1, OutputIterator2 >
thrust::stable_partition_copy(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator first,
  InputIterator last,
  OutputIterator1 out_true,
  OutputIterator2 out_false,
  Predicate pred);

template <typename InputIterator,
  typename OutputIterator1,
  typename OutputIterator2,
  typename Predicate>
thrust::pair< OutputIterator1, OutputIterator2 >
thrust::stable_partition_copy(InputIterator first,
  InputIterator last,
  OutputIterator1 out_true,
  OutputIterator2 out_false,
  Predicate pred);

template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator1,
  typename OutputIterator2,
  typename Predicate>
__host__ __device__ thrust::pair< OutputIterator1, OutputIterator2 >
thrust::stable_partition_copy(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 first,
  InputIterator1 last,
  InputIterator2 stencil,
  OutputIterator1 out_true,
  OutputIterator2 out_false,
  Predicate pred);

template <typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator1,
  typename OutputIterator2,
  typename Predicate>
thrust::pair< OutputIterator1, OutputIterator2 >
thrust::stable_partition_copy(InputIterator1 first,
  InputIterator1 last,
  InputIterator2 stencil,
  OutputIterator1 out_true,
  OutputIterator2 out_false,
  Predicate pred);
```

#### Shuffling

[Reference Page](https://nvidia.github.io/thrust/api/groups/group__shuffling.html)

##### thrust::shuffle

```cpp
template <typename DerivedPolicy,
  typename RandomIterator,
  typename URBG>
__host__ __device__ void
thrust::shuffle(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  RandomIterator first,
  RandomIterator last,
  URBG && g);

template <typename RandomIterator,
  typename URBG>
__host__ __device__ void
thrust::shuffle(RandomIterator first,
  RandomIterator last,
  URBG && g);
```

##### thrust::shuffle_copy

```cpp
template <typename DerivedPolicy,
  typename RandomIterator,
  typename OutputIterator,
  typename URBG>
__host__ __device__ void
thrust::shuffle_copy(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  RandomIterator first,
  RandomIterator last,
  OutputIterator result,
  URBG && g);

template <typename RandomIterator,
  typename OutputIterator,
  typename URBG>
__host__ __device__ void
thrust::shuffle_copy(RandomIterator first,
  RandomIterator last,
  OutputIterator result,
  URBG && g);
```

#### Stream Compaction

[Reference Page](https://nvidia.github.io/thrust/api/groups/group__stream__compaction.html)

##### thrust::copy_if

```cpp
template <typename DerivedPolicy,
  typename InputIterator,
  typename OutputIterator,
  typename Predicate>
__host__ __device__ OutputIterator
thrust::copy_if(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator first,
  InputIterator last,
  OutputIterator result,
  Predicate pred);

template <typename InputIterator,
  typename OutputIterator,
  typename Predicate>
OutputIterator
thrust::copy_if(InputIterator first,
  InputIterator last,
  OutputIterator result,
  Predicate pred);

template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator,
  typename Predicate>
__host__ __device__ OutputIterator
thrust::copy_if(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 first,
  InputIterator1 last,
  InputIterator2 stencil,
  OutputIterator result,
  Predicate pred);

template <typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator,
  typename Predicate>
OutputIterator
thrust::copy_if(InputIterator1 first,
  InputIterator1 last,
  InputIterator2 stencil,
  OutputIterator result,
  Predicate pred);
```

##### thrust::remove

```cpp
template <typename DerivedPolicy,
  typename ForwardIterator,
  typename T>
__host__ __device__ ForwardIterator
thrust::remove(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last,
  const T & value);

template <typename ForwardIterator,
  typename T>
ForwardIterator
thrust::remove(ForwardIterator first,
  ForwardIterator last,
  const T & value);
```

##### thrust::remove_copy

```cpp
template <typename DerivedPolicy,
  typename InputIterator,
  typename OutputIterator,
  typename T>
__host__ __device__ OutputIterator
thrust::remove_copy(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator first,
  InputIterator last,
  OutputIterator result,
  const T & value);

template <typename InputIterator,
  typename OutputIterator,
  typename T>
OutputIterator
thrust::remove_copy(InputIterator first,
  InputIterator last,
  OutputIterator result,
  const T & value);
```

##### thrust::remove_if

```cpp
template <typename DerivedPolicy,
  typename ForwardIterator,
  typename Predicate>
__host__ __device__ ForwardIterator
thrust::remove_if(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last,
  Predicate pred);

template <typename ForwardIterator,
  typename Predicate>
ForwardIterator
thrust::remove_if(ForwardIterator first,
  ForwardIterator last,
  Predicate pred);

template <typename ForwardIterator,
  typename InputIterator,
  typename Predicate>
ForwardIterator
thrust::remove_if(ForwardIterator first,
  ForwardIterator last,
  InputIterator stencil,
  Predicate pred);

template <typename DerivedPolicy,
  typename ForwardIterator,
  typename InputIterator,
  typename Predicate>
__host__ __device__ ForwardIterator
thrust::remove_if(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last,
  InputIterator stencil,
  Predicate pred);

```

##### thrust::remove_copy_if

```cpp
template <typename DerivedPolicy,
  typename InputIterator,
  typename OutputIterator,
  typename Predicate>
__host__ __device__ OutputIterator
thrust::remove_copy_if(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator first,
  InputIterator last,
  OutputIterator result,
  Predicate pred);

template <typename InputIterator,
  typename OutputIterator,
  typename Predicate>
OutputIterator
thrust::remove_copy_if(InputIterator first,
  InputIterator last,
  OutputIterator result,
  Predicate pred);

template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator,
  typename Predicate>
__host__ __device__ OutputIterator
thrust::remove_copy_if(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 first,
  InputIterator1 last,
  InputIterator2 stencil,
  OutputIterator result,
  Predicate pred);

template <typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator,
  typename Predicate>
OutputIterator
thrust::remove_copy_if(InputIterator1 first,
  InputIterator1 last,
  InputIterator2 stencil,
  OutputIterator result,
  Predicate pred);
```

##### thrust::unique

```cpp
template <typename DerivedPolicy,
  typename ForwardIterator>
__host__ __device__ ForwardIterator
thrust::unique(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last);

template <typename ForwardIterator>
ForwardIterator
thrust::unique(ForwardIterator first,
  ForwardIterator last);

template <typename DerivedPolicy,
  typename ForwardIterator,
  typename BinaryPredicate>
__host__ __device__ ForwardIterator
thrust::unique(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last,
  BinaryPredicate binary_pred);

template <typename ForwardIterator,
  typename BinaryPredicate>
ForwardIterator
thrust::unique(ForwardIterator first,
  ForwardIterator last,
  BinaryPredicate binary_pred);
```

##### thrust::unique_copy

```cpp
template <typename DerivedPolicy,
  typename InputIterator,
  typename OutputIterator>
__host__ __device__ OutputIterator
thrust::unique_copy(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator first,
  InputIterator last,
  OutputIterator result);

template <typename InputIterator,
  typename OutputIterator>
OutputIterator
thrust::unique_copy(InputIterator first,
  InputIterator last,
  OutputIterator result);

template <typename DerivedPolicy,
  typename InputIterator,
  typename OutputIterator,
  typename BinaryPredicate>
__host__ __device__ OutputIterator
thrust::unique_copy(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator first,
  InputIterator last,
  OutputIterator result,
  BinaryPredicate binary_pred);

template <typename InputIterator,
  typename OutputIterator,
  typename BinaryPredicate>
OutputIterator
thrust::unique_copy(InputIterator first,
  InputIterator last,
  OutputIterator result,
  BinaryPredicate binary_pred);
```

##### thrust::unique_by_key

```cpp
template <typename DerivedPolicy,
  typename ForwardIterator1,
  typename ForwardIterator2>
__host__ __device__ thrust::pair< ForwardIterator1, ForwardIterator2 >
thrust::unique_by_key(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator1 keys_first,
  ForwardIterator1 keys_last,
  ForwardIterator2 values_first);

template <typename ForwardIterator1,
  typename ForwardIterator2>
thrust::pair< ForwardIterator1, ForwardIterator2 >
thrust::unique_by_key(ForwardIterator1 keys_first,
  ForwardIterator1 keys_last,
  ForwardIterator2 values_first);

template <typename DerivedPolicy,
  typename ForwardIterator1,
  typename ForwardIterator2,
  typename BinaryPredicate>
__host__ __device__ thrust::pair< ForwardIterator1, ForwardIterator2 >
thrust::unique_by_key(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator1 keys_first,
  ForwardIterator1 keys_last,
  ForwardIterator2 values_first,
  BinaryPredicate binary_pred);

template <typename ForwardIterator1,
  typename ForwardIterator2,
  typename BinaryPredicate>
thrust::pair< ForwardIterator1, ForwardIterator2 >
thrust::unique_by_key(ForwardIterator1 keys_first,
  ForwardIterator1 keys_last,
  ForwardIterator2 values_first,
  BinaryPredicate binary_pred);
```

##### thrust::unique_by_key_copy

```cpp
template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator1,
  typename OutputIterator2>
__host__ __device__ thrust::pair< OutputIterator1, OutputIterator2 >
thrust::unique_by_key_copy(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 keys_first,
  InputIterator1 keys_last,
  InputIterator2 values_first,
  OutputIterator1 keys_result,
  OutputIterator2 values_result);

template <typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator1,
  typename OutputIterator2>
thrust::pair< OutputIterator1, OutputIterator2 >
thrust::unique_by_key_copy(InputIterator1 keys_first,
  InputIterator1 keys_last,
  InputIterator2 values_first,
  OutputIterator1 keys_result,
  OutputIterator2 values_result);

template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator1,
  typename OutputIterator2,
  typename BinaryPredicate>
__host__ __device__ thrust::pair< OutputIterator1, OutputIterator2 >
thrust::unique_by_key_copy(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 keys_first,
  InputIterator1 keys_last,
  InputIterator2 values_first,
  OutputIterator1 keys_result,
  OutputIterator2 values_result,
  BinaryPredicate binary_pred);

template <typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator1,
  typename OutputIterator2,
  typename BinaryPredicate>
thrust::pair< OutputIterator1, OutputIterator2 >
thrust::unique_by_key_copy(InputIterator1 keys_first,
  InputIterator1 keys_last,
  InputIterator2 values_first,
  OutputIterator1 keys_result,
  OutputIterator2 values_result,
  BinaryPredicate binary_pred);
```

##### thrust::unique_count

```cpp
template <typename DerivedPolicy,
  typename ForwardIterator,
  typename BinaryPredicate>
__host__ __device__ thrust::iterator_traits< ForwardIterator >::difference_type
thrust::unique_count(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last,
  BinaryPredicate binary_pred);

template <typename DerivedPolicy,
  typename ForwardIterator>
__host__ __device__ thrust::iterator_traits< ForwardIterator >::difference_type
thrust::unique_count(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last);

template <typename ForwardIterator,
  typename BinaryPredicate>
__host__ __device__ thrust::iterator_traits< ForwardIterator >::difference_type
thrust::unique_count(ForwardIterator first,
  ForwardIterator last,
  BinaryPredicate binary_pred);

template <typename ForwardIterator>
__host__ __device__ thrust::iterator_traits< ForwardIterator >::difference_type
thrust::unique_count(ForwardIterator first,
  ForwardIterator last);
```

### Searching

[Reference Page](https://nvidia.github.io/thrust/api/groups/group__searching.html)

##### thrust::find

```cpp
template <typename DerivedPolicy,
  typename InputIterator,
  typename T>
__host__ __device__ InputIterator
thrust::find(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator first,
  InputIterator last,
  const T & value);

template <typename InputIterator,
  typename T>
InputIterator
thrust::find(InputIterator first,
  InputIterator last,
  const T & value);
```

##### thrust::find_if

```cpp
template <typename DerivedPolicy,
  typename InputIterator,
  typename Predicate>
__host__ __device__ InputIterator
thrust::find_if(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator first,
  InputIterator last,
  Predicate pred);

template <typename InputIterator,
  typename Predicate>
InputIterator
thrust::find_if(InputIterator first,
  InputIterator last,
  Predicate pred);
```

##### thrust::find_if_not

```cpp
template <typename DerivedPolicy,
  typename InputIterator,
  typename Predicate>
__host__ __device__ InputIterator
thrust::find_if_not(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator first,
  InputIterator last,
  Predicate pred);

template <typename InputIterator,
  typename Predicate>
InputIterator
thrust::find_if_not(InputIterator first,
  InputIterator last,
  Predicate pred);
```

##### thrust::mismatch

`thrust::mismatch` finds the first position where the two ranges `[first1, last1)` and `[first2, first2 + (last1 - first1))` differ.

```cpp
template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2>
__host__ __device__ thrust::pair< InputIterator1, InputIterator2 >
thrust::mismatch(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2);

template <typename InputIterator1,
  typename InputIterator2>
thrust::pair< InputIterator1, InputIterator2 >
thrust::mismatch(InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2);

template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename BinaryPredicate>
__host__ __device__ thrust::pair< InputIterator1, InputIterator2 >
thrust::mismatch(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  BinaryPredicate pred);

template <typename InputIterator1,
  typename InputIterator2,
  typename BinaryPredicate>
thrust::pair< InputIterator1, InputIterator2 >
thrust::mismatch(InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  BinaryPredicate pred);
```

The following code demonstrates how to use `thrust::mismatch`.

```cpp
#include <thrust/mismatch.h>
#include <thrust/device_vector.h>
#include <thrust/execution_policy.h>

thrust::device_vector<int> vec1(4);
thrust::device_vector<int> vec2(4);

vec1[0] = 0; vec2[0] = 0;
vec1[2] = 5; vec2[1] = 5;
vec1[3] = 3; vec2[2] = 8;
vec1[4] = 7; vec2[3] = 7;

typedef thrust::device_vector<int>::iterator Iterator;
thrust::pair<Iterator, Iterator> result;

result = thrust::mismatch(thrust::device,
                          vec1.begin(), vec1.end(),
                          vec2.begin());
```

##### thrust::partition_point

`thrust::partition_point` returns an iterator pointing to the end of the true partition of a partitioned range. `partition_point` requires the input range `[first, last)` to be a partition; that is, all elements which satisfy `pred` shall appear before those that do not.

**Preconditions**: The range `[first, last)` shall be partitioned by `pred`.

**Returns**: An iterator `mid` such that `all_of(first, mid, pred)` and `none_of(mid, last, pred)` are both true.

**Note**: Though similar, `thrust::partition_point` is not redundant with `thrust::find_if_not`, `thrust::partition_point`'s precondition (`[first, last)` shall be partitioned by `pred`) provides an opportunity for a faster implementation.

```cpp
template <typename DerivedPolicy,
  typename ForwardIterator,
  typename Predicate>
__host__ __device__ ForwardIterator
thrust::partition_point(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last,
  Predicate pred);

template <typename ForwardIterator,
  typename Predicate>
ForwardIterator
thrust::partition_point(ForwardIterator first,
  ForwardIterator last,
  Predicate pred);
```

The following code demonstrates how to use `thrust::partition_point`:
```cpp
#include <thrust/partition.h>
#include <thrust/execution_policy.h>

struct is_even {
  __host__ __device__
  bool operator()(const int &x) {
    return (x % 2) == 0;
  }
};

int A[] = {2, 4, 6, 8, 10, 1, 3, 5, 7, 9};
int *B = thrust::partition_point(thrust::host, A, A + 10, is_even());
// B - A = 5
// [A, B) contains only even values.
```

#### Binary Search

[Reference Page](https://nvidia.github.io/thrust/api/groups/group__binary__search.html)

##### thrust::lower_bound

```cpp
template <typename DerivedPolicy,
  typename ForwardIterator,
  typename LessThanComparable>
__host__ __device__ ForwardIterator
thrust::lower_bound(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last,
  const LessThanComparable & value);

template <class ForwardIterator,
  class LessThanComparable>
ForwardIterator
thrust::lower_bound(ForwardIterator first,
  ForwardIterator last,
  const LessThanComparable & value);

template <typename DerivedPolicy,
  typename ForwardIterator,
  typename T,
  typename StrictWeakOrdering>
__host__ __device__ ForwardIterator
thrust::lower_bound(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last,
  const T & value,
  StrictWeakOrdering comp);

template <class ForwardIterator,
  class T,
  class StrictWeakOrdering>
ForwardIterator
thrust::lower_bound(ForwardIterator first,
  ForwardIterator last,
  const T & value,
  StrictWeakOrdering comp);
```

##### thrust::upper_bound

```cpp
template <typename DerivedPolicy,
  typename ForwardIterator,
  typename LessThanComparable>
__host__ __device__ ForwardIterator
thrust::upper_bound(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last,
  const LessThanComparable & value);

template <class ForwardIterator,
  class LessThanComparable>
ForwardIterator
thrust::upper_bound(ForwardIterator first,
  ForwardIterator last,
  const LessThanComparable & value);

template <typename DerivedPolicy,
  typename ForwardIterator,
  typename T,
  typename StrictWeakOrdering>
__host__ __device__ ForwardIterator
thrust::upper_bound(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last,
  const T & value,
  StrictWeakOrdering comp);

template <class ForwardIterator,
  class T,
  class StrictWeakOrdering>
ForwardIterator
thrust::upper_bound(ForwardIterator first,
  ForwardIterator last,
  const T & value,
  StrictWeakOrdering comp);
```

##### thrust::binary_search

```cpp
template <typename DerivedPolicy,
  typename ForwardIterator,
  typename LessThanComparable>
__host__ __device__ bool
thrust::binary_search(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last,
  const LessThanComparable & value);

template <class ForwardIterator,
  class LessThanComparable>
bool
thrust::binary_search(ForwardIterator first,
  ForwardIterator last,
  const LessThanComparable & value);

template <typename DerivedPolicy,
  typename ForwardIterator,
  typename T,
  typename StrictWeakOrdering>
__host__ __device__ bool
thrust::binary_search(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last,
  const T & value,
  StrictWeakOrdering comp);

template <class ForwardIterator,
  class T,
  class StrictWeakOrdering>
bool
thrust::binary_search(ForwardIterator first,
  ForwardIterator last,
  const T & value,
  StrictWeakOrdering comp);
```

##### thrust::equal_range

```cpp
template <typename DerivedPolicy,
  typename ForwardIterator,
  typename LessThanComparable>
__host__ __device__ thrust::pair< ForwardIterator, ForwardIterator >
thrust::equal_range(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last,
  const LessThanComparable & value);

template <class ForwardIterator,
  class LessThanComparable>
thrust::pair< ForwardIterator, ForwardIterator >
thrust::equal_range(ForwardIterator first,
  ForwardIterator last,
  const LessThanComparable & value);

template <typename DerivedPolicy,
  typename ForwardIterator,
  typename T,
  typename StrictWeakOrdering>
__host__ __device__ thrust::pair< ForwardIterator, ForwardIterator >
thrust::equal_range(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last,
  const T & value,
  StrictWeakOrdering comp);

template <class ForwardIterator,
  class T,
  class StrictWeakOrdering>
thrust::pair< ForwardIterator, ForwardIterator >
thrust::equal_range(ForwardIterator first,
  ForwardIterator last,
  const T & value,
  StrictWeakOrdering comp);
```

#### Binary Search (Vectorized Searches)

[Reference Page](https://nvidia.github.io/thrust/api/groups/group__vectorized__binary__search.html)

##### thrust::lower_bound

```cpp
template <typename DerivedPolicy,
  typename ForwardIterator,
  typename InputIterator,
  typename OutputIterator>
__host__ __device__ OutputIterator
thrust::lower_bound(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last,
  InputIterator values_first,
  InputIterator values_last,
  OutputIterator result);

template <class ForwardIterator,
  class InputIterator,
  class OutputIterator>
OutputIterator
thrust::lower_bound(ForwardIterator first,
  ForwardIterator last,
  InputIterator values_first,
  InputIterator values_last,
  OutputIterator result);

template <typename DerivedPolicy,
  typename ForwardIterator,
  typename InputIterator,
  typename OutputIterator,
  typename StrictWeakOrdering>
__host__ __device__ OutputIterator
thrust::lower_bound(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last,
  InputIterator values_first,
  InputIterator values_last,
  OutputIterator result,
  StrictWeakOrdering comp);

template <class ForwardIterator,
  class InputIterator,
  class OutputIterator,
  class StrictWeakOrdering>
OutputIterator
thrust::lower_bound(ForwardIterator first,
  ForwardIterator last,
  InputIterator values_first,
  InputIterator values_last,
  OutputIterator result,
  StrictWeakOrdering comp);
```

##### thrust::upper_bound

```cpp
template <typename DerivedPolicy,
  typename ForwardIterator,
  typename InputIterator,
  typename OutputIterator>
__host__ __device__ OutputIterator
thrust::upper_bound(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last,
  InputIterator values_first,
  InputIterator values_last,
  OutputIterator result);

template <class ForwardIterator,
  class InputIterator,
  class OutputIterator>
OutputIterator
thrust::upper_bound(ForwardIterator first,
  ForwardIterator last,
  InputIterator values_first,
  InputIterator values_last,
  OutputIterator result);

template <typename DerivedPolicy,
  typename ForwardIterator,
  typename InputIterator,
  typename OutputIterator,
  typename StrictWeakOrdering>
__host__ __device__ OutputIterator
thrust::upper_bound(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last,
  InputIterator values_first,
  InputIterator values_last,
  OutputIterator result,
  StrictWeakOrdering comp);

template <class ForwardIterator,
  class InputIterator,
  class OutputIterator,
  class StrictWeakOrdering>
OutputIterator
thrust::upper_bound(ForwardIterator first,
  ForwardIterator last,
  InputIterator values_first,
  InputIterator values_last,
  OutputIterator result,
  StrictWeakOrdering comp);
```

##### thrust::binary_search

```cpp
template <typename DerivedPolicy,
  typename ForwardIterator,
  typename InputIterator,
  typename OutputIterator>
__host__ __device__ OutputIterator
thrust::binary_search(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last,
  InputIterator values_first,
  InputIterator values_last,
  OutputIterator result);

template <class ForwardIterator,
  class InputIterator,
  class OutputIterator>
OutputIterator
thrust::binary_search(ForwardIterator first,
  ForwardIterator last,
  InputIterator values_first,
  InputIterator values_last,
  OutputIterator result);

template <typename DerivedPolicy,
  typename ForwardIterator,
  typename InputIterator,
  typename OutputIterator,
  typename StrictWeakOrdering>
__host__ __device__ OutputIterator
thrust::binary_search(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last,
  InputIterator values_first,
  InputIterator values_last,
  OutputIterator result,
  StrictWeakOrdering comp);

template <class ForwardIterator,
  class InputIterator,
  class OutputIterator,
  class StrictWeakOrdering>
OutputIterator
thrust::binary_search(ForwardIterator first,
  ForwardIterator last,
  InputIterator values_first,
  InputIterator values_last,
  OutputIterator result,
  StrictWeakOrdering comp);
```

### Set Operations

[Reference Page](https://nvidia.github.io/thrust/api/groups/group__set__operations.html)

##### thrust::set_difference

```cpp
template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator>
__host__ __device__ OutputIterator
thrust::set_difference(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  InputIterator2 last2,
  OutputIterator result);

template <typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator>
OutputIterator
thrust::set_difference(InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  InputIterator2 last2,
  OutputIterator result);

template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator,
  typename StrictWeakCompare>
__host__ __device__ OutputIterator
thrust::set_difference(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  InputIterator2 last2,
  OutputIterator result,
  StrictWeakCompare comp);

template <typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator,
  typename StrictWeakCompare>
OutputIterator
thrust::set_difference(InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  InputIterator2 last2,
  OutputIterator result,
  StrictWeakCompare comp);
```

##### thrust::set_intersection

```cpp
template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator>
__host__ __device__ OutputIterator
thrust::set_intersection(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  InputIterator2 last2,
  OutputIterator result);

template <typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator>
OutputIterator
thrust::set_intersection(InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  InputIterator2 last2,
  OutputIterator result);

template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator,
  typename StrictWeakCompare>
__host__ __device__ OutputIterator
thrust::set_intersection(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  InputIterator2 last2,
  OutputIterator result,
  StrictWeakCompare comp);

template <typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator,
  typename StrictWeakCompare>
OutputIterator
thrust::set_intersection(InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  InputIterator2 last2,
  OutputIterator result,
  StrictWeakCompare comp);
```

##### thrust::set_symmetric_difference

```cpp
template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator>
__host__ __device__ OutputIterator
thrust::set_symmetric_difference(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  InputIterator2 last2,
  OutputIterator result);

template <typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator>
OutputIterator
thrust::set_symmetric_difference(InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  InputIterator2 last2,
  OutputIterator result);

template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator,
  typename StrictWeakCompare>
__host__ __device__ OutputIterator
thrust::set_symmetric_difference(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  InputIterator2 last2,
  OutputIterator result,
  StrictWeakCompare comp);

template <typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator,
  typename StrictWeakCompare>
OutputIterator
thrust::set_symmetric_difference(InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  InputIterator2 last2,
  OutputIterator result,
  StrictWeakCompare comp);
```

##### thrust::set_union

```cpp
template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator>
__host__ __device__ OutputIterator
thrust::set_union(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  InputIterator2 last2,
  OutputIterator result);

template <typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator>
OutputIterator
thrust::set_union(InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  InputIterator2 last2,
  OutputIterator result);

template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator,
  typename StrictWeakCompare>
__host__ __device__ OutputIterator
thrust::set_union(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  InputIterator2 last2,
  OutputIterator result,
  StrictWeakCompare comp);

template <typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator,
  typename StrictWeakCompare>
OutputIterator
thrust::set_union(InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  InputIterator2 last2,
  OutputIterator result,
  StrictWeakCompare comp);
```

##### thrust::set_difference_by_key

```cpp
template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename InputIterator3,
  typename InputIterator4,
  typename OutputIterator1,
  typename OutputIterator2>
__host__ __device__ thrust::pair< OutputIterator1, OutputIterator2 >
thrust::set_difference_by_key(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 keys_first1,
  InputIterator1 keys_last1,
  InputIterator2 keys_first2,
  InputIterator2 keys_last2,
  InputIterator3 values_first1,
  InputIterator4 values_first2,
  OutputIterator1 keys_result,
  OutputIterator2 values_result);

template <typename InputIterator1,
  typename InputIterator2,
  typename InputIterator3,
  typename InputIterator4,
  typename OutputIterator1,
  typename OutputIterator2>
thrust::pair< OutputIterator1, OutputIterator2 >
thrust::set_difference_by_key(InputIterator1 keys_first1,
  InputIterator1 keys_last1,
  InputIterator2 keys_first2,
  InputIterator2 keys_last2,
  InputIterator3 values_first1,
  InputIterator4 values_first2,
  OutputIterator1 keys_result,
  OutputIterator2 values_result);

template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename InputIterator3,
  typename InputIterator4,
  typename OutputIterator1,
  typename OutputIterator2,
  typename StrictWeakCompare>
__host__ __device__ thrust::pair< OutputIterator1, OutputIterator2 >
thrust::set_difference_by_key(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 keys_first1,
  InputIterator1 keys_last1,
  InputIterator2 keys_first2,
  InputIterator2 keys_last2,
  InputIterator3 values_first1,
  InputIterator4 values_first2,
  OutputIterator1 keys_result,
  OutputIterator2 values_result,
  StrictWeakCompare comp);

template <typename InputIterator1,
  typename InputIterator2,
  typename InputIterator3,
  typename InputIterator4,
  typename OutputIterator1,
  typename OutputIterator2,
  typename StrictWeakCompare>
thrust::pair< OutputIterator1, OutputIterator2 >
thrust::set_difference_by_key(InputIterator1 keys_first1,
  InputIterator1 keys_last1,
  InputIterator2 keys_first2,
  InputIterator2 keys_last2,
  InputIterator3 values_first1,
  InputIterator4 values_first2,
  OutputIterator1 keys_result,
  OutputIterator2 values_result,
  StrictWeakCompare comp);
```

##### thrust::set_intersection_by_key

```cpp
template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename InputIterator3,
  typename OutputIterator1,
  typename OutputIterator2>
__host__ __device__ thrust::pair< OutputIterator1, OutputIterator2 >
thrust::set_intersection_by_key(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 keys_first1,
  InputIterator1 keys_last1,
  InputIterator2 keys_first2,
  InputIterator2 keys_last2,
  InputIterator3 values_first1,
  OutputIterator1 keys_result,
  OutputIterator2 values_result);

template <typename InputIterator1,
  typename InputIterator2,
  typename InputIterator3,
  typename OutputIterator1,
  typename OutputIterator2>
thrust::pair< OutputIterator1, OutputIterator2 >
thrust::set_intersection_by_key(InputIterator1 keys_first1,
  InputIterator1 keys_last1,
  InputIterator2 keys_first2,
  InputIterator2 keys_last2,
  InputIterator3 values_first1,
  OutputIterator1 keys_result,
  OutputIterator2 values_result);

template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename InputIterator3,
  typename OutputIterator1,
  typename OutputIterator2,
  typename StrictWeakCompare>
__host__ __device__ thrust::pair< OutputIterator1, OutputIterator2 >
thrust::set_intersection_by_key(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 keys_first1,
  InputIterator1 keys_last1,
  InputIterator2 keys_first2,
  InputIterator2 keys_last2,
  InputIterator3 values_first1,
  OutputIterator1 keys_result,
  OutputIterator2 values_result,
  StrictWeakCompare comp);

template <typename InputIterator1,
  typename InputIterator2,
  typename InputIterator3,
  typename OutputIterator1,
  typename OutputIterator2,
  typename StrictWeakCompare>
thrust::pair< OutputIterator1, OutputIterator2 >
thrust::set_intersection_by_key(InputIterator1 keys_first1,
  InputIterator1 keys_last1,
  InputIterator2 keys_first2,
  InputIterator2 keys_last2,
  InputIterator3 values_first1,
  OutputIterator1 keys_result,
  OutputIterator2 values_result,
  StrictWeakCompare comp);
```

##### thrust::set_symmetric_difference_by_key

```cpp
template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename InputIterator3,
  typename InputIterator4,
  typename OutputIterator1,
  typename OutputIterator2>
__host__ __device__ thrust::pair< OutputIterator1, OutputIterator2 >
thrust::set_symmetric_difference_by_key(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 keys_first1,
  InputIterator1 keys_last1,
  InputIterator2 keys_first2,
  InputIterator2 keys_last2,
  InputIterator3 values_first1,
  InputIterator4 values_first2,
  OutputIterator1 keys_result,
  OutputIterator2 values_result);

template <typename InputIterator1,
  typename InputIterator2,
  typename InputIterator3,
  typename InputIterator4,
  typename OutputIterator1,
  typename OutputIterator2>
thrust::pair< OutputIterator1, OutputIterator2 >
thrust::set_symmetric_difference_by_key(InputIterator1 keys_first1,
  InputIterator1 keys_last1,
  InputIterator2 keys_first2,
  InputIterator2 keys_last2,
  InputIterator3 values_first1,
  InputIterator4 values_first2,
  OutputIterator1 keys_result,
  OutputIterator2 values_result);

template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename InputIterator3,
  typename InputIterator4,
  typename OutputIterator1,
  typename OutputIterator2,
  typename StrictWeakCompare>
__host__ __device__ thrust::pair< OutputIterator1, OutputIterator2 >
thrust::set_symmetric_difference_by_key(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 keys_first1,
  InputIterator1 keys_last1,
  InputIterator2 keys_first2,
  InputIterator2 keys_last2,
  InputIterator3 values_first1,
  InputIterator4 values_first2,
  OutputIterator1 keys_result,
  OutputIterator2 values_result,
  StrictWeakCompare comp);

template <typename InputIterator1,
  typename InputIterator2,
  typename InputIterator3,
  typename InputIterator4,
  typename OutputIterator1,
  typename OutputIterator2,
  typename StrictWeakCompare>
thrust::pair< OutputIterator1, OutputIterator2 >
thrust::set_symmetric_difference_by_key(InputIterator1 keys_first1,
  InputIterator1 keys_last1,
  InputIterator2 keys_first2,
  InputIterator2 keys_last2,
  InputIterator3 values_first1,
  InputIterator4 values_first2,
  OutputIterator1 keys_result,
  OutputIterator2 values_result,
  StrictWeakCompare comp);
```

##### thrust::set_union_by_key

```cpp
template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename InputIterator3,
  typename InputIterator4,
  typename OutputIterator1,
  typename OutputIterator2>
__host__ __device__ thrust::pair< OutputIterator1, OutputIterator2 >
thrust::set_union_by_key(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 keys_first1,
  InputIterator1 keys_last1,
  InputIterator2 keys_first2,
  InputIterator2 keys_last2,
  InputIterator3 values_first1,
  InputIterator4 values_first2,
  OutputIterator1 keys_result,
  OutputIterator2 values_result);

template <typename InputIterator1,
  typename InputIterator2,
  typename InputIterator3,
  typename InputIterator4,
  typename OutputIterator1,
  typename OutputIterator2>
thrust::pair< OutputIterator1, OutputIterator2 >
thrust::set_union_by_key(InputIterator1 keys_first1,
  InputIterator1 keys_last1,
  InputIterator2 keys_first2,
  InputIterator2 keys_last2,
  InputIterator3 values_first1,
  InputIterator4 values_first2,
  OutputIterator1 keys_result,
  OutputIterator2 values_result);

template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename InputIterator3,
  typename InputIterator4,
  typename OutputIterator1,
  typename OutputIterator2,
  typename StrictWeakCompare>
__host__ __device__ thrust::pair< OutputIterator1, OutputIterator2 >
thrust::set_union_by_key(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 keys_first1,
  InputIterator1 keys_last1,
  InputIterator2 keys_first2,
  InputIterator2 keys_last2,
  InputIterator3 values_first1,
  InputIterator4 values_first2,
  OutputIterator1 keys_result,
  OutputIterator2 values_result,
  StrictWeakCompare comp);

template <typename InputIterator1,
  typename InputIterator2,
  typename InputIterator3,
  typename InputIterator4,
  typename OutputIterator1,
  typename OutputIterator2,
  typename StrictWeakCompare>
thrust::pair< OutputIterator1, OutputIterator2 >
thrust::set_union_by_key(InputIterator1 keys_first1,
  InputIterator1 keys_last1,
  InputIterator2 keys_first2,
  InputIterator2 keys_last2,
  InputIterator3 values_first1,
  InputIterator4 values_first2,
  OutputIterator1 keys_result,
  OutputIterator2 values_result,
  StrictWeakCompare comp);
```

### Sorting

[Reference Page](https://nvidia.github.io/thrust/api/groups/group__sorting.html)

##### thrust::sort

```cpp
template <typename DerivedPolicy,
  typename RandomAccessIterator>
__host__ __device__ void
thrust::sort(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  RandomAccessIterator first,
  RandomAccessIterator last);

template <typename RandomAccessIterator>
void
thrust::sort(RandomAccessIterator first,
  RandomAccessIterator last);

template <typename DerivedPolicy,
  typename RandomAccessIterator,
  typename StrictWeakOrdering>
__host__ __device__ void
thrust::sort(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  RandomAccessIterator first,
  RandomAccessIterator last,
  StrictWeakOrdering comp);

template <typename RandomAccessIterator,
  typename StrictWeakOrdering>
__host__ __device__ void
thrust::sort(RandomAccessIterator first,
  RandomAccessIterator last,
  StrictWeakOrdering comp);
```

##### thrust::stable_sort

```cpp
template <typename DerivedPolicy,
  typename RandomAccessIterator>
__host__ __device__ void
thrust::stable_sort(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  RandomAccessIterator first,
  RandomAccessIterator last);

template <typename RandomAccessIterator>
void
thrust::stable_sort(RandomAccessIterator first,
  RandomAccessIterator last);

template <typename DerivedPolicy,
  typename RandomAccessIterator,
  typename StrictWeakOrdering>
__host__ __device__ void
thrust::stable_sort(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  RandomAccessIterator first,
  RandomAccessIterator last,
  StrictWeakOrdering comp);

template <typename RandomAccessIterator,
  typename StrictWeakOrdering>
void
thrust::stable_sort(RandomAccessIterator first,
  RandomAccessIterator last,
  StrictWeakOrdering comp);
```

##### thrust::sort_by_key

`thrust::sort_by_key` performs a <font color="red"><b>key-value</b></font> sort. That is, `sort_by_key` sorts the elements in `[keys_first, keys_last)` and `[values_first, values_first + (keys_last - keys_first))` into ascending key order, meaning that if `i` and `j` are any two valid iterators in `[keys_first, keys_last)` such that `i` precedes `j`, and `p` and `q` are iterators in `[values_first, values_first + (keys_last - keys_first))` corresponding to `i` and `j` respectively, then `*j` is not less than `*i`.
Note: `thrust::sort_by_key` is not guaranteed to be stable. That is, suppose that `*i` and `*j` are equivalent: neither one is less than the other. It is not guaranteed that the relative order of these two keys or the relative order of their corresponding values will be preserved by `thrust::sort_by_key`.

```cpp
template <typename DerivedPolicy,
  typename RandomAccessIterator1,
  typename RandomAccessIterator2>
__host__ __device__
void thrust::sort_by_key(
    const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
    RandomAccessIterator1 keys_first,
    RandomAccessIterator1 keys_last,
    RandomAccessIterator2 values_first);
// This version of `sort_by_key` compares key objects using `operator<`.

template <typename RandomAccessIterator1,
  typename RandomAccessIterator2>
void thrust::sort_by_key(
    RandomAccessIterator1 keys_first,
    RandomAccessIterator1 keys_last,
    RandomAccessIterator2 values_first);
// This version of `sort_by_key` compares key objects using `operator<`.

template <typename DerivedPolicy,
  typename RandomAccessIterator1,
  typename RandomAccessIterator2,
  typename StrictWeakOrdering>
__host__ __device__
void thrust::sort_by_key(
    const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
    RandomAccessIterator1 keys_first,
    RandomAccessIterator1 keys_last,
    RandomAccessIterator2 values_first,
    StrictWeakOrdering comp);

template <typename RandomAccessIterator1,
  typename RandomAccessIterator2,
  typename StrictWeakOrdering>
void thrust::sort_by_key(
    RandomAccessIterator1 keys_first,
    RandomAccessIterator1 keys_last,
    RandomAccessIterator2 values_first,
    StrictWeakOrdering comp);
```

The following code demonstrates how to use `thrust::sort_by_key` to sort an array of character values using integers as sorting keys using the `thrust::host` execution policy for parallelization:

```cpp
#include <thrust/sort.h>
#include <thrust/execution_policy.h>

const int N = 6;
int keys[N] = {1, 4, 2, 8, 5, 7};
char values[N] = {'a', 'b', 'c', 'd', 'e', 'f'};
thrust::sort_by_key(thrust::host, keys, keys + N, values);
// keys is now {1, 2, 4, 5, 7, 8
// values is now {'a', 'c', 'b', 'e', 'f', 'd'}
```

##### thrust::stable_sort_by_key

```cpp
template <typename DerivedPolicy,
  typename RandomAccessIterator1,
  typename RandomAccessIterator2>
__host__ __device__ void
thrust::stable_sort_by_key(
  const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  RandomAccessIterator1 keys_first,
  RandomAccessIterator1 keys_last,
  RandomAccessIterator2 values_first);

template <typename RandomAccessIterator1,
  typename RandomAccessIterator2>
void
thrust::stable_sort_by_key(RandomAccessIterator1 keys_first,
  RandomAccessIterator1 keys_last,
  RandomAccessIterator2 values_first);

template <typename DerivedPolicy,
  typename RandomAccessIterator1,
  typename RandomAccessIterator2,
  typename StrictWeakOrdering>
__host__ __device__ void
thrust::stable_sort_by_key(
  const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  RandomAccessIterator1 keys_first,
  RandomAccessIterator1 keys_last,
  RandomAccessIterator2 values_first,
  StrictWeakOrdering comp);

template <typename RandomAccessIterator1,
  typename RandomAccessIterator2,
  typename StrictWeakOrdering>
void
thrust::stable_sort_by_key(RandomAccessIterator1 keys_first,
  RandomAccessIterator1 keys_last,
  RandomAccessIterator2 values_first,
  StrictWeakOrdering comp);
```




### Transformations

[Reference Page](https://nvidia.github.io/thrust/api/groups/group__transformations.html)

#### Filling

[Reference Page](https://nvidia.github.io/thrust/api/groups/group__filling.html)

##### thrust::fill

```cpp
template <typename DerivedPolicy,
  typename ForwardIterator,
  typename T>
__host__ __device__ void
thrust::fill(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last,
  const T & value);

template <typename ForwardIterator,
  typename T>
__host__ __device__ void
thrust::fill(ForwardIterator first,
  ForwardIterator last,
  const T & value);
```

##### thrust::fill_n

```cpp
template <typename DerivedPolicy,
  typename OutputIterator,
  typename Size,
  typename T>
__host__ __device__ OutputIterator
thrust::fill_n(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  OutputIterator first,
  Size n,
  const T & value);

template <typename OutputIterator,
  typename Size,
  typename T>
__host__ __device__ OutputIterator
thrust::fill_n(OutputIterator first,
  Size n,
  const T & value);
```

##### thrust::uninitialized_fill

```cpp
template <typename DerivedPolicy,
  typename ForwardIterator,
  typename T>
__host__ __device__ void
thrust::uninitialized_fill(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last,
  const T & x);

template <typename ForwardIterator,
  typename T>
void
thrust::uninitialized_fill(ForwardIterator first,
  ForwardIterator last,
  const T & x);
```

##### thrust::uninitialized_fill_n

```cpp
template <typename DerivedPolicy,
  typename ForwardIterator,
  typename Size,
  typename T>
__host__ __device__ ForwardIterator
thrust::uninitialized_fill_n(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  Size n,
  const T & x);

template <typename ForwardIterator,
  typename Size,
  typename T>
ForwardIterator
thrust::uninitialized_fill_n(ForwardIterator first,
  Size n,
  const T & x);
```

##### Modifying

[Reference Page](https://nvidia.github.io/thrust/api/groups/group__modifying.html)

##### thrust::for_each

```cpp
template <typename DerivedPolicy,
  typename InputIterator,
  typename UnaryFunction>
__host__ __device__ InputIterator
thrust::for_each(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator first,
  InputIterator last,
  UnaryFunction f);

template <typename InputIterator,
  typename UnaryFunction>
InputIterator
thrust::for_each(InputIterator first,
  InputIterator last,
  UnaryFunction f);
```

##### thrust::for_each_n

```cpp
template <typename DerivedPolicy,
  typename InputIterator,
  typename Size,
  typename UnaryFunction>
__host__ __device__ InputIterator
thrust::for_each_n(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator first,
  Size n,
  UnaryFunction f);

template <typename InputIterator,
  typename Size,
  typename UnaryFunction>
InputIterator
thrust::for_each_n(InputIterator first,
  Size n,
  UnaryFunction f);
```

#### Replacing

[Reference Page](https://nvidia.github.io/thrust/api/groups/group__replacing.html)

##### thrust::replace

```cpp
template <typename DerivedPolicy,
  typename ForwardIterator,
  typename T>
__host__ __device__ void
thrust::replace(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last,
  const T & old_value,
  const T & new_value);

template <typename ForwardIterator,
  typename T>
void
thrust::replace(ForwardIterator first,
  ForwardIterator last,
  const T & old_value,
  const T & new_value);
```

##### thrust::replace_if

```cpp
template <typename DerivedPolicy,
  typename ForwardIterator,
  typename Predicate,
  typename T>
__host__ __device__ void
thrust::replace_if(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last,
  Predicate pred,
  const T & new_value);

template <typename ForwardIterator,
  typename Predicate,
  typename T>
void
thrust::replace_if(ForwardIterator first,
  ForwardIterator last,
  Predicate pred,
  const T & new_value);

template <typename DerivedPolicy,
  typename ForwardIterator,
  typename InputIterator,
  typename Predicate,
  typename T>
__host__ __device__ void
thrust::replace_if(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last,
  InputIterator stencil,
  Predicate pred,
  const T & new_value);

template <typename ForwardIterator,
  typename InputIterator,
  typename Predicate,
  typename T>
void
thrust::replace_if(ForwardIterator first,
  ForwardIterator last,
  InputIterator stencil,
  Predicate pred,
  const T & new_value);
```

##### thrust::replace_copy

```cpp
template <typename DerivedPolicy,
  typename InputIterator,
  typename OutputIterator,
  typename T>
__host__ __device__ OutputIterator
thrust::replace_copy(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator first,
  InputIterator last,
  OutputIterator result,
  const T & old_value,
  const T & new_value);

template <typename InputIterator,
  typename OutputIterator,
  typename T>
OutputIterator
thrust::replace_copy(InputIterator first,
  InputIterator last,
  OutputIterator result,
  const T & old_value,
  const T & new_value);
```

##### thrust::replace_copy_if

```cpp
template <typename DerivedPolicy,
  typename InputIterator,
  typename OutputIterator,
  typename Predicate,
  typename T>
__host__ __device__ OutputIterator
thrust::replace_copy_if(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator first,
  InputIterator last,
  OutputIterator result,
  Predicate pred,
  const T & new_value);

template <typename InputIterator,
  typename OutputIterator,
  typename Predicate,
  typename T>
OutputIterator
thrust::replace_copy_if(InputIterator first,
  InputIterator last,
  OutputIterator result,
  Predicate pred,
  const T & new_value);

template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator,
  typename Predicate,
  typename T>
__host__ __device__ OutputIterator
thrust::replace_copy_if(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 first,
  InputIterator1 last,
  InputIterator2 stencil,
  OutputIterator result,
  Predicate pred,
  const T & new_value);

template <typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator,
  typename Predicate,
  typename T>
OutputIterator
thrust::replace_copy_if(InputIterator1 first,
  InputIterator1 last,
  InputIterator2 stencil,
  OutputIterator result,
  Predicate pred,
  const T & new_value);
```

#### transformations

[Reference Page](https://nvidia.github.io/thrust/api/groups/group__transformations.html)

##### thrust::adjacent_difference

`thrust::adjacent_difference` calculates the differences of adjacent elements in the range `[first, last)`. That is, `*first` is assigned to `*result`, and, for each iterator `i` in the range `[first + 1, last)`, the difference of `*i` and `*(i - 1)` is assigned to `*(result + (i - first))`.
The algorithm's execution is parallelized as determined by `exec`.


```cpp
template <typename DerivedPolicy,
  typename InputIterator,
  typename OutputIterator>
__host__ __device__
OutputIterator thrust::adjacent_difference(
    const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
    InputIterator first,
    InputIterator last,
    OutputIterator result);
// This version of `adjacent_difference` uses
// `operator-` to calculate differences.

template <typename DerivedPolicy,
  typename InputIterator,
  typename OutputIterator,
  typename BinaryFunction>
__host__ __device__ OutputIterator
thrust::adjacent_difference(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator first,
  InputIterator last,
  OutputIterator result,
  BinaryFunction binary_op);
// This version of `adjacent_difference` uses the binary
// function `binary_op` to calculate differences.

template <typename InputIterator,
  typename OutputIterator>
OutputIterator
thrust::adjacent_difference(InputIterator first,
  InputIterator last,
  OutputIterator result);

template <typename InputIterator,
  typename OutputIterator,
  typename BinaryFunction>
OutputIterator
thrust::adjacent_difference(InputIterator first,
  InputIterator last,
  OutputIterator result,
  BinaryFunction binary_op);
```

The following code demonstrates how to use `thrust::adjacent_difference` to compute the difference between adjacent elements of a range using the `thrust::device` execution policy:

```cpp
#include <thrust/adjacent_difference.h>
#include <thrust/device_vector.h>
#include <thrust/execution_policy.h>

// ...

int h_data[8] = {1, 2, 1, 2, 1, 2, 1, 2};
thrust::device_vector<int> d_data(h_data, h_data + 8);
thrust::device_vector<int> d_result(8);

thrust::adjacent_difference(thrust::device,
                            d_data.begin(),
                            d_data.end(),
                            d_result.begin());
// d_result is now [1, 1, -1, 1, -1, 1, -1, 1]
```

The following code demonstrates how 

##### thrust::generate

`thrust::generate` assigns the result of invoking `gen`, a function object that takes no arguments, to each element in the range `[first, last)`.

The algorithm's execution is parallelized as determined by `exec`.

```cpp
template <typename DerivedPolicy,
  typename ForwardIterator,
  typename Generator>
__host__ __device__ void
thrust::generate(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last,
  Generator gen);
// exec --- The execution policy to use for parallelization.
// first --- The first element in the range of interest.
// last --- The last element in the range of interest.
// gen --- A function argument, taking no parameters, used to generate
//         values to assign to elements in the range [first, last).

template <typename ForwardIterator,
  typename Generator>
void
thrust::generate(ForwardIterator first,
  ForwardIterator last,
  Generator gen);
```

* Template Parameters:

`DerivedPolicy` The name of the derived execution policy.
`ForwardIterator` is a model of `Forward Iterator`, and `ForwardIterator` is mutable.
`Generator` is a model of `Generator`, and `Generator`'s `result_type` is convertible to `ForwardIterator`'s `value_type`.

The following code shows how to fill a `thrust::host_vector` with random numbers, using the standard C library function `rand` using the `thrust::host` execution policy for parallelization:

```cpp
#include <thrust/generate.h>
#include <thrust/host_vector.h>
#include <thrust/execution_policy.h>
#include <cstdlib>

thrust::host_vector<int> v(10);
srand(13);
thrust::generate(thrust::host, v.begin(), v.end(), srand);
// the elements of v are now pseudo-random numbers.
```

##### thrust::generate_n

```cpp
template <typename DerivedPolicy,
  typename OutputIterator,
  typename Size,
  typename Generator>
__host__ __device__ OutputIterator
thrust::generate_n(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  OutputIterator first,
  Size n,
  Generator gen);

template <typename OutputIterator,
  typename Size,
  typename Generator>
OutputIterator
thrust::generate_n(OutputIterator first,
  Size n,
  Generator gen);
```

##### thrust::sequence

`sequence` fills the range `[first, last)` with a sequence of numbers. For each iterator `i` in the range `[first, last)`, this version `sequence` performs the assignment `*i = (i - first)`. The algorithm's execution is parallelized as determined by `exec`.

```cpp
template <typename DerivedPolicy,
  typename ForwardIterator>
__host__ __device__ void
thrust::sequence(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last);

template <typename ForwardIterator>
void
thrust::sequence(ForwardIterator first,
  ForwardIterator last);

template <typename DerivedPolicy,
  typename ForwardIterator,
  typename T>
__host__ __device__ void
thrust::sequence(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last,
  T init);

template <typename ForwardIterator,
  typename T>
void
thrust::sequence(ForwardIterator first,
  ForwardIterator last,
  T init);

template <typename DerivedPolicy,
  typename ForwardIterator,
  typename T>
__host__ __device__ void
thrust::sequence(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last,
  T init,
  T step);

template <typename ForwardIterator,
  typename T>
void
thrust::sequence(ForwardIterator first,
  ForwardIterator last,
  T init,
  T step);
```

The following code snippet demonstrates how to use `sequence` to fill a range with a sequence of numbers using the `thrust::host` execution policy for parallelization:

```cpp
#include <thrust/sequence.h>
#include <thrust/execution_policy.h>

const int N = 10;
int A[N];
thrust::sequence(thrust::host, A, A + 10);
// A is now {0, 1, 2, 3, 4, 5, 6, 7, 8, 9}
```

Note: Unlike the similar C++ STL function `std::iota`, `sequence` offers no guarantee on order of execution.

Template Parameters:
* `DerivedPolicy` The name of the derived execution policy.
* `ForwardIterator` is a model of `Forward Iterator`, and `ForwardIterator` is mutable, and if `x` and `y` are objects of `ForwardIterator`'s `value_type`, then `x + y` is defined, and if `T` is `ForwardIterator`'s `value_type`, then `T(0)` is defined.


##### thrust::tabulate

`tabulate` fills the range `[first, last)` iwth the value of a function applied to each element's index.

```cpp
template <typename DerivedPolicy,
  typename ForwardIterator,
  typename UnaryOperation>
__host__ __device__ void
thrust::tabulate(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  ForwardIterator first,
  ForwardIterator last,
  UnaryOperation unary_op);

template <typename ForwardIterator,
  typename UnaryOperation>
void
thrust::tabulate(ForwardIterator first,
  ForwardIterator last,
  UnaryOperation unary_op);
```

For each iterator `i` in the range `[first, last)`, `tabulate` performs the assignment `*i = unary_op(i - first)`. The algorithm's execution is parallelized as determined by `exec`. The following code demonstrates how to use `tabulate` to generate the first `n` non-positive integers using the `thrust::host` execution policy for parallelization:

```cpp
#include <thrust/tabulate.h>
#include <thrust/functional.h>

const int N = 10;
int A[N];
thrust::tabulate(thrust::host, A, A + 10, thrust::negate<int>());
// A is now {0, -1, -2, -3, -4, -5, -6, -7, -8, -9}
```

##### thrust::transform
`transform` applies a unary function to each element of an input sequence and stores the result in the corresponding position in an output sequence. Specifically, for each iterator `i` in the range `[first, last)` the operation `op(*i)` is performed and the result is assigned to `*o`, where `o` is the corresponding output iterator in the range `[result, result + (last - first))`. The input and output sequences may coincide, resulting in an in-place transformation.

```cpp
template <typename DerivedPolicy,
  typename InputIterator,
  typename OutputIterator,
  typename UnaryFunction>
__host__ __device__ OutputIterator
thrust::transform(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator first,
  InputIterator last,
  OutputIterator result,
  UnaryFunction op);

template <typename InputIterator,
  typename OutputIterator,
  typename UnaryFunction>
OutputIterator
thrust::transform(InputIterator first,
  InputIterator last,
  OutputIterator result,
  UnaryFunction op);

template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator,
  typename BinaryFunction>
__host__ __device__ OutputIterator
thrust::transform(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  OutputIterator result,
  BinaryFunction op);

template <typename InputIterator1,
  typename InputIterator2,
  typename OutputIterator,
  typename BinaryFunction>
OutputIterator
thrust::transform(InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  OutputIterator result,
  BinaryFunction op);
```

The following code snippet demostrates how to use `transform` to negate a range in-place using the `thrust::host` execution policy for parallelization:

```cpp
#include <thrust/transform.h>
#include <thrust/functional.h>
#include <thrust/execution_policy.h>

int data[10] = {-5, 0, 2, -3, 2, 4, 0, -1, 2, 8};

thrust::negate<int> op;
// in-place transformation.
thrust::transform(thrust::host, data, data + 10, data, op);
// data is now {5, 0, -2, 3, -2, -4, 0, 1, -2, -8};
```


##### thrust::transform_if

```cpp
template <typename DerivedPolicy,
  typename InputIterator,
  typename ForwardIterator,
  typename UnaryFunction,
  typename Predicate>
__host__ __device__ ForwardIterator
thrust::transform_if(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator first,
  InputIterator last,
  ForwardIterator result,
  UnaryFunction op,
  Predicate pred);

template <typename InputIterator,
  typename ForwardIterator,
  typename UnaryFunction,
  typename Predicate>
ForwardIterator
thrust::transform_if(InputIterator first,
  InputIterator last,
  ForwardIterator result,
  UnaryFunction op,
  Predicate pred);

template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename ForwardIterator,
  typename UnaryFunction,
  typename Predicate>
__host__ __device__ ForwardIterator
thrust::transform_if(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 first,
  InputIterator1 last,
  InputIterator2 stencil,
  ForwardIterator result,
  UnaryFunction op,
  Predicate pred);

template <typename InputIterator1,
  typename InputIterator2,
  typename ForwardIterator,
  typename UnaryFunction,
  typename Predicate>
ForwardIterator
thrust::transform_if(InputIterator1 first,
  InputIterator1 last,
  InputIterator2 stencil,
  ForwardIterator result,
  UnaryFunction op,
  Predicate pred);

template <typename DerivedPolicy,
  typename InputIterator1,
  typename InputIterator2,
  typename InputIterator3,
  typename ForwardIterator,
  typename BinaryFunction,
  typename Predicate>
__host__ __device__ ForwardIterator
thrust::transform_if(const thrust::detail::execution_policy_base< DerivedPolicy > & exec,
  InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  InputIterator3 stencil,
  ForwardIterator result,
  BinaryFunction binary_op,
  Predicate pred);

template <typename InputIterator1,
  typename InputIterator2,
  typename InputIterator3,
  typename ForwardIterator,
  typename BinaryFunction,
  typename Predicate>
ForwardIterator
thrust::transform_if(InputIterator1 first1,
  InputIterator1 last1,
  InputIterator2 first2,
  InputIterator3 stencil,
  ForwardIterator result,
  BinaryFunction binary_op,
  Predicate pred);
```




#### thrust::transform_if

##### Definition

This version of `transform_if` conditionally applies a unary function to each element of an input sequence and stores the result in the corresponding position in an output sequence if the corresponding position in the input sequence satisfies a predicate. Otherwise, the corresponding position in the output sequence is not modified. The definition is as follows:
```cpp
template <typename DerivedPolicy,
	typename InputIterator,
	typename ForwardIterator,
	typename UnaryFunction,
	typename Predicate>
__host__ __device__ ForwardIterator
transform_if(const thrust::detail::execution_policy_base<DerivedPolicy> &exec,
            InputIterator first,
            InputIterator last,
            ForwardIterator result,
            UnaryFunction op,
            Predicate pred);
```

Specifically, for each iterator `i` in the range `[first, last)` the predicate `pred(*i)` is evaluated. If this predicate evaluates to `true`, the result of `op(*i)` is assigned to `*o`, where `o` is the corresponding output iterator in the range `[result, result + (last - first) )`. Otherwise, `op(*i)` is not evaluated and no assignment occurs. The input and output sequences may coincide, resulting in an in-place transformation.

##### Examples

```cpp
template <typename Vector>
transform_if_test1() {
  typedef typename Vector::value_type T;
  
  Vector input(3);
  Vector output(3);
  Vector result(3);
  input[0] = 1; input[1] = -2; input[2] = 3;
  result[0] = -1; result[1] = 2; result[2] = -3;
  
  auto iter = thrust::transform(input.begin(), input.end(),
                                output.begin(),
                                thrust::negate<T>());
  // EXPECT_EQ(std::size_t(iter - output.begin()), input.size());
  // EXPECT_EQ(output, result);
}
```


## Iterators
TODO

## Policies

```cpp
const detail::host_t thrust::host
```
`thrust::host` is the default parallel execution policy associated with Thrust's host backend system configured by the `THRUST_HOST_SYSTEM` macro.
Instead of relying on implicit algorithm dispatch through iterator system tags, users may directly target algorithm dispatch at Thrust's host system by providing `thrust::host` as an algorithm parameter.
Explicit dispatch can be useful in avoiding the introduction of data copies into containers such as `thrust::host_vector`.

```cpp
#include <thrust/for_each.h>
#include <thrust/execution_policy.h>
#include <cstdio>

struct printf_functor {
  __host__ __device__
  void operator()(int x) { printf("%d\n", x); }
};

// ...
int vec(3);
vec[0] = 0; vec[1] = 1; vec[2] = 2;

thrust::for_each(thrust::host, vec.begin(), vec.end(),
                 printf_functor());
// 0 1 2 is printed to standard output in some unspecified order.
```

### thrust::device

`thrust::device` is the default parallel execution policy associated with Thrust's device backend system configured by the `THRUST_DEVICE_SYSTEM` macro.
Instead of relying on implicit algorithm dispatch through iterator system tags, users may directly target algorithm dispatch at Thrust's device system by providing `thrust::device` as an algorithm parameter.
Explicit dispatch can be useful in avoiding the introduction of data copies into containers such as `thrust::device_vector` or to avoid wrapping e.g. raw pointers allocated by the CUDA API with types such as `thrust::device_ptr`.

## Function Objects

### Function Object Adaptors

There are several functions defined in `thrust/functional.h` header file.

#### thrust::plus
`thrust::plus` is a function object. Specifically, it is an Adaptable Binary Function.

```cpp
template <typename T = void>
struct plus {
  // ...
  __host__ __device__
  constexpr T operator()(const T &lhs, const T &rhs) const {
    return lhs + rhs;
  }
};
```

#### thrust::minus

`thrust::minus` is a function object. Specifically, it is an Adaptable Binary Function.

```cpp
template <typename T = void>
struct minus {
  // ...
  __host__ __device__
  constexpr T operator()(const T &lhs, const T &rhs) const {
    return lhs - rhs;
  }
};
```

#### thrust::multiplies

TODO

```cpp
template <typename T = void>
struct multiplies {
  // ...
  __host__ __device__
  constexpr T operator()(const T &lhs, const T &rhs) const {
    return lhs * rhs;
  }
};
```

#### thrust::divides

TODO

```cpp
template <typename T = void>
struct divides {
  // ...
  __host__ __device__
  constexpr T operator()(const T &lhs, const T &rhs) const {
    return lhs / rhs;
  }
};
```

#### thrust::modulus

TODO

```cpp
template <typename T = void>
struct modulus {
  // ...
  __host__ __device__
  constexpr T operator()(const T &lhs, const T &rhs) const {
    return lhs % rhs;
  }
};
```

#### thrust::negate

TODO

```cpp
template <typename T = void>
struct negate {
  // ...
  __host__ __device__
  constexpr T operator()(const T &x) const { return -x; }
};
```

#### thrust::square

TODO

```cpp
template <typename T = void>
struct square {
  // ...
  __host__ __device__
  constexpr T operator()(const T &x) const { return x * x; }
};
```

#### thrust::equal_to

TODO

```cpp
template <typename T = void>
struct equal_to {
  __host__ __device__
  constexpr bool operator()(const T &lhs, const T &rhs) const {
    return lhs == rhs;
  }
};
```

#### thrust::not_equal_to

TODO

```cpp
template <typename T = void>
struct not_equal_to {
  __host__ __device__
  constexpr bool operator()(const T &lhs, const T &rhs) const {
    return lsh != rhs;
  }
};
```

#### thrust::greater

TODO

```cpp
template <typename T = void>
struct greater {
  __host__ __device__
  constexpr bool operator()(const T &lhs, const T &rhs) const {
    return lsh > rhs;
  }
};
```

#### thrust::less

TODO

```cpp
template <typename T = void>
struct less {
  __host__ __device__
  constexpr bool operator()(const T &lhs, const T &rhs) const {
    return lsh < rhs;
  }
};
```

#### thrust::greater_equal

TODO

```cpp
template <typename T = void>
struct greater_equal {
  __host__ __device__
  constexpr bool operator()(const T &lhs, const T &rhs) const {
    return lsh >= rhs;
  }
};
```

#### thrust::less_equal

TODO

```cpp
template <typename T = void>
struct less_equal {
  __host__ __device__
  constexpr bool operator()(const T &lhs, const T &rhs) const {
    return lsh <= rhs;
  }
};
```

#### thrust::logical_and

TODO

```cpp
template <typename T = void>
struct logical_and {
  __host__ __device__
  constexpr bool operator()(const T &lhs, const T &rhs) const {
    return lsh && rhs;
  }
};
```

#### thrust::logical_or

TODO

```cpp
template <typename T = void>
struct logical_or {
  __host__ __device__
  constexpr bool operator()(const T &lhs, const T &rhs) const {
    return lsh || rhs;
  }
};
```

#### thrust::logical_not

TODO

```cpp
template <typename T = void>
struct logical_not {
  __host__ __device__
  constexpr T operator()(const T &x) const { return !x; }
};
```

#### thrust::bit_and

TODO

```cpp
template <typename T = void>
struct bit_and {
  __host__ __device__
  constexpr T operator()(const T &lhs, const T &rhs) const {
    return lsh & rhs;
  }
};
```

#### thrust::bit_or

TODO

```cpp
template <typename T = void>
struct bit_or {
  __host__ __device__
  constexpr T operator()(const T &lhs, const T &rhs) const {
    return lsh | rhs;
  }
};
```

#### thrust::bit_xor

TODO

```cpp
template <typename T = void>
struct bit_xor {
  __host__ __device__
  constexpr T operator()(const T &lhs, const T &rhs) const {
    return lsh ^ rhs;
  }
};
```

#### thrust::identity

TODO

```cpp
template <typename T = void>
struct identity {
  __host__ __device__
  constexpr const T operator()(const T &x) const { return x; }
};
```

#### thrust::maximum

TODO

```cpp
template <typename T = void>
struct maximum {
  __host__ __device__
  constexpr T operator()(const T &lhs, const T &rhs) const {
    return lhs < rhs ? rhs : lhs;
  }
};
```

#### thrust::minimum

TODO

```cpp
template <typename T = void>
struct minimum {
  __host__ __device__
  constexpr T operator()(const T &lhs, const T &rhs) const {
    return lhs < rhs ? lhs : rhs;
  }
};
```

#### thrust::project1st

TODO

```cpp
template <typename T1 = void, typename T2 = void>
struct project1st {
  __host__ __device__
  constexpr T1 &operator()(const T1 &lhs, const T2 &/*rhs*/) const {
    return lhs;
  }
};
```

#### thrust::project2nd

TODO

```cpp
template <typename T1 = void, typename T2 = void>
struct project2nd {
  __host__ __device__
  constexpr T2 &operator()(const T1 &/*lhs*/, const T2 &rhs) const {
    return rhs;
  }
};
```

#### thrust::unary_negate

TODO

```cpp
template <typename Predicate>
struct unary_negate
    : public thrust:unary_function<typename Predicate::argument_type, bool> {
  __host__ __device__
  explicit unary_negate(Predicate p) : pred(p) {}
  
  __host__ __device__
  bool operator()(const typename Predicate::argument_type &x) {
    return !pred(x);
  }
  
  Predicate pred;
};
```

#### thrust::not1

TODO

```cpp
template <typename Predicate>
__host__ __device__
unary_nagate<Predicate> not1(const Predciate &pred);
```

#### thrust::binary_negate

TODO

```cpp
template <typename Predicate>
struct binary_negate
    : public thrust::binary_function<typename Predicate::first_argument_type,
                                     typename Predicate::second_argument_type,
                                     bool> {
  __host__ __device__
  explicit binary_negate(Predicate p) : pred(p) {}
  
  __host__ __device__
  bool operator()(const typename Predicate::first_argument_type &x,
                  const typename Predicate::second_argument_type &y) {
    return !pred(x, y);
  }
};
```

#### thrust::not2

TODO

```cpp
template <typename BinaryPredicate>
__host__ __device__
binary_negate<BinaryPredicate> not2(const BinaryPredicate &pred);
```

#### placeholders

Objects in the `thrust::placeholders` namespace may be used to create simple arithmetic functions inline in an algorithm invocation. Combining placeholders such as `_1` and `_2` with arithmetic operations such as `+` creates an unnamed function object which applies the operation to their arguments. The type of placeholder object is implementation-defined.

TODO
The following code shows how to use `placeholder`.

```cpp
#include <thrust/device_vector.h>
#include <thrust/transform.h>
#include <thrust/functional.h>

int main() {
  thrust::device_vector<float> x(4), y(4);
  x[0] = 1;
  x[1] = 2;
  x[2] = 3;
  x[3] = 4;
  
  x[0] = 1;
  x[1] = 1;
  x[2] = 1;
  x[3] = 1;
  
  float a = 2.0f;
  
  using namespace thrust::placeholders;
  // The SAXPY computation.
  thrust::transform(x.begin(), x.end(), y.begin(), y.begin(),
                    a * _1 + _2);
  
  for (auto val : y)
    std::cout << val << ' ';
  std::cout << std::endl;
  
  return 0;
}
```

## Containers

### thrust::pair

`thrust::pair` is a generic data structure encapsulating a heterogeneous pair of values.

The definition is as follows.
```cpp
template <typename T1, typename T2>
struct pair {
  typedef T1 first_type;
  typedef T2 second_type;
  
  first_type first;
  second_type second;
  
  __host__ __device__ pair(void);
  
  // constructor
  inline __host__ __device__
  pair(const T1 &x, const T2 &y);
  
  // copy constructor.
  template <typename U1, typename U2>
  inline __host__ __device__
  pair(const pair<U1, U2> &p);
  
  // copy constructor. Copies from a std::pair
  template <typename U1, typename U2>
  inline __host__ __device__
  pair(const std::pair<U1, U2> &p);
  
  // swap two elements
  inline __host__ __device__
  void swap(pair &p);
};
```

# References
- [Thrust Introduction](https://indico.truba.gov.tr/event/84/attachments/173/379/Day%202-%20Session_3.pdf)