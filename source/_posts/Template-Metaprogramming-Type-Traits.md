---
title: Template Metaprogramming---Type Traits
date: 2022-11-06 18:19:30
cover: /img/cpp/cpp.png
excerpt: An overview notes about type_traits.h.
tags:
  - Meta-Programming
  - C++
---

# The Aims

* How to implement and how to use
* Exploration of the standard set of type traits
   * Focus on techniques for implementing type traits
* Remove some of the mystique that still surrounds template metaprogramming.
* Practical advice from a regular user.
* So you can more readily use the standard set and implement your own when
  needed.

# What is Meta-Programming

* In general, when programs treat programs as data
* Could be other programs or itself
* Could be at "compile time" or "run time"
* We will discuss compile time metaprogramming in C++
* Wide array of current techniques, but still considered a niche
* This two-part tutorial helps shed light on a very few essential ideoms

# Why Care About Metaprogramming (and type traits in particular)

* Each new standard library employs more metaprogramming techniques
* Some requirements are impossible without advanced techniques(e.g., std::optional)
* Many third party libraries, not just Boost
* Tools and idioms have become well developed, no longer black magic, limited to STL and Boost.
* All C++ programmers should understand the basics.
* Any library developer should understand a good bit more
* C++20 - concepts and independent requires expressions

It's kind of a paradigm shift, there are a lot of things that make metaprogramming more look like
regular functional programming.

# Meta-Functions

* A meta-function is not a function but a class/struct
* Meta-functions are not part of the language and have no formal language support
* They exist as an idiomatic use of existing language features
* Their use is not enforced by the language
* Their use is dictated by convention
* C++ community has created common "standard" conventions

## The Definition of meta-function
* Technically, a class with zero+ template parameters and zero+ return types and values
* Convention is that a meta-function should return one thing, like a regular function
* Convention was developed over time, so plenty of existing examples that do not follow this convention
* More modern meta-functions do follow this convention

## Return From a Meta-Function

* Expose a public value "value"

```cpp
template <typename T>
struct TheAnswer {
  static constexpr int value = 42;
};
```

* Expose a public type "type"

```cpp
template <typename T>
struct Echo {
  using type = T;
};
```

Meta-functions yield back some types to you.

### Value Meta-functions

* Simple regular function: identity

This is a very simple regular function.

```cpp
int int_identity(int x) { reutrn x; }

assert(42 == int_identity(42));
```

* The Simple Meta-Function: identity

```cpp
template <int X>
struct IntIdentity {
  static constexpr int value = X;
};

static_assert(42 == IntIdentity<42>::value)
```

* Generic Identity Function

```cpp
template <typename T>
T identity(T x) { return x; }

// Returned type will be int
assert(42 == identity(42));

// Returned type will be unsigned long long
assert(42ull == identity(42ull));
```

* Generic Identity Meta-Function

```cpp
template <typename T, T Value>
struct ValueIdentity {
  static constexpr T value = Value;
};

// The type of value will be int
static_assert(42 == ValueIdentity<int, 42>::value);

// The type of value will be unsigned long long
static_assert(ValueIdentity<unsigned long long, 42ull>::value == 42ull);
```

* Generic Identity Meta-Function (C++17)

Template accepts non-type template parameter since C++17.

```cpp
template <auto X>
struct ValueIdentity {
  static constexpr auto value = X;
};

// The type of value will be int
static_assert(42 == ValueIdentity<42>::value);

// The type of value will be unsigned long long
static_assert(42ull == ValueIdentity<42ull>::value);
```

* Two forms of Sum Function

```cpp
template <typename X, typename Y>
constexpr auto sum(X x, Y y) { return x + y; }

// Return type will be unsigned long long
assert(42ull == sum(30, 12ull));

// It takes two separate non-type template parameters
template <auto X, auto Y>
struct Sum {
  static constexpr auto value = X + Y;
};

// Return type will be unsigned long long
static_assert(42ull == Sum<30, 12ull>::value);
```

### Type Meta-Functions

* Type meta-functions just like a workhorse (especially will the advent of constexpr) which manipulate
types.
* "Returns" a type.

This is a type meta-function demo

```cpp
template <typename T>
struct TypeIdentity { using type = T; };
```

