---
title: Aggregate Initialization
toc: true
date: 2022-11-13 18:35:17
cover: /img/cpp/cpp-basics-5.jpg
excerpt: You have already known about list initialization introduced in C++11. But if a class inherit from another aggregate class, how do you initialize the variables in base class using list initialization? If you don't know, please click this article, and know how to do this.
tags:
  - C++17
  - C++20
---

An aggregate is one of the following types:
* array type
* class type (typically, `struct` or `union`), that has
  * no private or protected non-static data members
  * no user-declared constructors
  * no user-provided, inherited, or explicit constructors
  * no user-declared or inherited constructors
  * no virtual, private, or protected base classes
  * no virtual member functions
  * no default member initializers

If we have a struct which has a single integer in it. We would need to do something like initialize the integer class member. If we use the `C++03` standard, we must have a constructor for this initialization. The code is as follows.
```cpp
struct S {
  S(int s_i) : i(s_i) {}
  int i;
};

int main() {
  S s(1);
  return s.i;
}
```

Now the C++11, we were given the ability to do this uniform initialization syntax. So instead of doing a constructor, we can do this brace initialization syntax. And this works for however many elements you happen to have.

```cpp
struct S {
  int i;
  float f;
};

int main() {
  S s{1, 5.4f};
  
  // We can see a truncation of the floating point value
  // 5.4 -> 5 on the return statement from main here.
  return s.f;
}
```

Well, what happens when we introduce class hierarchy here. We have a base class. We get an error if we actually use inheritance from our struct S and that `there is no matching constructor for initialization of s`. Because we don't have any way to initialize the base class.

```cpp
struct B {
  double d;
};

struct S : B {
  int i;
  float f;
};

int main() {
  S s{1, 2.3};
  
  // Error: no matching constructor for
  // initialization of 'S'.
  return s.f;
}
```

So what C++17 is giving us is the ability to explicitly specify that we want the base class initialized also.

```cpp
#include <iostream>

struct B {
  double d;
};

struct S : B {
  int i;
  float f;
};

int main() {
  // Here, we use the default initialization of
  // the members of struct B.
  S s{{}, 1, 2.3};
  // Here when we return d in base class B, we
  // return 0 since d is the default initialization.
  std::cout << int(s.d) << std::endl; // 0
  
  // If we specify the value for d in base class B,
  // then we've got truncated value as we would expect.
  S s2{{54.3}, 1, 2.3};
  std::cout << int(s2.d) << std::endl; // 54
  
  return 0;
}
```

So we now have with our uniform initialization syntax a way of initializing base class objects 

## Aggregate Initialization

Aggregate initialization, for example, an array initialize. So we could always initialize like this with a list initialization which is then going to perform aggreagte initialization. So list initialization means you have these braces. In C++20, we can now instead of the braces use parents which is direct initialization.
```cpp
// aggregate initialization
int a1[]{1, 2, 3};
int a1[](1, 2, 3);
```

Why C++ committee do this? Because you couldn't really perfect forward aggregates before C++20, that just wasn't possible. Now you can implement emplace_back so now you can in-place aggregates in C++20. The other thing you can now do which is a little bit more like rare. You couldn't really do aggregate initialization inside a macro because if you had inside the macro if you have curlies and then you have a comma in there the compiler would think that that's then the next macro argument after the comma and then the puzzle will just explode. Now you can use parents so that actually compiles and now works since C++20.
```cpp
struct A {
  int i;
  bool b;
};

int main() {
  std::vector<A> v;
  v.emplace_back(42, true); // works since C++20
  
  return 0;
}
```

## Aggregate init inside a macro.

```cpp
struct A {
  int i;
  bool b;
  
  bool isValid() const { return i == 42; }
};

int main() {
  // Error: parser explodes :(
  assert(A{42, true}.isValid());
  // OK since C++20
  assert(A(42, true).isValid());
  
  return 0;
}
```
So introduce this feature, we solved two problems.

## Narrowing conversions.

```cpp
struct A {
  int i;
  bool b;
};

// Error: narrowing conversion from 'double' to 'int'.
A a1{42.5, true};
// OK
A a2(42.5, true);
```

Brace elision.

If you have a brace elision like that you have a nested aggregate. So you have an aggregate inside an aggregate. If you do curlys, you get brace solutions so you can basically do the flat right out the flat initializations and it's going to recurse into the sub aggregates. But the parents doesn't work.

```cpp
struct A {
  int i;
  bool b;
};

struct AA {
  A a1;
  A a2;
};

AA aa1{42, true, 43, false}; // OK
AA aa1(42, true, 43, false); // Error
```

## Lifetime Extension of Temporary Object

You have a reference member, so what list initialization is going to do if you do aggregation via list initialization, it's going to actually extend the lifetime of the reference if you initialize it with a temporary.

```cpp
struct A {
  int&& i;
  bool b;
};

int main() {
  A a1{42, true}; // OK
  return a1.i; // returns 42
  
  // If you use the parents, then you got
  // Dangling reference error, and then you
  // got a behavior and that is going to be
  // sad.
  A a2(42, true); // Dangling reference
  return a2.i; // Undefined behavior
}
```

Another case.
If you have a `struct A`, which has one aggregation member, and have a non-aggregation member since `struct C` has a user-defined constructor. Then if you want to initialize this aggregate with a list initialization by just initialization both of these members. That works with curlys and works with parens.

```cpp
struct C {
  explicit C() = default;
};

struct A {
  int i;
  C c; // This is not aggregation member
       // since struct C has a user-defined
       // constructor.
};

auto a1 = A{42, C{}}; // OK
auto a2 = A{42, C()}; // OK

// Error: can't call explicit ctor.
auto a3 = A{42};
auto a4 = A(42);

// Error: can't call explicit ctor.
auto a5 = A{};

// OK: value initialization,
// NOT aggregate initialization!
auto a6 = A();
```

`auto a5 = A{};` is wrong, because you get an implicit initialized aggregate member, and if you do that with curlies, it is going to copy initialize that aggregate member as if by copy initialization from an empty braced init list. And copy initialization doesn't work with explicit constructors, so you get a compiler error.

`auto a6 = A();` is OK. This is actually has a meaning, it has the meaning of `value initialization` since C++03, which is going to do zero initialization. This has the same meaning as before, so that's going to just zero out the struct, and that's fine.


# References
* [Direct Aggregate Initialization - Timur Doumler](https://www.youtube.com/watch?v=flLNi0aejew)
* [C++17's Aggregate Initializations](https://www.youtube.com/watch?v=qMqWoT016cA)
* [Aggregate Initialization](https://en.cppreference.com/w/cpp/language/aggregate_initialization)