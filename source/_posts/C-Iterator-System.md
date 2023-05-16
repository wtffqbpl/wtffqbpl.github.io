---
title: C++ Iterator System
toc: true
date: 2023-05-14 11:20:57
cover: /img/cpp/cpp-basics-5.jpg
excerpt: My C++ best practices.
tags:
- C++
- C++11
- C++14
- C++17
- C++20
---

# Categories

Iterators can be distinguished by the form of access:
* **InputIterator**: an iterator concept for reading the referred entries (but only once).
* **OutputIterator**: an iterator concept for writing to the referred entries (but only once).

Note that the ability to write doesn't imply readability; e.g., an `ostream_iterator` is an STL interface used for writing to output streams like `std::cout` or output files.

Categories of the form of traversal:

* **ForwardIterator**: a concept for iterators that can pass from one element to the next, i.e., types that provide an `operator++`. It is a refinement of `InputIterator` and `OutputIterator`. In contrast to those, `ForwardIterator` allows for reading values twice and for traversing multiple times.
* **BidirectionalIterator**: a concept for iterators with step-wise forward and backward traversal, i.e., types with `operator++` and `operator--`. It refines `ForwardIterator`.
* **RandomAccessIterator**: a concept for iterators to which an arbitrary positive or negative offset can be added, i.e., types that additionally provide `operator[]`. it refines `BidirectionalIterator`.

A clever design choice of all iterator interfaces was that their operations are also provided by pointers. Every pointer models `RandomAccesIterator` so that all STL algorithms can be applied on old-style arrays via pointers.

| Iterator Category     | Write    | Read     | Increment  | Decrement | Random Access | Contiguous Storage |
|-----------------------|----------|----------|------------|-----------|---------------|--------------------|
| OutputIterator        | Required |          | Required   |           |               |                    |
| InputIterator         |          | Required | Required   |           |               |                    |
| ForwardIterator       |          | Required | Required   |           |               |                    |
| BidirectionalIterator |          | Required | Required   | Required  |               |                    |
| RandomAccessIterator  |          | Required | Required   | Required  | Required      |                    |
| ContiguousIterator    |          | Required | Required   | Required  | Required      | Required           |

