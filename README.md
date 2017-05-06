* [Foundation](#foundation)
    * [Getting Started](#getting-started)
        * [Insertion Sort](#insertion-sort)
        * [Order of growth](#order-of-growth)
        * [Divide and conquer](#divide-and-conquer)
        * [Merge Sort](#merge-sort)
    * [Growth of functions](#growth-of-functions)
        * [`Theta` notation](#theta-notation)
        * [`O` notation](#o-notation)
        * [`omega` notation](#omega-notation)
        * [`o` notation](#o-notation)
        * [`w` notation](#w-notation)
    * [Divide and conquer](#divide-and-conquer)
        * [Find Max sub-array](#find-max-sub-array)
* [Sorting and order statistics](#sorting-and-order-statistics)
    * [Heap-Sort](#heap-sort)
        * [Heaps](#heaps)
        * [Heap Sort](#heap-sort)
        * [Priority queues](#priority-queues)
    * [Quick-Sort](#quick-sort)
        * [Performance of quick-sort](#performance-of-quick-sort)
    * [Sorting in linear time](#sorting-in-linear-time)
        * [Counting Sort](#counting-sort)
        * [Radix Sort](#radix-sort)
        * [Bucket Sort](#bucket-sort)

--------------------------------------------------------------------------


# Foundation

## Getting Started
### Insertion Sort

```cpp
template<typename BiItr>
void insertion_sort(BiItr first, BiItr last) {
    for (auto i = first + 1; i < last; ++i) {
       auto key = *i;
       auto j = i;
       while (--j >= first && *j > key) *(j + 1) = *j;
       *(j + 1) = key;
    }
}
```

1. Design technique - Incremental approach
2. Insertion sort - quadratic function of n
3. Worst-case * upper bound on the running time for any inputs
4. Average-case - roughly as bad as worst-case

### Order of growth
Consider the leading term of a formula
Lower orders are less significant for larger values of n
  
### Divide and conquer 
1. Break problems into several sub-problems
2. Solve sub-problems recursively
3. combine them to create a solution to the original problem

### Merge Sort

```cpp
void merge_sort(itr first, itr last) {
    if (last > first) {
        auto middle = first + (last - first) / 2;
        merge_sort(first, middle);
        merge_sort(middle, last);
        std::inplace_merge(first, middle, last);
    }
}

// implementation for inplace_merge
template<typename itr>
void merge(itr first, itr middle, itr last) {
    while (middle < last) {
        auto key = *middle;
        auto j = middle - 1;
        while (j >= first && *j > key) {
            *(j + 1) = *j;
            --j;
        }
        *(j + 1) = key;
        ++middle;
    }
}
```

## Growth of functions
### `Theta` notation
> If there exists `x0, c1, c2` such that `0 <= c1 g(x)  <=  f(x)  <=  c2 g(x) for all n >= n0`

### `O` notation
> If there exists n0 and c1 such that `0 <= f(x) <= c1 g(x) for n >= n0`
asymptotic upper bound

### `omega` notation
> If there exists n0 and c1 such that `0 <= c1 g(x) <= f(x) for n >= n0`
asymptotic lower bound

### `o` notation
1. same as O notation
2. O notation may not be asymptotically tight
3. o notation denotes upper bound which is not asymptotically tight
4. O notation hold for some constant `c1 > 0`
5. o notation holds for all constant `c1 > 0`

### `w` notation
> same as O vs o, here omega vs w.

## Divide and conquer
### Find Max sub-array
1. Find a sub-array such that highest sum of contiguous elements
2. Array contains `-ve` and `+ve` numbers
3. 3 cases when divide the array into 2
    * sub-array right
    * sub-array left
    * sub-array across two halves
4. 3 Methods
    * Bruit-force method
    * Divide and conquer
    * Kadane’s method

```cpp
//bruit-force alogorithm
template<typename itr>
int bf_algo(itr first, itr last) {
    auto m_s = 0;
    for (auto i = first; i < last; ++i) {
        auto cur = 0;
        for (auto j = i; j < last; ++j) {
            cur += *j;
            m_s = std::max(m_s, cur);
        }
    }
    return m_s;
}

//divide-conquir algorithm
template<typename itr>
int cr_algo(itr first, itr middle, itr last) {
    auto c_l = 0, c_r = 0;
    auto m_l = 0, m_r = 0;

    for (auto i = middle - 1; i >= first; --i) {
        c_l += *i;
        m_l = std::max(m_l, c_l);
    }

    for (auto i = middle; i < last; ++i) {
        c_r += *i;
        m_r = std::max(m_r, c_r);
    }
    return m_l + m_r;
}

template<typename itr>
int dc_algo(itr first, itr last) {
    if (first == last - 1) return *first;

    auto middle = first + (last - first) / 2;
    return std::max({dc_algo(first, middle), dc_algo(middle, last), cr_algo(first, middle, last)});
}

//kadanes algorithm
template<typename itr>
int kd_algo(itr first, itr last) {
    auto m_s = 0, c_m = 0;
    for (auto i = first; i < last; ++i) {
        c_m = std::max(*i, c_m + *i);
        m_s = std::max(m_s, c_m);
    }
    return m_s;
}
```

# Sorting and order statistics
## Heaps
1. data structure nearly complete binary tree
2. tree completely filled except the lowest [which filled from left up to a point
3. length represent the total number of elements 
4. `heap-size` - how many elements in the heap are stored with in array
5. Two kinds of heaps [heap properties]
    * `max-heap  —> A[parent(i)] >= A[i]`
    * `min-heap  —> A[parent(i)] <= A[i]`
6. For heapsort max-heap is used.

1. `max-heapify ==> O(log n)`
2. `build-max-heap ==> O(n)`
3. `heapsort ==> O(n log n)`

1. `parent(i) ==> return(i/2)`
2. `left(i) ==> return 2i`
3. `right(i) ==> return 2i + 1`

## Heap Sort

```cpp
constexpr auto left(std::size_t i) { return i << 1; }
constexpr auto right(std::size_t i) { return (i << 1) + 1; }

void max_heapify(std::vector<int>& vec, std::size_t i, std::size_t heap_size) {
    auto l = left(i);
    auto r = right(i);
    auto largest = heap_size >= l && vec[l-1] > vec[i-1] ? l : i;
    largest = heap_size >= r && vec[r-1] > vec[largest-1] ? r : largest;

    if (largest != i) {
        std::swap(vec[largest-1], vec[i-1]);
        max_heapify(vec, largest, heap_size);
    }
}

void build_max_heap(std::vector<int>& vec) {
    for (auto j = vec.size() / 2; j > 0; --j) {
        max_heapify(vec, j, vec.size());
    }
}

void heap_sort(std::vector<int>& vec) {
    build_max_heap(vec);
    auto heap_size = vec.size();
    for (auto i = vec.size(); i > 1; --i) {
        std::swap(vec[0], vec[i - 1]);
        heap_size--;
        max_heapify(vec, 1, heap_size);
    }
}
```

1. `max_heapify` - maintain the max heap property `{parent(i) >= i}`
2. `build_max_heap` 
    1. all nodes under any node will follow the `max_heapify`
    2. Which means all elements under the node will be lesser than the parent node.
3. `heap-sort`
    1. After build heap first element is the largest
    2. Swap the first with the last, then call `max_heapify` on first.
    3. Max `heapify` on first with `heap_size - 1` will bring the second largest to first 
    4. continue till `heap_size > 1` will sort the elements

```cpp
template<typename Itr, typename Comp>
void heapify(Itr first, Itr last, size_t i, Comp comp) {
    auto l = left(i);
    auto r = right(i);
    const size_t distance = last - first;
    auto index = l <= distance && comp(*(first + l - 1), *(first + i - 1)) ? l : i;
    index = r <= distance && comp(*(first + r - 1), *(first + index - 1)) ? r : index;

    if (index != i) {
        std::swap(*(first + i - 1), *(first + index - 1));
        heapify(first, last, index, comp);
    }
}

template<typename Itr, typename Comp>
void build_heap(Itr first, Itr last, Comp comp) {
    if (last -first < 2) return;

    size_t distance = (last - first) / 2;
    while (distance > 0) heapify(first, last, distance--, comp);
}

template<typename Itr, typename Comp>
void heap_sort(Itr first, Itr last, Comp comp) {
    build_heap(first, last, comp);

    while (last - first > 1) 
        heapify(++first, last, 1, comp);
}
```

### Priority queues

1. Data structure maintaining set of S elements associated with a key
2. Operations supports
    1. Insert
    2. maximum
    3. extract-maximum
    4. Increase-key
3. max-priority queues and min-priority queues

```cpp
template<typename Itr, typename Comp>
void heap_max(Itr first, Itr last, Comp comp) {
    std::swap(*first, *(last - 1));
    heapify(first, last - 1, 1, comp);
}

template<typename Itr, typename C>
void heap_insert(Itr first, Itr last, C comp) {
    size_t i = last - first;
    while (i > 1 && comp(*(first + i - 1), *(first + parent(i) - 1))) {
        std::swap(*(first + i - 1), *(first + parent(i) - 1));
        i = parent(i);
    }
}
```

> Note:  
> Here max_element will be pushed to the last and the rest is
> `heapified` One can get the `max_element` by `pop_back`. 
> Similary `heap_insert` expected the  element is inserted at the end. Then
> rearrange the container to make it a heap.

## Quick-Sort
1. Worst case `O(n^2)` 
2. expected `O(n log n)`

```cpp
template<typename Itr, typename C>
Itr qpart(Itr first, Itr last, C comp) {
    auto val = *(last - 1);
    auto i = first - 1;
    for (auto j = first; j < last - 1; ++j) {
        if (comp(*j, val)) {
            ++i;
            std::swap(*i, *j);
        }
    }
    std::swap(*(i + 1), *(last - 1));
    return i + 1;
}

template<typename Itr, typename C>
void quick_sort(Itr first, Itr last, C comp) {
    if (last - 1 > first) {
        Itr pivot = qpart(first, last, comp);
        quick_sort(first, pivot, comp);
        quick_sort(pivot + 1, last, comp);
    }
}
```

### Performance of quick-sort
1. Performance depends on the balanced partition
2. If the partition is balanced it run asymptotically as fast as merge sort
3. If the partition is unbalanced it can run as slow as insertion sort
4. Best case when the partition happens exactly at the middle
5. Worst case when the partition happens with (n-1) element and 1
6. Randomized quick sort when the pivot selected randomly

```cpp
template<typename Itr, typename C>
auto rand_part(Itr first, Itr last, C comp) {
    auto i = rand(first, last);
    std::swap(*i, *(last - 1));
    return qpart(first, last, comp);
}
```

7. Tail-recursive quick-sort
    1. quick sort contains two recursive calls to itself
    2. Second recursive call is not necessary and can be avoided.
    3. Compilers can optimize tail-recursive calls using stack space `O(1)`
    4. Reusing the same stack for all other recursive calls

```cpp
template<typename Itr, typename C>
void tail_qsort(Itr first, Itr last, C comp) {
    while (last > first) {
        auto pivot = qpart(first, last, comp);
        tail_qsort(first, pivot, comp);
        first = pivot + 1;
    }
}
```

## Sorting in linear time
1. Any comparison sort algorithm requires  `Omega(n lg n)`  comparisons in the worst case
2. Sorting algorithms like heap sort, merge sort all runs `Omega(n lg n)`
3. These sorts are called comparison sorts as they compare elements to sort
4. Comparison sorts lower bound would be `(n lg n)`
5. sorting algorithms that run linear time
    1. counting sort
    2. radial sort
    3. bucket sort
6. These are operational sorting algorithms instead of comparison

### Counting Sort
1. any element is an _integer_ in the range `0..k`
2. counting sort of `n` elements in the range `[0..k]` requires two arrays
    * array to store sorted elements `arr_s[0..n]`
    * array `arr_w[0..k]` as the working storage

```cpp
template<size_t N, typename BiItr>
void count_sort(BiItr first, BiItr last) {
    using type = typename std::iterator_traits<BiItr>::value_type;
    
    type arr_w[N] = {0};
    for (auto i = first; i < last; ++i)
        arr_w[*i] += 1;
    
    std::partial_sum(std::begin(arr_w), std::end(arr_w), std::begin(arr_w));
    
    std::vector<type> arr_s(std::distance(first, last), 0);
    //from last to first makes it stable sort
    for (auto i = last - 1; i >= first; --i) {
        arr_s[arr_w[*i] - 1] = *i;
        arr_w[*i] -= 1;
    }
    
    std::copy(std::begin(arr_s), std::end(arr_s), first);
}
```
1. Overall time complexity is `theta(k + n)`
2. in practice when we have `k = O(n)`, then time complexity is `theta(n)`
3. Counting sort is a stable sort
4. Counting sort is not a comparison sort, it uses the actual values of the
elements to index into an array
5. Counting sort normally work with integers, If we provide a function to extract number from
the element, then it works with all types

### Radix Sort
1. Sort the least significant digit first, then the next least significant digit
2. A stable sort [counting sort] is used to sort the digits

```cpp
template<size_t N, typename BiItr>
void radix_sort(BiItr first, BiItr last) {
    using type = typename std::iterator_traits<BiItr>::value_type;
    
    auto j = 0;
    std::vector<type> arr_s(std::distance(first, last), 0);
    while (++j <= N) {
        auto key = [=](const type val) {
            auto pow1 = static_cast<int>(std::pow(10, j));
            auto pow2 = static_cast<int>(std::pow(10, j - 1));
            return (val % pow1) / pow2;
        };
        
        int arr_w[10] = {0};
        for (auto i = first; i < last; ++i)
            arr_w[key(*i)] += 1;
        
        std::partial_sum(std::begin(arr_w), std::end(arr_w), std::begin(arr_w));
        
        //from last to first makes it stable sort
        for (auto i = last - 1; i >= first; --i) {
            arr_s[arr_w[key(*i)] - 1] = *i;
            arr_w[key(*i)] -= 1;
        }
        
        std::copy(std::begin(arr_s), std::end(arr_s), first);
    }
}
```
### Bucket Sort
1. Bucket sort assume the input is drawn from a **uniform distribution** and 
has average running time of `O(n)`
2. It assumes the input are in the range `[0, 1)`
3. Bucket sort divides the interval `[0, 1)` into `n equal sized array` or buckets
4. Sort each bucket and output the numbers from bucket in order to get the final result
5. It requires  `an auxiliary array of n linked lists` (buckets)
```cpp
template<size_t N, typename BiItr>
void bucket_sort(BiItr first, BiItr last) {
    using type = typename std::iterator_traits<BiItr>::value_type;
    
    //Create N buckets
    std::vector<type> vec[N];
    for (auto i = first; i < last; ++i) {
        //multiplying each elements with N
        auto val = static_cast<int>(*i * N);
        vec[val].push_back(*i);
    }
    
    //sorting individual bucket
    for (auto& v : vec) 
        std::sort(std::begin(v), std::end(v));
    
    //Copying the content to the input collection
    auto sz = 0;
    for (auto& v : vec) {
        std::copy(std::begin(v), std::end(v), first + sz);
        sz += v.size();
    }
}
```
