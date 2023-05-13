---
title: Perfect Forwarding
toc: true
date: 2023-05-13 17:18:48
cover: /img/cpp/cpp-basics-5.jpg
excerpt: Do you want to learn
tags:
- Perfect Forwarding
- C++
- C++14
- C++17
- C++20
---

# Forward References

`T&&` can accept lvalues and rvalues, so this is also called `Universal Reference`.

```c++
template <typename TPara>
void foo(TPara && p) {}

// usage
foo(3);
foo(std::move(i));
foo(std::move(up));
```

|     | &   | &&   |
| --- | --- |------|
| T&  | T & | T &  |
| T&& | T & | T && |

The upper table shows that references are collapsed to a lvalue reference when at least one of them is a lvalue reference.
The lack of type substitution is the reason why the non-template rvalue reference do not accept lvalues. The only reason why the function parameter can be an lvalue is that an lvalue reference is introduced by the substitution. Without this substitution, no lvalue reference is involved and references are not collapsed.

# Perfect Forwarding
We've seen that template argument deduction would change reference to lvalue reference in case there is an lvalue reference exists.
However, if we want the lvalue reference to be passed as an lvalue and the rvalue reference as an rvalue, we should use `std::forward`. It casts an rvalue reference into an rvalue and leaves an lvalue as it is. `std::forward` must be instantiated with the type parameter.

```c++
template <typename TPara>
void foo(TPara &&p)
{
  bar(std::forward<TPara>(p));
}
```
The argument of `foo` is passed with the same value category to `bar`. Whatever was passed as an lvalue to `foo` is passed as an lvalue to `bar`, likewise for every rvalue.
Like `std::move`, `std::forward` is a pure cast and does not generate a single machine operation. People have phrased this as: `std::move` does not move and `std::forward` does not forward. They rather cast their arguments to be moved or forwarded.

# const T& --- Universal Reference
May be you are wondering that why `const T&` can bind to rvalues and lvalues.
C++98 didn't have rvalue references, only "references" --- what we now call "lvalue references". The rule was simply that a mutable reference would bind only to a mutable lvalue, but a const reference could bind to anything.

```c++
void change(int &);
void observe(const int &);

int main()
{
  int i = 41;
  change(i);    // OK, lvalue
  change(42);   // Error, rvalue
  observe(i);   // OK, lvalue
  observe(42);  // OK, rvalue
  
  return 0;
}
```

This also applies even when that type `int` comes from type deduction:

```c++
template <typename T>
void observe(const T&);

int main()
{
  int i = 41;
  const int ci = 42;
  observe(i);     // OK, lvalue, T = int
  observe(ci);    // OK, lvalue, T = int
  observe(43);    // OK, rvalue, T = int
  
  return 0;
}
```

When C++11 invented rvalue references, none of this behavior changed at all. `const T&` still binds happily to both lvalues and rvalues.

> const T& is the O.G. universal reference.

C++11 also invented the forwarding reference: that when there's a deduced type T directly modified by `&&`, T can sometimes be deduced as an lvalue reference type.

```c++
template <typename T>
void forward(T &&) {}

int main()
{
  int i = 41;
  const int ci = 42;
  
  forward(i);     // OK, lvalue, T = int &
  forward(ci);    // OK, lvalue, T = const int &
  forward(43);    // OK, rvalue, T = int
  
  return 0;
}
```

The advantage of `T &&` is that (, by looking at whether T deduced as a reference type) you can tell whether your caller considered the argument an rvalue or an rvalue. That's not useful information in its own right; it is useful only if you are planning to forward your argument as its original value category - lvalues as lvalues, rvalues as rvalues. That's what `std::forward<T>(t)` is for.

In exchange for this advantage - forwardability - you pay in template bloat. Notice taht we get three difference instantiations of void `forward(T &&)` above, whereas we got only a single template instantiation of `void observe(const T&)`.

```c++
// Instantiated from forward(i)
template<> void forward<int &>(int &) {}

// Instantiated from forward(ci)
template<> void forward<const int &>(const int &) {}

// Instantiated from forward(43)
template<> void forward<int>(int &&) {}
```

# References
* ["Universal reference" or "forwarding reference"?](https://quuxplusone.github.io/blog/2022/02/02/look-what-they-need/)