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

### General Guidelines
* 
