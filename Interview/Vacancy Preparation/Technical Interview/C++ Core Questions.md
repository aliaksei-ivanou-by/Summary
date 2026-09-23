# General C++ Technical Interview Questions and Answers

> Reusable C++ question bank: language and build model, object lifetime, STL, concurrency, systems, performance, debugging and design. Vacancy-specific files should link here instead of copying these answers.

# Question Index

> **C-specific questions are in a separate bank**: [C Language Questions](<./C Language Questions.md>) - standards and C89, undefined behaviour, memory sections, alignment and packing, flexible array members, strings, function pointers, opaque structs, the preprocessor, static and shared libraries, `dlopen`/`LD_PRELOAD`, core dumps, Valgrind and sanitizers. This bank is C++.

Questions use stable topic-specific IDs. Every answer begins with a short bullet summary and keeps details, examples and edge cases below it.

## Modern C++ (CPP-001–CPP-020)

- [CPP-001. What are the Rule of Zero, Three, Five, and the so-called Seven/Ten variants?](#question-cpp-001)
- [CPP-002. What does `std::move` actually do?](#question-cpp-002)
- [CPP-003. What is the difference between lvalue and rvalue?](#question-cpp-003)
- [CPP-004. What are prvalue, xvalue and glvalue?](#question-cpp-004)
- [CPP-005. What is a forwarding reference?](#question-cpp-005)
- [CPP-006. What is `std::forward` used for?](#question-cpp-006)
- [CPP-007. Why should move constructors often be `noexcept`?](#question-cpp-007)
- [CPP-008. When does the compiler generate a move constructor?](#question-cpp-008)
- [CPP-009. What is copy elision?](#question-cpp-009)
- [CPP-010. Why can `const T&` bind to a temporary?](#question-cpp-010)
- [CPP-011. When does lifetime extension not work?](#question-cpp-011)
- [CPP-012. What is the difference between `auto` and `decltype(auto)`?](#question-cpp-012)
- [CPP-013. What is `decltype`?](#question-cpp-013)
- [CPP-014. What is `explicit` used for?](#question-cpp-014)
- [CPP-015. What do `= default` and `= delete` mean?](#question-cpp-015)
- [CPP-016. What is undefined behavior?](#question-cpp-016)
- [CPP-017. Undefined vs unspecified vs implementation-defined behavior?](#question-cpp-017)
- [CPP-018. What is the as-if rule?](#question-cpp-018)
- [CPP-019. What is `volatile` for?](#question-cpp-019)
- [CPP-020. Can you add things to namespace `std`?](#question-cpp-020)

## Object Model and OOP (CPP-021–CPP-028)

- [CPP-021. Why does a base class often need a virtual destructor?](#question-cpp-021)
- [CPP-022. How do virtual functions usually work?](#question-cpp-022)
- [CPP-023. Is there a vtable per object?](#question-cpp-023)
- [CPP-024. What happens when calling a virtual function from a constructor or destructor?](#question-cpp-024)
- [CPP-025. What is object slicing?](#question-cpp-025)
- [CPP-026. Overloading vs overriding?](#question-cpp-026)
- [CPP-027. What is virtual inheritance?](#question-cpp-027)
- [CPP-028. What determines the size of a C++ class?](#question-cpp-028)

## RAII and Smart Pointers (CPP-029–CPP-038)

- [CPP-029. What is RAII?](#question-cpp-029)
- [CPP-030. How does `std::unique_ptr` work?](#question-cpp-030)
- [CPP-031. How should `unique_ptr` be passed to a function?](#question-cpp-031)
- [CPP-032. How does `shared_ptr` work?](#question-cpp-032)
- [CPP-033. What is the difference between `make_shared<T>()` and `shared_ptr<T>(new T)`?](#question-cpp-033)
- [CPP-034. Why is this dangerous?
](#question-cpp-034)
- [CPP-035. Why are cyclic `shared_ptr` references a problem?](#question-cpp-035)
- [CPP-036. What does `weak_ptr::lock()` do?](#question-cpp-036)
- [CPP-037. What is `enable_shared_from_this`?](#question-cpp-037)
- [CPP-038. Is `shared_ptr` thread-safe?](#question-cpp-038)

## STL and Data Structures (CPP-039–CPP-051)

- [CPP-039. How does `std::vector` work?](#question-cpp-039)
- [CPP-040. What is the difference between `size()` and `capacity()`?](#question-cpp-040)
- [CPP-041. `reserve()` vs `resize()`?](#question-cpp-041)
- [CPP-042. When are vector iterators invalidated?](#question-cpp-042)
- [CPP-043. Why is `push_back` amortized O(1)?](#question-cpp-043)
- [CPP-044. Why is `vector` often faster than `list`?](#question-cpp-044)
- [CPP-045. `map` vs `unordered_map`?](#question-cpp-045)
- [CPP-046. Why can `unordered_map` become O(n)?](#question-cpp-046)
- [CPP-047. What is rehashing?](#question-cpp-047)
- [CPP-048. What is strict weak ordering?](#question-cpp-048)
- [CPP-049. `push_back` vs `emplace_back`?](#question-cpp-049)
- [CPP-050. What is `std::string_view`?](#question-cpp-050)
- [CPP-051. What is `std::span`?](#question-cpp-051)

## Multithreading and Memory Model (CPP-052–CPP-070)

- [CPP-052. What is a data race?](#question-cpp-052)
- [CPP-053. Race condition vs data race?](#question-cpp-053)
- [CPP-054. What does a mutex provide?](#question-cpp-054)
- [CPP-055. `lock_guard` vs `unique_lock`?](#question-cpp-055)
- [CPP-056. What is `scoped_lock`?](#question-cpp-056)
- [CPP-057. What causes deadlock?](#question-cpp-057)
- [CPP-058. What is a condition variable?](#question-cpp-058)
- [CPP-059. Why must condition variables use a predicate?](#question-cpp-059)
- [CPP-060. What is `std::atomic`?](#question-cpp-060)
- [CPP-061. Atomic vs mutex?](#question-cpp-061)
- [CPP-062. What is compare-and-swap?](#question-cpp-062)
- [CPP-063. `compare_exchange_weak` vs `strong`?](#question-cpp-063)
- [CPP-064. What is `memory_order_relaxed`?](#question-cpp-064)
- [CPP-065. What are acquire and release semantics?](#question-cpp-065)
- [CPP-066. What is happens-before?](#question-cpp-066)
- [CPP-067. What is sequential consistency?](#question-cpp-067)
- [CPP-068. What is false sharing?](#question-cpp-068)
- [CPP-069. How would you implement producer-consumer?](#question-cpp-069)
- [CPP-070. How would you make a thread-safe queue?](#question-cpp-070)

## Performance (CPP-071–CPP-079)

- [CPP-071. How do you investigate a performance problem?](#question-cpp-071)
- [CPP-072. Latency vs throughput?](#question-cpp-072)
- [CPP-073. What are P50, P95 and P99?](#question-cpp-073)
- [CPP-074. What is batching?](#question-cpp-074)
- [CPP-075. What is throttling?](#question-cpp-075)
- [CPP-076. What is coalescing?](#question-cpp-076)
- [CPP-077. What is backpressure?](#question-cpp-077)
- [CPP-078. Why can allocation be expensive?](#question-cpp-078)
- [CPP-079. How do you reduce allocation overhead?](#question-cpp-079)

## Windows and Debugging (CPP-080–CPP-085)

- [CPP-080. Static vs dynamic library?](#question-cpp-080)
- [CPP-081. What are `LoadLibrary` and `GetProcAddress`?](#question-cpp-081)
- [CPP-082. Why is `DllMain` dangerous for complex work?](#question-cpp-082)
- [CPP-083. How would you debug a host-process crash caused by a native plug-in?](#question-cpp-083)
- [CPP-084. How would you investigate a deadlock?](#question-cpp-084)
- [CPP-085. How would you debug a bug that appears only after several hours?](#question-cpp-085)

## Rapid-Fire C++ (CPP-086–CPP-096)

- [CPP-086. `new` vs `malloc`?](#question-cpp-086)
- [CPP-087. `delete` vs `delete[]`?](#question-cpp-087)
- [CPP-088. What is placement new?](#question-cpp-088)
- [CPP-089. What is `std::terminate`?](#question-cpp-089)
- [CPP-090. What is stack unwinding?](#question-cpp-090)
- [CPP-091. Strong exception guarantee?](#question-cpp-091)
- [CPP-092. What is copy-and-swap?](#question-cpp-092)
- [CPP-093. What is ODR?](#question-cpp-093)
- [CPP-094. What does `inline` really mean?](#question-cpp-094)
- [CPP-095. What is ABI?](#question-cpp-095)
- [CPP-096. What is name mangling?](#question-cpp-096)

## Build Model, Language Details and Templates (CPP-097–CPP-114)

- [CPP-097. What is a translation unit, and how does C++ source become an executable?](#question-cpp-097)
- [CPP-098. What belongs in a header, and include guards vs `#pragma once`?](#question-cpp-098)
- [CPP-099. Forward declaration vs `#include`?](#question-cpp-099)
- [CPP-100. How do C/C++ macros work, and what are the common traps?](#question-cpp-100)
- [CPP-101. Declaration vs definition?](#question-cpp-101)
- [CPP-102. What are internal, external and no linkage?](#question-cpp-102)
- [CPP-103. What should you know about fundamental types, `nullptr` and `std::byte`?](#question-cpp-103)
- [CPP-104. What are integer promotions and usual arithmetic conversions?](#question-cpp-104)
- [CPP-105. `enum` vs `enum class`?](#question-cpp-105)
- [CPP-106. How does `const` work with pointers and member functions?](#question-cpp-106)
- [CPP-107. When should each C++ cast be used?](#question-cpp-107)
- [CPP-108. What initialization forms exist, and why use braces carefully?](#question-cpp-108)
- [CPP-109. `constexpr` vs `consteval` vs `constinit`?](#question-cpp-109)
- [CPP-110. How do lambda captures work, and what can dangle?](#question-cpp-110)
- [CPP-111. How do template instantiation and specialization work?](#question-cpp-111)
- [CPP-112. What are variadic templates and fold expressions?](#question-cpp-112)
- [CPP-113. SFINAE vs concepts and `requires`?](#question-cpp-113)
- [CPP-114. Which C++20/C++23 features are most relevant in production?](#question-cpp-114)

## STL and Concurrency Extensions (CPP-115–CPP-128)

- [CPP-115. How do you choose between `vector`, `deque`, `list` and `forward_list`?](#question-cpp-115)
- [CPP-116. How do `map`/`set` differ from their `multi` variants?](#question-cpp-116)
- [CPP-117. What contract must a hash function and equality predicate satisfy?](#question-cpp-117)
- [CPP-118. What are iterator categories and why do they matter?](#question-cpp-118)
- [CPP-119. Why prefer STL algorithms and ranges to handwritten loops?](#question-cpp-119)
- [CPP-120. What are the erase-remove idiom and `std::erase_if`?](#question-cpp-120)
- [CPP-121. `lower_bound` vs `upper_bound` vs `equal_range`?](#question-cpp-121)
- [CPP-122. What is `std::optional`, and when should it not be used?](#question-cpp-122)
- [CPP-123. `std::variant` vs `std::any`?](#question-cpp-123)
- [CPP-124. When should you use `pair`, `tuple` and structured bindings?](#question-cpp-124)
- [CPP-125. How should `std::chrono` be used?](#question-cpp-125)
- [CPP-126. `std::thread`: `join`/`detach` vs `std::jthread`?](#question-cpp-126)
- [CPP-127. How do `future`, `promise` and `async` work?](#question-cpp-127)
- [CPP-128. When is `shared_mutex` useful?](#question-cpp-128)

## Systems and Networking Foundations (CPP-129–CPP-139)

- [CPP-129. Process vs thread, and what is a context switch?](#question-cpp-129)
- [CPP-130. What are a call stack and a stack frame?](#question-cpp-130)
- [CPP-131. How do virtual memory, page faults and the TLB relate?](#question-cpp-131)
- [CPP-132. How do cache hierarchy, alignment and padding affect performance?](#question-cpp-132)
- [CPP-133. What is endianness?](#question-cpp-133)
- [CPP-134. System calls vs interrupts, CPU exceptions and OS signals?](#question-cpp-134)
- [CPP-135. What IPC mechanisms would you choose between?](#question-cpp-135)
- [CPP-136. TCP vs UDP, and why does TCP need message framing?](#question-cpp-136)
- [CPP-137. What is the socket lifecycle, and how do I/O multiplexers help?](#question-cpp-137)
- [CPP-138. HTTP vs HTTPS vs WebSocket?](#question-cpp-138)
- [CPP-139. Livelock and starvation vs deadlock?](#question-cpp-139)

## Software Design and APIs (CPP-140–CPP-149)

- [CPP-140. What do the SOLID principles mean in practice?](#question-cpp-140)
- [CPP-141. How do DRY, KISS and YAGNI complement each other?](#question-cpp-141)
- [CPP-142. Why prefer composition over inheritance?](#question-cpp-142)
- [CPP-143. How do PImpl, header-only and compiled libraries trade off?](#question-cpp-143)
- [CPP-144. How do common GoF patterns differ?](#question-cpp-144)
- [CPP-145. Observer vs pub-sub and event-driven architecture?](#question-cpp-145)
- [CPP-146. Exceptions vs error codes vs `std::expected`?](#question-cpp-146)
- [CPP-147. How do you evolve an API without breaking source or binary compatibility?](#question-cpp-147)
- [CPP-148. What does ACID mean?](#question-cpp-148)
- [CPP-149. How does an LRU cache work?](#question-cpp-149)

## Algorithms and Interview Patterns (CPP-150–CPP-163)

- [CPP-150. How does Floyd's tortoise-and-hare cycle detection work?](#question-cpp-150)
- [CPP-151. How does Brent's cycle-detection algorithm differ from Floyd's?](#question-cpp-151)
- [CPP-152. Two pointers vs sliding window?](#question-cpp-152)
- [CPP-153. How do you write binary search with a correct invariant?](#question-cpp-153)
- [CPP-154. BFS vs DFS?](#question-cpp-154)
- [CPP-155. How does Dijkstra's shortest-path algorithm work?](#question-cpp-155)
- [CPP-156. How does topological sorting work, and how does it detect a cycle?](#question-cpp-156)
- [CPP-157. How does Union-Find / Disjoint Set Union work?](#question-cpp-157)
- [CPP-158. How do heaps solve priority and top-K problems?](#question-cpp-158)
- [CPP-159. How does Kadane's maximum-subarray algorithm work?](#question-cpp-159)
- [CPP-160. How does Knuth-Morris-Pratt string search work?](#question-cpp-160)
- [CPP-161. How do you merge overlapping intervals?](#question-cpp-161)
- [CPP-162. What are prefix sums and difference arrays?](#question-cpp-162)
- [CPP-163. What are monotonic stacks and queues used for?](#question-cpp-163)

## UI Architecture (CPP-164–CPP-166)

- [CPP-164. MVC, MVP and MVVM - what actually differs?](#question-cpp-164)
- [CPP-165. What belongs in a view model, and what must not?](#question-cpp-165)
- [CPP-166. How do you refactor a fat UI class into that shape without stopping delivery?](#question-cpp-166)

---

# 1. Modern C++

## Question CPP-001

[↑ Back to question index](#question-index)

### Question CPP-001 — What are the Rule of Zero, Three, Five, and the so-called Seven/Ten variants?

**Short answer**

- Prefer the **Rule of Zero**: put ownership into RAII members and let the compiler generate destruction, copy and move behavior.
- If a class owns a resource manually, the **Rule of Three** covers destructor, copy constructor and copy assignment; modern C++ extends the policy to the **Rule of Five** by considering both move operations as well.
- These are design guidelines, not syntax rules. A class may define all operations, default them, delete them or deliberately support only a subset, but the ownership semantics must stay consistent.
- The standard has six special member functions, including the default constructor. “Rule of Seven” and “Rule of Ten” are not canonical C++ terms; when someone uses them, ask which extra operations they count.

**Details and nuances**

The canonical interview answer has three guidelines:

| Guideline | Members written by the class | Typical use |
|---|---|---|
| Rule of Zero | None of the copy/move/destructor set | Ordinary value types built from RAII members |
| Rule of Three | Destructor, copy constructor, copy assignment | Pre-C++11 or copyable manual resource ownership |
| Rule of Five | Rule of Three plus move constructor and move assignment | Manual resource ownership in modern C++ |

Rule of Three: if a class manually defines one of:

- destructor
- copy constructor
- copy assignment operator

it usually needs all three.

Rule of Five extends this with:

- move constructor
- move assignment operator

Rule of 0 means that ideally resource ownership is delegated to RAII types such as `std::vector`, `std::string`, `std::unique_ptr`, so the class does not need to manually implement any special member functions.

These are design guidelines, not language rules. The trigger is usually manual ownership, not merely the presence of a constructor.

**Broken example: a destructor without copy control**

```cpp
#include <cstddef>

class BrokenBuffer {
public:
    explicit BrokenBuffer(std::size_t size)
        : data_(new int[size]) {}

    ~BrokenBuffer() {
        delete[] data_;
    }

private:
    int* data_{};
};

BrokenBuffer first(16);
BrokenBuffer second = first; // shallow copy: both objects own the same pointer
// Both destructors call delete[] for the same allocation: undefined behavior.
```

Because the destructor expresses ownership, copying and assignment also need an explicit ownership policy.

**Rule of Three example: deep copy without move operations**

```cpp
#include <algorithm>
#include <cstddef>

class CopyableBuffer {
public:
    explicit CopyableBuffer(std::size_t size)
        : size_(size),
          data_(size == 0 ? nullptr : new int[size]{}) {}

    ~CopyableBuffer() {
        delete[] data_;
    }

    CopyableBuffer(const CopyableBuffer& other)
        : CopyableBuffer(other.size_) {
        if (size_ != 0) {
            std::copy_n(other.data_, size_, data_);
        }
    }

    CopyableBuffer& operator=(const CopyableBuffer& other) {
        if (this == &other) {
            return *this;
        }

        int* replacement =
            other.size_ == 0 ? nullptr : new int[other.size_];
        if (other.size_ != 0) {
            std::copy_n(other.data_, other.size_, replacement);
        }

        delete[] data_;
        data_ = replacement;
        size_ = other.size_;
        return *this;
    }

private:
    std::size_t size_{};
    int* data_{};
};
```

Allocation and copying finish before the old resource is released, so a failed allocation leaves the target unchanged. Because copy operations and a destructor are user-declared, this class has no implicitly generated move operations. An rvalue may still be copied because `const CopyableBuffer&` can bind to it.

**Rule of Five example: manual ownership with deep copy and cheap move**

```cpp
#include <algorithm>
#include <cstddef>
#include <utility>

class Buffer {
public:
    Buffer() = default;

    explicit Buffer(std::size_t size)
        : size_(size),
          data_(size == 0 ? nullptr : new int[size]{}) {}

    ~Buffer() {
        delete[] data_;
    }

    Buffer(const Buffer& other)
        : Buffer(other.size_) {
        if (size_ != 0) {
            std::copy_n(other.data_, size_, data_);
        }
    }

    Buffer& operator=(const Buffer& other) {
        if (this != &other) {
            Buffer copy(other); // copy first: if allocation throws, *this is unchanged
            swap(copy);
        }
        return *this;
    }

    Buffer(Buffer&& other) noexcept
        : size_(std::exchange(other.size_, 0)),
          data_(std::exchange(other.data_, nullptr)) {}

    Buffer& operator=(Buffer&& other) noexcept {
        if (this != &other) {
            delete[] data_;
            size_ = std::exchange(other.size_, 0);
            data_ = std::exchange(other.data_, nullptr);
        }
        return *this;
    }

    void swap(Buffer& other) noexcept {
        using std::swap;
        swap(size_, other.size_);
        swap(data_, other.data_);
    }

private:
    std::size_t size_{};
    int* data_{};
};
```

The copy constructor duplicates the resource. Copy assignment uses copy-and-swap for the strong exception guarantee. Move operations transfer ownership and leave the source valid and empty.

**Rule of Zero example: delegate ownership**

```cpp
#include <cstddef>
#include <vector>

class Buffer {
public:
    Buffer() = default;

    explicit Buffer(std::size_t size)
        : data_(size) {}

private:
    std::vector<int> data_;
};
```

`std::vector` already implements destruction, copying and moving correctly, so all five special member functions can remain compiler-generated.

**Rule of Four-and-a-Half: a related copy-and-swap variant**

This is an informal name, not a separate language rule. It usually means destructor, copy constructor, move constructor, one assignment operator taking its argument by value, and a non-throwing `swap` helper—the "half".

```cpp
#include <algorithm>
#include <cstddef>
#include <utility>

class CompactBuffer {
public:
    CompactBuffer() = default;

    explicit CompactBuffer(std::size_t size)
        : size_(size),
          data_(size == 0 ? nullptr : new int[size]{}) {}

    ~CompactBuffer() {
        delete[] data_;
    }

    CompactBuffer(const CompactBuffer& other)
        : CompactBuffer(other.size_) {
        if (size_ != 0) {
            std::copy_n(other.data_, size_, data_);
        }
    }

    CompactBuffer(CompactBuffer&& other) noexcept {
        swap(*this, other);
    }

    CompactBuffer& operator=(CompactBuffer other) noexcept {
        swap(*this, other);
        return *this;
    }

    friend void swap(CompactBuffer& left, CompactBuffer& right) noexcept {
        using std::swap;
        swap(left.size_, right.size_);
        swap(left.data_, right.data_);
    }

private:
    std::size_t size_{};
    int* data_{};
};
```

For `target = source`, the by-value parameter is copy-constructed. For `target = std::move(source)`, it is move-constructed. Swapping then commits the new state, and the parameter destroys the old state when the function returns. The ordinary Rule of Five is often clearer when copy and move assignment need different behavior or exception specifications.

**What about a Rule of Six—or Rules of One, Two, and Four?**

- There is no generally accepted sequence in which every number names a standard rule.
- "Rule of Three-and-a-Half" is an older copy-and-swap name for the Rule of Three plus a `swap` helper; the Four-and-a-Half variant adds move construction and lets the by-value assignment parameter use either copying or moving.
- "Rule of Six" has sometimes meant the Rule of Five plus a custom `swap`, but this is informal and not a C++ language rule.
- The default constructor is also a special member function, but it is not normally counted in the Rule of Five because default construction policy is separate from copy/move/destruction ownership policy.
- In an interview, lead with Zero, Three, and Five. Mention Four-and-a-Half or Six only as related terminology, and explain which operations you mean.

**What about the so-called Rule of Seven and Rule of Ten?**

Neither name has one definition in the C++ standard or the C++ Core Guidelines. A useful interview response is: “Zero, Three and Five are the established ownership guidelines; please clarify which operations you include in Seven or Ten.” Two author-specific interpretations sometimes encountered are:

- **“Seven”** — the six special member functions plus a non-throwing `swap`.
- **“Ten”** — that set plus value-type operations such as equality, ordering and hashing.

Those lists can be useful as a review checklist, but they are not additional language rules. Equality, ordering and hashing are not special member functions, and not every class should provide them.

**Complete modern class-design checklist**

| Concern | Question to decide |
|---|---|
| Default construction | Does an empty/default state make sense, and does it preserve the invariant? |
| Destruction | Is cleanup automatic through RAII, non-throwing and correct through base pointers? |
| Copy construction | Is the type a value, and if so, what does an independent copy mean? |
| Copy assignment | Does replacement preserve self-assignment safety and the intended exception guarantee? |
| Move construction | Can ownership be transferred cheaply, and what valid state remains in the source? |
| Move assignment | Is self-move harmless enough for the contract, and can the operation be `noexcept`? |
| Swap | Is it useful for generic code or committing state, and can it be non-throwing? |
| Equality | Does the type have value equality, identity equality or no meaningful equality? |
| Ordering | Is a total/partial ordering meaningful, possibly through C++20 `<=>`? |
| Hashing | If the type is a hash key, is the hash consistent with equality? |

This is a ten-point design review, not a “Rule of Ten.” It separates mandatory ownership decisions from optional value-type facilities.

The practical rule from the C++ Core Guidelines is stronger and easier to apply: define none of the copy/move/destructor set when RAII members already provide the right behavior; if one needs an explicit policy, explicitly define or delete the complete set and keep their semantics consistent.

Primary references:

- [Current C++ working draft `[class.special]`: six special member functions](https://eel.is/c++draft/special)
- [C++ Core Guidelines C.20: Rule of Zero](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#Rc-zero)
- [C++ Core Guidelines C.21: Rule of Five](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#Rc-five)
- [WG21 P0198R0 discussion of an informal Rule of Six with `swap`](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2016/p0198r0.pdf)

**Senior point:** Rule of 0 is generally preferable. Write the Rule of Five only when the class genuinely owns a resource that existing RAII types cannot model cleanly.

[↑ Back to question index](#question-index)

---

## Question CPP-002

[↑ Back to question index](#question-index)

### Question CPP-002 — What does `std::move` actually do?

**Short answer**

- `std::move` performs an unconditional cast to an xvalue; it does not itself transfer resources.
- The selected move constructor or move assignment performs the transfer. If no viable move overload exists—or the source is `const`—copying may still occur.
- After a move, the source is valid but usually has an unspecified value: destroy it, assign a new value, or call only operations whose preconditions still hold.

**Details and nuances**

`std::move` does not move anything.

It is essentially a cast that converts an expression into an xvalue, allowing move-enabled overloads to be selected.

Conceptually:

```cpp
template<class T>
std::remove_reference_t<T>&& move(T&& t) noexcept {
    return static_cast<std::remove_reference_t<T>&&>(t);
}
```

The actual resource transfer happens inside the move constructor or move assignment operator.

After a successful move, the source object is still valid but its value is usually unspecified. It may be assigned to or destroyed, but code should not assume that it is empty unless the type documents that guarantee.

```cpp
#include <string>
#include <utility>

std::string source = "payload";
std::string destination = std::move(source); // selects string's move constructor

source.clear();       // valid: reuse by assigning a new state
source = "next";     // also valid
```

Moving from a `const` object usually copies because a normal move constructor needs a non-const rvalue reference in order to modify the source:

```cpp
const std::string source = "payload";
std::string destination = std::move(source); // const string&& cannot bind to string&&
                                              // the copy constructor is selected
```

[↑ Back to question index](#question-index)

---

## Question CPP-003

[↑ Back to question index](#question-index)

### Question CPP-003 — What is the difference between lvalue and rvalue?

**Short answer**

- An lvalue expression identifies a persistent object; an rvalue is a prvalue or xvalue used as a temporary value or as a source whose resources may be reused.
- Non-const lvalue references bind to lvalues, while rvalue references bind to rvalues; `const T&` can bind to either.
- Value category belongs to an expression, not merely to its declared type: a named `T&&` variable is an lvalue expression.

**Details and nuances**

An lvalue represents an object with identity and a stable location.

An rvalue is typically a temporary value or an object whose resources may be reused.

Examples:

```cpp
int x = 10;   // x is an lvalue
int y = x;    // x is still an lvalue

int z = 10;   // literal 10 is a prvalue
```

`std::move(x)` produces an xvalue.

[↑ Back to question index](#question-index)

---

## Question CPP-004

[↑ Back to question index](#question-index)

### Question CPP-004 — What are prvalue, xvalue and glvalue?

**Short answer**

- A **glvalue** identifies an object and is either an lvalue or xvalue; an **rvalue** is either a prvalue or xvalue.
- A **prvalue** computes a value used to initialize an object; an **xvalue** identifies an expiring object whose resources may be reused.
- These categories affect overload resolution, reference binding, `decltype`, temporary materialization and lifetime rules.

**Details and nuances**

Modern C++ value categories can be viewed like this:

```text
expression
├── glvalue
│   ├── lvalue
│   └── xvalue
└── rvalue
    ├── prvalue
    └── xvalue
```

- **lvalue** — has identity and is not expiring
- **xvalue** — has identity but may be moved from
- **prvalue** — pure value, often used to initialize an object
- **glvalue** — expression with identity
- **rvalue** — prvalue or xvalue

[↑ Back to question index](#question-index)

---

## Question CPP-005

[↑ Back to question index](#question-index)

### Question CPP-005 — What is a forwarding reference?

**Short answer**

- A forwarding reference is `T&&` where `T` is deduced in that context, including most `auto&&` declarations; it can bind to both lvalues and rvalues.
- Reference collapsing preserves the caller category: an lvalue deduces `T` as `U&`, while an rvalue deduces `T` as `U`.
- `const T&&` and an `T&&` using an already-known class template parameter are ordinary rvalue references, not forwarding references.

**Details and nuances**

A forwarding reference is `T&&` where `T` is a deduced template parameter.

Example:

```cpp
template<class T>
void foo(T&& value);
```

It can bind to both lvalues and rvalues.

Reference collapsing rules determine the final type:

```text
T&  + &  -> T&
T&  + && -> T&
T&& + &  -> T&
T&& + && -> T&&
```

[↑ Back to question index](#question-index)

---

## Question CPP-006

[↑ Back to question index](#question-index)

### Question CPP-006 — What is `std::forward` used for?

**Short answer**

- `std::forward<T>(value)` conditionally casts a forwarding-reference parameter back to the value category with which the caller supplied it.
- It is needed because every named variable is an lvalue expression, even when its type is `T&&`.
- Use it with the exact deduced template parameter and normally forward a parameter only once; using it elsewhere can unexpectedly move from an object.

**Details and nuances**

`std::forward<T>` preserves the original value category of an argument.

Example:

```cpp
template<class T>
void wrapper(T&& value) {
    process(std::forward<T>(value));
}
```

Without `std::forward`, the named variable `value` is always an lvalue inside the function.

[↑ Back to question index](#question-index)

---

## Question CPP-007

[↑ Back to question index](#question-index)

### Question CPP-007 — Why should move constructors often be `noexcept`?

**Short answer**

- A non-throwing move lets containers relocate elements efficiently while preserving their exception guarantees.
- `std::vector` may copy during reallocation when moving could throw and copying is available; for a move-only throwing type, the strong guarantee may be unavailable.
- Declare `noexcept` only when every operation used by the move is non-throwing; conditional `noexcept` is appropriate for generic types.

**Details and nuances**

Standard containers such as `std::vector` try to provide strong exception guarantees.

During reallocation, `vector` may prefer copying instead of moving if the move constructor can throw.

So:

```cpp
T(T&&) noexcept;
```

can allow `vector` to move elements efficiently.

```cpp
#include <iostream>
#include <vector>

struct Item {
    Item() = default;
    Item(const Item&) { std::cout << "copy\n"; }
    Item(Item&&) noexcept { std::cout << "move\n"; }
};

std::vector<Item> items;
items.reserve(1);
items.emplace_back();
items.emplace_back(); // reallocation can move the first Item
```

If `Item(Item&&)` can throw and copying is available, `vector` may copy existing elements during reallocation so that a failed operation does not leave the original vector partially moved-from. Do not add `noexcept` merely for speed: a move declared `noexcept` that throws calls `std::terminate`.

[↑ Back to question index](#question-index)

---

## Question CPP-008

[↑ Back to question index](#question-index)

### Question CPP-008 — When does the compiler generate a move constructor?

**Short answer**

- The compiler implicitly declares move operations only when no user-declared copy constructor, copy assignment, move operation or destructor suppresses them.
- “User-declared” includes `= default` and `= delete`; a default constructor alone does not suppress moves.
- A declared move can still be defined as deleted when a base/member cannot be moved, and an rvalue may silently fall back to a copy overload.

**Details and nuances**

The compiler implicitly declares a move constructor only when the class has no user-declared:

- copy constructor
- move constructor
- copy assignment operator
- move assignment operator
- destructor

A default constructor does not suppress implicit move generation. A destructor written as `~T() = default` is still user-declared and does suppress it.

```cpp
#include <string>
#include <utility>

struct ImplicitMove {
    std::string value;
};

struct DestructorSuppressesMove {
    std::string value;
    ~DestructorSuppressesMove() = default; // user-declared
};

struct ExplicitPolicy {
    ExplicitPolicy() = default;
    ~ExplicitPolicy() = default;

    ExplicitPolicy(const ExplicitPolicy&) = default;
    ExplicitPolicy& operator=(const ExplicitPolicy&) = default;
    ExplicitPolicy(ExplicitPolicy&&) noexcept = default;
    ExplicitPolicy& operator=(ExplicitPolicy&&) noexcept = default;
};
```

`DestructorSuppressesMove` can still appear move-constructible to `std::is_move_constructible`: its copy constructor may accept an rvalue. That trait tests whether construction from `T&&` is possible, not whether an actual move constructor exists.

An explicitly defaulted special member may also become implicitly deleted when a member cannot perform the requested operation:

```cpp
#include <memory>

struct Owner {
    std::unique_ptr<int> value;

    Owner() = default;
    Owner(const Owner&) = default;            // defined as deleted: unique_ptr is non-copyable
    Owner& operator=(const Owner&) = default; // defined as deleted for the same reason
    Owner(Owner&&) noexcept = default;        // valid
    Owner& operator=(Owner&&) noexcept = default;
};
```

[↑ Back to question index](#question-index)

---

## Question CPP-009

[↑ Back to question index](#question-index)

### Question CPP-009 — What is copy elision?

**Short answer**

- Copy elision constructs an object directly in its destination instead of creating and moving/copying an intermediate object.
- Since C++17, several prvalue cases are guaranteed; NRVO for a named local remains permitted but not universally guaranteed.
- Returning a normal local by `std::move` can inhibit NRVO, so prefer `return value;` unless a distinct reason requires the cast.

**Details and nuances**

Copy elision allows construction directly in the final destination instead of constructing a temporary and copying/moving it.

Example:

```cpp
T make() {
    return T{};
}
```

Since C++17, some cases are guaranteed and no move/copy constructor is required.

```cpp
struct Token {
    Token() = default;
    Token(const Token&) = delete;
    Token(Token&&) = delete;
};

Token makeToken() {
    return Token{}; // guaranteed direct construction in C++17 and later
}
```

NRVO is a related optimization for named local variables:

```cpp
T make() {
    T t;
    return t;
}
```

NRVO itself is not guaranteed in all cases.

Do not add `std::move` to a normal local return just to make it "faster": it can prevent NRVO.

```cpp
T good() {
    T value;
    return value;            // NRVO when applied; otherwise value can be moved
}

T usuallyWorse() {
    T value;
    return std::move(value); // forces an xvalue and commonly blocks NRVO
}
```

[↑ Back to question index](#question-index)

---

## Question CPP-010

[↑ Back to question index](#question-index)

### Question CPP-010 — Why can `const T&` bind to a temporary?

**Short answer**

- The language permits a const lvalue reference to bind to a temporary and, for direct local binding, extends that temporary to the reference lifetime.
- Non-const lvalue references cannot bind because they would allow ordinary mutation of a temporary; an rvalue reference can bind and may also extend lifetime in specific direct-binding contexts.
- Lifetime extension depends on the initialization expression and is not a general ownership mechanism.

**Details and nuances**

C++ allows a const lvalue reference to bind to a temporary and extend the temporary's lifetime.

Example:

```cpp
const std::string& s = std::string("hello");
```

The temporary lives as long as `s`.

The same principle works for a local rvalue reference bound directly to a temporary:

```cpp
const std::string& first = std::string("first");
std::string&& second = std::string("second");

// Both temporaries live until their local reference variables leave scope.
```

This is a language rule, not related to where `const` objects are stored in memory.

[↑ Back to question index](#question-index)

---

## Question CPP-011

[↑ Back to question index](#question-index)

### Question CPP-011 — When does lifetime extension not work?

**Short answer**

- Lifetime extension is not transitive: returning or forwarding a reference that already refers to a temporary does not extend it again.
- Temporaries bound to reference parameters last only through the full expression containing the call, and references stored beyond that point may dangle.
- Reference members, `string_view`, `span`, iterators and lambda captures all require an explicit check that the owner outlives the view/reference.

**Details and nuances**

Lifetime extension is not transitive.

Example:

```cpp
const T& getRef() {
    return T{};
}
```

This returns a dangling reference.

Also, storing a reference member initialized from a temporary can be dangerous depending on context.

The key point is that lifetime extension only happens in specific language-defined binding contexts.

Returning or forwarding a reference does not create a second lifetime extension:

```cpp
#include <string>

const std::string& passthrough(const std::string& value) {
    return value;
}

const std::string& bad = passthrough(std::string("temporary"));
// The temporary is destroyed at the end of this statement; bad then dangles.
```

Non-owning views have the same fundamental risk:

```cpp
#include <string>
#include <string_view>

std::string_view view = std::string("temporary");
// The string is already destroyed; view dangles.
```

[↑ Back to question index](#question-index)

---

## Question CPP-012

[↑ Back to question index](#question-index)

### Question CPP-012 — What is the difference between `auto` and `decltype(auto)`?

**Short answer**

- Plain `auto` uses template-like deduction and normally removes references and top-level `const`; `auto&`, `const auto&` and `auto&&` request particular reference forms.
- `decltype(auto)` applies `decltype` to the initializer and can preserve an exact reference and cv-qualification.
- This precision is useful for forwarding return types but dangerous when it preserves a reference to a local or temporary.

**Details and nuances**

`auto` follows template-like deduction and usually drops references and top-level `const`.

`decltype(auto)` uses `decltype` rules and preserves the exact type category.

Example:

```cpp
int x = 10;
int& ref = x;

auto a = ref;          // int
decltype(auto) b = ref; // int&
```

[↑ Back to question index](#question-index)

---

## Question CPP-013

[↑ Back to question index](#question-index)

### Question CPP-013 — What is `decltype`?

**Short answer**

- For an unparenthesized name/member access, `decltype` yields the entity's declared type.
- Otherwise it encodes expression category: lvalue gives `T&`, xvalue gives `T&&`, and prvalue gives `T`.
- The extra parentheses in `decltype((x))` therefore matter; `decltype` is unevaluated, so the expression is generally not executed.

**Details and nuances**

`decltype(expr)` determines the type of an expression.

Important rule:

```cpp
decltype(x)
```

for an unparenthesized variable gives its declared type.

But:

```cpp
decltype((x))
```

uses expression category rules and usually gives `T&` for an lvalue.

[↑ Back to question index](#question-index)

---

## Question CPP-014

[↑ Back to question index](#question-index)

### Question CPP-014 — What is `explicit` used for?

**Short answer**

- `explicit` prevents a constructor or conversion operator from participating in unwanted implicit conversions and copy-initialization.
- Direct initialization remains available, and `explicit operator bool` still works in contextual Boolean expressions such as `if`.
- Since C++20, `explicit(condition)` can make explicitness depend on a compile-time condition in generic code.

**Details and nuances**

`explicit` prevents unwanted implicit conversions through constructors or conversion operators.

Example:

```cpp
struct Meter {
    explicit Meter(double value);
};
```

This prevents:

```cpp
Meter m = 10.0;
```

but allows:

```cpp
Meter m(10.0);
```

It also prevents an accidental conversion at a function boundary:

```cpp
struct Meter {
    explicit Meter(double value) : value(value) {}
    double value;
};

void printDistance(Meter distance);

printDistance(3.5);        // error: implicit conversion is disabled
printDistance(Meter{3.5}); // explicit and clear
```

Conversion operators can be explicit too. `explicit operator bool` allows contextual checks such as `if (handle)` without allowing arbitrary numeric conversions.

[↑ Back to question index](#question-index)

---

## Question CPP-015

[↑ Back to question index](#question-index)

### Question CPP-015 — What do `= default` and `= delete` mean?

**Short answer**

- `= default` requests the compiler-defined implementation and documents that the operation is intentionally supported.
- `= delete` keeps a function visible to overload resolution but makes selecting it ill-formed, which cleanly disables copying or dangerous conversions.
- Where the declaration is defaulted can affect properties such as triviality and implicit `noexcept`; a defaulted operation can also become deleted because of a base/member.

**Details and nuances**

`= default` asks the compiler to generate the normal implementation.

```cpp
Foo(const Foo&) = default;
```

`= delete` makes a function unavailable.

```cpp
Foo(const Foo&) = delete;
```

Useful for preventing copying or unwanted overloads.

```cpp
class Connection {
public:
    Connection() = default;
    ~Connection() = default;

    Connection(const Connection&) = delete;
    Connection& operator=(const Connection&) = delete;
    Connection(Connection&&) noexcept = default;
    Connection& operator=(Connection&&) noexcept = default;
};

void send(int value);
void send(double) = delete; // rejects accidental narrowing or wrong-unit calls

Connection first;
// Connection second = first;       // error: copying is forbidden
Connection second = std::move(first); // ownership transfer is allowed

send(42);
// send(3.14); // error: explicitly rejected overload
```

Prefer `= default` to an empty handwritten implementation: it preserves the compiler's normal semantics and can retain properties such as triviality when the language rules allow it.

[↑ Back to question index](#question-index)

---

## Question CPP-016

[↑ Back to question index](#question-index)

### Question CPP-016 — What is undefined behavior?

**Short answer**

- Undefined behavior means the C++ standard places no requirements on the program after that operation; a crash is only one possible symptom.
- Optimizers assume UB does not occur, so effects can appear far from the cause or disappear between debug and release builds.
- Prevent it with ownership and bounds-safe design, warnings, sanitizers, static analysis and tests—but understand that tools detect subsets, not every UB case.

**Details and nuances**

Undefined behavior means the C++ standard imposes no requirements on what happens.

Examples:

- dereferencing a null pointer
- signed integer overflow
- accessing an object after lifetime ended
- data race
- out-of-bounds access

Compilers may optimize under the assumption that UB never happens.

[↑ Back to question index](#question-index)

---

## Question CPP-017

[↑ Back to question index](#question-index)

### Question CPP-017 — Undefined vs unspecified vs implementation-defined behavior?

**Short answer**

- **Undefined:** no requirements; the program must avoid it.
- **Unspecified:** the implementation may choose among allowed outcomes for each occurrence and need not document the choice.
- **Implementation-defined:** the implementation chooses an allowed behavior and must document it; portability requires accounting for that choice.

**Details and nuances**

- **Undefined behavior** — no requirements
- **Unspecified behavior** — one of several valid behaviors, implementation does not need to document which
- **Implementation-defined behavior** — implementation chooses and documents behavior

Example of implementation-defined behavior: exact size of some integer types.

[↑ Back to question index](#question-index)

---

## Question CPP-018

[↑ Back to question index](#question-index)

### Question CPP-018 — What is the as-if rule?

**Short answer**

- The implementation may transform the program in any way that preserves the observable behavior required by the abstract machine.
- It permits optimization, including removing objects or calls, but does not legalize changing required I/O, volatile accesses or synchronization-visible behavior.
- Once a program has undefined behavior, there may be no valid observable behavior for the compiler to preserve.

**Details and nuances**

The compiler may transform code however it wants as long as observable behavior remains equivalent according to the language rules.

This allows aggressive optimization.

[↑ Back to question index](#question-index)

---

## Question CPP-019

[↑ Back to question index](#question-index)

### Question CPP-019 — What is `volatile` for?

**Short answer**

- `volatile` tells the implementation that accesses are observable side effects, mainly for memory-mapped I/O and similarly implementation-specific low-level work.
- It does not make a compound operation atomic, establish happens-before or replace a mutex/`std::atomic` for inter-thread synchronization.
- Signal handling has narrow standard rules; ordinary concurrent communication through a volatile object is still a data race.

**Details and nuances**

`volatile` tells the compiler that an object may change for reasons outside normal program flow.

Typical use cases:

- memory-mapped hardware registers
- some low-level embedded interactions

It is **not** a synchronization primitive and does not make code thread-safe.

Use `std::atomic`, mutexes, or other synchronization tools for concurrency.

[↑ Back to question index](#question-index)

---

## Question CPP-020

[↑ Back to question index](#question-index)

### Question CPP-020 — Can you add things to namespace `std`?

**Short answer**

- In general, adding declarations or overloads to `std` is undefined behavior because the namespace belongs to the implementation.
- Only explicitly permitted customizations are valid; some full specializations for program-defined types are allowed when all standard requirements are met.
- Prefer public customization points such as comparators, hash functors, `formatter` specializations where permitted, or argument-dependent lookup in your own namespace.

**Details and nuances**

In general, no.

Adding declarations to `std` is undefined behavior unless the standard explicitly permits it.

One notable historical exception is specializing certain standard templates for user-defined types where the standard allows it, for example some `std::hash` specializations.

You should not add new overloads of standard functions to `std`.

[↑ Back to question index](#question-index)

---

# 2. Object Model and OOP

## Question CPP-021

[↑ Back to question index](#question-index)

### Question CPP-021 — Why does a base class often need a virtual destructor?

**Short answer**

- If an object may be deleted through a base pointer, the base destructor must normally be public and virtual so destruction reaches the complete derived object.
- Deleting through a base with a non-virtual destructor is undefined behavior when the dynamic type differs.
- If polymorphic deletion is intentionally forbidden, use a protected non-virtual destructor and expose another lifetime policy; virtual methods alone do not automatically require public deletion.

**Details and nuances**

If an object may be destroyed through a base pointer, the base destructor must be virtual.

```cpp
#include <memory>

struct Base {
    virtual ~Base() = default;
    virtual void run() = 0;
};

struct Derived final : Base {
    ~Derived() override {
        // releases resources owned by Derived
    }

    void run() override {}
};

std::unique_ptr<Base> object = std::make_unique<Derived>();
// Destruction through Base* correctly invokes Derived::~Derived(), then Base::~Base().
```

Without a virtual destructor, behavior is undefined because the derived destructor may not run correctly.

If a type is not intended for polymorphic deletion, an alternative is to make its destructor protected and non-virtual so deletion through a base pointer is rejected rather than silently becoming undefined behavior.

[↑ Back to question index](#question-index)

---

## Question CPP-022

[↑ Back to question index](#question-index)

### Question CPP-022 — How do virtual functions usually work?

**Short answer**

- Typical ABIs store a hidden `vptr` in each polymorphic subobject; it points to a shared `vtable` containing function addresses and runtime metadata.
- A virtual call selects the final overrider from the object's dynamic type, while qualified calls and some devirtualized calls can be resolved directly.
- The standard specifies behavior, not a vtable layout, so object layout and binary compatibility remain ABI concerns.

**Details and nuances**

Typical implementations use:

- a hidden pointer in each polymorphic object: `vptr`
- a virtual function table: `vtable`

The object points to the table for its dynamic type.

Virtual calls use the table to select the final override at runtime.

This is an implementation strategy, not something mandated exactly by the C++ standard.

[↑ Back to question index](#question-index)

---

## Question CPP-023

[↑ Back to question index](#question-index)

### Question CPP-023 — Is there a vtable per object?

**Short answer**

- Usually each polymorphic object contains one or more vptrs, while vtables are shared read-only data for a class/ABI—not copied into every object.
- Multiple or virtual inheritance can require several polymorphic base subobjects, pointer adjustments and multiple tables or table sections.
- This is an implementation convention rather than a guarantee of the C++ language.

**Details and nuances**

Usually no.

Objects typically contain a `vptr`, while the `vtable` is shared between objects of the same dynamic type.

[↑ Back to question index](#question-index)

---

## Question CPP-024

[↑ Back to question index](#question-index)

### Question CPP-024 — What happens when calling a virtual function from a constructor or destructor?

**Short answer**

- Dispatch is limited to the class whose constructor/destructor is currently running; more-derived state is not yet constructed or has already been destroyed.
- A base constructor therefore calls the base override, not a future derived override, and relying on derived invariants is erroneous.
- Avoid virtual hooks from construction/destruction; use complete initialization, factories or an explicit post-construction step when dynamic behavior is required.

**Details and nuances**

Dynamic dispatch is limited to the class currently being constructed or destroyed.

During `Base` construction, a virtual call resolves to `Base`, not a future `Derived` override.

Similarly during destruction, derived parts may already be gone.

```cpp
#include <iostream>

struct Base {
    Base() { identify(); }
    virtual ~Base() { identify(); }

    virtual void identify() const {
        std::cout << "Base\n";
    }
};

struct Derived : Base {
    Derived() { identify(); }
    ~Derived() override { identify(); }

    void identify() const override {
        std::cout << "Derived\n";
    }
};

Derived object;
// Typical output:
// Base     - Base constructor
// Derived  - Derived constructor body
// Derived  - Derived destructor body
// Base     - Base destructor
```

Do not call a virtual hook from a base constructor when correctness depends on derived state: that state has not been constructed yet.

[↑ Back to question index](#question-index)

---

## Question CPP-025

[↑ Back to question index](#question-index)

### Question CPP-025 — What is object slicing?

**Short answer**

- Slicing happens when a derived object is copied into a base object by value; only the base subobject remains and derived data/behavior is lost.
- Passing or storing polymorphic objects by value can therefore silently destroy dynamic-type semantics.
- Use references, pointers or an explicit polymorphic clone/value wrapper when heterogeneous value ownership is needed.

**Details and nuances**

Object slicing occurs when a derived object is copied into a base object by value.

```cpp
Derived d;
Base b = d;
```

The derived-specific part is lost.

Use references or pointers for polymorphic behavior.

[↑ Back to question index](#question-index)

---

## Question CPP-026

[↑ Back to question index](#question-index)

### Question CPP-026 — Overloading vs overriding?

**Short answer**

- **Overloading** chooses among same-name functions with different parameter lists, primarily at compile time.
- **Overriding** supplies the final implementation of a virtual base function with a compatible signature and is selected by dynamic dispatch.
- Use `override`; remember that a derived same-name declaration can hide all base overloads unless they are reintroduced with `using Base::name`.

**Details and nuances**

**Overloading**: same function name with different parameter lists, usually resolved at compile time.

**Overriding**: derived class provides a new implementation of a virtual function from a base class.

Use `override` to let the compiler verify correctness.

[↑ Back to question index](#question-index)

---

## Question CPP-027

[↑ Back to question index](#question-index)

### Question CPP-027 — What is virtual inheritance?

**Short answer**

- Virtual inheritance makes multiple inheritance paths share one virtual base subobject, solving the duplicated-base part of the diamond problem.
- The most-derived constructor initializes the virtual base, regardless of which intermediate class first names it.
- It adds layout, pointer-adjustment and initialization complexity, so prefer composition unless the shared-base identity is genuinely part of the model.

**Details and nuances**

Virtual inheritance solves duplicate base subobjects in diamond inheritance.

Without virtual inheritance:

```text
    A
   / \
  B   C
   \ /
    D
```

`D` can contain two separate `A` subobjects.

With virtual inheritance, `B` and `C` share one `A` subobject.

[↑ Back to question index](#question-index)

---

## Question CPP-028

[↑ Back to question index](#question-index)

### Question CPP-028 — What determines the size of a C++ class?

**Short answer**

- Size includes non-static data members, base subobjects, padding/alignment and implementation metadata such as vptrs; static members are stored separately.
- Empty-base optimization and `[[no_unique_address]]` may remove otherwise-empty storage, while an ordinary empty object still needs distinct-address representation.
- Layout varies with member order, inheritance, ABI, packing and platform; verify with `sizeof`/`alignof` instead of assuming offsets.

**Details and nuances**

Factors include:

- data members
- alignment
- padding
- base classes
- potentially a `vptr`
- empty base optimization
- platform ABI

An empty class normally has size at least 1 so distinct objects can have distinct addresses.

[↑ Back to question index](#question-index)

---

# 3. RAII and Smart Pointers

## Question CPP-029

[↑ Back to question index](#question-index)

### Question CPP-029 — What is RAII?

**Short answer**

- RAII binds a resource lifetime to an object's lifetime: acquire during construction/factory creation and release in a non-throwing destructor.
- Automatic destruction on normal exits and stack unwinding gives deterministic cleanup for memory, locks, files, handles and COM references.
- Prefer single-purpose owning wrappers and move semantics; construction must either establish the invariant completely or fail without leaking.

**Details and nuances**

Resource Acquisition Is Initialization means resource ownership is tied to object lifetime.

Acquire in construction, release in destruction.

Examples:

- `std::unique_ptr`
- `std::vector`
- `std::lock_guard`
- file/socket wrapper classes

RAII makes cleanup deterministic and naturally integrates with exceptions.

```cpp
#include <cstdio>
#include <memory>
#include <stdexcept>

using File = std::unique_ptr<std::FILE, decltype(&std::fclose)>;

File openFile(const char* path) {
    File file(std::fopen(path, "rb"), &std::fclose);
    if (!file) {
        throw std::runtime_error("cannot open file");
    }
    return file;
}

void readHeader(const char* path) {
    File file = openFile(path);
    // Any return or exception below still calls fclose through File's deleter.
}
```

[↑ Back to question index](#question-index)

---

## Question CPP-030

[↑ Back to question index](#question-index)

### Question CPP-030 — How does `std::unique_ptr` work?

**Short answer**

- `unique_ptr` represents exclusive ownership, destroys through its deleter, cannot be copied and transfers ownership by move.
- Use `make_unique` by default; use a custom deleter for non-`delete` resources and the array specialization only when a container is unsuitable.
- `get()` borrows without transfer, `release()` gives up ownership without deleting, and `reset()` replaces/deletes the current resource.

**Details and nuances**

`unique_ptr` has exclusive ownership of an object.

It:

- cannot be copied
- can be moved
- destroys the owned object automatically
- may use a custom deleter

Ownership transfer is explicit through `std::move`.

[↑ Back to question index](#question-index)

---

## Question CPP-031

[↑ Back to question index](#question-index)

### Question CPP-031 — How should `unique_ptr` be passed to a function?

**Short answer**

- Pass `unique_ptr<T>` by value when the callee consumes ownership; the caller must use `std::move` for a named pointer.
- Pass `T&`/`const T&` for a required non-owning object and `T*` for an optional non-owning object.
- Pass `unique_ptr<T>&` only when the callee must reseat/reset the owner's pointer; `const unique_ptr<T>&` is rarely the clearest non-owning interface.

**Details and nuances**

Depends on semantics.

```cpp
void take(std::unique_ptr<T> p);
```

means ownership is transferred.

```cpp
void observe(const T& t);
```

means no ownership transfer.

```cpp
void maybeObserve(const T* p);
```

means non-owning and nullable.

Avoid passing `unique_ptr&` unless the function specifically needs to modify or replace the smart pointer itself.

[↑ Back to question index](#question-index)

---

## Question CPP-032

[↑ Back to question index](#question-index)

### Question CPP-032 — How does `shared_ptr` work?

**Short answer**

- Copies share a control block with strong/weak counts and deletion metadata; the object dies at strong count zero and the control block after all weak owners disappear.
- Construct one ownership group once—normally with `make_shared`—then copy/alias it; independently wrapping the same raw pointer creates double ownership.
- Reference counting manages lifetime, not object thread safety or cycle collection, and its atomic bookkeeping has performance and ownership-design costs.

**Details and nuances**

`shared_ptr` typically refers to a control block containing:

- strong reference count
- weak reference count
- deleter
- allocator-related metadata

The managed object is destroyed when the strong count reaches zero.

The control block survives until weak references are also gone.

[↑ Back to question index](#question-index)

---

## Question CPP-033

[↑ Back to question index](#question-index)

### Question CPP-033 — What is the difference between `make_shared<T>()` and `shared_ptr<T>(new T)`?

**Short answer**

- `make_shared` normally performs one allocation for object plus control block, improving locality, allocation cost and construction exception safety.
- A separate allocation permits a custom deleter and can release the object's storage even while weak references keep the control block alive.
- Prefer `make_shared` unless access control, custom allocation/deletion or weak-retention memory behavior requires separate construction.

**Details and nuances**

`make_shared` can often allocate:

- object
- control block

in one allocation.

That improves locality and reduces allocation overhead.

A caveat: when `weak_ptr`s remain, memory containing the combined allocation may remain allocated even after the object itself is destroyed.

[↑ Back to question index](#question-index)

---

## Question CPP-034

[↑ Back to question index](#question-index)

### Question CPP-034 — Why is this dangerous?

```cpp
T* p = new T;
std::shared_ptr<T> a(p);
std::shared_ptr<T> b(p);
```

**Short answer**

- `a` and `b` create different control blocks for the same raw pointer, so both believe they are the sole group responsible for deletion.
- When the groups expire, the object is deleted twice: undefined behavior, commonly heap corruption or a crash.
- Create the object with `make_shared` or construct one `shared_ptr` and copy it; inside an owned object use `enable_shared_from_this`, never `shared_ptr(this)`.

**Details and nuances**

`a` and `b` have separate control blocks.

Each believes it owns `p`.

Both eventually attempt to delete the same object, causing double deletion / undefined behavior.

[↑ Back to question index](#question-index)

---

## Question CPP-035

[↑ Back to question index](#question-index)

### Question CPP-035 — Why are cyclic `shared_ptr` references a problem?

**Short answer**

- Reference counting only sees counts, so a strongly connected cycle can keep every count above zero even when no external owner remains.
- Break non-owning/back-reference edges with `weak_ptr`, or redesign ownership as a directed tree with one clear owner.
- Decide which relationship controls lifetime; replacing arbitrary edges with `weak_ptr` without that model can create premature expiration instead.

**Details and nuances**

Example:

```text
A -> shared_ptr<B>
B -> shared_ptr<A>
```

Reference counts never reach zero.

The objects leak.

Usually one side should use `std::weak_ptr`.

```cpp
#include <memory>

struct Child;

struct Parent {
    std::shared_ptr<Child> child; // Parent owns Child
};

struct Child {
    std::weak_ptr<Parent> parent; // Child observes Parent without owning it
};

auto parent = std::make_shared<Parent>();
parent->child = std::make_shared<Child>();
parent->child->parent = parent;

if (auto owner = parent->child->parent.lock()) {
    // owner keeps Parent alive for this scope
}
```

[↑ Back to question index](#question-index)

---

## Question CPP-036

[↑ Back to question index](#question-index)

### Question CPP-036 — What does `weak_ptr::lock()` do?

**Short answer**

- `lock()` atomically attempts to acquire a strong reference from a weak reference.
- It returns a non-empty `shared_ptr` if the object is still alive, otherwise an empty one; keeping the result locally pins the object during use.
- Prefer one `if (auto p = weak.lock())` operation over `expired()` followed by construction, which has a time-of-check/time-of-use race.

**Details and nuances**

It attempts to create a `shared_ptr` from a `weak_ptr`.

If the object still exists, it returns a valid `shared_ptr`.

Otherwise, it returns an empty `shared_ptr`.

[↑ Back to question index](#question-index)

---

## Question CPP-037

[↑ Back to question index](#question-index)

### Question CPP-037 — What is `enable_shared_from_this`?

**Short answer**

- It lets an object already managed by `shared_ptr` obtain another owner that shares the existing control block through `shared_from_this()`.
- The object must first be placed in a compatible `shared_ptr`; calling too early typically throws `std::bad_weak_ptr`.
- It prevents the separate-control-block bug but does not justify unmanaged `this` lifetime or remove cycle risks.

**Details and nuances**

It allows an object already owned by `shared_ptr` to safely obtain another `shared_ptr` sharing the same control block.

Without it, doing:

```cpp
std::shared_ptr<T>(this)
```

would create another unrelated control block and can cause double deletion.

[↑ Back to question index](#question-index)

---

## Question CPP-038

[↑ Back to question index](#question-index)

### Question CPP-038 — Is `shared_ptr` thread-safe?

**Short answer**

- Different `shared_ptr` objects sharing one control block may be copied/reset/destroyed concurrently; the reference-count bookkeeping is synchronized.
- Concurrent non-const access to the same `shared_ptr` object still needs synchronization or `std::atomic<std::shared_ptr<T>>`.
- The pointed-to `T` receives no automatic protection: its mutable state needs its own thread-safety policy.

**Details and nuances**

The control block reference counting operations are thread-safe across different `shared_ptr` instances.

But the managed object itself is not automatically thread-safe.

Two threads can safely copy/destroy separate `shared_ptr`s referring to the same object, but concurrent unsynchronized mutation of the object still causes a data race.

[↑ Back to question index](#question-index)

---

# 4. STL and Data Structures

## Question CPP-039

[↑ Back to question index](#question-index)

### Question CPP-039 — How does `std::vector` work?

**Short answer**

- `vector` owns a contiguous dynamic array with a logical size and an allocated capacity, enabling random access and cache-friendly traversal.
- When capacity is exhausted it allocates a larger block, moves/copies elements and releases the old block; that invalidates all references/iterators.
- Appending is amortized O(1), indexed access O(1), and insertion/erasure away from the end O(n); choose it as the default sequence container unless constraints disagree.

**Details and nuances**

`vector` stores elements contiguously.

It tracks roughly:

- pointer to storage
- size
- capacity

When size exceeds capacity, it allocates a larger block and moves or copies existing elements.

[↑ Back to question index](#question-index)

---

## Question CPP-040

[↑ Back to question index](#question-index)

### Question CPP-040 — What is the difference between `size()` and `capacity()`?

**Short answer**

- `size()` counts constructed elements; `capacity()` is how many elements fit in current storage before reallocation is required.
- `capacity() >= size()`, but reserved unused slots are raw storage, not elements that may be indexed.
- `clear()` destroys elements without necessarily releasing capacity, and `shrink_to_fit()` is a non-binding request.

**Details and nuances**

`size()` = number of constructed elements.

`capacity()` = number of elements that fit before another reallocation is required.

[↑ Back to question index](#question-index)

---

## Question CPP-041

[↑ Back to question index](#question-index)

### Question CPP-041 — `reserve()` vs `resize()`?

**Short answer**

- `reserve(n)` ensures capacity of at least `n` and leaves the number of constructed elements unchanged.
- `resize(n)` changes logical size, constructing new elements when growing and destroying trailing elements when shrinking.
- Reserve when an approximate final count is known to avoid reallocations; repeatedly reserving tiny increments can defeat geometric growth.

**Details and nuances**

`reserve(n)` changes capacity but does not change logical size.

`resize(n)` changes the number of actual elements.

[↑ Back to question index](#question-index)

---

## Question CPP-042

[↑ Back to question index](#question-index)

### Question CPP-042 — When are vector iterators invalidated?

**Short answer**

- Any reallocation invalidates every iterator, pointer and reference into the vector.
- Without reallocation, insertion invalidates the insertion point and everything after it; erasure invalidates the erased range and everything after it, including the old `end()`.
- Operations at the end can still invalidate the past-the-end iterator, so consult the exact operation contract and never retain views across an uncontrolled mutation.

**Details and nuances**

A reallocation invalidates:

- iterators
- pointers
- references

to all elements.

Without reallocation, insertion/erase may invalidate only positions at or after the modified location depending on operation.

```cpp
#include <vector>

std::vector<int> values{1, 2, 3};
values.reserve(3);

int* first = &values[0];
values.push_back(4); // exceeds capacity, so reallocation is required

// *first is now undefined behavior: first points into the old allocation.
first = &values[0]; // obtain a fresh pointer after the mutation
```

When an API stores pointers, references or iterators into a vector, capacity-changing operations must be part of its lifetime contract.

[↑ Back to question index](#question-index)

---

## Question CPP-043

[↑ Back to question index](#question-index)

### Question CPP-043 — Why is `push_back` amortized O(1)?

**Short answer**

- Most appends construct one element in existing storage and cost O(1); occasional growth relocates O(n) elements.
- Geometric capacity growth means an element is relocated only a bounded logarithmic number of times across many pushes, making total work O(n).
- One individual push is still O(n), and relocation cost/exception behavior depends on the element's move/copy operations.

**Details and nuances**

Most pushes are constant time.

Occasionally `vector` reallocates and moves O(n) elements.

Because capacity typically grows geometrically, the total cost of many insertions is linear, so average amortized cost per insertion is O(1).

[↑ Back to question index](#question-index)

---

## Question CPP-044

[↑ Back to question index](#question-index)

### Question CPP-044 — Why is `vector` often faster than `list`?

**Short answer**

- Contiguous storage gives fewer allocations, compact metadata, cache locality, prefetching and efficient iteration.
- A list offers stable nodes and O(1) insertion/erase only after the position is already known; finding it remains O(n) and pointer chasing is expensive.
- Choose from measured access/mutation patterns and invalidation needs, not Big-O alone; `vector` is the usual default.

**Details and nuances**

Despite `list` having O(1) insertion at a known position, `vector` benefits from:

- contiguous memory
- better cache locality
- fewer allocations
- hardware prefetching
- lower pointer chasing overhead

Modern CPU behavior often dominates theoretical operation counts.

[↑ Back to question index](#question-index)

---

## Question CPP-045

[↑ Back to question index](#question-index)

### Question CPP-045 — `map` vs `unordered_map`?

**Short answer**

- `map` is ordered and normally provides O(log n) lookup/insertion with stable node references and range/order operations.
- `unordered_map` is hash-based, usually O(1) average but O(n) worst case, with no ordering and rehash behavior to manage.
- Decide using ordering/range needs, key/hash quality, memory/locality, adversarial input, iterator stability and measured workload.

**Details and nuances**

`std::map`:

- typically balanced tree
- ordered keys
- O(log n) operations

`std::unordered_map`:

- hash table
- average O(1)
- worst-case O(n)
- no ordering guarantee

Choice depends on ordering needs, key type, hash quality, memory usage and access pattern.

[↑ Back to question index](#question-index)

---

## Question CPP-046

[↑ Back to question index](#question-index)

### Question CPP-046 — Why can `unordered_map` become O(n)?

**Short answer**

- Lookup is proportional to the number of elements in the selected bucket; poor or adversarial hashes can put many keys together.
- High load factor and equality cost also increase work, while a deliberate collision attack can turn expected O(1) into O(n).
- Use a correct well-distributed hash, reserve appropriately, monitor load/collisions and choose a different structure for hostile inputs.

**Details and nuances**

Many keys may end up in the same bucket because of poor hashing or adversarial input.

Then lookup may degrade toward linear traversal.

[↑ Back to question index](#question-index)

---

## Question CPP-047

[↑ Back to question index](#question-index)

### Question CPP-047 — What is rehashing?

**Short answer**

- Rehashing replaces the bucket array and redistributes elements, usually after load-factor growth or an explicit `rehash`/`reserve`.
- It costs O(n) average and invalidates iterators, but standard unordered containers keep references/pointers to elements valid.
- Reserving for expected size can prevent latency spikes and repeated rehashes, at the cost of extra memory.

**Details and nuances**

When load factor grows beyond a threshold, an unordered container may create a larger bucket array and redistribute elements.

Rehashing can invalidate iterators and is O(n).

[↑ Back to question index](#question-index)

---

## Question CPP-048

[↑ Back to question index](#question-index)

### Question CPP-048 — What is strict weak ordering?

**Short answer**

- A comparator must be irreflexive and transitive, with a transitive equivalence relation defined by `!comp(a,b) && !comp(b,a)`.
- Equivalent keys need not be equal, but ordered containers treat them as the same ordering class; comparator state must remain consistent.
- Violating the requirement breaks algorithm/container preconditions and can cause missing entries, bad sorting or undefined behavior.

**Details and nuances**

Comparators used by ordered STL algorithms/containers must behave like a consistent ordering.

Properties include:

- irreflexive
- transitive
- consistent equivalence relation

Incorrect comparators can make algorithms behave unpredictably and may violate library preconditions.

[↑ Back to question index](#question-index)

---

## Question CPP-049

[↑ Back to question index](#question-index)

### Question CPP-049 — `push_back` vs `emplace_back`?

**Short answer**

- `push_back` inserts a `T`; `emplace_back(args...)` forwards arguments to construct `T` directly in container storage.
- Emplacement can avoid a temporary and enables explicit constructors, but if a `T` already exists, moving with `push_back` is usually equally clear and efficient.
- Neither avoids vector reallocation, and forwarded arguments can dangle or create surprising conversions, so prefer the clearer call unless measurement/design favors emplacement.

**Details and nuances**

`push_back` inserts an already-created value.

`emplace_back` constructs the object directly in container storage from constructor arguments.

`emplace_back` is not automatically faster.

If you already have a `T`, `push_back(std::move(t))` can be equally good.

[↑ Back to question index](#question-index)

---

## Question CPP-050

[↑ Back to question index](#question-index)

### Question CPP-050 — What is `std::string_view`?

**Short answer**

- `string_view` is a non-owning `(pointer, length)` view over contiguous characters, cheap to copy and useful for read-only parameters/parsing.
- The referenced storage must outlive the view and remain stable; views into temporary strings or reallocated/mutated owners can dangle.
- It is not necessarily null-terminated and can contain embedded nulls, so C APIs need an explicit length or an owning conversion.

**Details and nuances**

A non-owning view over character data.

It avoids copying, but lifetime must be handled carefully.

Dangerous:

```cpp
std::string_view f() {
    return std::string("abc");
}
```

The returned view dangles.

[↑ Back to question index](#question-index)

---

## Question CPP-051

[↑ Back to question index](#question-index)

### Question CPP-051 — What is `std::span`?

**Short answer**

- `span<T>` is a non-owning view over contiguous `T` elements, carrying a pointer and either dynamic or compile-time extent.
- It expresses an array-shaped API without copying and works with arrays, vectors and other contiguous storage.
- It does not extend lifetime or prevent owner reallocation; constness of the span object differs from `span<const T>`, which makes elements read-only.

**Details and nuances**

A non-owning view over contiguous elements.

It can represent arrays, vectors and other contiguous storage without copying.

Useful for API boundaries when ownership stays elsewhere.

[↑ Back to question index](#question-index)

---

# 5. Multithreading and Memory Model

## Question CPP-052

[↑ Back to question index](#question-index)

### Question CPP-052 — What is a data race?

**Short answer**

- A data race exists when threads perform conflicting accesses to the same memory location, at least one is a write, and the accesses are neither atomic nor ordered by happens-before.
- In C++, a data race causes undefined behavior—not merely an occasionally stale result.
- Protect the state with one synchronization policy: mutex ownership, atomics with correct ordering, immutability or thread confinement.

**Details and nuances**

A data race occurs when:

- two or more threads access the same memory location
- at least one access is a write
- accesses are not properly synchronized

In C++, a data race causes undefined behavior.

```cpp
#include <atomic>
#include <thread>

int unsafeCounter = 0;
std::atomic<int> safeCounter{0};

void unsafeIncrement() {
    ++unsafeCounter; // data race if several threads execute this
}

void safeIncrement() {
    safeCounter.fetch_add(1, std::memory_order_relaxed);
}
```

`relaxed` is sufficient only because this counter does not publish or protect any other state.

[↑ Back to question index](#question-index)

---

## Question CPP-053

[↑ Back to question index](#question-index)

### Question CPP-053 — Race condition vs data race?

**Short answer**

- A race condition is any correctness bug whose result depends on timing or ordering; it is a broad design concept.
- A data race is the precise C++ memory-model case of unordered conflicting non-atomic memory accesses and is undefined behavior.
- Atomics can remove a data race while leaving a logical check-then-act race, so both memory safety and higher-level invariants must be reviewed.

**Details and nuances**

A **race condition** is a broader logical problem where behavior depends on timing/order.

A **data race** has a precise C++ memory-model definition involving unsynchronized memory accesses.

You can have a race condition without a data race.

[↑ Back to question index](#question-index)

---

## Question CPP-054

[↑ Back to question index](#question-index)

### Question CPP-054 — What does a mutex provide?

**Short answer**

- A mutex provides exclusive ownership of a critical section and protects an invariant spanning one or several values.
- Unlocking a mutex synchronizes with a later successful lock of the same mutex, publishing prior writes to the acquiring thread.
- Use RAII lock objects, keep the protected-data contract explicit and avoid holding a mutex across slow I/O, callbacks or COM calls.

**Details and nuances**

A mutex provides mutual exclusion and synchronization.

It ensures only one protected critical section executes at a time and establishes memory-order relationships between unlock and subsequent lock operations.

[↑ Back to question index](#question-index)

---

## Question CPP-055

[↑ Back to question index](#question-index)

### Question CPP-055 — `lock_guard` vs `unique_lock`?

**Short answer**

- `lock_guard` is the smallest scope-bound wrapper: it locks on construction and unlocks on destruction.
- `unique_lock` tracks ownership and supports deferred/try/timed locking plus explicit unlock/relock and ownership transfer.
- `condition_variable` needs `unique_lock` because waiting must atomically release and later reacquire the mutex; otherwise prefer the simpler wrapper.

**Details and nuances**

`std::lock_guard`:

- simple RAII lock
- locks immediately
- cannot manually unlock/relock

`std::unique_lock`:

- more flexible
- can defer locking
- unlock/relock
- required by `condition_variable`

It has slightly more overhead because it stores additional state.

[↑ Back to question index](#question-index)

---

## Question CPP-056

[↑ Back to question index](#question-index)

### Question CPP-056 — What is `scoped_lock`?

**Short answer**

- `scoped_lock` is an RAII lock that can acquire one or several mutexes and release them at scope exit.
- For multiple mutexes it uses deadlock-avoidance equivalent to `std::lock`, which is safer than independently locking them in inconsistent order.
- All participants must follow the same locking protocol; do not pass the same non-recursive mutex twice or assume it solves unrelated lock cycles.

**Details and nuances**

`std::scoped_lock` can lock one or several mutexes using deadlock-avoidance mechanisms.

Example:

```cpp
std::scoped_lock lock(m1, m2);
```

Useful when multiple mutexes must be acquired together.

[↑ Back to question index](#question-index)

---

## Question CPP-057

[↑ Back to question index](#question-index)

### Question CPP-057 — What causes deadlock?

**Short answer**

- Deadlock requires mutual exclusion, hold-and-wait, no preemption and a circular wait; breaking any one prevents that cycle.
- Common causes are inconsistent lock ordering, waiting while holding a lock, callbacks/reentrancy and UI/COM cross-thread waits.
- Use a global lock order or joint acquisition, short critical sections, explicit ownership and dump-based wait-chain analysis; timeouts detect symptoms but do not prove correctness.

**Details and nuances**

Classic Coffman conditions:

1. mutual exclusion
2. hold and wait
3. no preemption
4. circular wait

Typical prevention techniques:

- consistent lock order
- `std::scoped_lock`
- smaller critical sections
- avoiding nested locking

[↑ Back to question index](#question-index)

---

## Question CPP-058

[↑ Back to question index](#question-index)

### Question CPP-058 — What is a condition variable?

**Short answer**

- A condition variable lets a thread sleep until shared state protected by a mutex may satisfy a predicate.
- `wait` atomically releases the mutex and sleeps, then reacquires it before rechecking the state; the notification itself stores no condition.
- Modify/check the predicate under the same mutex, notify after publishing the change, and define shutdown/cancellation so waiters cannot remain blocked forever.

**Details and nuances**

A condition variable allows threads to sleep until some condition becomes true.

Example:

```cpp
std::unique_lock lock(m);
cv.wait(lock, [&] { return !queue.empty(); });
```

The mutex protects the condition state.

The producer changes the predicate while holding the same mutex, then notifies:

```cpp
{
    std::lock_guard lock(m);
    queue.push(value);
}
cv.notify_one();
```

Updating the predicate and checking it under one mutex prevents lost logical state. The notification itself does not carry the state.

[↑ Back to question index](#question-index)

---

## Question CPP-059

[↑ Back to question index](#question-index)

### Question CPP-059 — Why must condition variables use a predicate?

**Short answer**

- Wakeups may be spurious, and a notification can occur before a waiter actually sleeps.
- The predicate is the durable truth; `wait(lock, pred)` loops under the mutex until it is true.
- Include terminal states such as shutdown/error in the predicate, otherwise consumers can hang when no more work will arrive.

**Details and nuances**

Because wakeups can be spurious and notifications may happen before a thread actually starts waiting.

The predicate checks the real condition under the mutex.

[↑ Back to question index](#question-index)

---

## Question CPP-060

[↑ Back to question index](#question-index)

### Question CPP-060 — What is `std::atomic`?

**Short answer**

- `std::atomic<T>` makes supported operations indivisible and places them in the C++ memory model with a chosen ordering.
- Atomic does not necessarily mean lock-free, and one atomic operation does not make a multi-variable invariant atomic.
- Use it for simple counters, flags and carefully designed synchronization; start with strong ordering, weaken only with a proven happens-before argument.

**Details and nuances**

`std::atomic<T>` provides operations that are indivisible with respect to other threads and participate in the C++ memory model.

It can be used for counters, flags and lock-free algorithms.

Atomicity alone does not solve every synchronization problem.

[↑ Back to question index](#question-index)

---

## Question CPP-061

[↑ Back to question index](#question-index)

### Question CPP-061 — Atomic vs mutex?

**Short answer**

- Atomics suit small independent state transitions and can avoid blocking; mutexes naturally protect compound state and complex invariants.
- Lock-free code is not automatically wait-free, faster or easier: retry contention, cache traffic, ABA and memory reclamation can dominate.
- Choose the clearest correct model, benchmark the real workload and hide synchronization behind an interface so it can evolve.

**Details and nuances**

Atomics are excellent for simple independent state transitions.

Mutexes are better when several values must be updated consistently or an invariant spans multiple operations.

A lock-free solution is not automatically faster or simpler.

[↑ Back to question index](#question-index)

---

## Question CPP-062

[↑ Back to question index](#question-index)

### Question CPP-062 — What is compare-and-swap?

**Short answer**

- Compare-and-swap atomically compares an object with `expected` and writes `desired` only when they match.
- On failure, C++ updates `expected` with the observed value, allowing a retry loop to recompute the next state.
- CAS enables lock-free algorithms but does not solve ABA, object lifetime/memory reclamation or the need for correct success/failure memory orders.

**Details and nuances**

CAS compares an atomic value with an expected value and replaces it only if they match.

C++ provides:

```cpp
compare_exchange_weak
compare_exchange_strong
```

It is a fundamental building block for many lock-free algorithms.

[↑ Back to question index](#question-index)

---

## Question CPP-063

[↑ Back to question index](#question-index)

### Question CPP-063 — `compare_exchange_weak` vs `strong`?

**Short answer**

- `weak` may fail spuriously even when the value matches, so it belongs in a loop that can cheaply retry.
- `strong` avoids spurious failure and is clearer for a one-shot attempt, but still fails normally on a value mismatch and updates `expected`.
- Both need valid memory orders; failure ordering cannot be `release` or `acq_rel` and must not be stronger than the success constraints allow.

**Details and nuances**

`weak` may fail spuriously even if the values match.

It is commonly used inside retry loops and can map more efficiently to some hardware.

`strong` does not allow this spurious-failure behavior.

[↑ Back to question index](#question-index)

---

## Question CPP-064

[↑ Back to question index](#question-index)

### Question CPP-064 — What is `memory_order_relaxed`?

**Short answer**

- Relaxed ordering guarantees atomicity and a modification order for that atomic object, but creates no synchronization for other memory.
- It fits independent statistics/IDs where only the atomic value matters, not publication of associated data.
- It does not repair a data race on non-atomic state; use acquire/release, a mutex or another publication protocol when visibility matters.

**Details and nuances**

It guarantees atomicity of the operation but provides no synchronization ordering with other memory operations.

Useful for things like independent statistics counters.

[↑ Back to question index](#question-index)

---

## Question CPP-065

[↑ Back to question index](#question-index)

### Question CPP-065 — What are acquire and release semantics?

**Short answer**

- A release operation publishes prior operations; an acquire operation that observes the matching release (or release sequence) imports those effects.
- Together they create a synchronizes-with edge and therefore happens-before, commonly through a ready flag or queue index.
- They order surrounding memory but are not a global total order; the protocol must guarantee which value is observed and keep the payload race-free.

**Details and nuances**

A release store and an acquire load that observes it can establish synchronization.

Conceptually:

Thread A:

```cpp
data = 42;
ready.store(true, std::memory_order_release);
```

Thread B:

```cpp
if (ready.load(std::memory_order_acquire)) {
    use(data);
}
```

If B observes the released value, writes before the release become visible to B after the acquire.

[↑ Back to question index](#question-index)

---

## Question CPP-066

[↑ Back to question index](#question-index)

### Question CPP-066 — What is happens-before?

**Short answer**

- Happens-before is the memory-model relation that makes earlier side effects visible and ordered before later conflicting accesses.
- It is built from sequenced-before within a thread, synchronizes-with edges such as mutex/atomic operations, and transitivity.
- Wall-clock order is insufficient: if conflicting accesses lack happens-before and are non-atomic, the program has a data race.

**Details and nuances**

A happens-before relationship guarantees ordering and visibility between operations in the C++ memory model.

If write A happens-before read B, B is guaranteed to observe effects consistent with that ordering.

[↑ Back to question index](#question-index)

---

## Question CPP-067

[↑ Back to question index](#question-index)

### Question CPP-067 — What is sequential consistency?

**Short answer**

- `memory_order_seq_cst` gives the strongest standard atomic ordering and places seq-cst operations in one total order consistent with each thread.
- It is easiest to reason about and is the default, though some hardware may need stronger barriers than for acquire/release.
- It does not make non-atomic races safe or turn several independent operations into one transaction.

**Details and nuances**

`memory_order_seq_cst` is the strongest standard memory ordering.

Operations behave as if they participate in one global order consistent with each thread's program order.

It is easiest to reason about but may impose more constraints on optimization/hardware.

[↑ Back to question index](#question-index)

---

## Question CPP-068

[↑ Back to question index](#question-index)

### Question CPP-068 — What is false sharing?

**Short answer**

- False sharing occurs when threads write independent variables that occupy the same cache line, forcing coherence ownership to bounce between cores.
- Correctness is unaffected, but throughput/latency may collapse under write contention despite no logical shared data.
- Confirm with profiling, then separate hot writers by layout/padding/alignment, shard state or aggregate per-thread; account for the target cache-line behavior.

**Details and nuances**

False sharing occurs when threads modify different variables that happen to reside on the same cache line.

The variables are logically independent, but cache coherence causes the cache line to bounce between cores.

This can severely reduce performance.

[↑ Back to question index](#question-index)

---

## Question CPP-069

[↑ Back to question index](#question-index)

### Question CPP-069 — How would you implement producer-consumer?

**Short answer**

- Put the queue and all predicates under one mutex; producers enqueue and notify, consumers wait for data-or-shutdown, then remove work under the lock.
- Release the lock before expensive processing so producers and other consumers can progress.
- In production define bounded capacity/backpressure, shutdown and cancellation, exception behavior, fairness and batching; use two predicates when producers must wait for space.

**Details and nuances**

Typical design:

```text
producer threads
      ↓
thread-safe queue
      ↓
consumer thread(s)
```

Use:

- mutex
- condition variable
- queue
- shutdown flag

For very high throughput, consider batching, bounded queues and backpressure.

[↑ Back to question index](#question-index)

---

## Question CPP-070

[↑ Back to question index](#question-index)

### Question CPP-070 — How would you make a thread-safe queue?

**Short answer**

- Encapsulate the container, mutex and condition variables so no caller can observe or modify the queue outside the synchronization contract.
- Make `push`/`pop` atomic with respect to the invariant, support move-only values and return results without exposing internal references.
- Specify bounded/unbounded capacity, blocking/try/timed operations, closure semantics, wake-all behavior and exception guarantees before optimizing.

**Details and nuances**

Basic version:

- mutex protects queue
- `condition_variable` waits for non-empty state
- `push()` locks, inserts, notifies
- `pop()` waits with predicate, removes under lock
- define shutdown semantics

For production code also consider:

- bounded capacity
- cancellation
- move-only values
- exception safety

A minimal queue with explicit shutdown can look like this:

```cpp
#include <condition_variable>
#include <mutex>
#include <optional>
#include <queue>
#include <utility>

template<class T>
class BlockingQueue {
public:
    bool push(T value) {
        {
            std::lock_guard lock(mutex_);
            if (stopped_) {
                return false;
            }
            queue_.push(std::move(value));
        }
        ready_.notify_one();
        return true;
    }

    std::optional<T> pop() {
        std::unique_lock lock(mutex_);
        ready_.wait(lock, [this] { return stopped_ || !queue_.empty(); });

        if (queue_.empty()) {
            return std::nullopt; // stopped and drained
        }

        T value = std::move(queue_.front());
        queue_.pop();
        return value;
    }

    void stop() {
        {
            std::lock_guard lock(mutex_);
            stopped_ = true;
        }
        ready_.notify_all();
    }

private:
    std::mutex mutex_;
    std::condition_variable ready_;
    std::queue<T> queue_;
    bool stopped_{};
};
```

For a bounded queue, producers need a second predicate for available capacity or a clearly documented drop/coalescing policy.

[↑ Back to question index](#question-index)

---

# 6. Performance

## Question CPP-071

[↑ Back to question index](#question-index)

### Question CPP-071 — How do you investigate a performance problem?

**Short answer**

- Reproduce a representative workload, state the metric/SLO and capture a baseline before changing code.
- Measure end-to-end and decompose wall time into CPU, allocation, locks, queueing, I/O, COM/IPC and tail latency with profilers/traces/counters.
- Form one hypothesis, change one bottleneck, rerun the same workload and add a regression benchmark; optimize the measured critical path, not intuition.

**Details and nuances**

Start with measurement, not assumptions.

Typical workflow:

```text
reproduce
↓
measure baseline
↓
profile CPU / memory / locks / I/O
↓
identify hot path
↓
optimize
↓
measure again
```

Always verify that the optimization changed the actual bottleneck.

[↑ Back to question index](#question-index)

---

## Question CPP-072

[↑ Back to question index](#question-index)

### Question CPP-072 — Latency vs throughput?

**Short answer**

- Latency is the time for one operation; throughput is completed work per unit time.
- Batching/concurrency often improve throughput while adding queueing or tail latency, and optimizing mean latency may not improve P99.
- Set separate targets under a stated load and watch saturation: once arrival rate approaches capacity, queues can make latency explode.

**Details and nuances**

**Latency** — time for one operation/request.

**Throughput** — number of operations completed per unit time.

Optimizations can improve one while hurting the other.

Batching often improves throughput but may increase latency.

[↑ Back to question index](#question-index)

---

## Question CPP-073

[↑ Back to question index](#question-index)

### Question CPP-073 — What are P50, P95 and P99?

**Short answer**

- Px is the latency value at or below which x% of measured operations complete: P50 is the median; P95/P99 describe the slow tail.
- Percentiles reveal user-visible outliers hidden by averages, but depend on window, population, sampling and aggregation method.
- Report them with load/error rate and enough sample count; do not average percentiles from separate hosts or intervals as if they were raw data.

**Details and nuances**

Percentiles describe latency distribution.

- P50 — median
- P95 — 95% of requests are faster than this
- P99 — 99% are faster than this

Tail latency is often more important than average latency in real-time systems.

[↑ Back to question index](#question-index)

---

## Question CPP-074

[↑ Back to question index](#question-index)

### Question CPP-074 — What is batching?

**Short answer**

- Batching amortizes fixed costs—locks, allocations, syscalls, serialization and COM crossings—over several items.
- It usually raises throughput but adds wait time, memory and larger failure/retry units.
- Bound a batch by count, bytes and maximum age, flush on shutdown, and tune adaptively against latency and backpressure metrics.

**Details and nuances**

Batching combines many small operations into fewer larger operations.

Benefits:

- fewer function calls
- fewer locks
- fewer COM crossings
- fewer syscalls
- better cache efficiency

Tradeoff: increased delay before the batch is processed.

[↑ Back to question index](#question-index)

---

## Question CPP-075

[↑ Back to question index](#question-index)

### Question CPP-075 — What is throttling?

**Short answer**

- Throttling caps execution or emission rate even if updates arrive faster, protecting downstream capacity and UI responsiveness.
- Define leading/trailing behavior and whether excess work is delayed, dropped or coalesced; those choices change correctness.
- It differs from debouncing, which waits for quiet, and from backpressure, which communicates or enforces downstream capacity.

**Details and nuances**

Throttling limits how frequently an action may execute.

Example:

```text
market updates: 20,000/sec
UI refresh: 10/sec
```

The system may process incoming data continuously but refresh the UI only every 100 ms.

[↑ Back to question index](#question-index)

---

## Question CPP-076

[↑ Back to question index](#question-index)

### Question CPP-076 — What is coalescing?

**Short answer**

- Coalescing merges pending updates for the same logical key, commonly retaining only the newest state before the consumer refreshes.
- It is safe only when intermediate transitions are disposable; transactions, counters and ordered events may need accumulation instead.
- Define key, merge rule, ordering and flush boundary, then combine with a bounded queue or scheduled snapshot.

**Details and nuances**

Coalescing combines multiple updates for the same logical item and keeps only the latest relevant state.

Example:

```text
AAPL: 100
AAPL: 101
AAPL: 102
```

Before the next UI refresh, only `102` may matter.

[↑ Back to question index](#question-index)

---

## Question CPP-077

[↑ Back to question index](#question-index)

### Question CPP-077 — What is backpressure?

**Short answer**

- Backpressure keeps a faster producer from creating unbounded work for a slower consumer.
- Policies include bounded blocking, rate feedback, rejection, sampling, dropping oldest/newest, coalescing and batch adaptation.
- Choose explicitly from data-loss tolerance and latency SLOs, expose queue depth/drop metrics and make overload behavior deterministic.

**Details and nuances**

Backpressure prevents a fast producer from overwhelming a slower consumer.

Techniques include:

- bounded queue
- dropping stale updates
- coalescing
- flow control
- slowing producer
- batch processing

[↑ Back to question index](#question-index)

---

## Question CPP-078

[↑ Back to question index](#question-index)

### Question CPP-078 — Why can allocation be expensive?

**Short answer**

- Heap allocation adds allocator metadata, synchronization, size-class work and possible OS interaction; the later memory access also costs cache/TLB misses.
- Many small objects increase fragmentation, pointer chasing and deallocation traffic, especially across threads.
- Allocation is not automatically the bottleneck: profile counts, sizes, lifetimes and contention before introducing complex pools.

**Details and nuances**

Heap allocation may involve:

- allocator bookkeeping
- synchronization
- fragmentation
- cache misses

In hot paths, excessive small allocations can significantly hurt performance.

[↑ Back to question index](#question-index)

---

## Question CPP-079

[↑ Back to question index](#question-index)

### Question CPP-079 — How do you reduce allocation overhead?

**Short answer**

- First remove unnecessary ownership/copies and reserve known container/string capacity; reuse buffers and batch objects with similar lifetimes.
- Then consider stack/inline storage, arenas/PMR resources, object pools or per-thread caches when profiling proves value.
- Bound retained memory, respect alignment/destruction and benchmark fragmentation and tail latency—pooling can worsen footprint and lifetime bugs.

**Details and nuances**

Possible techniques:

- reuse buffers
- reserve containers
- move instead of copy
- small-object optimization
- pooling where justified
- avoid temporary strings/objects
- process data in-place

Always profile first.

[↑ Back to question index](#question-index)

---

# 7. Windows and Debugging

## Question CPP-080

[↑ Back to question index](#question-index)

### Question CPP-080 — Static vs dynamic library?

**Short answer**

- A static library is copied into each final binary at link time; deployment is simpler, but updates require relinking and code may be duplicated.
- A dynamic library is loaded at runtime and can be shared/updated independently, but introduces discovery/versioning, ABI, bitness and loader-lifetime concerns.
- Across DLL boundaries use an intentionally stable API, consistent ownership/runtime rules and explicit version compatibility.

**Details and nuances**

Static library code is linked into the executable at build time.

Dynamic libraries are loaded as separate modules, usually DLLs on Windows.

Dynamic libraries enable:

- shared binaries
- plugin architecture
- independent deployment

but introduce ABI/versioning concerns.

[↑ Back to question index](#question-index)

---

## Question CPP-081

[↑ Back to question index](#question-index)

### Question CPP-081 — What are `LoadLibrary` and `GetProcAddress`?

**Short answer**

- `LoadLibrary` loads a DLL and returns an `HMODULE`; `GetProcAddress` resolves an exported symbol by exact name or ordinal.
- Cast to the exact calling convention/signature, check errors and keep the module loaded while any function pointer or object code can execute.
- Use safe explicit search paths to avoid DLL preloading attacks, version the plugin contract and balance the module handle with `FreeLibrary` when safe.

**Details and nuances**

`LoadLibrary` loads a DLL dynamically.

`GetProcAddress` obtains the address of an exported function by name or ordinal.

Useful for plugin systems and optional runtime dependencies.

[↑ Back to question index](#question-index)

---

## Question CPP-082

[↑ Back to question index](#question-index)

### Question CPP-082 — Why is `DllMain` dangerous for complex work?

**Short answer**

- `DllMain` runs under the loader lock with severe restrictions, so loading modules, initializing COM, waiting on threads or taking contested locks can deadlock.
- Notifications may occur during fragile process/thread startup or teardown when dependencies are not usable.
- Keep it minimal and non-blocking; perform fallible initialization through an explicit exported function or lazy one-time path outside the loader lock.

**Details and nuances**

`DllMain` executes under the Windows loader lock.

Doing complex operations there can cause deadlocks or loader-related issues.

Avoid:

- thread creation patterns that wait
- loading additional DLLs
- COM initialization
- complex synchronization

Keep `DllMain` minimal.

[↑ Back to question index](#question-index)

---

## Question CPP-083

[↑ Back to question index](#question-index)

### Question CPP-083 — How would you debug a host-process crash caused by a native plug-in?

**Short answer**

- Capture the exact host/plug-in build, exception code, full dump and matching symbols; inspect the faulting stack, all threads and loaded modules.
- Separate immediate fault from earlier corruption: check ownership, callbacks after shutdown, ABI/bitness, buffer bounds and races with verifier/sanitizers where applicable.
- Correlate structured logs and reproduce with add-in features toggled, then fix the root cause and preserve the dump/test as a regression artifact.

**Details and nuances**

Typical workflow:

1. reproduce if possible
2. obtain crash dump
3. load correct symbols/PDBs
4. inspect exception code
5. inspect crashing thread
6. inspect call stack
7. inspect object/lifetime state
8. check recent logs
9. verify heap corruption / race possibilities
10. reproduce under sanitizers or diagnostic builds where possible

[↑ Back to question index](#question-index)

---

## Question CPP-084

[↑ Back to question index](#question-index)

### Question CPP-084 — How would you investigate a deadlock?

**Short answer**

- Capture a hang dump without perturbing the process and inspect every thread's stack, owned locks and waits to build a wait-for cycle.
- In a UI/plug-in system, specifically inspect message pumping, UI↔worker synchronous waits, external calls and locks held across callbacks.
- Fix ownership/order or remove the synchronous cycle; then add lock/wait telemetry and a stress regression—timeouts only mask or recover from the symptom.

**Details and nuances**

Capture a hang dump and inspect all thread stacks.

Look for:

- threads waiting on locks
- circular lock dependencies
- COM apartment waits
- UI thread blocked while another thread waits for UI
- inconsistent lock ordering

[↑ Back to question index](#question-index)

---

## Question CPP-085

[↑ Back to question index](#question-index)

### Question CPP-085 — How would you debug a bug that appears only after several hours?

**Short answer**

- Add low-overhead timestamped correlation logs and trend memory, handles, COM references, queue depth, thread count, errors and latency from process start.
- Automate stress/replay and trigger dumps near thresholds/failure; compare multiple runs to distinguish leaks, exhaustion, races, backlog and time-based overflow/lifetime faults.
- Shorten feedback with accelerated clocks/data and diagnostic builds without changing the essential scheduling, then retain the smallest reproducer and regression monitor.

**Details and nuances**

Suspect issues such as:

- leaks
- handle exhaustion
- reference-count leaks
- races
- accumulated queue backlog
- integer overflow
- stale pointers
- timer/lifetime bugs

Use:

- long-running telemetry
- counters
- memory graphs
- handle counts
- periodic dumps
- structured logs
- stress testing

[↑ Back to question index](#question-index)

---

# 8. Rapid-Fire C++

## Question CPP-086

[↑ Back to question index](#question-index)

### Question CPP-086 — `new` vs `malloc`?

**Short answer**

- `new T(args...)` allocates suitably aligned storage and constructs a typed object; ordinary failure throws `std::bad_alloc`.
- `malloc(bytes)` returns raw untyped storage without constructors and reports failure with null; it is paired with `free`, while `new` is paired with matching `delete`.
- Never mix families. In C++ prefer RAII containers/smart pointers and use raw allocation only inside a well-defined owner/allocator.

**Details and nuances**

`new` allocates memory and constructs the object.

`malloc` only allocates raw bytes.

`new` returns typed pointer and throws `std::bad_alloc` by default.

`malloc` returns `void*` and returns null on failure.

[↑ Back to question index](#question-index)

---

## Question CPP-087

[↑ Back to question index](#question-index)

### Question CPP-087 — `delete` vs `delete[]`?

**Short answer**

- Use `delete` for a pointer from scalar `new` and `delete[]` for one from `new[]`; a mismatch is undefined behavior.
- Array deletion must destroy every constructed element and may rely on hidden allocation metadata, so the forms are not interchangeable.
- Prefer `vector`, `array`, `string` or smart pointers; if raw arrays are unavoidable, make allocation/deallocation ownership explicit.

**Details and nuances**

Use `delete` for objects allocated with scalar `new`.

Use `delete[]` for arrays allocated with `new[]`.

Mixing them is undefined behavior.

[↑ Back to question index](#question-index)

---

## Question CPP-088

[↑ Back to question index](#question-index)

### Question CPP-088 — What is placement new?

**Short answer**

- Placement new starts an object's lifetime by constructing it in caller-provided, suitably sized and aligned storage; it does not allocate that storage.
- The owner must call the destructor explicitly and release the underlying storage through its original mechanism, including all partial-construction failure paths.
- Prefer `std::construct_at`/`std::destroy_at` in modern generic code and account for pointer/lifetime rules when reusing storage for a new object.

**Details and nuances**

Placement new constructs an object in already allocated storage.

```cpp
new (buffer) T(args...);
```

You must later call the destructor manually.

[↑ Back to question index](#question-index)

---

## Question CPP-089

[↑ Back to question index](#question-index)

### Question CPP-089 — What is `std::terminate`?

**Short answer**

- `std::terminate` ends exception processing when the runtime cannot safely continue, then invokes the installed terminate handler and normally aborts.
- Typical triggers are an exception escaping `noexcept`, a second exception leaving a destructor during unwinding, or `throw;` with no active exception.
- It is not normal error handling: keep destructors non-throwing, catch at thread/ABI boundaries and log crash context without attempting unsafe recovery.

**Details and nuances**

The runtime calls `std::terminate` when exception handling cannot continue safely.

Examples:

- exception escapes a `noexcept` function
- destructor throws during stack unwinding and another exception is already active

[↑ Back to question index](#question-index)

---

## Question CPP-090

[↑ Back to question index](#question-index)

### Question CPP-090 — What is stack unwinding?

**Short answer**

- While an exception propagates toward a matching handler, automatic objects whose construction completed are destroyed in reverse order.
- This is why RAII releases locks, memory and handles on exceptional paths; raw resources not owned by destructors are easy to leak.
- Destructors must not let exceptions escape during unwinding, and paths ending in immediate termination may not unwind all scopes.

**Details and nuances**

When an exception propagates, local automatic objects in exited scopes are destroyed in reverse construction order.

RAII relies on this for cleanup.

[↑ Back to question index](#question-index)

---

## Question CPP-091

[↑ Back to question index](#question-index)

### Question CPP-091 — Strong exception guarantee?

**Short answer**

- The strong guarantee is transactional: if an operation throws, the externally visible state remains unchanged.
- Implement by preparing new state first with RAII, then committing through a non-throwing swap/move; copy-and-swap is one pattern.
- State the alternatives too: basic guarantee preserves invariants/no leaks, while no-throw guarantees completion; choose the strongest affordable contract and test failure points.

**Details and nuances**

If an operation fails, program state remains unchanged.

Other common guarantees:

- basic guarantee — invariants preserved, no leaks
- no-throw guarantee — operation cannot fail by throwing

[↑ Back to question index](#question-index)

---

## Question CPP-092

[↑ Back to question index](#question-index)

### Question CPP-092 — What is copy-and-swap?

**Short answer**

- Copy-and-swap constructs a temporary new value, swaps it with `*this`, and lets the temporary destroy the old state.
- If construction can throw but `swap` is non-throwing, assignment gets self-assignment safety and the strong exception guarantee.
- A by-value assignment can serve copy and move inputs, but may allocate unnecessarily or prevent storage reuse; explicit assignments can be faster/clearer for some types.

**Details and nuances**

A classic assignment technique:

1. copy into temporary
2. swap with current object
3. temporary destroys old state

It can provide strong exception safety, though move semantics and modern designs often reduce the need for manual use.

[↑ Back to question index](#question-index)

---

## Question CPP-093

[↑ Back to question index](#question-index)

### Question CPP-093 — What is ODR?

**Short answer**

- The One Definition Rule controls how many definitions an entity may have across a program: most non-inline odr-used entities need exactly one program definition.
- Templates, inline functions/variables and class definitions may appear in multiple translation units only under strict equivalent-definition rules.
- Violations can be compile/link errors or ill-formed-no-diagnostic-required behavior, causing subtle ABI/runtime faults; headers and build flags must stay consistent.

**Details and nuances**

ODR = One Definition Rule.

A program generally must have exactly one definition of entities requiring one, with special rules for inline functions/templates and equivalent definitions across translation units.

Violations can cause linker errors or undefined behavior.

[↑ Back to question index](#question-index)

---

## Question CPP-094

[↑ Back to question index](#question-index)

### Question CPP-094 — What does `inline` really mean?

**Short answer**

- `inline` is primarily an ODR/linkage facility allowing an identical function definition in multiple translation units; C++17 inline variables extend the idea to data.
- It does not require the optimizer to substitute the function body at call sites, and optimization may inline functions without the keyword.
- Put inline definitions in headers, keep every definition equivalent and avoid treating the keyword as a performance guarantee.

**Details and nuances**

It allows identical definitions in multiple translation units under ODR rules.

It does **not** force machine-code inlining.

The optimizer decides whether to inline function calls.

[↑ Back to question index](#question-index)

---

## Question CPP-095

[↑ Back to question index](#question-index)

### Question CPP-095 — What is ABI?

**Short answer**

- An ABI is the binary contract for calling conventions, type/object layout, alignment, mangled names, vtables, exceptions and runtime ownership.
- Compatible source code can still be binary-incompatible across compiler versions/options, standard libraries, architectures or debug/release runtimes.
- For durable DLL/plugin boundaries prefer a small versioned C ABI, opaque handles/PImpl and explicit allocation ownership; test binary compatibility in CI.

**Details and nuances**

Application Binary Interface defines binary-level compatibility rules such as:

- calling conventions
- object layout
- name mangling
- register usage
- exception ABI
- vtable layout

ABI stability matters heavily across DLL/plugin boundaries.

[↑ Back to question index](#question-index)

---

## Question CPP-096

[↑ Back to question index](#question-index)

### Question CPP-096 — What is name mangling?

**Short answer**

- C++ compilers encode namespaces, classes, overload parameter types and other signature information into linker symbol names.
- The encoding is ABI/compiler-specific, so exporting mangled C++ APIs tightly couples producer and consumer toolchains.
- `extern "C"` gives C linkage and predictable unmangled-style names for C-compatible functions, but it does not make C++ classes/exceptions/templates ABI-neutral.

**Details and nuances**

The compiler encodes C++ function/type information into linker symbol names.

This supports overloading.

`extern "C"` disables C++ name mangling for compatible declarations and uses C linkage rules.

[↑ Back to question index](#question-index)

---

# 9. Build Model, Language Details and Templates

## Question CPP-097

[↑ Back to question index](#question-index)

### Question CPP-097 — What is a translation unit, and how does C++ source become an executable?

**Short answer**

- Preprocessing expands includes/macros and produces a translation unit; each translation unit is compiled independently into an object file.
- The linker combines object files and libraries, resolves external symbols and relocations, and creates the executable or shared library.
- Syntax/type errors are normally compile-time errors; missing or duplicate externally linked definitions are commonly link-time errors.

**Details and nuances**

A translation unit is roughly one source file after preprocessing, including the declarations pulled in through headers. Separate translation explains why one `.cpp` file can compile while the complete program still fails to link.

Typical pipeline:

1. preprocessing: `#include`, macros and conditional compilation;
2. compilation: parse, type-check, optimize and generate object code;
3. assembly/object generation: machine code plus symbols and relocations;
4. linking: resolve references and combine code/data with libraries.

Headers normally publish declarations. Non-inline definitions live in one implementation file; template/inline definitions usually need to be reachable in every translation unit that instantiates/uses them. C++20 modules change distribution mechanics but not the need for declarations, definitions and linkage to be consistent.

Primary references: [C++ draft: program and linkage](https://eel.is/c++draft/basic.link) and [translation phases](https://eel.is/c++draft/lex.phases).

[↑ Back to question index](#question-index)

---

## Question CPP-098

[↑ Back to question index](#question-index)

### Question CPP-098 — What belongs in a header, and include guards vs `#pragma once`?

**Short answer**

- A header should be self-contained and expose the smallest necessary interface: declarations, type definitions, templates and genuinely inline definitions.
- Include guards are portable standard preprocessing; `#pragma once` is widely supported but implementation-specific. Use one consistent mechanism per header.
- Avoid non-inline external definitions, mutable globals, `using namespace`, hidden ordering dependencies and unnecessary heavy includes in public headers.

**Details and nuances**

Portable include guard:

```cpp
#ifndef PRODUCT_WIDGET_HPP
#define PRODUCT_WIDGET_HPP

class Widget {
public:
    void run();
};

#endif
```

`#pragma once` is shorter and prevents repeated processing in supported toolchains, but unusual filesystem aliases can complicate identity. Guards depend on a globally unique macro name but are specified using ordinary preprocessing behavior.

Templates, inline functions and inline variables may be defined in headers because their equivalent definitions are allowed in multiple translation units. An ordinary non-inline function or variable definition in a header can violate ODR. Include what the public declaration needs and confirm the header compiles when included first.

[↑ Back to question index](#question-index)

---

## Question CPP-099

[↑ Back to question index](#question-index)

### Question CPP-099 — Forward declaration vs `#include`?

**Short answer**

- Forward-declare when only a name and incomplete type are sufficient, typically for pointers/references or function declarations.
- Include the defining header when layout or members are needed: value members, base classes, `sizeof`, member access, most inline implementation and many template uses.
- Forward declarations reduce coupling and rebuilds, but should not replace a required dependency or redeclare third-party/standard-library internals manually.

**Details and nuances**

```cpp
// Controller.hpp
class Engine;

class Controller {
public:
    Controller();
    ~Controller(); // defined in .cpp where Engine is complete

private:
    std::unique_ptr<Engine> engine_;
};
```

The corresponding `.cpp` includes both headers and defines the destructor after `Engine` is complete. This PImpl-style arrangement prevents implementation changes from forcing every consumer to rebuild.

Prefer “include what you use”: if a public declaration requires a complete type, include its owning header directly rather than relying on a transitive include. A forward declaration must exactly match the real entity, so do not guess template declarations from another library.

[↑ Back to question index](#question-index)

---

## Question CPP-100

[↑ Back to question index](#question-index)

### Question CPP-100 — How do C/C++ macros work, and what are the common traps?

**Short answer**

- A macro is token substitution performed before C++ parsing; function-like macros can stringify with `#`, concatenate tokens with `##`, and control compilation with `#if`/`#ifdef`.
- Macros have no types, namespaces or ordinary evaluation rules, so precedence, repeated side effects, name collisions and poor diagnostics are common failures.
- Prefer `constexpr`, inline functions, templates, enums and build-system configuration; keep unavoidable macros narrow, parenthesized and uniquely named.

**Details and nuances**

```cpp
#define BAD_SQUARE(x) x * x
#define ALSO_BAD_SQUARE(x) ((x) * (x))

int i = 2;
auto a = BAD_SQUARE(1 + 2); // expands to 1 + 2 * 1 + 2
auto b = ALSO_BAD_SQUARE(i++); // increments i twice: bad API

constexpr auto square(auto value) {
    return value * value; // typed; argument evaluated once
}
```

`#x` converts the argument tokens to a string literal; `a ## b` forms a new preprocessing token. Both are occasionally useful for generated registration/tests but make code harder for debuggers and refactoring tools.

Conditional compilation should select platform/build facilities, not create many untested behavioral variants. Avoid putting commas, control flow or multiple statements in expression-like macros unless a carefully reviewed legacy interface demands it.

[↑ Back to question index](#question-index)

---

## Question CPP-101

[↑ Back to question index](#question-index)

### Question CPP-101 — Declaration vs definition?

**Short answer**

- A declaration introduces an entity and its type/name; a definition supplies the complete entity, function body or storage.
- An entity can usually be declared repeatedly consistently, but ODR controls how many definitions are permitted across the program.
- `extern int count;` and `void run();` are declarations; `int count = 0;` and `void run() {}` are definitions.

**Details and nuances**

```cpp
class Service;          // declaration of an incomplete type
int calculate(int);    // function declaration
extern int requests;   // variable declaration, no definition here

class Service {};      // class definition
int calculate(int x) { return x * 2; }
int requests = 0;      // storage-defining declaration
```

A class definition defines the type but does not create an object. A pure virtual function can still have a definition. `= delete` is a definition, and an inline definition may legally appear equivalently in multiple translation units.

The useful diagnostic distinction is: compile errors mean the current translation unit cannot understand/use a declaration; unresolved or multiply defined symbols mean translation units disagree at link time.

[↑ Back to question index](#question-index)

---

## Question CPP-102

[↑ Back to question index](#question-index)

### Question CPP-102 — What are internal, external and no linkage?

**Short answer**

- External linkage lets the same name denote one entity across translation units; internal linkage limits the named entity to one translation unit.
- Namespace-scope `static` and unnamed namespaces commonly provide internal linkage; local variables and function parameters normally have no linkage.
- Linkage concerns name identity, not lifetime: storage duration and visibility/scope are separate properties.

**Details and nuances**

```cpp
extern int shared_count; // external linkage declaration

namespace {
int helper_state;        // internal linkage
void helper() {}
}

void f(int parameter) {  // parameter has no linkage
    int local = 0;       // no linkage, automatic storage duration
}
```

Use an unnamed namespace for implementation-only names in a `.cpp`; reserve `static` at namespace scope mainly for legacy/C interoperation. Header-defined namespace-scope constants, inline variables and templates have additional linkage/ODR rules, so make intent explicit instead of relying on defaults.

Do not confuse “visible here” with “same entity everywhere.” Scope determines where a name can be used; linkage determines whether declarations in different scopes/translation units refer to the same entity.

[↑ Back to question index](#question-index)

---

## Question CPP-103

[↑ Back to question index](#question-index)

### Question CPP-103 — What should you know about fundamental types, `nullptr` and `std::byte`?

**Short answer**

- Fundamental type sizes/ranges follow minimum and implementation-defined rules; use fixed-width integers for exact external formats only when that width exists, and `size_t` for object sizes/index domains.
- Signed overflow is undefined; unsigned arithmetic is modulo 2^N. Plain `char` signedness and object endianness are platform properties.
- `nullptr` has type `std::nullptr_t` and selects pointer overloads safely; `std::byte` represents raw storage without pretending bytes are arithmetic characters.

**Details and nuances**

Do not assume `int` is 32-bit or `long` has the same width on Windows and Unix-like 64-bit ABIs. Use `std::int32_t` for a protocol field that is exactly 32 bits, not automatically for every counter.

`nullptr` converts to pointer and pointer-to-member types but not to an arbitrary integer, avoiding the overload ambiguity of `0`/`NULL`. `std::byte` supports bitwise operations and explicit integer conversion, which makes raw object-representation code more intentional than `char` arithmetic.

For serialization, specify width, signed representation/range, byte order and validation at the boundary. Internal arithmetic still needs overflow and narrowing checks even when the chosen type has an exact width.

[↑ Back to question index](#question-index)

---

## Question CPP-104

[↑ Back to question index](#question-index)

### Question CPP-104 — What are integer promotions and usual arithmetic conversions?

**Short answer**

- Types narrower than `int` are usually promoted to `int`/`unsigned int` before arithmetic; mixed operands are then converted to a common type.
- Signed/unsigned mixing can convert a negative value to a large unsigned value, producing surprising comparisons and arithmetic.
- Keep domains consistent, validate before narrowing, enable conversion warnings and use C++20 comparison helpers such as `std::cmp_less` when appropriate.

**Details and nuances**

```cpp
int value = -1;
std::size_t size = 1;

bool surprising = value < size;       // often false: value converts to size_t
bool intended = std::cmp_less(value, size); // true
```

`char`, `signed char`, `unsigned char`, `short` and `bool` generally undergo integral promotion. The usual arithmetic conversions then balance rank and signedness; the exact rules matter in comparisons, bit operations and variadic calls.

Avoid “fixing” warnings with an unchecked cast. First establish range/domain, then convert at a narrow boundary. In loops, either use the container's size type consistently or use a deliberate signed size abstraction.

[↑ Back to question index](#question-index)

---

## Question CPP-105

[↑ Back to question index](#question-index)

### Question CPP-105 — `enum` vs `enum class`?

**Short answer**

- An unscoped `enum` injects enumerator names into its surrounding scope and can convert implicitly to an integer.
- `enum class` is scoped and strongly typed, preventing accidental mixing with integers or other enums; it is the modern default.
- Specify an underlying type for ABI/protocol/storage contracts, and define explicit bitwise operators if the enum represents flags.

**Details and nuances**

```cpp
enum class State : std::uint8_t { idle, running, failed };

State state = State::running;
// int value = state; // error; use an intentional conversion
```

The underlying representation does not validate incoming integers. When decoding external data, range-check before converting and include an `unknown` handling policy if future versions may add values.

An enum used as flags is a different abstraction from a single-choice state. Overload `|`, `&` and helpers deliberately rather than relying on implicit integer conversions.

[↑ Back to question index](#question-index)

---

## Question CPP-106

[↑ Back to question index](#question-index)

### Question CPP-106 — How does `const` work with pointers and member functions?

**Short answer**

- `const T*` points to read-only `T`; `T* const` is a non-reseatable pointer; `const T* const` has both restrictions.
- A `const` member function receives `this` as a pointer to const and cannot modify ordinary members, but this is logical—not automatic deep—constness.
- Removing const and writing is valid only when the original object was non-const; modifying a truly const object through `const_cast` is undefined behavior.

**Details and nuances**

```cpp
const int* pointer_to_const = nullptr;
int* const const_pointer = get_storage();
const int* const both = get_read_only_storage();
```

Top-level const qualifies the object itself; low-level const qualifies what a pointer/reference reaches. Value parameters lose top-level const in function type, while pointee const participates in overload compatibility.

`mutable` can support caches or synchronization inside a logically const operation, but it does not remove data-race requirements. Returning a mutable pointer/reference from a const object can break the abstraction even if the compiler permits an indirect path.

[↑ Back to question index](#question-index)

---

## Question CPP-107

[↑ Back to question index](#question-index)

### Question CPP-107 — When should each C++ cast be used?

**Short answer**

- `static_cast` handles checked-at-compile-time conversions; `dynamic_cast` performs runtime-checked navigation in polymorphic hierarchies.
- `const_cast` changes cv-qualification; `reinterpret_cast` performs low-level representation/address reinterpretation with very limited portability guarantees.
- Avoid C-style casts because they can silently try several cast categories; every explicit cast should document and localize a proven boundary assumption.

**Details and nuances**

`dynamic_cast<Derived*>(base)` returns null on failure; the reference form throws `std::bad_cast`. It requires a polymorphic source type and is appropriate when runtime type navigation is genuinely part of the design.

`static_cast` does not make a downcast safe: if the object's dynamic type is wrong, use is undefined. `reinterpret_cast` does not by itself start object lifetime, fix alignment or permit aliasing; byte inspection should normally use `std::byte`/`memcpy`/`bit_cast` as applicable.

Use `const_cast` mainly to call a legacy API that incorrectly omitted const while guaranteeing it will not write. If the underlying object is actually const and the callee writes, the program has undefined behavior.

[↑ Back to question index](#question-index)

---

## Question CPP-108

[↑ Back to question index](#question-index)

### Question CPP-108 — What initialization forms exist, and why use braces carefully?

**Short answer**

- C++ has default, value, direct, copy, list and aggregate initialization; they differ in overload resolution, zero-initialization and explicit-constructor handling.
- Braced list-initialization rejects narrowing and avoids the most vexing parse, but gives `initializer_list` constructors special priority.
- Initialize members in declaration order, not initializer-list order, and choose a form that makes conversion/constructor intent unambiguous.

**Details and nuances**

```cpp
Widget a;              // default-initialization
Widget b{};            // value/list-initialization
Widget c(arg);         // direct-initialization
Widget d = arg;        // copy-initialization
std::vector<int> x(3); // three zeroes
std::vector<int> y{3}; // one element whose value is 3
```

`T object();` declares a function—the classic most vexing parse—while `T object{};` creates an object. Braces are therefore a useful default, but the vector example shows why “always braces” is too simple.

List-initialization's narrowing check rejects silent lossy constants such as `int{3.5}`. For aggregate initialization, adding constructors/private members or changing member order can change source compatibility, so public aggregate layout is an API decision.

[↑ Back to question index](#question-index)

---

## Question CPP-109

[↑ Back to question index](#question-index)

### Question CPP-109 — `constexpr` vs `consteval` vs `constinit`?

**Short answer**

- `constexpr` means a variable is a constant expression or a function can participate in constant evaluation when called with suitable inputs.
- `consteval` makes every potentially evaluated call compile-time; it is for immediate functions that must never run at runtime.
- `constinit` requires static/thread-storage initialization to be static, preventing dynamic-initialization-order problems, but does not make the object immutable.

**Details and nuances**

```cpp
constexpr int square(int x) { return x * x; }
consteval int checked_id(int x) { return x > 0 ? x : throw "invalid"; }

constinit int process_counter = 0; // initialized statically; still mutable
constexpr int answer = square(42);
```

A `constexpr` function may execute at runtime. A `constexpr` object is const and must have a constant initializer. `constinit` applies only to static/thread storage, cannot combine with `constexpr`, and controls initialization timing rather than later writes.

Prefer compile-time evaluation when it improves correctness or startup without making APIs obscure or compile times excessive. Feature/toolchain constraints matter at shared-library boundaries.

[↑ Back to question index](#question-index)

---

## Question CPP-110

[↑ Back to question index](#question-index)

### Question CPP-110 — How do lambda captures work, and what can dangle?

**Short answer**

- A lambda creates a closure object; value capture stores members, reference capture stores access to existing objects, and init-capture creates explicitly initialized members.
- `[this]` captures the pointer, not the object; `[*this]` captures an object copy. Reference/`this` captures can dangle when asynchronous callbacks outlive the source scope/object.
- `mutable` permits modification of value-captured members; generic lambdas use `auto` parameters and behave like templated call operators.

**Details and nuances**

```cpp
auto make_task(std::string text) {
    return [value = std::move(text)]() mutable {
        value += " processed";
        return value;
    };
}
```

Default captures (`[=]`, `[&]`) are concise but hide lifetime and dependency choices. For queued work, prefer explicit owning captures; a reference capture is safe only when the execution/lifetime relationship is guaranteed.

Capturing a `shared_ptr` extends lifetime and can form callback cycles; capturing `weak_ptr` plus `lock()` often models optional continued lifetime. Capturing a move-only value makes the closure move-only in relevant language versions/uses.

[↑ Back to question index](#question-index)

---

## Question CPP-111

[↑ Back to question index](#question-index)

### Question CPP-111 — How do template instantiation and specialization work?

**Short answer**

- A template is a recipe; implicit instantiation generates needed specializations when a use requires their complete definition.
- Explicit instantiation can centralize generated code; explicit specialization supplies a distinct implementation for selected arguments.
- Class/variable templates support partial specialization; function templates do not—use overloading, constraints or a specialized helper instead.

**Details and nuances**

Template definitions normally live in headers because the compiler must see them at the point of instantiation. An `extern template` declaration can suppress repeated implicit instantiation while one `.cpp` provides an explicit instantiation definition.

```cpp
template<class T>
struct Serializer;

template<>
struct Serializer<bool> { /* full specialization */ };

template<class T>
struct Serializer<T*> { /* partial specialization */ };
```

Specializations must be declared before uses that would instantiate the primary template, and ODR still applies. Prefer a constrained primary design when a family of types shares behavior; specialization is best for a genuine type-dependent implementation boundary.

[↑ Back to question index](#question-index)

---

## Question CPP-112

[↑ Back to question index](#question-index)

### Question CPP-112 — What are variadic templates and fold expressions?

**Short answer**

- A parameter pack represents zero or more template/function arguments; pack expansion applies a pattern to every element.
- C++17 fold expressions reduce a pack with an operator using unary/binary left/right forms, with associativity and empty-pack rules that matter.
- Constrain accepted arguments and preserve value categories only when forwarding is part of the contract; packs can otherwise produce difficult diagnostics.

**Details and nuances**

```cpp
template<class... Values>
auto sum(Values... values) {
    return (values + ... + 0); // binary fold; works for an empty pack
}

template<class... Values>
void log_all(Values&&... values) {
    (log_one(std::forward<Values>(values)), ...);
}
```

`(pack op ...)` and `(... op pack)` associate differently for non-associative operators. A unary fold over an empty pack is valid only for certain operators; supplying an identity value makes intent explicit.

Use `sizeof...(Values)` for pack length. Avoid clever recursive metaprogramming where a fold, concept and ordinary algorithm express the behavior directly.

[↑ Back to question index](#question-index)

---

## Question CPP-113

[↑ Back to question index](#question-index)

### Question CPP-113 — SFINAE vs concepts and `requires`?

**Short answer**

- SFINAE removes a template candidate when substitution in its immediate context fails; `enable_if`/detection idioms encode availability indirectly.
- C++20 concepts and `requires` express named or local constraints directly, improve diagnostics and participate in constrained overload ordering.
- Constraints check compile-time syntactic/semantic requirements, not runtime values or every behavioral promise of an interface.

**Details and nuances**

```cpp
template<class T>
concept Addable = requires(T a, T b) {
    { a + b } -> std::same_as<T>;
};

template<Addable T>
T combine(const T& a, const T& b) {
    return a + b;
}
```

SFINAE remains important for C++17 code and compatibility libraries, but concepts should usually be the public C++20 vocabulary. Constraints can distinguish overloads by subsumption, whereas hand-written `enable_if` conditions are harder for both compilers and readers to compare.

A `requires` expression asks whether expressions/types are valid; it does not execute them. Document semantic requirements such as associativity, ownership or thread safety even when they cannot be mechanically checked.

[↑ Back to question index](#question-index)

---

## Question CPP-114

[↑ Back to question index](#question-index)

### Question CPP-114 — Which C++20/C++23 features are most relevant in production?

**Short answer**

- High-value C++20 facilities include concepts, ranges, `span`, `jthread`/`stop_token`, atomic wait/notify, synchronization primitives, `<=>`, `format`, `consteval` and modules where toolchains support them.
- C++23 adds facilities such as `expected`, `print`, `mdspan`, `stacktrace`, more ranges/containers and language improvements including explicit object parameters.
- Adoption is a toolchain/deployment decision: verify compiler, standard-library, ABI and feature-test macro support before designing a public interface around a feature.

**Details and nuances**

The best interview answer groups features by problem rather than reciting a release list:

- safer interfaces: `span`, concepts, `expected`;
- concurrency/lifetime: `jthread`, `stop_token`, semaphores/latches/barriers, atomic wait;
- clearer data processing: ranges, views and projections;
- diagnostics/tooling: `source_location`, `stacktrace`;
- build boundaries: modules, where the build ecosystem is mature enough.

Language publication does not imply equal implementation quality across MSVC, Clang, GCC and their libraries. Use standard feature-test macros and CI on every supported toolchain; provide a compatibility layer when an externally consumed ABI must remain older.

[↑ Back to question index](#question-index)

---

# 10. STL and Concurrency Extensions

## Question CPP-115

[↑ Back to question index](#question-index)

### Question CPP-115 — How do you choose between `vector`, `deque`, `list` and `forward_list`?

**Short answer**

- Use `vector` by default: contiguous storage, random access and excellent locality usually outweigh relocation cost.
- Use `deque` for efficient growth/removal at both ends without requiring one contiguous block; iterator/reference rules are more complex.
- Use `list`/`forward_list` only when stable nodes, splice operations or very specific insertion patterns beat their allocation and cache costs.

**Details and nuances**

| Container | Strength | Main cost |
|---|---|---|
| `vector` | contiguous, random access, compact | reallocation/insertion moves elements |
| `deque` | O(1) ends, random access | segmented storage, larger/complex invalidation |
| `list` | stable nodes, bidirectional splice | allocation and pointer chasing per element |
| `forward_list` | smallest singly linked nodes | forward-only, no constant-time size |

Big-O is not enough: inserting into a list is O(1) only after the position is known, and finding it is usually O(n). A vector's linear movement can still be faster due to cache locality.

Choose based on access order, ownership, invalidation and measured element count. If stable identity is the only requirement, a vector of owning pointers/handles may outperform a node container.

[↑ Back to question index](#question-index)

---

## Question CPP-116

[↑ Back to question index](#question-index)

### Question CPP-116 — How do `map`/`set` differ from their `multi` variants?

**Short answer**

- `set` stores unique keys; `map` stores unique key-value pairs. `multiset`/`multimap` permit multiple comparator-equivalent keys.
- All are ordered associative containers, typically balanced trees with O(log n) lookup/insertion/erase and stable references to non-erased elements.
- For multi containers, use `equal_range` to process the full equivalent-key group; `find` returns only one matching element.

**Details and nuances**

The comparator defines equivalence as neither key being less than the other. It does not have to be `operator==`, but must provide strict weak ordering and remain compatible with stored data.

Keys are effectively immutable because changing one in place would break tree ordering. C++17 node handles allow extract-modify-reinsert while preserving allocation where supported.

Choose a multi container when duplicate-key identity/order is intrinsic. Otherwise a map from key to a collection can make group ownership and update behavior clearer.

[↑ Back to question index](#question-index)

---

## Question CPP-117

[↑ Back to question index](#question-index)

### Question CPP-117 — What contract must a hash function and equality predicate satisfy?

**Short answer**

- If `key_equal(a, b)` is true, `hash(a)` and `hash(b)` must be equal; unequal keys may still collide.
- Both operations must stay consistent for as long as the key is stored, so mutating fields involved in hashing/equality breaks the container invariant.
- Good distribution, inexpensive computation and a suitable load factor preserve expected O(1); hostile input may require a hardened hash or ordered container.

**Details and nuances**

Custom equality and hashing must describe the same logical identity—for example, a case-insensitive comparison needs a matching case-insensitive hash. A stronger hash cannot compensate for inconsistent equality.

Use `reserve` when expected element count is known and inspect `max_load_factor` only with measurements. Rehashing changes buckets and invalidates iterators but not references/pointers to elements in standard unordered containers.

Hash values are normally process/implementation details, not stable serialized IDs. Do not persist them unless the algorithm and version are explicitly part of the format.

[↑ Back to question index](#question-index)

---

## Question CPP-118

[↑ Back to question index](#question-index)

### Question CPP-118 — What are iterator categories and why do they matter?

**Short answer**

- Categories describe supported traversal: input/output, forward, bidirectional, random-access and contiguous.
- Algorithms select behavior and complexity from these capabilities: `sort` needs random access, while `find` only needs input traversal.
- Category does not guarantee validity; every container operation has separate iterator/reference invalidation rules.

**Details and nuances**

Forward iterators support repeated multi-pass traversal; bidirectional iterators add decrement; random-access adds constant-time jumps/distance and ordering; contiguous iterators additionally model adjacent memory.

Output iterators represent destinations rather than readable positions. Adapters such as `std::back_inserter` turn container insertion into an output iterator, avoiding manual resizing:

```cpp
std::transform(input.begin(), input.end(),
               std::back_inserter(output), convert);
```

C++20 iterator concepts separate readable/writable/incrementable properties more precisely. Still never compare or use iterators from unrelated containers, and re-evaluate saved iterators after mutation.

[↑ Back to question index](#question-index)

---

## Question CPP-119

[↑ Back to question index](#question-index)

### Question CPP-119 — Why prefer STL algorithms and ranges to handwritten loops?

**Short answer**

- Algorithms state intent (`find`, `transform`, `sort`) and carry known correctness/complexity contracts, reducing indexing and boundary mistakes.
- Ranges compose iterator/sentinel pairs, views and projections; views are often lazy and non-owning.
- Use an explicit loop when control flow/state is clearer that way—readability and correctness matter more than eliminating every loop.

**Details and nuances**

Algorithms separate “what operation” from container mechanics and can support different iterator types or execution strategies. Projections avoid temporary transformed collections in operations such as sorting by a member.

```cpp
std::ranges::sort(records, {}, &Record::timestamp);
auto active = records | std::views::filter(&Record::is_active);
```

A view can dangle when its underlying range dies or mutates, and lazy work can repeat on every traversal. Parallel execution policies impose callable/data-race requirements and can change exception behavior, so they are not a free speed switch.

[↑ Back to question index](#question-index)

---

## Question CPP-120

[↑ Back to question index](#question-index)

### Question CPP-120 — What are the erase-remove idiom and `std::erase_if`?

**Short answer**

- `remove`/`remove_if` reorder retained elements toward the front and return the new logical end; they cannot shrink a container by themselves.
- The classic idiom erases the tail: `v.erase(std::remove_if(v.begin(), v.end(), pred), v.end())`.
- C++20 `std::erase`/`std::erase_if` package the correct container operation and should normally be preferred.

**Details and nuances**

The elements between the returned iterator and old end remain valid but have unspecified retained/moved-from values until erased. On `vector`, the erase step destroys them and shifts as needed, invalidating iterators at/after the erase point.

Associative containers cannot reorder const keys with the remove algorithm; `std::erase_if(container, pred)` iterates and erases nodes appropriately.

If removal is frequent and ordering is irrelevant, partition-and-erase or a different data structure may perform better. Measure, especially when elements are expensive to move.

[↑ Back to question index](#question-index)

---

## Question CPP-121

[↑ Back to question index](#question-index)

### Question CPP-121 — `lower_bound` vs `upper_bound` vs `equal_range`?

**Short answer**

- On a range partitioned by the same ordering, `lower_bound` finds the first element not less than the key; `upper_bound` finds the first element greater than it.
- `equal_range` returns both boundaries, identifying every comparator-equivalent element.
- The generic algorithms make O(log n) comparisons, but non-random-access iterators may require linear increments; use associative-container member functions for tree traversal.

**Details and nuances**

For sorted insertion preserving order, insert at a chosen boundary. To test exact presence, check `lower != end && !comp(key, *lower)` (plus the reverse relation as appropriate), rather than assuming the lower bound is equal.

The range must be sorted/partitioned using an ordering compatible with the call. Mixing ascending data with a descending comparator breaks the precondition.

Heterogeneous lookup with transparent comparators can search maps/sets without constructing a full key, reducing allocation and conversion overhead.

[↑ Back to question index](#question-index)

---

## Question CPP-122

[↑ Back to question index](#question-index)

### Question CPP-122 — What is `std::optional`, and when should it not be used?

**Short answer**

- `optional<T>` stores either no value or one in-place `T`, making ordinary absence explicit without heap allocation.
- Use it when “missing” is the only extra state; use `expected<T,E>` when failure reason matters and `variant` for several meaningful alternatives.
- Check before dereference or use `value_or`; `value()` throws on absence, and an optional does not automatically make a borrowed value safe.

**Details and nuances**

Good examples include a parsed optional field, a cache lookup without diagnostics, or a function that may naturally find nothing. Avoid nested/sentinel combinations whose states are unclear.

`optional<bool>` has three states, which can be useful but often surprises readers; name the semantics explicitly. `optional<unique_ptr<T>>` also distinguishes empty optional from present null pointer—usually one state too many unless intentional.

Returning optional by value is usually simple and efficient. Do not return a reference/view inside it unless the referenced owner and invalidation contract are obvious.

[↑ Back to question index](#question-index)

---

## Question CPP-123

[↑ Back to question index](#question-index)

### Question CPP-123 — `std::variant` vs `std::any`?

**Short answer**

- `variant<A,B,...>` is a closed type-safe union: alternatives are known at compile time and processed with `get_if`/`visit`.
- `any` can hold any copy-constructible type and is recovered by runtime type identity with `any_cast`; it is more flexible but less explicit.
- Prefer `variant` for domain states/protocols and exhaustive handling; use `any` only at truly open extension/type-erasure boundaries.

**Details and nuances**

```cpp
using Result = std::variant<Success, Retry, Failure>;

std::visit([](const auto& state) {
    handle(state);
}, result);
```

`std::monostate` can provide an explicit empty/default alternative. A variant can become `valueless_by_exception` during a throwing type change, so generic code should understand that rare state.

`any_cast<T>(&value)` returns a pointer or null without throwing; value/reference forms throw `bad_any_cast` on mismatch. Neither facility serializes itself—define a stable external tag/schema separately.

[↑ Back to question index](#question-index)

---

## Question CPP-124

[↑ Back to question index](#question-index)

### Question CPP-124 — When should you use `pair`, `tuple` and structured bindings?

**Short answer**

- `pair` is appropriate for two conventional roles; `tuple` is useful for generic heterogeneous grouping and multiple internal return values.
- Prefer a named struct when fields carry domain meaning, invariants, documentation or likely evolution.
- Structured bindings can copy or bind by reference depending on `auto`, `auto&` and `const auto&`; choose explicitly to avoid hidden copies/dangling.

**Details and nuances**

```cpp
auto [iterator, inserted] = values.insert(item); // names clarify a standard pair

for (const auto& [key, value] : map) {
    use(key, value); // binds to elements; no pair copy
}
```

Returning a tuple couples callers to positional order, so changing/adding fields breaks uses. A result struct allows names, defaults and member functions while still supporting structured bindings when it remains aggregate-like.

`std::tie` creates a tuple of lvalue references for assignment/comparison; never return it when the referenced locals will die.

[↑ Back to question index](#question-index)

---

## Question CPP-125

[↑ Back to question index](#question-index)

### Question CPP-125 — How should `std::chrono` be used?

**Short answer**

- `duration<Rep,Period>` represents an amount of time with type-safe units; `time_point<Clock>` represents a point on a specific clock.
- Use `steady_clock` for elapsed time/timeouts because it is monotonic; use `system_clock` for civil/wall-clock timestamps.
- Convert units explicitly where precision/range changes, and do not compare/subtract time points from unrelated clocks.

**Details and nuances**

```cpp
using namespace std::chrono_literals;

const auto start = std::chrono::steady_clock::now();
perform_work();
const auto elapsed = std::chrono::steady_clock::now() - start;
if (elapsed > 100ms) { report_slow(); }
```

`duration_cast` performs explicit conversions; implicit conversion is allowed only when it is safely representable by chrono rules. Integer duration conversions can truncate, so round/floor/ceil deliberately.

Wall time can jump because of synchronization or user changes. For persisted timestamps, define epoch, precision, time zone and daylight-saving interpretation; C++20 calendar/time-zone facilities help but deployment data availability still matters.

[↑ Back to question index](#question-index)

---

## Question CPP-126

[↑ Back to question index](#question-index)

### Question CPP-126 — `std::thread`: `join`/`detach` vs `std::jthread`?

**Short answer**

- A joinable `std::thread` must be joined or detached before destruction; otherwise its destructor calls `std::terminate`.
- `join` gives an explicit lifetime boundary; `detach` removes that boundary and is risky because captured objects/process shutdown may outlive one another.
- C++20 `jthread` requests stop and joins on destruction, and integrates cooperative cancellation through `stop_token`; it is the safer default for owned threads.

**Details and nuances**

Joining blocks until completion and must not be called from the same thread. Use RAII so exceptions cannot bypass the join. Moving a thread transfers the thread handle/join responsibility.

A stop request is cooperative, not forced termination: the worker checks its token or waits with stop-aware primitives and exits while preserving invariants.

Prefer task pools/executors over one thread per small operation. If detaching is unavoidable, move all required state into independent ownership and define how shutdown observes/completes the detached work.

[↑ Back to question index](#question-index)

---

## Question CPP-127

[↑ Back to question index](#question-index)

### Question CPP-127 — How do `future`, `promise` and `async` work?

**Short answer**

- A `promise<T>` sets one value/exception; its associated `future<T>` waits and consumes that result, propagating the exception on `get()`.
- `async` creates a future for a callable, but the default launch policy may run asynchronously or defer execution until waiting.
- Standard futures provide basic one-result synchronization, not a full continuation/cancellation/executor model; avoid blocking critical event-loop/UI threads on them.

**Details and nuances**

`future::get()` is normally one-shot and invalidates the future. `shared_future` permits multiple consumers of the same result. Destroying a promise without setting a result makes the consumer observe a broken-promise error.

Use `std::launch::async` when concurrent execution is required and `std::launch::deferred` when lazy execution is intended. A future produced by async execution can block during destruction in relevant cases, so ownership/scope affects latency.

`packaged_task` wraps a callable whose result feeds a future and is useful in a custom queue. For application architecture, a callback/Promise/executor abstraction may compose better than synchronously waiting on `future`.

[↑ Back to question index](#question-index)

---

## Question CPP-128

[↑ Back to question index](#question-index)

### Question CPP-128 — When is `shared_mutex` useful?

**Short answer**

- `shared_mutex` allows multiple shared readers or one exclusive writer; `shared_lock` owns the shared mode and `unique_lock` the write mode.
- It helps only when reads are truly concurrent, frequent and long enough to offset more expensive locking/coordination.
- Fairness and upgrade semantics are not generally guaranteed; writer starvation and read-modify races need explicit design.

**Details and nuances**

A “reader” must not mutate shared caches/counters unless those have separate synchronization. Returning references after releasing the shared lock can expose data that a later writer invalidates.

There is no portable atomic promotion from shared ownership to exclusive ownership. Release-and-reacquire requires rechecking the condition because another writer may change state in between.

Benchmark against an ordinary mutex, immutable snapshots or single-owner design. Short read sections can be faster with a simple mutex due to lower overhead and better fairness.

[↑ Back to question index](#question-index)

---

# 11. Systems and Networking Foundations

## Question CPP-129

[↑ Back to question index](#question-index)

### Question CPP-129 — Process vs thread, and what is a context switch?

**Short answer**

- A process is a resource/isolation boundary with its own virtual address space; threads are execution streams inside a process and share its memory/resources.
- Process isolation improves fault/security boundaries but communication is explicit; threads communicate cheaply through shared memory but create synchronization/lifetime risks.
- A context switch saves one execution context and restores another; scheduler work plus cache/TLB disruption can make excessive threads expensive.

**Details and nuances**

Each thread has its own registers, instruction pointer, stack and thread-local storage. Threads normally share code, heap, open process handles/files and global state.

A process switch may require more address-space bookkeeping than switching between threads of one process, but modern costs depend on platform, CPU and which cache/TLB state remains useful. A switch can be voluntary (waiting/yielding) or preemptive.

Choose processes for isolation, independent deployment/recovery or privilege boundaries. Choose threads for low-latency shared-state work when a clear ownership/synchronization model exists; use bounded pools rather than matching one thread to every request.

[↑ Back to question index](#question-index)

---

## Question CPP-130

[↑ Back to question index](#question-index)

### Question CPP-130 — What are a call stack and a stack frame?

**Short answer**

- A thread's call stack tracks active calls; a conceptual frame contains return/control information, saved registers and some parameters/local storage.
- Exact layout is ABI/compiler/optimization-dependent: values may live only in registers, frames may be omitted, and inlining can remove calls entirely.
- Returning references/pointers to automatic locals dangles, and unbounded recursion/large locals can exhaust finite stack space.

**Details and nuances**

A call instruction transfers control while recording where execution should return; the prologue/epilogue may adjust the stack pointer and preserve ABI-mandated registers. Exceptions use unwind metadata to identify destructors/handlers rather than requiring one universal frame shape.

Debug backtraces are reconstructions from symbols, unwind information and machine state. Optimized code can show inlined frames, tail calls or unavailable variables, so the source-level call sequence may not map one-to-one to memory frames.

Automatic storage duration is lexical/lifetime behavior, not a promise that every object physically occupies stack memory.

[↑ Back to question index](#question-index)

---

## Question CPP-131

[↑ Back to question index](#question-index)

### Question CPP-131 — How do virtual memory, page faults and the TLB relate?

**Short answer**

- Virtual memory maps each process's virtual pages to physical memory or backing storage, providing isolation, sparse address spaces and controlled sharing.
- The TLB caches recent virtual-to-physical translations; a TLB miss needs a page-table walk, while a page fault needs OS handling because the mapping/presence/permission is not immediately usable.
- Minor faults can establish an already-memory-resident page; major faults require storage I/O and are much more expensive.

**Details and nuances**

Allocating virtual address space does not necessarily commit/touch physical pages immediately. First access can trigger demand-zero allocation or loading a mapped file page.

Page size affects mapping overhead, locality and fragmentation. Huge pages can reduce TLB pressure for large stable workloads but increase memory granularity and operational complexity.

When diagnosing latency, separate allocator time, page faults, working-set pressure and cache misses. “Memory available” does not guarantee the hot working set remains resident, and random access over a large region stresses both caches and TLBs.

[↑ Back to question index](#question-index)

---

## Question CPP-132

[↑ Back to question index](#question-index)

### Question CPP-132 — How do cache hierarchy, alignment and padding affect performance?

**Short answer**

- Registers and L1/L2/L3 caches are progressively larger/slower before RAM; contiguous predictable access exploits cache lines and hardware prefetching.
- Alignment is the address constraint for an object; padding lets members/elements satisfy alignment and can increase `sizeof`.
- Layout changes can reduce cache misses or false sharing, but over-padding increases footprint; measure the actual access pattern.

**Details and nuances**

Array-of-structures is convenient for whole-record access; structure-of-arrays can improve locality/vectorization when hot loops read only a few fields. Reordering members may reduce padding but can break ABI/serialization assumptions.

Misaligned access may be slower or invalid for particular instructions/platforms. Use normal type alignment, aligned allocation and `alignas` rather than packed reinterpretation unless an external binary format is copied/decoded safely.

Two threads writing different variables on one cache line can suffer false sharing ([CPP-068](#question-cpp-068)). Conversely, data read together should often live together; layout optimization must balance sharing, working-set size and maintainability.

[↑ Back to question index](#question-index)

---

## Question CPP-133

[↑ Back to question index](#question-index)

### Question CPP-133 — What is endianness?

**Short answer**

- Endianness is the byte order used to represent multi-byte scalar values: little-endian stores the least-significant byte first; big-endian the most-significant.
- Network/file protocols must specify byte order explicitly; copying a native struct is not portable because of endianness, padding, alignment and ABI layout.
- Convert at boundaries with protocol helpers or explicit serialization; C++20 can inspect native order and C++23 provides `std::byteswap`.

**Details and nuances**

For integer `0x01020304`, little-endian memory begins `04 03 02 01`, while big-endian begins `01 02 03 04`. Endianness describes bytes, not the textual order in which a number is printed.

Traditional Internet “network byte order” is big-endian. APIs such as `htons`/`ntohl` convert selected integer widths; structured protocols should use a serializer that also handles lengths, signedness and versioning.

Byte swapping a floating-point/object representation requires a specified external format, not an assumption that every platform uses the same representation.

[↑ Back to question index](#question-index)

---

## Question CPP-134

[↑ Back to question index](#question-index)

### Question CPP-134 — System calls vs interrupts, CPU exceptions and OS signals?

**Short answer**

- A system call is an intentional controlled transition from user code to a kernel service.
- A hardware interrupt is asynchronous to the current instruction; a CPU exception/fault is synchronous to instruction execution, such as a page fault or divide error.
- An OS signal is a process/thread notification abstraction often caused by software or translated faults; it is distinct from a C++ language exception.

**Details and nuances**

Terminology varies by CPU/OS, but the interview distinction is intent and timing. The kernel handles low-level events, may schedule another thread, and later resumes or terminates user execution.

POSIX signal handlers can safely call only a narrow async-signal-safe set and should normally set minimal state or write to a safe channel. Windows structured exceptions and console/control notifications have different contracts.

C++ `try`/`catch` handles language exceptions under the C++ runtime. Portable code must not assume it catches access violations, signals or every hardware fault; translate only at a platform boundary designed for it.

[↑ Back to question index](#question-index)

---

## Question CPP-135

[↑ Back to question index](#question-index)

### Question CPP-135 — What IPC mechanisms would you choose between?

**Short answer**

- Pipes and local sockets provide stream/message communication with kernel-managed isolation; message queues add discrete buffered messages; shared memory offers lowest-copy bulk exchange but needs synchronization.
- RPC/COM hides transport behind request/response interfaces but adds serialization, versioning, reentrancy and distributed-failure semantics.
- Choose by process/machine boundary, payload/rate, latency, security, ownership, backpressure, recovery and deployment—not raw throughput alone.

**Details and nuances**

| Mechanism | Good fit | Main burden |
|---|---|---|
| anonymous/named pipe | parent-child or local stream | framing and lifecycle |
| local/network socket | portable bidirectional endpoint | protocol, partial I/O, security |
| message queue | discrete asynchronous messages | capacity and broker/kernel semantics |
| shared memory/mapped file | large low-copy local data | synchronization, corruption and cleanup |
| COM/RPC | typed component/service calls | marshaling, versioning and call coupling |

Every IPC boundary needs message size limits, schema evolution, timeouts/cancellation, authentication/authorization where relevant, and behavior when a peer dies halfway through an operation.

For a native engine connected to another UI/runtime process, a process boundary can contain native crashes while batching offsets serialization/IPC overhead.

[↑ Back to question index](#question-index)

---

## Question CPP-136

[↑ Back to question index](#question-index)

### Question CPP-136 — TCP vs UDP, and why does TCP need message framing?

**Short answer**

- TCP is a reliable ordered bidirectional byte stream with congestion/flow control; it preserves bytes, not application message boundaries.
- UDP sends independent datagrams with preserved datagram boundaries but no built-in delivery, ordering, duplicate suppression or congestion policy for the application.
- TCP protocols need framing—fixed size, delimiter, length prefix or self-describing format—and must handle partial reads/writes.

**Details and nuances**

One `send` does not correspond to one peer `recv`: bytes may be split or coalesced. A length-prefixed protocol validates the length against a configured maximum, buffers until complete, then parses exactly one frame.

TCP retransmission can cause head-of-line delay; its sliding windows regulate unacknowledged data and receiver capacity. UDP can reduce latency/control overhead but the application must implement every required reliability/order/congestion behavior.

Choose semantics first. Telemetry that tolerates loss may fit UDP; commands and state synchronization often fit TCP/TLS or a higher-level protocol. QUIC offers reliable multiplexed streams over UDP but is not “raw UDP without reliability.”

[↑ Back to question index](#question-index)

---

## Question CPP-137

[↑ Back to question index](#question-index)

### Question CPP-137 — What is the socket lifecycle, and how do I/O multiplexers help?

**Short answer**

- A server creates a socket, binds, listens, accepts connected sockets, performs partial I/O and closes; a client creates, connects, exchanges data and closes.
- Blocking one thread per connection is simple but scales poorly at large concurrency; multiplexers report readiness/completion for many descriptors/handles.
- `select`/`poll`/`epoll`/`kqueue` are readiness-oriented families; Windows IOCP is completion-oriented. The application still owns framing, buffers, timeouts and backpressure.

**Details and nuances**

All calls require error handling: connect can complete asynchronously, accept can fail transiently, reads can return fewer bytes, writes can make partial progress, and orderly peer close is distinct from reset/error.

Level-triggered readiness continues reporting while work remains; edge-triggered designs must drain until “would block” or risk missing progress. Completion APIs report finished operations and require buffer lifetime until completion.

Libraries such as libuv abstract platform mechanisms but cannot remove application-level overload. Cap connections, input/output buffer sizes and outstanding work; apply idle/deadline cancellation.

[↑ Back to question index](#question-index)

---

## Question CPP-138

[↑ Back to question index](#question-index)

### Question CPP-138 — HTTP vs HTTPS vs WebSocket?

**Short answer**

- HTTP is a request/response application protocol with methods, status codes, headers and bodies; versions differ in framing/multiplexing/transport details.
- HTTPS is HTTP protected by TLS, providing authenticated encryption/integrity when certificate and hostname validation are correct.
- WebSocket begins with an HTTP upgrade/handshake and then carries long-lived bidirectional messages, useful when server push and low per-message overhead matter.

**Details and nuances**

HTTP APIs still need idempotency, caching, authentication, timeouts, body limits and versioned schemas. HTTP/2 multiplexes streams over one TCP connection; HTTP/3 uses QUIC to avoid TCP-level cross-stream head-of-line blocking.

TLS does not authorize the application user, validate payload semantics or protect already-compromised endpoints. Define certificate trust, supported protocol versions and secret handling operationally.

WebSocket provides message framing but not business-level delivery guarantees, replay recovery or infinite buffering. Implement heartbeats, reconnect/resubscribe behavior, bounded queues and backpressure.

[↑ Back to question index](#question-index)

---

## Question CPP-139

[↑ Back to question index](#question-index)

### Question CPP-139 — Livelock and starvation vs deadlock?

**Short answer**

- Deadlocked participants wait forever in a dependency cycle; livelocked participants keep changing/retrying but make no useful progress.
- Starvation means one participant is continually denied progress while others proceed, often because of unfair scheduling/locking or reader/writer preference.
- Prevent with ownership/order, bounded randomized backoff, fair admission/queues and progress metrics—not retries or timeouts alone.

**Details and nuances**

Two polite threads that repeatedly release a resource whenever they see contention can livelock in synchrony. A CAS loop under extreme contention can starve one thread despite global throughput.

Semaphores limit concurrent access or represent available permits; they do not automatically guarantee fairness and are not ownership locks. Reader/writer locks can starve writers if readers arrive continuously, depending on implementation.

Define the required progress guarantee: obstruction-free, lock-free and wait-free have precise meanings stronger than “uses atomics.” Instrument retry counts and wait age, not only average throughput.

[↑ Back to question index](#question-index)

---

# 12. Software Design and APIs

## Question CPP-140

[↑ Back to question index](#question-index)

### Question CPP-140 — What do the SOLID principles mean in practice?

**Short answer**

- SOLID is a set of design heuristics: single reason to change, substitutable extensions, focused interfaces and dependencies pointed toward abstractions.
- The goal is localized change and testable boundaries, not one class/interface per line of code.
- Apply the principles where variation/ownership exists and evaluate cohesion, coupling and failure modes; over-abstraction is also a maintenance cost.

**Details and nuances**

- **S — Single Responsibility:** group behavior that changes for the same reason; separate unrelated policy/mechanism.
- **O — Open/Closed:** add supported behavior through stable extension points without repeatedly editing fragile central logic.
- **L — Liskov Substitution:** derived implementations preserve the base contract, including preconditions, postconditions and invariants.
- **I — Interface Segregation:** consumers depend only on capabilities they need; avoid “fat” interfaces.
- **D — Dependency Inversion:** high-level policy depends on stable abstractions, while concrete I/O/framework details plug in below.

Example: calculation policy should consume plain data through a small interface; persistence, transport and UI integration are adapters. This enables deterministic tests without pretending every implementation detail needs runtime polymorphism.

[↑ Back to question index](#question-index)

---

## Question CPP-141

[↑ Back to question index](#question-index)

### Question CPP-141 — How do DRY, KISS and YAGNI complement each other?

**Short answer**

- DRY removes duplicated knowledge/rules, not every visually similar line; one business rule should have one authoritative representation.
- KISS chooses the simplest design that satisfies current correctness/operability needs.
- YAGNI postpones speculative capabilities until a real requirement appears; together they resist both copy-paste divergence and premature frameworks.

**Details and nuances**

Two code fragments that happen to look alike may evolve independently; extracting them can create false coupling. Conversely, duplicating a protocol constant or validation rule risks inconsistent fixes and should be centralized.

“Simple” is not “missing failure handling.” A bounded queue with explicit overload policy is more code than an unbounded vector but simpler operationally because behavior is predictable.

Delay irreversible abstraction decisions, not evidence gathering. Build a narrow seam around known variation and refactor when a second/third concrete case reveals the stable commonality.

[↑ Back to question index](#question-index)

---

## Question CPP-142

[↑ Back to question index](#question-index)

### Question CPP-142 — Why prefer composition over inheritance?

**Short answer**

- Composition builds behavior from owned/collaborating objects and exposes only the chosen contract, reducing coupling to implementation details.
- Inheritance is appropriate for a genuine substitutable “is-a” relationship or interface, not merely code reuse.
- Prefer delegation/policies when behavior varies independently; use inheritance when Liskov substitution and lifecycle/destruction semantics are explicit.

**Details and nuances**

Implementation inheritance can expose protected state, create fragile base dependencies and combine dimensions into a large hierarchy. Composition allows replacing one strategy/member without changing the object's public identity.

```cpp
class ReportService {
public:
    ReportService(std::unique_ptr<Formatter> formatter,
                  std::unique_ptr<Storage> storage);
};
```

Runtime composition may add indirection/allocation; templates can provide compile-time policies when ABI/build cost is acceptable. Do not replace a clear small hierarchy with needless dependency objects solely to follow a slogan.

[↑ Back to question index](#question-index)

---

## Question CPP-143

[↑ Back to question index](#question-index)

### Question CPP-143 — How do PImpl, header-only and compiled libraries trade off?

**Short answer**

- PImpl keeps private representation in a separately compiled implementation, reducing rebuild coupling and helping preserve class ABI.
- Header-only libraries simplify distribution and enable templates/inlining, but increase compile time, expose implementation and amplify ODR/toolchain differences.
- Compiled libraries hide implementation and reduce consumer builds, but require binary artifacts, export/version rules and a compatible ABI.

**Details and nuances**

PImpl usually stores `unique_ptr<Impl>`; constructors/destructor/moves that need the complete `Impl` are defined out-of-line. It adds allocation/indirection unless storage is otherwise arranged and does not stabilize every semantic dependency.

Header-only does not mean “no ABI concerns” when inline code manipulates standard-library types or crosses shared-library boundaries. It shifts more compatibility responsibility to source rebuilds.

For a public DLL, prefer a narrow C-compatible or abstract/opaque interface, explicit ownership and version negotiation. Keep compiler-specific containers/exceptions/allocations from crossing unless producer and consumer toolchains are intentionally locked together.

[↑ Back to question index](#question-index)

---

## Question CPP-144

[↑ Back to question index](#question-index)

### Question CPP-144 — How do common GoF patterns differ?

**Short answer**

- Creational patterns such as Factory separate object selection/construction; behavioral patterns such as Strategy/Command encapsulate interchangeable behavior or requests.
- Structural patterns differ by intent: Adapter changes an interface, Decorator adds behavior, Facade simplifies a subsystem, Proxy controls access to another object.
- Name a pattern only after stating the problem, ownership and tradeoff; patterns are vocabulary, not mandatory architecture.

**Details and nuances**

| Pattern | Core intent |
|---|---|
| Factory Method | defer/select creation behind an interface |
| Strategy | replace an algorithm/policy |
| Adapter | translate one interface into another |
| Decorator | wrap the same interface to add behavior |
| Facade | provide a simpler entry point to a subsystem |
| Proxy | stand in for remote/lazy/protected access |
| Command | represent a request as an object/value |

One wrapper can resemble several patterns structurally; intent distinguishes them. An RPC proxy controls/routes access to a remote object, while an adapter translates an external API into an application-specific port.

Each abstraction adds indirection, lifetime and error paths. Prefer ordinary functions/data when runtime substitution, composition or boundary isolation is not needed.

[↑ Back to question index](#question-index)

---

## Question CPP-145

[↑ Back to question index](#question-index)

### Question CPP-145 — Observer vs pub-sub and event-driven architecture?

**Short answer**

- Observer directly registers subscribers with a subject; pub-sub usually inserts a topic/broker so publishers and consumers need not know one another.
- Event-driven systems decouple timing/components but need explicit ordering, delivery, backpressure, retries, idempotency and observability contracts.
- The hardest local issue is subscription lifetime/reentrancy; the hardest distributed issue is duplicate/lost/out-of-order delivery and schema evolution.

**Details and nuances**

Observer calls can be synchronous: a callback may unsubscribe, destroy objects, re-enter the publisher or throw. Use connection tokens/RAII unsubscription, define callback thread and avoid invoking unknown code under internal locks.

Pub-sub can be in-process or broker-based. “Published” may mean accepted to a queue, persisted or delivered; define semantics rather than assuming exactly-once behavior.

For high-rate UI updates, events can feed a coalescing state store, while a scheduled UI subscriber renders snapshots. This preserves decoupling without turning every incoming tick into a rendering callback.

[↑ Back to question index](#question-index)

---

## Question CPP-146

[↑ Back to question index](#question-index)

### Question CPP-146 — Exceptions vs error codes vs `std::expected`?

**Short answer**

- Exceptions separate uncommon failure propagation from the success path and compose with RAII, but require an exception-safe codebase and cannot cross C/COM/other ABI boundaries.
- Error codes are explicit and boundary-friendly but are easy to ignore and can clutter propagation unless wrapped in a systematic result type.
- C++23 `expected<T,E>` represents either a value or typed expected failure; it is good for recoverable frequent errors, not cancellation/panic by itself.

**Details and nuances**

Choose one strategy per layer and translate at boundaries. Constructors that cannot establish invariants naturally throw; parsers/network operations often benefit from typed result values; COM returns `HRESULT`; Node async APIs reject a Promise/error-first callback.

An error type should preserve stable category/code and safe diagnostic context. Avoid global last-error state and avoid logging at every propagation layer, which creates duplicates without ownership of recovery.

No mechanism replaces invariants and cleanup: use RAII for all partial progress. Do not use exceptions for ordinary control flow, and do not discard `expected`/status results.

[↑ Back to question index](#question-index)

---

## Question CPP-147

[↑ Back to question index](#question-index)

### Question CPP-147 — How do you evolve an API without breaking source or binary compatibility?

**Short answer**

- Source compatibility means clients still compile; binary compatibility means old binaries still load/call correctly; semantic compatibility means behavior/contracts remain acceptable.
- Add capabilities through new functions/interfaces/versioned messages and tolerant readers; avoid changing exported layout, virtual order, calling convention, ownership or existing meaning.
- Define deprecation/version windows, capability negotiation and compatibility tests using real old/new producer-consumer combinations.

**Details and nuances**

Adding a field can be source-compatible yet break aggregate initialization or binary serialization. Adding a virtual function can change vtable ABI; changing an inline function changes behavior only after consumers rebuild.

For COM, publish a new IID for an incompatible interface and let `QueryInterface` discover it. For IPC, version the envelope/schema, ignore safe unknown fields and reject unsupported required capabilities clearly.

For native DLLs, use opaque handles/PImpl, fixed-width boundary types and explicit create/destroy functions so allocation stays in the owning module. Document thread, lifetime and error contracts as part of compatibility—not just signatures.

[↑ Back to question index](#question-index)

---

## Question CPP-148

[↑ Back to question index](#question-index)

### Question CPP-148 — What does ACID mean?

**Short answer**

- **Atomicity:** a transaction commits completely or has no effect; **Consistency:** it preserves declared database invariants.
- **Isolation:** concurrent transactions behave according to a defined isolation level; **Durability:** committed data survives the promised failures.
- ACID is not “no concurrency anomalies under every setting”: actual guarantees depend on isolation level, database engine, schema constraints and deployment.

**Details and nuances**

Consistency is application/schema correctness, not the same “C” as consistency in CAP. Isolation levels trade concurrency for protection against dirty reads, non-repeatable reads, phantoms and write anomalies.

Durability can depend on flush/replication configuration and the failure model being claimed. A successful client response before truly durable replication may offer weaker semantics than assumed.

Keep transactions short, perform external side effects with outbox/idempotency patterns, retry only retryable failures, and make the whole operation safe under uncertain commit outcomes.

[↑ Back to question index](#question-index)

---

## Question CPP-149

[↑ Back to question index](#question-index)

### Question CPP-149 — How does an LRU cache work?

**Short answer**

- An LRU cache evicts the least recently used entry when capacity is exceeded, approximating temporal-locality value.
- Typical O(1) design combines a hash map for lookup with a doubly linked recency list; every hit/update moves the node to the front.
- Production design also needs capacity by bytes/cost, expiration/invalidation, concurrency and cache-miss stampede policy—not only entry count.

**Details and nuances**

The hash map stores key → list iterator; the list stores key/value nodes from most to least recent. Insert/update touches the front, and overflow removes the back plus its map entry.

Pointers/iterators and ownership must remain valid across moves/erase. Under a mutex, avoid performing slow value creation while holding the global cache lock; per-key coordination can prevent many threads from recomputing the same missing value.

LRU performs poorly for scans larger than capacity because one pass can evict the hot working set. Alternatives include segmented LRU, LFU/TinyLFU or workload-specific admission; measure hit rate, bytes, eviction reason and latency.

[↑ Back to question index](#question-index)

---

# 13. Algorithms and Interview Patterns

## Question CPP-150

[↑ Back to question index](#question-index)

### Question CPP-150 — How does Floyd's tortoise-and-hare cycle detection work?

**Short answer**

- Move a slow pointer by one successor and a fast pointer by two; if a reachable cycle exists, their relative motion guarantees a meeting inside it.
- If fast reaches null there is no linked-list cycle. Detection takes O(n) time and O(1) extra memory without modifying nodes.
- To find the cycle entry, reset one pointer to the head after the meeting and advance both one step; their next meeting is the entry.

**Details and nuances**

```cpp
struct Node {
    int value{};
    Node* next{};
};

Node* cycle_entry(Node* head) {
    Node* slow = head;
    Node* fast = head;

    do {
        if (fast == nullptr || fast->next == nullptr) {
            return nullptr;
        }
        slow = slow->next;
        fast = fast->next->next;
    } while (slow != fast);

    slow = head;
    while (slow != fast) {
        slow = slow->next;
        fast = fast->next;
    }
    return slow;
}
```

Let `mu` be the non-cyclic prefix length and `lambda` the cycle length. At the first meeting, the distance relationship is a multiple of `lambda`; advancing one pointer from the head and one from the meeting point by equal steps aligns them at the entry after `mu` steps.

The algorithm applies to a deterministic successor sequence (a functional graph), not arbitrary graph-cycle detection. Always validate `fast` and `fast->next` before the double step. It can also find cycle length by walking once around from the meeting point.

The technique is commonly attributed to Floyd; modern formalization explicitly describes it as “ascribed to Floyd”: [Archive of Formal Proofs](https://www.isa-afp.org/browser_info/devel/AFP/TortoiseHare/document.pdf).

[↑ Back to question index](#question-index)

---

## Question CPP-151

[↑ Back to question index](#question-index)

### Question CPP-151 — How does Brent's cycle-detection algorithm differ from Floyd's?

**Short answer**

- Brent keeps one checkpoint and advances the other pointer one step, doubling the checkpoint interval at powers of two.
- It still finds a cycle in O(mu + lambda) time and O(1) space, often with fewer successor-function evaluations than Floyd.
- Floyd is usually simpler for linked lists; Brent is attractive when computing the next state is expensive.

**Details and nuances**

Brent compares the moving hare with a stationary tortoise for blocks of length `1, 2, 4, ...`. When a block ends, the tortoise jumps to the hare's current position and the block length doubles. A match yields the cycle length `lambda`.

To find entry distance `mu`, place two pointers at the start, advance one by `lambda`, then move both one step until they meet. The common state is the cycle entry.

Both algorithms require equality and deterministic repeated application of the same successor function. Neither stores visited states; if memory is available and equality/successors have different costs, a hash set can offer a simpler alternative with O(n) space.

Primary reference: Richard Brent, [“An improved Monte Carlo factorization algorithm”](https://maths-people.anu.edu.au/brent/pd/rpb051i.pdf), which includes the cycle-finding method.

[↑ Back to question index](#question-index)

---

## Question CPP-152

[↑ Back to question index](#question-index)

### Question CPP-152 — Two pointers vs sliding window?

**Short answer**

- “Two pointers” is the broad pattern of coordinating two indices/iterators: opposite ends, fast/slow, read/write compaction or partition boundaries.
- A sliding window is a specific contiguous-range pattern that expands one boundary and shrinks the other while maintaining aggregate state.
- O(n) movement requires a monotonic invariant; negative values or non-monotonic constraints can invalidate the usual variable-window logic.

**Details and nuances**

Common patterns:

- sorted two-sum: left/right move according to the sum;
- remove duplicates: read pointer scans, write pointer compacts;
- fixed window: add the entering item and remove the leaving item;
- variable window: grow until invalid, shrink until valid, record the optimum.

For “smallest subarray with sum at least K,” a simple positive-number window works because expanding never decreases the sum. With negative numbers that invariant disappears; prefix sums plus a monotonic deque may be required.

State the invariant before coding—for example, “the half-open range `[left,right)` is valid after the inner loop”—and count each boundary's total movement to justify O(n).

[↑ Back to question index](#question-index)

---

## Question CPP-153

[↑ Back to question index](#question-index)

### Question CPP-153 — How do you write binary search with a correct invariant?

**Short answer**

- Define the boundary being found and a monotonic predicate, then maintain a precise interval invariant—commonly the answer is in half-open `[low, high)`.
- Compute `mid = low + (high - low) / 2`; move exactly one boundary so the interval strictly shrinks.
- Return the insertion/boundary position and separately test equality; this handles empty ranges and “not found” without special-case loops.

**Details and nuances**

```cpp
template<class Predicate>
std::size_t first_true(std::size_t low,
                       std::size_t high,
                       Predicate predicate) {
    // predicate is false before the answer and true from the answer onward.
    while (low < high) {
        const auto mid = low + (high - low) / 2;
        if (predicate(mid)) {
            high = mid;
        } else {
            low = mid + 1;
        }
    }
    return low;
}
```

`lower_bound` is the first element not less than the key; `upper_bound` is the first greater. Prefer the standard algorithms for ordinary sorted ranges and explain the invariant when implementing from scratch.

Binary search also applies “on the answer” when feasibility is monotonic. Verify overflow in predicate calculations, termination with adjacent bounds, and whether the high endpoint is an included candidate or exclusive sentinel.

[↑ Back to question index](#question-index)

---

## Question CPP-154

[↑ Back to question index](#question-index)

### Question CPP-154 — BFS vs DFS?

**Short answer**

- BFS explores by distance layers with a queue and finds minimum edge-count paths in an unweighted graph.
- DFS explores one branch deeply using recursion or an explicit stack and is natural for reachability, components, backtracking, cycle/topological/SCC analyses.
- Both run O(V + E) on adjacency lists, but result/order depends on neighbor order and DFS recursion can overflow on deep graphs.

**Details and nuances**

Mark a BFS vertex when it is enqueued, not when dequeued, to avoid duplicate queue entries. Store a predecessor to reconstruct paths; distance is the BFS layer.

DFS often tracks colors—unvisited, active, finished. In a directed graph an edge to an active vertex identifies a cycle; undirected traversal must ignore the immediate parent edge.

Neither algorithm alone solves weighted shortest paths. BFS works for equal weights; 0-1 BFS uses a deque for weights 0/1; nonnegative general weights lead to Dijkstra.

[↑ Back to question index](#question-index)

---

## Question CPP-155

[↑ Back to question index](#question-index)

### Question CPP-155 — How does Dijkstra's shortest-path algorithm work?

**Short answer**

- Maintain tentative distances from the source, repeatedly finalize the unprocessed vertex with minimum distance, and relax its outgoing edges.
- With adjacency lists and a min-priority queue, the common lazy implementation is O((V + E) log V).
- It requires nonnegative edge weights; negative edges need another algorithm, and arithmetic must guard infinity/overflow.

**Details and nuances**

Because `std::priority_queue` has no decrease-key, push an updated `(distance, vertex)` entry and skip it later when its distance is stale. Record predecessors only when a relaxation improves the distance.

The invariant is that when the smallest tentative non-stale vertex is removed, no future nonnegative path can improve it. A negative edge breaks that proof even when no negative cycle exists.

Use BFS for unit weights, 0-1 BFS for weights `{0,1}`, Bellman-Ford for reachable negative weights/cycle detection, and A* when an admissible heuristic targets one destination.

Primary reference: E. W. Dijkstra, [“A Note on Two Problems in Connexion with Graphs”](https://eudml.org/doc/131436).

[↑ Back to question index](#question-index)

---

## Question CPP-156

[↑ Back to question index](#question-index)

### Question CPP-156 — How does topological sorting work, and how does it detect a cycle?

**Short answer**

- A topological order lists every directed edge from an earlier vertex to a later one and exists only for a DAG.
- Kahn's algorithm repeatedly removes a zero-indegree vertex and decrements outgoing neighbors; DFS can alternatively order by finish time while detecting back edges.
- If Kahn processes fewer than V vertices, a directed cycle remains. Complexity is O(V + E).

**Details and nuances**

Build indegrees, enqueue all zero-indegree vertices, emit one, and enqueue a neighbor when its indegree becomes zero. A priority queue can choose the lexicographically smallest valid order at extra logarithmic cost.

Multiple valid orders are normal. Dependency edge direction must be consistent: if `A → B` means “A before B,” emit A first; if it means “A depends on B,” reverse it for scheduling.

The processed-count test identifies existence of a cycle but not its exact path. DFS colors or a second pass over the remaining subgraph can reconstruct a cycle for diagnostics.

Primary reference: A. B. Kahn, [“Topological sorting of large networks”](https://doi.org/10.1145/368996.369025).

[↑ Back to question index](#question-index)

---

## Question CPP-157

[↑ Back to question index](#question-index)

### Question CPP-157 — How does Union-Find / Disjoint Set Union work?

**Short answer**

- DSU maintains a partition with `find(x)` for a representative and `unite(a,b)` for merging components.
- Parent forests plus path compression and union by rank/size give near-constant amortized time: O(alpha(n)) per operation.
- It fits incremental undirected connectivity and Kruskal's MST, but not arbitrary deletions, paths or directed reachability.

**Details and nuances**

```cpp
class DisjointSet {
public:
    explicit DisjointSet(std::size_t n) : parent_(n), size_(n, 1) {
        std::iota(parent_.begin(), parent_.end(), 0);
    }

    std::size_t find(std::size_t x) {
        if (parent_[x] != x) parent_[x] = find(parent_[x]);
        return parent_[x];
    }

    bool unite(std::size_t a, std::size_t b) {
        a = find(a);
        b = find(b);
        if (a == b) return false;
        if (size_[a] < size_[b]) std::swap(a, b);
        parent_[b] = a;
        size_[a] += size_[b];
        return true;
    }

private:
    std::vector<std::size_t> parent_;
    std::vector<std::size_t> size_;
};
```

Path compression flattens searches; union by size/rank prevents tall trees. The representative is an implementation detail and can change after a union, so do not use it as a permanent external ID.

Primary reference: Robert Tarjan, [“Efficiency of a Good But Not Linear Set Union Algorithm”](https://doi.org/10.1145/321879.321884).

[↑ Back to question index](#question-index)

---

## Question CPP-158

[↑ Back to question index](#question-index)

### Question CPP-158 — How do heaps solve priority and top-K problems?

**Short answer**

- A binary heap keeps the extreme element at the root: peek O(1), push/pop O(log n), and building from n elements O(n).
- C++ `priority_queue` is a max-heap by default; use `greater` for a min-heap and include tie-breaking in the comparator when needed.
- For largest K items, keep a min-heap of size K for O(n log K); offline `nth_element` can partition in average linear time when order/streaming is unnecessary.

**Details and nuances**

Heaps provide partial, not full, ordering. Iterating the underlying storage is not sorted, and repeatedly popping to sort costs O(n log n).

Common uses include schedulers, Dijkstra/A*, merging sorted streams and bounded top-K. A lazy-update heap may hold stale entries; validate on pop rather than requiring decrease-key.

Define stable tie behavior explicitly if observable. A comparator must be a strict weak ordering and is inverted relative to “comes first” intuition in `priority_queue`: the element considered largest by the ordering appears at `top()`.

[↑ Back to question index](#question-index)

---

## Question CPP-159

[↑ Back to question index](#question-index)

### Question CPP-159 — How does Kadane's maximum-subarray algorithm work?

**Short answer**

- Track the maximum sum of a non-empty subarray ending at the current position: either extend the previous one or start at the current element.
- Track the best value seen globally; this dynamic program is O(n) time and O(1) extra space.
- Initialize from the first element for the non-empty problem so an all-negative array returns its largest element, not incorrectly zero.

**Details and nuances**

```cpp
std::optional<long long> maximum_subarray(std::span<const int> values) {
    if (values.empty()) return std::nullopt;

    long long ending_here = values.front();
    long long best = ending_here;
    for (int value : values.subspan(1)) {
        ending_here = std::max<long long>(value, ending_here + value);
        best = std::max(best, ending_here);
    }
    return best;
}
```

To return indices, remember the tentative start whenever beginning at the current element wins, and snapshot it when the global best improves.

The empty-subarray variant deliberately permits answer zero and uses different initialization. Use a wider/safely checked sum type when input totals can overflow.

[↑ Back to question index](#question-index)

---

## Question CPP-160

[↑ Back to question index](#question-index)

### Question CPP-160 — How does Knuth-Morris-Pratt string search work?

**Short answer**

- KMP preprocesses the pattern into prefix/failure information: after a mismatch, it reuses the longest prefix that is also a suffix instead of rechecking text.
- Preprocessing is O(m), searching is O(n), and extra space is O(m), giving deterministic O(n + m).
- It is useful for repeated/self-overlapping patterns and streaming-style scans; simpler/library search may be preferable unless worst-case behavior matters.

**Details and nuances**

For each pattern position, the prefix table records the length of the longest proper prefix matching a suffix ending there. During search, mismatch shortens the matched prefix using this table until comparison can resume or reach zero.

For pattern `ababaca`, the table encodes that a failed longer match can retain the already-known `aba`/related prefix structure. The text index never moves backward.

Define empty-pattern semantics and character units (bytes, code units or Unicode code points) at the API boundary. KMP compares a sequence; it does not solve Unicode normalization/case-folding by itself.

Primary reference: Knuth, Morris and Pratt, [“Fast Pattern Matching in Strings”](https://doi.org/10.1137/0206024).

[↑ Back to question index](#question-index)

---

## Question CPP-161

[↑ Back to question index](#question-index)

### Question CPP-161 — How do you merge overlapping intervals?

**Short answer**

- Sort intervals by start, then scan once: extend the current merged end when the next interval overlaps, otherwise emit and start a new interval.
- Complexity is O(n log n) for sorting plus O(n) scanning; already sorted input is linear.
- Define endpoint semantics first: closed `[a,b]`, half-open `[a,b)`, and whether touching intervals merge change the overlap condition.

**Details and nuances**

Validate/reorder malformed endpoints according to contract. Sort ties by start then end so containment behaves predictably.

For half-open intervals, `[1,3)` and `[3,5)` do not overlap but may still be coalesced if adjacency is considered equivalent. For closed integer intervals they share endpoint 3.

If intervals carry metadata, merging needs a business rule—unioning time spans does not define how owners/priorities/payloads combine. For many event points or concurrent counts, a sweep-line algorithm may be a better abstraction.

[↑ Back to question index](#question-index)

---

## Question CPP-162

[↑ Back to question index](#question-index)

### Question CPP-162 — What are prefix sums and difference arrays?

**Short answer**

- A prefix array stores cumulative values so a half-open range sum `[l,r)` is `prefix[r] - prefix[l]` after O(n) preprocessing.
- A difference array records range-update boundaries; after all O(1) updates, one prefix pass materializes the final values.
- They trade update/query patterns and require careful indexing, overflow sizing and static/offline assumptions.

**Details and nuances**

```cpp
std::vector<long long> prefix(values.size() + 1, 0);
for (std::size_t i = 0; i < values.size(); ++i) {
    prefix[i + 1] = prefix[i] + values[i];
}
auto range_sum = [&](std::size_t left, std::size_t right) {
    return prefix[right] - prefix[left]; // [left, right)
};
```

For adding `delta` to `[l,r)`, do `diff[l] += delta; diff[r] -= delta`, then cumulative-sum `diff`. Two-dimensional prefix sums use inclusion-exclusion for rectangle queries.

If updates and queries are interleaved online, use a Fenwick tree or segment tree rather than rebuilding a static prefix array.

[↑ Back to question index](#question-index)

---

## Question CPP-163

[↑ Back to question index](#question-index)

### Question CPP-163 — What are monotonic stacks and queues used for?

**Short answer**

- A monotonic stack keeps candidates in increasing/decreasing order for nearest-greater/smaller boundaries, histogram rectangles and span problems.
- A monotonic deque additionally removes expired front indices and dominated back candidates, giving sliding-window min/max in O(n).
- Each item is pushed and popped at most once; correctness depends on the precise dominance and equal-value policy.

**Details and nuances**

For sliding maximum, before inserting index `i`: remove front indices outside the window, then pop back indices whose values are no greater than `value[i]`; the front is the current maximum.

Store indices rather than values when expiration, distance or result positions matter. Decide whether equality pops the older or newer candidate—both can work but affect tie positions.

The O(n) proof is amortized: an individual iteration may pop many elements, but no element returns after being popped. These structures require an order-compatible domination rule; they are not general-purpose sorted containers.

[↑ Back to question index](#question-index)

---

# 14. UI Architecture

## Question CPP-164

[↑ Back to question index](#question-index)

### Question CPP-164 — MVC, MVP and MVVM - what actually differs?

**Short answer**

- All three separate what the application *knows* from what it *shows*; they differ in who mediates and in which direction the dependency runs.
- **MVC**: the controller handles input and updates the model; the view observes the model. **MVP**: the presenter sits between them and the view is passive, talking only to the presenter. **MVVM**: the view binds to a view model that exposes state as observable properties, so the view model never references the view at all.
- The property that matters in all three is the same: the model knows nothing about the UI, so the logic can be tested without instantiating a widget.

**Details and nuances**

| | Who handles input | What the view depends on | What the mediator depends on |
|---|---|---|---|
| MVC | Controller | Model (observes it) | Model |
| MVP | Presenter | Presenter interface | View interface, model |
| MVVM | View, via bindings | View model (binds to it) | Model only |

MVVM needs a binding mechanism to be worth the name - in Qt that is the property system, signals and slots, or Model/View with roles. Without bindings you have written MVP and called it MVVM, which is fine as long as nobody is being misled.

The reason the honest label is usually "MVVM-style" rather than MVVM: real applications have a view model that is not purely declarative, a view that occasionally holds state, and a controller layer that does not fit the diagram. Claiming the pure pattern invites a question the code cannot answer.

**Example or evidence boundary**

Production experience: refactoring a C++17/Qt Widgets SIP call flow into an MVC/MVVM-style architecture, separating UI state, user actions, media negotiation and SDK integration. I describe it as MVC/MVVM-*style* deliberately - it is that shape, not a textbook implementation.

[↑ Back to question index](#question-index)

---

## Question CPP-165

[↑ Back to question index](#question-index)

### Question CPP-165 — What belongs in a view model, and what must not?

**Short answer**

- In: the state the UI displays, in the form the UI needs it - already formatted, already filtered, already ordered - plus the commands the UI can invoke and whether each is currently enabled.
- Out: any reference to a widget, any framework type that only exists to draw something, and any business rule that would still be true in a command-line version of the program.
- The test is whether the view model can be constructed and exercised in a unit test with no UI at all. If it cannot, the separation is nominal.

**Details and nuances**

The boundary that is easiest to get wrong is formatting versus meaning. "This call is on hold" is model state; "the hold button shows a resumed icon and is enabled" is view-model state; the pixel is the view. Putting the second in the model spreads UI concerns into the domain, and putting it in the view spreads logic into the part that cannot be tested.

The second is the enabled/disabled decision: it is genuinely logic - it depends on state and on what operations are legal right now - so it belongs in the view model, not in a slot that inspects three widgets to decide.

Where asynchrony is involved, one rule prevents most of the bugs: the view model owns the current state, events carry what they were computed from, and anything stale is dropped rather than applied. That is the same rule as [COM-041](<./COM and Excel Questions.md#question-com-041>), arrived at from the UI side.

[↑ Back to question index](#question-index)

---

## Question CPP-166

[↑ Back to question index](#question-index)

### Question CPP-166 — How do you refactor a fat UI class into that shape without stopping delivery?

**Short answer**

- Not all at once. Pick the state that causes the most bugs - usually whatever several widgets read and write - and move that out first, leaving everything else where it is.
- Extract state before extracting behaviour: once the state has one owner and the widgets read it rather than each holding a copy, most of the inconsistency bugs stop even before the logic moves.
- Keep each step shippable and reviewable, and expect the result to be a mixture for a long time - a half-refactored class that works beats a complete design that was never finished.

**Details and nuances**

The order matters because state duplication is the actual defect source. A dialog that caches what it thinks the connection state is, alongside a status bar with its own copy, produces disagreement the moment an event arrives out of order - and no amount of tidy layering fixes that if both copies remain.

What makes the steps safe is that each one is behaviour-preserving and small enough to review on its own, so a reviewer can disagree about one extraction rather than about the whole redesign. That is also what makes it possible to stop half way when priorities change, without leaving the codebase worse than it started.

The risk to name honestly if asked: a large refactor near a release is a real hazard, and the mitigation is the size of the steps plus tests around the extracted state rather than confidence.

**Example or evidence boundary**

Production experience: a late-project call-architecture refactor introducing explicit session, service, adapter and presentation boundaries, completed as internal engineering work with a large migration surface near the end of the project - which is exactly the risk described above.

[↑ Back to question index](#question-index)

