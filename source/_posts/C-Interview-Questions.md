---
title: C++ Interview Questions
toc: true
date: 2023-05-13 11:14:52
cover: /img/cpp/cpp-basics-5.jpg
excerpt: Most frequently C++ interview questions you should know.
tags:
    - C++
    - Interview
---

# What is actual use of friend function and classes in C++?

If you declare a function is a friend of a class, then this function can visit private members in this class.
For example, it is a good idea of the `<<` and `>>` operator overloading and adding as a friend of those classes.

```c++
#include <iostream>

class Base {
  int x;
  
public:
  Base() {}
  Base(int x) : x{x} {}
  
  friend void func(Base &obj);
};

void fun(Base &obj) {
  std::cout << obj.x << std::endl;
  obj.x = 20;
  std::cout << obj.x << std::endl;
}

int main()
{
  Base b{10};
  fun(b);
  
  return 0;
}

```

The `friend` specifier allows the designated class access to protected data or functionality within the class making the friend statement. For example, in the below code anyone may ask a child for their name, but only the mother and the child may change the name.
You can take this simple example further by considering a more complex class such as a window. Quite likely a window will have many function/data elements that should not be publicly accessible, but are needed by a related class such as a WindowManger.

```c++
class Child {
  // Mother class members can access the private parts of class Child.
  friend class Mother;
  
public:
  string name(void);
  
protected:
  void setName(string newName);
};
```

At work we `use friends for testing code`, extensively. It means we can provide proper encapsulation and information hiding for the main application code. But also we can have separate test code that uses friends to inspect internal state and data for testing.

* **Friend Definition**

Friend definition allows to define a function in class-scope, but the function will not be defined as a member function, but as a free function of the enclosing namespace, and won't be visible normally except for argument dependent lookup. That makes it especially useful for operator overloading:

```c++
namespace utils {

class f {
private:
  using int_type = int;
  int_type value;
  
public:
  // let's assume it doesn't only need .value, but some internal stuff.
  friend f operator+(f cosnt &a, f const &b) {
    // name resolution finds names in class-scope.
    // int_type is visible here.
    return f(a.value + b.value);
  }
  
  int getValue() cosnt { return value; }
};
}

int main() {
  utils::f a, b;
  std::cout << (a + b).getValue(); // valid;
  
  return 0;
}
```

* **Private CRTP Base Class**

Sometimes, you find the need that a policy needs access to the derived class:

```c++
// Possible policy used for flexible-class
template <typename Derived>
struct Policy {
  void doSomething() {
    // casting this to Derived* requires us to see that we are a 
    // base-class of Derived.
    some_type cosnt &t = static_cast<Derived *>(this)->getSomething();
  }
};

// not, derived privately
template <typename <typename> class SomePolicy>
struct FlexibleClass : private SomePolicy<FlexibleClass> {
  // We derive privately, so the base-class wouldn't notice that,
  // (even though it's the base itself!), so we need a friend declaration
  // to make the base a friend of us.
  friend class SomePolicy<FlexibleClass>;
  
  void doStuff() {
    // calls doSomething of the policy.
    this->doSomething();
  }
  
  // will return useful information
  some_type getSomething();
};
```

You will find a non-contrived example for that in this example. Another code 
