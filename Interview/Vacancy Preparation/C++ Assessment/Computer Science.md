# Computer Science Assessment — Answer Guide

This handbook covers computer architecture, operating systems, concurrency, networking, binary tooling, and performance topics relevant to C++ engineers. Every question is followed by a model answer. Platform-specific answers use Linux/POSIX as the reference where appropriate and explicitly separate portable concepts from implementation details.

Labels:

- **[Basic]** — expected core knowledge.
- **[Deep dive]** — mechanisms, tradeoffs, and edge cases.
- **[Code]** — code or command analysis.
- **[Design]** — an open-ended engineering discussion.

## Contents

1. [CPU and machine architecture](#1-cpu-and-machine-architecture)
   - [RISC versus CISC](#11-risc-versus-cisc)
   - [Endianness](#12-endianness)
   - [Out-of-order execution and branch prediction](#13-out-of-order-execution-and-branch-prediction)
   - [Memory alignment and padding](#14-memory-alignment-and-padding)
2. [Memory systems](#2-memory-systems)
   - [Memory hierarchy](#21-memory-hierarchy-l1l2l3-ram-and-storage)
   - [Cache lines, misses, and false sharing](#22-cache-lines-cache-misses-and-false-sharing)
   - [Virtual memory, pages, page faults, and the TLB](#23-virtual-memory-pages-page-faults-and-the-tlb)
   - [LRU caching](#24-lru-as-a-caching-pattern)
   - [Memory-mapped I/O](#25-memory-mapped-io-mmap)
3. [Procedure execution and operating-system boundaries](#3-procedure-execution-and-operating-system-boundaries)
   - [Call stack, stack frames, and procedure calls](#31-call-stack-stack-frames-and-procedure-calls)
   - [Operating-system role and system calls](#32-operating-system-role-and-system-calls)
   - [Interrupts, processor exceptions, and OS signals](#33-interrupts-processor-exceptions-and-os-signals)
4. [Processes and IPC](#4-processes-and-ipc)
   - [Processes on Linux](#41-process-creation-states-termination-waiting-and-signals-on-linux)
   - [Interprocess communication](#42-interprocess-communication-pipes-shared-memory-and-message-queues)
5. [Threads and concurrency](#5-threads-and-concurrency)
   - [Thread model, POSIX threads, and context switches](#51-thread-model-posix-threads-and-context-switches)
   - [Race conditions, critical sections, mutexes, and semaphores](#52-race-conditions-critical-sections-mutexes-and-semaphores)
   - [Deadlock, livelock, and starvation](#53-deadlock-livelock-and-starvation)
   - [Dining philosophers and readers-writers](#54-dining-philosophers-and-readers-writers)
   - [Thread scheduling and lock ordering](#55-thread-scheduling-and-lock-ordering)
6. [Files, memory safety, and cryptography](#6-files-memory-safety-and-cryptography)
   - [Files, directories, and hard/symbolic links](#61-files-directories-and-hardsymbolic-links)
   - [Buffer overflow](#62-buffer-overflow)
   - [Symmetric versus asymmetric cryptography](#63-symmetric-versus-asymmetric-cryptography)
7. [Networking](#7-networking)
   - [OSI and TCP/IP models](#71-osi-and-tcpip-models)
   - [IPv4, subnets, CIDR, and NAT](#72-ipv4-addresses-subnets-masks-cidr-and-nat)
   - [Berkeley sockets](#73-berkeley-sockets)
   - [TCP](#74-tcp-connection-establishment-sliding-windows-and-byte-streams)
   - [UDP](#75-udp-datagrams-versus-tcp)
   - [DNS](#76-dns-resolution-nslookup-and-dig)
   - [HTTP, HTTPS, and TLS](#77-http-https-versions-and-tls)
   - [WebSocket](#78-websocket)
   - [`select`, `poll`, and `epoll`](#79-io-multiplexing-select-poll-and-epoll)
   - [`tcpdump` and Wireshark](#710-basic-analysis-with-tcpdump-and-wireshark)
8. [Toolchain, binaries, debugging, and profiling](#8-toolchain-binaries-debugging-and-profiling)
   - [Compilation, linking, and libraries](#81-compilation-linking-and-staticdynamic-libraries)
   - [ELF basics](#82-elf-basics)
   - [`gdb`](#83-gdb-backtraces-breakpoints-and-watchpoints)
   - [Profiling with `perf` and Valgrind](#84-profiling-with-perf-valgrind-and-related-tools)
9. [Performance engineering](#9-performance-engineering)
   - [Latency, throughput, and percentiles](#91-latency-throughput-and-percentiles)
   - [Batching, coalescing, throttling, and backpressure](#92-batching-coalescing-throttling-and-backpressure)
   - [Allocation cost and memory behavior](#93-allocation-cost-and-memory-behavior)
   - [Diagnosing long-running and production-only failures](#94-diagnosing-long-running-and-production-only-failures)

---

# 1. CPU and machine architecture

## 1.1. RISC versus CISC

1. **[Basic] What is the traditional distinction between RISC and CISC?**

   **Answer.** RISC emphasizes a relatively small, regular instruction set, many registers, load/store memory access, and instructions that are easy to pipeline. CISC historically emphasizes richer instructions, varied encodings, and operations that can combine memory access with computation. These are architectural tendencies rather than precise modern categories.

2. **[Deep dive] Why is the RISC/CISC distinction less clear on modern processors?**

   **Answer.** Modern x86 cores decode complex variable-length instructions into simpler internal micro-operations, then execute them in a deeply pipelined, out-of-order engine. Modern ARM and other RISC ISAs also contain sophisticated vector, atomic, cryptographic, and multi-register operations. ISA style still affects decoding, code density, tooling, and compatibility, but microarchitecture determines much observed performance.

3. **[Deep dive] What are the tradeoffs between instruction regularity and code density?**

   **Answer.** Fixed or regular encodings simplify parallel decode and compiler reasoning but can require more instructions for a task. Dense variable-length instructions reduce instruction-cache and fetch bandwidth pressure but need more complex decoding. The best result depends on the workload, cache behavior, implementation, and compiler; instruction count alone is not execution time.

4. **[Design] Can one conclude that RISC is faster or more energy-efficient than CISC?**

   **Answer.** No. Performance and energy depend on the particular core, process technology, cache/memory system, frequency, vector units, power limits, compiler, and workload. Compare measured systems under the relevant constraints. ISA labels are useful background, not a benchmark result.

## 1.2. Endianness

1. **[Basic] What are little-endian and big-endian byte order?**

   **Answer.** They describe how the bytes of a multi-byte value are placed at increasing memory addresses. For `0x12345678`, little-endian stores `78 56 34 12`; big-endian stores `12 34 56 78`. Endianness does not reverse the bit order within each byte and is irrelevant to a single byte.

2. **[Basic] Why does endianness matter for networks and file formats?**

   **Answer.** Raw in-memory representation is machine-dependent. A protocol or file format must define a canonical byte order so different hosts agree; Internet protocol fields traditionally use network byte order, which is big-endian. Serialize fields explicitly and convert at the boundary rather than writing structs directly.

3. **[Code] Which APIs and C++ facilities help convert byte order?**

   **Answer.** POSIX networking provides `htons`, `htonl`, `ntohs`, and `ntohl` for 16/32-bit values. C++20 provides `std::endian` to describe native order, and C++23 provides `std::byteswap` for integral values. Conversion code should operate on unsigned fixed-width values, avoid aliasing violations, and handle mixed-endian formats field by field.

4. **[Deep dive] Why is `send(fd, &object, sizeof object, ...)` not a portable serializer even when both machines use the same endianness?**

   **Answer.** The object may contain padding, pointers, implementation-specific integer/enum widths, vptrs, and representations that differ by compiler or ABI. Padding may contain indeterminate data and leak information. A serializer must define field types, order, byte order, lengths, encoding, versioning, and validation independently of C++ layout.

## 1.3. Out-of-order execution and branch prediction

1. **[Basic] What is out-of-order execution?**

   **Answer.** A superscalar CPU may execute independent instructions in an order different from program order so that stalled operations do not leave execution units idle. Register renaming removes false name dependencies, reservation structures schedule ready work, and a reorder buffer commonly retires results in architectural order. The processor preserves the single-thread-visible ISA behavior except where the memory model permits reordering.

2. **[Deep dive] What dependencies restrict instruction reordering?**

   **Answer.** A true read-after-write dependency must be respected because a consumer needs a producer's result. Write-after-read and write-after-write are name dependencies that register renaming can remove. Memory dependencies are harder because addresses may be unknown; processors predict/speculate and recover on conflicts. Serializing instructions and memory-order constraints reduce available reordering.

3. **[Basic] What is branch prediction, and what is the cost of a misprediction?**

   **Answer.** The processor predicts a branch's direction and often its target so it can keep fetching and executing speculatively. If wrong, speculative work is discarded and the pipeline restarts at the correct path, costing cycles proportional to the pipeline/front-end design. Data-dependent unpredictable branches in tight loops can therefore be expensive.

4. **[Deep dive] What do C++20 `[[likely]]` and `[[unlikely]]` guarantee?**

   **Answer.** They communicate that a control-flow path is expected to be taken more or less often, allowing an implementation to influence layout or optimization. They do not force a hardware prediction, do not change semantics, and can hurt performance when the hint is wrong. Profile-guided optimization usually has better evidence; use manual hints only when domain knowledge is stable and measurement supports them.

5. **[Deep dive] Why can speculative execution matter to security even when incorrect results are retired?**

   **Answer.** Speculative instructions can change microarchitectural state such as caches even though their architectural results are discarded. Timing those side effects can reveal protected data, as in Spectre-class attacks. Security boundaries may require speculation barriers, masking, constant-time code, compiler support, and platform mitigations beyond ordinary language-level correctness.

## 1.4. Memory alignment and padding

1. **[Basic] What is memory alignment?**

   **Answer.** A type's alignment is the address boundary on which its objects must begin, commonly a power of two. `alignof(T)` reports the requirement and `alignas(N)` can request a stricter valid alignment. Allocators and object layout must provide storage satisfying the type's alignment throughout its lifetime.

2. **[Basic] Why does a struct contain padding?**

   **Answer.** The compiler inserts internal padding so each member is correctly aligned and tail padding so every element of an array of the struct is aligned. Therefore `sizeof(Struct)` can exceed the sum of member sizes. Reordering members from higher to lower alignment often reduces size, but ABI/serialization layouts must not be changed casually.

3. **[Code] Why can these two layouts have different sizes?**

   ```cpp
   struct A { char c; double d; int i; };
   struct B { double d; int i; char c; };
   ```

   **Answer.** `A` commonly needs padding between `c` and `d`, plus possible tail padding; `B` naturally places the most strictly aligned member first and can pack the smaller members into the remaining space. Exact sizes are implementation-defined—confirm with `sizeof`, `alignof`, and compiler layout tools rather than assuming common 64-bit results.

4. **[Deep dive] What can happen on a misaligned access?**

   **Answer.** Some architectures support it with a performance penalty or multiple memory transactions; others trap, and atomicity guarantees may be lost. In C++, forming/using a `T*` that does not meet `T`'s alignment requirement is undefined behavior. Read packed external bytes with `memcpy`/explicit decoding instead of casting them to an aligned type.

5. **[Deep dive] Why are packed structs risky?**

   **Answer.** Packing changes ABI/layout and can create misaligned members; taking a reference or pointer to such a member may be unsafe. It also does not solve endianness, bit-field layout, representation, or versioning. Use packed layouts only for a controlled compiler/platform boundary, and usually copy fields into naturally aligned native objects before computation.

---

# 2. Memory systems

## 2.1. Memory hierarchy: L1/L2/L3, RAM, and storage

1. **[Basic] Why does a memory hierarchy exist?**

   **Answer.** No technology simultaneously provides maximum capacity, minimum latency, maximum bandwidth, low energy, and low cost. Systems therefore place small fast storage near execution units and progressively larger slower storage farther away. Hardware and software exploit locality to make the average access cost much lower than accessing the slowest level every time.

   ```mermaid
   flowchart TB
       R[Registers: tiniest / fastest] --> L1[L1 cache: per core]
       L1 --> L2[L2 cache: usually per core]
       L2 --> L3[L3 / last-level cache: often shared]
       L3 --> RAM[Main memory]
       RAM --> SSD[SSD / persistent storage]
       SSD --> NET[Remote storage: often highest latency]
   ```

2. **[Basic] What are temporal and spatial locality?**

   **Answer.** Temporal locality means recently accessed data/instructions are likely to be accessed again. Spatial locality means nearby addresses are likely to be accessed soon. Caches fetch blocks rather than isolated bytes, so contiguous traversal and reuse of a compact working set tend to perform well.

3. **[Deep dive] Are L1, L2, and L3 always private/shared in the same way?**

   **Answer.** No. Common CPUs have private per-core L1 data/instruction caches, private or partially shared L2, and a shared last-level cache, but topology varies by vendor and generation. Non-uniform memory access systems add socket/local-node effects. Query the target hardware and measure; do not encode one topology as a language guarantee.

4. **[Design] How should the hierarchy influence data-structure design?**

   **Answer.** Prefer compact representations, contiguous access, predictable traversal, batching, and working sets that fit relevant caches. Avoid unnecessary pointer chasing and random access when performance matters. Algorithmic complexity remains important, but a theoretically superior structure can lose on real inputs because of cache misses and allocation overhead.

5. **[Deep dive] Is disk automatically an extension of RAM?**

   **Answer.** Virtual memory can page anonymous/file-backed data and the OS page cache uses RAM for file contents, but storage latency is orders of magnitude different and failure/durability semantics differ. Heavy paging causes thrashing, not a smoothly slower form of RAM. Applications with large datasets need explicit I/O, locality, and memory-pressure design.

## 2.2. Cache lines, cache misses, and false sharing

1. **[Basic] What is a cache line?**

   **Answer.** It is the unit transferred and tracked by a hardware cache, commonly—but not universally—64 bytes on contemporary desktop/server CPUs. Reading one byte typically brings its entire containing line into cache. Coherence also operates at line granularity, which matters for shared writes.

2. **[Basic] What is a cache miss?**

   **Answer.** A miss occurs when the requested line is not present in the relevant cache and must be obtained from another level. Compulsory misses are first touches, capacity misses occur when the working set exceeds cache capacity, and conflict misses arise when mapping/associativity forces useful lines to evict each other. Hardware prefetching may hide predictable misses but not arbitrary latency.

3. **[Deep dive] What does cache associativity mean?**

   **Answer.** A set-associative cache maps an address to a set and allows the line to occupy one of several ways in that set. Higher associativity reduces conflicts but costs power, latency, and implementation complexity. Access patterns separated by unfortunate power-of-two strides can contend for the same sets despite unused capacity elsewhere.

4. **[Basic] What is false sharing?**

   **Answer.** Different threads update different variables that happen to occupy the same cache line. Although no source-level data is shared, coherence repeatedly transfers/invalidate the line between cores, serializing traffic and destroying scalability. It is a performance problem, not by itself a C++ data race when the variables are independently synchronized/atomic.

5. **[Code] How can false sharing be reduced?**

   **Answer.** Separate frequently written per-thread/per-core fields onto different lines, batch updates locally, and combine later. C++17 exposes `std::hardware_destructive_interference_size` as an implementation hint; `alignas` plus padding can help, but layout and allocator placement must be verified. Excess padding wastes memory and can worsen cache capacity, so measure before and after.

6. **[Deep dive] How do array-of-structures and structure-of-arrays layouts affect caching?**

   **Answer.** Array of structures keeps all fields of one entity together, which is good when each iteration uses most fields. Structure of arrays keeps one field contiguous across entities, improving bandwidth/vectorization when a pass touches only selected fields. Hybrid/blocked layouts often balance several access patterns; choose based on hot loops rather than aesthetic preference.

7. **[Design] How do you confirm that cache behavior is the bottleneck?**

   **Answer.** Establish a reproducible benchmark, then use hardware counters through tools such as `perf stat`/`perf record` to inspect cycles, instructions, cache references/misses, stalled cycles, and coherence-related events available on the machine. Correlation is not proof: compare alternative layouts and input sizes, control CPU frequency/noise, and verify that wall-clock improvement matches the counter hypothesis.

## 2.3. Virtual memory, pages, page faults, and the TLB

1. **[Basic] What is virtual memory?**

   **Answer.** Each process uses a virtual address space mapped by the OS and hardware to physical memory or backing storage. This provides isolation, relocation, sparse address spaces, protection bits, shared mappings, and demand allocation. A virtual address is not inherently a physical RAM location.

2. **[Basic] What are pages and page tables?**

   **Answer.** Virtual and physical memory are divided into fixed-size pages/frames, commonly 4 KiB with optional larger pages. Page tables map virtual page numbers to physical frames and store permissions/state. Multi-level tables avoid allocating entries for unused regions, while the CPU's memory-management unit performs translation.

3. **[Basic] What is a page fault, and how do minor and major faults differ?**

   **Answer.** A page fault is a synchronous exception when a translation is absent or access violates permissions. The kernel may resolve it—for example by allocating a zero page, completing copy-on-write, or loading file data—then resume the instruction. A minor fault needs no storage I/O; a major fault needs data from storage and is much slower. Invalid access normally becomes a process signal such as `SIGSEGV` or `SIGBUS` on Linux.

4. **[Basic] What is the TLB?**

   **Answer.** The Translation Lookaside Buffer caches recent virtual-to-physical page translations and permissions. A TLB hit avoids a page-table walk; a TLB miss triggers a hardware or software walk but is not necessarily a page fault. Large scattered working sets can suffer TLB pressure even when their data is in CPU cache.

5. **[Deep dive] What are demand paging and copy-on-write?**

   **Answer.** Demand paging creates mappings before physical pages are populated and materializes them on first access. Copy-on-write lets mappings initially share read-only physical pages; a write fault creates a private copy. Linux `fork` relies heavily on this, making creation cheap initially but potentially expensive when parent and child dirty many pages.

6. **[Deep dive] What is thrashing?**

   **Answer.** Thrashing occurs when the actively used pages exceed available physical memory and the system spends most of its time evicting and reloading pages. Throughput collapses while storage I/O and fault rates soar. Reduce the working set/concurrency, improve locality, add memory, or redesign data access; caching more data can make the problem worse.

7. **[Deep dive] When can huge pages help, and what are the tradeoffs?**

   **Answer.** Larger pages reduce page-table size and TLB pressure for large, densely accessed regions. They can increase internal fragmentation, allocation/compaction difficulty, copy-on-write cost, and latency spikes. Transparent Huge Pages may help or hurt unpredictably; database/HPC workloads often configure and measure them deliberately.

## 2.4. LRU as a caching pattern

1. **[Basic] What does an LRU cache evict?**

   **Answer.** Least Recently Used evicts the entry whose last access is oldest, approximating the idea that recently used data is more likely to be reused. It needs a capacity definition—entry count, bytes, cost, or another budget—and a precise decision about whether reads and writes both refresh recency.

2. **[Code] How is an O(1) LRU cache commonly implemented?**

   **Answer.** Use a hash map from key to a node/iterator and a doubly linked list ordered from most to least recent. Lookup uses the map, then splices the node to the front; insertion adds to the front and removes the back when over capacity. In C++, `std::list` provides stable iterators and constant-time `splice`, but allocation/locality overhead may matter.

3. **[Deep dive] What makes a concurrent LRU cache difficult?**

   **Answer.** Every hit mutates recency, turning reads into contended writes. A single mutex is correct and may be sufficient; sharding reduces contention but approximates global LRU. More advanced schemes batch recency updates or use policies such as CLOCK/TinyLFU. Also prevent duplicate expensive loads with per-key coordination and define whether loaders run under locks.

4. **[Deep dive] When does LRU perform poorly?**

   **Answer.** Sequential scans can evict a valuable working set, recency may not predict reuse, and equal-sized entry accounting fails when costs differ. TTL, LFU/TinyLFU, segmented LRU, admission policies, or workload-specific caches may do better. Hit rate alone is insufficient: measure latency, load cost, memory, and staleness correctness.

5. **[Deep dive] What is the offline-optimal cache policy, and how can it help evaluate a real policy?**

   **Answer.** Belady's MIN (OPT) assumes the complete future request trace is known. On each miss into a full cache, it evicts the resident item whose next request is farthest in the future, or an item that is never requested again. For equal-size entries, equal miss costs, and fixed capacity, this minimizes the number of misses. It is not deployable as an online policy because a real cache does not know future requests.

   Replay the same representative trace through OPT and the candidate policy to obtain a lower bound on misses and quantify the policy gap across several capacities. Also report warm-up treatment, hit and miss counts, latency or weighted miss cost, memory budget, and workload phases. With variable object sizes, unequal load costs, expiry, or invalidation, the simple farthest-next-use rule is no longer the complete optimum, so the oracle and metric must match the real objective.

## 2.5. Memory-mapped I/O (`mmap`)

1. **[Basic] What does `mmap` do?**

   **Answer.** On POSIX systems, `mmap` creates a virtual-memory mapping backed by a file/device or anonymous memory. The process accesses the region through ordinary loads/stores, while page faults populate pages and the kernel manages caching. It does not necessarily read the entire file immediately.

2. **[Basic] How do `MAP_SHARED` and `MAP_PRIVATE` differ?**

   **Answer.** `MAP_SHARED` makes modifications visible to other mappings of the same region and eligible to be written back to the file. `MAP_PRIVATE` creates copy-on-write changes private to the process and does not update the underlying file. Neither flag alone defines application-level synchronization or durable transaction semantics.

3. **[Deep dive] Which correctness hazards accompany file mappings?**

   **Answer.** Access beyond the mapped range is invalid, and accessing pages beyond a file that another process truncates can raise `SIGBUS`. File size, offsets, page alignment, object lifetime, and concurrent modifications must be coordinated. Mapping bytes does not construct C++ objects or make arbitrary struct layout portable.

4. **[Deep dive] Does `msync` make a complex data structure crash-consistent?**

   **Answer.** It can request synchronization of dirty mapped pages, but crash consistency also depends on update ordering, metadata, filesystem/device guarantees, and atomicity. A crash between related writes can leave torn logical state. Durable formats need journaling, copy-on-write, checksums/versioning, or a transactional protocol; consult the exact platform guarantees.

5. **[Design] When is `mmap` preferable to `read`/`write`, and when is it not?**

   **Answer.** It is attractive for random access to large files, shared memory, executable loading, and read-mostly data where the page cache should manage demand. Explicit I/O can provide clearer error timing, controlled buffering, streaming behavior, and simpler handling of truncation or remote filesystems. `mmap` page faults can introduce unpredictable latency and address-space pressure; benchmark the actual access pattern.

---

# 3. Procedure execution and operating-system boundaries

## 3.1. Call stack, stack frames, and procedure calls

1. **[Basic] What is a call stack?**

   **Answer.** It is a per-thread region commonly used to manage nested function calls in last-in-first-out order. A call frame may contain a return address, saved registers, spilled/local variables, alignment space, and outgoing arguments. Exact contents are determined by the compiler, optimization, and platform ABI; some functions need little or no stack space.

2. **[Basic] What do machine-level call and return operations do?**

   **Answer.** A call transfers control to a target while preserving a return address, either on the stack (as x86 `call` commonly does) or in a link register (as many RISC ISAs do). A return transfers control to that saved address. Function prologue/epilogue code adjusts the stack and saves/restores registers as required; optimized code can inline calls or use tail calls, removing these steps.

3. **[Deep dive] What is a calling convention?**

   **Answer.** It is the ABI agreement for passing arguments/results, which registers each side must preserve, stack alignment, name representation, and unwinding metadata. Caller-saved registers may be overwritten by a call; callee-saved registers must be restored by the callee. Code compiled with incompatible conventions can link yet corrupt registers/stack at runtime.

4. **[Deep dive] Why can optimized stack traces be incomplete or surprising?**

   **Answer.** Inlining removes physical frames, tail-call optimization reuses a frame, frame pointers may be omitted, and debug information must reconstruct variable locations that move or disappear. Corrupted stacks and missing unwind metadata also hinder backtraces. Build with debug info and appropriate unwind/frame-pointer options for production diagnostics when the overhead is acceptable.

5. **[Basic] What causes stack overflow?**

   **Answer.** Unbounded/deep recursion, very large automatic arrays, or excessive per-frame storage can exceed the thread's finite stack mapping. The guard page typically triggers a fault and the OS terminates/signals the process; recovery is unreliable because handling itself needs stack. Use bounded recursion, heap-backed containers, iterative algorithms, or deliberately configured thread-stack sizes.

6. **[Deep dive] Is tail-call optimization guaranteed in C++?**

   **Answer.** No. A compiler may replace a final call with a jump and reuse the frame when ABI, cleanup, debugging, and optimization constraints permit, but the standard does not require it. Correctness must not rely on tail recursion having constant stack usage.

## 3.2. Operating-system role and system calls

1. **[Basic] What are the core responsibilities of an operating-system kernel?**

   **Answer.** It manages CPU scheduling, virtual memory, processes/threads, devices, filesystems, networking, timers, security/isolation, and controlled sharing of hardware resources. It exposes abstractions such as files, sockets, address spaces, and processes while enforcing privileges and coordinating concurrent access.

2. **[Basic] What is a system call?**

   **Answer.** It is a controlled request from user-space code to the kernel for a privileged service, such as `read`, `write`, `mmap`, or `clone`. A special instruction crosses into kernel mode, the kernel validates arguments and performs/starts the operation, then returns a result/error. The exact ABI and mechanism are architecture/OS-specific.

3. **[Deep dive] Is every C library call a system call?**

   **Answer.** No. Functions such as `strlen` and most `malloc` operations run entirely in user space; `malloc` occasionally obtains pages with `brk`/`mmap`. A library call such as buffered `fwrite` may delay or combine kernel writes. Conversely, libc wrappers adapt the raw syscall ABI into C return values and `errno`.

4. **[Deep dive] Why are syscall boundaries important for correctness and performance?**

   **Answer.** Crossing the boundary has overhead from privilege transition, validation, and security mitigations; the operation may also block for far longer than the transition itself. Batch work, buffer I/O, and avoid needless tiny calls—but never skip required validation. Syscalls can return partial results or be interrupted; correct loops handle documented semantics rather than assuming all requested bytes complete.

5. **[Code] How can Linux system-call activity be inspected?**

   **Answer.** `strace -f -o trace.txt program args...` records calls, results, errors, and durations (with options such as `-tt`, `-T`, and filters like `-e trace=file,network`). It is useful for missing files, permissions, blocking, and unexpected process creation. Tracing changes timing and can expose sensitive arguments/data, so use it deliberately.

## 3.3. Interrupts, processor exceptions, and OS signals

1. **[Basic] How do hardware interrupts differ from processor exceptions?**

   **Answer.** An interrupt is usually asynchronous to the current instruction stream and originates from hardware such as a timer or network device. A processor exception is synchronous to an instruction, for example a page fault, invalid opcode, breakpoint, or divide error. Architecture terminology varies; both transfer control through privileged handlers using saved machine state.

2. **[Deep dive] What are faults, traps, and aborts?**

   **Answer.** In common x86 terminology, a fault is reported so the instruction can often be restarted after correction (page fault), a trap is reported after the instruction (breakpoint/single-step), and an abort indicates a severe condition with unreliable restart. These are hardware categories, not C++ exception classes, and other architectures use different terminology.

3. **[Deep dive] How are CPU events related to Unix signals?**

   **Answer.** The kernel handles the low-level event first. If a user process caused an unrecoverable condition, the kernel may deliver a signal such as `SIGSEGV`, `SIGILL`, `SIGFPE`, or `SIGTRAP`. The mapping is policy- and platform-dependent: a page fault may be resolved invisibly, while an invalid mapping becomes `SIGSEGV`. Signals can also originate from other processes, terminals, timers, or kernel subsystems without a CPU exception.

4. **[Deep dive] What restrictions apply inside a POSIX signal handler?**

   **Answer.** It can interrupt code at almost any point, so only async-signal-safe functions may be called. Most C++ library operations, allocation, locking, streams, and throwing are unsafe. A robust handler usually sets a `volatile sig_atomic_t` flag or writes a byte to a self-pipe/event descriptor, then normal event-loop code performs the real work.

5. **[Basic] How do C++ exceptions differ from hardware exceptions and Unix signals?**

   **Answer.** C++ exceptions are a language-level, synchronous control-flow mechanism with typed matching and stack unwinding. CPU exceptions are architectural events, and signals are OS process notifications with asynchronous-delivery rules. Some platforms translate selected hardware faults into language/runtime exceptions, but portable C++ cannot treat arbitrary access violations as catchable C++ exceptions.

---

# 4. Processes and IPC

## 4.1. Process creation, states, termination, waiting, and signals on Linux

1. **[Basic] What is a process?**

   **Answer.** A process is an executing program instance with a virtual address space, security credentials, open-file table references, signal dispositions, environment, and one or more threads. The kernel schedules threads, while the process groups resources and isolation. A PID identifies it temporarily; PIDs are reused, so long-lived identity needs more care.

2. **[Basic] What does `fork()` do?**

   **Answer.** It creates a child process whose user-space state initially resembles the parent. It returns the child's PID in the parent, `0` in the child, and `-1` on failure. Memory is normally implemented with copy-on-write, while open file descriptors refer to the same underlying open file descriptions, sharing offsets/status where specified.

3. **[Deep dive] Why is `fork()` delicate in a multithreaded process?**

   **Answer.** Only the calling thread exists in the child, but locks may remain in states held by vanished threads. Until `exec`, the child may safely call only async-signal-safe operations under POSIX restrictions. Prefer `posix_spawn` or an immediate, carefully implemented `exec` path; library allocation/logging in the child can deadlock.

4. **[Basic] What does an `exec` function do?**

   **Answer.** It replaces the current process image with a new program; on success it does not return. The PID remains, and open file descriptors remain unless marked close-on-exec. Address space, code, stack, and most process image state are replaced; arguments/environment are supplied anew. On failure, it returns with `errno` set.

5. **[Basic] Which process states are commonly visible on Linux?**

   **Answer.** A task can be runnable/running, interruptible sleeping, uninterruptible sleeping (often waiting in kernel I/O), stopped/traced, or zombie. Tools may use letters such as `R`, `S`, `D`, `T`, and `Z`. Exact kernel states are more detailed and can change; a sleeping process is not consuming CPU while waiting.

6. **[Basic] What are zombies and orphans?**

   **Answer.** After a child exits, a small zombie entry retains status/accounting until its parent calls `wait`/`waitpid`; unreaped zombies consume process-table entries but no normal address space. An orphan is a live child whose parent exited; it is reparented to a designated subreaper/init-like process, which later reaps it. Ignoring child lifecycle creates resource leaks.

7. **[Deep dive] How do Unix signals work at a high level?**

   **Answer.** A signal has a disposition—default action, ignore, or handler—and can be blocked per thread, becoming pending until unblocked. Delivery to a multithreaded process follows signal-specific targeting/mask rules. Standard signals generally do not queue multiple identical pending instances; real-time signals can. `SIGKILL` and `SIGSTOP` cannot be caught, blocked, or ignored.

8. **[Code] Show the essential `fork`–`exec`–`waitpid` flow.**

   **Answer.** The child performs minimal failure-safe setup and calls `exec`; the parent waits and interprets status with macros.

   ```cpp
   pid_t pid = ::fork();
   if (pid == 0) {
       ::execlp("tool", "tool", "--version", static_cast<char*>(nullptr));
       ::_exit(127); // exec failed; do not run parent C++ cleanup
   }
   if (pid < 0) {
       throw_system_error(errno, "fork");
   }

   int status = 0;
   pid_t waited = -1;
   do {
       waited = ::waitpid(pid, &status, 0);
   } while (waited < 0 && errno == EINTR);
   if (waited < 0) {
       throw_system_error(errno, "waitpid");
   }
   // Check WIFEXITED/WEXITSTATUS or WIFSIGNALED/WTERMSIG.
   ```

## 4.2. Interprocess communication: pipes, shared memory, and message queues

1. **[Basic] How do anonymous and named pipes work?**

   **Answer.** A pipe is a kernel byte stream with a read and write end, commonly used between related processes after `fork`. A FIFO is a named filesystem entry that unrelated processes can open. Writes up to `PIPE_BUF` have specified atomicity among writers, but reads/writes can be partial; pipes do not preserve application message boundaries.

2. **[Deep dive] What are the main properties of shared memory IPC?**

   **Answer.** Processes map the same physical pages, avoiding repeated payload copying and offering high bandwidth/low latency. The OS provides mapping, not a safe data structure: participants need process-shared synchronization, layout/version agreements, lifetime/crash recovery, and pointer-independent representations. Ordinary pointers stored by one process are usually meaningless in another address space.

3. **[Basic] What do message queues add over byte-stream pipes?**

   **Answer.** They preserve message boundaries and may provide priorities, blocking/nonblocking operations, and kernel-managed lifetime. POSIX and System V queues differ in APIs and semantics; distributed brokers add durability/routing. Queue capacity is finite, so producers must handle blocking/full conditions and consumers must validate untrusted messages.

4. **[Deep dive] Why are Unix-domain sockets a common IPC choice?**

   **Answer.** They provide bidirectional stream or datagram/sequence-packet semantics, work with event loops, can pass file descriptors/credentials on supported systems, and reuse the sockets API without network routing. They are usually simpler to secure and faster than loopback TCP for same-host communication, while retaining explicit serialization and failure boundaries.

5. **[Design] How do you choose an IPC mechanism?**

   **Answer.** Compare payload size/rate, latency, message boundaries, process relationship, bidirectionality, durability, security, backpressure, crash recovery, portability, and operational observability. Pipes/sockets favor simplicity and isolation; shared memory favors high throughput at much greater correctness complexity; message queues favor decoupling and discrete work. Start with the simplest mechanism meeting measured requirements.

---

# 5. Threads and concurrency

## 5.1. Thread model, POSIX threads, and context switches

1. **[Basic] How does a thread differ from a process?**

   **Answer.** Threads in one process share the address space and most process resources, while each has its own registers, stack, scheduling state, signal mask, and thread-local storage. Sharing makes communication cheap but removes memory isolation: one thread's invalid write can corrupt the entire process. Processes have stronger isolation but require explicit IPC.

2. **[Basic] What are the essential POSIX thread lifecycle operations?**

   **Answer.** `pthread_create` starts a thread at a C-compatible entry function. A joinable thread's termination result/resources are collected with `pthread_join`; a detached thread cleans up automatically and cannot be joined. Every created thread must eventually be joined or detached. In portable C++, prefer `std::thread` or C++20 `std::jthread` unless POSIX-specific control is needed.

3. **[Deep dive] What is a context switch?**

   **Answer.** The scheduler stops one runnable thread and resumes another by saving/restoring architectural state and switching relevant kernel/accounting context. Switching between processes may also change address spaces and TLB behavior. The direct save/restore cost is only part of the impact: lost cache/TLB locality and scheduler migration can dominate.

4. **[Deep dive] What causes context switches?**

   **Answer.** A thread may block on I/O, a lock, sleep, or page fault; yield; exhaust its time slice; or be preempted by a higher-priority runnable thread. Interrupt handling can prompt scheduling but is not itself always a user-thread switch. Excess runnable threads cause oversubscription, queueing, and cache disruption.

5. **[Basic] What is thread-local storage?**

   **Answer.** A `thread_local` object has one instance per thread and thread storage duration. It is useful for per-thread buffers, random generators, and context that should not contend. Initialization/destruction occurs per thread under language/runtime rules; hidden TLS dependencies complicate tests, task migration, and thread-pool code because logical requests are not the same as threads.

6. **[Design] How many worker threads should a program create?**

   **Answer.** CPU-bound pools often start near the available CPU quota/core count; blocking workloads may benefit from more, but only after considering I/O concurrency and downstream limits. Containers/cgroups, SMT, NUMA, task duration, and mixed workloads change the answer. Bound the pool/queue, measure throughput and tail latency, and avoid creating one OS thread per tiny task.

## 5.2. Race conditions, critical sections, mutexes, and semaphores

1. **[Basic] What is the difference between a race condition and a C++ data race?**

   **Answer.** A race condition is any correctness bug whose outcome depends on timing/interleaving, even when all accesses are individually synchronized. A C++ data race has a precise definition: conflicting accesses to the same memory location from different threads, at least one a write, without a happens-before relationship and without all relevant accesses being atomic. A data race causes undefined behavior.

2. **[Basic] What is a critical section?**

   **Answer.** It is code that accesses a shared invariant/resource and must not overlap with incompatible operations. The protected invariant, not merely a variable, determines the boundary. Keep the section small enough to limit contention but large enough that no observer can see partially updated state.

3. **[Basic] What guarantees does a mutex provide?**

   **Answer.** It provides mutual exclusion among threads using the same mutex and synchronization: a successful lock acquisition observes writes sequenced before the previous unlock. It protects only code that consistently follows the protocol. Use RAII (`std::lock_guard`, `std::unique_lock`, `std::scoped_lock`) so exceptions and returns cannot forget to unlock.

4. **[Basic] How does a semaphore differ from a mutex?**

   **Answer.** A mutex represents ownership of an exclusive critical section and must normally be unlocked by its owning thread. A counting semaphore tracks permits; acquire decrements/waits and release increments, possibly from another thread. Semaphores model resource counts, bounded queues, and notifications but do not automatically protect a complex invariant like a mutex does.

5. **[Deep dive] When are atomics preferable to a mutex?**

   **Answer.** Atomics are effective for small independent state, counters, flags, and carefully designed lock-free structures. They do not make a multi-variable invariant atomic and correct memory ordering is subtle. A mutex is often clearer, supports blocking, and can be faster under low contention than a complex retry loop. Choose based on semantics and measurement, not a blanket belief that lock-free is faster.

6. **[Basic] Why do condition-variable waits use a predicate loop?**

   **Answer.** Wakeups may be spurious, multiple waiters compete, and the condition may become false again before a thread reacquires the mutex. `cv.wait(lock, predicate)` atomically releases the mutex while waiting and rechecks the predicate after reacquisition until true. The predicate's shared state must be protected by the same mutex.

7. **[Code] Why is this increment unsafe, and how can it be fixed?**

   ```cpp
   int counter = 0;
   // Executed concurrently:
   ++counter;
   ```

   **Answer.** Increment is a read-modify-write, and concurrent unsynchronized accesses create a data race. If the counter is independent, use `std::atomic<int> counter; counter.fetch_add(1, std::memory_order_relaxed);`. If it participates in a larger invariant, protect the whole invariant with a mutex; making only the integer atomic may preserve memory safety while leaving the algorithm wrong.

## 5.3. Deadlock, livelock, and starvation

1. **[Basic] What are the four Coffman conditions for deadlock?**

   **Answer.** Mutual exclusion: some resource is non-shareable. Hold and wait: an actor holds resources while requesting more. No preemption: resources cannot be forcibly taken safely. Circular wait: actors form a cycle, each waiting for the next. All four are necessary for classic resource deadlock; breaking any one prevents that form.

2. **[Code] How does inconsistent lock ordering cause deadlock?**

   **Answer.** If one path locks `A` then `B` while another locks `B` then `A`, each can hold one and wait forever for the other. Establish a global order and acquire locks only in that order, or use `std::scoped_lock(a, b)`/`std::lock` to acquire a set with a deadlock-avoidance algorithm. The rule must cover callbacks and hidden locks too.

3. **[Basic] How do livelock and starvation differ from deadlock?**

   **Answer.** Deadlocked participants make no progress because they wait permanently. In livelock they remain active and repeatedly react/retry but still make no useful progress. Starvation means one participant is continually denied progress while others continue, often due to unfair scheduling, lock policy, or a constant stream of readers.

4. **[Deep dive] How can deadlocks involving condition variables or futures occur without two obvious mutexes?**

   **Answer.** Deadlock is a wait-for cycle, not specifically a two-lock cycle. A thread may hold a lock while waiting for a task that needs the same lock, a bounded queue may have every worker blocked producing work for itself, or callbacks may synchronously wait back into their caller. Model resource/task dependencies and never wait for external/user code while holding unrelated locks.

5. **[Deep dive] How can deadlocks be diagnosed?**

   **Answer.** Capture all thread stacks while the process is hung (`gdb`, `pstack`, core dump), inspect mutex/futex waits, and build a wait-for graph. Add lock ownership/order diagnostics and contention tracing in debug builds. ThreadSanitizer detects many data races and some lock-order issues but does not prove freedom from all deadlocks.

6. **[Design] What prevention strategies exist besides lock ordering?**

   **Answer.** Avoid hold-and-wait by acquiring all resources together, reduce shared mutable state, use message passing/immutable snapshots, use try-lock with bounded randomized backoff where semantics permit, partition ownership, or impose timeouts/cancellation. Timeouts detect/bound waiting but do not automatically restore consistency; rollback must be designed.

## 5.4. Dining philosophers and readers-writers

1. **[Basic] What does the dining philosophers problem demonstrate?**

   **Answer.** Several actors need multiple shared resources; if each acquires one fork and waits for the next, circular wait creates deadlock. It illustrates resource ordering, partial acquisition, fairness, and the gap between local correctness and global progress.

2. **[Deep dive] Name valid solutions to dining philosophers.**

   **Answer.** Number forks and always acquire lower before higher; allow at most `N-1` philosophers to compete via a semaphore; use an arbitrator that grants both forks together; or use `std::scoped_lock` for both mutexes. A solution should also discuss starvation/fairness, not only absence of deadlock.

3. **[Basic] What is the readers-writers problem?**

   **Answer.** Multiple readers may safely enter concurrently, while a writer needs exclusive access. A readers-writer lock (`std::shared_mutex`) models this, but the policy for queued readers/writers determines fairness and latency. Read-heavy workloads are not automatically faster because shared-lock bookkeeping and cache contention have costs.

4. **[Deep dive] What are reader-preference and writer-preference tradeoffs?**

   **Answer.** Reader preference maximizes read concurrency but a steady stream of readers can starve writers. Writer preference bounds writer delay but can increase reader latency and potentially starve readers. Fair/phase-based policies alternate groups at extra coordination cost. Choose according to latency requirements and measure under realistic load.

5. **[Design] When is copying or versioning better than a readers-writer lock?**

   **Answer.** For small/read-mostly state, immutable snapshots published atomically let readers avoid locks. Copy-on-write, RCU-like schemes, or versioned data can scale reads but make reclamation and updates more complex. Use them only when contention measurements justify the extra lifetime/memory model.

## 5.5. Thread scheduling and lock ordering

1. **[Basic] How does a preemptive scheduler allocate CPU time?**

   **Answer.** It maintains runnable threads and selects them based on scheduling class, priority, fairness, affinity, and CPU topology. A timer or higher-priority event can preempt a running thread. Ordinary programs should not assume a deterministic interleaving or exact time slice; correctness must hold for every permitted schedule.

2. **[Deep dive] What are CPU affinity and migration?**

   **Answer.** Affinity constrains which CPUs may run a thread. Keeping a thread on one CPU can preserve cache locality, while migration helps load balancing and thermal/fairness goals. Pinning can harm throughput when it leaves CPUs idle or ignores NUMA placement; use it only with measured topology-aware reasons.

3. **[Deep dive] What is priority inversion?**

   **Answer.** A high-priority thread waits for a lock held by a low-priority thread, which is itself delayed by medium-priority work. Priority inheritance temporarily boosts the lock owner; priority ceiling protocols provide stronger planning in real-time systems. Ordinary mutexes/platform policies may not provide these semantics automatically.

4. **[Design] How should a project establish lock ordering?**

   **Answer.** Assign locks a documented global hierarchy based on subsystem or object identity and require acquisition in ascending order. For dynamic peer objects, use a stable key or acquire the set with `std::scoped_lock`; never use a movable address as an undocumented order without considering lifetime. Encapsulate multi-lock operations so callers cannot violate the rule.

5. **[Deep dive] How can lock contention be reduced without sacrificing correctness?**

   **Answer.** Shorten critical sections, move I/O/callbacks outside locks, shard independent state, batch operations, use per-thread accumulation, reduce write frequency, and use immutable snapshots where appropriate. More locks can introduce overhead and deadlocks; fewer locks can serialize work. Profile contention and tail latency rather than guessing.

---

# 6. Files, memory safety, and cryptography

## 6.1. Files, directories, and hard/symbolic links

1. **[Basic] What is a file descriptor on Unix-like systems?**

   **Answer.** It is a small process-local integer indexing an open file description/reference in the kernel. Descriptors can refer to regular files, directories, pipes, sockets, devices, and more. Duplicated/inherited descriptors may share an underlying file offset and status flags. Close every owned descriptor with RAII and account for `fork`/`exec` inheritance.

2. **[Basic] What is a directory conceptually?**

   **Answer.** It maps names to filesystem objects (commonly inode-like identities). Paths are resolved by walking directory entries, subject to permissions, mount points, and links. A filename belongs to a directory entry rather than being an intrinsic permanent property of the file object.

3. **[Basic] How do hard links and symbolic links differ?**

   **Answer.** A hard link is another directory entry referring to the same underlying file identity; all names are peers, and the data remains until link count and open references are gone. A symbolic link is a separate object containing a path that is resolved when followed; it can cross filesystems and can dangle. Hard links generally cannot target directories and cannot cross filesystem boundaries.

4. **[Deep dive] What happens when an open file is unlinked?**

   **Answer.** Its directory entry is removed, but processes with open descriptors can continue using the underlying object. Storage is reclaimed only when no hard links and no open references remain. This enables safe temporary files and log rotation, but disk space can remain consumed invisibly until a process closes the deleted file.

5. **[Deep dive] Why is path-based check-then-open unsafe?**

   **Answer.** Between checking permissions/type and opening, an attacker or concurrent process can replace a path component or symlink—a TOCTOU race. Open first with appropriate flags such as `O_NOFOLLOW`, `O_CLOEXEC`, and creation/exclusivity modes where supported, then validate via the descriptor. Directory-relative APIs (`openat` family) help constrain resolution.

6. **[Design] How can a file be replaced atomically?**

   **Answer.** Write a temporary file in the same filesystem/directory, flush it as required, set metadata, then rename it over the destination; POSIX rename is atomic with respect to namespace visibility under documented conditions. Durability across crash may also require syncing the file and containing directory. Atomic visibility is not identical to durable persistence.

## 6.2. Buffer overflow

1. **[Basic] What is a buffer overflow?**

   **Answer.** Code writes beyond the bounds of an allocated object/buffer, corrupting adjacent memory. It can occur on stack, heap, static storage, or within an object and is undefined behavior in C/C++. Consequences range from silent data corruption and crashes to control-flow hijacking or information disclosure.

2. **[Code] Why is this code unsafe?**

   ```cpp
   char destination[16];
   std::strcpy(destination, input);
   ```

   **Answer.** `strcpy` has no destination capacity and copies until a null terminator; an input longer than 15 bytes overflows. Use a length-aware representation and validate before copying, for example return/store `std::string`, or accept `std::span<char>` and produce an explicit truncation/error result. Replacing it with `strncpy` blindly is not sufficient because termination and padding semantics are error-prone.

3. **[Deep dive] Which other bugs are related to buffer overflows?**

   **Answer.** Off-by-one errors, integer overflow in size calculations, signed/unsigned conversion, use-after-free, iterator invalidation, underflow before a buffer, and incorrect encoding-length assumptions can all lead to out-of-bounds access. Validating only one copy call misses the upstream arithmetic and lifetime causes.

4. **[Deep dive] Which mitigations exist, and what do they not solve?**

   **Answer.** Stack canaries detect some overwritten frames; ASLR randomizes locations; NX/W^X prevents executing writable data; control-flow protection and hardened libraries raise exploitation difficulty. Sanitizers and fuzzing find bugs during testing. These are defense in depth, not memory safety: valid-looking data corruption and information leaks may remain, and undefined behavior must still be removed.

5. **[Design] How should modern C++ code reduce overflow risk?**

   **Answer.** Prefer containers and views carrying size (`vector`, `array`, `span`, `string`, `string_view`), bounds-checked parsing at trust boundaries, checked arithmetic, RAII ownership, and algorithms instead of raw pointer loops. Compile with warnings and hardening, test with AddressSanitizer/UndefinedBehaviorSanitizer and fuzzers, and keep unsafe C boundaries small and reviewed.

## 6.3. Symmetric versus asymmetric cryptography

1. **[Basic] How do symmetric and asymmetric encryption differ?**

   **Answer.** Symmetric encryption uses the same secret key (or trivially related keys) for encryption and decryption; it is fast and suited to bulk data but requires secure key sharing. Asymmetric cryptography uses a public/private key pair; it supports key agreement, encryption in suitable schemes, and signatures, but is slower and has stricter size/algorithm constraints.

2. **[Basic] Why do real protocols use hybrid cryptography?**

   **Answer.** They use asymmetric authentication/key agreement to establish fresh symmetric session keys, then efficient symmetric authenticated encryption for application data. This combines deployable identity/key distribution with high throughput. TLS is the canonical example, though its exact handshake and algorithms depend on the version/cipher suite.

3. **[Deep dive] What is authenticated encryption, and why is encryption alone insufficient?**

   **Answer.** AEAD schemes such as AES-GCM or ChaCha20-Poly1305 provide confidentiality and integrity/authenticity for ciphertext plus optional associated data. Encryption without authentication can allow undetected modification and padding/oracle attacks. Nonce uniqueness requirements are critical; nonce reuse can catastrophically break security even when the key remains secret.

4. **[Basic] How do cryptographic hashes and digital signatures differ from encryption?**

   **Answer.** A cryptographic hash maps data to a fixed-size digest and is one-way; it provides no secrecy. A digital signature uses a private key to authenticate data and is verified with the public key, providing integrity/authenticity and scheme-specific non-repudiation properties. Passwords need a salted password-hashing/KDF algorithm, not a fast general hash or reversible encryption.

5. **[Deep dive] Why is key management often harder than choosing an algorithm?**

   **Answer.** Systems must generate keys with secure randomness, protect them at rest/use, restrict access, rotate/revoke them, back up/recover appropriately, and audit usage. Hard-coded keys, nonce reuse, accidental logs, and broad access defeat strong algorithms. Use established key-management systems and define compromise recovery before deployment.

6. **[Design] Why should application developers avoid designing their own cryptographic protocol?**

   **Answer.** Secure composition depends on subtle choices involving modes, nonces, padding, key derivation, transcript binding, replay protection, side channels, and error behavior. Use maintained high-level libraries and standardized protocols, select safe defaults, and obtain expert review. “The primitive is secure” does not imply the composed protocol is secure.

---

# 7. Networking

## 7.1. OSI and TCP/IP models

1. **[Basic] What are the seven OSI layers?**

   **Answer.** From lowest to highest: Physical, Data Link, Network, Transport, Session, Presentation, and Application. The model is conceptual: real protocols and implementations do not always fit one layer cleanly. It is useful for vocabulary and isolating where a failure or responsibility belongs.

2. **[Basic] How does the practical TCP/IP model map to OSI?**

   **Answer.** Link roughly covers OSI physical/data-link; Internet covers network (IP); Transport covers TCP/UDP; Application combines OSI session, presentation, and application responsibilities. TLS is often described between transport and application but uses both application-visible policy and lower-layer transport.

3. **[Basic] What is encapsulation?**

   **Answer.** Each layer treats higher-layer data as payload and adds its own header/trailer: an HTTP message travels in a TCP byte stream, TCP segments are carried in IP packets, and IP packets in link-layer frames. The receiver removes and validates layers in reverse. Offloading and capture points can make the packets observed by tools look different from final wire frames.

   ```mermaid
   flowchart LR
       H[HTTP / application data] --> T[TCP segment]
       T --> I[IP packet]
       I --> E[Ethernet or Wi-Fi frame]
       E --> W[physical medium]
   ```

4. **[Deep dive] What do switches and routers primarily operate on?**

   **Answer.** An Ethernet switch forwards frames using link-layer MAC addresses within a broadcast domain. A router forwards IP packets between networks based on routing tables and decrements TTL. Real devices can combine switching, routing, firewalling, NAT, tunneling, and application inspection, so the layer label describes the forwarding decision rather than the whole appliance.

5. **[Deep dive] What is MTU, and why does it matter?**

   **Answer.** Maximum Transmission Unit is the largest network-layer packet payload a link can carry without link-specific fragmentation/segmentation. Oversized IPv4 packets may fragment (unless prohibited); IPv6 routers do not fragment in transit, relying on path MTU discovery. Lost ICMP feedback or tunnels with smaller effective MTU can create “works for small messages” failures.

6. **[Design] How does layering help troubleshoot a connectivity problem?**

   **Answer.** Work upward: link state/addressing, local IP and route, neighbor resolution, path reachability/MTU, transport handshake/ports, TLS identity/handshake, then application protocol. Avoid treating `ping` success/failure as proof about a TCP service: ICMP may be filtered and the application can fail independently.

## 7.2. IPv4 addresses, subnets, masks, CIDR, and NAT

1. **[Basic] What does an IPv4 address represent?**

   **Answer.** It is a 32-bit address assigned to a network interface/logical endpoint, written as four decimal octets. A prefix length divides it into network and host portions for routing, but the same address can be reachable differently in separate routing domains. An address identifies a location/interface, not permanently a person or process.

2. **[Basic] How do a subnet mask and CIDR prefix work?**

   **Answer.** A prefix `/n` means the first `n` bits select the network. `/24` corresponds to mask `255.255.255.0`; bitwise `address & mask` gives the network prefix. CIDR supports arbitrary prefix lengths and route aggregation, replacing old classful assumptions.

3. **[Code] What range does `192.0.2.64/26` cover?**

   **Answer.** `/26` leaves six host bits, so the block has 64 addresses: `192.0.2.64` through `192.0.2.127`. In a traditional subnet, `.64` is the network address, `.127` the directed broadcast, and `.65`–`.126` usable host addresses. Point-to-point `/31` and host `/32` routes have special semantics, so “subtract two” is not universal.

4. **[Basic] Which IPv4 ranges are commonly non-public?**

   **Answer.** Private-use ranges are `10.0.0.0/8`, `172.16.0.0/12`, and `192.168.0.0/16`. Loopback is `127.0.0.0/8`; link-local is `169.254.0.0/16`. Documentation examples should use reserved ranges such as `192.0.2.0/24`, not random public addresses. Private addresses are not automatically secure.

5. **[Deep dive] How does a host choose a route?**

   **Answer.** It selects the matching route with the longest prefix, then considers policy/metrics according to the OS routing system. A directly connected destination is resolved on-link; otherwise the packet goes to a next-hop router. The source address is selected based on interface, route, and policy. Use `ip route get ADDRESS` on Linux to inspect the actual decision.

6. **[Basic] What is NAT, and what is PAT/NAPT?**

   **Answer.** Network Address Translation rewrites IP address information across a boundary. The common home/enterprise form also translates transport ports so many private endpoints share one public address; this is PAT/NAPT. The translator keeps flow state and recomputes checksums. NAT conserves IPv4 addresses but breaks end-to-end reachability and complicates inbound connections, peer-to-peer protocols, and diagnostics.

7. **[Deep dive] Is NAT a firewall?**

   **Answer.** Not inherently. Stateful outbound NAT often incidentally drops unsolicited inbound packets because no mapping exists, but security policy should be expressed by an actual firewall/ACL. Port forwarding creates mappings, and compromised internal hosts still communicate outward. Treat NAT and filtering as separate responsibilities.

## 7.3. Berkeley sockets

1. **[Basic] What is a socket?**

   **Answer.** It is an OS communication endpoint represented on Unix by a file descriptor. `socket(domain, type, protocol)` chooses an address family such as `AF_INET`/`AF_INET6` and semantics such as `SOCK_STREAM` or `SOCK_DGRAM`. The descriptor is an owned resource and should be closed through RAII.

2. **[Basic] What is the typical TCP server call sequence?**

   **Answer.** `socket` creates the listening endpoint; `bind` assigns a local address/port; `listen` marks it passive and establishes a pending-connection queue; `accept` returns a new connected socket for one client. The listening socket remains separate and continues accepting. Each call can fail and may block unless configured otherwise.

3. **[Basic] What is the typical TCP client sequence?**

   **Answer.** Create a socket, resolve/select a destination address, and call `connect`. The kernel may choose a source address and ephemeral port automatically. For a blocking socket, successful return means the transport connection is established, not that the application request has succeeded or that TLS is complete.

4. **[Deep dive] What does `bind` to `0.0.0.0` mean, and why is it security-relevant?**

   **Answer.** It requests all suitable local IPv4 interfaces, making the service reachable according to routing/firewall rules rather than loopback only. Binding to `127.0.0.1` limits ordinary remote reachability to the host. Container/network namespaces and IPv6 dual-stack behavior complicate exposure; verify actual listening addresses with tools such as `ss -lntp`.

5. **[Deep dive] What does the `listen` backlog represent?**

   **Answer.** It is a hint/limit associated with pending connections waiting for acceptance, interpreted and capped by the OS. Linux separates partially and fully established queues internally, and tuning involves more than the application argument. A large backlog cannot compensate for an application that accepts/processes too slowly or for resource exhaustion.

6. **[Code] Why must stream-socket code loop around `send` and `recv`?**

   **Answer.** `send` may accept fewer bytes than requested; `recv` may return any positive number currently available, `0` for orderly peer shutdown, or an error. TCP has no message boundaries, so an application must buffer and parse a framing protocol. Handle interruption and nonblocking `EAGAIN`/`EWOULDBLOCK` according to the event-loop design.

7. **[Deep dive] How do `close` and `shutdown` differ?**

   **Answer.** `shutdown` disables reads, writes, or both on the socket's connection and can send a TCP FIN while keeping the descriptor for the other direction. `close` releases that descriptor; the underlying socket closes only when no references remain, and buffered transmission/linger rules apply. Half-close is useful for protocols where end-of-request is signaled by EOF while a response still follows.

## 7.4. TCP: connection establishment, sliding windows, and byte streams

1. **[Basic] How is a TCP connection established?**

   **Answer.** The three-way handshake exchanges `SYN`, `SYN-ACK`, and `ACK`, synchronizing initial sequence numbers and negotiating options such as MSS, window scaling, and selective acknowledgements. It confirms bidirectional reachability at that moment. Application data may follow according to stack/protocol features, but the conceptual handshake remains.

   ```mermaid
   sequenceDiagram
       participant C as Client
       participant S as Server
       C->>S: SYN, seq=x
       S->>C: SYN-ACK, seq=y, ack=x+1
       C->>S: ACK, ack=y+1
       Note over C,S: established byte stream
   ```

2. **[Basic] What does it mean that TCP is a byte stream?**

   **Answer.** TCP delivers an ordered sequence of bytes, not the sender's write calls. One `send` can arrive through several `recv`s, and several sends can be combined in one receive. The application must define framing—fixed sizes, delimiters with escaping, or length prefixes—and defend against invalid or excessive lengths.

3. **[Basic] How does TCP provide reliable ordered delivery?**

   **Answer.** Bytes are sequence-numbered; receivers acknowledge progress, checksums detect corruption in transit, and missing data is retransmitted based on timers/duplicate acknowledgements. Out-of-order segments are held or discarded according to implementation and delivered to the application in order. “Reliable” does not mean infinite retry or proof the remote application processed the data.

4. **[Deep dive] What is the sliding window?**

   **Answer.** The receiver advertises available buffer space (receive window), allowing multiple bytes/segments in flight before acknowledgement. As acknowledgements advance, the sender's permitted range slides forward. Window scaling supports large bandwidth-delay products. A zero window applies backpressure while TCP probes for reopening.

5. **[Deep dive] How do flow control and congestion control differ?**

   **Answer.** Flow control protects the receiving endpoint from buffer overflow using the advertised receive window. Congestion control protects the network by limiting the congestion window based on loss/latency/algorithm signals. The sender can transmit only within both limits. Algorithms such as CUBIC or BBR are implementation choices, not part of the application protocol.

6. **[Deep dive] What is TCP head-of-line blocking?**

   **Answer.** Later bytes cannot be delivered to the application until a missing earlier byte is recovered, even if later segments arrived. Multiplexing independent logical requests over one TCP stream can therefore couple their latency. HTTP/2 has stream multiplexing at the application layer but still encounters transport-level loss blocking; QUIC addresses streams over UDP with different recovery behavior.

7. **[Deep dive] Why does `TIME_WAIT` exist?**

   **Answer.** The endpoint performing the active close commonly remains in `TIME_WAIT` long enough for delayed duplicate segments to expire and to retransmit the final ACK if necessary. Many short connections can create numerous entries, but blindly bypassing the state risks protocol correctness. Connection reuse/pooling and correct server design are preferable to unsafe tuning.

## 7.5. UDP datagrams versus TCP

1. **[Basic] What service does UDP provide?**

   **Answer.** UDP sends independent datagrams identified by source/destination addresses and ports, with a checksum but no connection handshake, retransmission, ordering, congestion control, or duplicate suppression supplied to the application. A receive returns one datagram (possibly truncated if the buffer is too small), preserving message boundaries.

2. **[Basic] When is UDP appropriate?**

   **Answer.** It suits request/response protocols with small bounded messages, real-time media that prefers loss over delayed retransmission, discovery, telemetry, and custom transports such as QUIC. The application/protocol must add any required authentication, retry, ordering, congestion control, path/MTU handling, and session semantics.

3. **[Deep dive] Why should large UDP datagrams be avoided?**

   **Answer.** They may exceed the path MTU and be fragmented at IP or rejected. Losing one fragment loses the entire datagram, middleboxes may drop fragments, and reassembly consumes resources. Protocols commonly keep datagrams below a conservative path size or implement segmentation and path-MTU logic themselves.

4. **[Deep dive] What does `connect` mean for a UDP socket?**

   **Answer.** It records a default peer and lets code use `send`/`recv`; the kernel filters incoming datagrams to that peer and can report some asynchronous network errors more directly. It does not perform a handshake or create a reliable connection. A peer can still be absent when `connect` succeeds.

5. **[Design] How should an application choose TCP versus UDP?**

   **Answer.** Choose required semantics, not perceived speed. TCP supplies a mature reliable ordered stream with congestion control; UDP supplies datagram boundaries and protocol freedom at the cost of implementing every missing property correctly. If the need is reliable multiplexed streams with modern transport behavior, an established QUIC library is safer than designing a custom reliable-UDP protocol.

## 7.6. DNS resolution, `nslookup`, and `dig`

1. **[Basic] What does DNS do?**

   **Answer.** The Domain Name System is a distributed hierarchical database that maps names to typed records, including addresses and service metadata. An application usually asks a stub resolver, which consults local configuration/cache and sends the query to a recursive resolver. DNS answers are cached according to TTL and can contain referrals or aliases.

2. **[Deep dive] How do recursive and iterative resolution differ?**

   **Answer.** In a recursive query, the resolver promises a final answer or error and performs downstream work for the client. During iterative resolution, a resolver follows referrals from root to top-level-domain and authoritative servers until it obtains the answer. Enterprise/public recursive resolvers cache this work, so clients rarely query the hierarchy directly.

3. **[Basic] What are common DNS record types?**

   **Answer.** `A` maps to IPv4, `AAAA` to IPv6, `CNAME` aliases one name, `MX` identifies mail exchangers, `NS` delegates/identifies authoritative name servers, `TXT` carries text-based policy/verification data, `PTR` supports reverse lookup, and `SOA` describes zone authority/timers. `SRV` describes service targets/ports for protocols that use it.

4. **[Deep dive] What does “DNS propagation” really mean?**

   **Answer.** Authoritative changes can be immediate on the serving system, but caches continue using old positive or negative answers until TTL-related expiry. Multiple authoritative servers also need correct zone update/replication. Lower TTL before a planned migration, but remember resolver minimums, application caches, and clients already connected to old addresses.

5. **[Code] How are `dig` and `nslookup` used for basic diagnosis?**

   **Answer.** `dig example.com A`, `dig example.com AAAA`, and `dig @resolver address type` show answer, authority/additional sections, flags, TTL, and responding server; `+trace` follows delegation from the root (subject to network policy). `nslookup` provides simpler interactive/noninteractive queries. Compare the configured recursive resolver with an authoritative server and inspect `/etc/resolv.conf`/system resolver state before concluding DNS is wrong.

6. **[Deep dive] Does ordinary DNS authenticate answers?**

   **Answer.** Traditional DNS does not provide end-to-end authenticity or confidentiality. DNSSEC validates signed data/delegation when correctly deployed but does not encrypt queries. DNS over TLS/HTTPS encrypts transport to a chosen resolver but shifts trust to that resolver and does not replace TLS certificate validation for application endpoints.

## 7.7. HTTP, HTTPS, versions, and TLS

1. **[Basic] What are the main parts of an HTTP request and response?**

   **Answer.** A request has a method, target, HTTP version/protocol framing, headers, and optional body. A response has a status code/reason metadata, headers, and optional body. HTTP semantics are defined independently of the wire version; HTTP/2 and HTTP/3 use binary framing rather than HTTP/1.1 text lines.

2. **[Basic] What are common HTTP methods, and what do safe and idempotent mean?**

   **Answer.** `GET` retrieves, `HEAD` retrieves metadata, `POST` submits processing, `PUT` replaces/creates a resource representation, `PATCH` applies a partial change, `DELETE` requests removal, and `OPTIONS` describes capabilities. Safe methods are intended not to change server state. Repeating an idempotent method has the same intended effect as one application, though logs/metrics still change; `PUT` and `DELETE` are idempotent by semantics, `POST` usually is not unless the API adds idempotency.

3. **[Basic] What do HTTP status-code classes mean?**

   **Answer.** `1xx` is informational, `2xx` success, `3xx` redirection, `4xx` client/request-related failure, and `5xx` server failure. Important distinctions include `200` success, `201` created, `204` no content, `301/308` permanent redirects, `302/307` temporary redirects, `400` malformed request, `401` unauthenticated, `403` forbidden, `404` not found, `409` conflict, `429` rate limited, `500` internal error, and `503` unavailable.

4. **[Deep dive] What roles do HTTP headers play?**

   **Answer.** They carry representation metadata (`Content-Type`, `Content-Encoding`), negotiation (`Accept`), authentication, caching, conditional requests (`ETag`, `If-None-Match`), routing/authority (`Host`/`:authority`), cookies, tracing, and connection/proxy information. Header names are case-insensitive by HTTP semantics, but values have header-specific grammar. Treat limits and untrusted parsing as security concerns.

5. **[Deep dive] How do HTTP/1.1, HTTP/2, and HTTP/3 differ?**

   **Answer.** HTTP/1.1 uses textual messages over TCP and persistent connections but pipelining is rarely relied upon; clients use several connections. HTTP/2 uses binary frames and multiplexed streams over one TCP connection with header compression, but TCP loss can delay all streams. HTTP/3 maps HTTP semantics onto QUIC over UDP, providing stream-level loss independence and integrated modern TLS, at greater protocol complexity.

6. **[Basic] What does HTTPS add?**

   **Answer.** HTTPS is HTTP over a TLS-protected transport. TLS authenticates the server through certificate validation (and optionally the client), negotiates cryptographic parameters/keys, and protects confidentiality/integrity in transit. It does not make the server trustworthy, validate application authorization, or protect data after either endpoint processes it.

7. **[Deep dive] What happens during a high-level TLS handshake?**

   **Answer.** Client and server negotiate a version/parameters; the server presents a certificate chain and proves possession of its private key; the client validates hostname, chain, time, usage, and trust anchors; an ephemeral key agreement derives session keys; finished messages authenticate the transcript. TLS 1.3 reduces round trips and removes many legacy choices. Session resumption can avoid much repeated work.

8. **[Deep dive] How does HTTP caching work?**

   **Answer.** `Cache-Control` directives and freshness metadata determine whether a private/shared cache may reuse a response. Validators such as `ETag` or `Last-Modified` enable conditional requests and `304 Not Modified`. `Vary` identifies request headers that select representations. Incorrect caching can leak personalized data or serve stale state, so define cacheability deliberately.

9. **[Deep dive] Why is unambiguous message framing security-critical in HTTP/1.1?**

   **Answer.** Intermediaries and origin servers must agree where one request ends. Conflicting or differently parsed `Content-Length` and `Transfer-Encoding`, invalid whitespace, or duplicated headers can produce request smuggling/desynchronization. Use hardened parsers, reject ambiguity, normalize carefully, and keep proxy/server interpretation aligned.

## 7.8. WebSocket

1. **[Basic] What problem does WebSocket solve?**

   **Answer.** It provides a long-lived, full-duplex, message-framed channel between client and server, commonly initiated through an HTTP upgrade (or extended CONNECT in newer HTTP versions). Either side can send without a new request/response exchange, making it suitable for interactive updates, collaboration, and real-time control messages.

2. **[Basic] How is a WebSocket connection established over HTTP/1.1?**

   **Answer.** The client sends an authenticated HTTP request with `Upgrade: websocket`, `Connection: Upgrade`, a random `Sec-WebSocket-Key`, and a version. A supporting server returns `101 Switching Protocols` and a derived accept value, preventing certain cache/proxy confusion. With `wss`, TLS is established before the HTTP upgrade.

3. **[Deep dive] What does WebSocket framing provide?**

   **Answer.** It preserves text/binary message semantics over frames, supports fragmentation, close frames, and ping/pong control frames. Client-to-server frames are masked; server frames are not, which is not encryption. Applications still need schema validation, message-size limits, authentication/authorization, and versioning.

4. **[Design] How should backpressure and liveness be handled?**

   **Answer.** Bound each connection's outbound queue; a slow client must not consume unlimited memory. Choose whether to drop replaceable updates, disconnect, or apply upstream pressure. Use protocol ping/pong or application heartbeats with deadlines to detect dead peers, while distinguishing temporary latency from failure. Reconnection needs resynchronization/resume semantics.

5. **[Deep dive] Which browser security issue is specific to WebSocket handshakes?**

   **Answer.** Browsers include an `Origin` header, but WebSocket is not governed by CORS in exactly the same way as fetch. Servers should validate allowed origins for browser clients, authenticate the user, authorize subscriptions/actions, and protect cookie-authenticated endpoints against cross-site WebSocket hijacking. TLS alone does not establish application authorization.

## 7.9. I/O multiplexing: `select`, `poll`, and `epoll`

1. **[Basic] What problem does I/O multiplexing solve?**

   **Answer.** One thread can wait for readiness events from many descriptors rather than blocking on one or creating a thread per connection. Readiness means an operation is likely to proceed without blocking, not that a complete application message is available. Descriptors are normally configured nonblocking and processed in bounded work per event.

2. **[Basic] How does `select` work, and what are its limitations?**

   **Answer.** The caller supplies bit sets of descriptors and a maximum descriptor number; the kernel modifies sets to report readiness. Sets must be rebuilt, scanning cost grows with the descriptor range, and `FD_SETSIZE` limits representable descriptors in common implementations. It remains portable and adequate for small sets.

3. **[Basic] How does `poll` differ from `select`?**

   **Answer.** `poll` takes an array of descriptor/event structures without `select`'s bit-set descriptor limit and reports readiness in `revents`. The kernel/caller still scans an O(number of registered descriptors) array each call, and the array is transferred repeatedly. It is simpler for sparse high-numbered descriptors but not ideal for huge mostly idle sets.

4. **[Basic] What does Linux `epoll` change?**

   **Answer.** Applications register an interest set in a kernel object using `epoll_ctl`; `epoll_wait` returns ready events, so work is closer to O(number of events) rather than rescanning every descriptor. It scales well for many mostly idle connections but has Linux-specific semantics involving duplicate file descriptions, close/reuse, edge triggering, and one-shot modes that must be understood.

5. **[Deep dive] How do level-triggered and edge-triggered modes differ?**

   **Answer.** Level-triggered mode continues reporting while the condition remains ready and is easier to use. Edge-triggered mode reports transitions; handlers must use nonblocking I/O and drain until `EAGAIN`, or data may remain unread without another notification. Edge triggering can reduce repeated notifications but is not automatically faster.

6. **[Design] What else must a production event loop manage?**

   **Answer.** It needs timers/deadlines, cancellation, accept loops, partial buffers, fair per-connection work, outbound backpressure, error/hangup handling, descriptor ownership, cross-thread wakeups, and safe close/reuse. Slow parsing or callbacks must not block the loop; dispatch CPU work to a bounded executor while preserving connection state and lifetime.

## 7.10. Basic analysis with `tcpdump` and Wireshark

1. **[Basic] What do `tcpdump` and Wireshark provide?**

   **Answer.** They capture and decode packets visible at a network interface/capture point. `tcpdump` is command-line friendly and uses capture/display filters; Wireshark provides rich protocol dissection, stream reconstruction, statistics, and a GUI. Both require appropriate privileges and careful handling of sensitive captured data.

2. **[Code] Give useful `tcpdump` examples.**

   **Answer.** `tcpdump -ni any host 192.0.2.10`, `tcpdump -ni eth0 'tcp port 443'`, and `tcpdump -ni eth0 -w capture.pcap 'host 192.0.2.10 and (tcp or icmp)'` avoid reverse lookup, filter traffic, and save a file for Wireshark. Add `-s 0` for full packets when needed and `-c N` to bound capture size. Quote filters so the shell does not interpret them.

3. **[Deep dive] Why might a local packet capture show invalid checksums or oversized segments?**

   **Answer.** Checksum, segmentation, and receive offloading can mean capture occurs before the NIC fills checksums or after the kernel aggregates packets. The wire traffic may be correct even though a host-side capture looks unusual. Capture on another machine/port or account for offload settings before diagnosing corruption.

4. **[Deep dive] What can a TCP trace reveal?**

   **Answer.** It can show handshake timing, retransmissions, duplicate ACKs, resets, zero windows, advertised MSS/window scale, round-trip estimates, teardown, and application request timing when payload is visible. Packet loss at the capture point can imitate network loss, and retransmission labels are analyzer inferences. Correlate both endpoints and application logs when possible.

5. **[Deep dive] What remains visible when traffic uses TLS?**

   **Answer.** IP addresses, ports, timing, packet sizes, direction, TCP/QUIC behavior, and some handshake metadata remain visible; application payload is encrypted. Names may be inferred from DNS or TLS SNI when not protected by newer mechanisms. Decryption requires session keys/authorized endpoint instrumentation and must respect security/privacy policy.

---

# 8. Toolchain, binaries, debugging, and profiling

## 8.1. Compilation, linking, and static/dynamic libraries

1. **[Basic] What are the major stages from C++ source to an executable?**

   **Answer.** Preprocessing expands includes/macros and selects conditional code, compilation parses/type-checks/optimizes a translation unit, assembly produces an object file, and linking resolves symbols/relocations across objects and libraries to create an executable/shared object. Build drivers often combine commands, but stage boundaries explain many diagnostics.

2. **[Basic] What does a linker resolve?**

   **Answer.** Object files contain defined and undefined symbols plus relocation records for addresses not yet known. The linker selects definitions from objects/archives, lays out code/data, resolves references, applies static relocations, and emits metadata for any remaining dynamic relocations. Missing definitions produce undefined-reference errors; conflicting strong definitions produce multiple-definition errors.

3. **[Basic] How does static linking work?**

   **Answer.** Needed object modules from static archives are copied into the final executable at link time. Deployment has fewer runtime library dependencies and behavior is insulated from shared-library replacement, but binaries are larger, security fixes require relinking/redeployment, and some system/licensing/plugin constraints remain. Archive extraction and command-line order matter on traditional Unix linkers.

4. **[Basic] How does dynamic linking work?**

   **Answer.** The executable records dependencies on shared libraries; the dynamic loader maps them, resolves dynamic symbols/relocations (eagerly or lazily), runs initialization, then transfers control. Code pages can be shared across processes and libraries can be updated independently if ABI remains compatible. Startup work, deployment search paths, symbol interposition, and version mismatch add complexity.

5. **[Deep dive] What are PIC, the GOT, and the PLT?**

   **Answer.** Position-independent code computes addresses in a relocatable way so shared code can map at different virtual addresses without rewriting most instructions. On common ELF ABIs, the Global Offset Table holds addresses for data/symbol references and the Procedure Linkage Table supports external function dispatch/lazy binding. Exact mechanisms are architecture and linker dependent.

6. **[Deep dive] How are runtime shared libraries found on Linux?**

   **Answer.** The dynamic loader uses ELF dependency names plus configured search rules involving `DT_RPATH`/`DT_RUNPATH`, `LD_LIBRARY_PATH`, loader cache, and default directories, with security restrictions for privileged binaries. Inspect with `readelf -d` and loader diagnostics. Prefer controlled install paths and `$ORIGIN`-relative runpaths where appropriate; avoid relying on the current directory.

7. **[Code] Why can static-library order cause an undefined reference?**

   **Answer.** Traditional linkers scan archives and extract a member only when it satisfies an unresolved symbol known at that point. Therefore objects/libraries that need symbols normally precede the archive providing them: `... consumer.o -lprovider`. Circular archive dependencies may require grouping options or, better, restructuring. Build systems should model target dependencies rather than manually accumulating flags.

## 8.2. ELF basics

1. **[Basic] What is ELF?**

   **Answer.** Executable and Linkable Format is the common binary format for Linux/Unix object files, executables, shared libraries, and core dumps. It defines headers, sections, segments, symbols, relocations, and dynamic-loading metadata. Specific ABI details depend on architecture, OS, and toolchain.

2. **[Basic] How do ELF sections differ from program segments?**

   **Answer.** Sections organize link-time information such as `.text`, `.rodata`, `.data`, `.bss`, symbol tables, relocations, and debug data. Program headers describe runtime segments the loader maps, with file/memory sizes and permissions. Several sections can be combined into one loadable segment; stripped executables can run without a section-header table in some circumstances because loading uses program headers.

3. **[Basic] What are `.text`, `.rodata`, `.data`, and `.bss`?**

   **Answer.** `.text` commonly contains executable code, `.rodata` read-only constants, `.data` initialized writable static storage, and `.bss` zero-initialized static storage represented mainly by a memory-size requirement rather than stored zero bytes. Names/layout are conventions, not direct C++ standard concepts.

4. **[Deep dive] What information is in ELF symbol and relocation tables?**

   **Answer.** Symbols describe names, bindings (local/global/weak), types, visibility, defining section, and value/size. Relocations tell a linker/loader how to adjust code/data when final addresses become known. Dynamic symbol tables contain the subset relevant to runtime linking; the full static symbol table may be stripped.

5. **[Code] Which tools inspect an ELF binary?**

   **Answer.** `file` identifies format/architecture; `readelf -h -l -S -s -r -d` inspects headers, segments, sections, symbols, relocations, and dynamic tags; `objdump -d -C` disassembles/demangles; `nm -C` lists symbols; `strings` is only a rough clue. `ldd` shows runtime dependencies but should not be used casually on untrusted binaries because some historical/implementation behaviors can execute code; use `readelf`/safe loader inspection.

6. **[Deep dive] What does stripping a binary remove, and how can debugging still work?**

   **Answer.** Stripping removes some symbol/debug/relocation information not required for execution, reducing artifact size and making symbolic diagnosis harder. Production builds can store separate debug files indexed by build ID and keep minimal dynamic symbols/unwind data. Preserve exact binaries and matching debug information for reliable core-dump analysis.

## 8.3. `gdb`: backtraces, breakpoints, and watchpoints

1. **[Basic] How should code be built for useful GDB debugging?**

   **Answer.** Add debug information (`-g`, often DWARF) and retain matching source/binaries. `-O0` makes stepping/local variables easiest but may hide optimization-only bugs; `-Og` is a useful compromise, and production crashes should ultimately be debugged with the actual optimized build plus symbols. Frame pointers/unwind tables can improve traces.

2. **[Basic] How are breakpoints used?**

   **Answer.** `break function`, `break file.cpp:line`, and `break *address` stop execution; `condition N expression` makes a breakpoint conditional, and `commands N` automates actions. Use `run`, `continue`, `next`, `step`, `finish`, and `until` to control execution. Optimized code can map several source lines to one instruction or eliminate a function.

3. **[Basic] How do you inspect a crash backtrace?**

   **Answer.** `bt` shows the current thread's frames; `bt full` includes available locals. Use `frame N`, `up`, `down`, `info args`, `info locals`, `print expression`, and `x` to inspect state/memory. A top frame in libc is often only where corruption was detected; walk to application callers and consider earlier memory damage.

4. **[Deep dive] What is a watchpoint?**

   **Answer.** `watch expr` stops when a value is written/changes; `rwatch` and `awatch` can stop on read or any access where supported. Hardware watchpoints are precise but limited in number/size; software watchpoints are much slower. They are excellent for discovering who corrupts a specific address after the address is known and stable.

5. **[Code] How do you debug multiple threads or attach to a running process?**

   **Answer.** Use `gdb -p PID`/`attach PID`, then `info threads`, `thread N`, and `thread apply all bt`. Scheduler-locking settings can control whether other threads run while stepping. Attaching pauses the process and requires permission; in production prefer a core dump or coordinated diagnostic window when a pause is unacceptable.

6. **[Deep dive] What is needed to analyze a core dump?**

   **Answer.** Load the exact executable and core (`gdb executable core`) plus matching shared libraries and separate debug symbols. ASLR addresses are reconstructed from the core mappings. Mismatched binaries create misleading stacks/types. Configure system core limits/collection in advance and treat dumps as sensitive because they can contain credentials and user data.

## 8.4. Profiling with `perf`, Valgrind, and related tools

1. **[Basic] What is profiling for?**

   **Answer.** Profiling identifies where time, CPU cycles, allocations, cache misses, blocking, or other resources are actually spent under a representative workload. It tests a performance hypothesis and guides optimization toward bottlenecks. A profiler does not replace an end-to-end benchmark with latency/throughput requirements.

2. **[Deep dive] How do sampling and instrumentation profilers differ?**

   **Answer.** Sampling periodically records instruction/call-stack state, giving low-overhead statistical attribution suitable for production-like loads. Instrumentation records function/event entry/exit or rewrites execution, offering detailed counts/timing at higher overhead and perturbation. Very short functions and blocked/off-CPU time need appropriate sampling frequency/events and good unwind data.

3. **[Code] What are common Linux `perf` workflows?**

   **Answer.** `perf stat -- program` summarizes counters/time; `perf record -g -- program` samples with call graphs; `perf report` explores hotspots; `perf top` observes a live system. `perf annotate`, flame-graph tooling, tracepoints, and `perf sched` answer deeper questions. Permissions, debug symbols, frame pointers/DWARF unwinding, virtualization, and multiplexed counters affect accuracy.

4. **[Basic] What does Valgrind provide?**

   **Answer.** Valgrind executes code under a dynamic instrumentation framework. Memcheck detects many invalid reads/writes, use of uninitialized values, and leaks; Callgrind profiles call costs; Cachegrind simulates cache/branch behavior; Helgrind/DRD find some threading issues. Slowdown can be large and instruction-set/platform support varies, so it is primarily a test/debug tool.

5. **[Deep dive] How do sanitizers compare with Valgrind?**

   **Answer.** Compiler sanitizers instrument a rebuilt program: AddressSanitizer finds many memory errors, UndefinedBehaviorSanitizer checks selected UB, ThreadSanitizer detects data races, and LeakSanitizer finds leaks. They are usually faster and understand compiler semantics but require compatible builds and have their own blind spots. Run several configurations in CI; do not combine incompatible sanitizers blindly.

6. **[Design] What makes a trustworthy performance experiment?**

   **Answer.** Define the metric and representative input; use release optimization; warm up relevant caches/JIT-like state; run enough repetitions; report distributions/tail latency; control CPU frequency, affinity, background load, NUMA, and I/O cache state as relevant. Change one factor, keep correctness tests, and compare end-to-end impact rather than only a microbenchmark.

7. **[Deep dive] Why is optimizing the hottest function not always the best action?**

   **Answer.** It may be inherently proportional to useful work, already near hardware limits, or hot only because an upstream design calls it too often. Blocking/queueing can dominate wall time while consuming little CPU. Apply Amdahl's law, inspect call paths and off-CPU time, eliminate unnecessary work/data movement first, and re-profile after every change.

---

# 9. Performance engineering

## 9.1. Latency, throughput, and percentiles

1. **[Basic] How do latency and throughput differ?**

   **Answer.** Latency is the time one operation takes, usually measured as a distribution from request arrival to completion. Throughput is the amount of work completed per unit time. They interact but are not interchangeable: batching may improve throughput while delaying individual work, and adding concurrency may increase throughput until contention/queueing raises latency sharply. A performance requirement should name the workload, concurrency, percentile, and measurement boundary.

2. **[Deep dive] Why do systems often show a “latency knee” near saturation?**

   **Answer.** As offered load approaches service capacity, small bursts or service-time variation create queues faster than they can drain. Utilization may increase only slightly while queueing delay grows dramatically. Retries and timeouts can add more work, producing a feedback loop. Capacity plans therefore need headroom and overload control rather than assuming that a resource operating at 100% remains responsive.

   ```mermaid
   flowchart LR
       A[offered load] --> B[work queue]
       B --> C[finite service capacity]
       C --> D[completed throughput]
       B --> E[queueing delay]
       E --> F[timeouts and retries]
       F -->|extra load| A
   ```

3. **[Basic] What do p50, p95, and p99 mean?**

   **Answer.** A p99 latency of 80 ms means 99% of recorded observations were at or below 80 ms and 1% were above it; p50 is the median. Percentiles expose tail behavior hidden by the mean, but they do not identify the worst case or explain the distribution. Report the sample window, population, units, and traffic mix. Do not average independently calculated percentiles across hosts; aggregate suitable histograms or raw observations.

4. **[Deep dive] Why are averages insufficient for latency-sensitive services?**

   **Answer.** A small fraction of very slow operations can be invisible in the average yet dominate user experience, deadlines, resource retention, and fan-out requests. In a request that waits for many downstream calls, the probability that at least one is in the tail increases with fan-out. Keep means for capacity/cost analysis, but pair them with percentiles, maximums under a defined window, error rates, and traces that explain outliers.

5. **[Deep dive] What are common latency-measurement errors?**

   **Answer.** Measuring only service time while excluding queue time, using a non-monotonic wall clock for durations, omitting timed-out requests, warming caches in a way production does not, and generating the next request only after the previous completes can all bias results. The last issue can create coordinated omission: the load generator stops sampling during stalls. Use a monotonic clock, a representative arrival process, bounded warm-up, full outcome accounting, and a histogram with adequate range/resolution.

6. **[Design] How should a performance investigation be structured?**

   **Answer.** State a falsifiable symptom and business metric; reproduce or collect production evidence; split end-to-end latency into queueing, CPU, I/O, locks, allocation, and downstream time; profile the relevant resource; change one factor; then remeasure correctness and the original metric. Preserve workload and environment metadata. A faster microbenchmark is not a successful optimization if end-to-end performance, memory, reliability, or maintainability regresses.

## 9.2. Batching, coalescing, throttling, and backpressure

1. **[Basic] What is batching, and what tradeoff does it make?**

   **Answer.** Batching processes several logical items in one operation, amortizing fixed costs such as syscalls, locks, network headers, transactions, and device submissions. It often improves throughput and CPU efficiency but makes early items wait while the batch fills, increases temporary memory, and creates larger failure/retry units. Production batching normally needs both a maximum item/byte count and a maximum wait time.

2. **[Basic] What is request or event coalescing?**

   **Answer.** Coalescing combines redundant or superseded work rather than merely executing all work together. Concurrent cache misses for the same key can share one fetch; repeated “set current value” updates can collapse to the latest value. It is safe only when the operation's semantics permit merging—commands such as “increment” or audit events usually cannot be discarded. Cancellation, errors, and per-caller deadlines still need defined behavior.

3. **[Basic] What is throttling?**

   **Answer.** Throttling limits admission or execution rate/concurrency to protect a resource, enforce quotas, or shape traffic. Common mechanisms include token buckets, leaky buckets, semaphores, and per-tenant concurrency limits. A good design defines burst allowance, fairness, rejection versus delay, retry guidance, and metrics. A single global limit can allow one noisy tenant to starve others.

4. **[Basic] What is backpressure?**

   **Answer.** Backpressure lets a slower downstream stage communicate limited capacity upstream so work is slowed, rejected, sampled, or shed instead of accumulating without bound. In a synchronous path it may be blocking or an explicit “busy” result; in an asynchronous stream it may be credits/demand, bounded queues, or flow-control windows. It is an end-to-end policy: merely moving an unbounded queue to another component does not solve overload.

5. **[Deep dive] Why is a bounded queue part of correctness, not only performance?**

   **Answer.** An unbounded queue turns sustained overload into unbounded memory and ever-growing delay; by the time memory is exhausted, queued work may already be useless because deadlines expired. A bound makes overload behavior explicit. When full, the system must block, reject, drop according to priority, spill to a durable store, or reduce upstream demand. The chosen policy must preserve ordering, durability, and user-visible guarantees.

6. **[Design] How do batching, throttling, and backpressure fit together?**

   **Answer.** Batching improves service efficiency, throttling caps how much work is admitted or active, and backpressure propagates insufficient downstream capacity. A practical pipeline uses bounded queues, size/time-limited batches, per-stage concurrency limits, and deadline-aware load shedding. Monitor queue depth/age, batch fill ratio, rejection rate, service time, and end-to-end latency; otherwise a throughput optimization can silently become a latency outage.

## 9.3. Allocation cost and memory behavior

1. **[Basic] Why can dynamic allocation be expensive?**

   **Answer.** Allocation may search/update allocator metadata, synchronize between threads, request/commit pages, fault in memory, and reduce cache/TLB locality. Deallocation can contend and may not return memory to the OS. The indirect cost—pointer chasing, fragmentation, larger working set, and unpredictable latency—is often more important than the allocator call itself. Modern allocators make common small allocations fast, so measure the actual workload.

2. **[Deep dive] What are internal and external fragmentation?**

   **Answer.** Internal fragmentation is unused space inside allocated blocks due to size classes, alignment, or over-allocation. External fragmentation is free memory split into pieces that cannot satisfy a larger contiguous request, even if the total is sufficient. Process RSS may remain high after frees because pages contain other live blocks or stay cached by the allocator. Object pools reduce some fragmentation patterns but can retain excessive memory themselves.

3. **[Design] What techniques reduce allocation overhead in C++?**

   **Answer.** First remove unnecessary ownership and copies; reserve container capacity when a reliable bound is known; store small values contiguously; reuse buffers; batch objects with the same lifetime in arenas; and consider `std::pmr` to inject a suitable memory resource. Small-buffer optimization and pools help selected size/lifetime patterns. Each technique changes memory retention, exception/lifetime handling, and complexity, so profile allocations and peak/steady-state memory before and after.

4. **[Deep dive] When is an arena or monotonic allocator appropriate?**

   **Answer.** It is ideal when many objects share a phase/request lifetime: allocate cheaply by bumping a pointer and release the whole region at once. Individual deallocation is absent or ineffective, destructors may need explicit handling, and memory remains until the arena resets. It is a poor fit when a few objects must outlive the phase, sizes are adversarial, or independent prompt reclamation is required. References into a reset arena become invalid immediately.

5. **[Code] How do you prove that allocation is the bottleneck?**

   **Answer.** Collect allocation counts, bytes, lifetimes, call stacks, contention, and RSS/heap profiles under a representative load; correlate them with CPU and latency. Then run a controlled change such as reserving a known container or substituting a scoped memory resource and compare the original end-to-end metric. A large allocation count alone is not proof—calls may be cheap and another resource may dominate. Include peak memory and tail latency, not only average CPU time.

## 9.4. Diagnosing long-running and production-only failures

1. **[Design] How do you investigate a failure that appears only after several hours?**

   **Answer.** Make the system observable before waiting: timestamped structured logs with correlation IDs, bounded diagnostic buffers, resource/queue/thread metrics, crash/core-dump collection, build identifiers, and configuration snapshots. Look for variables correlated with time or work—memory, handles, queue age, counters, file descriptors, connections, cache size, clock transitions, and rare inputs. Accelerate safely with stress, smaller limits, deterministic seeds, and fault injection, while preserving evidence from the original environment.

2. **[Code] How do you distinguish a deadlock, livelock, starvation, and a slow dependency in a “hung” process?**

   **Answer.** Capture all thread stacks repeatedly. A stable wait-for cycle around locks suggests deadlock; changing stacks with no useful progress suggests livelock; one runnable/ready worker repeatedly losing access suggests starvation; many threads blocked in the same socket/file operation points to an external dependency or missing timeout. Add lock/queue telemetry, scheduler/off-CPU profiling, syscall tracing, and downstream health. One stack snapshot shows where threads are, not necessarily why they arrived there.

3. **[Deep dive] Why are core dumps and exact build artifacts important?**

   **Answer.** A core preserves memory mappings, registers, stacks, and much process state near failure, enabling offline inspection without keeping production paused. Useful analysis requires the exact executable, shared libraries, debug symbols, and preferably source/build metadata; mismatched artifacts produce plausible but wrong frames and variables. Dumps may contain credentials and user data, so collection, transfer, access, and retention need security controls.

4. **[Design] What diagnostic features should exist before a production incident?**

   **Answer.** Define stable metrics for latency, errors, saturation, queues, memory, handles, and restarts; structured rate-limited logs; distributed/request correlation; health and readiness signals; safe dynamic log levels; watchdog or hang-dump support; symbolized crash reporting; and a way to reproduce configuration/build provenance. Diagnostics themselves need bounded resource use and privacy rules. Retrofitting observability after a rare incident often means the decisive evidence is already gone.

---

# Assessment usage notes

- For a short screening, select 12–15 **[Basic]** questions across CPU, OS, concurrency, and networking, plus 2–3 **[Code]** questions.
- For a 60–90 minute interview, follow one scenario end to end—for example a slow Linux TCP service—from source and syscalls through scheduling, memory, packets, and profiling.
- Strong answers distinguish language guarantees, OS/ABI contracts, common implementations, and measurements on a particular machine.
- Ask candidates to draw byte layouts, state transitions, wait-for graphs, packet flows, or address-prefix calculations rather than only reciting definitions.
- Tool knowledge should include what evidence a command provides, what it cannot prove, and how observation changes the system.
