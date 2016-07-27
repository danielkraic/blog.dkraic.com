+++
date = "2015-09-24T10:33:23+02:00"
title = "CPP random engine"
tags = [ "dev", "cpp", "cpp11", "random" ]
+++

C++11 random engine

#### Example - sleep for 100-1000 milliseconds
```cpp
#include <random>
#include <thread>
#include <chrono>

std::random_device rd;
std::default_random_engine dre(rd());
std::uniform_int_distribution<int> id(100,1000);

std::this_thread::sleep_for(std::chrono::milliseconds(id(dre)));
```
