# C++ Standard Library (STL) Assessment — Answer Guide

This handbook covers the C++17 standard-library foundations and the most important C++20/C++23 additions. Every question is followed by a model answer. Complexity statements assume the operation's documented preconditions; invalidation and thread-safety rules are part of correctness, not merely performance details.

“STL” historically refers to containers, iterators, algorithms, and function objects. In everyday usage it is often used for the broader C++ standard library; this guide follows that broader convention.

Labels:

- **[Basic]** — expected core knowledge.
- **[Deep dive]** — guarantees, tradeoffs, and edge cases.
- **[Code]** — code reading or refactoring.
- **[Design]** — API/container selection discussion.

## Contents

1. [Sequence containers and text](#1-sequence-containers-and-text)
   - [`std::vector`](#11-stdvector)
   - [`std::string` and `std::string_view`](#12-stdstring-and-stdstring_view)
   - [`std::deque`](#13-stddeque)
   - [`std::list`](#14-stdlist-doubly-linked)
   - [`std::forward_list`](#15-stdforward_list-singly-linked)
   - [`std::array`](#16-stdarray)
2. [Associative containers](#2-associative-containers)
   - [`std::map` and `std::multimap`](#21-stdmap-and-stdmultimap)
   - [`std::set` and `std::multiset`](#22-stdset-and-stdmultiset)
   - [Unordered maps and sets](#23-stdunordered_map-and-stdunordered_set)
3. [Container adapters and iterators](#3-container-adapters-and-iterators)
   - [`std::stack` and `std::queue`](#31-stdstack-and-stdqueue)
   - [Iterator categories, invalidation, and insertion iterators](#32-iterator-categories-invalidation-and-insertion-iterators)
4. [Algorithms](#4-algorithms)
   - [Core algorithms](#41-find-count-sort-copy-for_each-and-transform)
   - [Binary search and rearrangement/removal algorithms](#42-lower_bound-upper_bound-remove_if-reverse-unique-and-erase-remove)
5. [Vocabulary and sum types](#5-vocabulary-and-sum-types)
   - [`std::optional`](#51-stdoptional)
   - [`std::variant` and `std::visit`](#52-stdvariant-and-stdvisit)
   - [`std::any`](#53-stdany)
   - [`std::pair` and `std::tuple`](#54-stdpair-and-stdtuple)
6. [Time and concurrency](#6-time-and-concurrency)
   - [`std::chrono`](#61-stdchrono-durations-and-time-points)
   - [`std::thread`](#62-stdthread-join-and-detach)
   - [Mutex and lock wrappers](#63-stdmutex-lock_guard-unique_lock-and-shared_lock)
   - [Condition variables and futures](#64-condition_variable-future-promise-and-async)
   - [Atomics and the memory model](#65-stdatomic-data-races-happens-before-and-the-memory-model)
7. [C++20 and C++23 library/language overview](#7-c20-and-c23-librarylanguage-overview)
   - [Important C++20 features](#71-important-c20-features)
   - [Important C++23 features](#72-important-c23-features)
8. [Advanced algorithms and lock-free design](#8-advanced-algorithms-and-lock-free-design)
   - [Sorting, selection, heaps, and container-aware lookup](#81-sorting-selection-heaps-and-container-aware-lookup)
   - [Lock-free progress and an SPSC queue](#82-lock-free-progress-and-an-spsc-queue)

---

# 1. Sequence containers and text

| Container | Storage | Random access | Efficient end operations | Typical handle stability |
| --- | --- | --- | --- | --- |
| `vector` | Contiguous dynamic | O(1) | Back | Reallocation invalidates all |
| `deque` | Segmented dynamic | O(1) | Front and back | End insertion invalidates iterators, preserves existing references |
| `list` | Doubly linked nodes | No | Front/back and known-position insert/erase | Only erased nodes are invalidated |
| `forward_list` | Singly linked nodes | No | Front and after-known-position operations | Only erased nodes are invalidated |
| `array` | Inline fixed-size contiguous | O(1) | Fixed size | Stable for the object's lifetime |

## 1.1. `std::vector`

1. **[Basic] What storage model does `std::vector<T>` provide?**

   **Answer.** It owns a dynamically sized contiguous sequence of `T`. Elements occupy one allocation in order, so pointer arithmetic, cache-friendly traversal, and C interoperability through `data()` are possible. The vector object itself usually stores pointer-like bookkeeping, but its exact representation and growth factor are implementation details.

2. **[Basic] How do `size()` and `capacity()` differ?**

   **Answer.** `size()` is the number of live elements. `capacity()` is how many elements fit in the current allocation before reallocation is required. The half-open range `[data(), data() + size())` contains live objects; memory between size and capacity is storage, not constructed `T` objects that clients may access.

3. **[Basic] What are the main complexity guarantees?**

   **Answer.** Indexing, `front`, `back`, and end insertion/removal without reallocation are constant time; `push_back`/`emplace_back` are amortized constant time because occasional growth moves/copies all elements. Insertion/erasure in the middle is linear in shifted elements. `clear` destroys elements linearly but does not generally release capacity.

4. **[Deep dive] How do `reserve` and `resize` differ?**

   **Answer.** `reserve(n)` changes only capacity (if growth is needed) and constructs no elements; it never reduces capacity. `resize(n)` changes the number of live elements, destroying excess elements or value/default-inserting new ones. Reserving a known lower bound can prevent repeated reallocations, but calling `reserve(size()+1)` before every append defeats geometric growth and can make insertion quadratic.

5. **[Deep dive] What invalidates vector iterators, pointers, and references?**

   **Answer.** Any reallocation invalidates all of them, including the past-the-end iterator. Without reallocation, insertion invalidates those at or after the insertion point (the old `end` included), while earlier element references/iterators remain valid. Erasure invalidates the erased range and everything after it. `clear` invalidates all element references/iterators; `swap` has its own allocator-dependent rules, while references generally continue to refer to the same elements now owned by the other container.

6. **[Basic] How do `push_back` and `emplace_back` differ?**

   **Answer.** `push_back` accepts an existing `T` lvalue/rvalue and copies/moves it into the vector. `emplace_back(args...)` forwards constructor arguments and constructs `T` in the destination slot. Emplacement can avoid a temporary but is not automatically faster; `push_back(value)` is clearer when a `T` already exists, and `emplace_back` can accidentally invoke surprising constructors. Since C++17, `emplace_back` returns `T&` to the inserted element.

7. **[Deep dive] What exception guarantee does append provide?**

   **Answer.** If construction/allocation fails, vector operations commonly provide the strong guarantee. During reallocation, vector prefers a non-throwing move or may copy when copying is available so the old elements remain intact. If `T` is move-only and its move constructor can throw, the standard permits weaker/unspecified effects for relevant operations. Make truthful move constructors `noexcept` when possible.

8. **[Code] What is wrong with retaining `&values[0]` and then appending?**

   **Answer.** `push_back` may reallocate, making the pointer dangle. Either use the pointer only before a potentially reallocating operation, reserve enough capacity first when a reliable upper bound exists, store an index and reacquire the address, or choose a container/ownership model with the required stability. Even with sufficient capacity, insertion before the element can shift it.

9. **[Deep dive] What do `shrink_to_fit` and `vector<bool>` require special attention for?**

   **Answer.** `shrink_to_fit` is a non-binding request; if it reallocates, all iterators/references/pointers are invalidated. `vector<bool>` is a space-optimized specialization that packs bits and returns proxy references rather than actual `bool&`; its iterators/references have surprising type and concurrency behavior. Use another representation when ordinary element-reference semantics matter.

10. **[Design] When is `vector` the default container choice?**

   **Answer.** Use it by default for a variable-size sequence unless requirements contradict contiguity/reallocation. It has low per-element overhead, excellent locality, and works with all random-access/contiguous algorithms. Prefer a different structure only for demonstrated needs such as stable nodes, cheap front growth, keyed lookup, or a fixed compile-time size.

## 1.2. `std::string` and `std::string_view`

1. **[Basic] What does `std::string` own and guarantee about storage?**

   **Answer.** `std::string` owns a contiguous sequence of `char`. Its characters are contiguous and followed by a null character accessible through `c_str()`/`data()`; `size()` excludes that terminator. C++17 provides non-const `data()` for modifying existing character positions, but writing beyond size or corrupting the terminator contract is invalid.

2. **[Deep dive] What is small-string optimization (SSO)?**

   **Answer.** Most implementations store short strings inside the string object to avoid allocation, but the standard does not require SSO or specify its threshold/layout. Code must not depend on address stability, allocation count, or object size implied by a particular implementation. Moving an SSO string may copy characters rather than transfer a heap pointer.

3. **[Basic] What is `std::string_view`?**

   **Answer.** It is a lightweight non-owning view—typically pointer plus length—over a contiguous character sequence. Copying/subview creation is cheap, and the data need not be null-terminated. It does not extend lifetime; every use requires the underlying characters to remain alive and unmodified in ways that invalidate the view.

4. **[Code] Why is this function wrong?**

   ```cpp
   std::string_view make_name() {
       std::string value = "worker";
       return value;
   }
   ```

   **Answer.** The view refers to `value`'s buffer, which is destroyed on return, so the result dangles. Return an owning `std::string`, or return a view only into storage whose lifetime is part of the function's contract (for example a caller-owned buffer or static literal).

5. **[Deep dive] How do `string::substr` and `string_view::substr` differ?**

   **Answer.** `string::substr` returns a new owning string and copies the selected characters (linear work). `string_view::substr` returns another view into the same storage (constant work) and inherits its lifetime/invalidation constraints. Confusing the two is a common source of either unnecessary allocation or dangling views.

6. **[Deep dive] Do strings and string views support embedded null characters?**

   **Answer.** Yes—their logical length is explicit, so `std::string{"a\0b", 3}` and a corresponding view contain three characters. C APIs using null-terminated strings will observe only the prefix unless they accept an explicit length. `string_view::data()` is not proof of null termination and must not be passed blindly to `%s`, `strlen`, or similar APIs.

7. **[Deep dive] What invalidates references, pointers, and views into a string?**

   **Answer.** Mutating operations that reallocate invalidate all; even without reallocation, operations can shift/replace characters and invalidate affected positions under the operation's specified rules. Non-const member calls should be treated carefully unless their guarantees are known. A string move also does not provide a blanket portable promise that an existing view now refers to the destination, especially with SSO/allocator differences.

8. **[Design] How should APIs choose among `string`, `const string&`, and `string_view`?**

   **Answer.** Use `string` by value to accept/store ownership (and move it), `const string&` when the API specifically needs a string object and only borrows during the call, and `string_view` for a non-owning read-only character range accepted only for the call. Do not retain a `string_view` unless the lifetime relationship is explicit; use an owning string for stored data.

## 1.3. `std::deque`

1. **[Basic] What storage model does `std::deque` use?**

   **Answer.** It is typically a segmented sequence: multiple fixed-size element blocks are indexed by a small map of block pointers. It provides random-access iterators and constant-time indexing but not one contiguous buffer. There is no single `data()` spanning all elements.

2. **[Basic] What are deque's main complexity strengths?**

   **Answer.** Insertion/removal at both front and back are amortized constant time; random access is constant time. Middle insertion/erasure is linear but may shift toward the nearer end. Compared with vector, deque supports efficient front growth without relocating all elements.

3. **[Deep dive] What are the iterator/reference invalidation rules for end insertion?**

   **Answer.** Inserting at either end invalidates all iterators (including past-the-end), but references/pointers to existing elements remain valid. Inserting in the middle invalidates all iterators and references. Erasure rules depend on position: middle erasure broadly invalidates, while removing at an end preserves references/iterators to non-erased elements subject to the precise standard operation rules; never assume the past-the-end iterator stays valid.

4. **[Deep dive] Why can deque be less cache-friendly than vector?**

   **Answer.** Traversal crosses separately allocated blocks and indexing adds a level of address calculation; elements are not globally contiguous for vectorized/C APIs. Within each block locality is still good, and avoiding large relocations can win. Measure the actual workload rather than inferring performance only from asymptotic complexity.

5. **[Code] Can `&deque[0]` be passed as an array of `deque.size()` elements?**

   **Answer.** No. Individual neighboring elements may happen to be adjacent within a block, but the whole deque is not guaranteed contiguous. Copy to a `vector`/array or use an API that accepts iterators/ranges or multiple spans.

6. **[Design] When is deque preferable to vector or list?**

   **Answer.** Choose it for a sequence needing frequent growth/removal at both ends plus random access, while tolerating iterator invalidation and segmented storage. It often outperforms a node-based list through much better locality and lower allocation overhead. A queue commonly uses deque as its default underlying container.

## 1.4. `std::list` (doubly linked)

1. **[Basic] What storage and complexity model does `std::list` provide?**

   **Answer.** It is a doubly linked sequence whose elements live in separate nodes. Given an iterator, insertion and erasure are constant time, and insertion does not move existing elements. Traversal/index lookup is linear, each element has link/allocation overhead, and locality/prefetching are poor compared with contiguous containers.

2. **[Deep dive] Which iterators and references are invalidated?**

   **Answer.** Insertion does not invalidate existing iterators/references. Erasing an element invalidates only iterators/references to that element. This stability is a central reason to choose list, but it does not protect pointers to an element after that element is erased or its owning list is destroyed.

3. **[Basic] What do `splice` operations do?**

   **Answer.** They relink one or more existing nodes within/between lists without copying/moving the elements, preserving iterators to transferred elements (which then belong to the destination). Allocator compatibility is required for cross-list transfer. Complexity can depend on whether a range comes from another list because size bookkeeping may require counting.

4. **[Deep dive] Why does list provide member `sort`, `merge`, `remove_if`, and `unique`?**

   **Answer.** `std::sort` requires random-access iterators, which list lacks; `list::sort` relinks nodes using a suitable stable algorithm. `merge` and `splice` similarly exploit node relinking. `unique` removes only consecutive equivalent elements, so sorting first is needed if all duplicates should become adjacent.

5. **[Design] Why is list rarely the default despite O(1) insertion?**

   **Answer.** Finding the insertion point is usually O(n), each node allocation/link consumes memory, and pointer chasing causes cache/TLB misses. Vector insertion can be faster even when it shifts elements, especially for small/movable types. Choose list when iterator/reference stability and node splicing are demonstrated requirements, not merely because insert/erase appears O(1).

## 1.5. `std::forward_list` (singly linked)

1. **[Basic] How does `forward_list` differ from `list`?**

   **Answer.** It stores one next link per node, supports only forward traversal, and exposes operations after a position (`insert_after`, `erase_after`, `splice_after`). It has lower link/object overhead than a doubly linked list but cannot move backward or efficiently access the previous node.

2. **[Basic] Why does it provide `before_begin()`?**

   **Answer.** There is no node before the first element, yet uniform “insert/erase after predecessor” operations need a position for front operations. `before_begin()` is a special iterator preceding `begin()`; it is not dereferenceable but can be passed to the appropriate `_after` operations.

3. **[Deep dive] Why does `forward_list` not provide `size()`?**

   **Answer.** The container is designed for minimal per-object overhead and constant-time node transfer operations; storing/updating a count would add state/work. Counting uses `std::distance(begin(), end())` and is linear. If frequent size queries are required, track size externally with the same mutation discipline or choose another container.

4. **[Deep dive] What are its invalidation and splicing properties?**

   **Answer.** Insertion preserves existing iterators/references; erasure invalidates only erased nodes. `splice_after` transfers nodes without moving elements, subject to allocator and range/position preconditions. Because the API names the predecessor, off-by-one/range mistakes deserve careful tests.

5. **[Design] When is `forward_list` appropriate?**

   **Answer.** Use it for highly constrained forward-only node-based structures where per-node overhead matters and insertion/removal after known predecessors dominates. In general application code, vector/deque often win through locality and usability; even list is easier when bidirectional traversal or erase-at-current-position is needed.

## 1.6. `std::array`

1. **[Basic] What is `std::array<T, N>`?**

   **Answer.** It is a fixed-size contiguous container that wraps a built-in array with standard container operations, iterators, `size`, assignment, and value semantics. The elements are stored inside the `array` object; whether that object is on the stack, heap, static storage, or inside another object depends on where the user places it.

2. **[Basic] How is it initialized?**

   **Answer.** It is an aggregate, so `std::array<int, 3> a{1, 2, 3};` initializes all elements and missing elements are value-initialized. `std::array<int, 3> a;` default-initializes elements, leaving fundamental elements indeterminate for an automatic object. C++17 class template argument deduction and C++20 `std::to_array` can reduce repetition.

3. **[Deep dive] What is special about `std::array<T, 0>`?**

   **Answer.** It is a valid container with `begin() == end()` and `size() == 0`. Calling `front()` or `back()` is undefined, and the value of `data()` need not behave like an address of an element because none exists. Generic code should work with the empty range without dereferencing it.

4. **[Deep dive] What happens to iterators/references when arrays are swapped?**

   **Answer.** `array::swap` performs element-wise swaps in linear time. Iterators and references are not invalidated, but they continue to refer to the same positions/objects within their original arrays, whose values have been exchanged. This differs from swapping pointer-owning containers, where iterators generally follow transferred element storage.

5. **[Design] Why use `std::array` instead of a built-in array?**

   **Answer.** It does not decay to a pointer on ordinary passing, carries its size in the type, supports assignment/comparison and standard algorithms, and exposes consistent iterator/access APIs. Built-in arrays remain relevant at language/C boundaries, but `std::array` is safer for value semantics and generic C++ code.

---

# 2. Associative containers

## 2.1. `std::map` and `std::multimap`

1. **[Basic] What guarantees do ordered associative containers provide?**

   **Answer.** They keep keys in comparator order and provide logarithmic lookup, insertion, and erasure. Implementations commonly use balanced trees such as red-black trees, but the standard specifies behavior/complexity rather than a particular data structure. Iteration visits elements in nondecreasing key order.

2. **[Basic] How do `map` and `multimap` differ?**

   **Answer.** `map<Key, T>` contains at most one element per key-equivalence class. `multimap<Key, T>` permits multiple equivalent keys; `equal_range(key)` returns their subrange. “Equivalent” is defined by the comparator (`!comp(a,b) && !comp(b,a)`), not necessarily `operator==`.

3. **[Basic] What is the difference among `operator[]`, `at`, `find`, and `contains`?**

   **Answer.** For `map`, `operator[]` returns the mapped value and inserts a value-initialized one when the key is absent; it therefore mutates and requires a constructible mapped value. `at` returns or throws `out_of_range` without insertion. `find` returns an iterator/end, and C++20 `contains` returns a boolean. `multimap` has no `operator[]`/`at` because a key can name several values.

4. **[Deep dive] When should `try_emplace` or `insert_or_assign` be used?**

   **Answer.** `try_emplace(key, args...)` constructs the mapped value only if insertion occurs, avoiding wasted construction/moves for an existing key. `insert_or_assign` inserts a new pair or assigns the mapped value when present. Plain `emplace` may construct a candidate even if the key already exists, and `operator[]` plus assignment unnecessarily default-constructs the mapped value.

5. **[Deep dive] What must a comparator satisfy?**

   **Answer.** It must impose a strict weak ordering: irreflexive, asymmetric, transitive, with transitive equivalence classes. It must remain consistent for keys stored in the container. Violating the contract can break the tree's invariants/lookup and yields undefined or otherwise invalid program behavior under the algorithm/container requirements.

6. **[Deep dive] Why are map keys exposed as `const Key`?**

   **Answer.** An element is effectively `pair<const Key, T>`; changing a key in place could invalidate its ordering position. Modify the mapped value freely. To change a key in C++17, extract a node handle, modify `node.key()`, then insert it again, checking for a uniqueness collision.

7. **[Deep dive] What are map invalidation guarantees?**

   **Answer.** Insertion does not invalidate existing iterators/references. Erasure invalidates only those to erased elements. Node extraction invalidates references/iterators to the extracted element while preserving its contained objects through the node handle; reinsertion establishes a new container position. This stability is stronger than vector/unordered iterators and costs node allocation/locality.

8. **[Deep dive] What is heterogeneous lookup?**

   **Answer.** With a transparent comparator such as `std::less<>`, overloads of `find`, `lower_bound`, and related operations can accept a different comparable key type without constructing `Key`. For example, a `map<string, T, less<>>` can look up a `string_view` when the cross-type ordering is valid. C++23 adds heterogeneous erasure for suitable associative containers.

9. **[Code] How are `lower_bound`, `upper_bound`, and `equal_range` used on a map/multimap?**

   **Answer.** `lower_bound(k)` is the first element not ordered before `k`; `upper_bound(k)` is the first ordered after `k`; `equal_range(k)` returns both. In `map` the range contains zero or one element, while in `multimap` it spans every equivalent key. Use member functions rather than generic algorithms because they exploit the tree and are logarithmic.

## 2.2. `std::set` and `std::multiset`

1. **[Basic] How do set containers differ from map containers?**

   **Answer.** A set stores keys as the entire element, while a map associates a key with a mutable mapped value. `set` has unique key-equivalence classes; `multiset` permits duplicates. Both keep comparator order and provide logarithmic search/update.

2. **[Deep dive] Why do set iterators provide effectively const access?**

   **Answer.** Mutating an element could change its comparator position and corrupt ordering, so elements cannot be modified through ordinary iterators. Replace by erase/insert or use a C++17 node handle to extract, modify, and reinsert. A field ignored by the comparator is still not generally mutable through the iterator because the element type is exposed as const.

3. **[Design] When is `set` a good choice?**

   **Answer.** Use it when unique membership plus sorted iteration, range queries, nearest-key operations, or iterator/reference stability are required. For simple membership, an unordered set often gives faster average lookup; for small collections, a sorted vector can be faster and smaller because of locality. C++23 `flat_set` offers a standard contiguous sorted-container adaptor where available.

4. **[Deep dive] How are duplicates represented in `multiset`?**

   **Answer.** Equivalent values occupy a contiguous ordered subrange accessible with `equal_range` or `count`. Their relative order is stable with respect to insertion rules specified for associative containers, but they are not individually identified by the key; erase-by-key removes all equivalents, while erase-by-iterator removes one.

5. **[Code] How do you test insertion into a set?**

   **Answer.** Unique-set insertion returns `pair<iterator, bool>`: the iterator points to the inserted or existing equivalent element and the boolean reports whether insertion happened.

   ```cpp
   if (auto [it, inserted] = ids.insert(id); !inserted) {
       report_duplicate(*it);
   }
   ```

## 2.3. `std::unordered_map` and `std::unordered_set`

1. **[Basic] What storage and complexity model do unordered containers provide?**

   **Answer.** They organize elements into buckets using a hash code and equality predicate. Lookup/insertion/erasure are average constant time when hashing distributes keys well and load is controlled, but worst-case linear. Iteration order is unspecified and can change after insertion/rehash or across executions/library versions.

2. **[Basic] What relationship must `Hash` and `KeyEqual` satisfy?**

   **Answer.** `KeyEqual` must be an equivalence relation. If two keys compare equal, the hash function must return the same value for both; unequal keys may collide. Both operations must remain stable while keys are stored. Hashing by case while equality is case-insensitive—or vice versa—violates the contract and makes lookup incorrect.

3. **[Deep dive] What are buckets and load factor?**

   **Answer.** `bucket_count()` is the number of buckets, and `load_factor()` is approximately `size()/bucket_count()`. When growth would exceed `max_load_factor()`, the container may rehash to more buckets. A lower maximum can reduce collision chains at the cost of memory/cache footprint; the implementation chooses bucket counts and policy.

4. **[Basic] How do `reserve` and `rehash` differ for unordered containers?**

   **Answer.** `reserve(n)` prepares for at least `n` elements under the current maximum load factor, choosing an appropriate bucket count. `rehash(b)` requests at least `b` buckets and enough for the current elements/load constraint. Reserving an expected size before bulk insertion can avoid repeated O(n) rehashes.

5. **[Deep dive] What invalidates unordered-container iterators and references?**

   **Answer.** Rehashing invalidates all iterators but does not invalidate references/pointers to elements. Insertion invalidates iterators only if it causes a rehash; erasure invalidates only the erased element. `clear` invalidates all element handles. Do not rely on bucket position or iteration order, and distinguish iterator stability from reference stability.

6. **[Code] How should a custom key hash be defined?**

   **Answer.** Define equality from the key's logical identity and combine hashes of exactly the same fields. The combination must be deterministic during the key's stored lifetime; it need not be cryptographic.

   ```cpp
   struct Key { int tenant; std::string name; };

   struct KeyEqual {
       bool operator()(const Key& a, const Key& b) const noexcept {
           return a.tenant == b.tenant && a.name == b.name;
       }
   };

   struct KeyHash {
       std::size_t operator()(const Key& key) const noexcept {
           auto h1 = std::hash<int>{}(key.tenant);
           auto h2 = std::hash<std::string>{}(key.name);
           return h1 ^ (h2 + 0x9e3779b9u + (h1 << 6) + (h1 >> 2));
       }
   };
   ```

7. **[Deep dive] How can adversarial keys affect unordered containers?**

   **Answer.** If many keys collide, nominal O(1) operations become O(n), enabling denial of service for untrusted input. Standard `std::hash<string>` is not required to be collision-resistant or randomized. Security-sensitive servers may use a keyed hardened hash, validate/bound input, or use ordered containers with guaranteed logarithmic behavior.

8. **[Deep dive] Can keys be modified in place?**

   **Answer.** No—the key determines bucket placement and equality, so unordered set elements and map keys are const through iterators. Use erase/insert or extract a C++17 node handle, modify its key/value as permitted, then reinsert. A modified mutable object referenced indirectly by a key can still break hashing if the hash observes that external state.

9. **[Deep dive] What is heterogeneous lookup in unordered containers?**

   **Answer.** C++20 supplies transparent lookup overloads when both the hash and equality functors declare `is_transparent` and support the alternate key type. This can find a `string` key using `string_view` without allocation. The cross-type hash/equality relationship must still satisfy “equivalent implies identical hash.”

10. **[Design] How do you choose between ordered and unordered associative containers?**

    **Answer.** Choose ordered containers for deterministic sorted traversal, range/nearest queries, logarithmic worst-case operations, and stable iterators. Choose unordered containers for average constant exact-key lookup when memory/hash quality and nondeterministic order are acceptable. For small read-heavy sets/maps, a sorted vector or C++23 flat container may outperform both due to locality.

---

# 3. Container adapters and iterators

## 3.1. `std::stack` and `std::queue`

1. **[Basic] What is a container adapter?**

   **Answer.** It restricts an underlying sequence container to expose a specific abstract data type rather than offering a new storage model. `stack` exposes LIFO operations; `queue` exposes FIFO operations. The underlying container is a protected member and supplies storage/complexity/invalidation properties.

2. **[Basic] What are the main stack operations?**

   **Answer.** `push`/`emplace` add at the top, `top` observes the last element, `pop` removes it, and `empty`/`size` inspect state. `pop` returns `void`; read/move `top()` first if the value is needed. Separating access from removal avoids an awkward exception-safety tradeoff for returning a value while mutating the container.

3. **[Basic] What are the main queue operations?**

   **Answer.** `push`/`emplace` add at the back, `front` observes the next element, `back` observes the newest, and `pop` removes the front. The default underlying container is `deque`, which supports efficient front removal and back insertion. `list` can also satisfy the requirements; vector lacks `pop_front`.

4. **[Deep dive] Which containers can underlie `stack`?**

   **Answer.** The sequence must support `back`, `push_back`, and `pop_back`; `deque` is the default, while vector and list are common alternatives. Vector gives compact contiguous storage but may reallocate; deque grows at the end without relocating existing elements; list adds node overhead and usually has poor locality.

5. **[Design] Why do stack and queue not expose iterators?**

   **Answer.** Arbitrary traversal/mutation would weaken their LIFO/FIFO abstraction and let clients depend on representation. If traversal is a real requirement, use the underlying sequence directly or provide a domain-specific snapshot/view. Do not derive merely to expose the protected container; that couples code to non-contractual internals.

6. **[Deep dive] How does `priority_queue` differ from `queue`?**

   **Answer.** `priority_queue` exposes the highest-priority element according to a comparator and typically stores a heap in a vector; it is not FIFO and iteration order is unavailable. `top` is constant time, while insertion/removal are logarithmic. As with ordered containers, comparator semantics define which element is “top.”

## 3.2. Iterator categories, invalidation, and insertion iterators

1. **[Basic] What is an iterator?**

   **Answer.** It is a cursor abstraction that identifies a position in a range and supports operations determined by its category/concept. Algorithms work with iterators rather than concrete containers. A half-open range `[first, last)` includes positions from `first` up to but not including the sentinel/end.

2. **[Basic] What capabilities do the main iterator categories provide?**

   **Answer.** Input iterators support single-pass reading; output iterators support writing; forward iterators add multi-pass traversal; bidirectional iterators add decrement; random-access iterators add constant-time jumps, distance, indexing, and ordering; contiguous iterators additionally guarantee adjacent objects in memory. A stronger iterator can be used where a weaker one is required.

3. **[Deep dive] How do legacy iterator categories and C++20 iterator concepts differ?**

   **Answer.** Legacy categories are tag/associated-type requirements used by pre-ranges algorithms. C++20 concepts express operations and semantic requirements more directly and separate an iterator from its sentinel. Some proxy iterators do not behave exactly like raw references, so concept-based algorithms use `iter_value_t`, `iter_reference_t`, `iter_move`, and `indirectly_*` concepts to model them correctly.

4. **[Basic] Can the past-the-end iterator be dereferenced?**

   **Answer.** No. It marks a boundary and can be compared/advanced only as permitted; dereferencing it is invalid. For an empty range, `begin() == end()`. Operations that change a container may invalidate a previously saved `end()` even when existing element references remain valid.

5. **[Deep dive] Is there one universal iterator-invalidation rule?**

   **Answer.** No. It depends on container and operation: vector reallocation invalidates everything; list insertion invalidates nothing; unordered rehash invalidates iterators but not element references; deque has position-specific rules. Algorithms that reorder elements usually preserve iterator validity but change which value occupies a position. Consult the operation's contract whenever handles cross mutation.

6. **[Basic] What do `back_inserter`, `front_inserter`, and `inserter` do?**

   **Answer.** They create output iterators whose assignment calls `push_back`, `push_front`, or `insert` at a tracked position. They let algorithms write into a growing container without pre-sizing it. The target must support the corresponding operation; `front_inserter` reverses apparent order when elements are inserted one by one at the front.

   ```cpp
   std::vector<int> destination;
   destination.reserve(source.size());
   std::copy(source.begin(), source.end(), std::back_inserter(destination));
   ```

7. **[Deep dive] What are reverse and move iterators?**

   **Answer.** `reverse_iterator` adapts a bidirectional iterator so increment moves backward; its `base()` points one position after the element represented by the reverse iterator, a frequent off-by-one source. `move_iterator` makes dereference produce an xvalue-like reference so algorithms move from source elements; moved-from elements remain and must be left valid.

8. **[Basic] How do `iterator`, `const_iterator`, and a const container differ?**

   **Answer.** A mutable container's `iterator` usually permits modifying elements; `const_iterator` does not. Calling `begin()` on a const container returns a const iterator, while `cbegin()` requests one explicitly from either const or mutable containers. “Const iterator” means the pointed-to element is const through that iterator, not that the iterator variable itself cannot advance.

9. **[Code] How do you erase selected elements safely while iterating?**

   **Answer.** Use the iterator returned by `erase`, which denotes the next element; otherwise increment normally. This pattern works for standard sequence/associative containers with their respective erase-return contracts.

   ```cpp
   for (auto it = values.begin(); it != values.end(); ) {
       if (should_remove(*it)) {
           it = values.erase(it);
       } else {
           ++it;
       }
   }
   ```

10. **[Deep dive] What do C++20 ranges and sentinels change?**

    **Answer.** Range algorithms accept a range object and use concepts, projections, and safer return types. The end sentinel may have a different type from the iterator, naturally representing counted or null-terminated input. Views compose lazy transformations but often borrow underlying storage, so iterator/view lifetime remains critical.

---

# 4. Algorithms

## 4.1. `find`, `count`, `sort`, `copy`, `for_each`, and `transform`

1. **[Basic] What do `find` and `find_if` return?**

   **Answer.** They linearly scan `[first,last)` and return an iterator to the first matching element or `last` if none. `find` compares with a value using equality; `find_if` calls a predicate. The iterator remains subject to the source range's lifetime and invalidation rules.

2. **[Basic] How do `count` and `count_if` differ from `find`?**

   **Answer.** They scan the entire range and return the number of matches, so they cannot stop at the first match. Use `find`/`any_of` when only existence matters. The result type is the iterator's difference type, not necessarily `size_t`.

3. **[Basic] What are the requirements and guarantees of `std::sort`?**

   **Answer.** It requires random-access iterators and permutable/swappable elements. Complexity is O(N log N) comparisons in the standard guarantee for modern C++; implementations commonly use introspective hybrids. It is not stable: equivalent elements can reorder. `stable_sort` preserves relative order at potentially different memory/performance costs.

4. **[Deep dive] What must a sorting comparator satisfy?**

   **Answer.** It must impose a strict weak ordering and must not mutate elements in a way that affects comparisons. Return “is a ordered before b,” not “a <= b”; using `<=` violates irreflexivity. NaNs and stateful/time-varying comparators require care because inconsistent results break algorithm preconditions.

5. **[Basic] How does `copy` choose the output location?**

   **Answer.** It writes beginning at an output iterator supplied by the caller; it does not allocate or resize a destination container. The destination must already contain enough writable positions, or use an insertion iterator such as `back_inserter`. The returned iterator points just past the last output position.

6. **[Deep dive] How should overlapping copies be handled?**

   **Answer.** `std::copy` is appropriate when the destination does not begin inside the source range in a damaging direction. For overlapping movement toward higher addresses, use `std::copy_backward`; toward lower addresses, ordinary forward copy can be appropriate under its preconditions. For trivially copyable raw bytes, `memmove` explicitly supports overlap. Do not assume every algorithm behaves like `memmove`.

7. **[Basic] What does `for_each` return, and when is it preferable to a range-for loop?**

   **Answer.** It applies a callable and returns that callable (moved), allowing accumulated state to be recovered. A range-for loop is often clearer for ordinary side effects/control flow; `for_each` composes with iterator ranges and execution policies. The ordinary overload visits in order; parallel/unsequenced policy overloads change ordering/concurrency assumptions.

8. **[Basic] What does `transform` do?**

   **Answer.** Unary `transform` applies an operation to each input and writes results to an output range; binary transform combines corresponding elements from two inputs. It may write back to the same positions for supported in-place use. It does not resize containers and its operation must not invalidate iterators or improperly modify input/output ranges.

9. **[Deep dive] Is algorithm invocation order always specified?**

   **Answer.** No. Some sequential algorithms specify traversal order, while others such as transform permit implementation freedom; execution-policy overloads may run calls in parallel or unsequenced. Code must not rely on side-effect order unless the algorithm contract guarantees it. Pure transformations are easier to parallelize and reason about.

10. **[Design] Why prefer standard algorithms over handwritten loops?**

    **Answer.** They communicate intent, encode tested boundary behavior, work across iterator/range types, and can use optimized implementations/execution policies. A loop remains preferable when control flow or coupled state does not map cleanly; forcing an algorithm chain can reduce clarity. With C++20 ranges, projections and views cover many previously awkward cases.

11. **[Deep dive] Why can `std::sort` outperform C's `qsort`, even when both sort the same array?**

    **Answer.** `qsort` erases the element type behind `void*`, receives the element size at runtime, and normally calls a comparator through a function pointer. That ABI is flexible, but it limits type checking and can prevent the compiler from inlining the comparison into the sorting loop. `std::sort` is instantiated for the iterator and comparator types, so the compiler sees element operations and a small lambda/function object, can inline them, and can optimize the combined loop. It also expresses the range as `[first,last)` without casts or a separate byte size.

    Faster is not a language guarantee: library implementation, data distribution, comparator cost, code size, link-time optimization, and hardware all matter. Benchmark optimized equivalent programs on identical pre-generated inputs, verify the results, exclude setup/I/O from the timed region, and repeat enough runs to inspect the distribution rather than relying on one timing.

## 4.2. `lower_bound`, `upper_bound`, `remove_if`, `reverse`, `unique`, and erase-remove

1. **[Basic] What does `lower_bound` return?**

   **Answer.** In a range partitioned according to the comparison with a value—normally sorted—it returns the first position whose element is not less than the value. This is the insertion position that preserves ordering before existing equivalents. If the precondition is violated, the result is not meaningful and behavior is undefined under the algorithm's requirements.

2. **[Basic] What does `upper_bound` return?**

   **Answer.** It returns the first position at which the value would be ordered before the element—equivalently the first element greater than the value under an ordinary ascending comparator. `[lower_bound(x), upper_bound(x))` spans all elements equivalent to `x`; `equal_range` computes the pair.

3. **[Deep dive] Is binary search always O(log N)?**

   **Answer.** `lower_bound`/`upper_bound` make logarithmically many comparisons, but with non-random-access iterators they may perform linear iterator increments. On `map`/`set`, use the member functions, which navigate the tree in logarithmic time rather than advancing a generic forward iterator linearly.

4. **[Basic] What does `remove_if` actually remove?**

   **Answer.** It does not change container size. It moves/assigns retained elements toward the front and returns a new logical end; elements in `[new_end, old_end)` remain valid but have unspecified values. The container must then erase that tail if physical removal is required.

5. **[Code] What is the erase-remove idiom?**

   **Answer.** For pre-C++20 sequence containers such as vector/string/deque:

   ```cpp
   values.erase(std::remove_if(values.begin(), values.end(), predicate),
                values.end());
   ```

   C++20 `std::erase_if(values, predicate)` expresses the complete operation directly. Associative containers cannot move keys this way and use iterator/key erasure or their `erase_if` overloads.

6. **[Basic] What does `reverse` require and guarantee?**

   **Answer.** It requires bidirectional iterators and swaps symmetric elements in place, performing half the range length swaps. Iterators still identify positions but the values at those positions change. Use `views::reverse` in C++20 for a lazy reversed view without mutating the underlying range.

7. **[Basic] What does `unique` do?**

   **Answer.** It removes consecutive duplicates logically by compacting one representative of each adjacent equivalence group and returning a new logical end. Like `remove`, it does not resize the container. To remove all equal values regardless of original position, sort first when reordering is acceptable, then call `unique` and erase the tail.

8. **[Deep dive] Why can an invalid comparator break both sorting and binary search?**

   **Answer.** Sorting assumes strict weak ordering to construct a partitioned range; binary search assumes the range is partitioned under a compatible comparator. If one operation compares by one field/direction and the other by another, results are incorrect even though types compile. Reuse the comparator/projection and test boundary/duplicate cases.

9. **[Code] How do you insert into a sorted vector while preserving order?**

   **Answer.** Find the position with `lower_bound` and insert there; use `upper_bound` if new equivalents should follow existing ones.

   ```cpp
   auto pos = std::lower_bound(values.begin(), values.end(), value);
   values.insert(pos, value);
   ```

   Search is logarithmic but insertion is linear due to shifting and may invalidate iterators through reallocation.

10. **[Design] When is a sorted vector preferable to a tree set/map?**

    **Answer.** It is excellent for small or read-mostly collections built in batches: compact storage, fast iteration, cache-friendly binary search, and low overhead. Tree containers win when frequent mid-sequence insertion/erasure, stable node references, or node extraction/splicing semantics matter. C++23 flat associative adapters standardize the contiguous sorted approach.

---

# 5. Vocabulary and sum types

## 5.1. `std::optional`

1. **[Basic] What does `std::optional<T>` represent?**

   **Answer.** It contains either one `T` or no value, making optional presence explicit without a sentinel or heap allocation. The `T` object is stored within the optional's own storage when engaged. It is well suited to “not found” or independently optional fields when absence is not itself an error needing explanation.

2. **[Basic] How is an optional constructed and inspected?**

   **Answer.** Default construction or `std::nullopt` creates an empty optional; a `T`, `std::in_place`, or `emplace` creates the value. Use `has_value()` or contextual boolean conversion to test. `reset()` destroys the contained value and disengages it.

3. **[Basic] How do `operator*`, `value()`, and `value_or()` differ?**

   **Answer.** `*opt`/`opt->` require engagement and otherwise have undefined behavior; `value()` checks and throws `std::bad_optional_access`; `value_or(fallback)` returns the value or a converted fallback by value. The fallback expression is evaluated before the call even when the optional is engaged, so do not put expensive lazy computation there without an explicit branch/monadic operation.

4. **[Deep dive] What happens when an optional is moved?**

   **Answer.** If engaged, its contained `T` is moved to the destination. The source optional generally remains engaged but contains a moved-from `T`; it is not automatically reset. Test/reset it explicitly if the program needs an empty postcondition.

5. **[Deep dive] Can `optional` contain a reference or represent detailed failure?**

   **Answer.** Standard `optional<T&>` is not permitted in C++17/20/23. Use `optional<reference_wrapper<T>>`, a pointer for optional borrowing, or redesign lifetime. `optional` explains only presence; use `expected<T,E>` (C++23), a variant, or an error result when callers need to know why a value is absent.

6. **[Code] Why can `optional<bool>` be confusing?**

   **Answer.** It has three states—empty, contained false, contained true—but `if (opt)` tests engagement, not the contained boolean. An engaged `false` enters the branch. Write the intended check explicitly, for example `opt.value_or(false)` or `opt && *opt`, and consider a descriptive enum for domain states.

7. **[Deep dive] Which monadic optional operations were added in C++23?**

   **Answer.** `and_then` chains a function returning another optional, `transform` maps the contained value, and `or_else` handles absence. They express pipelines without repeated engagement checks while preserving short-circuit behavior. Lambdas can still capture lifetime-sensitive references, so compositional syntax does not remove ownership concerns.

## 5.2. `std::variant` and `std::visit`

1. **[Basic] What is `std::variant<Ts...>`?**

   **Answer.** It is a type-safe tagged union that contains exactly one alternative at a time (except the rare `valueless_by_exception` state). Storage is inline and large/aligned enough for its largest alternative plus discriminator/overhead. Alternatives form a closed compile-time set.

2. **[Basic] Which alternative is default-constructed?**

   **Answer.** The first alternative is default-constructed, so the variant is default-constructible only when that alternative is. Put `std::monostate` first to represent an empty/default state when no domain alternative should be default-constructed.

3. **[Basic] How is the active alternative inspected/accessed?**

   **Answer.** `holds_alternative<T>` tests by unique type; `index()` reports the zero-based alternative; `get<T>`/`get<I>` returns or throws `bad_variant_access` if wrong; `get_if` returns a pointer or null. When a type appears more than once, type-based access is ill-formed and index-based access is required.

4. **[Basic] What does `std::visit` do?**

   **Answer.** It invokes a visitor with the active alternative(s), providing exhaustive type-safe dispatch without manual index switches. The visitor must be invocable for every possible combination and produce a compatible result type under the selected standard rules.

   ```cpp
   struct Overloaded {
       void operator()(int value) const { use_integer(value); }
       void operator()(const std::string& value) const { use_text(value); }
   };

   std::visit(Overloaded{}, value);
   ```

5. **[Code] What is the common overloaded-lambda helper?**

   **Answer.** It combines several closure `operator()` members into one visitor:

   ```cpp
   template<class... Fs>
   struct overloaded : Fs... { using Fs::operator()...; };
   template<class... Fs>
   overloaded(Fs...) -> overloaded<Fs...>;

   std::visit(overloaded{
       [](int x) { /* ... */ },
       [](const std::string& s) { /* ... */ }
   }, value);
   ```

6. **[Deep dive] What is `valueless_by_exception`?**

   **Answer.** During replacement of an alternative, construction/move can throw after the old value is gone, leaving no alternative active; `valueless_by_exception()` reports it and `index()` returns `variant_npos`. Implementations avoid it when guarantees permit, but robust generic visitors should understand the possibility. Non-throwing moves reduce the risk.

7. **[Design] When is variant preferable to virtual polymorphism?**

   **Answer.** Use it when the set of types is closed/known, value semantics and inline storage are useful, and operations can be expressed as visitors. A hierarchy is better when third parties add types independently and operations are stable. Variant makes adding an operation easy but adding an alternative requires updating exhaustive visitors; inheritance has the opposite extensibility bias.

8. **[Deep dive] How do copy/move/destruction properties of a variant arise?**

   **Answer.** Its corresponding operation is available only when all alternatives satisfy required properties and may be trivial/noexcept only when conditions allow. A single noncopyable alternative makes the whole variant noncopyable. Large alternatives make every variant object large; storing owning pointers as alternatives trades inline size for allocation/indirection.

## 5.3. `std::any`

1. **[Basic] What does `std::any` represent?**

   **Answer.** It is a type-erased container holding one value of an arbitrary CopyConstructible type or nothing. Unlike variant, the set of possible types is open and not represented in the static type. `has_value()`, `type()`, `reset`, and `emplace` manage/inspect the erased value.

2. **[Basic] How does `any_cast` behave?**

   **Answer.** Value/reference forms such as `any_cast<T&>(a)` throw `std::bad_any_cast` on a type mismatch. Pointer forms such as `any_cast<T>(&a)` return null instead. Matching is exact after the API's cv/reference rules; it does not perform arbitrary numeric/base conversions.

3. **[Deep dive] Does `any` allocate?**

   **Answer.** It may allocate dynamically. Implementations commonly use small-object optimization for suitably small, non-throwing-movable values, but buffer size/eligibility are not portable guarantees. Type erasure also adds indirect operations and prevents ordinary compile-time exhaustiveness.

4. **[Design] When is `any` appropriate?**

   **Answer.** It is useful for heterogeneous property bags, framework extension points, and metadata when types are genuinely open and callers have an external type agreement. Overuse moves type errors to runtime, hides schema/ownership, and creates string-key plus cast protocols. Prefer a domain type, variant, or polymorphic interface when the alternatives/contract are known.

5. **[Deep dive] Why can `any` not directly hold a move-only value in C++17/20/23?**

   **Answer.** `any` is copyable and requires its contained type to be CopyConstructible so copying the wrapper has defined behavior. Wrap shared ownership if that matches semantics or use a move-only type-erasure abstraction/custom variant. Do not add shared ownership solely to satisfy the container without considering lifetime meaning.

## 5.4. `std::pair` and `std::tuple`

1. **[Basic] What do pair and tuple provide?**

   **Answer.** `pair<T,U>` groups two values as `first`/`second`; `tuple<Ts...>` groups a fixed heterogeneous sequence accessed by index or unique type. They provide value semantics, comparisons where element types support them, and integrate with structured bindings and generic tuple-like protocols.

2. **[Basic] How do structured bindings work with them?**

   **Answer.** `auto [key, value] = pair;` copies/moves elements into a hidden value; `auto& [key, value] = pair;` binds to existing elements; `const auto&` avoids copying and prevents mutation. In map loops, the key binding is const because elements are `pair<const Key,T>`.

3. **[Deep dive] What do `make_pair`, `make_tuple`, `tie`, and `forward_as_tuple` do?**

   **Answer.** `make_pair`/`make_tuple` deduce decayed value types. `tie` creates a tuple of lvalue references, useful for unpacking/comparison and `std::ignore`. `forward_as_tuple` stores forwarding references and does not extend temporary lifetimes; retaining its result can dangle, so it is mainly for immediate forwarding/construction.

4. **[Basic] How are pairs/tuples compared?**

   **Answer.** Comparisons are lexicographic: compare the first differing element, subject to each element's operators and standard version. This is convenient for multi-key ordering, for example `std::tie(a.last, a.first) < std::tie(b.last, b.first)`, but every compared field must produce a valid consistent ordering.

5. **[Deep dive] What do `tuple_cat` and `apply` do?**

   **Answer.** `tuple_cat` concatenates tuple-like objects into a new tuple with deduced element/reference types. `std::apply(f, tuple)` expands tuple elements as arguments to `f`, useful for deferred calls and generic construction. Reference elements still carry lifetime constraints.

6. **[Design] When should a named struct replace a pair/tuple?**

   **Answer.** Use a named domain type when fields have meaning beyond a local mechanical grouping, invariants/behavior, documentation, or API longevity. `pair` is excellent for generic two-part results and map elements; tuples are useful for local plumbing/metaprogramming. Public APIs returning `tuple<int,string,bool>` force callers to remember positional meaning and evolve poorly.

---

# 6. Time and concurrency

## 6.1. `std::chrono`: durations and time points

1. **[Basic] What is a `std::chrono::duration`?**

   **Answer.** It represents a number of ticks with a representation type and compile-time period ratio. `milliseconds` is `duration<integer-like, milli>`, while `duration<double>` can represent fractional seconds. Units are part of the type, preventing accidental unchecked mixing and enabling compile-time conversions.

2. **[Deep dive] When is a duration conversion implicit, and when is `duration_cast` needed?**

   **Answer.** Conversions that cannot lose tick information under the representation/period rules are implicit (for example integral seconds to integral milliseconds). Potentially truncating conversions such as milliseconds to integral seconds require `duration_cast`; C++17 also provides `floor`, `ceil`, and `round` for explicit rounding semantics. Overflow of the representation remains the programmer's responsibility.

3. **[Basic] What is a `time_point`?**

   **Answer.** It represents a duration since a particular clock's epoch: `time_point<Clock, Duration>`. Subtracting two points from the same clock yields a duration; adding/subtracting a duration yields a point. Time points from unrelated clocks are not implicitly interchangeable because their epochs/properties differ.

4. **[Basic] How do `system_clock` and `steady_clock` differ?**

   **Answer.** `system_clock` relates to civil/wall time and can be converted to/from `time_t`, but it may jump when the clock is adjusted. `steady_clock` is monotonic and never moves backward, making it suitable for elapsed times and deadlines. `high_resolution_clock` is an implementation-chosen clock/alias and is not guaranteed steady.

5. **[Code] How should an operation with a timeout compute its deadline?**

   **Answer.** Use a steady absolute deadline so repeated waits do not restart the full relative timeout after spurious wakeups/interruption.

   ```cpp
   const auto deadline = std::chrono::steady_clock::now() + timeout;
   while (!ready()) {
       if (cv.wait_until(lock, deadline) == std::cv_status::timeout && !ready()) {
           return Error::timeout;
       }
   }
   ```

6. **[Deep dive] What do chrono literals provide?**

   **Answer.** With `using namespace std::chrono_literals;`, suffixes such as `250ms`, `2s`, `5min`, and `24h` produce typed durations. They improve readability and prevent unit comments/scale mistakes. Avoid broad using-directives in headers; a local function scope is appropriate.

7. **[Deep dive] What did C++20 add for calendars and time zones?**

   **Answer.** It added civil calendar types (`year`, `month`, `day`, `year_month_day`), clock conversions, formatting/parsing support, and a time-zone database API with `zoned_time` where implemented. Civil time is not a fixed duration: daylight-saving transitions can create ambiguous/nonexistent local times. Store unambiguous instants plus zone identifiers when future local presentation matters.

8. **[Design] Which time concept should an API accept?**

   **Answer.** Accept a duration for “how long,” a steady-clock time point/deadline for monotonic timeout scheduling, and a system/civil timestamp for externally meaningful wall time. Make units and clock explicit in the type. Inject a clock/provider into deterministic tests rather than sleeping against real time.

## 6.2. `std::thread`, `join`, and `detach`

1. **[Basic] What happens when constructing a `std::thread`?**

   **Answer.** It starts a new thread of execution that invokes a decayed copy/move of the callable and arguments (subject to standard invocation rules). Construction can throw if the system cannot create a thread. Argument passing is not ordinary reference binding: use `std::ref`/`std::cref` for intentional references and ensure their lifetimes exceed execution.

2. **[Basic] What do `join` and `detach` do?**

   **Answer.** `join` blocks until the thread completes and synchronizes with its completion, then makes the `std::thread` non-joinable. `detach` separates the handle; the thread continues independently and its resources are reclaimed on exit, but the program can no longer join/control it through that object. Detached code must not outlive referenced state or the process shutdown plan.

3. **[Deep dive] What happens if a joinable `std::thread` is destroyed?**

   **Answer.** `std::terminate()` is called. This prevents silently detaching a thread whose lifetime/ownership is unclear. Use an RAII joining wrapper, guarantee join on every path, or prefer C++20 `std::jthread`, whose destructor requests stop and joins.

4. **[Deep dive] How does `std::jthread` improve ownership?**

   **Answer.** It is move-only like thread, automatically joins at destruction, and integrates cooperative cancellation through `stop_source`, `stop_token`, and `stop_callback`. If the callable can accept a `stop_token` as its first argument, jthread supplies one. Stop is a request: work must observe it and make blocking operations cancellable/wakeable.

5. **[Deep dive] What happens when an exception escapes a thread function?**

   **Answer.** `std::terminate()` is called; it does not automatically travel to the joining thread. Catch within the thread and communicate via `promise`/`future`, `exception_ptr`, a task abstraction, or another synchronized result. Exceptions stored in a future are rethrown by `get()`.

6. **[Deep dive] Is `std::thread::hardware_concurrency()` a reliable worker count?**

   **Answer.** It is only a hint and may return zero. It can reflect logical processors rather than physical cores and may ignore container CPU quotas, affinity, NUMA, or workload blocking. Use it as an initial input, cap concurrency, and tune/measure under deployment constraints.

7. **[Code] What is dangerous about this capture?**

   ```cpp
   void start() {
       std::string message = "work";
       std::thread([&] { use(message); }).detach();
   }
   ```

   **Answer.** The detached thread captures a reference to a local destroyed when `start` returns, producing a dangling access. Capture ownership by value/move and still define shutdown/error handling, or keep a joinable/jthread owner whose lifetime encloses the referenced state.

## 6.3. `std::mutex`, `lock_guard`, `unique_lock`, and `shared_lock`

1. **[Basic] What does `std::mutex` provide?**

   **Answer.** Non-recursive mutual exclusion plus synchronization: a successful lock acquisition observes memory effects sequenced before the previous unlock. A thread must not lock the same ordinary mutex twice without unlocking, and only the owner unlocks it. Protect an invariant consistently; the mutex does nothing for code that bypasses it.

2. **[Basic] How does `lock_guard` work?**

   **Answer.** It locks a mutex on construction and unlocks on destruction, is noncopyable, and has minimal API. Use it for a straightforward lexical critical section. It makes early returns and exceptions safe by tying ownership to scope.

3. **[Basic] When is `unique_lock` needed?**

   **Answer.** It is a movable mutex-ownership object that supports deferred locking, try/timed locking (when the mutex supports it), manual unlock/relock, ownership transfer, and condition-variable waiting. That flexibility costs a little state. Use it when the lock lifetime is not simply the whole scope; otherwise `lock_guard` is clearer.

4. **[Deep dive] What do `defer_lock`, `try_to_lock`, and `adopt_lock` mean?**

   **Answer.** `defer_lock` constructs without locking; `try_to_lock` attempts without blocking; `adopt_lock` assumes the current thread already owns the mutex and makes the wrapper responsible for unlocking. An incorrect `adopt_lock` is undefined behavior. These tags make ownership transitions explicit but require careful preconditions.

5. **[Basic] What do `shared_mutex` and `shared_lock` provide?**

   **Answer.** Multiple threads can hold shared ownership for reading, while one `unique_lock`/exclusive owner blocks all others for writing. `shared_lock` manages shared ownership via RAII. Reader-writer locking helps only when reads are sufficiently long/frequent; fairness is implementation-dependent and bookkeeping can cost more than a plain mutex.

6. **[Deep dive] How should several mutexes be acquired?**

   **Answer.** Use a global lock order or `std::scoped_lock(m1, m2, ...)`, which uses a deadlock-avoidance algorithm and releases all on scope exit. `std::lock` can acquire several deferred `unique_lock`s. All code paths must follow the same protocol; hidden locks in callbacks can reintroduce cycles.

7. **[Deep dive] Why should callbacks and blocking I/O usually run outside a lock?**

   **Answer.** Their duration/reentrancy is uncontrolled: callbacks can call back into the object or acquire locks in another order, while I/O can block indefinitely. Copy/move the required stable state under the lock, release it, then invoke external work. Revalidate state afterward if the operation is optimistic.

8. **[Design] Does `const` make an object thread-safe?**

   **Answer.** No. Const is a type-level mutation restriction through that access path; mutable caches, pointed-to state, and other aliases can still change. Standard containers permit concurrent const operations on the same object under their specified thread-safety rules, but any overlapping mutation needs synchronization, and element-level independent updates require careful rules.

## 6.4. `condition_variable`, `future`, `promise`, and `async`

1. **[Basic] What does `condition_variable::wait` do?**

   **Answer.** Given a `unique_lock<mutex>`, it atomically releases the mutex and blocks; before returning it reacquires the mutex. The predicate overload repeatedly waits until `predicate()` is true. State is protected by the mutex, not by the notification itself.

2. **[Basic] What is a spurious wakeup?**

   **Answer.** A wait may return even though no useful notification/condition occurred. Therefore always wait in a loop or use `wait(lock, predicate)`. Even a genuine notification does not imply the predicate is still true when this thread reacquires the mutex because another thread may consume/change the state first.

3. **[Deep dive] What is a lost wakeup?**

   **Answer.** Condition-variable notifications are not stored as durable events. If code checks state without the mutex and begins waiting after a notification, it can sleep forever. Correct code changes and checks the predicate under the same mutex and uses atomic unlock-and-wait; if the predicate was already true, the predicate overload does not sleep.

4. **[Deep dive] Should notification happen while holding the mutex?**

   **Answer.** The shared predicate must be modified while locked. Notification can legally occur before or after unlock; notifying after unlock often avoids waking a thread only to have it block on the mutex, while notifying under lock can simplify certain lifetime/handoff protocols. Correctness comes from predicate synchronization, so choose based on the exact design and measurement.

5. **[Basic] What do `promise` and `future` represent?**

   **Answer.** They form a one-shot shared state: a producer sets a value or exception through `promise<T>`, and a consumer waits/gets it through `future<T>`. `future::get()` returns/rethrows and consumes the result, so it can be called once. `shared_future` is copyable and permits multiple reads/waiters.

6. **[Deep dive] What is a broken promise?**

   **Answer.** If a promise is destroyed without setting a value/exception, the shared state becomes ready with `future_error(broken_promise)`. This prevents a waiting future from blocking forever because its producer disappeared. Destroying a future does not generally signal cancellation to the producer.

7. **[Basic] What does `std::async` do?**

   **Answer.** It invokes a callable and returns a future for its result/exception. With `launch::async`, execution occurs as if on a new thread; with `launch::deferred`, execution starts synchronously when a waiting/get operation demands it. The default permits the implementation to choose either (and implementation-defined additional policies), so request a policy when concurrency is semantically required.

8. **[Deep dive] Why can destroying an `async` future block?**

   **Answer.** A future referring to a task launched with `std::async` under `launch::async` may wait for completion in its destructor when it is the last reference and has not already waited. A discarded temporary can therefore make apparently asynchronous calls serialize. Store futures and manage lifetime explicitly; async is not a general-purpose thread pool.

9. **[Deep dive] What are `packaged_task` and `shared_future` useful for?**

   **Answer.** `packaged_task<R(Args...)>` wraps a callable so invoking it fulfills an associated future, useful when a scheduler/queue controls execution. `shared_future` lets several consumers wait/read one result and is obtained via `future::share()`. Neither provides cancellation/backpressure automatically.

10. **[Design] How should task cancellation be modeled?**

    **Answer.** Futures/promises themselves do not stop work. Use a cooperative cancellation token/flag (C++20 stop tokens where integrated), wake blocking waits, define cancellation points, and ensure cleanup/invariants. Distinguish “request sent” from “task stopped”; abandoned results still need owned task lifetime and exception handling.

## 6.5. `std::atomic`, data races, happens-before, and the memory model

1. **[Basic] What does `std::atomic<T>` provide?**

   **Answer.** Accesses through the atomic interface are indivisible with respect to other atomic accesses to that object and participate in the C++ memory model. It prevents a data race on that atomic object; it does not automatically make surrounding non-atomic data or multi-step invariants safe. Available operations depend on `T` and the standard version.

2. **[Basic] What is a C++ data race?**

   **Answer.** Two potentially concurrent conflicting actions access the same memory location, at least one is a write, neither is atomic, and no happens-before ordering exists between them. A data race is undefined behavior. “It works on x86” or a debugger observation does not repair the missing language-level synchronization.

3. **[Deep dive] What is happens-before?**

   **Answer.** It is the formal ordering that makes side effects visible and rules out data races, built from sequencing within a thread plus inter-thread synchronization such as mutex unlock/lock, thread start/join, and compatible atomic release/acquire operations. Real-time chronology alone is insufficient: if two threads happen to run in one order without synchronization, the standard need not make writes visible accordingly.

4. **[Basic] What are atomic read-modify-write operations?**

   **Answer.** Operations such as `fetch_add`, `exchange`, and successful `compare_exchange` atomically read and update as one modification, with a defined position in the atomic's modification order. Writing `load(); compute; store();` is not equivalent because another update can occur between steps.

5. **[Deep dive] What does `memory_order_relaxed` guarantee?**

   **Answer.** Atomicity and a coherent modification order for that atomic, but no synchronization/ordering of other memory. It is appropriate for independent statistics/counters or algorithms that establish ordering elsewhere. It is not appropriate for publishing ordinary data to another thread by itself.

6. **[Deep dive] How do release and acquire publish data?**

   **Answer.** Non-atomic writes sequenced before a release store become visible to a thread whose acquire load reads that store (or appropriate release sequence), establishing synchronization/happens-before.

   ```cpp
   Data data;
   std::atomic<bool> ready{false};

   // Producer:
   data = build_data();
   ready.store(true, std::memory_order_release);

   // Consumer:
   if (ready.load(std::memory_order_acquire)) {
       use(data); // producer's prior writes are visible
   }
   ```

   All accesses to `data` must still follow this protocol.

7. **[Deep dive] What does sequential consistency provide?**

   **Answer.** The default `memory_order_seq_cst` provides acquire/release effects as appropriate plus a single total order of sequentially consistent atomic operations consistent with thread order. It is the easiest order to reason about, though sometimes costlier on weakly ordered hardware. Start with it unless a measured need and proof justify weaker ordering.

8. **[Deep dive] How do `compare_exchange_weak` and `compare_exchange_strong` differ?**

   **Answer.** Both compare the atomic with `expected`; on success they store desired, while on failure they update `expected` with the observed value. The weak form may fail spuriously and is intended for retry loops; the strong form avoids spurious failure and is convenient for one-shot decisions, though real value changes can still make it fail.

   ```cpp
   auto current = counter.load();
   while (!counter.compare_exchange_weak(current, current + 1)) {
       // current was updated; recompute and retry
   }
   ```

9. **[Deep dive] Are atomic operations always lock-free?**

   **Answer.** No. Use `is_lock_free()`/`is_always_lock_free` for the type/platform; an implementation may use internal locks. Lock-free means system-wide progress under suspension of one thread, not wait-free completion for every thread, fairness, or higher performance. Correct reclamation makes lock-free pointer structures particularly difficult.

10. **[Deep dive] What is the ABA problem?**

    **Answer.** A compare-and-swap observes value A both before and after another thread changed A→B→A, so it cannot tell that the underlying object/state changed. Pointers are vulnerable if memory is removed and reused. Tagged/versioned pointers, hazard pointers, epoch reclamation, reference counting, or locks address different aspects; CAS alone is not safe reclamation.

11. **[Deep dive] What did C++20 atomic wait/notify add?**

    **Answer.** `atomic::wait(old)` blocks efficiently until notified and the value differs from `old`; `notify_one/all` wakes waiters. Implementations can use OS primitives rather than spinning. Callers still loop/check state because value changes can race and ABA can make the same value reappear; notifications are tied to the atomic-value protocol, not a general event count.

12. **[Basic] Why is `volatile` not a thread-synchronization primitive?**

    **Answer.** It neither makes accesses atomic nor creates happens-before or inter-thread visibility guarantees. It is intended for implementation-defined interactions such as memory-mapped device I/O, not shared-memory concurrency. Use atomics, mutexes, condition variables, or other standard synchronization.

13. **[Design] When should a mutex be preferred over atomics?**

    **Answer.** Prefer a mutex for compound invariants, multiple fields, blocking coordination, or whenever the lock-based solution is clearer. Atomics are ideal for small independent state and proven nonblocking algorithms, but memory-order/reclamation bugs are severe. Measure contention: uncontended mutexes are often cheap, while a hot atomic cache line can serialize cores.

---

# 7. C++20 and C++23 library/language overview

## 7.1. Important C++20 features

1. **[Basic] What are the major themes of C++20?**

   **Answer.** It is a large release adding concepts, ranges, modules, coroutines, three-way comparison, designated initialization, expanded constant evaluation, and `char8_t` to the language. Major library additions include `span`, `format`, calendar/time-zone support, `jthread`/stop tokens, synchronization primitives, atomic waiting, bit operations, and `source_location`.

2. **[Basic] What do concepts and requires-expressions improve?**

   **Answer.** They express template requirements as named constraints, remove invalid overloads cleanly, improve diagnostics, and participate in overload ordering. A requires-expression checks syntax/types/properties; it cannot generally prove semantic laws such as associativity. Prefer standard concepts (`ranges::range`, `integral`, `invocable`) and compose named concepts to preserve subsumption.

3. **[Basic] What are ranges and views?**

   **Answer.** Range algorithms accept ranges and use concepts/projections; views are lightweight, composable, often lazy range adaptors such as `filter` and `transform`. A pipeline does not usually produce a container until iterated/materialized. Views frequently borrow underlying storage, so returning a view of destroyed locals or mutating invalidating storage creates dangling behavior.

4. **[Deep dive] What problem do modules address?**

   **Answer.** Modules provide language-level import/export boundaries rather than textual header inclusion, aiming to reduce repeated parsing, macro leakage, and some ODR fragility. They do not automatically create a stable binary ABI or solve architecture. Build-system/compiler workflows and header-unit interactions require deliberate migration.

5. **[Deep dive] What are C++20 coroutines?**

   **Answer.** Functions using `co_await`, `co_yield`, or `co_return` can suspend and resume through compiler-generated state and a promise/handle protocol. The core language supplies mechanics, not a universal scheduler, task type, I/O runtime, or cancellation model. Lifetime of the coroutine frame, awaited objects, references, and destruction must be designed by the library type.

6. **[Basic] What does the three-way comparison operator `<=>` provide?**

   **Answer.** It produces an ordering category (`strong_ordering`, `weak_ordering`, or `partial_ordering`) and enables rewritten relational operators. Defaulting it can generate member-wise comparison and, under the language rules, a matching equality operation. Choose a category consistent with domain semantics; floating-point comparison is partial because of NaN.

7. **[Basic] What is `std::span`?**

   **Answer.** It is a non-owning view of a contiguous sequence, carrying a pointer and runtime or compile-time extent. It accepts arrays, vectors, and compatible contiguous ranges without allocation and exposes bounds-aware size/iteration (though `operator[]` is not generally checked). It does not extend lifetime and must not outlive/reallocation-invalidate the source.

8. **[Basic] What do `std::jthread` and stop tokens add?**

   **Answer.** `jthread` automatically joins on destruction and integrates cooperative stop requests. A `stop_token` can be polled or registered with `stop_callback`; requesting stop does not forcibly terminate code. Blocking operations need a cancellation-aware wait/wakeup path, and cleanup must still follow RAII.

9. **[Basic] Which synchronization primitives were added?**

   **Answer.** `counting_semaphore`/`binary_semaphore` manage permits, `latch` is a one-shot countdown gate, and `barrier` is reusable across phases with an optional completion step. Atomic `wait`/`notify` provides efficient value-based waiting. Each solves a different coordination pattern and does not replace mutex protection of arbitrary invariants.

10. **[Basic] What do `std::format` and `std::source_location` provide?**

    **Answer.** `format` offers typed, positional formatting without stream-state side effects and reports format errors under its contract; C++23 extends compile-time checking/usability through related APIs. `source_location::current()` captures file, line, column, and function at the call site when used as a default argument, enabling logging/assertion APIs without macros for many cases.

11. **[Basic] How do `consteval` and `constinit` differ?**

    **Answer.** A `consteval` immediate function must be evaluated at compile time for ordinary calls. `constinit` applies to static/thread-storage variables and requires static initialization, preventing a dynamic-initialization-order problem; it does not make the variable const. `constexpr` makes constant evaluation possible/required only in constant-expression contexts.

12. **[Deep dive] What restrictions do C++20 designated initializers have?**

    **Answer.** They apply to aggregate class members, use `.member = value`, and must follow declaration order. C++ does not support C's arbitrary ordering, nested designator syntax, or array index designators, and designated/non-designated clauses cannot be freely mixed at one level. Adding constructors/private members may remove aggregate eligibility and break callers.

13. **[Basic] Which bit/representation facilities were added?**

    **Answer.** `<bit>` includes `bit_cast`, `endian`, rotations, population count, leading/trailing zero/count, and power-of-two helpers. `bit_cast<To>(from)` safely copies object representation between equal-size trivially copyable types without aliasing casts, but resulting values and padding still require valid representations and protocol endianness handling.

14. **[Deep dive] What did chrono add in C++20?**

    **Answer.** Calendar types, days/weeks/months/years durations, clock conversions, time-zone database/zoned time, and chrono formatting/parsing make civil-time work more type-safe. Local times can be ambiguous or nonexistent at time-zone transitions. Implementation availability/data deployment must be checked without making the domain depend on a hard-coded offset.

15. **[Deep dive] How does `co_await expression` obtain and use an awaiter, including `operator co_await`?**

    **Answer.** In an ordinary coroutine body, the promise may first transform the operand through `promise.await_transform(expression)`. The resulting awaitable is then converted to an awaiter by a member `operator co_await`, a non-member overload found by argument-dependent lookup under the coroutine rules, or, if neither applies, by using the awaitable itself. `operator co_await` is therefore a customization step that adapts a domain object to the awaiter protocol; it does not itself suspend the coroutine.

    The compiler calls `await_ready()` first. If it returns true, execution continues without suspension. Otherwise it suspends and calls `await_suspend(coroutine_handle)`: a `void` return leaves it suspended, `bool` keeps it suspended for true and resumes it for false, and a returned coroutine handle transfers execution to that coroutine. When the original coroutine continues, `await_resume()` supplies the value of the `co_await` expression or throws. The protocol provides mechanics only: the awaitable library must define scheduling, ownership, cancellation, thread-affinity, and the lifetime of both the coroutine frame and operation state.

## 7.2. Important C++23 features

1. **[Basic] What is the character of C++23 compared with C++20?**

   **Answer.** It is more incremental: it completes and improves ranges/constexpr/library ergonomics while adding several important vocabulary and utility types. Notable additions include `expected`, `print`, `stacktrace`, `mdspan`, flat associative containers, `move_only_function`, range materialization/adaptors, explicit object parameters, and many smaller APIs.

2. **[Basic] What is `std::expected<T,E>`?**

   **Answer.** It contains either a success `T` or an error `E`, providing explicit typed recoverable failure without exception propagation. `unexpected(error)` constructs the error case; accessors include `value`, `error`, dereference, and boolean checks. Monadic `and_then`, `transform`, `or_else`, and `transform_error` support pipelines. It is not a replacement for exceptions in every layer or for bug/precondition handling.

3. **[Basic] What range improvements stand out in C++23?**

   **Answer.** `ranges::to` materializes a range into a container; new views include `zip`, `zip_transform`, `adjacent`, `adjacent_transform`, `chunk`, `chunk_by`, `slide`, `stride`, `cartesian_product`, `join_with`, `repeat`, and `enumerate`. Fold algorithms and additional range algorithms reduce boilerplate. Lazy-view lifetime and single-pass properties still matter.

4. **[Basic] What is `std::mdspan`?**

   **Answer.** It is a non-owning multidimensional view over existing contiguous/addressable storage with extents, layout mapping, and accessor policy. It does not allocate or own elements. Static/dynamic extents and layouts such as left/right/stride make scientific and tiled data access expressive without committing ownership to one container.

5. **[Basic] What are `flat_map`, `flat_multimap`, `flat_set`, and `flat_multiset`?**

   **Answer.** They are sorted contiguous-container adaptors offering associative lookup with strong locality and compact storage. Search is logarithmic, but insertion/erasure are linear due to shifting and can invalidate handles. They are well suited to read-heavy or batch-built data, while node-based containers remain better for frequent mutation/stability.

6. **[Basic] What do `std::print`/`println` and `std::stacktrace` provide?**

   **Answer.** `print`/`println` use format-style typed formatting and write directly to standard output or a `FILE*`, avoiding much iostream ceremony/state. `stacktrace` captures a representation of the current call stack for diagnostics. Symbol quality, inlining, optimization, debug files, and platform support affect stacktrace usefulness; it is not an error-recovery mechanism.

7. **[Basic] What is `std::move_only_function`?**

   **Answer.** It is a type-erased callable wrapper that can own move-only targets, unlike `std::function`'s CopyConstructible target requirement. It supports richer cv/ref/noexcept-qualified signatures. Type erasure still introduces indirect calls and possible allocation; templates remain preferable when the exact callable type can stay static.

8. **[Deep dive] What are explicit object parameters (“deducing `this`”)?**

   **Answer.** A member-like function can declare its object parameter explicitly, for example `void f(this Widget& self)`, enabling one template to deduce constness/value category/derived type and reducing duplicated ref-qualified overloads. It also enables recursive lambdas and CRTP-like patterns with different syntax. The function has restrictions and no implicit `this` inside; use `self`.

9. **[Basic] What does `if consteval` add?**

   **Answer.** It selects a branch specifically when evaluation occurs in a manifestly constant-evaluated context, with an `else` runtime path. This is clearer and semantically stronger than some `is_constant_evaluated()` patterns, especially when the compile-time branch calls immediate functions.

10. **[Basic] What small utilities are useful in C++23?**

    **Answer.** `std::byteswap` reverses integral byte order, `std::to_underlying` converts an enum to its underlying value, and `std::unreachable` tells the optimizer control cannot reach a point (reaching it is undefined behavior). `basic_string::contains`/`string_view::contains` improve membership checks. Use `unreachable` only for proven invariants, not input validation.

11. **[Deep dive] What are `out_ptr` and `inout_ptr` for?**

    **Answer.** They adapt smart pointers to C APIs that return resources through `T**` output/in-out parameters, arranging safe reset/adoption with a deleter. They reduce manual `release`/temporary raw-pointer code but require matching the C API's ownership and replacement semantics exactly. Prefer a dedicated RAII wrapper when the boundary has additional rules.

12. **[Basic] What is `std::generator`?**

    **Answer.** It is a coroutine-based synchronous range whose body uses `co_yield` to lazily produce elements. Iteration resumes the coroutine, so referenced inputs and the generator frame must remain valid; exceptions propagate during iteration. It is not an asynchronous stream or scheduler.

13. **[Deep dive] Which container/range construction APIs were improved?**

    **Answer.** `from_range` constructors and `assign_range`, `insert_range`, `append_range`, and `prepend_range` let containers consume ranges directly without spelling iterator pairs. Availability differs by container/operation. They preserve the destination's complexity/invalidation behavior and do not make a single-pass source reusable.

14. **[Design] How should a project adopt C++20/C++23 features safely?**

    **Answer.** Select a declared language/library baseline, enable it consistently in every translation unit, check compiler and standard-library feature-test macros rather than version folklore, and test all supported toolchains. Encapsulate poorly supported features behind small compatibility layers. Adoption should improve contracts/clarity; mixing configurations can create ODR/ABI problems.

---

# 8. Advanced algorithms and lock-free design

## 8.1. Sorting, selection, heaps, and container-aware lookup

1. **[Basic] Why does `std::sort` work with `vector` but not with `list`?**

   **Answer.** `std::sort` requires random-access iterators because its comparison and partitioning strategy jumps by arbitrary offsets and relies on constant-time iterator distance. `vector` and `deque` provide such iterators; `list` provides only bidirectional iterators. `list::sort` is a member algorithm designed for linked nodes: it can relink nodes without moving element values and preserves iterator/reference validity. Algorithm requirements are compile-time correctness constraints, not just performance hints.

2. **[Basic] How do `sort` and `stable_sort` differ?**

   **Answer.** Both order a random-access range according to a strict weak ordering. `stable_sort` additionally preserves the relative order of elements that compare equivalent, which matters for multi-stage sorting and records with hidden arrival order. `sort` is typically introsort and requires O(N log N) comparisons; `stable_sort` commonly uses merge techniques and may allocate temporary storage. Request stability only when it is part of the result's semantics.

3. **[Deep dive] When should you use `partial_sort` or `nth_element`?**

   **Answer.** `partial_sort(first, middle, last)` places the smallest `K = middle-first` elements in sorted order at the front, useful when the ordered top K is required; its typical comparison bound is O(N log K). `nth_element` partitions so the element at `nth` is the one that a full sort would place there, with no element before it greater and no element after it smaller; the two partitions are otherwise unsorted. It has expected/average linear behavior and is ideal for medians, percentiles, and an unordered top K followed by an optional sort.

4. **[Basic] What do the standard heap algorithms provide?**

   **Answer.** `make_heap` turns a random-access range into a max-heap under the comparator; `push_heap` and `pop_heap` maintain it as an element is appended or the top is moved to the end; `sort_heap` consumes a heap into sorted order. `priority_queue` packages the same concept as a restricted container adapter. Heap construction is linear, top access is constant time, and push/pop are logarithmic. A min-heap uses an inverted comparator such as `std::greater<>`.

5. **[Code] How would you compute the largest K elements without sorting the entire input?**

   **Answer.** Maintain a min-heap of at most K values. Fill it, then for each remaining value replace the minimum only when the new value is larger. This takes O(N log K) time and O(K) extra memory; the heap is not a sorted result, so sort its K elements if presentation order matters. For an in-memory mutable range, `nth_element` may be faster and use no additional container, but it reorders the input and does not naturally support streaming.

6. **[Deep dive] Why should associative-container lookup use the member `find` instead of `std::find`?**

   **Answer.** `map::find`, `set::find`, and their unordered counterparts use the data structure: logarithmic tree search or average constant-time hash lookup. `std::find` only scans through iterators and compares each element linearly; for a map it also compares `pair<const Key, Value>` values rather than accepting a key directly. Member lookup may support heterogeneous keys through transparent comparators/hashers, avoiding temporary key construction.

7. **[Deep dive] What is a transparent comparator or hasher?**

   **Answer.** A function object with a nested `is_transparent` marker and overloads capable of comparing/hashing the stored key and lookup type enables heterogeneous lookup. For example, a `map<string, V, std::less<>>` can often be searched with `string_view` or `const char*` without allocating a temporary `string`. The cross-type equality and hash relation must remain coherent: if two lookup values are equal under the predicate, their hash values must match.

8. **[Deep dive] What must an algorithm comparator guarantee?**

   **Answer.** Ordering algorithms generally require a strict weak ordering: irreflexive (`comp(x,x)` is false), asymmetric, transitive, and with transitive equivalence induced by `!comp(a,b) && !comp(b,a)`. A comparator such as `a <= b`, a stateful comparator that changes during the call, or a floating-point comparison that ignores NaN semantics can violate the contract. Once the precondition is violated, an algorithm may return nonsense, loop unexpectedly, or access memory incorrectly; the implementation need not validate the comparator.

9. **[Basic] What do ranges projections add to algorithms?**

   **Answer.** Many C++20 ranges algorithms accept a projection applied before comparison, so callers can sort or search records by a member without writing a bespoke comparator. For example, `std::ranges::sort(records, {}, &Record::id)` orders by `id`. Projections compose more cleanly with generic comparators and concepts, but projected values must still satisfy the algorithm's ordering/equality requirements and must not dangle.

## 8.2. Lock-free progress and an SPSC queue

1. **[Basic] Distinguish blocking, lock-free, wait-free, and obstruction-free progress.**

   **Answer.** A blocking algorithm can prevent all progress when a thread holding a required lock is suspended. Lock-free guarantees system-wide progress: in a finite number of steps, some operation completes, though one thread may starve. Wait-free guarantees every operation completes within a bounded number of its own steps. Obstruction-free guarantees progress only when a thread eventually runs without interference. These are progress properties, not claims of fairness, low latency, or higher throughput.

2. **[Design] What makes a single-producer/single-consumer (SPSC) ring buffer simpler than an MPMC queue?**

   **Answer.** Exactly one thread writes the producer index and exactly one writes the consumer index. Each side can therefore update its own index without compare-and-swap; it only reads the other side's published index. Fixed storage also avoids concurrent node allocation and reclamation. An MPMC queue needs arbitration among producers and consumers, more complex slot states or CAS loops, and usually a safe memory-reclamation strategy if it is node-based.

3. **[Code] What state and invariant does a bounded SPSC ring buffer need?**

   **Answer.** It needs storage, a write position, and a read position. One common design reserves one slot: empty means `read == write`, while full means `next(write) == read`, giving usable capacity N-1. Another uses monotonically increasing counters and computes slot indices modulo N, with a carefully chosen unsigned-distance rule. The API must explicitly define capacity, full/empty behavior, construction/destruction of `T`, and whether `try_push`/`try_pop` block or fail.

   ```text
   consumer owns read index                     producer owns write index
              |                                             |
              v                                             v
      +---+---+---+---+---+---+---+---+
      |   | A | B | C |   |   |   |   |   circular storage
      +---+---+---+---+---+---+---+---+
            occupied range; one slot may be reserved to distinguish full/empty
   ```

4. **[Deep dive] Which memory-order relationship publishes an SPSC element safely?**

   **Answer.** The producer constructs/writes the slot, then release-stores the new write index. The consumer acquire-loads that index before reading the slot; this makes the element writes visible. Symmetrically, after consuming/destroying a slot, the consumer release-stores the read index and the producer acquire-loads it before reusing that slot. Accesses to a thread's own index can often be relaxed, but every weakened ordering must follow from a written proof of the ownership and publication protocol.

5. **[Deep dive] How should wraparound be handled?**

   **Answer.** Slot selection may use `counter % capacity`, or a mask when capacity is a power of two. If counters are allowed to overflow, use unsigned arithmetic and ensure the algorithm never needs to distinguish distances outside the representable safe range. Resetting shared indices opportunistically is dangerous because both threads can observe inconsistent epochs. Tests should force many wraps with a tiny capacity and include full/empty boundary transitions.

6. **[Deep dive] Why should the two indices often be placed on separate cache lines?**

   **Answer.** Although producer and consumer write different atomic variables, placing them on the same cache line causes the line to bounce between cores as each write invalidates the other's cached copy—false sharing. Padding or `alignas(std::hardware_destructive_interference_size)` where supported can separate frequently written fields. Layout is implementation/hardware-sensitive, so confirm with counters and benchmarks; padding every field can waste cache and make performance worse.

7. **[Deep dive] Does a fixed-capacity SPSC ring buffer have the same reclamation problem as a lock-free linked queue?**

   **Answer.** It avoids freeing nodes while another thread may still hold their addresses, so hazard-pointer/epoch reclamation is usually unnecessary. It still must not overwrite a slot until the consumer has finished with and logically released it, and non-trivial `T` objects must be constructed and destroyed exactly once. Returning references that outlive the pop protocol can reintroduce a lifetime race even though storage itself remains allocated.

8. **[Design] When is a lock-free queue the wrong choice?**

   **Answer.** Prefer a mutex/condition-variable queue when blocking is desirable, contention is moderate, element operations can throw or block, dynamic capacity is needed, or the team cannot maintain a rigorous memory-order and lifetime proof. Lock-free spinning can waste CPU, amplify cache traffic, starve individual threads, and perform worse under oversubscription. Choose it only for measured latency/progress requirements, specify overload behavior, and test on all supported architectures—not only x86.

---

# Assessment usage notes

- For a short screening, choose 10–14 **[Basic]** questions across containers, iterators, algorithms, vocabulary types, and concurrency, plus 2–3 **[Code]** questions.
- For a 60–90 minute interview, start from a concrete workload and ask the candidate to choose a container, predict invalidation, apply algorithms, and explain thread/lifetime behavior.
- Complexity answers should include important preconditions and constant-factor/locality tradeoffs; “O(1)” alone is not a container-selection argument.
- Require explicit distinction among iterator, pointer, reference, and view invalidation.
- Treat modern feature knowledge as understanding of problems/contracts, not a checklist of names or compiler-support trivia.