C++20 introduces std::type_identity

### Calling Type Meta-Functions

```cpp
ValueIdentity<42>::value;
TypeIdentity<int>::type;
```

Typename Dance

```cpp
typename TypeIdentity<T>::type;
```

### Convenience Calling Conventions

* Value meta-functions use variable templates ending with "_v".

```cpp

template <auto X>
struct ValueIdentity {
  static constexpr auto value = X;
};

// variable template
// This is a convenient way to call a value meta-functions using
// variable template.
template <auto X>
inline constexpr auto ValueIdentity_v = ValueIdentity<X>::value;

static_assert(42 == ValueIdentity<42>::value);
static_assert(42 == ValueIdentity_v<42>);
```

* Type Meta-Functions use alias templates ending with "_t". `Typename Dance`.

```cpp
template <typename T>
using TypeIdentity_t = typename TypeIdentity<T>::type;

static_assert(std::is_same_v<int, TypeIdentity_t<int>>);
```

These calling conventions are easier to use. But each one must be explicitly handwritten.

* A meta-convention to get around that which I may get to if time for bonus material.

# Type Traits

## Some Useful Meta-Functions

### std::integral_constant

A very useful meta-function. It allows us to wrap a constant with its type.


```cpp
template <class T, T v>
struct integral_constant {
  static constexpr T value = v;
  
  using value_type  = T;
  using type        = integral_constant<T, v>;
  
  constexpr operator value_type() const noexcept {
    return value;
  }
  // this is a functor, a call operator.
  constexpr value_type operator()() const noexcept {
    return value;
  }
};
```

### std::bool_constant

This is Convenient helpers.


```cpp
template <bool B>
using bool_constant = integral_constant<bool, B>;

// alias templates
// this is equivalent to integral_constant<bool, true>.
using true_type = bool_constant<true>;
// this is equivalent to integral_constant<bool, false>.
using false_type = bool_constant<false>;
```

`true_type` and `false_type` are going to be meta-functions. They are called nullary meta-functions
because they have no parameters.

```cpp
true_type::value;
false_type::value;
```

# Standard Type Trait Requirements

## Cpp17 Unary Type Trait
Cpp20 introduces very different meta-programming techniques.

For a unary type trait in the standard library which is what we're got which is what we are talking
about. Unary type trait in the standard library it has a class template of one template type
argument

Cpp17UnaryTypeTrait

* Class Template
* One template type argument*
* Cpp17DefaultConstructible
* Cpp17CopyConstructible
* Publicly and unambiguously derived from a specialization of `std::integral_constant`.
  All the unary type traits have to derive from `integral_constant`.
* The member names of the base characteristic shall not be hidden and shall be unambiguously available
  Basically, this means if you inherit from it you can't hide any of that stuff, you got to let all
  that stuff be available publicly.

## Cpp17BinaryTypeTrait

This is an exactly same thing with `Cpp17UnaryTypeTrait` except Cpp17BinaryTypeTrait has two
template type argument*.


## Cpp17TransformationTrait

* Class Template
* One template type argument*
* Define a publicly asccessible nested type name `type`.
* No default/copy constructible requirement
* No inheritance requirement

# Specialization

## is_void (Unary Type Trait)

* Value meta-function: is the type void? yields true_type or false_type

Specialization

* Primary template: general case
```cpp
template <typename T>
struct is_void : std::false_type {};
```

* Specialization: special case(s)
```cpp
// The empty angle brackets mean it's an explicit full
// specialization, and then we take the type that we
// are specializing for. And we put it in right place.
// In this case, we are going to return true and so
// these static_assert.
template<>
struct is_void<void> : std::true_type {};

static_assert(is_void<void>{});
static_assert(not is_void<int>{});
```

* Why does is void reutrn true type false type instead of true false values?

The reason because it is a meta-function returning the true type(the actual type of it).
First of all, the `is_void` is inherited from `integral_constant`. `false_type` is just integral
constant bool false. `true_type` is just `integral_constant` bool true. The standard says that
unary meta-functions must inherit from one of those.
And the reason because if all we did was just return a true value where is a 

`is_void` is inherit from a `true_type`, and `true_type` is already having a type.

