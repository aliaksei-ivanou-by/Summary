# Algorithms and Data Structures Assessment — Answer Guide

This handbook covers the algorithmic patterns most often expected in C++ interviews. Every question has a model answer. Strong answers should state the invariant, prove why the algorithm is correct, derive time and space complexity, and discuss boundary cases—not only reproduce code from memory.

Labels:

- **[Basic]** — expected foundations.
- **[Deep dive]** — proof, complexity, or subtle edge cases.
- **[Code]** — implementation or diagnosis in C++.
- **[Design]** — selecting a structure or adapting a pattern.

## Contents

1. [Problem solving and complexity](#1-problem-solving-and-complexity)
2. [Core data structures and representations](#2-core-data-structures-and-representations)
3. [Searching and range techniques](#3-searching-and-range-techniques)
   - [Binary search and invariants](#31-binary-search-and-invariants)
   - [Two pointers and sliding windows](#32-two-pointers-and-sliding-windows)
   - [Prefix sums and difference arrays](#33-prefix-sums-and-difference-arrays)
4. [Graph algorithms and disjoint sets](#4-graph-algorithms-and-disjoint-sets)
   - [BFS and DFS](#41-bfs-and-dfs)
   - [Dijkstra's shortest-path algorithm](#42-dijkstras-shortest-path-algorithm)
   - [Topological sorting](#43-topological-sorting)
   - [Disjoint Set Union](#44-disjoint-set-union)
5. [Cycle detection and priority problems](#5-cycle-detection-and-priority-problems)
   - [Floyd and Brent cycle detection](#51-floyd-and-brent-cycle-detection)
   - [Heaps and top-K](#52-heaps-and-top-k)
6. [Sequence and string patterns](#6-sequence-and-string-patterns)
   - [Kadane's maximum-subarray algorithm](#61-kadanes-maximum-subarray-algorithm)
   - [Knuth-Morris-Pratt string search](#62-knuth-morris-pratt-string-search)
   - [Merging intervals](#63-merging-intervals)
   - [Monotonic stacks and queues](#64-monotonic-stacks-and-queues)
7. [Integrated implementation exercises](#7-integrated-implementation-exercises)
   - [Cache replacement policies](#71-cache-replacement-policies)
   - [Geometry: triangle intersection](#72-geometry-triangle-intersection)
   - [Numerical methods: determinant](#73-numerical-methods-determinant)
   - [Language frontend and interpreter: ParaCL](#74-language-frontend-and-interpreter-paracl)

---

# 1. Problem solving and complexity

1. **[Basic] What do Big O, Big Omega, and Big Theta describe?**

   **Answer.** They describe asymptotic growth as input size tends toward infinity. Big O is an upper bound, Big Omega is a lower bound, and Big Theta is a tight bound when both apply. They ignore constant factors and lower-order terms, so `3N + 20` is Theta(N). State what N represents, what operation is counted, and whether the claim is worst-case, average/expected, or amortized; saying only “it is O(N)” is often incomplete and may be a loose bound.

2. **[Deep dive] How do worst-case, average-case, expected, and amortized complexity differ?**

   **Answer.** Worst-case bounds every valid input of a size. Average-case averages over an explicitly defined input distribution. Expected complexity averages over algorithmic randomness and/or a stated input model. Amortized analysis bounds the average cost per operation over every sequence, even without randomness—for example geometric `vector` growth makes append amortized constant time despite occasional linear reallocations. Do not call an expected hash-table result a guaranteed or amortized worst-case result.

3. **[Basic] Why should space complexity include more than the input container?**

   **Answer.** Auxiliary space includes recursion frames, queues/stacks, hash tables, temporary arrays, copied substrings, and hidden materialization. Whether the output itself counts should be stated. A recursive DFS uses O(depth) call-stack space even when it mutates a visited bitset in place; on a path-shaped graph, that becomes O(V) and can overflow the process stack. Memory traffic and peak live memory may also matter even when asymptotic space is equal.

4. **[Deep dive] What is an algorithm invariant?**

   **Answer.** An invariant is a property that holds before and after every iteration or recursive step. It connects local operations to global correctness. A proof normally shows initialization, preservation, and termination: the invariant holds initially, one step keeps it true, and when the loop ends it implies the postcondition. Examples include “all indices before `lo` are false” in lower-bound binary search and “the heap root is the smallest retained top-K candidate.”

5. **[Design] How do input constraints guide algorithm choice?**

   **Answer.** Translate constraints into an approximate operation and memory budget. A quadratic method may be fine for N=1,000 but impossible for N=10^6; a dense adjacency matrix may be appropriate for a small dense graph and disastrous for a sparse large one. Consider value ranges, sortedness, mutability, streaming versus random access, duplicates, negative weights, recursion depth, and whether preprocessing is amortized across queries. Then choose the simplest algorithm that satisfies the actual bound.

6. **[Code] Which edge cases should be tested for a typical container algorithm?**

   **Answer.** Test an empty input, one element, two elements in both orders, all equal values, already sorted and reverse sorted data, duplicates around the answer, answer at each boundary, no answer, maximum/minimum representable values, and inputs large enough to expose overflow or recursion depth. For randomized/property tests, compare with a slow reference on many small inputs and assert structural properties such as sortedness and multiset preservation.

7. **[Deep dive] Which integer-overflow mistakes commonly appear in algorithms?**

   **Answer.** `mid = (lo + hi) / 2` can overflow; use `lo + (hi - lo) / 2` or `std::midpoint`. Counts, path distances, products, and prefix sums often require a wider type than individual inputs. Converting a negative index to unsigned `size_t` creates a huge value, and loops such as `for (size_t i=n-1; i>=0; --i)` never terminate by the intended condition. Choose a representation that covers intermediate results and validate sentinel arithmetic before adding to it.

---

# 2. Core data structures and representations

1. **[Basic] How do a contiguous dynamic array and a linked list trade off?**

   **Answer.** A dynamic array provides constant-time random access, compact storage, few allocations, and excellent cache locality; insertion/erasure in the middle shifts elements and growth may relocate all elements. A linked list provides constant-time relinking at a known node and stable node addresses but no random access, one or more pointers per element, many allocations, and poor locality. Finding the insertion position is still linear. In practice `vector` is the default unless stable nodes or frequent known-position splicing is a demonstrated requirement.

2. **[Basic] What abstract behavior do stack, queue, and deque provide?**

   **Answer.** A stack is LIFO with push/pop at one end; a queue is FIFO with insertion at the back and removal at the front; a deque supports both ends. They express access discipline rather than one physical layout. Stacks support parsing, DFS, and undo; queues support BFS and work scheduling; deques support sliding-window algorithms. Restricting the interface makes invariants easier to see and prevents accidental access that breaks the algorithm.

3. **[Basic] How does a hash table work, and what are its essential contracts?**

   **Answer.** A hash maps a key to a bucket; collisions are resolved by chaining or an open-addressing scheme. Lookup compares only plausible entries in that bucket/probe sequence. Equal keys must have equal hashes, while unequal keys may collide; equality must be an equivalence relation, and keys must not change in a way that affects their hash/equality while stored. Operations are average/expected constant time under good distribution and load control, but worst-case linear.

4. **[Deep dive] When is a balanced search tree preferable to a hash table?**

   **Answer.** A balanced tree provides deterministic O(log N) search/update, sorted iteration, range queries, predecessor/successor operations, and no dependence on hash quality. A hash table typically has lower expected lookup cost but unordered iteration, rehash spikes, and potentially adversarial collisions. Tree nodes have allocation/cache overhead; flat sorted arrays can outperform both for mostly-read data. Choose by required operations and latency guarantees, not only a lookup complexity table.

5. **[Basic] What invariant defines a binary search tree, and why does its height matter?**

   **Answer.** Under a strict weak ordering, every key in a node's left subtree precedes the node and every key in its right subtree follows it, with a deliberate policy for equivalent keys. An inorder traversal is therefore sorted. Search, insertion, and deletion follow a root-to-leaf path and cost O(h), where h is the height: O(log N) for a balanced tree but O(N) when insertion order degenerates it into a chain. The search-tree invariant alone does not guarantee balance.

6. **[Deep dive] Why do tree rotations preserve search order, and what must an implementation update?**

   **Answer.** A left or right rotation changes a constant-size set of parent/child links while preserving the inorder sequence. In a right rotation, for example, the old root's left child becomes the new root and that child's right subtree moves between them; every key remains on the correct side of both nodes. Code must repair the parent link (including the overall root), transferred-child links, and all augmented metadata. Recompute metadata bottom-up—old root before new root—because the new root depends on the repaired child.

7. **[Deep dive] Compare the AVL and red-black balance invariants.**

   **Answer.** An AVL tree requires the heights of a node's two subtrees to differ by at most one, producing a tightly bounded height and usually fast lookups at the cost of maintaining heights and potentially more rebalancing on updates. A red-black tree colors nodes so the root and null leaves are black, red nodes have no red children, and every path from a node to a descendant null leaf has the same black height. This looser invariant still gives O(log N) height and often needs fewer rotations for updates. Standard ordered containers guarantee behavior and complexity, not either implementation.

8. **[Design] How can a balanced BST answer rank and k-th-smallest queries in O(log N)?**

   **Answer.** Store each node's subtree size: `size = 1 + size(left) + size(right)`. To find the k-th smallest key, compare k with the left-subtree size and descend left, return the current node, or subtract the skipped prefix and descend right. To count keys less than a value, descend as in search and accumulate the left-subtree size plus the current node whenever moving right. Insertions, deletions, and rotations must repair sizes on every affected path; with a valid balance invariant, update and query costs remain O(log N).

9. **[Deep dive] Why is `distance(set.lower_bound(lo), set.upper_bound(hi))` not a logarithmic-time range-count query?**

   **Answer.** Each bound lookup is O(log N), but ordinary `std::set` iterators are bidirectional rather than random-access, so `std::distance` advances through every element in the range. The total cost is O(log N + K), where K is the answer size. That is optimal when the elements must be visited, but not when only a count is needed. An order-statistics tree augmented with subtree sizes can compute two ranks and subtract them in O(log N); the standard `std::set` interface exposes no such rank operation.

10. **[Basic] What invariant defines a binary heap?**

   **Answer.** In a min-heap, every parent is no greater than its children, so the root is a minimum; a max-heap reverses the relation. A binary heap is normally stored in an array: for zero-based index `i`, children are `2*i+1` and `2*i+2`, and the parent is `(i-1)/2`. It is only partially ordered—searching for an arbitrary value remains linear. Push and pop repair a root-to-leaf path in O(log N).

11. **[Basic] What are adjacency lists and adjacency matrices?**

   **Answer.** An adjacency list stores each vertex's outgoing neighbors and uses O(V+E) space, making traversal O(V+E); it is the usual sparse-graph representation. An adjacency matrix uses O(V^2) space and gives constant-time edge-existence lookup, making it useful for dense/small graphs and some dynamic-programming or bitset techniques. For weighted graphs, edges also store weights. Directed graphs store only the stated direction; undirected graphs commonly insert both directions.

12. **[Deep dive] What implementation choices matter for graph node identity?**

   **Answer.** Dense integer IDs allow vectors for colors, distances, and adjacency, giving compact predictable access. Arbitrary external IDs can be compressed to dense indices through a map while preserving a reverse mapping. Raw pointers to nodes require lifetime/stability guarantees and complicate hashing/serialization. Separate identity from display data, and decide whether parallel edges and self-loops are valid because algorithms and tests may treat them differently.

13. **[Design] How do you choose between preprocessing and answering each query directly?**

   **Answer.** Compare preprocessing cost and memory with the number and type of queries. Sorting once for many binary searches costs O(N log N + Q log N), often better than Q linear scans. Prefix sums spend O(N) time and space to answer range sums in O(1), while a mutable workload may need a Fenwick/segment tree with logarithmic updates and queries. Include update frequency, latency distribution, cache behavior, and whether data fits in memory.

---

# 3. Searching and range techniques

## 3.1. Binary search and invariants

1. **[Basic] What precondition makes binary search possible?**

   **Answer.** The search space must have a monotonic predicate: once the answer changes from false to true (or the reverse), it never changes back. A sorted range is one instance—`value < target` is true for a prefix. The technique also searches an implicit numeric answer space such as “smallest capacity for which scheduling is feasible,” provided feasibility is monotonic and each predicate evaluation terminates.

2. **[Deep dive] Give a robust lower-bound invariant.**

   **Answer.** Use a half-open interval `[lo, hi)` and maintain that every index before `lo` is known to be too small while every index at or after `hi` is known to satisfy the predicate; the unknown region is `[lo, hi)`. Test `mid`, then set `lo = mid + 1` when false or `hi = mid` when true. The interval strictly shrinks, and at termination `lo == hi` is the first true position. A sentinel formulation can encode known-false and known-true boundaries instead.

   ```cpp
   template<class Pred>
   std::size_t first_true(std::size_t n, Pred pred) {
       std::size_t lo = 0;
       std::size_t hi = n;
       while (lo < hi) {
           const auto mid = lo + (hi - lo) / 2;
           if (pred(mid)) hi = mid;
           else lo = mid + 1;
       }
       return lo; // n means no true element
   }
   ```

3. **[Basic] How do `lower_bound` and `upper_bound` relate to duplicates?**

   **Answer.** `lower_bound` returns the first position where the value could be inserted without preceding an equivalent value—the first element not less than the target. `upper_bound` returns the first element greater than the target. Their half-open distance is the count of equivalent elements, and `equal_range` returns both. They require a range partitioned according to the same ordering used by the search.

4. **[Code] What are common binary-search bugs?**

   **Answer.** Mixing inclusive and half-open endpoints, failing to remove `mid` from the next interval, overflowing the midpoint, reading `a[n]` when “not found” returns n, and using a predicate that is not monotonic are typical. Duplicates expose algorithms that find an arbitrary match when the requirement is first/last. Write the invariant before the loop and use standard algorithms for ordinary sorted-range searches.

5. **[Deep dive] How do you binary-search a numeric answer without infinite loops?**

   **Answer.** For integers, choose a closed or half-open invariant and guarantee that each update removes at least one value; use overflow-safe midpoint arithmetic. For floating point, exact equality and “until lo equals hi” are unsuitable. Stop after a fixed number of iterations or when interval/error tolerance is met, while accounting for scale and predicate stability. Return a bound with clearly stated direction and error, not a falsely exact value.

## 3.2. Two pointers and sliding windows

1. **[Basic] What is the two-pointer technique?**

   **Answer.** It maintains two indices that move monotonically through a sequence instead of restarting a nested scan. Pointers may move from opposite ends (pair sum in a sorted array) or in the same direction (compaction, merging sorted ranges). The proof must explain why moving one pointer cannot discard a possible solution. Because each pointer advances at most N times, many such algorithms are O(N).

2. **[Code] How does the sorted two-sum algorithm work?**

   **Answer.** Start at the smallest and largest elements. If their sum is too small, increment the left pointer because pairing that smallest value with any remaining value cannot produce a larger result than the current largest pairing; if too large, decrement the right pointer. Use a wide type for the sum. The method is O(N) after sorting, but sorting costs O(N log N) and may destroy original indices, so retain index/value pairs when indices are required.

3. **[Basic] What distinguishes a sliding window from generic two pointers?**

   **Answer.** A sliding window represents a contiguous range and maintains an aggregate as the right boundary expands and the left boundary contracts. It works when window validity changes monotonically enough that a discarded left boundary never needs to return. Examples include longest substring with no repeated symbol and minimum-length subarray with a sum threshold when elements are non-negative. Negative values can destroy sum monotonicity and require prefix-sum or deque techniques instead.

4. **[Deep dive] Why is a nested `while` sliding-window algorithm still often linear?**

   **Answer.** Although the inner loop may run many times in one outer iteration, the left boundary only moves forward and advances at most N times over the whole algorithm; the right boundary also advances N times. Aggregate work is therefore O(N), an amortized argument. If the implementation erases from the front of a vector or recomputes the whole window on every move, its actual complexity can still be quadratic.

5. **[Design] How do you recognize that a sliding window does not fit?**

   **Answer.** If adding an element can make an invalid window valid again without removing from the left, or if removing the left item can unpredictably change validity, a simple monotonic window is suspect. Requirements involving arbitrary negative sums, non-contiguous choices, or future-dependent constraints often need prefix sums plus a map/deque, dynamic programming, or a different ordering. Test the claimed invariant on a minimal counterexample before coding.

## 3.3. Prefix sums and difference arrays

1. **[Basic] How does a one-dimensional prefix sum answer range-sum queries?**

   **Answer.** Build `prefix` of size N+1 with `prefix[0]=0` and `prefix[i+1]=prefix[i]+a[i]`. Then the sum of half-open range `[l,r)` is `prefix[r]-prefix[l]`. The extra leading zero eliminates a special case for `l=0`. Building is O(N), each query is O(1), and sums should use a sufficiently wide type.

2. **[Deep dive] What can prefix techniques compute besides sums?**

   **Answer.** Any operation with an appropriate inverse can often answer ranges from two prefixes: counts, XOR, and multidimensional rectangular sums are common. Prefix minima cannot be subtracted to recover an arbitrary range minimum, so they only answer prefix queries; range-minimum data structures are different. Frequency prefix arrays trade memory for constant-time counts over bounded categories.

3. **[Basic] What is a difference array?**

   **Answer.** It represents changes between adjacent values. To add `x` to every element of `[l,r)`, add `x` at `diff[l]` and subtract it at `diff[r]`; one final prefix sum materializes all updates. This makes Q offline range additions O(N+Q) instead of O(NQ). It does not provide immediate arbitrary online values unless combined with a Fenwick/segment tree or periodically materialized.

4. **[Code] What boundary mistakes occur with prefix and difference arrays?**

   **Answer.** Mixing inclusive `[l,r]` with half-open `[l,r)`, allocating only N difference entries and then writing `diff[r]` when `r==N`, and using a narrow accumulator are common. Define one interval convention for the API and encode it in variable names/tests. Test empty ranges, the full range, and updates/queries touching both ends.

---

# 4. Graph algorithms and disjoint sets

## 4.1. BFS and DFS

1. **[Basic] How do BFS and DFS differ?**

   **Answer.** Breadth-first search uses a FIFO queue and explores vertices in nondecreasing number of edges from the source. Depth-first search uses recursion or an explicit LIFO stack and explores one path before backtracking. Both visit a graph in O(V+E) with adjacency lists. Their traversal trees and applications differ: BFS gives shortest unweighted path lengths; DFS naturally exposes nesting, reachability structure, and finish order.

2. **[Basic] Why must a graph traversal record visited vertices?**

   **Answer.** Graphs can contain cycles and multiple paths to the same vertex. Without visited state, traversal can loop forever or repeat exponential work. In BFS, mark a vertex when enqueuing, not when dequeuing; otherwise several parents can enqueue it and increase memory/work. DFS cycle detection may need more than a boolean: white/gray/black states distinguish an active recursion path from a completed vertex.

3. **[Code] How does BFS reconstruct a shortest path?**

   **Answer.** When first discovering vertex `v` from `u`, store `parent[v]=u` and `distance[v]=distance[u]+1`. Because BFS processes layers in order, first discovery is a shortest number-of-edges path. If the target is reached, follow parent links back to the source and reverse them. For multiple sources, enqueue all sources initially with distance zero; the same proof yields distance to the nearest source.

4. **[Deep dive] Recursive or iterative DFS—which should be chosen?**

   **Answer.** Recursive DFS is concise and mirrors the proof, but depth can reach V and overflow a bounded call stack. Iterative DFS moves frames to a heap-backed container and can control memory, but preserving exact recursive enter/exit behavior may require storing the next neighbor index in each frame. Choose based on maximum depth and whether exit-time processing is needed; do not assume a balanced tree when input can be a path.

5. **[Design] Which problems commonly reduce to BFS or DFS?**

   **Answer.** Connected components, reachability, flood fill, bipartite testing, maze traversal, and tree processing are common. Use BFS for minimum edge count or layer-by-layer propagation; use DFS for topological ordering, back-edge cycle detection, subtree aggregation, and exhaustive backtracking. Weighted shortest paths require algorithms matched to weights: 0–1 BFS for weights 0/1, Dijkstra for non-negative weights, and other algorithms for negative edges.

## 4.2. Dijkstra's shortest-path algorithm

1. **[Basic] What problem does Dijkstra's algorithm solve?**

   **Answer.** It computes shortest-path distances from one source in a graph whose edge weights are non-negative. It repeatedly finalizes the unsettled vertex with minimum tentative distance and relaxes its outgoing edges. With adjacency lists and a binary heap, complexity is O((V+E) log V), commonly written O(E log V) for connected sparse graphs. It can store predecessors to reconstruct paths.

2. **[Deep dive] Why are non-negative weights required?**

   **Answer.** The proof says that once the smallest tentative vertex `u` is extracted, any alternative path reaching `u` through an unsettled vertex cannot be shorter because it would add a non-negative edge to a distance no smaller than `dist[u]`. A negative edge breaks that argument: a later path can reduce a finalized distance. Use Bellman-Ford or a problem-specific transformation when negative edges are possible; a negative cycle means no finite shortest path for reachable affected vertices.

3. **[Code] Why do common C++ implementations push duplicate heap entries?**

   **Answer.** `std::priority_queue` has no efficient decrease-key handle. On improvement, push a new `(distance, vertex)` pair; when popping, discard it if its distance differs from the current `dist[vertex]`. Each successful relaxation adds an entry, keeping complexity O(E log E), equivalent to O(E log V) up to graph-size relations for ordinary analysis. Marking a vertex visited at first insertion would be wrong because a shorter route may be found before extraction.

   ```cpp
   using Item = std::pair<long long, int>;
   std::priority_queue<Item, std::vector<Item>, std::greater<>> pq;
   pq.push({0, source});
   while (!pq.empty()) {
       auto [d, u] = pq.top();
       pq.pop();
       if (d != dist[u]) continue;
       for (auto [v, w] : graph[u]) {
           if (d <= INF - w && d + w < dist[v]) {
               dist[v] = d + w;
               parent[v] = u;
               pq.push({dist[v], v});
           }
       }
   }
   ```

4. **[Deep dive] How should infinity and overflow be handled?**

   **Answer.** Choose a distance type capable of the maximum simple-path sum and an `INF` sentinel above every valid answer. Never blindly compute `INF + weight`; guard the sentinel or use a checked comparison such as `d <= INF - w`. Signed overflow is UB, while unsigned wrap silently corrupts ordering. Also validate that weights satisfy the non-negative precondition before converting signed input to unsigned.

5. **[Design] When is another shortest-path algorithm a better fit?**

   **Answer.** Use BFS for equal weights, 0–1 BFS for weights only zero and one, a DAG dynamic program after topological sort for weighted acyclic graphs (even with negative edges), and Bellman-Ford when negative edges and cycle detection are required. A* can reduce explored states for a single target with an admissible/consistent heuristic. All-pairs workloads may favor repeated Dijkstra, Floyd-Warshall, or Johnson's algorithm depending on density, weights, and V.

## 4.3. Topological sorting

1. **[Basic] What is a topological order, and when does one exist?**

   **Answer.** It is a linear ordering of a directed graph in which every edge `u -> v` places `u` before `v`. Such an order exists exactly for directed acyclic graphs (DAGs). It models prerequisite constraints in builds, courses, migrations, and task scheduling. The order need not be unique; uniqueness requires exactly one valid choice at each step under an appropriate algorithmic test.

2. **[Basic] How does Kahn's algorithm work?**

   **Answer.** Compute every vertex's indegree, enqueue all zero-indegree vertices, repeatedly remove one, append it to the result, and decrement the indegree of its outgoing neighbors, enqueuing newly zero values. Every processed edge removes one unsatisfied prerequisite. Complexity is O(V+E). A FIFO queue yields one valid order; a min-heap yields the lexicographically smallest available order at extra logarithmic cost.

3. **[Deep dive] How does topological sorting detect a cycle?**

   **Answer.** In Kahn's algorithm, a directed cycle leaves every vertex on the cycle with at least one incoming edge from the unprocessed set, so none becomes eligible. If fewer than V vertices are emitted, a cycle exists. In DFS, an edge to a gray/active vertex is a back edge and proves a cycle; reverse exit order is topological only if no such edge is found.

4. **[Design] What does a topological order not solve in scheduling?**

   **Answer.** It respects precedence but does not optimize duration, resource capacity, fairness, deadlines, or assignment to workers. It also does not explain which cycle to break unless the algorithm records enough evidence to reconstruct one. Real schedulers combine dependency order with critical-path analysis, resource constraints, priorities, and failure/retry policies.

## 4.4. Disjoint Set Union

1. **[Basic] What operations does Disjoint Set Union provide?**

   **Answer.** DSU, or Union-Find, maintains a partition of elements into disjoint components. `find(x)` returns a representative for x's component; `union(a,b)` merges two components and reports whether they were distinct. It answers dynamic connectivity as edges are added, and supports Kruskal's minimum-spanning-tree algorithm and grouping/equivalence problems.

2. **[Basic] How is DSU represented?**

   **Answer.** Each element stores a parent index, forming a forest; a root is its own parent and represents the set. `find` follows parents to a root. `union` makes one root the child of the other. Component size/rank may be stored at roots, and the number of components decreases only when two different roots merge.

3. **[Deep dive] What do path compression and union by size/rank accomplish?**

   **Answer.** Path compression rewrites nodes encountered by `find` to point closer or directly to the root. Union by size/rank attaches the shallower/smaller tree under the larger one. Together, a sequence of M operations on N elements takes O(M alpha(N)) time, where the inverse Ackermann function is effectively constant for practical sizes. Either heuristic alone is useful but has a weaker bound.

4. **[Code] Show a compact DSU implementation and its invariant.**

   **Answer.** Roots have `parent[x] == x`; only roots carry authoritative sizes. `unite` first finds both roots, so linking cannot create a cycle between nodes already in one component.

   ```cpp
   class Dsu {
       std::vector<int> parent_;
       std::vector<int> size_;
   public:
       explicit Dsu(int n) : parent_(n), size_(n, 1) {
           std::iota(parent_.begin(), parent_.end(), 0);
       }
       int find(int x) {
           if (parent_[x] != x) parent_[x] = find(parent_[x]);
           return parent_[x];
       }
       bool unite(int a, int b) {
           a = find(a); b = find(b);
           if (a == b) return false;
           if (size_[a] < size_[b]) std::swap(a, b);
           parent_[b] = a;
           size_[a] += size_[b];
           return true;
       }
   };
   ```

5. **[Deep dive] What can ordinary DSU not do efficiently?**

   **Answer.** It handles merges but not arbitrary edge deletion or splitting a component. It also does not retain the actual path between two vertices. Offline algorithms can process deletions in reverse as additions; rollback DSU can undo unions when path compression is avoided/managed; fully dynamic connectivity needs more advanced structures. Recognizing this monotonicity limitation prevents forcing DSU onto an unsuitable online problem.

---

# 5. Cycle detection and priority problems

## 5.1. Floyd and Brent cycle detection

1. **[Basic] What kind of cycle does Floyd's tortoise-and-hare algorithm detect?**

   **Answer.** It detects a cycle in a deterministic successor sequence `x, f(x), f(f(x)), ...` using O(1) auxiliary space. This covers linked lists and iterated functions where each state has at most one successor. It is not a general directed-graph cycle detector because a graph vertex may have several outgoing edges and traversal choices.

2. **[Deep dive] Why do the slow and fast pointers meet if a cycle exists?**

   **Answer.** After the slow pointer enters a cycle of length lambda, consider the relative position of fast versus slow modulo lambda. Each iteration advances that relative position by one because fast moves two steps and slow one. Within at most lambda iterations the difference becomes zero, so they meet. If the sequence reaches a null/end state, no cycle exists.

3. **[Code] How are the cycle entry and length found after a meeting?**

   **Answer.** To get the entry, place one pointer at the start and leave the other at the meeting point, then move both one step; their next meeting is the first cycle node. To get length lambda, hold one pointer at a cycle node and count steps until it returns. The pre-cycle length mu can be counted while locating the entry. The entry proof follows congruence between the distance traveled before meeting and multiples of lambda.

4. **[Deep dive] How does Brent's algorithm differ from Floyd's?**

   **Answer.** Brent keeps one stationary reference for a power-of-two block while another advances, doubling the block length whenever no match is found. It still uses O(1) space and O(mu+lambda) successor evaluations, but often calls an expensive `f` fewer times than Floyd, which evaluates it three times per loop iteration. It also obtains the cycle length naturally; a second phase finds the entry. Floyd is usually easier to recall and review.

5. **[Design] When should a visited set be preferred over O(1)-space cycle detection?**

   **Answer.** A visited map/set is simpler when memory is available and can report the first repeated state and its original index directly. It also works with richer traversal histories and general graphs when paired with appropriate state colors. Floyd/Brent are valuable for huge or implicit functional graphs where states can be compared and recomputed but retaining all of them is too expensive. If equality is costly or non-deterministic, their assumptions may fail.

## 5.2. Heaps and top-K

1. **[Basic] Why is a heap a good structure for repeated priority selection?**

   **Answer.** It keeps the best-priority element at the root in O(1) access and restores the invariant after insertion/removal in O(log N). Building a heap from N values with bottom-up heapify is O(N), not O(N log N). Unlike a balanced tree, it does not support ordered iteration or efficient arbitrary search, but it uses compact array storage and exactly matches “repeatedly take the next best” workloads.

2. **[Code] Which heap orientation is used for top K largest values?**

   **Answer.** Keep a min-heap of the K largest values seen. Its root is the weakest retained candidate: discard a new value no larger than the root, otherwise replace the root. A max-heap would expose the strongest candidate and would not tell which retained element should be evicted. For K smallest, use the symmetric max-heap.

3. **[Deep dive] How do heap top-K, sorting, and `nth_element` compare?**

   **Answer.** Full sorting is O(N log N) and gives total order. A size-K heap is O(N log K), uses O(K) memory, and works on a stream. `nth_element` has expected linear time and partitions an in-memory mutable range but neither sorts the selected portion nor naturally handles streaming. Choose based on K/N, whether input can be reordered, output ordering, memory, and worst-case/latency requirements.

4. **[Design] How are ties and deterministic output handled?**

   **Answer.** Define a total tie-break rule—such as score descending then ID ascending—and use it consistently in heap retention and final ordering. If only an equivalence-class result is required, any K tied elements may be valid, but tests should not assume a specific choice. Stable arrival order requires storing a sequence number because a heap is not stable by default.

---

# 6. Sequence and string patterns

## 6.1. Kadane's maximum-subarray algorithm

1. **[Basic] What problem does Kadane's algorithm solve?**

   **Answer.** It finds a contiguous non-empty subarray with maximum sum in O(N) time and O(1) auxiliary space. At each position it tracks the best sum of a subarray that must end there: either start at the current element or extend the previous ending subarray. A global best records the strongest ending value seen. The recurrence is `ending = max(a[i], ending + a[i])`.

2. **[Deep dive] Why is discarding a negative prefix safe?**

   **Answer.** If the best subarray ending before the current element has a negative sum, adding it makes any subarray starting at the current element strictly worse. Therefore no optimal subarray that begins at the current or a later position needs that prefix. This local dominance property is the proof behind the greedy-looking recurrence.

3. **[Code] How do you return the subarray indices as well as its sum?**

   **Answer.** Track a candidate start whenever the recurrence chooses the current element alone. When the current ending sum improves the global best, save candidate start and current index. Initialize from the first element—not zero—when the result must be non-empty; otherwise an all-negative array would incorrectly return an empty sum of zero.

   ```cpp
   auto best = a[0];
   auto ending = a[0];
   std::size_t candidate = 0, best_l = 0, best_r = 1;
   for (std::size_t i = 1; i < a.size(); ++i) {
       if (ending + a[i] < a[i]) { ending = a[i]; candidate = i; }
       else { ending += a[i]; }
       if (best < ending) { best = ending; best_l = candidate; best_r = i + 1; }
   }
   ```

4. **[Deep dive] Which variants require a different algorithm or state?**

   **Answer.** Circular maximum subarray combines ordinary maximum with total sum minus a minimum subarray, with special handling for all-negative input. Maximum product needs both maximum and minimum ending products because a negative value swaps their roles. Maximum submatrix typically fixes row pairs and applies Kadane to compressed columns, increasing complexity. Length bounds may require prefix sums with a deque or other range structure.

## 6.2. Knuth-Morris-Pratt string search

1. **[Basic] What problem does KMP solve, and what is its complexity?**

   **Answer.** KMP finds occurrences of a pattern of length M in text of length N in O(N+M) time and O(M) preprocessing space. When a mismatch occurs, it uses information about the pattern's own borders to avoid rechecking text characters already known to match. A straightforward search is often adequate in practice, but KMP provides a deterministic linear guarantee and is useful in streaming-style matching.

2. **[Basic] What is the prefix-function/LPS table?**

   **Answer.** For each pattern prefix ending at position `i`, it stores the length of the longest proper prefix that is also a suffix. “Proper” excludes the whole prefix. If a match of length `j` fails, the table says how much of that matched suffix is also a valid pattern prefix, so matching can resume there without moving the text index backward.

3. **[Deep dive] Why is KMP linear despite fallback loops?**

   **Answer.** During table construction and search, the matched-prefix length increases only when characters match and decreases along previously computed borders on mismatch. Across the entire run it cannot increase or decrease without bound; the text index never retreats. A potential/amortized argument bounds total fallback steps by the total forward progress, yielding O(N+M).

4. **[Code] What edge cases must a string-search API define?**

   **Answer.** Define whether an empty pattern matches at position zero, every boundary, or is rejected; standard `find`-like semantics usually return zero. Decide whether overlapping matches are reported—after a full match, falling back through the LPS table permits overlaps. Byte-based matching is not Unicode grapheme/code-point matching; UTF-8 text may require normalization and a clear unit of indexing. Avoid dangling `string_view`s when inputs are non-owning.

5. **[Design] When is KMP not the best string-search choice?**

   **Answer.** Library search implementations may use highly optimized vectorized or hybrid algorithms for ordinary in-memory byte strings. Boyer-Moore-family algorithms can skip ahead on suitable alphabets/patterns; rolling hashes support multiple comparisons with collision handling; Aho-Corasick searches many patterns simultaneously. Choose using pattern count, alphabet, streaming needs, adversarial guarantees, preprocessing reuse, and actual measurements.

## 6.3. Merging intervals

1. **[Basic] How are overlapping intervals merged?**

   **Answer.** Sort intervals by start (and usually by end as a tie-break), then scan. If the next interval overlaps the current merged interval, extend the current end to their maximum; otherwise emit the current interval and start a new one. Sorting dominates at O(N log N), and the scan is O(N). If input is already sorted, the operation is linear.

2. **[Deep dive] How does interval convention affect overlap?**

   **Answer.** For closed intervals `[a,b]`, `[1,2]` and `[2,3]` intersect at 2 and normally merge. For half-open intervals `[a,b)`, `[1,2)` and `[2,3)` are adjacent but do not overlap, though a domain may intentionally coalesce adjacency. Empty intervals are natural in half-open form. State the convention and whether adjacency counts before writing the comparison.

3. **[Code] What input validation is appropriate?**

   **Answer.** Decide whether `start > end` is invalid, normalized by swapping, or meaningful in the domain; silently changing it can hide upstream bugs. Consider overflow if converting inclusive integer intervals into half-open form by adding one to the end. Preserve attached metadata deliberately: merging timestamps may require combining IDs, provenance, or payload rather than keeping an arbitrary record.

4. **[Design] How do dynamic interval queries differ from one-time merging?**

   **Answer.** Re-sorting and scanning is ideal for an offline batch. Frequent insertions plus overlap queries may require an interval tree, ordered map of disjoint intervals, segment tree, sweep-line event structure, or domain-specific index. The right choice depends on coordinate range, updates, queries (point, overlap, coverage), and whether intervals can be deleted.

## 6.4. Monotonic stacks and queues

1. **[Basic] What is a monotonic stack?**

   **Answer.** It stores candidates whose values are kept monotonically increasing or decreasing. Before pushing a new item, dominated items are popped because they can no longer be the nearest/best answer for future positions. It solves next-greater/smaller element, stock span, histogram rectangle, and boundary problems. Store indices when distance, expiry, or access to original values is needed.

2. **[Deep dive] Why are monotonic-stack algorithms usually O(N)?**

   **Answer.** One iteration can pop many elements, but each input index is pushed once and popped at most once. Thus total stack operations are O(N), even with an inner `while`. This is aggregate/amortized analysis similar to a sliding window; it should be stated explicitly when defending the complexity.

3. **[Basic] What is a monotonic queue/deque used for?**

   **Answer.** It maintains the maximum or minimum over a moving window. For a maximum, values/indices decrease from front to back: remove expired indices from the front, remove values no greater than the incoming value from the back, then append the new index. The front is the window maximum. Each index enters and leaves once, giving O(N) time and O(K) space for window size K.

4. **[Deep dive] How do equality rules affect a monotonic structure?**

   **Answer.** Popping `<` versus `<=` decides whether equal earlier candidates are retained. Either can be correct depending on whether the problem needs the nearest index, counts duplicates, uses strict versus non-strict boundaries, or prefers older values for expiry. Histogram algorithms are especially sensitive: choose one side strict and the other non-strict consistently to avoid double-counting equal heights.

5. **[Code] What are common implementation mistakes?**

   **Answer.** Storing values when indices are required for expiry, expiring after reading the answer, using the wrong comparison direction, and mishandling empty structures are common. For circular next-greater problems, iterate a virtual range of length 2N but normally push each original index only during the first pass. Test all-equal, strictly increasing/decreasing, K=1, K=N, duplicates at a boundary, and invalid window sizes.

---

# 7. Integrated implementation exercises

## 7.1. Cache replacement policies

1. **[Design] How would you implement and evaluate ARC, 2Q, LFU, or LIRS for a fixed-capacity request trace?**

   **Answer.** Define the observable contract first: the capacity and request stream are inputs, a request already resident is a hit, and a miss may admit the key and evict another. Specify capacity zero, repeated keys, counter overflow/aging, and whether entries have equal size. Aim for O(1) average processing with a hash table plus stable list iterators or intrusive nodes. LFU also needs frequency buckets with an LRU tie-break; 2Q separates recent probationary entries from a main queue; ARC adapts the balance between recent and frequent lists using bounded ghost histories; LIRS tracks reuse distance through resident/nonresident stack metadata. Each policy needs explicit bounds for resident data and history metadata.

   Compare hit counts on the same traces against simple LRU/FIFO and the offline-optimal Belady/MIN policy, which evicts the item whose next use lies farthest in the future. MIN requires future knowledge, so it is an evaluation upper bound rather than an online production policy. Precompute next-use positions by scanning backward, or maintain suitable future-position queues, then verify that an online policy never exceeds the oracle on the same model. Test scans, loops slightly larger than capacity, phase changes, hot/cold mixtures, all-unique input, capacity one/zero, and randomized traces checked against a slow reference. Assert list/map membership, uniqueness, size, and ghost-history invariants after every operation.

## 7.2. Geometry: triangle intersection

1. **[Code] How can the area of intersection of two planar triangles be computed robustly?**

   **Answer.** Treat each triangle as a convex polygon. Normalize vertex orientation, then clip one triangle successively against the three half-planes of the other with a convex-polygon clipping algorithm such as Sutherland-Hodgman. The result has at most six vertices; compute its area with the shoelace formula and return zero for an empty or lower-dimensional intersection. Segment-line intersections and inside tests should share one orientation convention.

   Robustness is the hard part. Define behavior for degenerate triangles, touching edges/vertices, collinear overlaps, large coordinates, and output tolerance. Exact integer/rational predicates can classify orientation reliably when the input domain permits them; floating-point code should use scale-aware error handling rather than one arbitrary epsilon for every magnitude. Test identical and disjoint triangles, containment, partial overlap, edge/vertex contact, reversed winding, degeneracy, symmetry `area(A,B) == area(B,A)`, and invariance under translation or vertex rotation.

2. **[Design] How would you find every triangle in a very large 3D set that intersects at least one other triangle?**

   **Answer.** Testing every pair is O(N^2) and is not viable for input approaching a million triangles. Use a broad phase that creates conservative candidates from axis-aligned bounding boxes, for example a sweep-and-prune structure, BVH/AABB tree, spatial grid, or another spatial index chosen for the coordinate distribution. Run an exact or robust triangle-triangle narrow phase only for candidate pairs, mark both indices on a confirmed intersection, and avoid duplicate work. Worst-case output/candidate complexity remains quadratic when many boxes or triangles overlap, so state that bound even if typical data is much better.

   The narrow phase must define coplanar overlap, shared vertices/edges, zero-area triangles, numeric tolerance, and whether contact counts as intersection. Validate the broad phase against brute force on many small randomized inputs, then add adversarial cases such as coplanar clusters, long thin triangles, identical boxes with disjoint geometry, huge/small coordinates, and dense all-intersecting sets. Measure candidate count, memory, and end-to-end time rather than only the predicate.

## 7.3. Numerical methods: determinant

1. **[Code] How should a matrix determinant be computed, and how do numeric and exact domains change the algorithm?**

   **Answer.** Do not use recursive Laplace expansion except for tiny teaching cases; it has factorial/exponential-scale work. For floating-point matrices, perform Gaussian elimination or an LU decomposition with partial pivoting in O(N^3) time and O(N^2) storage (or in place). Each row swap flips the sign, a zero pivot makes the determinant zero, and the determinant is the signed product of the resulting diagonal pivots. Pivoting and scaling reduce instability, but a tolerance must be relative to the matrix scale and application rather than a universal constant.

   Integer input needs a declared result domain. Ordinary division can truncate and intermediate products can overflow even when the final determinant fits. Use a fraction-free method such as Bareiss with a sufficiently wide/exact integer type, rational arithmetic, or modular determinants plus reconstruction when bounds permit. Test empty/1x1 conventions as required, triangular matrices, a row swap, duplicate/dependent rows, singular and near-singular floating matrices, identity/permutation matrices, and randomized small cases against an exact or trusted reference.

## 7.4. Language frontend and interpreter: ParaCL

1. **[Design] How would you structure and test a ParaCL frontend and simulator supporting arithmetic, variables, input/output, `if`, and `while`?**

   **Answer.** Split the system into a lexer, parser, typed or explicitly tagged abstract syntax tree, diagnostics, and an evaluator over a variable environment plus abstract input/output streams. Define grammar precedence/associativity and error recovery separately from execution. AST ownership should be explicit, usually through values and `std::unique_ptr`; an open virtual node hierarchy supports adding node types without rewriting a central variant, while `std::variant` plus visitors gives a closed exhaustive node set. Avoid raw `void*` payloads or an unprotected union with a manually synchronized type tag.

   Evaluation must specify undefined variables, redeclaration, integer overflow/division by zero, condition truth rules, input exhaustion, and nontermination/resource limits. Keep source ranges on tokens/nodes so syntax and runtime diagnostics identify the failing construct. Test lexer and parser components independently, round-trip or snapshot ASTs where useful, and execute small programs covering precedence, nested blocks, both `if` branches, zero/many loop iterations, input/output, and failures. Differential/property tests can compare expression evaluation with a trusted reference; fuzz malformed programs and bound execution so an infinite loop cannot hang the test suite.

---

# Assessment usage notes

- Ask the candidate to state the invariant before coding and to derive complexity from how often each state changes.
- For a 60–90 minute interview, combine one implementation task with follow-ups that change constraints—for example add negative values, streaming input, or concurrent updates.
- Evaluate boundary conventions, overflow, ownership, iterator validity, and input validation as part of algorithmic correctness.
- Prefer a correct simple solution followed by a justified optimization over a memorized advanced algorithm with an unstated precondition.
- Use property-based tests or a slow reference implementation for small randomized inputs when validating interview exercises.
