---
title: C++ Value Categories
toc: true
date: 2023-05-14 12:29:51
cover: /img/cpp/cpp-basics-5.jpg
excerpt: C++ value categories.
tags:
- C++
- C++14
- C++17
- C++20
---

# Motivation

Consider the following code:

```c++
struct Data {
  Data(size_t s);       // constructor
  Data(const Data &);   // copy constructor
  Data(Data &&)         // move constructor
  
  size_t s;
  int *b;
};

const Data getData(size_t s) { return Data{s}; }

// Consider what times does following code call constructors?
auto d1 = Data{42};         // 1 --- constructor
auto d2 = std::move(d1);    // 0 --- d1 is pointer, then just move d1 value to d2;
auto d3 = getData(42);      // 1 --- constructor
auto d4 = std::move(getData(42)); // 2, one for constructor, and since the
                                  // return type is const Data, so the copy
                                  // constructor is called for d4 construction.
```

Explanation
`auto d4 = std::move(getData(42));`
* Firstly, `Data(size_t s)` constructor is called by `getData()` function. Then the copy constructor is called because `getData` function returns a `const Data` object, so the copy constructor creates a new `Data` object, and `std::move` moves new object for `d4`.

# Value Categories

Value Categories were inherited from C, with the porting of "lvalue expression", which is originally referred to the location of expression with regards to assignment.
```c++
auto a = int(42);
```
* lvalue (left-value) was on the left of the assignment.
* rvalue (right-value) was on the right of the assignement.

Value Category of an entity defines:
1. **lifetime**
   * Can it be moved from
   * Is it a temporary
   * Is it observable after change, etc
2. **Identity**
   * Object has identity if its address can be taken and used safely

**Value Categories** affect two very important aspects:
> Performance
> Overload resolution

Value Category is a quality of an expression.

```c++
struct Data {
  Data(int x);
  int x_;
};

void foo(Data &&x) { x = 42; }

// ...
Data &&a = 42;
foo(a);         // Fail! lvalue!
foo(Data{73});  // OK
```
**Explanation**
`Data &&a` is `rvalue reference`, but this is confusing because `a` have a name we can take and change it, which means this is an `lvalue`.
* **a's Type**:  rvalue reference to Data
* **a's Value Category**: lvalue
The entity can have different `Value Category` in **different contexts**.
So let's look at function `foo` that takes data, during calling this function, we firstly create this unamed temporary data with 73, `foo` function binds the temporary data object to this rvalue reference, now in this function `foo`, this temporary entity has a name inside the scope of the `foo` function, so inside the scope of the foo function, this entity is an **lvalue**, because we can take its address we can assign to it. So the different scope gave different value category to the same entity.

Each **expression** has two properties:
* A **TYPE** (including CV qualifiers)
* A **VALUE CATEGORY**

**Value Category** is a quality of an expression.

Expression Category taxonomy
```
                expression
                /        \
           glvalue      rvalue
           /     \      /    \
         lvalue    xvalue    prvalue
```

* C++11: added rvalue references, move semantics:

|                              | Has Identity (glvalue) | Doesn't have identity |
|------------------------------|------------------------|-----------------------|
| Can't be moved from          | lvalue                 | -                     |
| Can't be moved from (rvalue) | xvalue                 | prvalue               |

* C++17: added guaranteed copy elision:
[[P0135](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2015/p0135r0.html)] Guaranteed copy elision through simplified value categories

|                              | Has Identity (glvalue) | Doesn't have identity   |
|------------------------------|------------------------|-------------------------|
| Can't be moved from          | lvalue                 | -                       |
| Can't be moved from (rvalue) | xvalue                 | prvalue materialization |

The result of a prvalue is the value that the expression stores into its context. So then we materialize this thing and we get the prvalue.

* C++20:
[p0527]: Implicitly move from rvalue references in return statements
Moved Value Categories section from [basic] to [expt --- expression]

* C++23:
[P0847]: Deducing this