`static_assert(is_void<void>{}`, the curly bracket, that is instantiating one of those things and it
implicit conversion operator to turn it into a true.

* Is `void const` void?
* Is `void volatile` void?
* `is_void` is in primary type categories.

Yes, the standard says `void` & `void const` & `void volatile` & `void const volatile` are all `void`.

`cv` stands for `const volatile`

For any given type T, the result of applying one of these templates to T and to cv T shall yield
the same result.

**The definition of `is_void`**

```cpp
// The primary template
template <typename T> struct is_void : std::false_type {};

// specialization for void.
template<>
struct is_void<void> : std::true_type {};
// specialization for void const
template<>
struct is_void<void const> : std::true_type {};
// specialization for void volatile
template<>
struct is_void<void volatile> : std::true_type {};
// specialization for void const volatile
template<>
struct is_void <void const volatile> : std::true_type {};

// The standard mandates this as well.
template <typename T>
inline constexpr bool is_void_v = is_void<T>::value;
```

## remove_const (Transformation Trait)

There are three type traits: unary traits/binary traits/transformation traits. `remove_const` is
a transformation traits. transformation traits are what they call they are type meta-functions.

* Formal Definition
> The member typedef type names the same type as T except that any top-level const-qualifier has
> been removed.

The `top-level` qualifier, like volatile/const which are attached to the type itself. 

```
remove_const<int> -> int
remove_const<const int> -> int
remove_const<const volatile int> -> volatile int

remove_const<int *> -> int *

remove_const<cont int *> -> const int *
// this because pointer to a constant,
// it is not a const pointer.

remove_const<int const * const> -> int const *
remove_const<int * const> -> int *
```

* The definition of `remove_const`

```cpp
template <typename T>
struct TypeIdentity { using type = T; };

// primary template, do nothing if no const
template <typename T>
struct remove_const : TypeIdentity<T> {};

// Partial specialization, when detect const
template <typename T>
struct remove_const<T const> : TypeIdentity<T> {};

// Standar mandated convenience alias
template <typename T>
using rmeove_const_t = typename remove_const<T>::type;
```

Contains `const` so the partial specialization will match.

```cpp
template <typename T>
struct remove_const<T const> : TypeIdentity<T> {};
```
The const is explicitly matched so the part remaining to match with the "T" is int volatile

## conditional

This is basically think of it as like an if statement in regular programming. Some conditions it
returns T, else return F.

In this you can read it, if the bool condition is true, return T, else return F.

```cpp
template <typename T>
struct TypeIdentitiy { using type = T; };

// This partial specialization means condition is true,
// then returns T.
template <bool Condition,  typename T, typename F>
struct conditional : TypeIdentity<T> {};

// This partial specialization means condition is false,
// then conditional returns F.
template <typename T, typename F>
struct conditional<false, T, F> : TypeIdentity<F> {};

static_assert(is_same_v<int, conditional_t<is_void<void>::value,
                                           int, long>);
static_assert(is_same_v<long, conditional_t<is_void<char>::value,
                                           int, long>);
```

Not all the type traits can be implemented by c++, the compiler has way more information about
the type system and about what's going on than it is exposed to the programmer through the
language.

Type traits can be implemented by intrinsics, and compiler can be more efficient for intrinsics
processing.

`is_union` should be supported by compiler.

# Primary Type Categories

There are 14 primary type categories.

```
is_void             is_class
is_null_pointer     is_function
is_integral         is_pointer
is_floating_point   is_lvalue_reference
is_array            is_rvalue_reference
is_enum             is_member_object_pointer
is_union            is_member_function_pointer
```

* All are to have base characteristic of either `true_type` or `false_type`.
* All should yield the same result in light of cv(const volatile) qualifiers.

## is_null_pointer

```cpp
template <typename T>
struct is_null_pointer : std::false_type {};

template <>
struct is_null_pointer<std::nullptr_t>
    : std::true_type {};
template <>
struct is_null_pointer<std::nullptr_t const>
    : std::true_type {};
template <>
struct is_null_pointer<std::nullptr_t volatile>
    : std::true_type {};
template <>
struct is_null_pointer<std::nullptr_t const volatile>
    : std::true_type {};

// The standard mandates this as well...
template <typename T>
inline constexpr bool is_null_pointer_v = is_null_pointer<T>::value;
```

