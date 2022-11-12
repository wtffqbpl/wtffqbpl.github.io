---
title: CMAKE---Multiple directories
date: 2022-10-31 00:01:50
cover: /img/cmake/cmake-logo.png
excerpt: CMake tutorials with multiple directories.
tags: CMake
---

# Overview

CMake is a directory-based project management tool. If your project has multiple directories, more `CMakeLists.txt` files should be created in your project directories.

# Situation

As the following directories, each directory is a standalone module. For example:

1. the root target has its own source file(s) in `${ROOT}/src` directory.
2. `hello_object_lib` is a `OBJECT` target, and it has its own including paths. So we use a CMakeLists.txt file for target management.
3. `hello_shared_lib` is a `SHARED` library target, the root project will link this shared library or provide for other projects.
4. `hello_static_lib` is a `STATIC` library target, this target will generate a static library file for other projects.

```
tree
.
├── CMakeLists.txt
├── hello_object_lib
│   ├── CMakeLists.txt
│   ├── inc
│   └── src
│       └── main.cc
├── hello_shared_lib
│   ├── CMakeLists.txt
│   ├── include
│   │   └── hello
│   │       └── hello.hpp
│   └── src
│       ├── hello.cc
│       ├── internal.cc
│       └── internal.hpp
├── hello_static_lib
│   ├── CMakeLists.txt
│   ├── include
│   │   └── hello
│   │       └── hello.hpp
│   └── src
│       ├── hello.cc
│       ├── internal.cc
│       └── internal.hpp
└── src
    └── main.cc
```

The content of root `CMakeLists.txt` is as follows. Each sub-directory should be added in this CMakeLists.txt.

In this CMakeLists.txt, we define a executable target `multiple_exec`, all of the other targets will be linked to this executable target. There's a difference should be noticed. `OBJECT` target cannot be linked into other targets using `target_link_libraries` command before CMake V3.12. So if you use the older CMake version, you should use the 

```cmake
cmake_minimum_required(VERSION 3.21)

project(multiple_dirs
        VERSION 1.0
        DESCRIPTION "Multiple directory project"
        LANGUAGES CXX
)

# Add subdirectories with examples.
add_subdirectory(hello_object_lib)
add_subdirectory(hello_shared_lib)
add_subdirectory(hello_static_lib)

# add an example executable
add_executable(multiple_exec)

# add sources to the example executable
target_sources(multiple_exec PRIVATE src/main.cc)
# link libraries
target_link_libraries(
        multiple_exec
        PRIVATE hello_object_target hello_shared_target hello_static_target)
```

# References

* [My-Advanced-CMake-Repo](https://github.com/wtffqbpl/AdvancedCMake)
