# C Language Technical Interview Questions and Answers

> Reusable bank for C-specific questions, kept separate from [C++ Core Questions](<./C++ Core Questions.md>) because the interesting parts of C are the things C++ took away: no RAII, no destructors, no overloading, and a preprocessor doing the work templates would do. Where a topic is shared - endianness, the build model, cache behaviour - the answer lives in the C++ bank and is not repeated here.

Answers follow the same shape as the other banks: a short answer that can be said in thirty to sixty seconds, then the detail a follow-up would reach for.

# Question Index

## Language and Standards (C-001–C-005)

|  |  |  |
|---|---|---|
| [C-001. What does "C89" actually mean, and what did C99, C11 and C17 add?](#question-c-001) | [C-002. What is undefined behaviour in C, and which cases must you know?](#question-c-002) | [C-003. `struct` vs `typedef struct`?](#question-c-003) |
| [C-004. What do `static`, `extern`, `const`, `volatile` and `restrict` mean in C?](#question-c-004) | [C-005. How does C handle errors without exceptions?](#question-c-005) |  |

## Memory and Data Layout (C-006–C-011)

|  |  |  |
|---|---|---|
| [C-006. What are a program's memory sections?](#question-c-006) | [C-007. `malloc`, `calloc`, `realloc`, `free` - what are the traps?](#question-c-007) | [C-008. How do alignment and padding work in a struct?](#question-c-008) |
| [C-009. What is a packed struct, and what does it cost?](#question-c-009) | [C-010. What is a flexible array member?](#question-c-010) | [C-011. What do you need to know about C strings?](#question-c-011) |

## Interfaces and Modularity (C-012–C-015)

|  |  |  |
|---|---|---|
| [C-012. What are function pointers used for in C?](#question-c-012) | [C-013. What is an opaque struct, and why is it the main encapsulation tool in C?](#question-c-013) | [C-014. What belongs in a header, and when do you forward-declare?](#question-c-014) |
| [C-015. What are the traps of the C preprocessor?](#question-c-015) |  |  |

## Build, Link and Load (C-016–C-018)

|  |  |  |
|---|---|---|
| [C-016. What are the stages from C source to a running program?](#question-c-016) | [C-017. Static vs shared libraries in C?](#question-c-017) | [C-018. How does a shared library get found and loaded, and how do you override one?](#question-c-018) |

## Debugging (C-019–C-020)

|  |  |  |
|---|---|---|
| [C-019. How do you debug a C crash from a core dump?](#question-c-019) | [C-020. Valgrind or sanitizers?](#question-c-020) |  |

# 1. Language and Standards

## Question C-001

[↑ Back to question index](#question-index)

### Question C-001 — What does "C89" actually mean, and what did C99, C11 and C17 add?

**Short answer**

- C89/C90 is the original ANSI/ISO standard: declarations at the start of a block, no `//` comments, no `inline`, no fixed-width integer types, and no boolean type.
- C99 added declarations anywhere, `//` comments, `long long`, `<stdint.h>` fixed-width types, `<stdbool.h>`, `inline`, designated initializers, compound literals, variable-length arrays and flexible array members.
- C11 added `_Static_assert`, `_Generic`, atomics, a threads API and `_Alignas`/`_Alignof`; C17 is a defect-fix release with no new features.

**Details and nuances**

The reason this question matters on a legacy codebase is not trivia. Working in C89 means the habits that feel natural in modern C are unavailable, and the code is shaped by their absence: a variable declared far from its first use because it had to go at the top of the block, `int` used where `int32_t` would say what is meant, integer flags instead of `bool`, and macros doing work that `inline` or `_Generic` would do now.

The practical trap is that a compiler will often accept a C99 construct in a project nominally on C89 unless `-std=c89 -pedantic` is set, so the code drifts and then fails on the one compiler that enforces it. Knowing which construct belongs to which standard is what stops that.

**Example or evidence boundary**

Production experience: two years on a decades-old platform where the core was C89, with strict backward-compatibility constraints, alongside Java and Scala services calling into it. See `2022-2024. Integrated Library System.md`.

[↑ Back to question index](#question-index)

---

## Question C-002

[↑ Back to question index](#question-index)

### Question C-002 — What is undefined behaviour in C, and which cases must you know?

**Short answer**

- Undefined behaviour is a construct the standard imposes no requirements on, so the compiler may assume it never happens - which means it optimizes on that assumption and the symptom can appear far from the cause.
- The ones to know by name: reading uninitialized memory, use after free, buffer overrun, signed integer overflow, null pointer dereference, strict-aliasing violations, data races, and modifying an object twice between sequence points.
- The dangerous property is not that it crashes but that it often does not: it works in debug, breaks under `-O2`, and looks like a compiler bug.

**Details and nuances**

**Signed overflow** is the one that surprises people. Because signed overflow is undefined, the compiler may assume `x + 1 > x` is always true and delete the overflow check you wrote. Unsigned overflow is defined - it wraps - which is why size arithmetic belongs in `size_t`.

**Strict aliasing** says two pointers of incompatible types do not refer to the same object, so the compiler may reorder loads and stores across them. The classic violation is reinterpreting a buffer through a differently-typed pointer; the standard-blessed routes are `memcpy` into a correctly-typed object, or a union, or `char*`, which may alias anything.

The practical posture on a legacy codebase: build with `-Wall -Wextra`, add `-fsanitize=undefined` in a test build, and treat any behaviour change between optimization levels as a UB report rather than a compiler problem.

[↑ Back to question index](#question-index)

---

## Question C-003

[↑ Back to question index](#question-index)

### Question C-003 — `struct` vs `typedef struct`?

**Short answer**

- In C, `struct Foo` declares a type in the separate struct tag namespace, so every use needs the `struct` keyword; a `typedef` creates an ordinary type name so `Foo` alone works.
- Both forms are fine; the choice is a house-style decision about whether the reader should see that a type is a struct.
- Keep the tag even when you typedef - `typedef struct Foo Foo;` - because without a tag the type cannot refer to itself, which rules out linked lists and trees.

**Details and nuances**

```c
struct Node { int value; struct Node* next; };   /* tag required inside */
typedef struct Node Node;                        /* now Node* works too */
```

The argument for typedef is brevity; the argument against is that it hides whether something is a struct, a pointer or a handle, which matters most when a library typedefs a pointer - `typedef struct Impl* Handle;` - and the caller cannot see that they are holding a pointer. That is a legitimate design choice for an opaque handle (see [C-013](#question-c-013)) and an unpleasant surprise anywhere else.

[↑ Back to question index](#question-index)

---

## Question C-004

[↑ Back to question index](#question-index)

### Question C-004 — What do `static`, `extern`, `const`, `volatile` and `restrict` mean in C?

**Short answer**

- `static` means two different things by position: at file scope it gives internal linkage, hiding the symbol from other translation units; inside a function it gives a local variable static storage duration, so it survives between calls.
- `extern` declares that a symbol is defined elsewhere; `const` means the object may not be modified through this name, which is weaker than "it is in read-only memory".
- `volatile` tells the compiler the object may change outside the program's control, so every access must actually happen; `restrict` promises a pointer is the only access path to its object, which enables optimization and is a promise the compiler cannot check.

**Details and nuances**

Two that get confused. **`volatile` is not a threading tool**: it prevents the compiler from eliding or reordering accesses, but it establishes no memory ordering and no atomicity, so it does not make a shared variable safe. It is for memory-mapped hardware registers, variables changed by a signal handler (`sig_atomic_t`), and `setjmp`/`longjmp` locals. Use C11 atomics for threads.

**`restrict` is a promise you make and pay for.** `memcpy` declares its pointers `restrict` and `memmove` does not - that is exactly the difference between them, and calling `memcpy` on overlapping regions is undefined behaviour rather than a bug in `memcpy`.

Worth adding if asked: C has no `mutable`, and `const` in C is weaker than in C++ - a `const` object at file scope still has external linkage, and casting `const` away and then writing is undefined behaviour.

[↑ Back to question index](#question-index)

---

## Question C-005

[↑ Back to question index](#question-index)

### Question C-005 — How does C handle errors without exceptions?

**Short answer**

- By return value, and the discipline is entirely the caller's: an integer status, a sentinel such as `NULL` or `-1`, or `errno` for library calls, with the actual result delivered through an out parameter.
- There is no stack unwinding, so every early return has to release what that function acquired - which is why the single-exit `goto cleanup` pattern exists and is good style in C rather than the sin it would be elsewhere.
- `errno` is only meaningful after a call that documents setting it, and only when that call has already indicated failure; reading it otherwise is meaningless.

**Details and nuances**

```c
int load(const char* path, Config* out) {
    int   rc  = -1;
    FILE* f   = NULL;
    char* buf = NULL;

    f = fopen(path, "rb");
    if (!f)   goto cleanup;
    buf = malloc(SIZE);
    if (!buf) goto cleanup;
    /* ... */
    rc = 0;

cleanup:
    free(buf);        /* free(NULL) is defined and does nothing */
    if (f) fclose(f);
    return rc;
}
```

This is the shape to draw when asked how you avoid leaks in C: one exit, resources released in reverse order of acquisition, and every failure path going through the same code. The reason C++ RAII feels like a large improvement is that it makes this structural rather than a convention that one careless early `return` can break.

`setjmp`/`longjmp` exists and is occasionally the right tool, but it does not run any cleanup, so anything acquired between the two is leaked. Mention it as something you know rather than as something you reach for.

[↑ Back to question index](#question-index)

---

# 2. Memory and Data Layout

## Question C-006

[↑ Back to question index](#question-index)

### Question C-006 — What are a program's memory sections?

**Short answer**

- `.text` holds executable code and is read-only; `.rodata` holds string literals and `const` data; `.data` holds initialized globals and statics; `.bss` holds zero-initialized ones and occupies no space in the binary.
- The heap grows upward under `malloc`, the stack grows downward per thread and holds frames, parameters and locals.
- The distinction that gets asked about is `.data` versus `.bss`: an initialized global is stored in the file, a zero-initialized one is only a size, which is why a large zeroed array costs nothing on disk.

**Details and nuances**

| Section | Contents | Written to the binary | Writable at run time |
|---|---|---|---|
| `.text` | Code | Yes | No |
| `.rodata` | String literals, `const` data | Yes | No |
| `.data` | Initialized globals and statics | Yes | Yes |
| `.bss` | Zero-initialized globals and statics | No, only a size | Yes |
| Heap | `malloc`/`free` | No | Yes |
| Stack | Frames, locals, parameters | No | Yes |

Two consequences worth naming. Writing through a pointer to a string literal is undefined behaviour because the literal lives in `.rodata` - the classic `char* s = "abc"; s[0] = 'A';`. And the stack is a fixed per-thread size, so a large local array or deep recursion overflows it; large buffers belong on the heap.

`size` and `nm` are how you look at this on a real binary, and `nm` is also how you answer "is this symbol actually in the library".

[↑ Back to question index](#question-index)

---

## Question C-007

[↑ Back to question index](#question-index)

### Question C-007 — `malloc`, `calloc`, `realloc`, `free` - what are the traps?

**Short answer**

- `malloc` leaves memory uninitialized, `calloc` zeroes it and checks the multiplication for overflow, so `calloc(n, size)` is safer than `malloc(n * size)`.
- `realloc` may move the block, which invalidates every other pointer into it, and it returns `NULL` on failure without freeing the original - so assigning its result straight back to the only pointer you have leaks the block on failure.
- `free` on `NULL` is defined and harmless; double free and use after free are undefined behaviour, and setting the pointer to `NULL` after freeing turns a use-after-free into a null dereference, which is far easier to diagnose.

**Details and nuances**

The `realloc` trap, written out, because it is the one interviewers actually ask for:

```c
char* tmp = realloc(buf, new_size);
if (!tmp) { /* buf is still valid and must still be freed */ return -1; }
buf = tmp;
```

`buf = realloc(buf, new_size);` loses the original pointer when `realloc` returns `NULL`.

Beyond that: allocation size must be checked against overflow before it is computed, the pointer returned must be checked before use, and every `malloc` needs exactly one `free` on every path, which is what makes [C-005](#question-c-005)'s single-exit pattern worth the structure.

[↑ Back to question index](#question-index)

---

## Question C-008

[↑ Back to question index](#question-index)

### Question C-008 — How do alignment and padding work in a struct?

**Short answer**

- Each member must sit at an offset that is a multiple of its alignment, so the compiler inserts padding between members, and the struct's own size is rounded up to a multiple of its strictest member's alignment so that arrays of it stay aligned.
- Member order therefore changes `sizeof`: declaring members from largest alignment to smallest usually removes most of the padding.
- Never compute a layout by adding up member sizes, and never assume two compilers produce the same layout - use `offsetof` and `sizeof`.

**Details and nuances**

```c
struct Bad  { char a; int b; char c; };   /* commonly 12 bytes */
struct Good { int b; char a; char c; };   /* commonly 8 bytes  */
```

Where this stops being cosmetic is a struct written to a file or sent over a socket: the padding bytes are uninitialized, so writing the struct raw leaks whatever was in memory and produces a file another build cannot read. Serialize field by field, or define the wire format explicitly, and keep endianness separate from alignment as its own decision.

[↑ Back to question index](#question-index)

---

## Question C-009

[↑ Back to question index](#question-index)

### Question C-009 — What is a packed struct, and what does it cost?

**Short answer**

- Packing removes the padding, typically with `__attribute__((packed))` or `#pragma pack`, so the layout matches a byte-exact external format.
- The cost is that members are no longer naturally aligned: on some architectures an unaligned access traps, and on x86 it is merely slower, so the penalty is either a crash or invisible.
- Worse, taking the address of a packed member yields an underaligned pointer, and dereferencing it is undefined behaviour even where the direct access would have worked.

**Details and nuances**

The legitimate use is a struct that must mirror a hardware register block or an on-the-wire packet layout. The safer alternative for the wire case is to parse field by field with explicit offsets and `memcpy`, which costs a few lines and works identically everywhere - so the honest answer to "would you use a packed struct" is "for hardware yes, for a protocol I would rather parse explicitly".

Both are non-standard extensions, so the syntax differs between GCC/Clang and MSVC, which is itself a reason to keep the packed definitions in one place.

[↑ Back to question index](#question-index)

---

## Question C-010

[↑ Back to question index](#question-index)

### Question C-010 — What is a flexible array member?

**Short answer**

- A C99 feature: the last member of a struct may be declared `type name[];` with no size, letting one allocation hold the header and a variable-length payload contiguously.
- You allocate `sizeof(struct) + n * sizeof(element)` and get a single block - one `malloc`, one `free`, and no second pointer chase.
- Before C99 the same thing was done with a one-element or zero-length array at the end, the "struct hack"; the zero-length form is a GCC extension rather than standard C89.

**Details and nuances**

```c
struct Packet { size_t len; unsigned char data[]; };

struct Packet* p = malloc(sizeof *p + n);
p->len = n;
```

Rules that follow from it: the struct must have at least one other member, it cannot be a member of another struct or an element of an array, and `sizeof` the struct does not include the flexible member - which is exactly why the allocation formula above is correct and why adding it again would over-allocate.

This comes up on legacy C because the pre-C99 variants are everywhere, and because the single-allocation property is a real performance argument, not just a style one.

[↑ Back to question index](#question-index)

---

## Question C-011

[↑ Back to question index](#question-index)

### Question C-011 — What do you need to know about C strings?

**Short answer**

- A C string is a `char` array terminated by `'\0'`, so length is O(n) and every operation depends on a terminator that nothing enforces.
- `strcpy` and `strcat` have no bound and are the classic overflow; `strncpy` bounds the write but does **not** guarantee termination and pads to the full size, which makes it the wrong fix most of the time.
- Prefer `snprintf`, which always terminates and returns the length it wanted, so truncation is detectable.

**Details and nuances**

```c
char dst[8];
strncpy(dst, src, sizeof dst);      /* may leave dst unterminated */
snprintf(dst, sizeof dst, "%s", src);  /* always terminated; returns intended length */
if (snprintf(dst, sizeof dst, "%s", src) >= (int)sizeof dst) { /* truncated */ }
```

Also worth having ready: a string literal is not writable ([C-006](#question-c-006)); `sizeof` an array gives the buffer size but `sizeof` a `char*` gives the pointer size, which is why passing an array to a function loses the length and it must travel as a separate parameter; and `strlen` in a loop condition turns a linear scan into a quadratic one.

[↑ Back to question index](#question-index)

---

# 3. Interfaces and Modularity

## Question C-012

[↑ Back to question index](#question-index)

### Question C-012 — What are function pointers used for in C?

**Short answer**

- They are C's mechanism for everything higher-level languages get from virtual functions, lambdas and interfaces: callbacks, dispatch tables, plugin boundaries, and comparator arguments like the one `qsort` takes.
- The syntax is `int (*fn)(int, int);` - the parentheses around `*fn` are what make it a pointer to a function rather than a function returning a pointer.
- A struct of function pointers is how C builds a vtable by hand, and that is exactly what a C API exposing polymorphism looks like.

**Details and nuances**

```c
typedef int (*Compare)(const void*, const void*);

struct Allocator {          /* a hand-written interface */
    void* (*alloc)(void* ctx, size_t n);
    void  (*free )(void* ctx, void* p);
    void*  ctx;             /* the "this" that C does not have */
};
```

The `ctx` member is the part worth calling out: C has no implicit receiver, so any callback that needs state takes a `void*` user-data parameter, and getting that ownership right - who allocates it, who outlives whom - is where callback APIs actually go wrong.

Two smaller points: a function pointer and an object pointer are not required to be interconvertible, which is why `dlsym` returning `void*` needs care; and calling through a pointer with the wrong prototype is undefined behaviour, which is a real hazard when a dispatch table is built by hand.

[↑ Back to question index](#question-index)

---

## Question C-013

[↑ Back to question index](#question-index)

### Question C-013 — What is an opaque struct, and why is it the main encapsulation tool in C?

**Short answer**

- The header declares the type without defining it - `typedef struct Session Session;` - and only the implementation file has the definition, so callers can hold `Session*` but cannot see or touch the fields.
- That gives C what `private` gives C++: the layout becomes an implementation detail, so fields can be added or reordered without recompiling or breaking callers.
- The cost is that callers cannot allocate it or know its size, so the API must provide `create` and `destroy`, and every operation becomes a function call.

**Details and nuances**

```c
/* session.h */
typedef struct Session Session;
Session* session_create(const char* host);
int      session_send  (Session*, const void* data, size_t len);
void     session_destroy(Session*);

/* session.c */
struct Session { int fd; char* host; /* free to change */ };
```

This is the single most important pattern for a C library that other code links against, because it is what makes ABI stability possible: expose the struct and its size becomes part of your binary contract, so adding a field breaks every caller that was compiled against the old header. Hide it and you can change the internals in a patch release.

It is also the shape behind every C API that looks object-oriented - `FILE*`, a database handle, a SIP session - and behind the handle typedefs mentioned in [C-003](#question-c-003).

**Example or evidence boundary**

Production experience from the C89 platform: modules maintained under strict backward-compatibility constraints, where what may change without breaking a caller is the daily question.

[↑ Back to question index](#question-index)

---

## Question C-014

[↑ Back to question index](#question-index)

### Question C-014 — What belongs in a header, and when do you forward-declare?

**Short answer**

- A header carries declarations, type definitions the caller needs, macros and `extern` declarations - not definitions, because a definition included by two translation units violates the one-definition rule at link time.
- Include guards or `#pragma once` prevent double inclusion within one translation unit; they do nothing about duplicate symbols across translation units.
- Forward-declare when only a pointer or reference to the type is needed: it removes an `#include` from the header, which cuts both compile time and the recompilation blast radius of a change.

**Details and nuances**

The rule of thumb is that a header should include what it needs to be self-contained and nothing more, and that anything used only as `struct Foo*` in the header can be a forward declaration with the real `#include` moved into the `.c` file. On a large C codebase this is the difference between a header change rebuilding four files and rebuilding four hundred.

A variable defined in a header is the classic mistake: `int counter;` in a header gives each translation unit its own tentative definition and, depending on the linker's tolerance for common symbols, either a link error or - worse - silently several copies. The correct shape is `extern int counter;` in the header and one definition in exactly one `.c` file.

[↑ Back to question index](#question-index)

---

## Question C-015

[↑ Back to question index](#question-index)

### Question C-015 — What are the traps of the C preprocessor?

**Short answer**

- A macro is text substitution before compilation, so it has no types, no scope and no respect for expressions: it can evaluate its argument more than once and it ignores operator precedence.
- Parenthesize the whole body and every parameter, and even then a macro like `MAX(a, b)` evaluates one argument twice, so `MAX(i++, j)` is a bug the compiler will not mention.
- Prefer a real function, a `static inline` function in C99, or an enum instead of `#define` for constants, because all three are visible to the compiler and the debugger and macros are not.

**Details and nuances**

```c
#define SQUARE(x) x * x            /* SQUARE(1+2) is 1 + 2*1 + 2 == 5   */
#define SQUARE(x) ((x) * (x))      /* better, but SQUARE(i++) still UB  */
```

Legitimate uses remain: include guards, conditional compilation for platforms, `assert`, and anything that genuinely needs the token text, such as stringification with `#` or token pasting with `##`. On C89 specifically the preprocessor also does work that `inline` and `_Generic` would do in later standards, which is why old codebases lean on it far more than a modern one should.

Debugging tip worth mentioning: `gcc -E` shows the post-preprocessor source, and it is the fastest way to settle an argument about what a macro expanded to.

[↑ Back to question index](#question-index)

---

# 4. Build, Link and Load

## Question C-016

[↑ Back to question index](#question-index)

### Question C-016 — What are the stages from C source to a running program?

**Short answer**

- Preprocessing resolves includes, macros and conditionals into one translation unit; compilation turns that into assembly and then into an object file with a symbol table; linking resolves symbols across object files and libraries into an executable.
- A fourth stage matters for shared libraries: the dynamic loader resolves the remaining symbols at process start, which is why a program can build cleanly and still fail to launch.
- Knowing which stage failed is most of the diagnosis - an undefined symbol is a link error, an unknown type is a compile error, and "cannot open shared object file" is neither.

**Details and nuances**

```
main.c → [cpp] → main.i → [cc1] → main.s → [as] → main.o → [ld] → a.out → [ld.so] → running
          -E              -S             -c
```

The flags are worth having ready because they are how you isolate a stage: `-E` to see the expanded source, `-S` for assembly, `-c` to stop at the object file. `nm` lists a symbol table, and a symbol marked `U` is undefined - which answers "why does the linker say it cannot find this" faster than reading the build log.

Link order matters for static libraries: the linker processes its inputs left to right and takes from an archive only what is needed at that point, so a library listed before the object that uses it will appear to be missing.

[↑ Back to question index](#question-index)

---

## Question C-017

[↑ Back to question index](#question-index)

### Question C-017 — Static vs shared libraries in C?

**Short answer**

- A static library is an archive of object files; the linker copies the needed members into the executable, so there is no run-time dependency and no version skew, at the cost of size and of needing a relink to pick up a fix.
- A shared library is loaded at run time and shared between processes, so a fix ships without relinking callers - but the ABI becomes a contract, and a mismatch is a run-time failure rather than a build failure.
- Shared code must be compiled position-independent (`-fPIC`), which is the difference between an object that can be mapped anywhere and one that cannot.

**Details and nuances**

| | Static (`.a` / `.lib`) | Shared (`.so` / `.dll`) |
|---|---|---|
| Resolution | Link time | Load time, and lazily per symbol |
| Distribution | One binary | Binary plus its dependencies |
| Fixing a bug | Relink every consumer | Replace the library |
| Failure mode | Link error, at build time | Loader error or a symbol mismatch, at run time |
| Cost | Size, duplicated across binaries | ABI discipline, versioning |

ABI stability is the whole difficulty of the shared case, and it is why [C-013](#question-c-013) matters: an exposed struct puts its layout in the contract, so adding a field is a breaking change for every caller compiled against the old header, while an opaque handle leaves you free.

[↑ Back to question index](#question-index)

---

## Question C-018

[↑ Back to question index](#question-index)

### Question C-018 — How does a shared library get found and loaded, and how do you override one?

**Short answer**

- At load time the dynamic linker searches, roughly in order: `DT_RPATH`, then `LD_LIBRARY_PATH`, then `DT_RUNPATH`, then the `ldconfig` cache, then the default system directories - and `ldd` shows what it actually resolved.
- `LD_PRELOAD` loads a library ahead of all others so its symbols win, which is the standard way to substitute or intercept a function without touching the program.
- For a deliberate plugin boundary, load explicitly with `dlopen`/`dlsym`/`dlclose` and an agreed entry point, so the substitution is a design feature rather than an environment trick.

**Details and nuances**

The two mechanisms answer different questions. `LD_PRELOAD` is for when you cannot change the program: interposing `malloc` to find a leak, stubbing a system call in a test, or swapping an implementation to reproduce a customer's environment. It is a debugging and operations tool, and relying on it in production is fragile because it is one environment variable away from not happening.

`dlopen` is the design answer: define an interface - in C that means a struct of function pointers ([C-012](#question-c-012)) returned by a known factory symbol - build each implementation as its own `.so`, and let the program choose at run time. The costs are that symbol errors move from link time to run time, that `dlsym` returns `void*` which must be converted to a function pointer with care, and that unloading a library while anything still points into it is a crash.

`RPATH` versus `LD_LIBRARY_PATH` is worth one sentence: the first is baked into the binary at link time and travels with it, the second is per-process environment. Shipping a program that depends on the caller setting `LD_LIBRARY_PATH` correctly is how "it works on my machine" happens.

[↑ Back to question index](#question-index)

---

# 5. Debugging

## Question C-019

[↑ Back to question index](#question-index)

### Question C-019 — How do you debug a C crash from a core dump?

**Short answer**

- You need the core and a binary with symbols: build with `-g`, keep the unstripped binary even if you ship a stripped one, and make sure the core limit is not zero (`ulimit -c unlimited`, or `coredumpctl` where systemd collects them).
- `gdb ./program core`, then `bt` for the stack, `frame N` and `info locals` to inspect, and `p` to print - that sequence answers most crashes.
- When the binary is stripped and only an address is available, `addr2line -e ./program <addr>` maps it back to file and line, and `nm` tells you whether a symbol is even present.

**Details and nuances**

The part that is easy to get wrong operationally: the core must be matched with *the same build*. A rebuilt binary has different addresses, so the backtrace will be confident and wrong. Keeping the unstripped binary and its build ID alongside every release is what makes a production core usable at all.

What a backtrace does not tell you is when the damage happened. A crash in `free` usually means a heap overrun somewhere earlier, and a crash inside a library usually means the caller passed something invalid. Reading the frames outward - whose data is this, who owned it, who last wrote it - is the actual work; the stack only says where the program noticed.

**Example or evidence boundary**

Production experience: diagnosing native failures on AWS EC2 Linux hosts over SSH with `gdb` and core dumps on the C89 platform.

[↑ Back to question index](#question-index)

---

## Question C-020

[↑ Back to question index](#question-index)

### Question C-020 — Valgrind or sanitizers?

**Short answer**

- Valgrind needs no rebuild and catches uninitialized reads, leaks and invalid accesses through binary instrumentation, at roughly 10-50x slowdown.
- AddressSanitizer is a compile-time instrumentation, perhaps 2x slower, and finds heap and stack overruns and use-after-free with much better reports - so it is the one to put in CI.
- They are complementary rather than alternatives: ASan for the fast feedback loop, Valgrind's memcheck when you need to run a binary you cannot rebuild, and neither replaces ThreadSanitizer for data races.

**Details and nuances**

| Tool | Finds | Rebuild needed | Cost |
|---|---|---|---|
| ASan | Heap/stack/global overflow, use-after-free, leaks | Yes (`-fsanitize=address`) | ~2x |
| UBSan | Undefined behaviour: overflow, misalignment, bad shifts | Yes | Small |
| TSan | Data races | Yes | ~5-15x |
| Valgrind memcheck | Uninitialized reads, leaks, invalid accesses | No | ~10-50x |

Two practical notes. ASan and TSan cannot be combined in one build, so they are separate CI jobs. And uninitialized-memory reads are Valgrind's strength and ASan's blind spot - MemorySanitizer covers them but requires every dependency to be instrumented too, which is usually the point where the answer becomes "use Valgrind for that one".

[↑ Back to question index](#question-index)
