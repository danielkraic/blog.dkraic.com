+++
date = "2015-09-24T09:10:10+02:00"
title = "CMake"
tags = [ "dev", "cpp" ]
+++

CMake is tool for managing software build process using a compiler-independent method. It is designed to support directory hierarchies and applications that depend on multiple libraries.

```bash
cmake -DCMAKE_BUILD_TYPE=Debug ..
```

```bash
export CC=/usr/bin/clang
export CXX=/usr/bin/clang++
cmake ..
```

```bash
cmake -DCMAKE_C_COMPILER=/usr/bin/clang -DCMAKE_CXX_COMPILER=/usr/bin/clang++ ..
```

### Example

```cmake
cmake_minimum_required(VERSION 2.6)
enable_language(CXX)
project(project-name)

# setting build output directory
set(EXECUTABLE_OUTPUT_PATH ${PROJECT_BINARY_DIR}/bin)

set(CMAKE_CXX_COMPILER "g++-4.8")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -O2 -Wall -Werror -Weffc++ -fopenmp -std=c++11")

include_directories("/usr/include/customlib")

FILE(GLOB WEB_BENCHMARK_SRCS *.cpp)
add_executable(bin-name ${PROJECT_SRCS})
include_directories(${CMAKE_SOURCE_DIR})

target_link_libraries(myapp test)

include(CheckCXXCompilerFlag)
CHECK_CXX_COMPILER_FLAG("-std=c++11" COMPILER_SUPPORTS_CXX11)
CHECK_CXX_COMPILER_FLAG("-std=c++0x" COMPILER_SUPPORTS_CXX0X)
if(COMPILER_SUPPORTS_CXX11)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
elseif(COMPILER_SUPPORTS_CXX0X)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++0x")
else()
        message(STATUS "The compiler ${CMAKE_CXX_COMPILER} has no C++11 support. Please use a different C++ compiler.")
endif()
```