## is_floating_point

`float`/`double`/`long double`

requires 12 specializations.

```cpp
template <typename T> struct is_floating_point : std::false_type {};

// float type
template <>
struct is_floating_point<float> : std::true_type {};
template <>
struct is_floating_point<float const>
    : std::true_type {};
template <>
struct is_floating_point<float volatile>
    : std::true_type {};
template <>
struct is_floating_point<float const volatile>
    : std::true_type {};

// double type
template <>
struct is_floating_point<double>
    : std::true_type {};
template <>
struct is_floating_point<double const>
    : std::true_type {};
template <>
struct is_floating_point<double volatile>
    : std::true_type {};
template <>
struct is_floating_point<double const volatile>
    : std::true_type {};

// long double type
template <>
struct is_floating_point<long double>
    : std::true_type {};
template <>
struct is_floating_point<long double const>
    : std::true_type {};
template <>
struct is_floating_point<long double volatile>
    : std::true_type {};
template <>
struct is_floating_point<long double const volatile>
    : std::true_type {};

// for convenience use
template <typename T>
inline constexpr bool is_floating_point_v =
    is_floating_point<T>::value;
```

## is_integral

* Five standard signed integer types: `signed char`, `short int`, `int`, `long int`, `long long int`.
* Implementation defined extended signed integer types.
* Corresponding, but different, unsigned integer types.
* char, char8_t, char16_t, char32_t, wchar_t.
* bool
* Requires 16 * 4 = 54 specializations.

# Meta-Function Abstractions

* We would have reached for this long before now with regular/normal programming.
* Treat meta-function programming like regular programming because, well, that's what it is.
* Step back to the land of regular functions.
* Pretend we needed to implement these same ideas with strings instead of types.

The regular type of `is_void`

```cpp
bool is_void(std::string_view s) {
  return s == "void"
    ||   s == "void const"
    ||   s == "void volatile"
    ||   s == "void const volatile"
    ||   s == "void volatile const";
}
```

A new version of type traits(A Step in the right direction)

```cpp
std::string_view remove_cv(std::string_view);

bool is_void(std::string_view s) {
  return remove_cv(s) == "void";
}
bool is_null_pointer(std::string_view s) {
  return remove_cv(s) == "std::nullptr_t";
}
bool is_floating_point(std::string_view input) {
  auto const s = remove_cv(input);
  return s == "float"
      || s == "double"
      || s == "long double";
}

std::string_view strip_signed(std::string_view);

bool is_integral(std::string_view input) {
  auto const s = strip_signed(remove_cv(input));
  return s == "bool"
      || s == "char8_t"
      || s == "char16_t"
      || s == "char32_t"
      || s == "wchar_t"
      || s == "char"
      || s == "short"
      || s == "int"
      || s == "long"
      || s == "long long";
}
```

We already have `remove_const`, we also need `remove_volatile`, compose them to get `remove_cv`.

## remove_volatile

* Formal Definition
> The member typedef type names the same type as T except that any top-level volatile-qualifier has
> been remove.

```cpp
template <typename T>
struct TypeIdentity { using type = T; };

// Primary template, do nothing if no volatile
template <typename T>
struct remove_volatile : TypeIdentity<T> {};

// Partial specialization, when detect volatile
template <typename T>
strut remove_volatile<T volatile> : TypeIdentity<T> {};

// Standard mandated convenience alias.
template <typename T>
using remove_volatile_t =
    typename remove_volatile<T>::type;
```

## remove_cv

* Formal Definition
> The member typedef type names the same type as T except that any top-level cv-qualifier has
> been removed.

```cpp
// template <typename T>
// using remove_cv = remove_const<typename remove_volatile<T>::type>;

// alias template
template <typename T>
using remove_cv = remove_const<remove_volatile_t<T>>;
// remove_volatile_t<T> is the same thing with typename
// remove_volatile<T>::type.
// Here we don't use remove_const_t, this because we want
// remove_cv to be a meta-function.
// If we use remove_const_t, remove_cv is just a type either
// meta-function.

template <typename T>
using remove_cv_t = typename remove_cv<T>::type;
```

