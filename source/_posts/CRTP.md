---
title: Curiously Recurring Template Pattern --- CRTP
toc: true
date: 2023-05-13 11:53:14
cover: /img/cpp/cpp-basics-5.jpg
excerpt: CRTP --- To achieve static polymorphism.
tags:
    - C++
    - C++14
    - C++17
    - C++20
---

# Curiously Recurring Template Pattern (CRTP)

The `Curiously Recurring Template Pattern` is an idiom in which a class X derives from a class template Y, taking a template parameter Z, where Y is initialized with `Z = X`. For example,

```c++
template <typename Z>
class Y {};

class X : public Y<X> {};
```

* **Intent**: To achieve static polymorphism
* **Implementation**: Make use of base class template specialization.
* **Also known as**: Upside-down inheritance, static-polymorphism.

CRTP may be used to implement "compile-time polymorphism", when a base class exposes an interface, and derived classes implement such interface.

```c++
#include <iostream>

template <typename Derived>
struct Base { void name() { (static_cast<Derived *>(this))->impl(); }};

struct D1 : public Base<D1> { void impl() { std::cout << "D1::impl()\n"; }};
struct D2 : public Base<D2> { void impl() { std::cout << "D2::impl()\n"; }};

void test()
{
  // Base<D1> b1; b1.name(); // undefined behavior
  // Base<D2> b2; b2.name(); // undefined behavior
  D1().name();
  D2().name();
}
```

Some use cases for this pattern are a `static polymorphism` and other meta-programming techniques.

## Static polymorphism
Typically, the base class template will take advantage of the fact that member function bodies (definitions) are not instantiated until long after their declarations, and will use members of the derived class within its own member functions, via the use of a cast. For example:

```c++
template <typename T>
struct Base {
  void interface()
  {
    // ... 
    static_cast<T *>(this)->impl();
    // ... 
  }
  
  static void static_func() {
    // ...
    T::static_sub_func();
    // ...
  }
};

struct Derived : public Base<Derived>
{
  void impl() { std::cout << "Derived().impl()\n"; }
  static void static_sub_func();
};
```

In the above example, the function `Base<Derived>::interface()`, though declared before the existence of the `struct Derived` is known by the compiler, is not actually instantiated by the compiler until it is actually called by some later code which occurs after the declaration of `Derived` (not shown in the above example), so that at the time the function "impl" is instantiated, the declaration of `Derived::impl()` is known.

This technique achieves a similar effect to the use of `virtual functions`, without the costs (and some flexibility) of `dynamic polymorphism`. This particular use of the CRTP has been called `simulated dynamic binding` by some.

To elaborate on the above example, consider a base class with no virtual functions. Whenever the base class calls another member function, it will always call its own base class functions. When we derive a class from this base class, we inherit all the member variables and member functions that were not overridden (no constructors or destructors). If the derived class calls an inherited function which then calls another member function, then that function will never call any derived or overridden member function which then another member function, then that function will never call any derived or overridden member functions in the derived class.
However, if base class member functions use CRTP for all member function calls, the overridden functions in the derived class will be selected at compile time. This effectively emulates the virtual function call system at compile time without hte costs in size or function call overhead (VTBL structures, and method lookups, multiple-inheritance VTBL machinery) at the disadvantage of not being able to make this choice at runtime.

## Object Counter

The main purpose of an object counter is retrieving statistics of object creation and destruction for a given class. This can be easily solved using CRTP.

```c++
template <typename T>
struct Counter {
  static inline int objects_created = 0;
  static inline int objects_alive = 0;
  
  Counter() {
    ++objects_created;
    ++objects_alive;
  }
  
  Counter(const Counter&) {
    ++objects_created;
    ++objects_alive;
  }
proteced:
  ~counter() // objects should never be removed through pointers of this type.
  {
    --objects_alive;
  };
};

class X : public Counter<X>
{
  // ...
};

class Y : public Counter<Y>
{
  // ...
};
```

Each time an object of class `X` is created, the constructor of `counter<X>` is called, incrementing both the created and alive count. Each time an object of class `X` is destroyed, the alive count is decremented. It is important to note that `Counter<X>` and `Counter<Y>` are two separate classes and this is whey they will keep separate counts of `X`s and `Y`s. In this example of CRTP, this distinction of classes is the only use of the template parameter (`T` in `Counter<T>`) and the reason whey we cannot use a simple un-templated base class.