TODO: Differences about value categories between C++11 and C++17 and C++20
TODO: [Value Categories in C++17](https://medium.com/@barryrevzin/value-categories-in-c-17-f56ae54bccbe)

## Main Categories (classification only)
* **glvalue**: (have identity) expression whose evaluation determines the identity of an object or function.
* **rvalue**: (can be moved from) a prvalue or an xvalue

## Subcategories
* **lvalue**: glvalue that is not an xvalue
* **xvalue**: glvalue that denotes an object whose resources can be reused (usually because it is near the end of its lifetime).
* **prvalue**: expression whoes evaluation initializes an object, or computes the value of the operand of an operator, as specified by the context in which it appears, or an expression that has type cv void.

* **glvalue**

```c++
struct Data { int n; int* pn = &n; };
Data& getData(Data& d) { return d; }
int a = 42;
int b = a;
int& la = a;
int* pa = &a;
int&& ra = 42;
a++;
++a;

int arr[] = {1, 2, 3};
arr[0] = 73;
Data d;
(&d)->n = 42;
d.n = 73;
*d.pn = 42;
string s = "Hello world";
a == b ? b : c;
Data c = getData(d);

```

* **prvalue**

```c++
struct Data {
  int n;
  int foo() { this->n = 4; }
};

int a = 42;
int* pa = &a;
pa = nullptr;
a++;
++a;

auto l = []() { return 2; };
Data d;
Data* dp = &d;
Data();
d->n = 6;
d.n = 6;
string s = "Hello world";
a == a ? throw 4 : throw 2;
bool equals = a == 42;
```

* **xvalue**

```c++
struct Data { int n; int* pn = &n; };

Data d1 = Data{42};
d1.*pn = 73;
Data d2 = std::move(d1);
Data().n;

Data getData() { return Data{73}; }
Data d3 = getData();
d1 == d2 ? Data{42} : Data{73};
```

# The Details of Binding

* Expressions with different Value Categories "bind" to different types of References.
* The Reference type which binds the expression determines the permitted operations.

```c++
int a = 42;
int& la = a;
const int& cla = a;
int&& ra = a + 73;
const int&& cra = 42;

// ... 
la = 73;  // OK, a = la = 73
cla = 42; // Error
ra = 73;  // OK, int&& ra = a + 73, this means there exits a ra, and ra value is a + 73,
          // then ra = 73 means change ra value to 73 since ra is lvalue category.
cra = 42; // Error
```

* Binding rules are important as part of the following "events":
  * Initialization or assignment
  * Function call (including non-static class member function called on an object)
  * Return statement

So all of these three different events that happens in your code needs to take the value category and the binding rules under consideration.

### Initialization or Assignment

```c++
int a = 42;
int& la1 = a;                 // OK la = 42
int& la2 = 73;                // Error
const int& cla1 = a;          // OK
cosnt int& cla2 = 73;         // OK
int&& ra1 = a;                // Error
int&& ra2 = a + 42;           // OK
const int&& cra1 = a;         // Error
const int&& cra2 = a + 42;    // OK
```

* Binding Rules

|                        | Binds lvalues?              | Binds rvalues?              |
|------------------------|-----------------------------|-----------------------------|
| lvalue reference       | YES                         | <font color="red">NO</font> |
| const lvalue reference | YES                         | YES                         |
| rvalue reference       | <font color="red">NO</font> | YES                         |
| const rvalue reference | <font color="red">NO</font> | YES                         |

rvalues can be bound to const lvalue reference and rvalue reference and to const rvalue reference. lvalues can be bound to lvalue reference and const lvalue reference. This is very important in the scope of talking about move constructor.
If a rvalue is bound to a const lvalue reference, it will extend its lifetime, rvalue reference can extend the lifetime of a temporary.

Limitations is the context of the function are according to the binding function:

|                        | Function can modify data??  | Caller can observe (old) data? |
|------------------------|-----------------------------|--------------------------------|
| lvalue reference       | YES                         | YES                            |
| const lvalue reference | <font color="red">NO</font> | YES                            |
| rvalue reference       | YES                         | <font color="red">NO</font>    |
| const rvalue reference | <font color="red">NO</font> | <font color="red">NO</font>    |

## Copy Elision Optimizations
Return Statement:
Starting from C++17, the behavior of VCs is affected by: "[PO135](https://google.com.hk/) Guaranteed copy elision (...)"
There are two mandatory elisions of copy and move constructors:

1. Object Initialization
```c++
Data d = Data{Data{42}};
// 1 CTOR (avoids: Copy CTOR)
// In C++17, copy constructor would be removed.
```

2. Return Statement
An un-named Return Value Optimization (RVO):
```c++
Data getData(int x) { return Data{x}; }
Data d = getData(42);
// 1 CTOR (avoids Move CTOR)
```

3. Return Statement: Materialization
Temporary materialization conversion [conv.rval](https://google.com.hk/)
A prvalue of type T can be converted to an xvalue of type T. This conversion initializes a temporary object of type T from the prvalue by **evaluating the prvalue** with the temporary object as its result object, and produces an xvalue denoting the temporary object.

In order to materialize, T shall be a complete type.

## The Details of Binding
* To summarize:
Binding rules apply in the following "events":
> 1. initialization or assignment
> 2. Function call (including non-static class member function called on an object)
> 3. Return statements

* Behavior of the entity(which :
The behavior of the entity is defined by the things that binds it.
> 1. Initialization: limits are according to the reference which binds it.
> 2. Function call: limits inside the function are according to the overload which binds it.
> 3. Return statement: limits as in initialization, with additional rules due to optimizations and const

# Reference Collision
In case of concatenation of multiple '&' symbols, such as in generic code, or in code using type aliases. Compiler performs **Reference Collision**.

```c++
typedef int& lr;
typedef int&& rr;

int a;

// Compiler knows the actual types, and then
// compiler performs reference collision, we can
// get the actual types.
lr& b = a;      // lr& -> int&& -> int&
lr&& c = a;     // lr&& -> int&&& -> int&
rr& d = a;      // rr& -> int&&& -> int&
rr&& e = 73;    // rr&& -> int&&&& -> int&&
```

## Forwarding Reference
Forwarding parameters inside a function template should consider Value Categories. The term for them was first suggested by Scott Myers, "universal reference", and later, formalized as "**forwarding reference**".
Due to TAD, "rvalue reference" has a special meaning in context of function template:

```c++
// In the following code, T&& looks like rvalue reference,
// But it is actually a forwarding reference (only in this
// context).
template <typename T>
void foo(T&& t) {
  // Type of T here
}

int a = 42;
const int& cla = a;
int&& b = 73;
// This conversion is due to Reference Collision.
foo(a);               // T = int&
foo(cla);             // T = const int&
foo(std::move(a));    // T = T = int
```
T&& keeps the value category of the type the instantiation is based on.

# tools for Handling Value Categories

This tools helps you to manipulate value categories to understand better and to control them in your code.

```
std::move
std::forward
std::decay
std::declval
decltype specifier
Deducing this (C++23)
```

## std::move
Utility function, produces an xvalue expression T&&, this equivalent to `static_cast` to a `T` value reference type: `static_cast<typename std::remove_reference<T>::type&&>(t)`

Notice that `std::move` may not always do what you hoped:
```c++
void foo(int& x) { std::cout << "int&"; }
void foo(const int& x) { std::cout << "const int&"; }
void foo(int&& x) { std::cout << "int&&"; }

int a = 73;
int& b = a;
const int& c = a;
const int&& d = 42;

foo(std::move(b));    // int&&        --> foo(int&&)
foo(std::move(c));    // const int&   --> foo(const int&)
foo(std::move(d));    // const int&&  --> foo(const int&)
```

## std::forward

```c++
std::forward<T>(expression);
```

[N1385 The forwarding problem: Arguments](https://google.com.hk), presented two issues: forwarding params, and returning result. Suggested utility function, preserves value category of the object passed to the template.
It suggests a solution for the forwarding problem. In this paper, they've recognized that there is an issue and the value categories are something be that needs to be preserved, and suggested this utility.

`std::forward` uses `std::remove_reference<T>` to get the value type. `std::forward` uses other utilities from the standard library in the implementation, and **it's commonly used combined with forwarding reference**.

```c++
// There are three overloads of the functions.
void foo(int& x) { std::cout << "int&"; }
void foo(const int& x) { std::cout << "const int&"; }
void foo(int&& x) { std::cout << "int&&"; }

template <typename T>
void wrapper(T&& t) { foo(std::forward<T>(t)); }

template <typename T>
void nfwrapper(T&& t) { foo(t); }

int a = 73;
const int& lca = a;
wrapper(a);         // int&
nfwrapper(a);       // int&
wrapper(lca);       // const int&
nfwrapper(lca);     // const int&
wrapper(6);         // int&&
nfwrapper(6);       // int&
```

## std::decay
```c++
std::decay<T>::type
```
Type trait, result is accessible through `_t`. Performs the following conversions:
1. Array to pointer
2. Function to function pointer
3. lvalue to rvalue (removes cv qualifiers, references) (issue for move-only types)

it is the `std::decay` is doing something very similar to what `auto` is doing. 

```c++
template <typename T, typename U>
struct decay_is_same :
    std::is_same<typename std::decay<T>::type, U>
{};

// How to use former definition:
decay_is_same<int&, int>::value; // True
```
This behavior should be familiar to you, as it resembles `auto`s behavior (`auto` performs auto-decay).
<font color="red">TODO</font>

## decltype specifier
```c++
decltype( expression );
```
`decltype` is a language thing, is a language utility, and it bascially gives you back the type of the object including value category, which is very important.

`decltype` evaluates  an expression, yields its **type + value category** (AKA the declared type).
`decltype` (unlike auto) preserves value category. For an expression of type T:
* If expression is xvalue, yields `T&&`.
* If expression is lvalue, yields `T&`.
* If expression is prvalue, yields `T`.

`decltype` can be used instead of a type, as a placeholder which preserves value categories
```c++
int&& foo(int& i) { return std::move(i); }

int i = 73;
auto a = foo(i);            // Type: int        | VC: lvalue
decltype(auto) b = foo(i);  // Type: rvalue ref | VC: lvalue
```

* The T prvalue doesn't materialize, so T can be an incomplete type (C++17).
* If evaluation fails (entity is not found or overload resolution fails), program is ill-formed.

`((expression))` has a special meaning, and **yields an lvalue expression**.
```c++
int&& a = 42;
decltype(a) b = 42;   // Type: rvalue ref to int | VC: lvalue
decltype((a)) c = 73; // Error! Binding non-const lvalue ref to prvalue
```

`decltype` main use cases:
1. When the type is unknown (syntax is available from C++14), we can use that to retrieve the type.
```c++
template <typename T, typename U>
decltype(auto) Add(T t, U u) { return t + u; }

template <typename T>
decltype(auto) Wrapper(T&& t) {
  // do something...
  return std::forward<T>(t);
}
```
2. To preserve the value category of the expression.
```c++
int && a = 32;        // Type: rvalue ref to int | VC: lvalue
decltype(a) b = a;    // Error! (binding rvalue ref to an lvalue ref a)
decltype(a) c = 73;   // Type: rvalue ref to int | VC: lvalue
decltype((a)) d = a;  // Type: lvalue ref to int | VC: lvalue
```

## std::declval
```c++
std::declval<T>( )
```
It basically takes an object and return the type.
Utility function produces:
* xvalue expression `T&&`.
* If T is void, returns `T`.

* `std::declval` can be used with expression to return the expression's reference type.
* It can **return a non-constructible or incomplete type**.

```c++
struct Type {
  int a;
  int foo() { return 42; }
private:
  Type() { }
};

// Fails, the Type() constructor is a private function.
Type t;
// Type, we can use this incomplete type for std::declval.
typeid(std::declval<Type>()).name();
```
So this is a way for you to communicate with compiler.
Combined with `decltype`, we can get the type of a member (even when Type is non-constructible).
```c++
decltype(std::declval<Type>().a) b = 73;
```
`std::declval` shouldn't be used in an evaluated context (evaluating `std::decltype` is an error).

`std::declval` allows us to access T members, in a way preserves value categories.

```c++
struct Type {
  int a;
  int& ra = a;
  int getA() { return int{73}; }
  int& getRefA() { return ra; }
  
private:
  Type(int i) : a(int(i)) {}
};

std::declval<Type>().a;         // xvalue
std::declval<Type>().ra;        // lvalue
std::declval<Type>().getA();    // prvalue
std::declval<Type>().getRefA(); // lvalue
```

`std::decltype` and `std::declval` are often used to transform between type and instance, for example:

## Deducing This (C++23)

```c++
template <typename T>
void Foo(this T&& t) { }
```

[P0847: Deducing this - voted into C++23](https://google.com.hk)
`this` allows specifying from within a member function the value category of the expression it's invoked on
```c++
struct Type {
  auto Foo() const &;
  auto Foo() &;
  auto Foo() &&;
};

// This can be re-written like this:
struct Type {
  auto Foo(this const Type&);
  auto Foo(this Type&);
  auto Foo(this Type&&);
};
```

Combined with the forwarding reference, we can now write all these in a single template function.
```c++
struct Type {
  template <typename Self>
  auto Foo(this Self&& self);
};
```

This would help you to write libraries, or multiple overloads.

"Deducing this" feature introduced two new utilities: `std::like_t` and `std::forward_like<T>(u)`. `std::like_t` applies CV and ref-qualifiers of T onto U. For example:

```c++
std::like_t<double&, int>;          // int&
std::like_t<const double&, int>;    // const int&
std::like_t<double&&, int>;         // int&&
std::like_t<const double&&, int>;   // const int&&
```

```c++
std::forward_like<T>(U) -> std::forward<std::like_t<T, decltype(u)>>(u)
```




# References
* [A Taxonomy of Expression Value Categories](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2010/n3055.pdf)
* [Master C++ Value Categories With Standard Tools](https://www.youtube.com/watch?v=tH0Z2OvHAd8&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=80)
* [Value Categories in C++17](https://medium.com/@barryrevzin/value-categories-in-c-17-f56ae54bccbe)