Eg. `remove_cv<int const volatile>`

```cpp
// Removing volatile, then const
template <typename T>
using remove_cv = remove_const<remove_volatile_t<T>>;

// remove_cv<int const volatile>
// remove_const<remove_volatile_t<int const volatile>>
// remove_const<typename remove_volatile<int const volatile>::type>
// remove_const<int const>
```

Eg. `remove_ct_t<int const volatile>`

```cpp
template <typename T>
using remove_cv_t = typename remove_cv<T>::type;

// remove_cv_t<int const volatile>
// typename remove_cv<int const volatile>::type
// typename remove_const<int const>::type
// int
```

## is_same

```cpp
template <typename T1, typename T2>
strut is_same : std::false_type {};

// Partial specialization --- when they are both the same.
// angle brackets
template <typename T>
struct is_same<T, T> : std::true_type {};
// When T1 and T2 are same, then is_same matches this partial
// specialization version. Otherwise, is_same matches the
// false_type version. Compiler only choose the best match version.

template <typename T1, typename T2>
constexpr bool is_same_v = is_same<T1, T2>::value;
```

**Examples**

* `static_assert(not is_same_v<int, unsigned>)`

T1 = int, T2 = unsigned, primary template matches. No way to make T to match
specialization.

* `static_assert(is_same_v<int, int>)`

T1 = int, T2 = int, primary template matches, T = int -- specialization matches

## is_same_raw

This is not standard type traits, but it is kind of useful. Take two types, and remove each
cv qualifiers and then compares them. If the two types are the same after removing these
cv qualifiers, then I'm treat them the same. So this might be helpful considering that
all of our type traits want us to remove both the const and the volatile qualifiers.

```cpp
template <typename T1, typename T2>
using is_same_raw = is_same<remove_cv_t<T1>, remove_cv_t<T2>>;

template <typename T1, typename T2>
constexpr bool is_same_raw_v = is_same_raw<T1, T2>::value;
```

## is_floating_point: redux

This is using alias template.

```cpp
template <typename T>
using is_floating_point = std::bool_constant<
        is_same_raw_v<float,       T>
     || is_same_raw_v<double,      T>
     || is_same_raw_v<long double, T>>;
```

## is_integral: redux

```cpp
template <typename T>
using is_integral = std::bool_constant<
        is_same_raw_v<bool,               T>
     || is_same_raw_v<char,               T>
     || is_same_raw_v<char8_t,            T>
     || is_same_raw_v<char16_t,           T>
     || is_same_raw_v<char32_t,           T>
     || is_same_raw_v<wchar_t,            T>
     || is_same_raw_v<signed char,        T>
     || is_same_raw_v<short,              T>
     || is_same_raw_v<int,                T>
     || is_same_raw_v<long,               T>
     || is_same_raw_v<long long,          T>
     || is_same_raw_v<unsigned char,      T>
     || is_same_raw_v<unsigned short,     T>
     || is_same_raw_v<unsigned int,       T>
     || is_same_raw_v<unsigned long,      T>
     || is_same_raw_v<unsigned long long, T>>;
```

It might be implemented using parameter pack.

`is_type_in_pack` is a meta-function, it takes a type and ti take a list of bunch of other types.
Adn `is_type_in_pack` will biscally returned true if that type was anywhere in that list.

```cpp
template <typename TargetT, typename ...Ts>
using is_type_in_pack = ...;

template <typename T>
using is_integral = is_type_inpack<remove_cv_t<T>,
    bool,
    char, char8_t, char16_t, char32_t, wchar_t,
    signed char, unsigned char,
    signed short, unsigned short,
    signed int, unsigned int,
    signed long, unsigned long,
    signed long long, unsigned long long>;
    
```

## is_array

The definition of `is_array`.

```cpp
template <typename T>
struct is_array : std::false_type {};

// inbounded array
template <typename T, std::size_t N>
struct is_arrya <T[N]> : std::true_type {};

// unbounded array
template <typename T>
struct is_array<T[]> : std::true_type {};
```

Some examples.

```cpp
static_assert(is_array,int[5]>);
// T = int[5] - primary template matches
// T = int, N = 5 - first specialization matches
// no way to form T to match second sepcialization

static_assert(is_array<int[]>);
// T = int[] == primary template matches
```

