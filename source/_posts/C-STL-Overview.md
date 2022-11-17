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

# PERMUTATIONS
The elements move around the collection, even if they don't change value.
![](https://raw.githubusercontent.com/wtffqbpl/blog-images/main/20221117124819.png)

## std::rotate

```cpp
// since C++11 and until C++20
template <typename ForwardIt>
ForwardIt rotate(ForwardIt first, ForwardIt n_first, ForwardIt last);

// since C++17
template <typename ExecutionPolicy, class ForwardIt>
ForwardIt rotate(ExecutionPolicy&& policy,
                 ForwardIt first, ForwardIt n_first,
                 ForwardIt last);
```
Performs a left rotation on a range of elements. Specifically, `std::rotate` swaps the elements in the range[first, last) in such a way that the element n_first becomes the first element of the new range and `n_first - 1` becomes the last element.

## std::shuffle
`std::shuffle` is used to randomly change elements in a container. So `std::shuffle` takes a collection with elements in a certain order, and takes something that generates random numbers, and rearranges the collection in random order. 

## std::next_permutation/std::prev_permutation
Given a collection of object, we can order them, order all their possible arrangements, and an intuitive way to see that is thinking about an alphabetical order.

```cpp
// until C++20
template <typename BidirIt>
bool next_permutation(BidirIt first, BidirIt last);

// until C++20
template <typename BidirIt, typename Compare>
bool next_permutation(BidirIt first, BidirIt last,
                      Compare comp);

// since C++20
template <typename BidirIt>
constexpr bool next_permutation(BidirIt first,
                                BidirIt last);

// since C++20
template <typename BidirIt, typename Compare>
constexpr bool next_permutation(BidirIt first, BidirIt last,
                                Compare comp);
```
Permutes the range [first, last) into the next permutation, where the set of all permutations is ordered lexicographically with respect to operator `<` or comp. Returns true if such a "next permutation" exists; otherwise transforms the range into the lexicographically first permutation (as if by `std::sort(first, last, comp)`) and returns `false`.
That's interesting for every possible arrangement of a collection, we can repeatedly call `std::next_permutation` until you've cycled back to the beginning.

# SECRET RUNES
The secret runes are things that you can combine with other algorithms to generate new algorithms. I called them runs because it's like going to see runes to augment your powers and also because I find that kind of cool as a name.

## PARTITIONING-SORT-HEAP

### stable_* rune
When we will see all the algorithm that go with the `stable` rune, so `stable` when you tack it onto an algorithm, it does what this algorithm does, but keep the relative order. Think `std::partition` for example, it put all the blue ones at the beginning that's for sure, but maybe in the process, some of them got swapped around. With `std::stable_partition`, they all keep the same relative order. Same thing with `std::stable_sort`.
```
stable_* ---> stable_sort
         \
           --> stable_partition
```

### is_* rune

The `is_*` rune checks for a predicate on the collection. We know what sort, partition, heap, means is_sort/is_partition/is_heap and returns a boolean. To indicate whether it's a sorted/partitioned/heap that we are looking at.
```
is_*   -----| is_sorted
            | is_partitioned 
            | is_heap
```

### is_*_until

`is_*_until` returns an iterator that's the first position where that predicate doesn't hold anymore. So for example, if we have a sorted collection, and then we call `std::is_sorted_until` we'll get the end of that collection.
```
is_*_until -----| is_sorted_until
                | is_partitioned_until
                | is_heap_until
```

# QUERIES
So one thing you can extract out of a collection is some sort of value.

## std::count
For example, the simplest one is probably `std::count` that takes a begin and an end a value, and returns how many times this value occurs in the collection.
![](https://raw.githubusercontent.com/wtffqbpl/blog-images/main/20221117131456.png)

## std::accumulate/(transform_)reduce
The `accumulate` makes some elements of the collection calling operator plus, or any custom function you'd pass it. In C++17, there's `std::reduce` appeared, that does practically the same thing as accumulate, except it has a slightly different interface. It can accept to take no initial value, and it can also be run in parallel, but essentially, it's the same as `std::accumulate`.

`std::transform_reduce` takes a function and applies that function to the element of the collection before calling the `reduce`.
![](https://raw.githubusercontent.com/wtffqbpl/blog-images/main/20221117131511.png)

## std::partial_sum
![](https://raw.githubusercontent.com/wtffqbpl/blog-images/main/20221117133825.png)
I think this is the `pre-sum` algorithm in std algorithms.
```cpp
// until C++20
template <typename InputIt, typename OutputIt>
OutputIt partial_sum(InputIt first, InputIt last,
                     OutputIt d_first);
// until C++20
template <typename InputIt, typename OutputIt,
          typename BinaryOperation>
OutputIt partial_sum(InputIt first, InputIt last,
                     OutputIt d_first,
                     BinaryOperation op);
// since C++20
template <typename InputIt, typename OutputIt>
OutputIt partial_sum(InputIt first, InputIt last,
                     OutputIt d_first);

// since C++20
template <typename InputIt, typename OutputIt,
          typename BinaryOperation>
constexpr OutputIt partial_sum(InputIt first,
                               InputIt last,
                               OutputIt d_first,
                               BinaryOperation op);
```
`std::partial_sum`, it sums the all the elements starting from the beginning to the current point of the collection. So here, `partial_sum` would return a collection with, in the first position, what was in the first position of the original collection. and the second one would plus the seco

Computes the partial sums of the elements in the sub-ranges of the range[first, last) and writes them to the range beginning at `d_dirst`. This is equivalent operation:
```cpp
*(d_first)     = *first;
*(d_first + 1) = *first + *(first + 1);
*(d_first + 2) = *first + *(first + 1) + *(first + 2);
*(d_first + 3) = *first + *(first + 1) + *(first + 2) + *(first + 3);
// ...
```

In C++17, there are `std::(transform_)inclusive_scan` and `std::(transform_)exclusive_scan`. `inclusive_scan` is the same thing as `partial_sum`, except it can run in parallel. `exclusive_scan` is the same as `inclusive_scan`, except it doesn't include the current element. So for example, with `exclusive_scan`, the second value is the same to the first element of the original collection. The third would be one plus second, so-on and so-forth.

```cpp
// *(d_first)     = ;
*(d_first + 1) = *first;
*(d_first + 2) = *first + *(first + 1);
*(d_first + 3) = *first + *(first + 1) + *(first + 2);
// ...
```


