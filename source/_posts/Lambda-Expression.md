---
title: Lambda Expression
date: 2022-11-08 12:54:15
cover: /img/cpp/cpp.png
excerpt: Learn how to use init-capture in lambda expression since C++14.
tags:
  - C++
  - C++14
---

# Lambda Expression

## Init Capture

This feature is introduced since C++14, it also called `generalized lambda capture`.


```cpp
namespace {
class Widget {
private:
  std::string name_;
  
public:
  explicit Widget(std::string name) : name_(std::move(name)) {}
  
  friend std::ostream &operator<<(std::ostream &os, const Widget &item) {
    os << item.name_;
    return os;
  }
  
  void test_capture_member() {
    [name = std::ref(name_)] { std::cout << "name: " << name; }();
  }
};

} // namespace

void test_init_capture() {
  auto pw_outer = std::make_unique<Widget>("widget");
  
  // test init capture.
  [pw = std::move(pw_outer)] { std::cout << *pw.get(); }();
  [pw = std::make_unique<Widget>("widget2")] { std::cout << *pw.get(); }();
  
  auto pw = std::make_unique<Widget>("test");
  // test class member capture.
  pw->test_capture_member();
}
```

Captures apply only to `non-static local variables` (including parameters) visible in the scope where
the lambda is created. So if you want to capture a class member, the original way is using a local
variable. For example:

```cpp
namespace {

class Widget {
private:
  std::string name_;
  
public:
  explicit Widget(std::string name) : name_(std::move(name)) {}
  
  void test_capture() {
    auto &name = name_;
    [name] { std::cout << name; }();
  }
};

} // namespace
```

Since C++14, we can use `init capture` feature for this situation. It means we can define a new
variable and initialize this local variable using class member during lambda capture. For example:

```cpp
namespace {
class Widget {
private:
  std::string name_;
  
  // ...
  
  void test_capture() {
    [name = std::ref(name_)] { std::cout << name; }();
  }
};

} // namespace
```

Please visit site for complete code [Lambda Expression Testcase](https://github.com/wtffqbpl/cpp_weekly/blob/main/paradigms/lambda_expressions.cc)


# References
* Item 31: Avoid default capture modes, Effective Modern C++, Scott Meyer