## is_pointer

```cpp
namespace detail {
// Primary template - most things are not pointers
template <typename T>
struct is_pointer_impl : std::false_type {};

// When we have a pointer
template <typename T>
struct is_pointer_impl<T *> : std::true_type {};

} // end of namespace detail

// alias template 
template <typename T>
using is_pointer = detail::is_pointer_impl<remove_cv_t<T>>;
```

## is_union

This meta-function is actually impossible to implement without support from the compiler. Both clang
and gcc provide this particular compiler intrinsic to determine if a type is a union.

```cpp
template <typename T>
using is_union = std::bool_constant<__is_union(T)>;
```


## is_class_or_union

What do we know about unions and classes that is unique to those two types?

* They can have members.
* Devise a way to detect if a type can have a member.
* How can you tell if a class has a member?
* The syntax for a pointer-to-member is valid for any class, even without any members.

Eg.

`int*` is a valid pointer type, but does not have to point to anything. meta-programming is aimed to
deal with types, not the data.

* int Foo::* is a member pointer type, does not have to point to anything.

```cpp
// An empty struct, with no members of any kind
struct Bar {};

// BarIntObjectMemPtr is an alias for a type that is a
// pointer to a member of class Bar, where the member
// is an int.
using BarintObjectMemPtr = int Bar::*;

// This, however, generates a hard compiler error
usign LongIntObjectMemPtr = ing long::*;
```

# Funciton Overload Resolution

```cpp
namespace detail {
std::true_type is_nullptr(std::nullptr_t);
// ... it will match anything. But it is the least priority.
// It will only ever be used if nothing else matches. It'll
// only be used if it's the only one that matches.
std::false_type is_nullptr(...);

} // end of namespace detail

template <typename T>
using is_null_pointer =
    decltype(detail::is_nullptr(std::devlval<T>()));
```

```cpp
static_assert(not is_null_pointer<int>::value);
// This only match the second one.
static_assert(is_null_pointer<std::nullptr_t>::value);
// This can match two versions of `is_nullptr`, but overload
// resolution will choose the first one, because the first
// one is the best match.
```

Another case.

```cpp
template <typename T>
struct TypeIdentity { using type = T; };

namespace detail {
template <typename T>
std::true_type isconst(TypeIdentity<T const>);

template <typename T>
std::false_type isconst(TypeIdentity<T>);
} // end of namespace detail

template <typename T>
using is_const =
    decltype(detail::isconst(std::declval<TypeIdentity<T>>()));
```

This uses technique called `Tag Dispatch`. `Tag Dispatch` is where we are creating a type that is
just being used as a tag.

`TypeIdentity` takes no space, and it is very efficient to pass these guys around.


# SFINAE (Substitution Failure Is Not An Error)

```cpp
template <typename T>
std::true_type can_have_pointer_to_member(int T::*);

template <typename T>
std::false_type can_have_pointer_to_member(...);
```

## is_class

Almost always implemented as compiler intrinsic. Because compiler is much faster dealing with
intrinsics than dealing with even the simplest template stuff. Without the support of compiler, it
is kind of impossible to distinguish between union and non-union class type.

We have `is_union` (with help from the compiler)

* The definition is:

```cpp
namespace detail {
template <typename T>
std::bool_constant<not std::is_union_v<T>>
is_class_or_union(int T::*);

// We only want to use the return type of `is_class_or_union`
// function. So we don't need to create implementation for this
// function.
template <typename T>
std::false_type is_class_or_union(...);
}

template <typename T>
using is_class = decltype(detail::is_class_or_union<T>(nullptr));
```

Implement `is_class` using `constexpr`.

```cpp
namespace detail {
template <typename T> constexpr bool is_class_or_union(int T::*) {
  return not std::is_union<T>::value;
}

template <typename T> constexpr bool is_class_or_union(...) {
  return false;
}
} // end of namespace detail

template <typename T>
using is_class =
    std::bool_constant<detail::is_class_or_union<T>(nullptr)>;
```