## Polymorphic Chaining
[Method chaining](https://en.wikipedia.org/wiki/Method_chaining), also known as named parameter idiom, is a common syntax for invoking multiple method calls in object-oriented programming languages. Each method returns an object, allowing the calls to be chained together in a single statement without requiring variables to store the intermediate results.
When the named parameter object pattern is applied to an object hierarchy, things can go wrong. Suppose we have such a base class:

```c++
class Printer {
public:
  Printer(std::ostream &pstream) : m_stream(pstream) {}
  
  template <typename T>
  Printer &print(T &&t) { m_stream << t; return *this; }
  
  template <typename T>
  Printer &println(T &&t) { m_stream << t << std::endl; return *this; }
  
private:
  std::ostream &m_stream;
};
```
Then Prints can be easily chained:
```shell
Printer(myStream).println("Hello").println(500);
```

However, if we define the following derived class:

```c++
class CoutPrinter : public Printer
{
public:
  CoutPrinter() : Printer(cout) {}
  
  CoutPrinter &SetConsoleColor(Color c)
  {
    // ...
    return *this;
  }
};
```

We 'lose' the concrete class as soon as we invoke a function of the base:

```c++
// v --- we have a 'Printer' here, not a 'CoutPrinter'
CoutPrinter().print("hello ").SetConsoleColor(Color.red).println("Printer!");
// compile error
```
This happens because 'print' is a function of the base - 'Printer' - and then it returns a 'Printer' instance.
The CRTP can be used to avoid such problem and to implement "Polymorphic chaining":

```c++
// Base class
template <typename ConcretePrinter>
class Printer {
public:
  Printer(std::ostream &pstream) : m_stream(pstream) {}
  
  template <typename T>
  ConcretePrinter &print(T &&t) {
    m_stream << t;
    return static_cast<ConcreatePrinter &>(*this);
  }
  
  template <typename T>
  ConcretePrinter &println(T &&t)
  {
    m_stream << t << std::endl;
    return static_cast<ConcretePrinter &>(*this);
  }
  
private:
  std::ostream &m_stream;
};

// Derived class
class CoutPrinter : public Printer<CoutPrinter>
{
public:
  CoutPrinter() : Printer(std::cout) {}
  
  CoutPrinter &SetConsoleColor(Color c)
  {
    // ...
    return *this;
  }
};

// Usage:
CoutPrinter().print("Hello ").SetConsoleColor(Color.red).println("Printer!");
```

## Polymorphic Copy Construction
When using polymorphism, one sometimes need to create copies of objects by the base class pointer. A commonly used idiom for this is adding a virtual clone function that is defined in every derived class. The CRTP can be used to avoid having to duplicate that function or other similar functions in every derived class.

```c++
// Base class has a pure virtual function for cloning
class AbstractShape {
public:
  virtual ~AbstractShape () = default;
  virtual std::unique_ptr<AbstractShape> clone() const = 0;
};

// This CRTP class implements clone() for Derived
template <typename Derived>
class Shape : public AbstractShape {
public:
  std::unique_ptr<AbstractShape> clone() const override {
    return std::make_unique<Derived>(static_cast<Derived const &>(*this));
  }
  
protected:
  // We make clear Shape class needs to be inherited
  Shape() = default;
  Shape(const Shape &) = default;
  Shape(Shape &&) = default;
};

// Every derived class inherits from CRTP class instead of abstract class
class Square : public Shape<Square> {};
class Circle : public Shape<Circle> {};
```
This allows obtaining copies of squares, circles or any other shapes by `shapePtr->clone()`.


CRTP widely employed for static polymorphism without bearing the cost of virtual dispatch mechanism. Consider the following code we have not used virtual keyword & still achieved the functionality of polymorphism (specifically satic polymorphism).

```c++
template <typename specific_animal>
struct animal {
  void who() { implementation().who(); }
  
private:
  specific_animal &implementation() {
    return *static_cast<specific_animal *>(this);
  }
};

struct dog : public animal<dog> {
  void who() {
    std::cout << "dog" << std::endl;
  }
};

struct cat : public animal<cat> {
  void who() {
    std::cout << "cat" << std::endl;
  }
};

template <typename specific_animal>
void who_am_i(animal<specific_animal> &animal) {
  animal.who();
}
```

## Pitfalls
One issue with static polymorphism is that without using a general base class like `AbstractShape` from the above example, derived classes cannot be stored homogeneously - that is, putting different types derived from the same base class in the same container. For example, a container defined as `std::vector<Shape *>` does not work because `Shape` is not a class, but a template needing specialization. A container defined as `std::vector<Shape<Circle>*>` can only store `Circle`s, not `Square`s. This is because each of the classes derived from the CRTP base class `Shape` is a unique type. A common solution to this problem is to inherit from a shared base class with a virtual destructor, like the `AbstractShape` example above, allowing for the creation of a `std::vector<AbstractShape*>`.
