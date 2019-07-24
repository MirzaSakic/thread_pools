# Thread Pool Library

![](https://img.shields.io/badge/language-c++-orange.svg) 
![](https://img.shields.io/github/stars/ganler/thread_pools.svg?style=social)
![](https://img.shields.io/github/languages/code-size/ganler/thread_pools.svg)

## Quick Start

```c++
#include <thread_pools.hpp>
#include <iostream>

int main()
{
    thread_pools::spool<10> pool;
    // thread_pools::pool pool(10);  // default pool
    // thread_pools::dpool dpool;    // dynamic pool

    auto result = pool.enqueue([]() { return 2333; });
    std::cout << result.get() << '\n';
}
```

## Introduction

In the last 2 days, I implemented 3 kinds of thread pools:
- **thread_pools::spool<Sz>**: Static pool which holds fixed number of threads in std::array<std::thread, Sz>.
- **thread_pools::pool**: Default pool which holds fixed number of threads in std::vector<std::thread>.
- **thread_pools::dpool**: Dynamic pool which holds unfixed number of threads in std::unordered_map<thread_index, std::thread>(This is very efficient).

These kinds of pools can be qualified with different tasks according to user's specific situations.

The `spool` and `pool` are nearly the same, as their only difference is that they use different containers.

However, I myself design the growing strategy of the dynamic pool. For a `dpool(J, I, K)`:

- Dynamically adapt to the task size;
- The total threads are no more than K(Set by the user);
- The number of idle threads are no more than I(Set by the user);
- Create J threads when constructing.(Please make sure: J <= I && I < K && K > 0, or there will be a `std::logic_error`)

## Advantages

- **Header-only**: Just move the .hpp file you to ur project and run.
- **Easy to use**: Just 2 API: Constructor and enqueue.
- **Easy to learn**: I tried to use lines as few as possible to make my code concise. Not only have I added some significant comments in the code, but I've drawn a figure to show u how I designed the dynamic pool as well. Good for guys who want to learn from my code.
- **High performance**: The results of the benchmark have shown this.

## Benchmark

```shell
mkdir build && cd build
cmake .. && make -j4 && ./thread_pools
```

Copy the output python script and run it. You can see the results.
(Note that each task cost 10ms. For each pool, it can at most create `psize` threads to finish `mission_sz` tasks.)
![](images/benchmark.png)

## How I designed the dynamic pool

![](images/thread_theory.png)

## TODOS

- Thread pool that holds fixed kind of functions. (Reduce dynamic allocation when std::function<void()> SOO cannot work)
- Replace shared_ptr<std::packaged_task<...>> with exception-safety wrapped raw pointer.
- Lock-free thread pool.
- Comparision with other libraries.