```cpp
template <typename T>
using is_const =
    decltype(detail::isconst(std::declval<TypeIdentity<T>>()));
```
* **decltype** --- tells you to pretend that compiler will evaluate this expression, and give me the
result the type that you would get from the evaluated expression.
* **declval** --- is there so you can grab a reference to any type. It just gives you a reference
  to something as if you had created one as if you had one. So it just declaration, it's there's no
  implementation.

## is_in_pack

```cpp
// Template declaration, with no definition
template <typename TargetT, typename... Ts>
struct IsInPack;

// Base case --- no more elements
template <typename TargetT>
struct IsInPack<TargetT> : std::false_type {};

// NOTES: is_in_pack uses partial specialization to match
// the two same types. If the first one matches the target,
// we are done.
template <typename TargetT, typename... Ts>
struct IsInpack<TargetT, TargetT, Ts...> : std::true_type {};

// Otherwise, check the remaining ones.
template <typename TargetT, typename T, typename... Ts>
struct IsInpack<TargetT, T, Ts...> : IsInPack<Target, Ts...> {};
```

Examples

```cpp
static_assert(IsInPack<int, double,char,int,float>::value);
static_assert(not IsInPack<long, double,char,int,float>::value);
```

The version of using `is_base_of`.

```cpp
namespace detail {
template <typename T>
struct TypeIdentitiy { using type = T; };

template <typename... Ts>
struct IsInPackImpl : TypeIdentity<Ts>... {};

tmeplate <typename TargetT, typename... Ts>
using IsInpack = std::is_base_of<
    TypeIdentity<TargetT>,
    detail::IsInPackImpl<Ts...>>;
} // end of namespace detail
```

Examples

```cpp
static_assert(IsInPack<int, double,char,int,float>::value);
static_assert(not IsInPack<long, double,char,int,float>::value);
```

## is_base_of

If Derived is derived from Base or if both are the same non-union class (in both cases
cv-qualification), provides the member constant value equal to `true`. Otherwise value is `false`.

The possible definition of `is_base_of` is as follows:

```cpp
namespace detail {
template <typename B>
std::true_type test_pre_ptr_convertible(const volatile B*);

template <typename>
std::false_type test_pre_ptr_convertible(const volatile void*);

template <typename, typename>
auto test_pre_is_base_of(...) -> std::true_type;

template <typename B, typename D>
auto test_pre_is_base_of(int) ->
  decltype(test_pre_ptr_convertible<B>(static_cast<D*>(nullptr)));
} // end of namespace detail

template <typename Base, typename Derived>
struct is_base_of :
    std::integral_constant<
        bool,
        std::is_class<Base>::value && std::is_class<Derived>::value &&
        decltype(details::test_pre_is_base_of<Base, Derived>(0))::value
    > {};
```

# Learning materials.
* [Template Metaprogramming: Type Traits Part I](https://www.youtube.com/watch?v=tiAVWcjIF6o)
* [Template Metaprogramming: Type Traits Part II](https://www.youtube.com/watch?v=dLZcocFOb5Q)

- Modern Template Metaprogramming: A Compendium, Part I, Walter E. Brown,
   CppCon 2014,
  Link: [Part  I](https://www.youtube.com/watch?v=Am2is2QCvxY)
  [Part II](https://www.youtube.com/watch?v=a0FliKwcwXE)

# References

* [Modern C++ Design](https://github.com/maybez/maybe/blob/master/Books/C%2B%2B/Modern%20C%2B%2B%20Design/Modern%20C%2B%2B%20Design_ENG.pdf)
* [About C++ Template Metaprogramming](https://github.com/ghjang/cpp_practical_tmp_study)
* [Template Metaprogramming with C++](https://github.com/PacktPublishing/Template-Metaprogramming-with-CPP)
* [Generic programming in OO Languages](https://hrjiang.github.io/fopl/13_templates-generics.pdf)
* [30 Core Guidelines for Writting Clean, Safe, and Fast Code](https://ptgmedia.pearsoncmg.com/images/9780137647842/samplepages/9780137647842_Sample.pdf)
* [Reflective Metaprogramming in C++](https://2018.cppconf-piter.ru/talks/day-2/track-a/1.pdf)
* [Tag Dispatching](https://blog.csdn.net/qmickecs/article/details/70574506)
