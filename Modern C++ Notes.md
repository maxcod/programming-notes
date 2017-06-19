### Usage specific Guidelines
* No Raw Loops
  * Use `STL` algorithms that operate on ranges
    * `std::rotate`
    * `std::accumulate`
    * `std::reduce`
    * `std::min_element`
    * `std::stable_partition`
    * `std::stable_sort`
    * `std::find...`
    * `std::copy...`
    * `std::transform`
  * Use range-based loops
    ```c++
    for(const auto& e: r) f(e);
    for(auto& e: r) e = f(e);
    ```
  * Keep the body of the loops short. If the body is more than few lines, name the block as a function.
  * Use lambdas for preducates, comparisons and projectsion, but keep them short
  If lambdas get longer, use a function and give it a name.
  * Use function objects with template member function to simulate `polymorphic lambda`
  
* No Raw Pointers (includes unique_ptr and shared_ptr)
  * Indirection through pointers
    * changes semantics of copy, assignment and equality
    * incidental data structures
    * thread safety concerns
    * inefficient
    * shared pointer as good as a global variable
  * Using make_shared<> to create shared_ptrs eliminates an extra heap allocation
  * pass `sink` arguments by value and move into place
  
* Less/No raw synchronization primities i.e. `mutexes, atomic, semaphore, memory fences`
  * Tasks can be scheduled in thread pools. Look up `std::async`

* 
### Features
#### Random Number Generator
* Don't use `rand()`.
  * `time` updates every second, so if it is run multiple times in a second, it outputs same.
  * `srand` takes 32-bit seed even if `time(NULL)` returns 64-bit entropy.
  * `rand()` has a range of 0 to 32767. It is `linear congruential`.
  * `random_generator%N` returns non-uniform distribution. Modulo -> Non-uniform distribution because the range of `rand()` may not be divisible by `N`.
* Use  `<random>`
  * `uniform_int_distribution`
  * `uniform_real_distribution`
  * `mt19937` \ `mt19937_64` is extremely fast, high-quality, seedable, reproducible(in another platform), but not cryptographically secure
  * `uniform_int_distribution` preserves perfect uniformity of the underlying generator. It is determinitstic, but not invariant.
  * deterministic
  ```c++
   std::mt19937 mt(1729); //mt: engine:[0,2^32], 1729- deterministic 32-bit seed
   std::uniform_int_distribution<int> dist(0,99); //distribution [0,99]
   std::cout<<dist(mt)<<" ";
  ```
  * non-deterministic
  ```c++
  std::random_device rd;
  std::mt19937 mt(rd());//non-deterministic 32-bit seed
  std::uniform_int_distribution<int> dist(0,99);
  std::cout<<dist(mt)<<" ";
  ```
  * `random_device` is non-seedable, non-reproducible, possibly crypto-secure and slow
    * `random_shuffle` uses `rand()`
    * use `shuffle`. It uses URNG in place, does not make a copy
    ```c++
    template <typename RanIt, typename URNG>
      void shuffle(RanIt f, RanIt l, URNG&& g)      
    ```
  * Multiple threads **cannot** simultaneously call the URNG.
  * `uniform_int_distribution` can be skipped if the target range is power of 2. e.g. [0, 2^17]. The LSB output of the random generator can be masked for use.
#### Chrono
#### Pointers  
* Don't use `NULL`. Use `null_ptr`
#### Inline namespaces

### General Guidelines

### Other Guidelines
* Use own-implmentation of routines using `STL` functions
    * slide
    ```c++
    template <typename It> 
    auto slide(It first, It last, It pos) -> std::pair<It, It>
    {
      if (pos < first) return { pos, std::rotate(pos, frist, last) };
      if (last < pos) return { std::rotate(first, last, pos), pos };
      return { first, llast };
    }
    ```
    * gather
    ```c++
    template <typename BiIt, typename UnPred> 
    auto gather(BiIt f, BiIt l, BiIt p, UnPred s) -> std::pair <BiIt, BiIt>
    {
        return { stable_partition(f, p, not1(s)), 
                 stable_partition(p, l, s) };
    }
    ```
    * trimming strings
    ```c++
    std::string trim(const std::string &s) {
        return trimLeft(trimRight(s));
    }

    std::string trimLeft(const std::string &s) {
        auto temp = s;
        temp.erase(std::begin(temp), 
                    std::find_if(std::begin(temp), std::end(temp), 
                        [](char c){return !std::isspace(c, std::locale()); 
                    }));
        return temp;
    }

    std::string trimRight(const std::string &s) {
        auto temp = s;
        temp.erase(std::find_if(std::rbegin(temp), std::rend(temp), 
                    [](char c){return !std::isspace(c, std::locale()); }).base(), 
                       std::end(temp));
        return temp;
    }
    ```

