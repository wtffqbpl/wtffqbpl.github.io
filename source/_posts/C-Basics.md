---
title: C++ Basics
toc: true
date: 2023-05-14 11:54:22
cover: /img/cpp/cpp-basics-5.jpg
excerpt: C++ Basic knowledges.
tags:
- C++
- C++14
- C++17
- C++20
---

# Template and Generic Programming

## Aims
* An understanding template purpose and semantics.
* A working knowledge of function and type template syntax in C++
* An understanding static polymorphism and how it is implemented using CRTP.
* An introduction to type traits, the basis of C++ metaprogramming.
* An understanding of different approaches to compile-time conditional coding.
* An understanding of policy classes.
* An understanding of how to implement perfect forwarding with variadic parameters and forwarding references.
* An understanding of how to determine the types of variables whose types are deduced.
* An understanding of the goals of Generic Programming.
* An understanding of the process of developing code using the Generic Programming paradigm.
* An understanding of how to use the STL to model and solve a real-world problem.
* Familiarity with the algorithms provided in the Standard Library.

## Outlines
* Template Syntax and Mechanics
  * Motivation
  * Template Instantiation
  * Argument Deduction
  * Two-phase Compilation
  * Dependent Types
  * Non-Type Template Parameters
  * Overloading Function Templates
    * Function Template Partial Ordering
  * Class Templates
    * Explicit Specialization
    * Partial Specialization
    * Dependent Base
* Modern Template Techniques
  * The Template Challenge
  * CRTP – Static Polymorphism
  * Type Traits – Basic Metaprogramming
  * Compile-time Conditional Overloading
  * Policy Classes
  * Perfect Forwarding
  * Viewing Deduced Types
* Introduction to Generic Programming
  * Alex Stepanov
  * Programming = Math + Memory
  * Algorithms
  * Abstraction
  * Generic Library Design
  * Generalizing Linear Search
  * Concepts and Models
    * Valid Expressions
    * Associated Types
    * Associate Semantics
  * Fundamental Concepts
  * Iterator Concepts
    * Input Iterator
    * Output Iterator
    * Forward Iterator
    * Bidirectional Iterator
    * Random Access Iterator
* Using the Standard Template Library
  * Example Domain: Graphs
* Survey of the Standard Algorithms
  * Non-modifying and Modifying
  * Minimum and Maximum
  * Permutation, Sorting, and Partitioning
  * Set, Heap, and Numeric