---
title: C++ STL Overview
toc: true
date: 2022-11-17 00:34:10
cover: /img/cpp/cpp-basics-5.jpg
excerpt: If you want to know all STL algorithms, or you want to learn how to master STL algorithms. Please click this article for more details.
tags:
  - STL
  - C++
---

**STL algorithms can make code more expressive**
* Raising levels of abstraction
* Sometimes, it can be spectacular.

**Avoid common mistakes**
* off-by-one
* empty loops
* naive complexity

When writing our own for-loops is about complexity. Some algorithms that they can be implemented naively in quadratic complexity, and in a more astute manner in a linear complexity.

**Used by lots of people**
* A common vocabulary
* Whatever the version of your compiler

  
Also, STL algorithms are used by billions of people every day. You don't need to afraid of them. I think we should level up to STL algorithms and not the other way around.
So even if you are running an older C++ version, you can still get most of the STL algorithms, like for example, if you are in C++03 for example, and you want to use `all_of`, `any_of` or `none_of` which appeared in C++11. Well you can just grab that code, and copy it into your codebase and just start using them. So most of them are within your reach whichever version of C++ you are on right now.

Many more of the STL algorithms that let you express more nuance when you write your code. I'm going to show you something to illustrate the fact that there's much more than `for_each` in the STL algorithms library.

Here is the world of C++ STL algorithms

![The World of C++ STL Algorithms](https://raw.githubusercontent.com/wtffqbpl/blog-images/main/stl_map.png)

The `Queries` means the stl algorithms which in this area just extract some piece of information, and they don't actually modify it. The most of the stl algorithms would somehow change the collection, like sort or partition or whatever. But most of them are just `read-only` operations.

# Heaps

A `Heap` is a data structure that looks like a tree, but that has a property is that every node must be smaller than its children. This is called `max heap`.
![Heaps-image](https://raw.githubusercontent.com/wtffqbpl/blog-images/main/20221117010011.png)
So one extremely interesting property of the heap is taht we can squash it down into a range like below.
![](https://raw.githubusercontent.com/wtffqbpl/blog-images/main/20221117010400.png)

## std::make_heap
When you do this, there is a nice property that it's a range. So we can represent it with a vector, for example which is quite convenient to work with. And also, to go froma node to its left child, it is essentially taking its index and multiplying it by twos, nearly that. So it's a way to represent a tree into a range. If you have a range, we have a beginning and an end and we can use STL algorithms.
So first thing we can do is taking values that are not particularly a heap, and rearrange them so that they respect the heap property. We use `std::make_heap` to make a heap. We rearrange the element inside of a collection. This is our first algorithm.
![](https://raw.githubusercontent.com/wtffqbpl/blog-images/main/20221117010904.png)

## std::push_heap

When we want to add a new value into a heap, we should move up the new value to right position in case to meet the heap criteria.
![](https://raw.githubusercontent.com/wtffqbpl/blog-images/main/20221117011138.png)

When we squash the heap into a vector, we add something to the end, and then we need something to make it bubble its way up to its final position, and that is `std::push_heap`.
![](https://raw.githubusercontent.com/wtffqbpl/blog-images/main/20221117011400.png)

## std::pop_heap

When we want to remove something from the heap. Usually we want to get the maximum value from this heap, `std::pop_heap` can be used to get this maximum value.
![](https://raw.githubusercontent.com/wtffqbpl/blog-images/main/20221117011803.png)
To do this, we swap the first one and the last one. At this point, we need to make it bubble its way down to its final position, and that's what pop_heap does. And if we want to remove, actually remove that value, we pop it back from the vector.

If we pop each value from heap, then we get a sorted collection, and that is what `std::sort_heap`.

# SORTING

## std::sort
Rearrange the collection, and we get a sorted order collection.

## std::partial_sort

Now if we only want to sort the beginning of a collection, this is a `partial_sort`. partial sort is kind of like a middle, something inside of the collection, and sorts the collection from the beginning to that point, and the rest is in unspecified order.
![](https://raw.githubusercontent.com/wtffqbpl/blog-images/main/20221117012627.png)

## std::nth_element
`nth_element` is a partial sorting algorithm that rearranges elements in [first, last).
```cpp
// Since C++20
template <typename RandomIt>
void nth_element(RandomIt first, RandomIt nth,
                 RandomIt last);

// Since C++20
template <typename RandomIt, typename Compare>
void nth_element(RandomIt first, RandomIt nth,
                 RandomIt last, Cmpare comp);

// Since C++17
template <typename ExecutionPolicy, typename RandomIt>
void nth_element(ExecutionPolicy&& policy,
                 RandomIt first, RandomIt nth,
                 RandomIt last);

// Since C++20
template <typename RandomIt>
constexpr void nth_element(RandomIt first, RandomIt nth,
                           RandomIt last);

// Since C++17
template <typename ExecutionPolicy, typename RandomIt,
          typename Compare>
void nth_element(ExecutionPolicy&& policy,
                 RandomIt first, RandomIt nth,
                 RandomIt last, Compare comp);
```
![](https://raw.githubusercontent.com/wtffqbpl/blog-images/main/20221117013337.png)

## std::sort_heap

## std::inplace_merge
`inplace_merge` is the incremental step in a merge sort. Merges two consecutive sorted ranges [first, middle) and [middle, last) into one sorted range [first, last).

```cpp
// Since C++17
template <typename ExecutionPolicy, typename BidirIt>
void inplace_merge(ExecutionPolicy&& policy, BidirIt first,
                   BidirIt middle, BidirIt last);

// Since C++17
template <typename ExecutionPolicy, typename BidirIt,
          typename Compare>
void inplace_merge(ExecutionPolicy&& policy, BidirIt first,
                   BidirIt middle, BidirIt last,
                   Compare comp);
```

# PARTITIONING
To partition a collection is to look at it through a predicate, something that returns a `Boolean`. If the _blue_ is the predicates. The original collection is depicted as follows.
![](https://raw.githubusercontent.com/wtffqbpl/blog-images/main/20221117014115.png)

Then we do partition, then the predicates are at the beginning, and other not-blue ones at the end.
![](https://raw.githubusercontent.com/wtffqbpl/blog-images/main/20221117014423.png)

The border between the blue ones and the not-blue ones is called a partition point, that's the end of the blue range and that's also the beginning of the not blue range.

