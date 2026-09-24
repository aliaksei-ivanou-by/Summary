# General C++ Technical Interview Questions and Answers

> Reusable C++ question bank: language and build model, object lifetime, STL, concurrency, systems, performance, debugging and design. Vacancy-specific files should link here instead of copying these answers.

# Question Index

> **C-specific questions are in a separate bank**: [C Language Questions](<./C Language Questions.md>) - standards and C89, undefined behaviour, memory sections, alignment and packing, flexible array members, strings, function pointers, opaque structs, the preprocessor, static and shared libraries, `dlopen`/`LD_PRELOAD`, core dumps, Valgrind and sanitizers. This bank is C++.

Questions use stable topic-specific IDs. Every answer begins with a short bullet summary and keeps details, examples and edge cases below it.

## Modern C++ (CPP-001–CPP-020)

|  |  |  |
|---|---|---|
| [CPP-001. What are the Rule of Zero, Three, Five, and the so-called Seven/Ten variants?](#question-cpp-001) | [CPP-002. What does `std::move` actually do?](#question-cpp-002) | [CPP-003. What is the difference between lvalue and rvalue?](#question-cpp-003) |
| [CPP-004. What are prvalue, xvalue and glvalue?](#question-cpp-004) | [CPP-005. What is a forwarding reference?](#question-cpp-005) | [CPP-006. What is `std::forward` used for?](#question-cpp-006) |
| [CPP-007. Why should move constructors often be `noexcept`?](#question-cpp-007) | [CPP-008. When does the compiler generate a move constructor?](#question-cpp-008) | [CPP-009. What is copy elision?](#question-cpp-009) |
| [CPP-010. Why can `const T&` bind to a temporary?](#question-cpp-010) | [CPP-011. When does lifetime extension not work?](#question-cpp-011) | [CPP-012. What is the difference between `auto` and `decltype(auto)`?](#question-cpp-012) |
| [CPP-013. What is `decltype`?](#question-cpp-013) | [CPP-014. What is `explicit` used for?](#question-cpp-014) | [CPP-015. What do `= default` and `= delete` mean?](#question-cpp-015) |
| [CPP-016. What is undefined behavior?](#question-cpp-016) | [CPP-017. Undefined vs unspecified vs implementation-defined behavior?](#question-cpp-017) | [CPP-018. What is the as-if rule?](#question-cpp-018) |
| [CPP-019. What is `volatile` for?](#question-cpp-019) | [CPP-020. Can you add things to namespace `std`?](#question-cpp-020) |  |

## Object Model and OOP (CPP-021–CPP-028)

|  |  |  |
|---|---|---|
| [CPP-021. Why does a base class often need a virtual destructor?](#question-cpp-021) | [CPP-022. How do virtual functions usually work?](#question-cpp-022) | [CPP-023. Is there a vtable per object?](#question-cpp-023) |
| [CPP-024. What happens when calling a virtual function from a constructor or destructor?](#question-cpp-024) | [CPP-025. What is object slicing?](#question-cpp-025) | [CPP-026. Overloading vs overriding?](#question-cpp-026) |
| [CPP-027. What is virtual inheritance?](#question-cpp-027) | [CPP-028. What determines the size of a C++ class?](#question-cpp-028) |  |

## RAII and Smart Pointers (CPP-029–CPP-038)

|  |  |  |
|---|---|---|
| [CPP-029. What is RAII?](#question-cpp-029) | [CPP-030. How does `std::unique_ptr` work?](#question-cpp-030) | [CPP-031. How should `unique_ptr` be passed to a function?](#question-cpp-031) |
| [CPP-032. How does `shared_ptr` work?](#question-cpp-032) | [CPP-033. What is the difference between `make_shared<T>()` and `shared_ptr<T>(new T)`?](#question-cpp-033) | [CPP-034. Why is constructing two `shared_ptr`s from the same raw pointer dangerous?](#question-cpp-034) |
| [CPP-035. Why are cyclic `shared_ptr` references a problem?](#question-cpp-035) | [CPP-036. What does `weak_ptr::lock()` do?](#question-cpp-036) | [CPP-037. What is `enable_shared_from_this`?](#question-cpp-037) |
| [CPP-038. Is `shared_ptr` thread-safe?](#question-cpp-038) |  |  |

## STL and Data Structures (CPP-039–CPP-051)

|  |  |  |
|---|---|---|
| [CPP-039. How does `std::vector` work?](#question-cpp-039) | [CPP-040. What is the difference between `size()` and `capacity()`?](#question-cpp-040) | [CPP-041. `reserve()` vs `resize()`?](#question-cpp-041) |
| [CPP-042. When are vector iterators invalidated?](#question-cpp-042) | [CPP-043. Why is `push_back` amortized O(1)?](#question-cpp-043) | [CPP-044. Why is `vector` often faster than `list`?](#question-cpp-044) |
| [CPP-045. `map` vs `unordered_map`?](#question-cpp-045) | [CPP-046. Why can `unordered_map` become O(n)?](#question-cpp-046) | [CPP-047. What is rehashing?](#question-cpp-047) |
| [CPP-048. What is strict weak ordering?](#question-cpp-048) | [CPP-049. `push_back` vs `emplace_back`?](#question-cpp-049) | [CPP-050. What is `std::string_view`?](#question-cpp-050) |
| [CPP-051. What is `std::span`?](#question-cpp-051) |  |  |

## Multithreading and Memory Model (CPP-052–CPP-070)

|  |  |  |
|---|---|---|
| [CPP-052. What is a data race?](#question-cpp-052) | [CPP-053. Race condition vs data race?](#question-cpp-053) | [CPP-054. What does a mutex provide?](#question-cpp-054) |
| [CPP-055. `lock_guard` vs `unique_lock`?](#question-cpp-055) | [CPP-056. What is `scoped_lock`?](#question-cpp-056) | [CPP-057. What causes deadlock?](#question-cpp-057) |
| [CPP-058. What is a condition variable?](#question-cpp-058) | [CPP-059. Why must condition variables use a predicate?](#question-cpp-059) | [CPP-060. What is `std::atomic`?](#question-cpp-060) |
| [CPP-061. Atomic vs mutex?](#question-cpp-061) | [CPP-062. What is compare-and-swap?](#question-cpp-062) | [CPP-063. `compare_exchange_weak` vs `strong`?](#question-cpp-063) |
| [CPP-064. What is `memory_order_relaxed`?](#question-cpp-064) | [CPP-065. What are acquire and release semantics?](#question-cpp-065) | [CPP-066. What is happens-before?](#question-cpp-066) |
| [CPP-067. What is sequential consistency?](#question-cpp-067) | [CPP-068. What is false sharing?](#question-cpp-068) | [CPP-069. How would you implement producer-consumer?](#question-cpp-069) |
| [CPP-070. How would you make a thread-safe queue?](#question-cpp-070) |  |  |

## Performance (CPP-071–CPP-079)

|  |  |  |
|---|---|---|
| [CPP-071. How do you investigate a performance problem?](#question-cpp-071) | [CPP-072. Latency vs throughput?](#question-cpp-072) | [CPP-073. What are P50, P95 and P99?](#question-cpp-073) |
| [CPP-074. What is batching?](#question-cpp-074) | [CPP-075. What is throttling?](#question-cpp-075) | [CPP-076. What is coalescing?](#question-cpp-076) |
| [CPP-077. What is backpressure?](#question-cpp-077) | [CPP-078. Why can allocation be expensive?](#question-cpp-078) | [CPP-079. How do you reduce allocation overhead?](#question-cpp-079) |

## Windows and Debugging (CPP-080–CPP-085)

|  |  |  |
|---|---|---|
| [CPP-080. Static vs dynamic library?](#question-cpp-080) | [CPP-081. What are `LoadLibrary` and `GetProcAddress`?](#question-cpp-081) | [CPP-082. Why is `DllMain` dangerous for complex work?](#question-cpp-082) |
| [CPP-083. How would you debug a host-process crash caused by a native plug-in?](#question-cpp-083) | [CPP-084. How would you investigate a deadlock?](#question-cpp-084) | [CPP-085. How would you debug a bug that appears only after several hours?](#question-cpp-085) |

## Rapid-Fire C++ (CPP-086–CPP-096)

|  |  |  |
|---|---|---|
| [CPP-086. `new` vs `malloc`?](#question-cpp-086) | [CPP-087. `delete` vs `delete[]`?](#question-cpp-087) | [CPP-088. What is placement new?](#question-cpp-088) |
| [CPP-089. What is `std::terminate`?](#question-cpp-089) | [CPP-090. What is stack unwinding?](#question-cpp-090) | [CPP-091. Strong exception guarantee?](#question-cpp-091) |
| [CPP-092. What is copy-and-swap?](#question-cpp-092) | [CPP-093. What is ODR?](#question-cpp-093) | [CPP-094. What does `inline` really mean?](#question-cpp-094) |
| [CPP-095. What is ABI?](#question-cpp-095) | [CPP-096. What is name mangling?](#question-cpp-096) |  |

## Build Model, Language Details and Templates (CPP-097–CPP-114)

|  |  |  |
|---|---|---|
| [CPP-097. What is a translation unit, and how does C++ source become an executable?](#question-cpp-097) | [CPP-098. What belongs in a header, and include guards vs `#pragma once`?](#question-cpp-098) | [CPP-099. Forward declaration vs `#include`?](#question-cpp-099) |
| [CPP-100. How do C/C++ macros work, and what are the common traps?](#question-cpp-100) | [CPP-101. Declaration vs definition?](#question-cpp-101) | [CPP-102. What are internal, external and no linkage?](#question-cpp-102) |
| [CPP-103. What should you know about fundamental types, `nullptr` and `std::byte`?](#question-cpp-103) | [CPP-104. What are integer promotions and usual arithmetic conversions?](#question-cpp-104) | [CPP-105. `enum` vs `enum class`?](#question-cpp-105) |
| [CPP-106. How does `const` work with pointers and member functions?](#question-cpp-106) | [CPP-107. When should each C++ cast be used?](#question-cpp-107) | [CPP-108. What initialization forms exist, and why use braces carefully?](#question-cpp-108) |
| [CPP-109. `constexpr` vs `consteval` vs `constinit`?](#question-cpp-109) | [CPP-110. How do lambda captures work, and what can dangle?](#question-cpp-110) | [CPP-111. How do template instantiation and specialization work?](#question-cpp-111) |
| [CPP-112. What are variadic templates and fold expressions?](#question-cpp-112) | [CPP-113. SFINAE vs concepts and `requires`?](#question-cpp-113) | [CPP-114. Which C++20/C++23 features are most relevant in production?](#question-cpp-114) |

## STL and Concurrency Extensions (CPP-115–CPP-128)

|  |  |  |
|---|---|---|
| [CPP-115. How do you choose between `vector`, `deque`, `list` and `forward_list`?](#question-cpp-115) | [CPP-116. How do `map`/`set` differ from their `multi` variants?](#question-cpp-116) | [CPP-117. What contract must a hash function and equality predicate satisfy?](#question-cpp-117) |
| [CPP-118. What are iterator categories and why do they matter?](#question-cpp-118) | [CPP-119. Why prefer STL algorithms and ranges to handwritten loops?](#question-cpp-119) | [CPP-120. What are the erase-remove idiom and `std::erase_if`?](#question-cpp-120) |
| [CPP-121. `lower_bound` vs `upper_bound` vs `equal_range`?](#question-cpp-121) | [CPP-122. What is `std::optional`, and when should it not be used?](#question-cpp-122) | [CPP-123. `std::variant` vs `std::any`?](#question-cpp-123) |
| [CPP-124. When should you use `pair`, `tuple` and structured bindings?](#question-cpp-124) | [CPP-125. How should `std::chrono` be used?](#question-cpp-125) | [CPP-126. `std::thread`: `join`/`detach` vs `std::jthread`?](#question-cpp-126) |
| [CPP-127. How do `future`, `promise` and `async` work?](#question-cpp-127) | [CPP-128. When is `shared_mutex` useful?](#question-cpp-128) |  |

## Systems and Networking Foundations (CPP-129–CPP-139)

|  |  |  |
|---|---|---|
| [CPP-129. Process vs thread, and what is a context switch?](#question-cpp-129) | [CPP-130. What are a call stack and a stack frame?](#question-cpp-130) | [CPP-131. How do virtual memory, page faults and the TLB relate?](#question-cpp-131) |
| [CPP-132. How do cache hierarchy, alignment and padding affect performance?](#question-cpp-132) | [CPP-133. What is endianness?](#question-cpp-133) | [CPP-134. System calls vs interrupts, CPU exceptions and OS signals?](#question-cpp-134) |
| [CPP-135. What IPC mechanisms would you choose between?](#question-cpp-135) | [CPP-136. TCP vs UDP, and why does TCP need message framing?](#question-cpp-136) | [CPP-137. What is the socket lifecycle, and how do I/O multiplexers help?](#question-cpp-137) |
| [CPP-138. HTTP vs HTTPS vs WebSocket?](#question-cpp-138) | [CPP-139. Livelock and starvation vs deadlock?](#question-cpp-139) |  |

## Software Design and APIs (CPP-140–CPP-149)

|  |  |  |
|---|---|---|
| [CPP-140. What do the SOLID principles mean in practice?](#question-cpp-140) | [CPP-141. How do DRY, KISS and YAGNI complement each other?](#question-cpp-141) | [CPP-142. Why prefer composition over inheritance?](#question-cpp-142) |
| [CPP-143. How do PImpl, header-only and compiled libraries trade off?](#question-cpp-143) | [CPP-144. How do common GoF patterns differ?](#question-cpp-144) | [CPP-145. Observer vs pub-sub and event-driven architecture?](#question-cpp-145) |
| [CPP-146. Exceptions vs error codes vs `std::expected`?](#question-cpp-146) | [CPP-147. How do you evolve an API without breaking source or binary compatibility?](#question-cpp-147) | [CPP-148. What does ACID mean?](#question-cpp-148) |
| [CPP-149. How does an LRU cache work?](#question-cpp-149) |  |  |

## Algorithms and Interview Patterns (CPP-150–CPP-163)

|  |  |  |
|---|---|---|
| [CPP-150. How does Floyd's tortoise-and-hare cycle detection work?](#question-cpp-150) | [CPP-151. How does Brent's cycle-detection algorithm differ from Floyd's?](#question-cpp-151) | [CPP-152. Two pointers vs sliding window?](#question-cpp-152) |
| [CPP-153. How do you write binary search with a correct invariant?](#question-cpp-153) | [CPP-154. BFS vs DFS?](#question-cpp-154) | [CPP-155. How does Dijkstra's shortest-path algorithm work?](#question-cpp-155) |
| [CPP-156. How does topological sorting work, and how does it detect a cycle?](#question-cpp-156) | [CPP-157. How does Union-Find / Disjoint Set Union work?](#question-cpp-157) | [CPP-158. How do heaps solve priority and top-K problems?](#question-cpp-158) |
| [CPP-159. How does Kadane's maximum-subarray algorithm work?](#question-cpp-159) | [CPP-160. How does Knuth-Morris-Pratt string search work?](#question-cpp-160) | [CPP-161. How do you merge overlapping intervals?](#question-cpp-161) |
| [CPP-162. What are prefix sums and difference arrays?](#question-cpp-162) | [CPP-163. What are monotonic stacks and queues used for?](#question-cpp-163) |  |

## UI Architecture (CPP-164–CPP-166)

|  |  |  |
|---|---|---|
| [CPP-164. MVC, MVP and MVVM - what actually differs?](#question-cpp-164) | [CPP-165. What belongs in a view model, and what must not?](#question-cpp-165) | [CPP-166. How do you refactor a fat UI class into that shape without stopping delivery?](#question-cpp-166) |

## OOP Principles and Polymorphism (CPP-167–CPP-170)

|  |  |  |
|---|---|---|
| [CPP-167. What are the principles of OOP, and what does each one actually buy you?](#question-cpp-167) | [CPP-168. IS-A vs HAS-A - how do you decide?](#question-cpp-168) | [CPP-169. What kinds of polymorphism does C++ have?](#question-cpp-169) |
| [CPP-170. Abstract class, pure virtual function - how do you model an interface in C++?](#question-cpp-170) |  |  |

## Traps That Pass Review (CPP-171–CPP-178)

|  |  |  |
|---|---|---|
| [CPP-171. Why is `std::vector<bool>` not a container of `bool`?](#question-cpp-171) | [CPP-172. What is the static initialization order fiasco, and what fixes it?](#question-cpp-172) | [CPP-173. In what order are function arguments evaluated, and why did that leak before C++17?](#question-cpp-173) |
| [CPP-174. What goes wrong when signed and unsigned meet in a comparison or a loop?](#question-cpp-174) | [CPP-175. What does `const` on a member function actually guarantee?](#question-cpp-175) | [CPP-176. What is a pure virtual call, and how do you get one?](#question-cpp-176) |
| [CPP-177. What happens when a constructor throws?](#question-cpp-177) | [CPP-178. What happens when a destructor throws?](#question-cpp-178) |  |

## Algorithms and Iterator Requirements (CPP-179–CPP-181)

|  |  |  |
|---|---|---|
| [CPP-179. Will `std::sort` work on a `std::vector`? On a `std::list`?](#question-cpp-179) | [CPP-180. Which sort does the standard library give you, and when do you need `stable_sort`, `partial_sort` or `nth_element`?](#question-cpp-180) | [CPP-181. Why do containers have their own `find` when `std::find` exists?](#question-cpp-181) |

## Lock-Free Concurrency (CPP-182–CPP-184)

|  |  |  |
|---|---|---|
| [CPP-182. What does lock-free actually mean?](#question-cpp-182) | [CPP-183. How would you build a single-producer single-consumer queue without locks?](#question-cpp-183) | [CPP-184. What are the ABA and reclamation problems?](#question-cpp-184) |

## Library Internals (CPP-185–CPP-189)

|  |  |  |
|---|---|---|
| [CPP-185. How does `std::string` store its data, and what is SSO?](#question-cpp-185) | [CPP-186. How does `std::function` work, and why can it allocate?](#question-cpp-186) | [CPP-187. What does `dynamic_cast` cost, and when should you use it?](#question-cpp-187) |
| [CPP-188. Implement `unique_ptr`.](#question-cpp-188) | [CPP-189. Implement `shared_ptr` - what does the control block hold?](#question-cpp-189) |  |

## Language Features Often Asked (CPP-190–CPP-194)

|  |  |  |
|---|---|---|
| [CPP-190. What are the rules for operator overloading, and what does `<=>` change?](#question-cpp-190) | [CPP-191. What is a lambda, really?](#question-cpp-191) | [CPP-192. What are type traits, and how does `if constexpr` change template code?](#question-cpp-192) |
| [CPP-193. What is CTAD, and when do you need a deduction guide?](#question-cpp-193) | [CPP-194. What is a C++20 coroutine, at the level you would be asked about it?](#question-cpp-194) |  |

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

Only `&& + &&` gives an rvalue reference; **an lvalue reference anywhere wins**. That single rule is what makes `T&&` bind to everything: pass an lvalue `int x` and `T` deduces to `int&`, so the parameter collapses to `int&`; pass `42` and `T` deduces to `int`, so the parameter stays `int&&`. Deducing `T` as a reference type happens *only* in this context—which is why the deduced `T` is not the argument's type and `std::forward<T>` needs `T` spelled explicitly.

**It is a syntactic pattern, not a type**, and the exceptions are the interview follow-up:

```cpp
template<class T> void f(T&& x);              // forwarding reference
template<class T> void g(std::vector<T>&& x); // NOT: T is not the whole parameter type
template<class T> void h(const T&& x);        // NOT: const disqualifies it
template<class T> struct S { void m(T&& x); };// NOT: T is the class's parameter, not deduced here
auto&& r = expr;                              // IS - auto follows the same deduction
```

**Why it matters:** it is the only way to write one overload that preserves constness *and* value category, instead of `f(T&)` + `f(const T&)` + `f(T&&)` growing as 2ⁿ in the number of parameters. It must be paired with `std::forward` inside ([CPP-006](#question-cpp-006)), because the named parameter is itself an lvalue.

**Two hazards to mention.** A forwarding-reference constructor is greedier than the copy constructor for a non-`const` lvalue of the same class, hijacking copies—constrain it with `requires`/`enable_if` on `std::decay_t<T>`. And a forwarded argument is *moved from* if the callee takes it by value, so forwarding the same parameter twice uses a moved-from object. In C++20, `template<class T> void f(T&&)` is spelled `void f(auto&&)`.

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

Without `std::forward`, the named variable `value` is always an lvalue inside the function—**anything with a name is an lvalue, including a parameter declared `T&&`**. So a wrapper that omits it silently turns every rvalue argument into a copy, and the bug is invisible: the code compiles and works, just slower.

**How it works** is a conditional cast, which is the one-line contrast with `std::move` ([CPP-002](#question-cpp-002)):

```cpp
template<class T> T&& forward(std::remove_reference_t<T>& t) noexcept
{ return static_cast<T&&>(t); }
```

If `T` deduced to `int&` (the caller passed an lvalue), `static_cast<int& &&>` collapses to `int&`—an lvalue comes out. If `T` deduced to `int`, the cast is to `int&&`—an rvalue comes out. Hence the mnemonic: **`move` casts unconditionally, `forward` casts conditionally, based on `T`**.

**The template argument is mandatory.** `std::forward(value)` without `<T>` fails to compile by design—the non-deduced parameter forces you to name the type that carries the value category. Writing `std::forward<decltype(value)>(value)` is the correct spelling in a generic lambda.

**Forward exactly once per argument.** Using it twice means the first call may have moved from the object:

```cpp
template<class T> void bad(T&& v) { a(std::forward<T>(v)); b(std::forward<T>(v)); } // b may get a husk
```

**Where you meet it in practice:** `make_unique`, `make_shared`, `emplace_back` and `std::thread`'s constructor are all perfect-forwarding factories ([CPP-049](#question-cpp-049)). "Perfect" is slightly overstated—forwarding does not preserve bit-fields or braced initialiser lists, and it cannot deduce `{1,2,3}` at all.

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

**The problem `decltype(auto)` exists to solve is the perfect-forwarding return type.** A wrapper that returns `auto` silently strips the reference, so this compiles and returns a dangling copy—or worse, breaks assignment through the result:

```cpp
template<class C, class I>
auto          get1(C& c, I i) { return c[i]; }   // returns a COPY: get1(v,0) = 5 won't compile
template<class C, class I>
decltype(auto) get2(C& c, I i) { return c[i]; }  // returns C::reference, as intended
```

**The trap is the extra parentheses**, and it is a standard interview follow-up:

```cpp
decltype(auto) f() { int x = 0; return  x;  }   // int  - fine
decltype(auto) g() { int x = 0; return (x); }   // int& - returns a reference to a local. Dangling.
```

`return x;` uses the declared type; `return (x);` is an expression, and an lvalue expression yields `T&` ([CPP-013](#question-cpp-013)).

**Summary of the three deducers:** `auto` uses template deduction—decays, drops references and top-level `const`; `auto&&` is a forwarding reference and preserves everything ([CPP-005](#question-cpp-005)); `decltype(auto)` uses `decltype` rules on the initialiser and preserves exactly. Use `auto` by default, `decltype(auto)` only for a generic return type or a generic variable that must mirror an expression's exact type.

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

uses expression category rules and gives `T&` for an lvalue.

**The full rule** for an expression `e` that is not a plain unparenthesized name or member access:

| value category of `e` | `decltype(e)` |
|---|---|
| lvalue | `T&` |
| xvalue (e.g. `std::move(x)`) | `T&&` |
| prvalue (e.g. `42`, `f()` by value) | `T` |

So `decltype(x)` is `int`, `decltype((x))` is `int&`, `decltype(std::move(x))` is `int&&`, and `decltype(x + 0)` is `int`.

**It is unevaluated**, like `sizeof`: the expression is type-checked but never run, so `decltype(f())` needs only a declaration of `f`, no definition, and no side effects occur. That is what makes `decltype(std::declval<T>().foo())` the standard idiom for "does `T` have `foo`"—`declval` produces a `T&&` in an unevaluated context without requiring `T` to be constructible.

**The main uses:**

```cpp
template<class T, class U>
auto add(T a, U b) -> decltype(a + b) { return a + b; }   // trailing return: a and b are in scope

template<class T>                                          // SFINAE / detection
auto has_size(T& t) -> decltype(t.size(), std::true_type{});
```

C++14's `decltype(auto)` covers most of the trailing-return cases ([CPP-012](#question-cpp-012)), and C++20 concepts cover most of the detection cases far more readably—but `decltype` is still what they are built on.

**Compared to `auto`:** `auto` deduces from an initialiser and decays; `decltype` reports an expression's exact type including reference and `const`. `decltype` also works with no initialiser at all, which is why it appears in member declarations and type traits.

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

Compilers may optimize under the assumption that UB never happens—and that is the part worth explaining, because it is why UB is not "it does something weird at that line".

**The compiler reasons backwards from the assumption.** Given

```cpp
void f(int* p) {
    *p = 1;              // if p were null this would be UB
    if (p) do_work();    // ...therefore p is not null...
}                        // ...therefore the branch is dead and the check is deleted
```

the null check disappears. The symptom then appears *before* the offending line, in code that looks unrelated. Similarly, signed overflow being UB is what lets `i + 1 > i` fold to `true`, and lets a loop with `int i` be assumed not to wrap so it can be vectorised. This is called time-travel UB, and it is why "it worked in debug" means nothing.

**Why the standard has UB at all:** it buys performance (no bounds checks, no overflow checks, aliasing-based optimisation) and portability of the *specification* (the standard need not describe every CPU's behaviour on a bad operation). The cost is that a single UB anywhere makes the whole program's behaviour unconstrained—the standard says "no requirements on the program", not "on that statement".

**Additions to the list worth naming:** strict-aliasing violations (reading an object through an unrelated type—`std::bit_cast` or `memcpy` is the legal route), uninitialised reads, invalid downcasts with `static_cast`, modifying a `const` object, returning nothing from a non-`void` function, and infinite loops with no side effects.

**How you actually defend against it**, which is the answer an interviewer is listening for: UBSan and ASan in CI, `-Werror` with a real warning set, `-fno-strict-aliasing` only as a stopgap on legacy code, `std::vector::at` or assertions at boundaries, `-D_GLIBCXX_ASSERTIONS` for debug container checks, and fuzzing for input-driven paths. Sanitizers catch it at the moment it happens, which is the only time the stack is still meaningful ([CPP-017](#question-cpp-017)).

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

| | Standard requires | Must be documented | Can differ per run |
|---|---|---|---|
| **Undefined** | nothing at all | no | yes — anything may happen |
| **Unspecified** | one of several valid behaviours | no | yes |
| **Implementation-defined** | one of several valid behaviours | **yes** | no — fixed for the implementation |

The practical difference: implementation-defined behaviour is something you can *look up and rely on* for your target; unspecified behaviour is something you may not rely on even after observing it, because the same compiler may choose differently in another build or optimisation level; undefined behaviour poisons the whole program ([CPP-016](#question-cpp-016)).

**Examples to have ready:**

- *Implementation-defined*: `sizeof(int)`, whether `char` is signed, the number of bits in a byte, the layout of `bool`, how `reinterpret_cast` between pointer types behaves, the underlying type of an unscoped enum.
- *Unspecified*: the order in which function arguments are evaluated; the value of a moved-from standard object; whether two identical string literals share storage; the amount `vector` grows by on reallocation.
- *Undefined*: signed overflow, null dereference, out-of-bounds access, data race, use after lifetime.

**A fourth category is worth naming because interviewers use it to separate candidates: *ill-formed, no diagnostic required* (IFNDR).** An ODR violation is the classic case—two different definitions of the same inline function across translation units ([CPP-093](#question-cpp-093)). The program is invalid, but no compiler or linker is obliged to tell you, so it behaves like UB that survives all your sanitizers.

**C++20 removed one famous item from this list**: signed integers are now guaranteed two's complement, so the *representation* is fixed—though signed **overflow** is still undefined, which surprises people.

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

The compiler may transform the program however it likes as long as the **observable behaviour** is preserved. Observable means I/O, volatile accesses and, in a threaded program, what other threads can legally see - not the instructions, not the order of operations, not whether your variable exists at all.

Concretely, the compiler may delete a local you never read, keep a value in a register instead of memory, reorder two independent stores, replace a loop with a constant, and elide a copy even when the copy constructor has side effects (copy elision is the one case where observable behaviour may legally change, and since C++17 it is mandatory in some contexts).

Two consequences that come up in interviews:

- **Undefined behaviour widens the rule.** If a program has UB, there is no defined observable behaviour to preserve, so the compiler may assume the UB path never happens and optimise on that assumption - which is why a signed-overflow check can be deleted entirely ([C-002](<./C Language Questions.md#question-c-002>)).
- **`volatile` is the opt-out for a single object**: every access must actually happen and may not be reordered relative to other volatile accesses. It says nothing about atomicity or about ordering against non-volatile memory, which is why it is not a threading tool ([CPP-019](#question-cpp-019)).

The practical version: reasoning about what the generated code does is only valid through the abstract machine. "It works in debug and breaks in release" is almost always a program that relied on something the as-if rule never promised.

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

**What `volatile` actually guarantees** is narrow: the compiler must not elide, duplicate or reorder accesses *to that object relative to other volatile accesses*, and must issue exactly the loads and stores you wrote. That is all. It does **not** emit memory barriers, does not make the operation atomic, and does not stop the *CPU* or other threads' caches from reordering anything.

So all three of the things people expect from it are absent:

```cpp
volatile int counter;
++counter;          // still load-modify-store: NOT atomic, still a data race
volatile bool ready;
ready = true;       // no release semantics: prior writes may still be invisible to another thread
```

A `volatile` flag used as a thread handshake is a data race by definition, and therefore undefined behaviour ([CPP-016](#question-cpp-016))—even though it usually appears to work, which is what keeps the myth alive. The myth comes from Java and C#, where `volatile` *does* imply acquire/release, and from MSVC, whose `/volatile:ms` mode adds those semantics as an extension (`/volatile:iso` is the standard behaviour and the right setting for portable code).

**What it is legitimately for:** memory-mapped hardware registers, where a read has a side effect and must actually be issued; a variable modified by a signal handler (`volatile std::sig_atomic_t`); and `setjmp`/`longjmp` locals. Embedded and driver work, essentially.

**In multithreaded C++ the replacement is `std::atomic`**, which gives atomicity *and* ordering ([CPP-060](#question-cpp-060), [CPP-065](#question-cpp-065)). `std::atomic<T>` with `memory_order_relaxed` is the closest correct analogue of what people wanted from `volatile`.

**`volatile` on member functions** and much of `volatile`'s use in compound expressions were deprecated in C++20 (P1152), precisely because the semantics were confusing and almost every use was wrong. Note also that `volatile` and `const` are orthogonal—`const volatile` is a meaningful combination for a read-only hardware register.

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

**What *is* allowed**, precisely: you may add a **full specialization of a class template** from `std` for a program-defined type, unless the standard forbids it for that particular template. `std::hash`, `std::numeric_limits`, `std::formatter` and (pre-C++20) `std::iterator_traits` are the ones you actually use. Partial specializations are allowed under the same conditions; adding *declarations*, new functions, new overloads, or anything at all to `std::chrono`, `std::literals` or the namespaces reserved for future standardisation is not.

```cpp
struct Point { int x, y; };

template<>                                    // legal: full specialization for a user type
struct std::hash<Point> {
    size_t operator()(const Point& p) const noexcept {
        return std::hash<int>{}(p.x) ^ (std::hash<int>{}(p.y) << 1);
    }
};
```

**Specializing a function template in `std` is explicitly not allowed since C++20** (P0551), which caught a lot of code that specialized `std::swap`. The correct mechanism was always ADL anyway:

```cpp
namespace mylib {
    struct Buf { /* ... */ };
    void swap(Buf& a, Buf& b) noexcept { /* ... */ }   // found by ADL
}

using std::swap;     // the two-step idiom: std::swap as fallback...
swap(a, b);          // ...ADL picks mylib::swap if it exists
```

C++20's `std::ranges::swap` is a customization point object that does this two-step for you, which is the modern pattern for all such hooks.

**Why the rule exists:** the standard library may add overloads, change signatures or rely on internal declarations in any release, so an addition of yours can collide silently. And violations are **IFNDR**—ill-formed, no diagnostic required—so nothing has to warn you; it may simply link to the wrong thing ([CPP-017](#question-cpp-017), [CPP-093](#question-cpp-093)).

**Related rule:** names beginning with an underscore followed by a capital, or containing a double underscore, are reserved to the implementation everywhere—so `_Foo` and `my__type` are yours to lose, not yours to use.

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

This is an implementation strategy, not something mandated exactly by the C++ standard—the standard specifies only that the *final overrider* is called; vtables are how every mainstream ABI achieves it.

**Cost of a virtual call**, which is what the question is really probing: load the `vptr` from the object, load the function address from a fixed offset in the table, call through it. Two dependent loads and an indirect branch. The arithmetic is cheap; what costs is that it is **opaque to the optimizer**—the callee is unknown, so it cannot be inlined, and losing inlining usually costs more than the indirection itself. Modern CPUs predict the indirect branch well when the dynamic type is stable at a call site, and badly when it alternates.

**Object-layout consequences:**

- One `vptr` per polymorphic object—8 bytes on a 64-bit ABI—so a class with two `int`s goes from 8 to 16 bytes the moment it gains a virtual function ([CPP-028](#question-cpp-028)).
- Multiple inheritance needs multiple vptrs and *thunks* that adjust `this` before dispatching.
- Virtual inheritance adds another indirection to reach the shared base ([CPP-027](#question-cpp-027)).
- The vtable also carries the RTTI pointer used by `dynamic_cast` and `typeid`.

**The rules that fall out of "the vptr is set by the constructor":** a virtual call from a constructor or destructor dispatches to the *current* class's version, because the vptr is updated as each level of construction completes—a pure virtual call there is an immediate crash. And a base class with virtual functions needs a virtual destructor, or `delete` through a base pointer is undefined behaviour.

**When it is elided:** `final` on a class or method lets the compiler devirtualize, as does calling on an object of known concrete type, or link-time optimization with full visibility. The alternatives when dispatch cost genuinely matters are CRTP (static polymorphism), `std::variant` + `std::visit` (closed set, no allocation), or a function-pointer table you control.

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

The usual layout: one vtable per *class*, one `vptr` per *object* (per polymorphic base subobject, so multiple inheritance gives more than one). So a class with virtual functions costs one pointer per instance regardless of how many virtual functions it has - which matters when you have millions of small objects, and not otherwise.

None of this is mandated. The standard describes behaviour, not implementation; vtables are simply what every mainstream compiler does. Saying that is worth a sentence, because the honest answer to "how do virtual functions work" is "here is how they are implemented in practice".

Two consequences worth naming:

- The `vptr` is set as each constructor runs, base first, which is exactly why dispatch during construction reaches the base override and not the derived one ([CPP-024](#question-cpp-024)).
- Because the `vptr` is part of the object, a class with virtual functions is not trivially copyable and cannot be safely `memcpy`-ed or sent over the wire as raw bytes.

`final` on a class or a method lets the compiler devirtualise when the dynamic type is known, which is the cheap way to get back the inlining a virtual call costs.

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

The derived-specific part is lost—and crucially, **so is the dynamic type**: `b`'s vptr is `Base`'s, so virtual calls on `b` resolve to `Base`'s overrides. The object is not a broken `Derived`; it is a genuine `Base` that was initialised from part of a `Derived`. There is no warning, no crash, and the class invariant may now be violated in a way nothing detects.

**Where it actually happens** is rarely the textbook line—it is parameters and containers:

```cpp
void log(Base b);                 // by value: every derived argument is sliced
std::vector<Base> v;
v.push_back(Derived{});           // sliced on insertion

void log(const Base& b);          // fixed
std::vector<std::unique_ptr<Base>> v;   // fixed
```

Assignment slices too, and worse: `base = derived;` copies only the base subobject into an existing `Base`, which is the same loss plus a partially-updated object.

**How to make it impossible** rather than remembering not to do it:

- Make the polymorphic base non-copyable: `Base(const Base&) = delete;` — then slicing does not compile.
- Or make the base abstract (a pure virtual, or a protected destructor), so a `Base` by value cannot exist at all.
- Or make copy/assignment `protected` in the base, so derived classes can still implement their own.
- Store polymorphic objects by pointer or reference; use a *clone* virtual function when you need value semantics.

**The C++ Core Guidelines version** (C.67) is "a polymorphic class should suppress public copy/move"—that is the answer to give, because it converts a silent runtime bug into a compile error. `-Wextra` gives `-Wsuggest-override` but not slicing; clang has `-Wpotentially-evaluated-expression` and clang-tidy has checks, but the language will not stop you.

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

| | Overloading | Overriding |
|---|---|---|
| Relationship | same scope, different signatures | base ↔ derived, **same** signature |
| `virtual` needed | no | yes on the base |
| Resolved | compile time, by overload resolution | run time, through the vtable |
| Return type | free | must match, or be covariant |

**Signature must match exactly**—parameter types, `const`, ref-qualifier, and (since C++20) the noexcept-ness is checked too. A mismatch does not error; it creates a *new* virtual function that hides nothing and overrides nothing:

```cpp
struct B { virtual void f(int) const; };
struct D : B { void f(int); };            // NOT an override: missing const. Silently compiles.
struct E : B { void f(int) const override; };  // compiler enforces the match
```

That silent failure is the entire reason `override` exists, and it is the one-word answer to "why use `override`".

**The third mechanism people conflate with these is *hiding*.** Declaring *any* `f` in a derived class hides *every* base `f`, overloads included:

```cpp
struct B { void g(int); void g(double); };
struct D : B { void g(int); };
D{}.g(3.14);        // calls D::g(int) - B::g(double) is hidden, not overloaded
struct E : B { using B::g; void g(int); };   // `using` brings the base overloads back in
```

Overload sets do not merge across scopes, so this bites in every class hierarchy sooner or later.

**Covariant return types** are the one permitted difference: an override may return `Derived*` where the base returns `Base*`, which is what makes a virtual `clone()` usable without a cast. Reference types work the same way; smart pointers do **not**—`unique_ptr<Derived>` is not covariant with `unique_ptr<Base>`.

**`final`** is the complement: on a method it forbids further overriding, on a class it forbids derivation, and both let the compiler devirtualize ([CPP-022](#question-cpp-022)).

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

With virtual inheritance, `B` and `C` share one `A` subobject—`class B : virtual public A`, written on `B` and `C`, not on `D`.

**What the duplicate actually breaks**: without it, `D` has `A::x` twice, so `d.x` is ambiguous and must be spelled `d.B::x`, and a `D*` cannot convert to `A*` without saying which one. Two independent copies of the base state also means an invariant held in `A` can disagree with itself.

**The rule that surprises everyone: the *most derived* class initialises the virtual base.** `D`'s constructor calls `A`'s constructor directly; the initialisers for `A` written in `B` and `C` are ignored. So a virtual base with no default constructor forces every most-derived class in the hierarchy to name it explicitly—and adding a virtual base to a library class breaks every derived class downstream. Construction order is also different: all virtual bases first, in depth-first left-to-right order, before any non-virtual base.

**The costs**, which is why this is a design question and not just a syntax question:

- The offset from a `D*` to its `A` subobject is not a compile-time constant, so access goes through a virtual-base-pointer or offset table—an extra indirection per access.
- Objects grow by that pointer per virtually-derived base.
- `static_cast` from `A*` back down to `D*` is **forbidden**; you must use `dynamic_cast`, which needs RTTI and is far more expensive.

**When it is actually right:** interface classes with no state—then there is no duplicated data to worry about and no constructor argument to thread through, and the cost is mostly theoretical. That is exactly how `std::basic_iostream` uses it to join `istream` and `ostream` over one `basic_ios`. For anything with state, composition is usually the better answer, and in modern code the diamond is more often a design smell than a requirement.

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

**Work through an example**, because that is what the interviewer will ask for:

```cpp
struct S {          // sizeof == 24 on a typical 64-bit ABI, not 13
    char  a;        // offset 0
                    // 7 bytes padding
    double b;       // offset 8  (alignof(double) == 8)
    int   c;        // offset 16
                    // 4 bytes tail padding, so alignof(S) == 8 divides sizeof(S)
};
struct T { double b; int c; char a; };   // sizeof == 16: same members, reordered
```

Two rules generate all of it: every member sits at an offset that is a multiple of its alignment, and `sizeof` is rounded up to a multiple of `alignof` so arrays stay aligned. **Ordering members from largest to smallest alignment** is therefore a free size win—and it matters for cache footprint, not just memory ([CPP-044](#question-cpp-044)).

**Other contributors:**

- A `vptr` costs one pointer per object the moment any function is virtual; multiple inheritance can mean more than one ([CPP-022](#question-cpp-022)).
- Virtual inheritance adds further per-object bookkeeping ([CPP-027](#question-cpp-027)).
- **Empty base optimization**: an empty base contributes 0 bytes, which is why `std::vector` can hold a stateless allocator for free and why policy classes are inherited rather than held. C++20's `[[no_unique_address]]` extends the same saving to *members*.
- Bit-fields pack within an allocation unit, with implementation-defined layout.
- `alignas` can raise alignment and therefore size; over-aligned types need `operator new` with an alignment argument (C++17).

**Check rather than guess:** `sizeof`, `alignof` and `offsetof` in a `static_assert`, or `-Wpadded` / clang's `-Xclang -fdump-record-layouts` / MSVC's `/d1reportSingleClassLayoutS` to see the actual layout. On a struct held in a million-element vector, this is a real memory and bandwidth decision.

**One caveat:** layout is ABI, not standard. `sizeof` is only guaranteed stable for a given compiler and target, so it must never be baked into a serialized format or a cross-compiler binary interface.

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

**It is a zero-overhead abstraction.** With the default deleter, `sizeof(unique_ptr<T>) == sizeof(T*)`: the deleter is an empty class held by empty-base optimization, so there is no space cost and, with inlining, no time cost versus a raw pointer plus a correct `delete`. That is the sentence that answers "why not just use a raw pointer".

**Non-copyable is the design, not a limitation.** The copy constructor is `= delete`, so exclusive ownership is enforced at compile time; `std::move` makes every transfer visible in the source. A moved-from `unique_ptr` is guaranteed null—one of the few move operations with a *specified* post-state ([CPP-003](#question-cpp-003)).

**The API worth knowing precisely:**

| | |
|---|---|
| `get()` | borrow the raw pointer, ownership unchanged |
| `release()` | give up ownership, return the pointer, **you** must delete it |
| `reset(p)` | delete the current object, take `p` |
| `operator bool` | non-null test |

`release()` versus `reset()` is a standard trap—`release` deletes nothing.

**The custom deleter is part of the type**, which has two consequences: `unique_ptr<T, D1>` and `unique_ptr<T, D2>` are different types, and a stateful deleter (a lambda, a function pointer) makes the object bigger than a pointer. This is the natural wrapper for C and OS handles:

```cpp
auto closer = [](FILE* f){ if (f) std::fclose(f); };
std::unique_ptr<FILE, decltype(closer)> file(std::fopen("x","r"), closer);
```

**The array form `unique_ptr<T[]>`** is a separate specialization: it calls `delete[]`, provides `operator[]`, and drops `operator*`/`->`. Use `std::make_unique<T[]>(n)`—but prefer `std::vector` unless you genuinely need a fixed-size buffer.

**Prefer `make_unique`** ([CPP-033](#question-cpp-033)): it is exception-safe in argument lists, it avoids writing `new`, and it value-initialises. Its one limitation is that it cannot take a custom deleter.

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

**The rule in one line: the parameter type should state the ownership contract, and nothing else.**

| Parameter | Contract | Caller writes |
|---|---|---|
| `unique_ptr<T> p` | **takes** ownership | `f(std::move(p))` — forced, so the transfer is visible |
| `const T&` / `T&` | uses the object, no ownership, never null | `f(*p)` |
| `T*` | uses the object, no ownership, **may be null** | `f(p.get())` |
| `unique_ptr<T>& p` | will **reseat** the caller's pointer | `f(p)` |
| `const unique_ptr<T>&` | almost always wrong — see below | |

**`const unique_ptr<T>&` is the one to call out.** It says "I will not take ownership and I will not reseat it", which is exactly what `const T&` says—but it additionally forces the caller to *have* a `unique_ptr`, so the function cannot be called with a stack object, a member, or a `shared_ptr`. It couples the callee to a storage decision that is none of its business.

**The same reasoning for `shared_ptr`:** take `shared_ptr<T>` by value only when the function genuinely extends the object's lifetime—storing it, or launching an async task that outlives the call. Taking `const shared_ptr<T>&` as a habit is the common C++ performance bug in this area, because a by-value copy is an atomic increment and decrement, and passing it down four layers that merely *read* the object pays that four times for nothing ([CPP-038](#question-cpp-038)).

**Returning is simple**: return `unique_ptr<T>` by value. It is a move, and guaranteed copy elision (C++17) means a returned prvalue is constructed in place—so a factory returning `unique_ptr` costs nothing over returning a raw pointer, and cannot leak.

**This is Core Guideline F.7**: "prefer `T*` or `T&` to a smart pointer parameter unless you need ownership semantics"—it keeps functions usable with any storage strategy and makes the ownership intent readable at the call site.

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

```text
shared_ptr ──┬──► control block { strong, weak, deleter, allocator }
             └──► T
```

**`shared_ptr` is two pointers wide**—one to the object, one to the control block—so it is twice the size of `unique_ptr` and cannot be passed in a single register. The separate object pointer is what makes the aliasing constructor possible: a `shared_ptr<Member>` can keep the *parent* alive while pointing at a subobject.

**The two counts do different jobs.** Strong reaching zero destroys the **object** (running the deleter); weak reaching zero—with strong already zero—frees the **control block**. The weak count is effectively strong-plus-one internally, which is why a `weak_ptr` never keeps the object alive but does keep the bookkeeping alive ([CPP-036](#question-cpp-036)).

**The counts are atomic; that is the cost.** Every copy is an atomic increment and every destruction an atomic decrement—tens of nanoseconds uncontended, far worse when several threads copy the same `shared_ptr` and the cache line ping-pongs between cores ([CPP-068](#question-cpp-068)). Hence: pass by `const&` or by raw pointer when you are only observing ([CPP-031](#question-cpp-031)).

**Type erasure lives in the control block.** The deleter and allocator are stored there, not in the type, so `shared_ptr<T>` with a custom deleter is still `shared_ptr<T>`—unlike `unique_ptr`. That is also why `shared_ptr<void>` can correctly destroy a `Derived`: the deleter captured the real type at construction. A consequence worth knowing: `shared_ptr` does **not** need a virtual destructor to delete a derived object through a base pointer, provided it was constructed from a `Derived*`.

**Two allocations by default**—object and control block—which `make_shared` collapses into one ([CPP-033](#question-cpp-033)).

**Use it only when ownership is genuinely shared.** `shared_ptr` as the default smart pointer is a design smell: it makes lifetime nondeterministic, invites cycles ([CPP-035](#question-cpp-035)), and costs atomics. Prefer `unique_ptr`, and reach for `shared_ptr` when several independent owners really do exist—caches, observers, async continuations.

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

A caveat: when `weak_ptr`s remain, memory containing the combined allocation may remain allocated even after the object itself is destroyed—the destructor has run, but the block cannot be freed until the last `weak_ptr` goes, so a large object plus a long-lived `weak_ptr` holds its whole footprint. With two separate allocations only the small control block lingers.

**The exception-safety argument is the one that is often the real answer.** Before C++17 this could leak:

```cpp
f(std::shared_ptr<T>(new T), g());     // if g() throws after `new T` but before the ctor, T leaks
f(std::make_shared<T>(), g());         // cannot leak
```

C++17 tightened argument evaluation so that the interleaving is no longer allowed, but `make_shared` still expresses the intent and leaves no raw `new` in sight.

**Performance**: one allocation instead of two, and the object sits next to its reference counts, so touching the object and touching the count share a cache line—which matters exactly where `shared_ptr` is hot ([CPP-032](#question-cpp-032)).

**When you cannot use `make_shared`:**

- you need a **custom deleter** (`shared_ptr<T>(p, del)`) or a specific allocator (`allocate_shared` covers the allocator case);
- you are adopting a pointer from a C API or a factory that already called `new`;
- the constructor is **private**—`make_shared` is not a friend, which is the usual reason a factory falls back to `new` or a pass-key idiom;
- you need `new (std::nothrow)` semantics;
- the object is large and long-lived `weak_ptr`s are expected, per the caveat above.

**`make_shared` also value-initialises**, so `make_shared<int>()` gives 0 where `shared_ptr<int>(new int)` gives garbage. And note the asymmetry: `make_unique` has none of these trade-offs, so for `unique_ptr` it is simply always preferred ([CPP-030](#question-cpp-030)).

[↑ Back to question index](#question-index)

---

## Question CPP-034

[↑ Back to question index](#question-index)

### Question CPP-034 — Why is constructing two `shared_ptr`s from the same raw pointer dangerous?

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

The `shared_ptr` constructor that takes a raw pointer *always allocates a new control block*. It has no way not to: the raw pointer carries no back-reference to an existing one. So `a` and `b` end up with two independent reference counts, each at 1, each convinced it is the sole owner group.

```text
a ──► control block #1 (use_count = 1) ──┐
                                          ├──► the same T
b ──► control block #2 (use_count = 1) ──┘
```

When `a` goes out of scope its count drops to zero and it deletes the object. `b`'s count is still 1 and knows nothing about it—`b` is now a dangling pointer, and when it expires it deletes the same memory again. Double-free: heap corruption in a release build, and typically a crash somewhere else entirely, long after the real fault.

**Sharing ownership requires copying an existing `shared_ptr`**, because only the copy constructor participates in the existing control block. `make_shared` makes the mistake hard to express in the first place—there is no raw pointer to reuse, and it allocates object and control block together, which is one allocation instead of two and better locality ([CPP-035](#question-cpp-035)).

**The same bug wearing a disguise is `shared_ptr<T>(this)`.** A member function that needs to hand out ownership of itself must derive from `std::enable_shared_from_this<T>` and call `shared_from_this()`, which reuses the control block the first owner created. Two caveats: it throws `std::bad_weak_ptr` if the object is not already owned by a `shared_ptr` (so never call it from the constructor), and since C++17 the standard is explicit that the owning `shared_ptr` initialises the internal weak reference.

**Related trap:** `weak_ptr` is tied to a control block, not to an address. Two `shared_ptr`s built from the same raw pointer therefore also give you two disjoint sets of weak pointers, and `lock()` on one can succeed while the object is already destroyed by the other ([CPP-036](#question-cpp-036)).

The defensible rule for review: a raw `new` should appear at most once, immediately inside a `make_*` or a single smart-pointer constructor, and never be stored in a variable that outlives that statement.

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

It is an **atomic check-and-promote**: it examines the strong count and, if it is non-zero, increments it and hands back a `shared_ptr` - all in one indivisible step.

That atomicity is the whole point, and it is why you cannot write it yourself as `if (!wp.expired()) auto sp = /* ... */`. Between the check and the use, another thread can drop the last strong reference and the object is gone. `expired()` is therefore only good for a hint or a log line; `lock()` is the only safe way to use the object.

```cpp
if (auto sp = wp.lock()) {      // one step: checked and owned
    sp->use();                  // guaranteed alive for this scope
}                               // released here
```

The returned `shared_ptr` keeps the object alive for as long as you hold it, which is exactly what makes the pattern safe in a callback: the observer stores a `weak_ptr`, locks it when the event arrives, and does nothing if the subject has gone. That is the standard answer to the dangling-callback problem ([CPP-035](#question-cpp-035)).

Cost: an atomic compare-and-swap loop on the strong count, so it is not free in a hot loop - lock once outside the loop rather than per iteration.

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

would create another unrelated control block and can cause double deletion ([CPP-034](#question-cpp-034)).

```cpp
class Session : public std::enable_shared_from_this<Session> {
public:
    void start() {
        socket.async_read([self = shared_from_this()](auto ec, auto n) { self->on_read(ec, n); });
    }                    // the lambda keeps `this` alive until the callback runs
};
```

That is the canonical use: an asynchronous operation must keep its owner alive for the duration of the call, and there is no other way to get a `shared_ptr` from inside a member function.

**How it works:** the base holds a `weak_ptr<T>`. When the *first* `shared_ptr` takes ownership of the object, the `shared_ptr` constructor detects the base and initialises that weak member with its own control block. `shared_from_this()` then just does `weak.lock()`.

**Which explains the two rules that trip people up:**

- **Never call it in the constructor.** No `shared_ptr` owns the object yet, the weak member is empty, and since C++17 it throws `std::bad_weak_ptr` (before C++17 it was undefined behaviour). The idiom is a private constructor plus a static `create()` that returns `shared_ptr` and does post-construction work afterwards.
- **The object must already be owned by a `shared_ptr`.** A stack object, or one held by `unique_ptr`, will throw. So inheriting from `enable_shared_from_this` is a public statement that instances of this class are always heap-allocated and shared.

**Inherit publicly and exactly once.** Private inheritance makes the `shared_ptr` constructor unable to see the base, so it silently never enables the mechanism—and `shared_from_this()` then throws at runtime. Two `enable_shared_from_this` bases in one hierarchy is ambiguous.

**C++17 added `weak_from_this()`**, which returns an empty `weak_ptr` instead of throwing when the object is not owned—useful when a callback may legitimately fire after the owner is gone.

**Watch for cycles**: a captured `shared_ptr` to self inside a member of self is a cycle; capture `weak_from_this()` and `lock()` in the callback when the operation should *not* extend lifetime ([CPP-035](#question-cpp-035)).

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

**Three separate things get confused here; separate them explicitly:**

1. **The control block** — thread-safe. Reference counts are atomic, so copying and destroying `shared_ptr` *instances* from multiple threads is safe by design.
2. **The pointed-to object** — not thread-safe. `shared_ptr` says nothing about it; you need a mutex or atomics as you would for any shared object ([CPP-054](#question-cpp-054)).
3. **A single `shared_ptr` *instance*** — **not** thread-safe. Two threads writing the same `shared_ptr` variable, or one writing while another reads it, is a data race:

```cpp
std::shared_ptr<T> g;
// thread A: g = make_shared<T>();     // writes both members of g
// thread B: auto local = g;           // reads both members - RACE
```

Point 3 is the one candidates miss. The pointer and the control-block pointer are two words updated non-atomically, so a reader can see a torn pair: the new object with the old control block.

**How to share a `shared_ptr` variable safely:**

```cpp
std::atomic<std::shared_ptr<T>> g;     // C++20: proper atomic specialization
auto local = g.load();                 // safe snapshot
g.store(std::make_shared<T>(next));    // safe publish
```

Before C++20 the route was the free functions `std::atomic_load(&g)` / `std::atomic_store(&g, p)`, deprecated in C++20 and removed in C++26. A mutex around the variable also works and is often simpler.

**The idiom that follows**: copy the `shared_ptr` into a local once, then use the local. That makes the reference count guarantee do its job—the object cannot die under you—while avoiding repeated atomic traffic ([CPP-032](#question-cpp-032)).

**`weak_ptr::lock()` is atomic** with respect to the object's destruction, so it is the correct way to test-and-acquire from another thread ([CPP-036](#question-cpp-036)). Checking `expired()` and then locking is a race.

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

**Three pointers, in practice**: `begin`, `end`, `end_of_capacity`—so `sizeof(vector)` is 24 bytes on a 64-bit ABI and `size()`/`capacity()` are subtractions.

**"Moves or copies" hides an important rule.** Reallocation uses `std::move_if_noexcept`: elements are moved only if the move constructor is `noexcept` (or the type is not copyable). Otherwise they are **copied**, because a throwing move halfway through reallocation would leave both buffers in a broken state and the strong guarantee could not be honoured ([CPP-091](#question-cpp-091)). This is the concrete reason to mark move constructors `noexcept`—forget it and your vector of a heavy type silently copies on every growth.

**Growth is geometric**—×2 in libstdc++ and MSVC uses ×1.5—which is what makes `push_back` amortized O(1) ([CPP-043](#question-cpp-043)). The factor is unspecified, so never rely on it.

**Invalidation, which is the practical half of the question:**

| Operation | Invalidates |
|---|---|
| reallocation (any growth past capacity) | **everything** — iterators, pointers, references |
| `insert`/`erase` in the middle | everything from that position onward |
| `push_back` within capacity | `end()` only |
| `reserve`, `shrink_to_fit` | everything, if capacity changes |

The classic bug is holding a reference or iterator across a `push_back`—including the self-referential `v.push_back(v[0])`, which the standard requires to work, so implementations handle it explicitly.

**Capacity is never reduced automatically.** `clear()` leaves capacity intact; `shrink_to_fit` is a non-binding request; the guaranteed way to release memory is `vector<T>(v).swap(v)`.

**`vector<bool>` is the famous exception**: a bit-packed specialization whose `operator[]` returns a proxy, not a `bool&`, so `auto&` on it misbehaves and it is not a real container. Use `std::vector<char>`, `std::deque<bool>` or `std::bitset` when you need one.

**`reserve` vs `resize`**: `reserve` allocates capacity only—no objects are constructed and `size()` is unchanged. `resize` changes `size()` and value-initialises (or destroys) elements. Reserving before a known-size fill removes every intermediate reallocation, which is the single cheapest vector optimisation there is.

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

The gap between them is raw storage: allocated, uninitialised, and not yet holding objects. That is why `v[i]` for `i >= size()` is undefined behaviour even when `i < capacity()` ([CPP-041](#question-cpp-041)).

Growth is geometric - typically 1.5x or 2x depending on the implementation - which is what makes `push_back` amortised O(1) ([CPP-043](#question-cpp-043)). The two factors trade differently: 2x is simpler, 1.5x can reuse previously freed blocks, and neither is mandated by the standard.

Three operations that get confused:

| Call | `size()` | `capacity()` | Elements |
|---|---|---|---|
| `clear()` | 0 | unchanged | destroyed |
| `shrink_to_fit()` | unchanged | *may* drop to `size()` - non-binding | unchanged |
| `std::vector<T>(v).swap(v)` | unchanged | drops to `size()` | copied once |

The swap idiom is the only guaranteed way to release capacity, because `shrink_to_fit` is a request the implementation may ignore - and it can reallocate, so it invalidates iterators like any other reallocation.

`capacity()` is also why a vector that grew to a million elements and was then cleared still holds the memory: `clear()` destroys objects, it does not deallocate.

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

The question underneath is usually "does it construct anything", and the answer is the whole distinction:

| | `reserve(n)` | `resize(n)` |
|---|---|---|
| Constructs elements | **No** - allocates raw storage only | **Yes** when growing; value-initialises them |
| `size()` after | unchanged | `n` |
| `capacity()` after | `>= n` | `>= n`, never reduced when shrinking |
| Shrinking | does nothing - `reserve` never reduces capacity | destroys the trailing elements |
| Requires of `T` | nothing | default-insertable (or copy-insertable for `resize(n, v)`) |

**The trap that follows directly:** after `v.reserve(10)` the memory exists and the elements do not, so `v[0] = 1;` is undefined behaviour - `operator[]` is only valid below `size()`. It will usually appear to work, which is what makes it dangerous. Use `push_back`/`emplace_back` after `reserve`, or use `resize` if you actually want `n` elements to exist.

**Value-initialisation is stronger than people expect.** `std::vector<int> v; v.resize(5);` gives five zeros, not five indeterminate values - which also means `resize` on a large vector of a trivial type still costs a write over the whole range, so it is not free the way `reserve` is.

**Both can reallocate, and reallocation invalidates everything** - all iterators, pointers and references into the vector. That is the reason to `reserve` before a loop that takes addresses of elements, and the reason a saved iterator across a `push_back` is a bug ([CPP-042](#question-cpp-042)).

Two more that tend to be the follow-up:

- Whether reallocation *moves* or *copies* the existing elements depends on `T`'s move constructor being `noexcept`, because `vector` must preserve the strong exception guarantee ([CPP-007](#question-cpp-007), [CPP-091](#question-cpp-091)).
- `resize` down destroys elements but keeps the capacity; `shrink_to_fit()` is a non-binding request to release it, and the guaranteed way is the swap idiom `std::vector<T>(v).swap(v)`.

And the constructor is a third thing again: `std::vector<T> v(n)` constructs `n` elements, while `v.reserve(n)` on an empty vector constructs none - the same `n`, three different meanings.

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

**The arithmetic, since the question invites it.** With growth factor 2, inserting n elements triggers reallocations at capacities 1, 2, 4, …, and the elements moved total

```text
1 + 2 + 4 + ... + n/2 + n  <  2n
```

—a geometric series, so **under 2n moves for n insertions**, i.e. a constant average. The generalisation for factor k is `n·k/(k-1)` moves, so ×2 averages 2 moves per element and ×1.5 averages 3.

**Why geometric and not additive**: growing by a fixed +C would reallocate n/C times, moving `C + 2C + 3C + …` ≈ n²/2C elements in total—**O(n) per insertion amortized**, quadratic overall. That is exactly the difference the word "amortized" is buying.

**What "amortized" does and does not promise.** It bounds the *total* over a sequence, not any single call: one individual `push_back` is still O(n) and allocates. So for a latency-sensitive path—a UI thread, a market-data handler—the tail latency is what you feel, and `reserve()` up front is how you move that cost to a moment you choose ([CPP-073](#question-cpp-073)).

**The trade-off of the growth factor** is a nice detail to have ready: ×1.5 (MSVC) can reuse previously freed blocks, because the sum of all prior allocations eventually exceeds the next request—with ×2 it never does, so the allocator keeps advancing into fresh memory. ×2 does fewer reallocations. Neither is mandated.

**The same amortized argument applies to `unordered_map`'s rehashing** ([CPP-047](#question-cpp-047)) and to any geometrically-grown buffer. `deque` avoids it differently: it adds fixed-size blocks, so `push_back` is O(1) worst case and existing elements never move—which is why `deque` is the better choice when references must stay valid across growth.

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

**Put numbers on it**, because that is what makes the argument land: an L1 hit is ~1 ns, a main-memory miss is ~100 ns. A `list` traversal is a dependent load per node—the next address is not known until the current node arrives, so the prefetcher cannot help and the misses cannot overlap. A `vector` traversal reads a 64-byte cache line at a time (8 doubles, or 16 ints) and the hardware prefetcher runs ahead of you. That is a difference of roughly two orders of magnitude on the same "O(n) traversal".

**Memory overhead compounds it.** A `std::list<int>` node is two pointers plus the `int` plus padding—24 bytes and a separate allocation for 4 bytes of payload. So the list touches six times the memory and allocates n times; the vector allocates O(log n) times.

**The O(1) insertion is usually not the operation you are actually doing.** `list::insert` is O(1) *given an iterator*—but finding that position is an O(n) traversal at ~100 ns per step, while `vector::insert` in the middle is an O(n) `memmove` at streaming bandwidth. The linear scan dominates the shift, which is why the crossover where `list` wins is much further out than the complexity table suggests—often never, for small trivially-copyable elements.

**So state the honest conditions under which `list` does win:**

- elements are **large or expensive to move**, so shifting is costly;
- you already hold iterators to the insertion/erase points and mutate heavily;
- you need **references and iterators to stay valid** across insertion and erasure ([CPP-039](#question-cpp-039)) — `deque` or a node-based container is often the real requirement here, not list's complexity;
- you need O(1) `splice` between lists.

**The general principle**, worth saying out loud: for data structures on modern hardware, *contiguity beats asymptotics at realistic n*. The same reasoning explains why a sorted `vector` with binary search often beats `std::map`, and why a small `vector` beats `unordered_map` ([CPP-045](#question-cpp-045)). And it is a claim to *measure*, not to assert—the answer depends on element size and access pattern.

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

| | `std::map` | `std::unordered_map` |
|---|---|---|
| Structure | balanced tree (red-black) | buckets + chaining |
| Lookup | O(log n) guaranteed | O(1) average, **O(n) worst** |
| Iteration | sorted by key | unspecified order |
| Requires | `operator<` / comparator (strict weak ordering) | `std::hash` **and** `operator==` |
| Range queries | `lower_bound`, `upper_bound` — yes | no |
| Iterator invalidation | only the erased element | **rehashing invalidates iterators** (references survive) |
| Per-element overhead | 3 pointers + colour | 1 pointer + bucket array |
| Latency profile | uniform | occasional O(n) rehash spike |

**Choose `map` when you need ordering**—iteration in key order, `lower_bound` for a range or a nearest-key lookup, or a key type with a natural order but no good hash. Choose `unordered_map` for pure lookup by exact key at large n.

**The non-obvious points that make a good answer:**

- **`map` has no pathological case.** `unordered_map` degrades to O(n) with a bad or adversarial hash, and C++ does not randomise its hash seed, so untrusted keys are a real hash-flooding risk ([CPP-046](#question-cpp-046)).
- **Latency versus throughput.** `map` is uniformly O(log n); `unordered_map` is faster on average but rehashes in O(n) at unpredictable moments. On a UI or feed thread that tail can matter more than the mean ([CPP-073](#question-cpp-073)).
- **Both chase pointers.** Neither is cache-friendly: a tree walk is log n dependent loads, a hash lookup is a bucket load plus a chain walk. For **small n—up to roughly a hundred—a sorted `std::vector` with `std::lower_bound` usually beats both**, and a flat vector of pairs with a linear scan beats them at very small n ([CPP-044](#question-cpp-044)).
- **`std::string` keys** are the common case, and `unordered_map` must hash the whole string on every lookup while `map` often decides on the first few characters. Measure rather than assume.

**Also on the table**: `std::flat_map` (C++23) — a sorted vector pair with map's interface, fast lookup and iteration, slow insertion; and for a fixed key set known at compile time, a perfect hash or a `switch` beats everything.

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

Average O(1) is a statement about a good hash and a bounded load factor. Both can fail.

**Poor hashing** puts many keys in one bucket, and libstdc++ and libc++ resolve collisions by chaining, so that bucket becomes a linked list and lookup within it is linear.

**Adversarial input** is the same thing on purpose: if an attacker can choose keys and the hash is predictable, they can force every key into one bucket and turn an O(1) lookup into O(n) - a hash-flooding denial of service. That is why some languages randomise their hash seed per process and C++ does not, which makes `unordered_map` a poor choice for untrusted keys unless you supply your own hash.

**Rehashing** is the second cost: when `size() / bucket_count()` exceeds `max_load_factor()`, the container rebuilds with more buckets and rehashes everything - O(n), and it invalidates all iterators ([CPP-047](#question-cpp-047)). `reserve()` up front avoids the repeated rebuilds.

Worth mentioning as the practical counterweight: for small collections `std::map` or even a sorted `std::vector` often beats `unordered_map` outright, because the tree or the linear scan is cache-friendly and the hash container chases pointers ([CPP-045](#question-cpp-045)).

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

Rehashing allocates a new bucket array and re-places every element into it. It happens when an insertion would push `size() / bucket_count()` past `max_load_factor()` (1.0 by default), or when you call `rehash(n)` or `reserve(n)` explicitly.

**The invalidation rule is unusual and worth stating precisely**, because it differs from every other standard container: rehashing invalidates **iterators**, but **references, pointers and the elements themselves stay valid**. That falls out of the node-based design—`unordered_map` stores each element in its own node, and rehashing only relinks nodes into different buckets; it never moves the elements. So this is safe:

```cpp
auto& v = m["key"];    // reference survives...
m.reserve(1'000'000);  // ...a rehash
v = 42;                // still fine
```

while holding an iterator across the same insert is not. Erasing invalidates only iterators and references to the erased element.

**Cost.** Amortised O(1) per insertion over a sequence, but each individual rehash is O(n) and, because it allocates the new bucket array before freeing the old, peak memory briefly holds both. That is a latency spike, not a throughput problem—which matters if the code sits on a UI or market-data path. `reserve(expected)` up front pays it once, at a chosen moment.

**`reserve` vs `rehash`.** `rehash(n)` sets the bucket count to at least `n`; `reserve(n)` sets it to at least `n / max_load_factor()`, i.e. it takes the number of *elements* you intend to insert. `reserve` is almost always the one you want, and mirrors `vector::reserve` in intent though not in mechanism ([CPP-046](#question-cpp-046)).

**Lowering `max_load_factor`** below 1.0 trades memory for fewer collisions and triggers rehashing earlier; raising it does the opposite. It is a real tuning knob, but distribution quality of the hash usually dominates it.

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

A comparator `comp(a, b)` meaning "a comes strictly before b" must satisfy:

1. **Irreflexive** — `comp(a, a)` is false.
2. **Asymmetric** — if `comp(a, b)` then not `comp(b, a)`.
3. **Transitive** — `comp(a, b)` and `comp(b, c)` imply `comp(a, c)`.
4. **Transitive equivalence** — with "equivalent" defined as `!comp(a,b) && !comp(b,a)`, that relation must itself be transitive.

Rule 4 is the one people have never heard of, and it is where real comparators break. Compare only a person's *surname*: Smith~Ann and Smith~Bob are equivalent, but if the comparator also sometimes inspects the first name inconsistently, equivalence stops being transitive and the ordering is incoherent.

**The classic bug is `<=`:**

```cpp
std::sort(v.begin(), v.end(), [](int a, int b){ return a <= b; });  // WRONG
```

It is reflexive—`comp(a,a)` is true—so it is not a strict weak ordering. The consequence is not "slightly wrong order": `std::sort`'s partition loop relies on the comparator to stop, so it runs **past the end of the array**. Real out-of-bounds writes, real crashes, and typically only on inputs above the introsort threshold, which is why it survives small tests.

**Same hazard from:**

```cpp
[](const auto& a, const auto& b){ return a.p < b.p || a.q < b.q; }   // not transitive
```

The correct form is lexicographic, and `std::tie` writes it for you: `return std::tie(a.p, a.q) < std::tie(b.p, b.q);` — or in C++20, `auto operator<=>(const S&) const = default;`.

**NaN breaks it silently.** `operator<` on `double` is not a strict weak ordering when NaN is present, because NaN compares false against everything, so *every* NaN is "equivalent" to *every* other value—destroying transitivity of equivalence. Filter NaNs, or use a total order.

**Where it applies:** `sort`, `stable_sort`, `nth_element`, `lower_bound`, `binary_search`, `set`/`map` and the priority queue. Violating it is undefined behaviour, not a wrong answer. `_GLIBCXX_DEBUG` and MSVC's debug iterators actually check this, which makes them worth enabling in CI.

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

If you already have a `T`, `push_back(std::move(t))` can be equally good—identical, in fact: both construct one `T` in place by moving.

**Where `emplace_back` genuinely wins** is when the object does not exist yet and constructing it separately would cost a temporary:

```cpp
v.push_back(std::string(10, 'x'));   // construct temporary, move-construct into the vector
v.emplace_back(10, 'x');             // construct once, directly in the vector's storage
```

For a type that is expensive to move, or not movable at all, `emplace_back` is the only option.

**Three reasons not to make it the default**, which is the real content of the question:

*It bypasses explicit constructors.* `emplace_back` calls direct-initialisation, so `std::vector<std::unique_ptr<T>> v; v.emplace_back(new T);` compiles—`push_back` would not, because the conversion is `explicit`. And if the vector then reallocates and throws, that raw pointer leaks. This is a safety regression, not a stylistic one.

*Overload resolution gets surprising.* `v.emplace_back(10)` on a `vector<vector<int>>` creates a vector of ten elements, not a vector containing 10. With `push_back` the intent is unambiguous.

*It can be slower.* `push_back` may check reallocation and construct in one path; `emplace_back` must handle the self-referential and aliasing cases generically. The difference is small, but "emplace is faster" is not something to assert without a measurement.

**`emplace_back` returns a reference to the new element since C++17**, which `push_back` does not—useful for building in place.

**Note the family:** `emplace` on `map`/`set` always constructs the node, even when the key turns out to be present, so it can allocate and throw away. `try_emplace` (C++17) constructs the value only if the key is absent—and also does not move from its arguments on failure, which `emplace` does. For a map, `try_emplace` is usually the correct default.

**Practical rule:** use `push_back` when you have an object, `emplace_back` when you have constructor arguments, and `try_emplace` for maps. Readability decides the rest.

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

`string_view` is literally `{const char* data; size_t size;}` with a string-like interface. Copying one is two words, so it is passed by value; it has no allocation, no ownership and no destructor that does anything.

**The right default for a read-only parameter.** `void f(std::string_view)` accepts `std::string`, a string literal and a `char*` buffer with a length, all without allocating—whereas `void f(const std::string&)` silently constructs a temporary `std::string` (and probably heap-allocates) when handed a `const char*`.

**Lifetime is the whole risk, and it has more shapes than the obvious one:**

```cpp
std::string_view f() { return std::string("abc"); }   // 1: returns a view of a destroyed temporary

std::string_view sv = obj.name() + "!";                // 2: same bug through a temporary expression

std::string s = "hello";
std::string_view v = s;
s += " world";                                         // 3: reallocation; v now dangles
s.clear();                                             // 4: owner still alive, contents gone
```

Case 3 is the nasty one, because nothing was destroyed—the owner merely reallocated, so the view points into freed capacity while `s` looks perfectly healthy. The rule that catches all four: a view may not outlive the expression that produced its owner, and the owner must not be mutated while any view of it is alive. `-Wdangling` and clang-tidy's `bugprone-dangling-handle` catch cases 1 and 2; nothing catches 3 and 4 for you.

**Not null-terminated.** `substr` returns a view into the same buffer, so `data()` is *not* a C string and passing it to `fopen`, `strlen` or any `printf("%s")` is undefined behaviour. Any C API needs either an explicit length (`"%.*s"` with `(int)sv.size(), sv.data()`) or an owning `std::string(sv)`. It can also contain embedded `\0`.

**`substr` is where it earns its keep**: `std::string::substr` allocates and copies, `std::string_view::substr` is pointer arithmetic—so a tokeniser over a large buffer goes from O(n) allocations to zero.

**Not for members, generally.** A `string_view` data member is a lifetime contract you have to document and enforce; store `std::string` unless you own the buffer and the profile says otherwise. The same reasoning and the same dangling patterns apply to `std::span` over sequences ([CPP-051](#question-cpp-051)).

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

It is the array analogue of `string_view` ([CPP-050](#question-cpp-050)): a pointer and a length, no ownership, no allocation, and it binds to a C array, a `std::array`, a `std::vector` or any contiguous range.

What it replaces is the pointer-plus-length pair that every C-style API carries, and the two bugs that come with it - a length that drifts out of sync with the pointer, and a function template instantiated once per container type for no reason.

```cpp
void process(std::span<const int> data);   // accepts all of these
process(carray);  process(vec);  process(arr);  process({p, n});
```

Two properties worth naming. It can be **dynamic or static extent** - `std::span<int, 4>` carries the size in the type, so the check is at compile time and the object is just a pointer. And `span<const T>` is how you express read-only, because constness belongs to the element type, not to the span.

The hazard is the same as every non-owning view: it does not extend any lifetime. A span into a `vector` is invalidated by anything that reallocates it, and returning a span to a local is a dangling reference the compiler will not catch.

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

**Data race** has an exact definition: two accesses to the same memory location, from different threads, at least one a write, not ordered by a happens-before relationship, and not both atomic ([CPP-066](#question-cpp-066)). It is **undefined behaviour**—not "you might read a stale value", but the whole program becomes meaningless, which is why the compiler is entitled to optimise on the assumption it cannot happen ([CPP-016](#question-cpp-016)).

**Race condition** is a design-level statement: the outcome depends on timing. Not a language term, and not automatically UB.

**Race condition without a data race** — every access properly locked, still wrong:

```cpp
if (!map.contains(k))     // lock taken and released
    map.insert(k, v);     // lock taken again - another thread may have inserted in between
```

Check-then-act across two critical sections. No UB, no sanitizer report, just a wrong answer sometimes. The fix is to widen the critical section, not to add more locks—or use an atomic operation like `try_emplace` that does both under one lock.

**Data race without a meaningful race condition** — a benign-looking unsynchronized flag:

```cpp
bool stop = false;                     // thread A writes, thread B reads in a loop
while (!stop) work();                  // UB: the compiler may hoist the load out of the loop
```

"It works in practice" until the optimizer caches `stop` in a register and the loop never exits. The fix is `std::atomic<bool>` ([CPP-060](#question-cpp-060)). There is no such thing as a benign data race in C++.

**Why the distinction earns its keep in an answer:** the tools differ. TSan finds data races—it checks the happens-before relation mechanically. Nothing finds race conditions for you; they need reasoning about invariants, and stress or deterministic-scheduling tests. So "TSan is clean" means the memory model is satisfied, not that the logic is correct.

**Related terms worth separating**: a *deadlock* is the opposite failure—too much locking ([CPP-057](#question-cpp-057)); a *TOCTOU* bug is a race condition across a security boundary.

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

A mutex gives you two things, and candidates usually name only the first.

**Mutual exclusion**: at most one thread holds the lock, so a critical section is not interleaved with another one on the same mutex.

**Ordering**: unlocking a mutex *synchronizes with* the next successful lock of that same mutex. Everything the first thread wrote before the unlock is guaranteed visible to the second thread after the lock—no torn reads, no stale caches, no compiler moving those writes across the boundary. Without this half, mutual exclusion alone would not make the data correct. It is a release on unlock and an acquire on lock, the same relationship as `memory_order_release`/`acquire` on an atomic ([CPP-062](#question-cpp-062)).

**A mutex protects an invariant, not a variable.** This is the framing that makes design decisions fall out: if `size_` and `buffer_` must agree, they are under one mutex; if two fields are genuinely independent, two mutexes give more concurrency. Writing the association down—"`m_` guards `queue_` and `closed_`"—is what keeps it true six months later, and is exactly what Clang's thread-safety annotations (`GUARDED_BY`) mechanise.

**Always RAII.** `std::lock_guard` for the simple case, `std::unique_lock` when you need deferred locking or a condition variable, `std::scoped_lock` for two or more mutexes because it uses a deadlock-avoiding algorithm rather than a fixed order ([CPP-056](#question-cpp-056)). A hand-written `lock()`/`unlock()` pair leaks the lock on the first early return or exception.

**What not to do while holding it.** Never call out to unknown code—an I/O operation, a user callback, a COM call that may be marshaled and pump messages, or anything that could take a second mutex—because you no longer control what runs inside your critical section, and that is how both deadlocks and surprise reentrancy arrive. Compute into a local, then take the lock only for the handover.

**Cost.** Uncontended, a `std::mutex` is an atomic RMW—tens of nanoseconds. Contended, it is a syscall and a context switch, orders of magnitude worse, and that cliff is the reason fine-grained locking sometimes loses to one coarse lock. It is also not recursive: locking a `std::mutex` you already hold is undefined behaviour, and reaching for `recursive_mutex` is usually a sign the ownership boundary is in the wrong place.

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

It has slightly more overhead because it stores additional state: a mutex pointer plus a bool, versus `lock_guard`'s reference alone. In practice the difference is negligible next to the lock itself—choose on semantics, not cost.

**`unique_lock` is movable**, which is what makes the extra state worth having: it can be returned from a function, stored in a container, or handed to a condition variable.

```cpp
std::unique_lock lock(m, std::defer_lock);   // construct without locking
std::unique_lock lock(m, std::try_to_lock);  // try, then check lock.owns_lock()
std::unique_lock lock(m, std::adopt_lock);   // already locked - just take ownership
```

**The condition-variable requirement is not arbitrary.** `cv.wait` must atomically release the mutex and suspend, then reacquire on wake—so it needs an object it can unlock and relock, which `lock_guard` cannot do ([CPP-059](#question-cpp-059)).

**The full family, since the interviewer may push:**

| | Use |
|---|---|
| `std::lock_guard` | the default: one mutex, whole scope, no flexibility needed |
| `std::scoped_lock` (C++17) | **two or more mutexes**, deadlock-avoiding acquisition; also works with one, so it can be the default in C++17 code ([CPP-056](#question-cpp-056)) |
| `std::unique_lock` | deferred/timed/try locking, manual unlock, moving, condition variables |
| `std::shared_lock` (C++14) | reader side of a `shared_mutex` |

**The classic bug both prevent** is the unnamed temporary: `std::lock_guard(m);` declares nothing—it creates a temporary that is destroyed at the end of the statement, so the mutex is unlocked immediately and the following code runs unprotected. It compiles cleanly. `std::lock_guard lock(m);` with a name is correct, and C++17 CTAD means you no longer write the template argument.

**Manual unlocking is a smell.** If you find yourself calling `lock.unlock()` halfway through a function, the usual right answer is a smaller scope—`{ std::lock_guard g(m); ... }`—which is harder to get wrong on an exception path.

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

The part that matters is the multi-mutex case: `scoped_lock` uses a deadlock-avoidance algorithm (the same as `std::lock`) rather than simply taking them in the order written. So two threads that acquire the same two mutexes in opposite orders cannot deadlock - which is the single most common deadlock in real code ([CPP-057](#question-cpp-057)).

```cpp
void transfer(Account& a, Account& b) {
    std::scoped_lock lock(a.m, b.m);   // safe regardless of argument order
    // ...
}
```

Written by hand that would be two `lock_guard`s and an ordering convention everyone has to remember, or `std::lock` followed by two `lock_guard`s with `std::adopt_lock`.

Since C++17 `scoped_lock` is the default choice over `lock_guard`: it does the same job for one mutex and the right thing for several. The one trap is CTAD-related - `std::scoped_lock lock(m);` is correct and `std::scoped_lock(m);` creates a temporary that unlocks immediately ([CPP-193](#question-cpp-193)).

`unique_lock` remains the one to use when you need to unlock early, transfer ownership, defer locking or wait on a condition variable.

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

**Break at least one Coffman condition, deliberately.** In practice the one you attack is **circular wait**, because the other three are usually inherent to mutexes.

*Global lock ordering* is the primary technique: define a total order over your mutexes—by address, by a documented level number, by module layering—and always acquire in that order. Documented and enforced, this makes a cycle impossible by construction.

*`std::scoped_lock`* handles the case where you need several at once and cannot impose an order at the call site: it uses a lock-and-back-off algorithm rather than a fixed sequence ([CPP-056](#question-cpp-056)). `std::lock` is the pre-C++17 spelling.

*Timed acquisition* (`try_lock_for`) breaks *no preemption*: you give up and retry rather than waiting forever. It converts a deadlock into a livelock risk, so it needs backoff and a bound.

**The deadlock people actually hit is not two mutexes**, and this is worth saying: it is **calling unknown code while holding a lock**—a callback, a virtual function, an I/O operation, or a COM call that pumps messages and re-enters your code (see the COM and Excel bank). You no longer control what locks that code takes, so you cannot reason about ordering at all. The rule is to compute under the lock and *call out* after releasing it.

**Others that qualify as deadlock in practice:** self-deadlock on a non-recursive `std::mutex` (undefined behaviour, usually a hang); a thread waiting on a condition variable whose notifier has exited without a terminal predicate ([CPP-059](#question-cpp-059)); and lock-order inversion between your code and a library's internal locks.

**How to find one after the fact:** attach a debugger and dump all thread stacks—the cycle is visible as two threads each blocked in `lock` ([CPP-084](#question-cpp-084)). ThreadSanitizer detects lock-order inversions even when they do not deadlock on that run, which makes it far more useful than waiting for the hang; and Clang's thread-safety annotations can enforce an order at compile time.

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

Two independent reasons, and naming both is what the question is testing.

**Spurious wakeups**: `wait` may return without any notification at all. That is permitted by the standard and does happen on real platforms, so a bare `wait` must always be inside a loop that rechecks the condition.

**Lost wakeups**: `notify_one` releases nobody if no thread is waiting yet. If the producer sets the flag and notifies before the consumer reaches `wait`, the consumer then waits forever - for a notification that already happened. The predicate form fixes this because it checks the condition *before* waiting.

```cpp
std::unique_lock lock(m);
cv.wait(lock, [&]{ return ready; });     // equivalent to: while (!ready) cv.wait(lock);
```

The mutex is part of the mechanism, not decoration: `wait` atomically releases it and suspends, then reacquires it before returning, which is what makes the check-and-wait indivisible. Modifying the condition without holding the mutex reintroduces the lost wakeup even with a correct predicate.

`notify_one` versus `notify_all`: one is enough when any single waiter can consume the event; use `notify_all` when waiters are waiting on different conditions, or the wrong one may wake, see its predicate is false, and go back to sleep while the right one is never woken.

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

**It gives you two things, and they are separable:** indivisibility (no torn reads or lost updates) and *ordering* (what other memory becomes visible around it). The default `memory_order_seq_cst` gives both; `memory_order_relaxed` gives only the first ([CPP-065](#question-cpp-065)).

```cpp
std::atomic<int> n{0};
n++;                                       // atomic read-modify-write, seq_cst
n.fetch_add(1, std::memory_order_relaxed); // atomic, but no ordering guarantees
int old = n.exchange(5);                   // atomic swap
n.compare_exchange_weak(expected, desired);// CAS ([CPP-062](#question-cpp-062))
```

**`is_lock_free()` is the question to ask about any `atomic<T>`.** Integral types and pointers up to the machine word are lock-free on every mainstream platform; `atomic<T>` for a larger struct compiles fine but is implemented with a hidden lock, so it is *not* usable in a signal handler and gives none of the performance you were after. `std::atomic<T>::is_always_lock_free` is a `constexpr` you can `static_assert` on.

**Atomic does not mean composable**, which is the "atomicity alone" point made concrete:

```cpp
if (n.load() < limit)   // two separate atomic operations...
    n.store(n + 1);     // ...another thread can act in between. Not atomic as a whole.
```

The fix is one operation: `fetch_add`, or a CAS loop. Anything spanning **two** variables needs a mutex or a lock-free algorithm designed for it ([CPP-182](#question-cpp-182)).

**Cost.** An uncontended atomic RMW is a locked instruction—on x86, roughly 10–20 ns, an order of magnitude more than a plain load. Under contention it is far worse, because the cache line must be acquired exclusively by each core in turn ([CPP-068](#question-cpp-068)). So "atomic counter instead of a mutex" is a win for a single counter and a loss if many threads hammer it—a sharded or thread-local counter summed at the end beats both.

**`atomic_flag`** is the only type guaranteed lock-free everywhere, and C++20 added `wait`/`notify_one` to atomics, so you can block on one without a condition variable. `volatile` is not a substitute for any of this ([CPP-019](#question-cpp-019)).

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

The interesting half of this question is how an atomic can be **worse** than a mutex, because that is the part people do not expect.

**It may not be lock-free at all.** `std::atomic<T>` is required to compile for any trivially copyable `T`, not to be lock-free. For anything wider than the platform's atomic word, the implementation takes a hidden lock - so you get a mutex anyway, with none of the visibility and none of the ability to choose its granularity. `std::atomic<T>::is_always_lock_free` is the compile-time check, and it is worth a `static_assert` in code that depends on the answer.

**Under contention it loses to a mutex.** A CAS loop that fails retries, and retrying means reading the cache line again, which means taking it back from whichever core just wrote it. Sixteen threads incrementing one atomic counter spend their time moving one cache line between cores; a mutex, by contrast, blocks the losers and lets the scheduler run something useful. The atomic version burns CPU to go slower, and the profile looks like the CPU is busy.

**Every write invalidates the line everywhere.** That is the same mechanism as false sharing ([CPP-068](#question-cpp-068)) but with the variable genuinely shared, so padding does not help. The fix is usually to stop sharing the variable - per-thread counters summed at the end, or batching - rather than to make the shared write cheaper.

**Atomics do not compose.** Two atomic variables are not an atomic pair: each operation is indivisible, the sequence of two is not. Code that checks one and then updates the other is a race, and it reads as obviously correct in review because every individual line is atomic. A mutex covering both is correct and honest; the atomic version needs a redesign into one value or a CAS on a combined word.

**Relaxed ordering is where the real bugs are.** `memory_order_relaxed` looks like free speed and silently removes the ordering another thread was relying on. It will almost always still pass on x86, which gives acquire/release semantics nearly for free, and then fail on ARM - so the bug ships from a developer machine that could not reproduce it. Default to the sequentially consistent operations and weaken only with a specific argument for why it is safe.

**Pointer-based lock-free structures add two more problems.** ABA - a value read as A, changed to B and back to A, so the CAS succeeds against state that is not the state you inspected - and reclamation, the question of when it is safe to free a node another thread may still be reading. Solving the second needs hazard pointers, epochs or RCU, and at that point the complexity is far beyond the mutex you were avoiding.

**And the failure mode is worse.** A mutex deadlock appears in a stack trace and points at two threads. A broken lock-free algorithm appears as corrupted data minutes later, on one machine, under load.

So the honest ordering: start with a mutex, keep the critical section small, measure, and move to atomics only where the profile shows lock contention *and* the state is a single value. Lock-free is a guarantee about system-wide progress, not a synonym for fast - and wait-free, which bounds every individual thread, is stronger still and rarer.

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

**Note the signature detail that catches people:** `expected` is passed **by reference and is updated on failure** with the value actually found. That is what makes the retry loop natural—you do not re-read.

```cpp
std::atomic<int> v{0};
int expected = v.load(std::memory_order_relaxed);
while (!v.compare_exchange_weak(expected, expected * 2)) {
    // on failure, `expected` now holds the current value - just loop
}
```

Semantically it is:

```text
if (v == expected) { v = desired; return true; }
else               { expected = v; return false; }
```

executed indivisibly. On x86 it is `lock cmpxchg`; on ARM and POWER it is an LL/SC pair (`ldaxr`/`stlxr`), which is exactly why the `weak` form exists ([CPP-063](#question-cpp-063)).

**Two memory orders, not one.** `compare_exchange_*(expected, desired, success_order, failure_order)`—the failure order may not be stronger than the success order, and a common correct choice is `acq_rel` on success, `acquire` on failure.

**Why CAS rather than a lock:** progress. A CAS loop is lock-free—if a thread is suspended mid-loop, others still make progress—whereas a thread suspended holding a mutex blocks everyone ([CPP-182](#question-cpp-182)). That property, not raw speed, is the reason to use it.

**The ABA problem is the standard follow-up.** CAS compares *values*, not history: a pointer can change A → B → A between your read and your CAS, so the CAS succeeds while the world has changed underneath—the node you are about to link may have been freed and recycled. Mitigations are a tagged/versioned pointer (a counter packed alongside, via `compare_exchange` on a double-width value), hazard pointers, or epoch-based reclamation. Plain reference counting does not solve it.

**And the honest caveat:** under heavy contention a CAS loop can spin and retry repeatedly, burning cache-coherence traffic, and a well-implemented mutex may beat it outright. Lock-free is a progress guarantee, not a performance guarantee—measure it ([CPP-184](#question-cpp-184)).

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

**Where the spurious failure comes from**: on architectures with load-linked/store-conditional—ARM, POWER, RISC-V—the store-conditional can fail because of a context switch, an interrupt, or an unrelated write to the same cache line, even though the compared value was correct. `weak` exposes that directly and compiles to a single LL/SC pair. `strong` must hide it, so the compiler wraps the pair in its own retry loop. On x86 there is no such distinction—`lock cmpxchg` never fails spuriously—so both compile to the same instruction and the choice is free.

**The rule follows mechanically:**

```cpp
// in a loop that must retry anyway -> weak (you would loop on failure regardless)
int expected = v.load();
while (!v.compare_exchange_weak(expected, expected + 1)) { }

// a single attempt whose result you branch on -> strong
if (!head.compare_exchange_strong(expected, node)) { take_slow_path(); }
```

Using `weak` outside a loop is a bug: you would take the failure path on a value that actually matched. Using `strong` inside a loop is merely wasteful—a nested retry loop inside your retry loop.

**One extra subtlety**: if recomputing `desired` after a failure is expensive, `strong` may win even in a loop, because `weak` can force that recomputation for no reason. So the fuller rule is: `weak` in a loop *when the loop body is cheap*.

**The `expected` reference is updated on failure** in both forms, which is what makes the loop above correct without a re-load ([CPP-062](#question-cpp-062)).

**Practical note:** on x86-64 this whole distinction is academic, so do not over-claim a performance difference. On ARM—increasingly common in servers and on Apple silicon—it is real and measurable in a hot CAS loop.

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

Atomicity and ordering are two separate guarantees, and `relaxed` buys only the first. The operation itself is indivisible - no torn read, no lost update - but the compiler and the CPU may move other loads and stores across it freely, so it publishes nothing and synchronises with nothing.

Where it is genuinely correct:

- A statistics counter whose value is read at the end and whose ordering against anything else does not matter.
- A reference count **increment**, because adding a reference cannot let anything die. The decrement must be stronger - `acq_rel` - so the thread that reaches zero sees every other thread's writes before running the destructor ([CPP-189](#question-cpp-189)).
- A flag whose only job is to be eventually observed, with no data attached to it.

Where it is wrong, and this is the common case: any flag that means "the data I wrote is now ready". That requires release on the store and acquire on the load, and `relaxed` silently removes it.

The reason this bug ships is that **x86 gives acquire/release ordering almost for free**, so a relaxed program that is formally broken behaves correctly on every developer machine and fails on ARM. That is the single strongest argument for defaulting to `seq_cst` and weakening only where you can state why it is safe ([CPP-061](#question-cpp-061)).

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

**The mental model**: release is "publish everything I did before this point"; acquire is "subscribe to everything the publisher did". The synchronization is established by the *pairing*—a release alone guarantees nothing, and an acquire that reads a value written by a relaxed store guarantees nothing either. Both sides must use the ordering, and B must actually **observe the value A stored**.

**What each one forbids, precisely:**

| | Prevents |
|---|---|
| `memory_order_release` (store) | earlier reads and writes moving **after** it |
| `memory_order_acquire` (load) | later reads and writes moving **before** it |

Each is a one-way barrier, which is why they are cheaper than a full fence. Neither prevents movement in the other direction—a later store may sink below an acquire—and that is fine, because the guarantee you need is only about the data the publisher wrote.

**Cost.** On x86 the ordering is nearly free: loads are already acquire and stores already release in the hardware model, so `acquire`/`release` cost no extra instructions—only `seq_cst` stores need an `mfence` or `xchg` ([CPP-067](#question-cpp-067)). On ARM and POWER they emit real barriers (`ldar`/`stlr` on ARMv8), so the difference between `relaxed`, `acq_rel` and `seq_cst` is measurable there.

**`memory_order_acq_rel`** is for read-modify-write operations that both consume and publish—the CAS in a lock-free stack push, for example. **`memory_order_consume`** exists on paper for data-dependent ordering, but every mainstream compiler promotes it to `acquire`, so treat it as unusable.

**This is the same relationship a mutex gives you**: unlock is a release, lock is an acquire ([CPP-054](#question-cpp-054)). Which is the honest framing to offer—if you understand acquire/release you understand what a mutex is doing, and if you do not need lock-free code you already have these semantics for free.

**The formal statement of "becomes visible" is happens-before** ([CPP-066](#question-cpp-066)): the release store *synchronizes with* the acquire load, which puts everything sequenced before the store in a happens-before relation with everything sequenced after the load.

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

If write A happens-before read B, B is guaranteed to observe effects consistent with that ordering—and if no such relation exists between a write and a read of the same location, that is precisely the definition of a **data race**, hence undefined behaviour ([CPP-053](#question-cpp-053)).

**It is built from two ingredients:**

1. **Sequenced-before** — ordering within a single thread, from the program text.
2. **Synchronizes-with** — a cross-thread edge, created by a release operation being observed by an acquire operation on the same variable, by a mutex unlock being observed by a subsequent lock, by a thread's constructor relative to the new thread's start, or by a thread's end relative to a successful `join` ([CPP-065](#question-cpp-065)).

Happens-before is the **transitive closure** of those two, and transitivity is what makes it useful: A writes data, A releases a flag, B acquires the flag, B reads the data. There is no direct edge between the two data accesses—the relation is assembled through the flag.

**It is a partial order, not a timeline.** Two operations in different threads with no synchronizing edge between them are simply *unordered*; asking which happened "first" in wall-clock terms is not a question the model answers, and the answer would not be useful because different cores can observe different orders. That is the conceptual jump from single-threaded reasoning.

**Consequences worth stating:**

- Adding a `sleep` creates no happens-before edge. Timing is not synchronization.
- `std::thread`'s constructor and `join()` are synchronization points, which is why data handed to a thread at construction and collected after `join` needs no further locking.
- `static` local initialisation is thread-safe and establishes happens-before—the "magic statics" guarantee that makes the Meyers singleton correct.
- `memory_order_relaxed` operations are atomic but create **no** happens-before edges, so they can be used for counters but not for publishing data.

**This is the formal machinery underneath everything else in this section**, and it is what TSan actually checks: it builds the happens-before graph at runtime and reports any unordered conflicting pair—which is why it finds races that did not manifest on that particular run.

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

**What it adds over acquire/release** is a *single total order over all seq_cst operations in the program*, which every thread agrees on. Acquire/release gives you pairwise publication; it does **not** guarantee that two threads observing two independent variables see them in the same order. The standard demonstration is IRIW (independent reads of independent writes): with `acq_rel`, thread C can see `x` set before `y` while thread D sees `y` before `x`, and both are legal. With `seq_cst` they cannot disagree.

**The classic case it fixes** is Dekker-style mutual exclusion:

```cpp
// thread 1                       // thread 2
x.store(true, seq_cst);           y.store(true, seq_cst);
if (!y.load(seq_cst)) critical(); if (!x.load(seq_cst)) critical();
```

With `seq_cst` at most one enters. Weaken the stores to `release` and the loads to `acquire` and **both** can enter—because a store may be buffered past a subsequent load of a different variable. That StoreLoad reordering is the one x86 permits, and it is exactly what `seq_cst` costs money to prevent.

**The cost, concretely.** On x86-64, `seq_cst` **loads** are free (a plain `mov`), but a `seq_cst` **store** needs an `mfence` or `xchg`—roughly 20–30 cycles. On ARMv8 it is `stlr`/`ldar` plus stronger fences. So the practical advice is: if you are storing in a hot loop, `release` may be a real win; if you are mostly loading, `seq_cst` costs nothing on x86.

**It is the default for a reason.** `x.store(v)` and `x.load()` without an order argument are `seq_cst`, because the weaker orders are genuinely hard to get right and the failures are non-deterministic and platform-specific ([CPP-065](#question-cpp-065)). The defensible position in an interview is: start at `seq_cst`, weaken only where a profile shows it matters and the reasoning has been written down—and note that even experts get relaxed orderings wrong, which is why the standard's own examples come with proofs.

**Note it is not "no reordering".** Non-atomic operations around it can still move; the total order constrains the seq_cst operations themselves, plus the happens-before edges they create ([CPP-066](#question-cpp-066)).

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

This can severely reduce performance—an order of magnitude on a hot counter is routine.

**Why it costs so much.** Coherence works at cache-line granularity, typically **64 bytes**. When core A writes any byte of a line, the MESI protocol must invalidate that line in every other core's cache. If core B then writes a *different* variable in the same line, it must first acquire the line exclusively—pulling it back across the interconnect. Two logically independent counters thus serialize on the cache line, at roughly 100 ns per bounce instead of 1 ns.

**The canonical shape:**

```cpp
struct Counters { std::atomic<long> a; std::atomic<long> b; };   // same 64-byte line
// thread 1 hammers c.a, thread 2 hammers c.b -> the line ping-pongs

struct alignas(64) Padded { std::atomic<long> v; };              // one per line
Padded counters[N];
```

C++17 gives you the portable constant: `std::hardware_destructive_interference_size` for padding apart, and `std::hardware_constructive_interference_size` for deliberately packing together.

**Where it hides in real code:**

- an array of per-thread accumulators, indexed by thread id — the textbook case;
- a lock-free queue whose head and tail indices are adjacent members, so producer and consumer fight over one line;
- a mutex sitting next to the data it protects — every lock acquisition invalidates the data's line;
- `shared_ptr` reference counts, when many threads copy pointers to the same object ([CPP-032](#question-cpp-032)).

**How to detect it**, which is the part that separates a memorised answer from experience: it does not show up in a normal profile as a single hot line—it shows up as **poor scaling**, where adding cores does not add throughput. `perf c2c` on Linux attributes cache-line contention to specific lines and offsets; VTune has a Memory Access analysis; `perf stat` showing high `HITM` events is the signature.

**Fixes, in order of preference:** don't share—give each thread its own accumulator and combine at the end; pad or align what must be shared; reorder struct members so read-mostly and write-hot fields sit in different lines ([CPP-028](#question-cpp-028)). Padding costs memory, so apply it where a measurement says to, not everywhere.

**True sharing** is the sibling problem: threads contending on the *same* variable. Padding does not help there—only reducing the sharing does.

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

```cpp
template<class T>
class Queue {
    std::mutex m_;
    std::condition_variable not_empty_, not_full_;
    std::queue<T> q_;
    size_t cap_;
    bool closed_ = false;
public:
    bool push(T v) {
        std::unique_lock lk(m_);
        not_full_.wait(lk, [&]{ return q_.size() < cap_ || closed_; });
        if (closed_) return false;
        q_.push(std::move(v));
        lk.unlock();                 // unlock before notifying: the woken thread
        not_empty_.notify_one();     // would otherwise immediately block on the mutex
        return true;
    }
    std::optional<T> pop() {
        std::unique_lock lk(m_);
        not_empty_.wait(lk, [&]{ return !q_.empty() || closed_; });
        if (q_.empty()) return std::nullopt;          // closed and drained
        T v = std::move(q_.front()); q_.pop();
        lk.unlock(); not_full_.notify_one();
        return v;
    }
    void close() { { std::lock_guard lk(m_); closed_ = true; } 
                   not_empty_.notify_all(); not_full_.notify_all(); }
};
```

**The four details that make it correct**, and each is a likely follow-up:

*Predicates, not bare waits.* Spurious and lost wakeups both require the condition to be rechecked under the mutex ([CPP-059](#question-cpp-059)).

*`closed_` is part of every predicate.* Without it, consumers block forever at shutdown—the single most common bug in this code. `close()` must `notify_all`, because every waiter needs to re-evaluate, and `pop` must drain remaining items before returning `nullopt`.

*Two condition variables, not one.* One CV for both "not empty" and "not full" means producers wake consumers and vice versa, and you must use `notify_all` to be safe—wasteful. Separate CVs let you use `notify_one`.

*Bounded capacity is the backpressure* ([CPP-077](#question-cpp-077)). An unbounded queue turns a slow consumer into unbounded memory growth; blocking the producer pushes the problem back to where it can be handled.

**Scaling it.** At high rates the mutex itself becomes the bottleneck, and the answers are: **batch**—pop everything available in one lock acquisition rather than item by item ([CPP-074](#question-cpp-074)); shard into per-consumer queues with work stealing; or move to a lock-free ring buffer (SPSC is genuinely simple and needs only acquire/release on two indices; MPMC is not) ([CPP-183](#question-cpp-183)). Pad head and tail apart to avoid false sharing ([CPP-068](#question-cpp-068)).

**For an Excel feed** the consumer side is the constrained one: the Excel thread must be the only one touching the object model, so the queue drains there and coalesces before writing (see the COM and Excel bank).

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

**Define the target first.** "Make it faster" is not actionable; "P99 update-to-screen latency under 50 ms at 20 000 ticks/second" is. The metric and the percentile both matter, because optimising the mean can make the tail worse ([CPP-073](#question-cpp-073)).

**Then find out what kind of problem it is**, because the tools differ:

| Symptom | Likely cause | Tool |
|---|---|---|
| one core pinned at 100% | CPU-bound hot path | sampling profiler: `perf`, VTune, WPA |
| all cores busy, no throughput gain | lock contention or false sharing | `perf c2c`, VTune Threading, lock profiler |
| low CPU, high wall time | I/O, syscalls, or waiting | strace/ETW, wait analysis |
| memory grows, then stalls | allocation churn or fragmentation | heaptrack, VTune Memory, `massif` |
| high IPC but slow | cache misses, branch misprediction | `perf stat` counters |

**Sampling beats instrumentation** for finding the hot path: instrumentation distorts small functions and can hide the very inlining you depend on. A flame graph from `perf record` answers "where is the time" in one picture.

**Change one thing at a time and re-measure**, on a machine that is quiet, with the workload that matters, across enough runs to see the variance. Report a distribution, not a single number. Keep the baseline so the claim is falsifiable.

**Algorithmic before micro.** An O(n²) loop over a workbook range is not fixed by tighter inner-loop code; reducing boundary crossings or changing the data structure is where the order-of-magnitude lives ([CPP-044](#question-cpp-044), and reducing COM crossings in the COM and Excel bank).

**The honest closing point for an interview** is the one candidates skip: check that the fix is worth its cost. An optimisation that adds a cache, a pool or a lock-free structure adds a class of bug; if the profile says it buys 3%, it is not worth it.

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

**They are not opposites, but they trade against each other.** A simple relation, Little's Law, connects them: `concurrency = throughput × latency`. So a system at fixed concurrency that doubles throughput has halved per-item latency only if nothing queues; in practice, pushing throughput toward saturation makes queues grow and latency rises sharply—the classic hockey-stick curve.

**Where the trade-off shows up concretely:**

- **Batching** amortises fixed cost over many items: higher throughput, and the first item in a batch waits for the batch to fill ([CPP-074](#question-cpp-074)).
- **Buffering / larger queues** absorb bursts and raise throughput, at the cost of queueing delay—bufferbloat is this mistake at network scale.
- **Threading** raises throughput but adds synchronization and context-switch cost to each individual operation.
- **Compression or caching** may cut I/O throughput requirements while adding per-request latency.

**Which one you optimise is a product question, not a technical one.** A batch ETL job cares only about throughput. A trading UI cares about the tail. A market-data add-in in Excel is interesting precisely because it is both: the feed side is throughput-bound at 20 000 updates a second, and the display side is latency-bound but only needs to be correct at human refresh rates—which is exactly why coalescing and throttling work there ([CPP-075](#question-cpp-075), [CPP-076](#question-cpp-076)).

**State the number you are optimising.** "Latency" without a percentile is ambiguous, and mean latency hides the behaviour users actually notice ([CPP-073](#question-cpp-073)).

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

**Why the mean is misleading.** Latency distributions are right-skewed: a few very slow operations pull the mean up without describing anything a user experiences. Worse, averaging away the tail hides the failures—if P99 is 2 seconds, one request in a hundred is unacceptable, and a user making a hundred interactions hits it.

**Tail amplification** is the point that makes this concrete. If a single operation fans out to N independent sub-operations and must wait for all of them, the chance of hitting the tail is `1 - (1-p)^N`. With a per-call P99, twenty calls means about **18%** of operations exceed that "1%" bound. That is why the tail is the metric in any system that composes calls—and why reducing the number of crossings helps more than speeding each one up.

**Where the tail comes from**, which is usually the follow-up question:

- allocation and GC-like pauses—in C++, a `vector` reallocation or a page fault ([CPP-043](#question-cpp-043));
- rehashing in a hash container ([CPP-047](#question-cpp-047));
- lock contention and convoying;
- an OS scheduling quantum, a context switch, or another process on the machine;
- a cold cache or TLB miss after a period of idleness;
- in Excel specifically, a full recalculation triggered by a write.

**Measuring it properly**: record a histogram, not a running average—HdrHistogram or `t-digest`, because percentiles cannot be averaged across intervals. And beware **coordinated omission**: a load generator that waits for a response before sending the next request stops measuring exactly when the system is slowest, understating the tail by orders of magnitude. Send at a fixed rate and measure against intended send time.

**P99.9 and max matter too** when the operation is user-visible or has a deadline. For a real-time feed, the honest statement of a requirement is a percentile and a bound: "P99 under 50 ms, no update older than 250 ms".

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

**It works whenever cost has a large fixed component.** If one operation costs `f + v·n` where `f` is fixed overhead and `n` is items, then processing items one at a time costs `n·(f + v)` and batching costs `f + n·v`. The saving is `(n-1)·f`—so the larger `f` is relative to `v`, the bigger the win. That is why batching is transformative for COM crossings and syscalls (huge `f`) and marginal for arithmetic (tiny `f`).

**Bounding the added latency is the design decision.** Two triggers, whichever fires first:

```text
flush when:  batch size >= N   (throughput bound)
         or  age of oldest item >= T   (latency bound)
```

Without the timer, a quiet period leaves the first item sitting indefinitely—the bug that turns a batching optimisation into a user-visible stall. `T` comes from the latency requirement (a UI refresh at 100–250 ms), `N` from memory and from the point where the per-item cost stops falling.

**Batching composes with coalescing** and the two are often confused: batching sends *n* updates in one operation; coalescing reduces *n* by discarding superseded updates for the same key ([CPP-076](#question-cpp-076)). In a market-data feed you do both—coalesce into a dirty map, then flush the map as one batch.

**Where it costs you:** memory proportional to batch size; a partial failure now affects a whole batch, so error handling must decide between all-or-nothing and per-item results; and debugging is harder because the correlation between an input and its effect is deferred.

**The Excel case is the extreme one**: a per-cell write is dominated by fixed cost, so one `Range` assignment of a 2-D array replaces a million crossings with one (see the COM and Excel bank). That is three orders of magnitude, not a percentage.

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

**The distinction to draw is between rate-limiting the *work* and rate-limiting the *output*.** In a feed, you must consume every tick—that is correctness—but you need not *render* every tick, because the display cannot show more than the refresh rate and the human eye cannot use it. Throttling the output side costs nothing real and removes most of the load.

**Two common shapes, and they behave differently:**

- **Throttle**: run at most once per interval — emits *during* a burst, at a steady rate. Right for a live price display.
- **Debounce**: run only after quiet for T — emits *after* the burst ends. Right for "recalculate when the user stops typing", wrong for a feed, because a continuous stream never goes quiet and nothing is ever emitted.

Mixing them up is the classic bug: a debounced market-data view shows nothing while the market is busy.

**Where the limit comes from.** Excel's RTD mechanism throttles for you—`Application.RTD.ThrottleInterval`, in milliseconds—so a well-behaved RTD server pushes updates and lets Excel pull at its own tempo (see the COM and Excel bank). If you are pushing to the object model yourself, a 100–250 ms timer on the Excel thread is the equivalent, and it must run on that thread.

**Related but distinct** are rate limiters for fairness and protection—token bucket (allows a burst up to the bucket size, then a steady rate) and leaky bucket (strictly smooths output). Those bound *resource use*; throttling a UI bounds *wasted work*.

**Combine it with coalescing** ([CPP-076](#question-cpp-076)): throttling decides *when* to flush, coalescing decides *what* is in the flush. Either alone is half the solution—throttling without coalescing means you either render stale data or still carry the full backlog.

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

**The data structure is a map keyed by the logical item**, not a queue:

```cpp
std::unordered_map<SymbolId, Quote> dirty_;   // last value wins
void on_tick(SymbolId s, const Quote& q) {
    std::lock_guard lk(m_);
    dirty_[s] = q;                            // overwrite in place - O(1), no growth
}
// on the UI/Excel thread, every 100 ms: swap out `dirty_` and write it as one block
```

That single line—overwrite rather than append—is what bounds memory by the number of *symbols* rather than the number of *ticks*. A queue under a 20 000/sec feed with a 10/sec consumer grows without limit; the dirty map cannot exceed the universe size, whatever the burst.

**It is lossy by design, and that must be a deliberate decision.** Coalescing is correct when the consumer needs *current state* (a price display, a position, a progress bar) and wrong when it needs *every event* (a trade log, an audit trail, anything that must be replayed or summed). Some fields are coalescable and others are not in the same message—last price coalesces, cumulative volume must be taken from the newest tick rather than summed, and individual trades may need to go to a separate non-lossy path.

**Swap, don't drain.** Flushing by swapping the map with an empty one under the lock keeps the critical section O(1) and lets the write happen outside the lock ([CPP-054](#question-cpp-054)).

**It pairs with throttling and batching**: throttling sets the flush interval ([CPP-075](#question-cpp-075)), coalescing collapses the content, batching writes the result in one operation ([CPP-074](#question-cpp-074)). Together they turn a 20 000/sec feed into ten bounded writes per second—which is exactly the architecture an Excel real-time add-in needs, because the object model is the scarce resource (see the COM and Excel bank).

**It is also a form of backpressure** ([CPP-077](#question-cpp-077))—one that never blocks the producer, at the cost of dropping intermediate states.

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

**Without it, the system fails in the worst possible way.** An unbounded queue turns "the consumer is 10% too slow" into unbounded memory growth, increasing latency, swapping, and eventually a bad_alloc or an OOM kill—hours after the real problem started, and with no signal until then ([CPP-085](#question-cpp-085)). Backpressure converts a resource failure into a visible, bounded behaviour.

**There are only three things you can do when the queue is full**, and choosing among them is a product decision:

| | Behaviour | Right when |
|---|---|---|
| **Block** the producer | throughput matches the consumer; the pressure propagates upstream | the producer can slow down — a file reader, an internal pipeline |
| **Drop** | bounded memory and latency; data is lost | the producer cannot be slowed — a UDP market-data feed, telemetry |
| **Coalesce** | bounded memory, no *state* lost, intermediate steps lost | the consumer needs current state, not every event ([CPP-076](#question-cpp-076)) |

For a live feed the answer is usually coalesce, then drop the oldest if even that is not enough; blocking is wrong there because you cannot slow the exchange down, and blocking your reader thread just moves the queue into the OS socket buffer.

**Dropping must be explicit and counted.** A dropped-message counter surfaced in telemetry is what distinguishes designed loss from a silent bug, and it is the first thing to look at when users report stale data.

**It has to propagate end to end.** A bounded queue in front of a component with an unbounded queue behind it only moves the accumulation. That is what "flow control" means—TCP's window, HTTP/2's flow control and reactive streams' request(n) are all this same idea made explicit across a boundary.

**Blocking producers can deadlock** if the producer and consumer ever depend on each other, so a bounded blocking queue needs the same lock-ordering discipline as anything else ([CPP-057](#question-cpp-057)), and its waits must include a shutdown predicate ([CPP-069](#question-cpp-069)).

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

A heap allocation is not one cost but four, and they compound:

- **Bookkeeping** - finding a free block, splitting it, updating the free list. Tens to hundreds of nanoseconds, far more than the arithmetic around it.
- **Synchronisation** - the global heap is shared, so allocation from several threads contends. Modern allocators (tcmalloc, jemalloc, mimalloc) fix most of this with per-thread caches, which is why swapping the allocator is sometimes a large free win.
- **Fragmentation** - long-running processes end up with memory they hold and cannot use, so RSS grows while the program believes it freed everything.
- **Cache behaviour** - separately allocated nodes land anywhere, so traversing them is a chain of cache misses. This usually dominates the other three, and it is the real reason `vector` beats `list` ([CPP-044](#question-cpp-044)).

What to do about it, in order: allocate less (reserve, reuse, batch), allocate contiguously (`vector` over node containers), keep objects on the stack or in a small buffer where the size is bounded, and only then reach for a pool or a custom allocator.

The measurement point matters too: allocation cost rarely shows up as one hot function in a profile - it shows as time spread across `malloc`, page faults and cache misses, which is why "it is not in the profile" is not evidence that it is not the problem.

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

**Know what you are paying for** ([CPP-078](#question-cpp-078)): a general-purpose allocation is a few tens of nanoseconds uncontended, involves bookkeeping and potentially a lock, and—more importantly—costs a likely cache miss when you touch the new memory. So the wins come from *removing* allocations, not from making them faster.

**In order of leverage:**

*Reserve.* `v.reserve(n)` before a known-size fill removes every intermediate reallocation and the moves that go with them ([CPP-043](#question-cpp-043)). The same for `unordered_map::reserve` ([CPP-047](#question-cpp-047)). Cheapest change in the list.

*Reuse the buffer.* A `std::string` or `std::vector` declared **outside** the loop and `clear()`ed inside keeps its capacity, so the loop allocates once instead of n times. `clear()` does not release capacity, which is exactly what you want here.

*Avoid temporaries at API boundaries.* `void f(std::string_view)` instead of `const std::string&` removes an allocation per call from every `const char*` caller ([CPP-050](#question-cpp-050)). `std::span` does the same for sequences ([CPP-051](#question-cpp-051)).

*Move, and mark moves `noexcept`.* A move constructor that is not `noexcept` makes `vector` **copy** on reallocation ([CPP-039](#question-cpp-039)) — a silent, large regression.

*Rely on SSO, and know its limit.* Short strings avoid the heap entirely — around 15 characters on libstdc++ and MSVC, 22 on libc++ ([CPP-042](#question-cpp-042)). Concatenating in a loop defeats it; `reserve` on the target restores it.

*Batch the lifetime.* An arena or monotonic buffer for objects that all die together turns n allocations and n frees into one of each; `std::pmr::monotonic_buffer_resource` with `std::pmr::vector` gives this from the standard library, optionally over a stack array. A fixed-size **pool** is the right tool when objects are uniform and churn constantly.

*Compute in place.* `emplace_back` when you have constructor arguments ([CPP-049](#question-cpp-049)); algorithms that write into an existing buffer rather than returning a new container.

**Then measure again**: heaptrack, VTune's memory analysis, or simply counting allocations with a global `operator new` hook in a debug build. Allocation counts are a stable, low-noise metric — much easier to reason about than wall time, and often the fastest way to see whether a change did anything ([CPP-071](#question-cpp-071)).

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

but introduce ABI/versioning concerns ([CPP-095](#question-cpp-095)).

| | Static (`.lib` / `.a`) | Dynamic (`.dll` / `.so`) |
|---|---|---|
| Linked | at build time, into the binary | at load time or on demand |
| Deployment | one file, no version skew | separate files that must match |
| Memory | a copy per process | shared text across processes |
| Update | relink and reship everything | replace one file |
| Optimization | LTO/inlining across the boundary | limited; calls go through the PLT/import table |
| Symbol resolution | at link time — missing symbols are build errors | at load or call time — **runtime** failures |
| Plugins | impossible | the whole point |

**Static libraries are not archives of code that all gets included.** The linker pulls in only the object files that resolve a referenced symbol, which is why a symbol defined in a `.lib` object nobody references is silently absent—and why static initialisers in a static library may never run. That is the classic "my plugin registers itself in a static initialiser and the registry is empty" bug; `--whole-archive` / `/WHOLEARCHIVE` is the blunt fix.

**Dynamic loading has two forms:** load-time linking through an import library, and runtime loading via `LoadLibrary`/`GetProcAddress` or `dlopen`/`dlsym` ([CPP-081](#question-cpp-081)). The second is what makes plugins possible and what a COM in-process server uses.

**The version problems are real and specific:** two modules each statically linking a different version of the same library end up with two copies of its globals; two modules dynamically linking incompatible versions of the same DLL collide. Allocating in one module and freeing in another is undefined if they use different heaps—which is why a DLL boundary should expose a `free` of its own. And on Windows the CRT linkage (`/MT` vs `/MD`) must match across the boundary or you get exactly that heap mismatch ([CPP-093](#question-cpp-093)).

**For an Excel COM Add-In this is not a choice:** the add-in is an in-process DLL loaded into EXCEL.EXE, its bitness must match the host, and its dependencies must resolve at load time—a missing runtime is one of the standard reasons an add-in fails to load with no useful error (see the COM and Excel bank).

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

They are the Windows equivalent of `dlopen`/`dlsym` ([C-018](<./C Language Questions.md#question-c-018>)), and the design question they answer is the same: load at run time instead of linking at build time, so the program starts without the library present and decides for itself what to do.

```cpp
HMODULE h = ::LoadLibraryW(L"plugin.dll");
if (!h) { /* GetLastError */ }
auto fn = reinterpret_cast<int(*)(int)>(::GetProcAddress(h, "entry"));
if (!fn) { /* exported? correct name? */ }
// ... ::FreeLibrary(h) when finished - and not while anything still points inside
```

Four practical points:

- **Name mangling**: a C++ function is exported under its decorated name, so `GetProcAddress` with the source name fails. Plugin entry points are declared `extern "C"` for exactly this reason ([CPP-096](#question-cpp-096)).
- **Reference counting**: each `LoadLibrary` increments a count and each `FreeLibrary` decrements it; the DLL unloads at zero, and unloading while a function pointer into it is still live is a crash.
- **Bitness must match** - a 32-bit DLL will not load into a 64-bit process ([COM-032](<./COM and Excel Questions.md#question-com-032>)).
- **Do not do real work in `DllMain`** ([CPP-082](#question-cpp-082)): it runs under the loader lock, so calling `LoadLibrary` or waiting on anything from there deadlocks.

`LoadLibrary` is also how optional dependencies are handled: try to load, fall back gracefully if absent, rather than failing to start.

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

**The loader lock is the whole explanation.** Windows holds a process-wide lock while running `DllMain`, so anything that needs the loader—or that waits for a thread which needs the loader—deadlocks. And the same lock is held during `LoadLibrary`, so the deadlock can be formed by two unrelated threads.

**The canonical deadlock**, worth being able to draw:

```text
Thread A: LoadLibrary(X)  -> takes loader lock -> X's DllMain -> waits on event E
Thread B: signals E ... but B is inside a DllMain of its own, or needs the loader lock
```

Neither can proceed. In particular, `WaitForSingleObject` on a thread handle inside `DllMain` is a guaranteed hang, because `DLL_THREAD_DETACH` for that thread also needs the loader lock.

**The forbidden list, and why each item is on it:**

- `LoadLibrary`/`FreeLibrary` — reentrant loader lock;
- `CoInitializeEx` and any COM call — COM loads DLLs and may pump messages;
- creating a thread *and waiting for it* — the new thread's `DLL_THREAD_ATTACH` needs the lock;
- any synchronization that can block on another thread;
- registry, networking, or anything that may load a DLL indirectly (including the CRT's lazy initialisation of some facilities);
- calling into another DLL whose `DllMain` has not run yet — **initialisation order across DLLs is not guaranteed**.

**What is safe:** `DisableThreadLibraryCalls` (to stop `DLL_THREAD_ATTACH`/`DETACH` noise), storing the module handle, and trivial variable initialisation. Everything else belongs in an explicit init function that the host calls later.

**For a COM Add-In that is exactly the design**: `DllMain` does nothing, and real initialisation happens in `IDTExtensibility2::OnConnection`, which Excel calls on its own thread with COM already up (see the COM and Excel bank). Shutdown symmetrically belongs in `OnBeginShutdown`/`OnDisconnection`, not in `DLL_PROCESS_DETACH`—which may not even run, and runs with the process already tearing down.

**C++ static initialisers in a DLL run from `DllMain`** under the same lock, so a global object whose constructor takes a lock, starts a thread or touches COM has the same problem without any `DllMain` of your own being visible. That is a strong argument for lazy, function-local statics at a DLL boundary.

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

**Getting the dump is step zero, and it has to be arranged in advance.** Windows Error Reporting can be configured to write full dumps to a directory (`HKLM\...\Windows Error Reporting\LocalDumps`), or the add-in can install `SetUnhandledExceptionFilter` and call `MiniDumpWriteDump` itself—with `MiniDumpWithFullMemory` if you want heap contents. Without symbols the dump is nearly useless, so **archive the PDBs for every shipped build** and keep a symbol server; matching is by GUID and age, not by filename.

**Reading it in WinDbg**, with the commands that actually get used:

```text
!analyze -v        ; first-pass triage: exception code, faulting module, likely cause
~*k                ; all thread stacks - essential, the crashing thread often is not the cause
!locks             ; critical section ownership
lm                 ; loaded modules and whether symbols matched
.ecxr              ; switch to the exception context before walking the stack
!heap -p -a <addr> ; who allocated this block, if page heap is on
```

**Interpret the exception code rather than just reporting it.** `0xC0000005` at a small address is a null dereference; at `0xDDDDDDDD`/`0xFEEEFEEE` it is use-after-free with debug heap fill patterns; `0xC0000374` is heap corruption, which means the damage happened *earlier* and the stack is innocent. Corruption is the case where the dump tells you the least—that is when you turn on **Application Verifier / page heap** (`gflags /p /enable excel.exe /full`), which makes the crash happen at the moment of the bad write instead of later.

**Then reproduce under instrumentation**: ASan is available in MSVC now and works on an add-in DLL; UBSan for the undefined-behaviour class; TSan on Linux if any part of the code can be built and exercised there ([CPP-016](#question-cpp-016)).

**The plugin-specific hypotheses to check early**, because they are the usual causes: an exception escaping into the host's call stack ([CPP-089](#question-cpp-089)); an object-model call from the wrong thread (see the COM and Excel bank); a reference released twice, or used after release; a callback firing after shutdown began; and a CRT or ABI mismatch between the add-in and its dependencies ([CPP-095](#question-cpp-095)).

**Say the non-technical part too:** a plugin crash lands on the *host's* reputation, so the shipped build needs a catch-all at every boundary, structured logging with a session id, and a version banner—so that the next report arrives with enough context to skip steps 1 to 8.

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

**A hang dump is the right artifact**, and the key is that all threads are already frozen in the deadlock—you do not need to catch it live. Attach non-invasively or use `procdump -ma -h` (which triggers on window hang) / Task Manager's "Create dump file".

**What to do with it:**

```text
~*k                ; every thread's stack - look for two or more blocked in a lock/wait
!locks             ; critical sections: owner thread and waiters -> read the cycle directly
!cs -l             ; locked critical sections only
!uniqstack         ; collapse identical stacks in a large thread pool
```

Two threads each sitting in `RtlEnterCriticalSection`, each owning what the other wants, is the textbook picture. On Linux the equivalent is `gdb -p`, `thread apply all bt`, and looking for `__lll_lock_wait`.

**The shapes that are not two mutexes**, and which you should scan for by name:

- the **UI/STA thread blocked in a wait** while a worker waits for a marshaled call into that apartment—COM's `CoWaitForMultipleHandles` frames and `OleMainThreadWndClass` in the stack are the tell (see the COM and Excel bank);
- a lock held across a callback into unknown code, so the ordering is not yours to control ([CPP-057](#question-cpp-057));
- a condition variable whose predicate never becomes true because the notifier exited and no terminal state is in the predicate ([CPP-059](#question-cpp-059)) — this looks like a deadlock but no lock is held;
- self-deadlock on a non-recursive mutex;
- a thread pool exhausted by tasks that wait on other tasks in the same pool.

**Prevention beats diagnosis**, and this is the part worth saying: ThreadSanitizer reports lock-order inversions *even on runs that do not deadlock*, so it turns a rare production hang into a deterministic CI failure. Clang's thread-safety annotations enforce an order at compile time. And `try_lock` with a timeout plus a log line converts a silent hang into a diagnosable error ([CPP-057](#question-cpp-057)).

**Instrument for it in shipped builds**: a watchdog that dumps all stacks when a heartbeat stops is often the only way to get the artifact from a user's machine.

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

**The framing that gets you to the answer: what accumulates?** A bug that takes hours is almost always a monotonic quantity crossing a threshold, or a rare event whose probability per operation is tiny. So the first move is not debugging—it is **measuring the trend**.

**Instrument and graph over time:**

| Metric | Points at |
|---|---|
| private bytes / RSS | leak or fragmentation |
| handle count, GDI/user objects | handle leak — a hard 10 000 limit per process on Windows |
| thread count | threads created and never joined |
| queue depth | consumer falling behind ([CPP-077](#question-cpp-077)) |
| COM reference counts / object counts | a reference never released — the usual cause of a lingering EXCEL.EXE |
| allocation count vs free count | churn and imbalance |

**Leak versus fragmentation is a real distinction**: a flat allocation count with growing RSS is fragmentation, not a leak, and the fix is different—arena or pool allocation for the churning size class ([CPP-079](#question-cpp-079)).

**Then take the artifacts.** Two dumps an hour apart, diffed, show what grew: `!heap -s` for heap summaries, `!heap -flt s <size>` to list blocks of the suspicious size, UMDH or Application Verifier's leak tracking for allocation stacks. On Linux, heaptrack or `valgrind --leak-check` on a scaled-down run.

**Compress time rather than waiting.** Accelerate the clock, raise the event rate, shrink the buffers and thresholds, and run the scenario in a loop overnight. A bug that needs eight hours at production rate may need eight minutes at fifty times the rate—and if it does not reproduce under acceleration, that itself is information: the trigger is wall-clock or calendar-based (a timer wraparound, a daily rollover, a certificate, a `time_t` boundary).

**Non-accumulating candidates** that also look like this: an integer or counter overflow at a specific count; a rare race whose window is nanoseconds but which fires once every few million operations ([CPP-053](#question-cpp-053)); a timer or sequence number wrapping; and a scheduled event—a midnight rollover, a DST change—that happens to coincide with "after several hours".

**The practical prerequisite** is that the shipped build can tell you any of this: counters exposed for sampling, structured logs with timestamps and a session id, and a way to request a dump from a user's machine without a developer present. Adding that instrumentation is usually the first real task, not a distraction from the bug.

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

`new T(args...)` is two operations fused: it calls `operator new(sizeof(T))` to obtain storage, then runs a constructor in it, and returns a `T*`. `malloc(n)` does only the first half and returns `void*`—no type, no constructor, no lifetime started.

| | `new` / `delete` | `malloc` / `free` |
|---|---|---|
| Construction | runs constructor / destructor | none |
| Result type | `T*` | `void*` (needs a cast in C++) |
| Size | computed from the type | you compute bytes yourself |
| Failure | throws `std::bad_alloc` | returns `nullptr` |
| Alignment | respects `alignof(T)`, including over-aligned types since C++17 | only up to `max_align_t` |
| Customisation | `operator new` is replaceable globally and per class | replaced only by linker tricks |
| Array form | `new[]` / `delete[]` | `calloc`/`realloc`, no element awareness |

**Never mix the families.** `free` on a `new`ed object skips the destructor and hands the pointer to the wrong allocator; `delete` on a `malloc`ed block runs a destructor on an object that was never constructed. Both are undefined behaviour even when the underlying heap happens to be the same one. `delete` vs `delete[]` is the same rule one level down—`new[]` may store an element count in front of the block, so the wrong form corrupts the heap.

**`realloc` has no C++ equivalent** for a good reason: it moves bytes, and a non-trivially-copyable object cannot be relocated by `memcpy`. That is why `std::vector` grows by allocating, move-constructing each element and destroying the originals, instead of calling `realloc`.

**The seam between them is `operator new` and placement new.** `operator new`/`operator delete` are ordinary replaceable functions—overriding them per class is how pool allocators and instrumentation hook in—and placement new constructs an object in storage you already own, whatever its source ([CPP-088](#question-cpp-088)). That is the honest way to build on top of `malloc`, an arena, or a shared-memory mapping.

**In application code you should be writing neither.** `std::make_unique`, `std::make_shared` and the containers cover almost everything; a bare `new` in a review is a question to answer, and `malloc` in C++ shows up legitimately only at a C API boundary, where the allocation must be freed by the same library that made it.

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

The reason they are different operations is that `new[]` usually stores the element count somewhere - often in a header just before the returned pointer - so `delete[]` knows how many destructors to run. `delete` on that pointer runs one destructor and frees from the wrong address; `delete[]` on a scalar allocation reads a count that was never written.

It is undefined behaviour in both directions, and it frequently *appears* to work for trivially destructible types, which is why it survives in code until someone adds a destructor.

```cpp
int*  a = new int[10];   delete a;      // UB, usually "works"
Foo*  b = new Foo[10];   delete b;      // UB, leaks 9 destructors
std::unique_ptr<Foo[]> c{new Foo[10]};  // correct: calls delete[]
```

The practical rule is to not write either: `std::vector` for a dynamic array, `std::unique_ptr<T[]>` or `std::make_unique<T[]>(n)` when you truly need raw ownership. `unique_ptr<T[]>` exists precisely because the single-object specialisation would call the wrong delete.

Worth knowing for completeness: `new` throws `std::bad_alloc` on failure while `new (std::nothrow)` returns null, and the matching `operator delete` is chosen at compile time from the static type - so deleting a derived object through a base pointer without a virtual destructor is a third variant of the same class of bug ([CPP-021](#question-cpp-021)).

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

It separates the two things ordinary `new` does together - allocate, then construct - and performs only the second, in storage you already have.

```cpp
alignas(T) std::byte buf[sizeof(T)];
T* p = new (buf) T{args...};   // construct in place, no allocation
p->~T();                        // destroy explicitly - no delete
```

Calling `delete` on that pointer is undefined behaviour, because the storage did not come from `operator new`. The destructor must be invoked by name, and forgetting it is a leak that no allocator tracks.

The requirement people miss is **alignment**: the buffer must be suitably aligned for `T`, which is what `alignas` above is for. A `char` array without it is undefined behaviour on architectures that care, and silently slower on x86.

Where it is actually used: inside containers, so `vector` can have capacity without constructed elements ([CPP-041](#question-cpp-041)); inside `std::optional` and `std::variant`, which hold storage and construct into it on demand; in memory pools and arenas; and in embedded code that must place an object at a fixed hardware address.

Worth knowing that `std::construct_at` (C++20) and `std::destroy_at` are the modern spellings, and that they work in `constexpr` contexts where placement `new` did not.

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

`std::terminate` is the runtime's answer to "the exception machinery has reached a state with no defined continuation". It calls the current handler installed by `std::set_terminate`, whose default is `std::abort`.

**The triggers worth being able to list:**

- an exception escapes a function marked `noexcept` (the standard does not even require the stack to be unwound—so destructors may never run);
- an exception leaves a destructor *while another exception is already propagating*—not "a destructor threw", which is legal in isolation, but two exceptions in flight at once ([CPP-090](#question-cpp-090));
- `throw;` with no exception currently being handled;
- an exception escapes `main`, a constructor or destructor of a namespace-scope object, or a thread's entry function;
- a `std::thread` is destroyed or move-assigned while still joinable—the single most common real-world hit, and nothing to do with exceptions at all;
- a `std::condition_variable` destructor with waiters, or an exception escaping a `noexcept` move constructor during a `vector` reallocation.

**Note the asymmetry with `noexcept` in destructors.** Destructors are implicitly `noexcept` since C++11, so a destructor that throws terminates the process *even without* a second exception, unless it is explicitly marked `noexcept(false)`. That change broke some pre-C++11 code that relied on throwing from a destructor.

**A terminate handler must not return**, and must not throw; if it does, `abort` is called anyway. Its legitimate use is a last-gasp diagnostic, and `std::current_exception()` is still usable inside it, which lets you log the actual exception type and message before dying:

```cpp
std::set_terminate([] {
    if (auto e = std::current_exception()) {
        try { std::rethrow_exception(e); }
        catch (const std::exception& ex) { log_fatal(ex.what()); }
        catch (...)                      { log_fatal("unknown exception"); }
    }
    std::abort();
});
```

Keep it minimal: the process is already in an undefined state, so allocating or taking locks there can hang instead of crashing.

**This is not error handling.** The design response is to make the triggers unreachable—destructors and swap/move operations `noexcept` and actually non-throwing, `join`/`detach` on every thread path (or `std::jthread`), and a catch-all at every boundary the exception must not cross: a thread entry point, a callback invoked by C or COM code, and any function exported across an ABI ([CPP-091](#question-cpp-091)).

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

Only objects whose construction **completed** are destroyed - which is the same rule that decides what happens when a constructor throws ([CPP-177](#question-cpp-177)). A partially constructed object has no destructor call, so anything it acquired raw is lost.

What unwinding does *not* clean up is the point of the answer: raw `new`, a `fopen` without a wrapper, a manually locked mutex, a handle from a C API. Every one of those leaks on the exceptional path unless an object owns it - which is the whole argument for RAII, stated as a mechanism rather than as advice.

Cases where unwinding does not happen at all, worth having ready:

- `std::terminate` - an exception escaping `noexcept`, or one thrown during unwinding ([CPP-089](#question-cpp-089)) - aborts without unwinding the remaining frames.
- `std::exit` runs static destructors but not automatic ones; `std::abort` and `_exit` run nothing.
- An exception escaping `main` or a thread's entry function calls `terminate`, and whether anything was unwound first is implementation-defined.

So a destructor is not a place to put behaviour the program depends on: it runs on every normal and exceptional path *through* the scope, and not at all if the process is terminated.

`-fno-exceptions` builds, common in embedded, remove the machinery entirely - worth mentioning, because it changes what error handling can look like on that target.

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

There are four levels, and naming all four with an example of each is what the question is really asking:

| Guarantee | Promise if an operation throws | Typical example |
|---|---|---|
| No-throw (`noexcept`) | It cannot throw at all | Destructors, `swap`, move operations that should be usable by containers |
| Strong | State is exactly as before - the operation either fully happened or did not | `vector::push_back` when the element type is nothrow-movable or copyable |
| Basic | Invariants hold and nothing leaks, but the state may have changed | `vector::insert` in the general case |
| None | Anything may be true afterwards, including a corrupt object | What you get by default if you never thought about it |

The mechanism behind the strong guarantee is always the same: **do all the work that can fail first, into somewhere new, then commit with an operation that cannot fail.** Copy-and-swap is that pattern written down - build a copy, then `swap`, which is `noexcept`.

This is also why move constructors should be `noexcept` ([CPP-007](#question-cpp-007)): `vector` reallocation must keep the strong guarantee, so if moving elements could throw it copies them instead. One missing `noexcept` silently turns every reallocation from moves into copies, and nothing reports it.

The practical decision: aim for the basic guarantee everywhere as a baseline, the strong guarantee where a caller would otherwise have to clean up after a failure, and `noexcept` where the standard requires it or where failure genuinely cannot happen. Promising `noexcept` and then throwing is worse than promising nothing - it calls `std::terminate` ([CPP-089](#question-cpp-089)).

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

```cpp
class Buffer {
    char* data_ = nullptr;
    size_t n_ = 0;
public:
    friend void swap(Buffer& a, Buffer& b) noexcept {   // never throws - that is the point
        using std::swap;
        swap(a.data_, b.data_);
        swap(a.n_,    b.n_);
    }
    Buffer& operator=(Buffer rhs) noexcept {   // by VALUE: the copy happens in the parameter
        swap(*this, rhs);
        return *this;
    }                                          // rhs destructs, taking the old state with it
};
```

**Three properties fall out of those five lines:**

*Strong exception safety.* Any allocation happens while constructing the parameter, **before** `*this` is touched. If it throws, the object is untouched ([CPP-091](#question-cpp-091)). Contrast the naive form—`delete data_; data_ = new char[rhs.n_];`—which destroys the old state first and leaves a wrecked object if the allocation throws.

*Self-assignment works for free.* No `if (this != &rhs)` guard is needed, because the copy was already made.

*One operator covers copy and move assignment.* Pass an lvalue and the parameter is copy-constructed; pass an rvalue and it is move-constructed. Which is also the catch: it is then a *unified* assignment operator, and you cannot declare a separate `operator=(Buffer&&)` alongside it—the call would be ambiguous.

**The cost, and why it is not the automatic answer any more.** Copy-and-swap always allocates fresh storage, so assigning into an object that already has enough capacity cannot reuse it—`std::string`'s and `std::vector`'s assignment deliberately do not use this idiom for exactly that reason. And separate copy/move assignment operators, which is what the Rule of Five gives you, are usually faster.

**So when to reach for it:** a class that owns a resource by hand, where correctness matters more than the last allocation, and where the strong guarantee is worth stating. If the class can instead be built from members that manage themselves—Rule of Zero ([CPP-001](#question-cpp-001))—you need none of this.

**The `swap` must be `noexcept`** or the guarantee evaporates, and it should be a hidden friend found by ADL rather than a specialization of `std::swap` ([CPP-020](#question-cpp-020)).

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

**Two different rules wear this name**, and separating them is what a good answer does.

*Rule 1 — one definition per translation unit*, for any entity used. Straightforward; violating it is a compile error.

*Rule 2 — one definition per **program*** for non-inline functions and variables. Violating it is a link error: "multiple definition of". This is why a function body in a header needs `inline` and a variable needs `inline` (C++17) or `extern` plus one definition.

*Rule 3 — where an entity may legally appear in several translation units* (inline functions, templates, class definitions, `constexpr` variables), **every definition must be token-for-token identical and mean the same thing**. This is the dangerous one, because violating it is **ill-formed, no diagnostic required**: the linker picks one definition arbitrarily and discards the rest, and the program silently does something else ([CPP-017](#question-cpp-017)).

**How Rule 3 gets broken in practice:**

```cpp
// a.cpp
#define NDEBUG
#include "widget.h"     // struct Widget { int a; };           - sizeof 4

// b.cpp
#include "widget.h"     // struct Widget { int a; int debug_; }; - sizeof 8
```

Different macros, different compiler flags (`/MT` vs `/MD`, `-D_GLIBCXX_DEBUG` on one library only, different `-std`), or different packing pragmas across translation units all produce two incompatible layouts of one class. One object file allocates 4 bytes and the other writes 8. The crash appears somewhere unrelated, much later.

**That is also the single most common cause of "it links but crashes" across a DLL boundary**, which makes it the practical companion to ABI ([CPP-095](#question-cpp-095)).

**How to defend:** identical compiler flags and preprocessor definitions for every translation unit that shares a header; no macro-conditional class layout in public headers; `-Wodr` with LTO on GCC, and `-flto` generally, because LTO can actually see the mismatch; `-fvisibility=hidden` plus explicit exports to keep symbol collisions from silently merging different entities; and in a large codebase, one build system that guarantees consistency rather than per-target flag lists.

**`inline` does not mean "inline me"**—it means "this definition may appear in multiple translation units, merge them" ([CPP-094](#question-cpp-094)). That linkage meaning is precisely an ODR mechanism.

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

The keyword's job is linkage, not optimisation. It tells the linker that multiple identical definitions of this entity are expected and must be folded into one, instead of being a one-definition-rule violation ([CPP-093](#question-cpp-093)). That is why a function defined in a header needs it and a function defined in a `.cpp` does not.

Whether the compiler actually inlines a call is a separate decision it makes from size and profitability, and it will inline functions never marked `inline` and decline to inline ones that are.

Two consequences:

- A member function defined **inside** the class body is implicitly `inline`, which is why header-only classes work without the keyword appearing anywhere.
- **`inline` variables** (C++17) apply the same rule to data, which is what finally allows a header-only library to define a global without the old trick of a function returning a static reference ([CPP-172](#question-cpp-172)).

If you genuinely need to influence the optimiser, the tools are `[[gnu::always_inline]]` / `__forceinline` and `[[gnu::noinline]]` - non-standard, and worth using only with a measurement, because forcing inlining of a large function inflates code size and can cost more in instruction-cache misses than the call ever cost.

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

**API vs ABI is the distinction to lead with**: API compatibility means the code still *compiles*; ABI compatibility means an already-compiled binary still *runs* correctly against the new build. You can break ABI without breaking API, and that is the failure mode that produces no error at all—just corruption.

**Changes that silently break ABI while the header still compiles:**

- adding a data member, reordering members, changing a member's type — every offset moves;
- adding the **first** virtual function (the object grows a vptr) or adding a virtual function anywhere but the end (vtable slots shift);
- changing a default argument compiled into the caller;
- changing an `inline` function's body that callers have already inlined;
- changing anything about a type passed **by value** across the boundary;
- switching compiler, standard library, or even `-D_GLIBCXX_USE_CXX11_ABI` — the `std::string` ABI change of GCC 5 is the standard war story.

**Which is why the standard library is not ABI-stable across toolchains.** Passing `std::string`, `std::vector` or an exception across a DLL boundary requires both sides to have been built with the same compiler, the same standard-library version and the same flags. In practice that constraint is unrealistic, so a plugin boundary is designed to avoid it.

**The techniques for a stable boundary**, which is what the interviewer is really after:

- a **C ABI** at the seam: `extern "C"` functions, opaque handles, POD structs with explicit sizes, an integer error code instead of exceptions ([CPP-096](#question-cpp-096));
- or a **pure-virtual interface** with a factory function, never adding to an existing interface—version it by adding `IFoo2`, which is exactly COM's model;
- **pimpl** to keep private members out of the header so the object's size never changes ([CPP-040](#question-cpp-040));
- allocate and free on the same side of the boundary, since the two sides may use different heaps;
- never let an exception cross ([CPP-089](#question-cpp-089)).

**For an Excel COM Add-In this is not academic**: the add-in DLL is loaded into Excel's process, and COM exists precisely because it defines a language-neutral, version-tolerant ABI over vtables and GUIDs (see the COM and Excel bank).

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

**Why it must exist:** C++ allows `void f(int)` and `void f(double)` in one program, so the linker needs distinct symbols. The mangled name therefore encodes the function's namespace, class, parameter types, `const`/ref qualifiers and template arguments—everything the signature contains except the return type (which is why you cannot overload on return type alone).

```text
void mylib::process(int, const char*)
  → GCC/Clang (Itanium ABI): _ZN5mylib7processEiPKc
  → MSVC:                     ?process@mylib@@YAXHPEBD@Z
```

The schemes are completely different and **not standardised**, which is one of the reasons a GCC-built object cannot link against an MSVC-built one ([CPP-095](#question-cpp-095)).

**Where this becomes a practical skill:** decoding a linker error. `undefined reference to '_ZN5mylib7processEiPKc'` means the *declaration* you compiled against does not match any *definition* that was linked—a missing library, or the definition built with a different signature, namespace, or `const`. `c++filt` on Linux and `undname` on Windows turn the symbol back into a signature, and `nm -C` / `dumpbin /SYMBOLS` list what a binary actually exports.

**`extern "C"` also implies no overloading**, since the symbol is just the bare name—so only one function of that name may have C linkage. The idiomatic header guard is:

```cpp
#ifdef __cplusplus
extern "C" {
#endif
    int plugin_init(void* ctx);
#ifdef __cplusplus
}
#endif
```

**What it does and does not change:** it changes linkage and name only. The function body is still C++—it can throw, use RAII, and call anything—so an `extern "C"` entry point must still catch everything before returning, because an exception crossing into C code is undefined ([CPP-089](#question-cpp-089)).

**Where you meet it:** exporting a plugin or DLL entry point, calling into a C library, and `DllGetClassObject`/`DllRegisterServer` in a COM server—all of which must be `extern "C"` and are usually listed in a `.def` file as well.

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

---

# 15. OOP Principles and Polymorphism

## Question CPP-167

[↑ Back to question index](#question-index)

### Question CPP-167 — What are the principles of OOP, and what does each one actually buy you?

**Short answer**

- **Encapsulation**: state is private and reachable only through operations the type defines, so the class can guarantee its own invariants instead of trusting every caller.
- **Abstraction**: the interface describes what something does; the implementation is free to change. **Inheritance**: a derived type is usable where the base is expected. **Polymorphism**: one call site dispatches to different behaviour.
- The four are usually recited; what an interviewer is listening for is what each one *costs*, because inheritance in particular is the one that is overused.

**Details and nuances**

Encapsulation is the load-bearing one. Once the only way to change state is through member functions, the class can check its invariant in one place, and a bug in that invariant has one suspect rather than every line that touched a public field. In C++ it is also what makes RAII possible at all: the constructor establishes the invariant and the destructor releases what it acquired, and neither can be skipped.

Abstraction and encapsulation are often conflated. The distinction worth stating: encapsulation hides *data*, abstraction hides *design*. An opaque handle in C ([C-013](<./C Language Questions.md#question-c-013>)) is encapsulation without any language support for it; a pure virtual interface is abstraction.

The honest part of this answer is inheritance. It couples the derived class to the base's implementation, so a change to the base can break a derived class nobody remembered existed - the fragile base class problem. Prefer composition ([CPP-142](#question-cpp-142)), and use public inheritance only where the substitution in [CPP-168](#question-cpp-168) genuinely holds.

Worth adding in C++ specifically: none of this requires classes with virtual functions. Templates give abstraction and polymorphism with no runtime cost and no inheritance at all, which is why C++ style diverged from the Java-flavoured OOP these four words come from.

[↑ Back to question index](#question-index)

---

## Question CPP-168

[↑ Back to question index](#question-index)

### Question CPP-168 — IS-A vs HAS-A - how do you decide?

**Short answer**

- IS-A is public inheritance: every derived object must be usable anywhere the base is expected. HAS-A is composition: the type holds another as a member and forwards what it chooses.
- The test is not English - "a car is a vehicle" proves nothing. The test is behavioural substitutability: can a caller holding a `Base&` do everything the base promises and still be correct?
- If the answer is "yes, except…" then it is not IS-A, and composition is the correct relationship no matter how natural the sentence sounds.

**Details and nuances**

That test is the Liskov substitution principle, and the canonical counterexample is square and rectangle: a square *is a* rectangle in geometry, and it is not one in code, because `setWidth` and `setHeight` on a rectangle promise independent dimensions and a square cannot keep that promise. Code written against `Rectangle&` breaks when handed a `Square`, and nothing in the type system warns you.

Concrete symptoms that IS-A was the wrong call:

- A derived class overrides a method to do nothing, or to throw.
- A derived class strengthens a precondition - it accepts less than the base promised to accept.
- Callers check the dynamic type to decide what to do, which means the abstraction is not carrying its weight.

C++ also gives you **private inheritance**, which is HAS-A expressed through inheritance - useful for the empty base optimisation or when you need to override a virtual, and not a substitutability claim. Saying that distinguishes someone who knows C++ from someone reciting general OOP.

[↑ Back to question index](#question-index)

---

## Question CPP-169

[↑ Back to question index](#question-index)

### Question CPP-169 — What kinds of polymorphism does C++ have?

**Short answer**

- **Subtype (runtime)**: virtual functions dispatched through the vtable - the type is chosen at runtime, at the cost of an indirect call and no inlining.
- **Parametric (compile-time)**: templates - one implementation instantiated per type, resolved at compile time, fully inlinable, at the cost of code size and error messages.
- **Ad-hoc**: overloading and specialisation, resolved at compile time by the argument types. Plus **CRTP**, which gets static dispatch with an inheritance-shaped syntax, and `std::variant` with `std::visit` for a closed set of types.

**Details and nuances**

```cpp
struct Shape { virtual double area() const = 0; };          // subtype, runtime

template <class T> double area(const T& s) { return s.area(); }   // parametric

template <class D> struct Base {                             // CRTP, static
    double area() const { return static_cast<const D&>(*this).areaImpl(); }
};

using AnyShape = std::variant<Circle, Square>;               // closed set
double area(const AnyShape& s) { return std::visit([](auto& x){ return x.area(); }, s); }
```

The decision rule worth stating: **is the set of types open or closed, and is it known at compile time?** Open and runtime - plugins, a set that grows without recompiling - needs virtual. Closed and known - a handful of message kinds - is better as a `variant`, because the compiler then checks that every case is handled and there is no allocation or indirection. Compile-time with no need for a common base at all is a template.

The cost that decides it in performance-sensitive code: a virtual call cannot be inlined, so a tiny function called in a loop pays both the indirect call and the lost optimisation around it. That is exactly why CRTP exists, and also why reaching for it before measuring is premature.

[↑ Back to question index](#question-index)

---

## Question CPP-170

[↑ Back to question index](#question-index)

### Question CPP-170 — Abstract class, pure virtual function - how do you model an interface in C++?

**Short answer**

- A pure virtual function (`= 0`) makes the class abstract: it cannot be instantiated, and a derived class must override it to become concrete.
- C++ has no `interface` keyword, so an interface is a class with only pure virtual functions, no data, and a **virtual (or protected non-virtual) destructor** - without that, deleting through a base pointer is undefined behaviour.
- A pure virtual function may still have a definition, which is occasionally useful as a default implementation a derived class calls explicitly.

**Details and nuances**

```cpp
class ICodec {
public:
    virtual ~ICodec() = default;                 // required for deletion via base*
    virtual bool encode(Span in, Buffer& out) = 0;
    virtual const char* name() const = 0;
};
```

Two refinements worth having ready. If the interface is never deleted polymorphically, a **protected non-virtual destructor** expresses that and costs nothing - it prevents `delete base_ptr` at compile time instead of leaving it undefined.

And the **non-virtual interface** idiom inverts the usual shape: the public function is non-virtual and does the invariant checking, logging and locking, then calls a private virtual for the part that varies. That keeps the contract in one place instead of relying on every derived class to remember it.

Worth naming the limit too: C++ does not enforce that an interface has no state, and it allows multiple inheritance of interfaces without the diamond problem only because they carry no data ([CPP-027](#question-cpp-027) is where that stops being true).

[↑ Back to question index](#question-index)

---

# 16. Traps That Pass Review

## Question CPP-171

[↑ Back to question index](#question-index)

### Question CPP-171 — Why is `std::vector<bool>` not a container of `bool`?

**Short answer**

- It is a specialisation that packs one bit per element, so it does not store `bool` objects and `operator[]` cannot return `bool&` - it returns a proxy object instead.
- That breaks the container contract: `&v[0]` does not give you a `bool*`, `auto x = v[i]` deduces the proxy rather than `bool`, and it does not satisfy the requirements a generic algorithm may rely on.
- Use `std::vector<char>`, `std::deque<bool>`, `std::bitset` for a fixed size, or `std::vector<std::uint8_t>` - and reach for the specialisation only when the memory saving is the actual point.

**Details and nuances**

```cpp
std::vector<bool> v{true, false};
auto x = v[0];      // x is a proxy, not bool
v[0] = false;       // x now observes false - it is a reference into the bitfield
bool* p = &v[0];    // does not compile
```

The `auto` case is the one that gets people, because the proxy keeps referring to the vector: copying what looks like a value actually copies a reference, and the value changes underneath. `auto x = static_cast<bool>(v[0])` or `bool x = v[0]` is the fix.

It is also not thread-safe per element the way other containers are: two threads writing different elements touch the same underlying word, so it is a data race where `vector<char>` would be fine. That one is easy to miss and hard to debug.

This is widely regarded as a standardisation mistake kept for compatibility, which is worth saying - it explains why the language has a rule that looks arbitrary.

[↑ Back to question index](#question-index)

---

## Question CPP-172

[↑ Back to question index](#question-index)

### Question CPP-172 — What is the static initialization order fiasco, and what fixes it?

**Short answer**

- Namespace-scope objects with dynamic initialisation are initialised in an unspecified order *across* translation units, so one global that uses another during its own construction may see it unconstructed.
- Within one translation unit the order is the order of definition, which is why the bug appears only after someone links the same code in a different order.
- The fix is the construct-on-first-use idiom: put the object in a function-local `static` and return a reference, so it is constructed on the first call rather than at an unspecified point before `main`.

**Details and nuances**

```cpp
// Broken: Logger may not exist yet when Config's constructor runs.
Logger  g_logger;          // some.cpp
Config  g_config;          // other.cpp - constructor calls g_logger

// Fixed: constructed on first use, in a defined order.
Logger& logger() { static Logger instance; return instance; }
```

Function-local statics are also the answer to a second question that often follows: since C++11 their initialisation is **thread-safe** - the standard requires concurrent callers to block until the first initialisation completes, which is why "magic statics" replaced hand-written double-checked locking. Double-checked locking written by hand before C++11 was famously broken without atomics, and there is no reason to write it now.

The remaining trap is the mirror image: the **destruction** order is the reverse of construction, so a static that logs from its destructor may find the logger already destroyed. Where that matters, deliberately leak - `static Logger* p = new Logger;` - because a never-destroyed object cannot be used after destruction.

The broader point worth making: a shared mutable global is the actual problem, and the idiom only makes the lifetime defined. Dependency injection removes the question instead of answering it.

[↑ Back to question index](#question-index)

---

## Question CPP-173

[↑ Back to question index](#question-index)

### Question CPP-173 — In what order are function arguments evaluated, and why did that leak before C++17?

**Short answer**

- The order in which function arguments are evaluated is **unspecified**, and before C++17 the evaluations of different arguments could also be *interleaved*.
- So `f(std::shared_ptr<T>(new T), may_throw())` could allocate the `T`, then run `may_throw()`, then throw - with the raw pointer owned by nobody. That is a leak the code does not look like it has.
- `std::make_shared<T>()` fixes it by making allocation and ownership one indivisible step, which is the real reason to prefer it over `shared_ptr<T>(new T)`.

**Details and nuances**

C++17 tightened this: each argument is now indivisibly sequenced with respect to the others, though the *order* between them is still unspecified. So the leak is gone, and code that depends on which argument runs first is still wrong.

The same family of traps, worth naming together:

- `i = i++ + 1` and friends were undefined before C++17 and are merely unsequenced-or-defined now depending on the form; the practical rule is to not modify a variable twice in one expression.
- `f(g(), h())` - `g` and `h` may run in either order, so a side effect in one that the other depends on is a bug that appears when the compiler version changes.
- `std::cout << f() << g()` has the same problem; the chained `<<` calls are ordered, the argument evaluations are not (before C++17).

The takeaway to state: if the order matters, put it in statements. A named local per step costs nothing and removes the whole class.

[↑ Back to question index](#question-index)

---

## Question CPP-174

[↑ Back to question index](#question-index)

### Question CPP-174 — What goes wrong when signed and unsigned meet in a comparison or a loop?

**Short answer**

- The usual arithmetic conversions turn the signed operand unsigned, so `-1 < v.size()` is **false**: `-1` becomes a very large unsigned value.
- A reverse loop written as `for (size_t i = v.size() - 1; i >= 0; --i)` never terminates, because an unsigned `i` is always `>= 0`; and on an empty vector `v.size() - 1` is already the maximum value, so the first iteration indexes out of bounds.
- Enable `-Wsign-compare` (part of `-Wall`/`-Wextra`), compare like with like, and use indices only where an iterator or a range-based loop will not do.

**Details and nuances**

```cpp
for (std::size_t i = v.size(); i-- > 0; )   // correct reverse loop
    use(v[i]);

for (auto it = v.rbegin(); it != v.rend(); ++it)   // clearer
    use(*it);
```

The `i-- > 0` form works because the comparison uses the value before the decrement, so the loop ends after `i` was 0 and the body sees `i` from `size()-1` down to `0` without ever going negative.

Two related surprises worth having ready. `v.size() - v.capacity()` is unsigned arithmetic, so a "negative" difference is an enormous positive number. And unsigned overflow is *defined* to wrap while signed overflow is undefined behaviour, which is why the compiler may optimise away a signed overflow check you wrote and will not do the same for unsigned.

C++20 added `std::cmp_less` and its family for exactly this: they compare integers by value regardless of signedness, which is the correct answer when you genuinely must mix them. `std::ssize` returns a signed size for the same reason.

[↑ Back to question index](#question-index)

---

## Question CPP-175

[↑ Back to question index](#question-index)

### Question CPP-175 — What does `const` on a member function actually guarantee?

**Short answer**

- Only that the object's own bits are not modified through `this` - the constness is shallow. A `const` member function may freely modify whatever a pointer or reference member points at, because the pointer is const, not the pointee.
- It also does not guarantee thread safety by itself, although the standard library's own types promise that concurrent `const` operations are safe, and that is the convention worth following in your own code.
- `mutable` deliberately breaks the guarantee for members that are not part of the observable state - a cache, a memoised value, a mutex.

**Details and nuances**

```cpp
class Widget {
    int*        data_;      // const method may write *data_
    std::vector<int> items_; // const method may not modify items_
    mutable std::mutex m_;   // lockable from a const method, by design
};
```

This is the difference between **bitwise const**, which is what the compiler enforces, and **logical const**, which is what the reader assumes. A `const` method that mutates through a pointer member is bitwise-const and not logically const, and nothing warns about it - which makes it exactly the sort of thing that survives review.

The thread-safety point deserves care because it is often stated too strongly: the language guarantees nothing. What exists is a convention, honoured by the standard library, that `const` means "safe to call concurrently". Once your `const` method mutates a cache without a mutex, you have broken that convention and a caller who relied on it has a race - which is why a cache inside a `const` method needs the `mutable` mutex above, not just a `mutable` value.

`std::propagate_const` exists as a wrapper for when you want the constness to reach through a pointer member, most usefully with PImpl ([CPP-143](#question-cpp-143)), where otherwise a `const` method can modify the entire implementation object.

[↑ Back to question index](#question-index)

---

## Question CPP-176

[↑ Back to question index](#question-index)

### Question CPP-176 — What is a pure virtual call, and how do you get one?

**Short answer**

- Calling a pure virtual function during base construction or destruction is undefined behaviour; in practice the runtime aborts with "pure virtual function call" or `R6025`.
- It happens because dispatch is restricted to the class currently being constructed or destroyed ([CPP-024](#question-cpp-024)) - and in the base, that function has no implementation at all, so there is nothing to dispatch to.
- The indirect form is the one that actually ships: the base constructor calls an ordinary member function, which calls the pure virtual. Nothing in the constructor mentions anything virtual.

**Details and nuances**

```cpp
struct Base {
    Base() { start(); }              // looks harmless
    void start() { run(); }          // ...calls a pure virtual
    virtual void run() = 0;
    virtual ~Base() = default;
};
struct Derived : Base { void run() override { /* never reached */ } };

Derived d;                           // "pure virtual function call" - abort
```

The direct form - `run()` written in the constructor body - is usually caught by the compiler with a warning. The indirect form above is not, because the compiler would have to prove which function the call reaches.

There is a second, nastier variant: a pure virtual call from a **destructor** of an object being destroyed by another thread, or after the vtable pointer has been reset during destruction. That one is intermittent and looks like memory corruption.

The fixes, in order of preference: do not call virtuals from constructors or destructors at all; use a two-step creation where a factory constructs the object and then calls `initialise()`; or pass the varying behaviour in as a parameter instead of inheriting it. If a virtual really must be called at construction time, that is a signal the design wants composition rather than inheritance ([CPP-168](#question-cpp-168)).

A pure virtual function may have a definition, which is occasionally used to give a default implementation ([CPP-170](#question-cpp-170)) - but that definition still does not make it callable through dispatch from a base constructor.

[↑ Back to question index](#question-index)

---

## Question CPP-177

[↑ Back to question index](#question-index)

### Question CPP-177 — What happens when a constructor throws?

**Short answer**

- The object never existed, so **its destructor is not called** - but every base and member whose construction had already completed is destroyed, in reverse order.
- That is why a raw pointer member leaks: the pointer's "destructor" does nothing, so whatever it points at is lost. A smart pointer or any RAII member is destroyed correctly.
- The caller sees the exception and no object; there is no half-constructed object to inspect or clean up, which is exactly the property you want.

**Details and nuances**

```cpp
class Broken {
    int*     a_ = new int[100];      // leaks if b_'s initialisation throws
    Resource b_;                     // may throw
};

class Fixed {
    std::unique_ptr<int[]> a_ = std::make_unique<int[]>(100);  // destroyed correctly
    Resource               b_;
};
```

The rule follows directly: **acquire every resource through an object that owns it.** If the constructor body has to acquire something raw, wrap it immediately, or use a function-try-block:

```cpp
Fixed::Fixed() try : a_(...), b_(...) { }
catch (...) { /* members already destroyed; cannot suppress - it rethrows */ }
```

Two details worth knowing about function-try-blocks, because they are usually misunderstood: in a *constructor* the handler cannot swallow the exception - it rethrows automatically when it returns - and the members are already destroyed by the time it runs, so it is for logging, not for recovery.

The alternative people reach for is two-phase initialisation: an empty constructor plus an `init()` that returns an error code. It is worse, because it creates a state where the object exists and is not usable, and every method now has to handle that state. Throwing from the constructor is the design that has no invalid state.

For allocation specifically: if `operator new` succeeds and then the constructor throws, the memory is released automatically - the matching `operator delete` is called. You do not leak the allocation, only whatever the constructor had acquired raw.

[↑ Back to question index](#question-index)

---

## Question CPP-178

[↑ Back to question index](#question-index)

### Question CPP-178 — What happens when a destructor throws?

**Short answer**

- Since C++11 destructors are implicitly `noexcept`, so an exception escaping one calls `std::terminate` immediately - the program dies, no unwinding, no handler.
- Even before that rule, throwing from a destructor during stack unwinding was fatal: a second exception while one is already propagating has no defined resolution, so the runtime terminates.
- So a destructor must swallow and log, not propagate. If the cleanup can genuinely fail in a way the caller needs to know about, expose an explicit `close()` that may throw, and have the destructor call it inside a `try`/`catch(...)` as a last resort.

**Details and nuances**

```cpp
class Writer {
public:
    void close() { flush(); }        // may throw - caller can handle it
    ~Writer() {
        try { close(); }
        catch (...) { /* log; never rethrow */ }
    }
};
```

This is the standard shape for anything whose release can fail - a file whose final flush can fail, a transaction whose commit can fail, a socket with a shutdown handshake. The caller who cares calls `close()` and handles the error; the destructor exists so that an exceptional path still releases the handle, and it accepts that it cannot report a problem.

`~T() noexcept(false)` opts out and is almost always the wrong answer: it does not make throwing safe, it only moves the failure from "terminates immediately" to "terminates if it happens during unwinding", which is the harder case to reproduce.

`std::uncaught_exceptions()` exists to let a destructor ask whether it is running during unwinding - it returns the count, and comparing it against the count captured in the constructor is how a scope-guard library implements "run this only on failure". Worth knowing as the mechanism behind `scope_fail`, not as something to hand-roll.

The connection to the rest: this is why `swap` and move operations are expected to be `noexcept` ([CPP-091](#question-cpp-091)) - the commit step of the strong guarantee has to be a step that cannot fail, and the same reasoning makes destructors non-throwing by construction.

[↑ Back to question index](#question-index)

---

# 17. Algorithms and Iterator Requirements

## Question CPP-179

[↑ Back to question index](#question-index)

### Question CPP-179 — Will `std::sort` work on a `std::vector`? On a `std::list`?

**Short answer**

- On a `vector`, yes: its iterators are contiguous, which satisfies `std::sort`'s requirement for **random-access** iterators.
- On a `list`, no - it will not compile. `list` iterators are bidirectional: you can step forwards and backwards, but not jump, and not compute a distance in constant time. `std::sort` needs both.
- `std::list` therefore provides its own `sort` member function, which merge-sorts by relinking nodes rather than moving elements.

**Details and nuances**

The requirement is not arbitrary. `std::sort` is introsort - quicksort with a heapsort fallback and insertion sort for small ranges - and every one of those needs to pick a pivot at an arbitrary position, partition by moving elements, and recurse on sub-ranges given by offsets. All three are `O(1)` on random access and `O(n)` on a linked list, which would turn `O(n log n)` into something far worse.

So the rule generalises past `sort`: an algorithm's iterator category tells you which containers it accepts.

| Algorithm | Needs | Works on `list`? |
|---|---|---|
| `std::find`, `std::count`, `std::copy` | input / forward | yes |
| `std::reverse` | bidirectional | yes |
| `std::sort`, `std::nth_element`, `std::binary_search`* | random access | no |
| `std::lower_bound` | forward (but `O(n)` steps without random access) | compiles, but scans |

*`binary_search` compiles on a forward iterator and degrades to a linear walk - which is the subtler trap: it still *works*, it just is not binary search any more.

`list::sort` being a member is the general pattern for node-based containers: `list` also has member `remove`, `unique`, `reverse` and `merge`, and they exist because the member version relinks nodes in `O(1)` each where the generic algorithm would copy values. Same for `map::find`, which is `O(log n)` against `std::find`'s `O(n)` ([CPP-181](#question-cpp-181)).

The everyday practical answer: this is one more reason `vector` is the default container ([CPP-044](#question-cpp-044)). Choosing `list` costs you most of `<algorithm>`.

[↑ Back to question index](#question-index)

---

## Question CPP-180

[↑ Back to question index](#question-index)

### Question CPP-180 — Which sort does the standard library give you, and when do you need `stable_sort`, `partial_sort` or `nth_element`?

**Short answer**

- `std::sort` is `O(n log n)` worst case (introsort) and **not stable** - equal elements may be reordered. `std::stable_sort` preserves their relative order, at `O(n log n)` with a temporary buffer, degrading to `O(n log² n)` if the allocation fails.
- `std::partial_sort` gives you the first `k` in order without sorting the rest; `std::nth_element` only guarantees that the element at position `n` is where it would be after a full sort, with everything smaller before it - which is what you want for a median or a top-K.
- Picking the weakest one that answers the question is a real difference: `nth_element` is `O(n)` on average where `sort` is `O(n log n)`.

**Details and nuances**

```cpp
std::nth_element(v.begin(), v.begin() + v.size()/2, v.end());
auto median = v[v.size()/2];                     // O(n) average, no full sort

std::partial_sort(v.begin(), v.begin() + 10, v.end());   // top 10, in order
```

Stability matters whenever you sort by one key after having sorted by another - sort by date, then `stable_sort` by name, and entries with the same name stay in date order. With `std::sort` that second pass silently destroys the first.

The comparator must be a **strict weak ordering** ([CPP-048](#question-cpp-048)). This is the one place where getting it wrong is not a wrong answer but undefined behaviour: a comparator that returns `true` for equal elements (using `<=` instead of `<`) lets `sort` run off the end of the range, and it usually crashes rather than mis-sorts - intermittently, on large inputs only.

C++20 adds the ranges versions - `std::ranges::sort(v)` - which take the container directly and check the iterator requirements through concepts, so the `std::list` case above produces a readable constraint error rather than a page of template instantiation.

[↑ Back to question index](#question-index)

---

## Question CPP-181

[↑ Back to question index](#question-index)

### Question CPP-181 — Why do containers have their own `find` when `std::find` exists?

**Short answer**

- Because the member version can use the container's structure. `std::map::find` is `O(log n)` through the tree, `std::unordered_map::find` is `O(1)` average through the hash - and `std::find` is `O(n)` on either, because a generic algorithm only knows how to walk.
- The same applies to `list::remove`, `list::unique` and `set::count`: the member relinks or looks up, the free algorithm iterates.
- Rule of thumb: if the container has a member with that name, it exists because it is asymptotically better - use it.

**Details and nuances**

The trap is that the generic version still compiles and still gives the right answer, so the mistake is invisible until the container is large. `std::find(m.begin(), m.end(), ...)` on a map with a million entries is a linear scan through a tree, which is both `O(n)` and cache-hostile.

A related pairing worth knowing: the erase-remove idiom ([CPP-120](#question-cpp-120)) is for sequence containers, because `std::remove` cannot erase - it only shuffles. For `list` the member `remove` does erase, and for associative containers `erase(key)` does it directly. C++20's `std::erase` and `std::erase_if` free functions finally give one spelling that does the right thing for each container.

[↑ Back to question index](#question-index)

---

# 18. Lock-Free Concurrency

## Question CPP-182

[↑ Back to question index](#question-index)

### Question CPP-182 — What does lock-free actually mean?

**Short answer**

- It is a **progress guarantee**, not a performance claim: in a lock-free algorithm, if threads run long enough, at least one of them makes progress - so the system cannot stall because one thread was suspended at the wrong moment.
- **Wait-free** is stronger: *every* thread completes in a bounded number of steps. **Obstruction-free** is weaker: a thread makes progress if it runs alone. A mutex gives none of these, because a thread holding the lock can be descheduled and everyone waits.
- Lock-free does not mean faster, and it does not mean no atomic instructions - it means no lock, and the failure mode changes from blocking to retrying ([CPP-061](#question-cpp-061)).

**Details and nuances**

The property that actually matters in practice is what happens when a thread is interrupted at the worst moment. With a mutex, a thread preempted inside the critical section blocks everyone until it is scheduled again - which is why a lock is unusable in a signal handler, in a real-time audio callback, or between a process and a shared-memory region it does not control. Lock-free code has no such window, and that - not throughput - is the reason to reach for it.

Where it genuinely earns its place:

- A signal handler or an interrupt context, where blocking is not allowed at all.
- A hard latency bound, where the tail matters more than the average.
- Shared memory across processes, where a lock held by a crashed process is never released.

Where it does not: ordinary application code under moderate contention, where a mutex is simpler, debuggable and usually faster.

`std::atomic<T>::is_always_lock_free` answers whether a given type qualifies on this platform; note that `std::atomic_flag` is the only type the standard guarantees is always lock-free.

[↑ Back to question index](#question-index)

---

## Question CPP-183

[↑ Back to question index](#question-index)

### Question CPP-183 — How would you build a single-producer single-consumer queue without locks?

**Short answer**

- A fixed-size ring buffer with two atomic indices: the producer owns `head`, the consumer owns `tail`, and neither writes the other's index.
- Because there is exactly one writer per index, no compare-and-swap is needed at all - a plain store with release ordering and a load with acquire ordering is enough, which is why SPSC is the one lock-free structure that is genuinely simple.
- Pad the two indices onto separate cache lines, or the two threads fight over one line and the queue is slower than a mutex ([CPP-068](#question-cpp-068)).

**Details and nuances**

```cpp
template <class T, std::size_t N>
class SpscQueue {                       // N a power of two
public:
    bool push(const T& v) {
        const auto h = head_.load(std::memory_order_relaxed);   // we own head_
        const auto next = (h + 1) & (N - 1);
        if (next == tail_.load(std::memory_order_acquire)) return false;  // full
        buf_[h] = v;
        head_.store(next, std::memory_order_release);           // publishes buf_[h]
        return true;
    }
    bool pop(T& out) {
        const auto t = tail_.load(std::memory_order_relaxed);   // we own tail_
        if (t == head_.load(std::memory_order_acquire)) return false;     // empty
        out = buf_[t];
        tail_.store((t + 1) & (N - 1), std::memory_order_release);
        return true;
    }
private:
    alignas(64) std::atomic<std::size_t> head_{0};
    alignas(64) std::atomic<std::size_t> tail_{0};
    std::array<T, N> buf_{};
};
```

The two orderings carry the whole correctness argument: the producer's **release** store on `head_` publishes the element written just before it, and the consumer's **acquire** load of `head_` makes that write visible. Weakening either to `relaxed` compiles, passes on x86 and fails on ARM.

Why it stops being simple the moment you add a second producer: two producers both reading `head_`, both writing a slot, both storing `head_ + 1` - now you need a CAS loop, and with a CAS loop come retries, the ABA question and the reclamation question ([CPP-184](#question-cpp-184)). Multi-producer multi-consumer lock-free queues are a library, not an exercise; use one rather than writing one.

[↑ Back to question index](#question-index)

---

## Question CPP-184

[↑ Back to question index](#question-index)

### Question CPP-184 — What are the ABA and reclamation problems?

**Short answer**

- **ABA**: a thread reads a value `A`, is descheduled, and by the time it runs its compare-and-swap the value has been changed to `B` and back to `A`. The CAS succeeds against state that is not the state it inspected.
- **Reclamation**: in a pointer-based lock-free structure you cannot free a node when you unlink it, because another thread may still be holding a pointer into it - and there is no lock to tell you when that stops being true.
- The two are related and both are why lock-free data structures are library work: the standard solutions are tagged pointers, hazard pointers, epoch-based reclamation or RCU, and each is substantially more machinery than a mutex.

**Details and nuances**

The classic ABA is a lock-free stack. Thread 1 reads the top node `A` and prepares to CAS the head to `A->next`. Meanwhile thread 2 pops `A`, pops `B`, frees them, allocates a new node that the allocator happens to place at `A`'s old address, and pushes it. Thread 1's CAS sees the head is still the pointer value `A` and succeeds - setting the head to a `next` pointer that belongs to a freed node.

The cheapest mitigation is a **tagged pointer**: pack a counter next to the pointer and CAS both together with a double-width compare-and-swap, so a pointer that came back is still a different value. It narrows the window rather than closing it, because the counter wraps.

Reclamation is the harder half and has no cheap answer:

| Technique | Idea | Cost |
|---|---|---|
| Hazard pointers | Each thread publishes what it is reading; a node is freed only when no hazard pointer names it | Per-read bookkeeping |
| Epoch / quiescent state | Free a node once every thread has passed a point where it held no references | Memory held longer |
| RCU | Readers are free, writers wait for a grace period | Reader-heavy workloads only |
| Never free | Pool and reuse nodes instead | Bounded memory, no reuse across types |

Saying this out loud is the senior answer to "would you write a lock-free queue": for SPSC yes ([CPP-183](#question-cpp-183)), and beyond that the correct engineering decision is to use an existing implementation or a mutex, because the failure mode is silent corruption under load on one machine.

[↑ Back to question index](#question-index)

---

# 19. Library Internals

## Question CPP-185

[↑ Back to question index](#question-index)

### Question CPP-185 — How does `std::string` store its data, and what is SSO?

**Short answer**

- A typical `std::string` is a pointer, a size and a capacity - three words - plus a **small string optimisation**: short strings are stored inside those bytes rather than on the heap, so they cost no allocation at all.
- The threshold is implementation-defined; libstdc++ stores about 15 characters inline, MSVC about 15, libc++ about 22, and `sizeof(std::string)` is 32 bytes on common 64-bit builds.
- The practical consequence: short strings are cheap and move is not free for them - moving an SSO string copies the buffer rather than stealing a pointer.

**Details and nuances**

That last point surprises people who assume move is always O(1). For a heap-allocated string it is; for one in the small buffer, there is nothing to steal, so the characters are copied. Fast either way, but not the same operation.

**Copy-on-write is forbidden since C++11**, and the reason is worth knowing: COW made `operator[]` on a non-const string potentially mutating - it had to detach the shared buffer - so two threads reading two copies of the same string could race on the shared reference count. C++11 requires that concurrent access to distinct objects be safe, which makes COW non-conforming. GCC carried a COW `std::string` for years and the ABI break to fix it is why `_GLIBCXX_USE_CXX11_ABI` exists ([BLD-009](<./Build Systems Questions.md#question-bld-009>) is where that bites in practice).

Related and frequently asked next: `std::string_view` ([CPP-050](#question-cpp-050)) avoids both the allocation and the copy, at the price of not owning anything - so returning a `string_view` to a temporary is a dangling reference, and that is the follow-up question.

[↑ Back to question index](#question-index)

---

## Question CPP-186

[↑ Back to question index](#question-index)

### Question CPP-186 — How does `std::function` work, and why can it allocate?

**Short answer**

- It is **type erasure**: `std::function<int(int)>` stores any callable with that signature behind a uniform interface, which means an indirect call through a vtable-like mechanism rather than a direct one.
- Implementations keep a small buffer inline, so a capture-free lambda or a small closure fits without allocating; a larger closure goes on the heap. The buffer size is implementation-defined and there is no way to query it portably.
- So it costs an indirect call, possible allocation, and lost inlining - which is why a template parameter is the right choice when the callable type is known at compile time.

**Details and nuances**

The mechanism in one sketch:

```cpp
struct Base { virtual int call(int) = 0; virtual ~Base() = default; };
template <class F> struct Model : Base {
    F f;  int call(int x) override { return f(x); }
};
// std::function holds a Base* into either its inline buffer or the heap
```

When to use which:

| | `template <class F> void run(F f)` | `std::function<void()> f` |
|---|---|---|
| Call | direct, inlinable | indirect, not inlinable |
| Allocation | never | possible |
| Type known at | compile time | run time |
| Use for | hot paths, algorithms | storing callbacks in a container, crossing an ABI, member variables |

`std::function` also requires the callable to be **copy-constructible**, which is why it cannot hold a lambda capturing a `unique_ptr`. C++23's `std::move_only_function` fixes exactly that, and mentioning it is a good signal.

For a Qt or SIP-style event system this is the trade in practice: signals and slots, `std::function` callbacks and templates all solve the same problem at different points on the compile-time/run-time line.

[↑ Back to question index](#question-index)

---

## Question CPP-187

[↑ Back to question index](#question-index)

### Question CPP-187 — What does `dynamic_cast` cost, and when should you use it?

**Short answer**

- It performs a run-time check using RTTI, walking the inheritance structure, so it is meaningfully more expensive than any other cast - typically tens of nanoseconds, and worse with multiple or virtual inheritance.
- It requires a polymorphic type (at least one virtual function), returns `nullptr` for a failed pointer cast and throws `std::bad_cast` for a failed reference cast.
- Frequent `dynamic_cast` in application logic is usually a design smell: the code is asking "what are you" where a virtual function would let the object answer "here is what I do".

**Details and nuances**

The legitimate uses are real and worth naming so the answer is not dogma: crossing a plugin or framework boundary where you receive a base pointer and genuinely must discover the type; implementing a visitor or serialisation layer; and safe downcasting in test code. `static_cast` down a hierarchy is faster and unchecked - correct only when you already know the type, and undefined behaviour when you are wrong.

RTTI can be disabled (`-fno-rtti`, common in embedded and in some game engines), which removes `dynamic_cast` and `typeid` entirely - so code that depends on them is not portable to those builds. That is a point worth raising in an embedded context.

The usual alternatives, in order of preference: a virtual function that does the thing; a `std::variant` with `std::visit` when the set of types is closed ([CPP-169](#question-cpp-169)); and an explicit type tag only when neither fits.

[↑ Back to question index](#question-index)

---

## Question CPP-188

[↑ Back to question index](#question-index)

### Question CPP-188 — Implement `unique_ptr`.

**Short answer**

- A pointer member, a destructor that deletes it, copy operations deleted, move operations that steal and null the source - that is the whole idea.
- The details an interviewer is checking: `explicit` constructor, `noexcept` on the move operations, self-assignment safety in move assignment, and `release`/`reset`/`get`/`operator*`/`operator->`/`operator bool`.
- The complete version also parameterises the deleter and specialises for arrays, which is worth mentioning even if you do not write it.

**Details and nuances**

```cpp
template <class T>
class UniquePtr {
public:
    UniquePtr() noexcept = default;
    explicit UniquePtr(T* p) noexcept : p_(p) {}
    ~UniquePtr() { delete p_; }

    UniquePtr(const UniquePtr&)            = delete;
    UniquePtr& operator=(const UniquePtr&) = delete;

    UniquePtr(UniquePtr&& o) noexcept : p_(o.release()) {}
    UniquePtr& operator=(UniquePtr&& o) noexcept {
        if (this != &o) reset(o.release());      // handles self-move
        return *this;
    }

    T*   release() noexcept { return std::exchange(p_, nullptr); }
    void reset(T* p = nullptr) noexcept { delete std::exchange(p_, p); }
    T*   get() const noexcept { return p_; }
    T&   operator*()  const { return *p_; }
    T*   operator->() const noexcept { return p_; }
    explicit operator bool() const noexcept { return p_ != nullptr; }

private:
    T* p_ = nullptr;
};
```

Points to raise while writing it, because they are what the exercise is actually testing: `explicit` on the raw-pointer constructor prevents an accidental implicit take-over of ownership; `std::exchange` makes release and reset obviously correct in one line; `noexcept` on the move operations is what lets containers move rather than copy ([CPP-091](#question-cpp-091)); and `explicit operator bool` allows `if (p)` without allowing `int x = p`.

The real `unique_ptr` takes `Deleter` as a second template parameter, stores it with the empty base optimisation so a stateless deleter costs nothing, and has a `T[]` specialisation that calls `delete[]` and provides `operator[]` instead of `operator*`.

[↑ Back to question index](#question-index)

---

## Question CPP-189

[↑ Back to question index](#question-index)

### Question CPP-189 — Implement `shared_ptr` - what does the control block hold?

**Short answer**

- Two pointers: one to the object, one to a **control block** holding a strong count, a weak count and the deleter. The strong count destroys the object when it reaches zero; the weak count frees the control block when it reaches zero.
- Both counters are atomic, which is why copying a `shared_ptr` is not free - it is an atomic increment, and contention on a hot shared pointer shows up in profiles.
- `make_shared` allocates the object and the control block in one block, which is faster and halves the allocations, at the cost that the object's memory is only released when the last *weak* reference is gone.

**Details and nuances**

```cpp
struct ControlBlock {
    std::atomic<long> strong{1};
    std::atomic<long> weak{1};        // one weak reference held by the strong group
    virtual void destroy() = 0;       // type-erased deleter
    virtual ~ControlBlock() = default;
};

template <class T>
class SharedPtr {
    T*            p_  = nullptr;
    ControlBlock* cb_ = nullptr;
public:
    SharedPtr(const SharedPtr& o) noexcept : p_(o.p_), cb_(o.cb_) {
        if (cb_) cb_->strong.fetch_add(1, std::memory_order_relaxed);
    }
    ~SharedPtr() {
        if (cb_ && cb_->strong.fetch_sub(1, std::memory_order_acq_rel) == 1) {
            cb_->destroy();                                  // destroy the object
            if (cb_->weak.fetch_sub(1, std::memory_order_acq_rel) == 1) delete cb_;
        }
    }
};
```

Three things worth saying out loud while drawing this:

- The two pointers are separate on purpose, which is what makes the **aliasing constructor** possible - a `shared_ptr` that keeps a parent alive while pointing at a member.
- The increment can be `relaxed` (adding a reference cannot let anything die) but the decrement must be `acq_rel`, because the thread that brings the count to zero must see every other thread's writes before running the destructor. Getting that wrong is a classic subtle bug.
- The control block is type-erased, which is why `shared_ptr<void>` can still call the right destructor, and why `shared_ptr` is two words while `unique_ptr` is one.

The follow-ups this invites are all already answerable: why `make_shared` versus `shared_ptr(new T)` ([CPP-033](#question-cpp-033), [CPP-173](#question-cpp-173)), cycles and `weak_ptr` ([CPP-035](#question-cpp-035)), and what "thread-safe" does and does not mean here ([CPP-038](#question-cpp-038)).

[↑ Back to question index](#question-index)

---

# 20. Language Features Often Asked

## Question CPP-190

[↑ Back to question index](#question-index)

### Question CPP-190 — What are the rules for operator overloading, and what does `<=>` change?

**Short answer**

- Overload only where the meaning is obvious to a reader; prefer non-member functions for symmetric operators so implicit conversions apply equally to both sides, and members for those that mutate `this` such as `+=`, `[]`, `()` and `->`.
- The canonical shape is to implement `+=` as a member and `+` as a non-member in terms of it, and to implement `==` and `<` and derive the rest - which is exactly the boilerplate C++20 removes.
- `operator<=>`, the three-way comparison, returns an ordering category and lets the compiler synthesise `<`, `>`, `<=` and `>=`; `= default` on it and on `==` gives you memberwise comparison for free.

**Details and nuances**

```cpp
struct Version {
    int major, minor, patch;
    auto operator<=>(const Version&) const = default;   // all four relations
    bool operator==(const Version&) const = default;    // == is separate
};
```

`==` is deliberately not synthesised from `<=>` in the defaulted case because equality can often be computed much faster than ordering - comparing sizes before contents, for example - so the standard keeps them independent.

The three ordering categories are the part that gets asked: `strong_ordering` means equal values are interchangeable; `weak_ordering` means equivalent but distinguishable (case-insensitive strings); `partial_ordering` means some pairs are unordered, which is what floating point returns because of NaN.

Rules that do not change: you cannot invent new operators or change precedence or arity; `&&`, `||` and `,` lose their short-circuit or sequencing behaviour when overloaded, which is why overloading them is nearly always wrong; and assignment, subscript, call and arrow must be members.

[↑ Back to question index](#question-index)

---

## Question CPP-191

[↑ Back to question index](#question-index)

### Question CPP-191 — What is a lambda, really?

**Short answer**

- A compiler-generated **closure type** - an unnamed class with a `const` `operator()` and one member per captured variable - and the lambda expression creates an object of that type.
- `mutable` removes the `const` from `operator()`, so a by-value capture can be modified; each call sees the modification because it is member state, not a fresh copy.
- A capture-free lambda is additionally convertible to a plain function pointer, which is what makes it usable as a C callback.

**Details and nuances**

```cpp
int n = 0;
auto f = [n]() mutable { return ++n; };   // 1, 2, 3 - state lives in the closure
auto g = [p = std::make_unique<T>()] { use(*p); };   // init-capture: move into the closure
auto h = [](auto x) { return x + x; };    // generic: templated operator()
```

**Init-capture** (C++14) is the answer to "how do you capture a move-only type", and it is also how you capture an expression rather than a variable - `[len = v.size()]`.

The capture defaults are where the bugs are. `[&]` captures everything by reference including `this`, so a lambda stored and called later dangles ([CPP-110](#question-cpp-110)); `[=]` captures `this` **by pointer** even though it looks like a copy, which is the same dangling problem wearing a disguise - C++17 added `[*this]` to capture a copy of the object, and C++20 deprecated the implicit `this` capture in `[=]` for this reason.

Worth knowing that a generic lambda's `operator()` is a template, so `auto` parameters make it usable with any type, and C++20 allows an explicit template parameter list - `[]<class T>(std::vector<T>& v)` - when you need the type by name.

[↑ Back to question index](#question-index)

---

## Question CPP-192

[↑ Back to question index](#question-index)

### Question CPP-192 — What are type traits, and how does `if constexpr` change template code?

**Short answer**

- Type traits are compile-time queries and transformations on types - `std::is_integral_v<T>`, `std::remove_reference_t<T>`, `std::is_nothrow_move_constructible_v<T>` - evaluated by the compiler with no run-time cost.
- `if constexpr` discards the untaken branch **at compile time**, so the discarded branch does not have to be valid for the current `T` - which replaces most `enable_if` and tag-dispatch machinery with an ordinary `if`.
- The result is that C++17 template code reads like normal code, and C++20 concepts then move the constraint into the signature where the error message can name it.

**Details and nuances**

```cpp
template <class T>
std::string describe(const T& v) {
    if constexpr (std::is_integral_v<T>)        return std::to_string(v);
    else if constexpr (requires { v.str(); })   return v.str();          // C++20
    else                                        return "unprintable";
}
```

Without `if constexpr` this needed either `enable_if` overloads or tag dispatch, and both scattered the logic across several functions. The evolution worth naming in one line: SFINAE and `enable_if` (C++11) → `if constexpr` (C++17) → concepts and `requires` (C++20), each making the same idea readable by more people.

Two practical notes. `if constexpr` only discards inside a template - in a non-template function both branches must compile. And a trait is a query, not a guarantee of behaviour: `std::is_nothrow_move_constructible_v` tells you what was declared, which is why declaring `noexcept` wrongly is worse than not declaring it ([CPP-091](#question-cpp-091)).

`static_assert` with a trait is the cheapest way to turn a confusing instantiation error into a one-line message, and is worth reaching for at the top of any template with real requirements.

[↑ Back to question index](#question-index)

---

## Question CPP-193

[↑ Back to question index](#question-index)

### Question CPP-193 — What is CTAD, and when do you need a deduction guide?

**Short answer**

- Class template argument deduction (C++17) lets you write `std::vector v{1, 2, 3};` or `std::lock_guard lock{m};` and have the template arguments deduced from the constructor arguments, the way function templates always could.
- The compiler builds implicit guides from the constructors; a **deduction guide** is an explicit rule you write when those do not give the answer you want.
- It is a readability feature with one sharp edge: the deduced type is sometimes not the one you assumed, so `auto` plus a factory function is still clearer in generic code.

**Details and nuances**

```cpp
template <class T> struct Box { Box(T) {} };
Box b{42};                            // Box<int> - implicit guide

template <class It> struct Range { Range(It, It); };
template <class It> Range(It, It) -> Range<It>;     // explicit guide

std::vector v{std::string("a")};      // vector<std::string>, one element
std::vector w(3, 0);                  // vector<int> of three zeros - different!
```

The classic surprise is that braces and parentheses select different constructors, and CTAD does not change that - it just makes the difference harder to notice because the type is no longer written down.

A guide is genuinely needed when the constructor takes something other than the template parameter - an iterator pair, an initializer list of a different type, or a type that needs `decay` applied. `std::pair`'s guide, which decays its arguments so `std::pair p{"a", 1}` gives `pair<const char*, int>` rather than a reference to an array, is the textbook example.

`std::lock_guard lock{m}` is the everyday win, and it removes a real bug class too: `std::lock_guard{m}` without a name is a temporary that unlocks immediately, and naming it is what CTAD makes painless.

[↑ Back to question index](#question-index)

---

## Question CPP-194

[↑ Back to question index](#question-index)

### Question CPP-194 — What is a C++20 coroutine, at the level you would be asked about it?

**Short answer**

- A function that can suspend and resume: using `co_await`, `co_yield` or `co_return` makes the compiler transform it into a state machine whose state lives in a heap-allocated frame rather than on the stack.
- C++20 provides only the **language machinery**, not usable types - there is no standard task or generator in C++20, so real use means a library (cppcoro, Boost.Asio, Qt's own) or writing promise types yourself, which is why adoption has been slow.
- The value is asynchronous code that reads sequentially: no callback nesting, no explicit state variable, with the compiler generating the state machine you would otherwise write by hand.

**Details and nuances**

```cpp
Task<Response> fetch(Request r) {
    auto conn = co_await connect(r.host);    // suspends, does not block
    auto resp = co_await conn.send(r);
    co_return resp;
}
```

The three keywords and what each does: `co_await` suspends until an awaitable is ready, `co_yield` produces a value and suspends (that is a generator), `co_return` finishes.

What to be careful about, and what an interviewer is likely probing: the frame is usually heap-allocated, so a coroutine in a hot loop is not free (the allocation is elidable in principle and often is not in practice); a reference parameter is a dangling-reference trap because the referent may die while the coroutine is suspended; and a suspended coroutine that is never resumed leaks its frame.

The honest framing for a C++17 codebase: this is worth knowing about and rarely worth introducing yet. C++23 adds `std::generator`, which is the first genuinely usable standard coroutine type.

[↑ Back to question index](#question-index)

