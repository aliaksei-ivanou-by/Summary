# C++ Core Assessment — Answer Guide

This handbook covers the C++17/C++20 core-language topics commonly discussed in technical assessments. Every question is followed by a model answer. The answers state the portable language rule first and, where useful, distinguish it from common implementation practice.

Labels:

- **[Basic]** — expected core knowledge.
- **[Deep dive]** — language-lawyer details, edge cases, or design implications.
- **[Code]** — code reading, diagnosis, or redesign.

## Contents

1. [Build model and preprocessor](#1-build-model-and-preprocessor)
2. [Types, conversions, and initialization](#2-types-conversions-and-initialization)
3. [Expressions, functions, and lambdas](#3-expressions-functions-and-lambdas)
4. [Classes, inheritance, and polymorphism](#4-classes-inheritance-and-polymorphism)
5. [Resource management and move semantics](#5-resource-management-and-move-semantics)
6. [Templates, SFINAE, and concepts](#6-templates-sfinae-and-concepts)
7. [Exceptions and safety guarantees](#7-exceptions-and-safety-guarantees)

---

# 1. Build model and preprocessor

## 1.1. Translation units, compilation, and linking

1. **[Basic] What is a translation unit, and how is it produced from a `.cpp` file?**

   **Answer.** A translation unit is the result of preprocessing one source file: included headers are inserted, macros are expanded, conditional-compilation branches are selected, and comments are removed. The compiler processes each translation unit independently. A program normally contains several translation units, which are later joined by the linker.

2. **[Basic] Describe the path from source code to an executable: preprocessor, compiler, assembler, and linker.**

   **Answer.** Conceptually, preprocessing produces expanded source; compilation parses and type-checks it and generates assembly or an equivalent intermediate representation; assembly creates an object file containing machine code, symbols, and relocation records; linking combines object files and libraries, resolves cross-unit symbols, performs relocations, and emits an executable or shared library. Modern drivers may merge these physical steps, but the model remains useful.

   ```mermaid
   flowchart LR
       A[.cpp + headers] -->|preprocess| B[translation unit]
       B -->|compile| C[assembly / IR]
       C -->|assemble| D[object file]
       D -->|link with objects and libraries| E[executable or shared library]
   ```

3. **[Basic] How does a compilation error differ from a linker error? Give two examples of each.**

   **Answer.** A compilation error occurs while one translation unit is being parsed or checked, for example a syntax error or calling a function with no viable overload. A linker error occurs after compilation, while object files are combined, for example an unresolved external caused by a missing definition, or a multiple-definition error caused by defining the same non-inline function in two object files.

4. **[Deep dive] What are a symbol table, name mangling, and relocation, and at which stages do they matter?**

   **Answer.** An object file's symbol table records defined and referenced entities such as functions and variables. C++ name mangling encodes information such as namespaces, classes, and parameter types into linker-visible symbol names so overloads can coexist; it is ABI-specific. Relocation records identify addresses that cannot be finalized until code and data are laid out. The compiler/assembler emit these records and the linker resolves symbols and applies relocations; a loader may perform additional dynamic relocations.

5. **[Deep dive] Why can the same header be processed differently in different translation units?**

   **Answer.** Inclusion is textual and each translation unit has its own preprocessor state. Different preceding `#define`s, build flags, include paths, language modes, platform macros, pragma state, or include order can therefore select different declarations or definitions. If this makes an `inline` function, template, class, or other ODR-sensitive entity differ between translation units, the program may be ill-formed with no required diagnostic.

6. **[Code] The code compiles, but the linker reports `undefined reference` or `unresolved external symbol`. What is your diagnostic sequence?**

   **Answer.** Start from the exact missing mangled symbol and identify the declaration that requested it. Verify that a matching definition exists: namespace, class scope, parameter cv/ref qualifiers, calling convention, template arguments, and `const`/`noexcept` details must agree. Then confirm the defining source file or library is part of the link, inspect symbols with tools such as `nm`, `objdump`, `dumpbin`, or `llvm-nm`, and check library order on linkers where order matters. Finally check conditional compilation, architecture/configuration mismatches, symbol visibility/export settings, C versus C++ linkage, and missing explicit template instantiations.

## 1.2. Header files: include guards, `#pragma once`, and header contents

1. **[Basic] Why are include guards needed? Show their canonical form.**

   **Answer.** A header may be reached more than once through a graph of includes. A guard ensures its contents are processed only once per translation unit, preventing repeated declarations that are illegal in the same unit and reducing work.

   ```cpp
   #ifndef PROJECT_MODULE_WIDGET_HPP
   #define PROJECT_MODULE_WIDGET_HPP

   // declarations

   #endif  // PROJECT_MODULE_WIDGET_HPP
   ```

2. **[Basic] How does `#pragma once` differ from include guards? What are its benefits and limitations?**

   **Answer.** `#pragma once` asks the implementation to include the physical header once per translation unit. It is concise, cannot suffer a copied guard macro, and is widely supported, but it is not specified by the C++ standard and unusual path/filesystem aliasing can confuse file identity. Macro guards are fully portable and can intentionally be controlled by a macro, but require a globally unique name.

3. **[Basic] What can normally be placed in a header without violating the ODR?**

   **Answer.** Headers commonly contain declarations; class and enum definitions; alias declarations; templates and their definitions; and definitions explicitly or implicitly `inline`, including functions defined inside a class, `constexpr`/`consteval` functions, and C++17 inline variables. Namespace-scope `const` objects have internal linkage by default unless declared otherwise, but one should still consider whether per-translation-unit copies are intended.

4. **[Deep dive] Why does defining an ordinary non-`inline` function or global variable in a header often cause a linker error?**

   **Answer.** Every translation unit that includes the header emits a definition with external linkage. The ODR permits only one program-wide definition of such an entity, so linking multiple units normally produces a duplicate-symbol error. Marking the entity `inline` changes the ODR rule; merely using a guard does not, because a guard operates separately in each translation unit.

5. **[Deep dive] When is placing an implementation in a header allowed or necessary?**

   **Answer.** It is normal for templates, because their definitions usually must be visible at the point of instantiation; for `inline`, `constexpr`, and `consteval` functions; for functions defined in a class body; and for C++17 inline variables. Header-only libraries use the same mechanisms. Non-template implementation details can instead live in a `.cpp` file to reduce coupling and build time.

6. **[Code] What is wrong with this header?**

   ```cpp
   // config.h
   using namespace std;

   int retry_count = 3;

   int next_id() {
       static int id = 0;
       return ++id;
   }
   ```

   **Answer.** `using namespace std;` pollutes every includer's namespace and can introduce ambiguities. `retry_count` and `next_id` are non-inline external definitions, so including the header in multiple translation units violates the ODR. In C++17, use `inline int retry_count = 3;` if one shared mutable variable is intentional, though an accessor or encapsulated state is usually safer. Make `next_id` `inline`, or declare it in the header and define it in one `.cpp`. Also consider synchronization because the increment is not safe under concurrent calls.

## 1.3. Forward declarations versus `#include`

1. **[Basic] What is a forward declaration, and why is it used?**

   **Answer.** A forward declaration introduces a name without supplying its complete definition, for example `class Customer;`. It lets code mention an incomplete type where its layout or members are not needed. This reduces header dependencies, avoids some include cycles, and can improve incremental-build times.

2. **[Basic] When is an incomplete class type sufficient, and when is a complete definition required?**

   **Answer.** An incomplete type is generally enough to declare pointers and references, function parameters and returns involving those indirections, and some templates whose operations are deferred. A complete type is required when its size, alignment, layout, base classes, members, or destructor behavior must be known—for example an object data member, inheritance, `sizeof(T)`, member access, object construction/destruction, or deletion at the point where `delete` is instantiated.

3. **[Deep dive] Can a field be an object of incomplete type? What about a pointer, reference, `std::unique_ptr`, or `std::shared_ptr`?**

   **Answer.** A non-static data member cannot be an incomplete object type because the containing layout needs its size. Pointers and references are fine. Both smart pointers can be declared with an incomplete `T`, but operations have different requirements: the default deleter used by `unique_ptr<T>` needs `T` complete where destruction/reset occurs, while `shared_ptr<T>` can usually be destroyed with `T` incomplete because deletion logic is stored in the control block; constructing it from `new T` still requires completeness.

4. **[Deep dive] Why is the destructor of a class containing `std::unique_ptr<Impl>` often defined in a `.cpp` after `Impl` is defined?**

   **Answer.** An inline implicitly generated destructor may instantiate `unique_ptr<Impl>`'s default deleter in client translation units where `Impl` is still incomplete. Declaring `~Owner();` in the header and defining `Owner::~Owner() = default;` in the `.cpp` after the full `Impl` definition moves that instantiation to a context where deletion is valid. This is the usual PImpl pattern.

5. **[Deep dive] How do forward declarations affect coupling and incremental build time?**

   **Answer.** They reduce textual inclusion and make a header depend only on an interface name rather than another type's full layout. Changes to the other header then rebuild fewer translation units. The tradeoff is that excessive indirection can complicate ownership and prevent inline/layout-dependent operations; forward-declaring standard-library implementation details is not permitted, so include the proper standard header when necessary.

6. **[Code] Which includes can be replaced with forward declarations?**

   ```cpp
   #include "Customer.h"
   #include "Order.h"

   class Report {
   public:
       Report(const Customer& customer);
       void add(const Order* order);
   private:
       const Customer* customer_;
   };
   ```

   **Answer.** Both can be replaced in this header with `class Customer;` and `class Order;`: only references and pointers occur. The `.cpp` implementing the constructor and `add`, or any code that accesses members, must include the complete definitions. The non-owning `customer_` pointer also creates a lifetime contract that should be documented or represented by a safer design if nullability/lifetime are unclear.

## 1.4. Macros

1. **[Basic] How are object-like and function-like macros declared? How does a macro differ from a function or `constexpr` variable?**

   **Answer.** `#define BUFFER_SIZE 4096` is object-like and `#define SQUARE(x) ((x) * (x))` is function-like; the latter requires no whitespace between its name and `(` in the definition. Macros perform untyped token substitution before C++ parsing, ignore scopes, and usually have poor diagnostics. Functions and `constexpr` entities obey types, scopes, overload resolution, and debugger/tooling rules, so they are preferred whenever they can express the requirement.

2. **[Basic] What do the `#` and `##` macro operators do?**

   **Answer.** In a replacement list, `#parameter` stringizes the unexpanded spelling of an argument into a string literal, while `a ## b` pastes preprocessing tokens into one token. A helper expansion layer is often needed if macro arguments must expand before stringizing or pasting. Token pasting must produce a valid preprocessing token.

3. **[Basic] How do `#if`, `#ifdef`, `#ifndef`, `#elif`, and `defined` work? When is conditional compilation justified?**

   **Answer.** They select tokens during preprocessing: `#ifdef X` and `#ifndef X` test macro presence, while `#if`/`#elif` evaluate an integer constant preprocessor expression and may use `defined(X)`. They are appropriate for platform/compiler integration, feature availability, header guards, and excluding code that cannot even be parsed on a target. For type-dependent behavior inside otherwise valid C++, prefer `if constexpr`, overloads, templates, or concepts.

4. **[Code] What problems do these macros have, and how should they be fixed?**

   ```cpp
   #define SQUARE(x) x * x
   #define MAX(a, b) ((a) > (b) ? (a) : (b))

   int x = SQUARE(1 + 2);
   int y = MAX(i++, j++);
   ```

   **Answer.** `SQUARE(1 + 2)` expands to `1 + 2 * 1 + 2`, yielding `5`, because the parameter and whole expansion are not parenthesized. Even a parenthesized `SQUARE` evaluates its argument twice. `MAX` also evaluates the selected argument again, so either `i` or `j` is incremented twice. Prefer `constexpr` function templates such as `square(T x) { return x * x; }` and `std::max`; each function argument is evaluated once.

5. **[Deep dive] Why can a macro argument be evaluated multiple times, and why is that dangerous?**

   **Answer.** Every occurrence of the parameter token is replaced with the argument tokens. If the expansion uses a parameter twice, an expression with side effects such as `i++`, I/O, locking, or allocation runs twice. This can change program state unexpectedly and, in older sequencing cases, even produce undefined behavior. Parentheses fix precedence, not repeated evaluation.

6. **[Deep dive] What does `do { ... } while (false)` accomplish in a multi-statement macro?**

   **Answer.** It packages multiple statements into one syntactic statement, so a call can safely be followed by a semicolon and used in `if (...) MACRO(); else ...` without breaking the `else`. The loop executes exactly once. It does not solve name collisions, repeated argument evaluation, typing, or control-flow surprises such as a `break` applying to the synthetic loop.

7. **[Deep dive] Which modern C++ features replace macros for constants, functions, types, and compile-time branching?**

   **Answer.** Use `constexpr` or `inline constexpr` variables for constants; inline/`constexpr` functions and templates for operations; `using` aliases for types; and `if constexpr`, overloads, concepts, or specialization for compile-time selection. Attributes and standard source-location facilities can replace some instrumentation macros. Preprocessor macros remain necessary for preprocessing-specific tasks such as conditional inclusion and token stringizing/pasting.

## 1.5. Declaration versus definition

1. **[Basic] How does a declaration differ from a definition?**

   **Answer.** A declaration introduces or redeclares a name and its type to the program. A definition is a declaration that additionally defines the entity: it supplies a function body, allocates storage for a variable, or completes a class/enum. Every definition is a declaration, but many declarations are not definitions.

2. **[Basic] Classify these lines as declarations, definitions, or both.**

   ```cpp
   extern int counter;
   int counter;
   int sum(int, int);
   int sum(int a, int b) { return a + b; }
   class Widget;
   class Widget {};
   ```

   **Answer.** `extern int counter;` is a non-defining declaration; `int counter;` is a definition (and declaration) with zero-initialization at namespace scope. The function prototype is a declaration, while the function with a body is a definition. `class Widget;` is a forward declaration; `class Widget {};` is the class definition. All six lines declare something.

3. **[Deep dive] What constitutes a definition of a type, function, variable, template, and static data member?**

   **Answer.** A class definition contains its body; an enum definition contains its enumerators. A function definition contains a body (including `= default` or `= delete` where permitted). A variable declaration is normally a definition unless it is only `extern` without an initializer or falls under another special rule. A template definition supplies the templated body. A non-inline static data member traditionally needs one namespace-scope definition if odr-used; a C++17 `inline static` member is defined in-class, and an in-class `constexpr` static member is implicitly inline.

4. **[Deep dive] Is `extern int x = 42;` a definition? Why?**

   **Answer.** Yes. An initializer makes this a definition and allocates storage; `extern` specifies external linkage rather than canceling the definition. By contrast, `extern int x;` without an initializer is only a declaration.

5. **[Deep dive] Which entities may be defined more than once, and under what conditions?**

   **Answer.** Inside one translation unit, definitions generally must be unique. Across translation units, types, templates, and inline functions/variables may have multiple definitions when each appears in a different translation unit, consists of the same token sequence, and name lookup within each definition satisfies the ODR's equivalence rules. Internal-linkage entities are distinct per translation unit. Ordinary external non-inline variables and functions require one program-wide definition if odr-used.

## 1.6. Scopes, object lifetime, and namespaces

1. **[Basic] Which scopes exist in C++: block, function, class, namespace, and template-parameter scope?**

   **Answer.** Block scope covers names declared in compound statements and control-statement declarations. Function scope is special mainly for labels, which are visible throughout the function. Class scope contains members; namespace scope contains namespace members, including global namespace names. Template parameters have template-parameter scope. C++ also specifies function-parameter, enumeration, and other precise scope categories; a name is usable only where lookup can find its declaration.

2. **[Basic] How does a name's scope differ from object lifetime and storage duration?**

   **Answer.** Scope is a source-code name-lookup property. Storage duration describes the minimum period for which storage exists. Lifetime is the runtime interval in which an object actually exists in that storage and its type's rules apply. They can differ: a dynamically allocated object may outlive the block containing its pointer; placement construction can begin a new lifetime in existing storage; and a name may be hidden while its object remains alive.

3. **[Basic] Describe automatic, static, thread, and dynamic storage duration and their relationship to lifetime.**

   **Answer.** Automatic storage normally lasts from block entry to exit; static storage lasts for the program; thread storage lasts for the thread; dynamic storage lasts from allocation until deallocation. Object lifetime usually fits within the storage duration but is not identical to it: construction begins lifetime after storage exists, destruction ends it before storage is reclaimed, and manual-lifetime techniques can host successive objects in the same storage.

4. **[Deep dive] What are name hiding and shadowing? How does lookup work with nested namespaces and classes?**

   **Answer.** An inner declaration can hide an outer declaration with the same name; “shadowing” commonly describes this for local variables. Unqualified lookup starts in the relevant innermost scope and proceeds outward, with class-specific base lookup and argument-dependent lookup where applicable. Once a nearer declaration set is found, unrelated outer overloads are generally hidden. Use qualification such as `ns::name`, `Base::name`, or a targeted `using Base::name` to expose the intended entity.

5. **[Deep dive] How does a using-declaration differ from a using-directive, and why is the latter undesirable in headers?**

   **Answer.** `using std::string;` introduces one selected name into the current scope. `using namespace std;` makes all suitable names from that namespace available to unqualified lookup. A using-directive in a header affects every includer after the directive, can silently change overload sets or create ambiguities, and makes dependencies unclear; namespace aliases or explicit qualification are safer.

6. **[Code] Which objects are alive at each point, and which access is invalid?**

   ```cpp
   const int* make_value() {
       int local = 42;
       static int cached = 7;
       return condition() ? &local : &cached;
   }
   ```

   **Answer.** Both `local` and `cached` are alive while the function body executes. `local` has automatic storage and its lifetime ends on return, so returning `&local` creates a dangling pointer and later dereference is undefined behavior. `cached` has static storage and remains alive until program termination, so returning `&cached` is valid, although shared mutable state may require synchronization. The returned pointer's validity therefore depends on the runtime branch—an unsafe interface.

## 1.7. Linkage: internal, external, and no linkage

1. **[Basic] What is linkage, and how does it differ from visibility and storage duration?**

   **Answer.** Linkage determines whether declarations in different scopes or translation units denote the same entity. External linkage can connect across translation units; internal linkage connects only within one translation unit; no linkage prevents such connection. Source-level scope controls lookup, storage duration controls storage lifetime, and binary symbol visibility controls whether a symbol is exported from a shared object—related but separate concepts.

2. **[Basic] Which names have external, internal, or no linkage?**

   **Answer.** Namespace-scope non-`const` functions and variables normally have external linkage. Namespace-scope names declared `static`, names in unnamed namespaces, and non-`extern` namespace-scope non-template `const` variables normally have internal linkage. Ordinary block-local variables and local classes have no linkage. There are detailed exceptions for `extern`, inline entities, templates, modules, and previously declared names.

3. **[Basic] What do `static` and `extern` mean for namespace-scope entities?**

   **Answer.** At namespace scope, `static` gives a function or variable internal linkage, creating a translation-unit-local entity. `extern` generally declares an entity with external linkage; without an initializer a variable declaration is normally not a definition, while with an initializer it is. Neither meaning should be confused with block-scope `static`, which primarily gives static storage duration.

4. **[Deep dive] How does an unnamed namespace differ from `static` for functions and variables in a `.cpp` file?**

   **Answer.** Both provide translation-unit-local identity in ordinary use. An unnamed namespace is more general: it can contain types, templates, variables, and functions, preserves normal namespace semantics, and is the modern idiom. Namespace-scope `static` applies only to variables and functions. Names in an unnamed namespace can participate naturally in ADL through their types.

5. **[Deep dive] What linkage do a namespace-scope `const`, an inline variable, and a template have?**

   **Answer.** A non-template, non-volatile namespace-scope `const` variable has internal linkage by default unless it is explicitly `extern`, previously declared external, or is `inline`. A non-`static` inline variable at namespace scope has external linkage and one program-wide identity despite definitions in multiple translation units. Names of ordinary namespace-scope templates normally have external linkage; their instantiated entities are governed by template and ODR rules.

6. **[Code] Is `counter` shared by the program or separate in every `.cpp` file?**

   ```cpp
   // metrics.h
   static int counter = 0;
   ```

   **Answer.** It is a distinct internal-linkage object in every translation unit that includes the header. Incrementing it in one translation unit does not update the others. If a single program-wide object is intended, use `inline int counter = 0;` in C++17+, or `extern int counter;` in the header plus exactly one definition in a `.cpp`; preferably encapsulate mutation behind an interface.

## 1.8. The ODR and `inline` functions/variables

1. **[Basic] State the practical meaning of the One Definition Rule.**

   **Answer.** Within one translation unit, a definable item generally has only one definition. Across the whole program, each odr-used non-inline function or variable with external linkage needs exactly one definition. Certain entities—classes, templates, inline functions, and inline variables—may be defined in multiple translation units only when their definitions meet strict equivalence requirements.

2. **[Basic] What does `inline` mean in the language? Must the compiler inline the call?**

   **Answer.** `inline` primarily changes declaration/definition and ODR rules: an inline entity may be defined identically in multiple translation units and has one program-wide identity when externally linked. It does not require call-site substitution. Compilers may inline functions without the keyword and may emit a real call for functions marked `inline`.

3. **[Basic] Why may an inline function be defined in a header?**

   **Answer.** Each translation unit that odr-uses it must see a reachable definition, and the ODR explicitly permits identical definitions of an inline function in multiple translation units. Linkers typically coalesce emitted copies, but the semantic guarantee—not the implementation technique—is what makes the header definition valid.

4. **[Basic] Why were inline variables added in C++17?**

   **Answer.** They allow a variable to be defined in a header with one shared program-wide identity, just as inline functions can be. This is especially useful for header-only libraries, static data members, and namespace-scope constants whose address must be the same everywhere, avoiding a separate `.cpp` definition.

5. **[Deep dive] What requirements apply to multiple definitions of an inline entity in different translation units?**

   **Answer.** The definition must be reachable where the entity is used, appear in separate translation units, and satisfy the ODR: commonly the same token sequence with equivalent name lookup, corresponding constants, language linkage, and default arguments. Each definition must declare the entity inline. Divergent macros, include order, or internal helpers can break equivalence even when the visible text looks similar.

6. **[Deep dive] What does “ill-formed, no diagnostic required” mean in the context of the ODR?**

   **Answer.** The program violates a language rule and has no defined C++ meaning, but implementations are not required to detect or report it. ODR violations often span translation units, while compilers process them separately and linkers see only binary artifacts. A successful build therefore does not prove that the program is valid.

7. **[Code] Find the ODR problem.**

   ```cpp
   // limit.h
   inline int limit() {
   #ifdef LARGE_BUILD
       return 1024;
   #else
       return 64;
   #endif
   }
   ```

   **Answer.** If `LARGE_BUILD` differs between translation units, the inline function has different token definitions, violating the ODR. The linker may silently keep one copy, so calls can behave unpredictably relative to how each unit was compiled. Use one consistent build definition, make the configuration a runtime value, or expose separately named/configured entities whose identities honestly differ.

## 1.9. `extern "C"` and C interoperability

1. **[Basic] Why is `extern "C"` used, and what does it change?**

   **Answer.** It gives declared functions—and, where applicable, names—C language linkage, allowing C++ code to link to symbols using the platform's C ABI naming conventions. It chiefly affects language linkage, including name/linkage encoding and function type rules. Exact calling conventions and ABI details remain implementation/platform matters.

2. **[Basic] Does `extern "C"` make a function C code or change C++ type rules inside it?**

   **Answer.** No. A function defined in a C++ translation unit is still parsed and executed as C++, with C++ types, overload resolution, exceptions, constructors, and so on. The linkage specification describes its interface identity. Exposing exceptions, C++ classes, references, or STL types across a C ABI is generally unsafe even if the compiler accepts a declaration.

3. **[Deep dive] Why are C APIs commonly wrapped in `#ifdef __cplusplus`?**

   **Answer.** A C compiler does not understand `extern "C"`, while a C++ compiler defines `__cplusplus`. The wrapper makes the same header syntactically valid in both languages and applies C linkage only in C++.

   ```cpp
   #ifdef __cplusplus
   extern "C" {
   #endif

   int process(const char* data, unsigned long size);

   #ifdef __cplusplus
   }
   #endif
   ```

4. **[Deep dive] Can functions with C language linkage be overloaded?**

   **Answer.** Different functions with C linkage cannot form a normal C++ overload set under the same name, because C linkage does not provide distinct mangled identities for overloads. A C-linkage function can coexist with C++ functions in certain scopes/namespaces under nuanced rules, but a portable C-facing API should use unique names.

5. **[Deep dive] Which ABI problems besides name mangling can arise at a C/C++ boundary?**

   **Answer.** Calling convention, integer widths, structure layout and packing, alignment, enum representation, endianness, allocator ownership, exception propagation, thread-local/runtime assumptions, and compiler ABI/version differences can all matter. Use fixed-width or explicitly documented C-compatible types, opaque handles, explicit ownership functions, and never let a C++ exception cross the C boundary.

6. **[Code] Make this header usable from both C and C++.**

   ```cpp
   // api.h
   int process(const char* data, unsigned long size);
   ```

   **Answer.** Add an ordinary include guard and an `extern "C"` block active only for C++. Keep the public declaration restricted to C-compatible types; also document the width expectation for `unsigned long`, because it differs between major data models.

   ```cpp
   #ifndef PROJECT_API_H
   #define PROJECT_API_H

   #ifdef __cplusplus
   extern "C" {
   #endif

   int process(const char* data, unsigned long size);

   #ifdef __cplusplus
   }
   #endif

   #endif
   ```

---

# 2. Types, conversions, and initialization

## 2.1. Fundamental types, sizes, ranges, `bool`, and `nullptr`

1. **[Basic] Which fundamental types exist in C++? What minimum-size and relative-size guarantees does the standard provide?**

   **Answer.** Fundamental types comprise `void`, `std::nullptr_t`, integral types, and floating-point types. Integral types include `bool`; the character families (`char`, signed/unsigned `char`, `wchar_t`, C++20 `char8_t`, `char16_t`, `char32_t`); and signed/unsigned `short`, `int`, `long`, and `long long`. Floating types are `float`, `double`, and `long double`. One byte is `sizeof(char) == 1` and has at least 8 bits. The standard guarantees `sizeof(char) <= sizeof(short) <= sizeof(int) <= sizeof(long) <= sizeof(long long)` and minimum widths of 8, 16, 16, 32, and 64 bits respectively for the usual signed integer sequence, including the sign bit; implementations may provide more. Exact widths require types such as `std::int32_t` when those types exist.

2. **[Basic] Why is it not portable to assume that `int` is always 32-bit and `long` is always 64-bit?**

   **Answer.** C++ specifies minimum ranges and ordering, not those exact widths. Common data models differ: LP64 systems use 32-bit `int` and 64-bit `long`, while 64-bit Windows uses LLP64, where both are 32-bit and `long long` is 64-bit. Use `sizeof`, `std::numeric_limits`, or fixed-width `<cstdint>` types when the representation is part of a protocol or ABI.

3. **[Basic] How does signed overflow differ from unsigned overflow?**

   **Answer.** Overflow of a signed integer operation is undefined behavior, allowing the optimizer to assume it never happens. Unsigned arithmetic is defined modulo `2^N`, where `N` is the number of value bits. Defined wraparound does not mean logically correct code: it can still cause bounds, allocation-size, and security bugs.

4. **[Deep dive] What are object representation, padding bits, and trap representations?**

   **Answer.** An object's representation is the sequence of `unsigned char`/`std::byte`-sized units occupying its storage. Value bits contribute to the represented value; padding bits may exist for alignment or implementation reasons and need not participate in equality. Some type/implementation combinations may have bit patterns that do not represent a valid value—a trap representation—and reading such a value through that type can be undefined. Byte-wise copying of a trivially copyable object into bytes and back is supported, but byte-wise equality is not generally the same as value equality because of padding and multiple representations.

5. **[Basic] How does `nullptr` differ from `0` and `NULL` in overload resolution?**

   **Answer.** `nullptr` is a dedicated null pointer literal of type `std::nullptr_t`; it converts to any pointer or pointer-to-member type but not implicitly to an ordinary integer. Literal `0` is an `int` and also a null pointer constant, so an `int` overload is an exact match. `NULL` is an implementation-defined macro, often `0` or `0L`, and can therefore select an integer overload or create ambiguity.

6. **[Deep dive] What is `std::nullptr_t`, and when is it useful?**

   **Answer.** `std::nullptr_t` is the type of `nullptr` (also obtainable as `decltype(nullptr)`). It is useful for overloads that specifically accept a null pointer token, generic code that stores or detects nullness, and APIs that distinguish “null” from integer zero. A function taking an actual pointer is often clearer unless null itself is a separate semantic case.

7. **[Code] Which overload is selected?**

   ```cpp
   void f(int);
   void f(void*);

   f(0);
   f(nullptr);
   ```

   **Answer.** `f(0)` selects `f(int)` because identity conversion is better than converting the null pointer constant to `void*`. `f(nullptr)` selects `f(void*)` because `nullptr` converts to a pointer and cannot implicitly convert to `int`.

## 2.2. Integral promotions and usual arithmetic conversions

1. **[Basic] What are integral promotions? To what types are `bool`, `char`, and `short` usually promoted?**

   **Answer.** Integral promotion converts `bool`, character types, and integer types with rank below `int` before most arithmetic. `bool` becomes `int`; `char` and `short` become `int` if `int` can represent all their values, otherwise `unsigned int`. The result depends on the implementation's ranges, especially for wide character types.

2. **[Basic] What happens in an arithmetic operation involving signed and unsigned integer types?**

   **Answer.** After promotions, the usual arithmetic conversions choose one common type. If signed and unsigned operands have the same rank, the signed operand converts to unsigned. With different ranks, the higher-ranked type may win; if the signed type can represent every value of the unsigned type, unsigned converts to signed, otherwise both convert to the unsigned counterpart of the signed type. Negative values can consequently become very large positive values.

3. **[Deep dive] How do the usual arithmetic conversions choose a common operand type?**

   **Answer.** The language first applies lvalue-to-rvalue and integral promotions. Floating operands then follow floating-conversion rank/subrank rules. For integers of the same signedness, the lower rank converts to the higher. For mixed signedness, rank and representable range determine whether conversion is to the signed type or its unsigned counterpart. Both operands are then operated on in that common type; the original destination type does not influence this choice.

4. **[Code] What is the comparison result, and why?**

   ```cpp
   int x = -1;
   unsigned y = 1;
   bool result = x < y;
   ```

   **Answer.** On the usual implementation where `int` and `unsigned` have the same rank, `x` converts to `unsigned`, producing `UINT_MAX`. The comparison is therefore false. This is a classic reason to avoid casually mixing signed values with container size types.

5. **[Code] What can go wrong in this loop?**

   ```cpp
   for (int i = values.size() - 1; i >= 0; --i) {
       use(values[i]);
   }
   ```

   **Answer.** `size()` returns an unsigned type. If the container is empty, `size() - 1` wraps to a huge value before conversion to `int`; that conversion is implementation-defined when unrepresentable. A large non-empty size can also exceed `int`. Prefer a forward transform, reverse iterators, or a safe countdown such as `for (std::size_t i = values.size(); i-- > 0;)`. C++20 also offers `std::ssize(values)` when a signed index is truly needed.

6. **[Deep dive] How does integer conversion rank differ from the actual size of a type?**

   **Answer.** Rank is a language-defined ordering used by promotions and conversions, not simply `sizeof`. No two standard signed integer types share a rank; rank increases from signed char through `long long`, and an unsigned type has the rank of its signed counterpart. Distinct types may have equal sizes but different ranks—for example `int` and `long` can both be 32-bit—so conversion rules cannot be inferred from byte size alone.

## 2.3. `enum` and `enum class`

1. **[Basic] How does an unscoped `enum` differ from `enum class`?**

   **Answer.** An unscoped enum injects enumerator names into its enclosing scope and its values can implicitly convert to integral types. A scoped enum (`enum class` or `enum struct`) keeps enumerators under the enum name and does not implicitly convert to integers. Scoped enums therefore avoid name pollution and accidental arithmetic/comparison with unrelated integers or enums.

2. **[Basic] How do you specify an enum's underlying type, and why might that be useful?**

   **Answer.** Write `enum class Status : std::uint8_t { ... };`. A fixed underlying type controls size/range where the implementation provides that integer type, helps define serialization and ABI contracts, and permits opaque forward declaration. Wire formats still need explicit encoding rules such as byte order and validation rather than raw object-byte copying.

3. **[Deep dive] Which implicit conversions are allowed for ordinary enums but prohibited for `enum class`?**

   **Answer.** A value of an unscoped enum promotes or converts to an integer, which then enables arithmetic and further standard conversions. A scoped-enum value has no implicit conversion to its underlying integer or to `bool`; use `static_cast<Underlying>(value)` explicitly. Neither form implicitly converts an arbitrary integer back to the enum.

4. **[Deep dive] When can an enum be forward-declared?**

   **Answer.** A scoped enum may be opaque-declared without spelling an underlying type because it defaults to `int`: `enum class Color;`. An unscoped enum needs a fixed underlying type: `enum Color : unsigned;`. The later definition must use the same underlying type. An opaque enum declaration is already a complete type of known size even though its enumerators are not yet known.

5. **[Code] Design a type-safe set of bit flags based on `enum class`.**

   **Answer.** Give each enumerator one bit, define only meaningful bitwise operators, operate through the underlying unsigned type, and provide a named test. Do not add implicit integer conversion.

   ```cpp
   #include <type_traits>

   enum class Permission : unsigned {
       none  = 0,
       read  = 1u << 0,
       write = 1u << 1,
       exec  = 1u << 2
   };

   constexpr Permission operator|(Permission a, Permission b) noexcept {
       using U = std::underlying_type_t<Permission>;
       return static_cast<Permission>(static_cast<U>(a) | static_cast<U>(b));
   }

   constexpr bool has(Permission set, Permission flag) noexcept {
       using U = std::underlying_type_t<Permission>;
       return (static_cast<U>(set) & static_cast<U>(flag)) == static_cast<U>(flag);
   }
   ```

## 2.4. `const`, `volatile`, pointers, and `std::byte`

1. **[Basic] Explain `const int*`, `int* const`, and `const int* const`.**

   **Answer.** `const int*` is a modifiable pointer to an `int` that cannot be modified through that pointer. `int* const` is a non-reseatable pointer through which the pointed-to `int` may be modified. `const int* const` is both a non-reseatable pointer and a read-only access path. Reading declarations from the identifier outward is a useful technique.

2. **[Basic] What does `const` on a non-static member function mean? Does it guarantee physical immutability?**

   **Answer.** It makes the implicit object parameter point to `const`, so ordinary non-`mutable` data members cannot be modified through `this`, and only `const` member functions may be called on them. It is a logical-constness contract, not deep or physical immutability: `mutable` members may change, pointed-to objects may change, global state may change, and another alias may modify a non-const original object.

3. **[Deep dive] What is `mutable` for?**

   **Answer.** A `mutable` data member may be modified inside a `const` member function. Typical uses are caches, lazy-computation flags, counters, and mutexes that do not change the object's externally observable logical value. It should not be used to hide semantic mutation that violates callers' expectations of `const`.

4. **[Basic] What does `volatile` guarantee and not guarantee? Is it suitable for inter-thread synchronization?**

   **Answer.** `volatile` tells the implementation that accesses are observable and must follow the language's volatile-access rules; it is intended for objects changed by mechanisms outside the abstract machine. It provides no atomicity, mutual exclusion, inter-thread happens-before relation, or general hardware memory ordering. Use `std::atomic` and synchronization primitives for threads.

5. **[Deep dive] Where is `volatile` genuinely used?**

   **Answer.** Its main low-level use is implementation/platform-specific access to memory-mapped device registers. It also appears with the narrow types permitted for communication with signal handlers, such as `volatile std::sig_atomic_t`, subject to severe restrictions. Exact semantics for devices require platform documentation, barriers, and often special intrinsics; `volatile` alone is not a complete device-memory model.

6. **[Basic] How does `std::byte` differ from `char`, `unsigned char`, and an integer type?**

   **Answer.** `std::byte` is an enum-like type representing a raw byte, designed for object representation and buffers. It supports bitwise operations but deliberately lacks ordinary arithmetic and implicit numeric conversions, making intent clearer than an integer. `char` and `unsigned char` are character/integer types and, like `std::byte`, may inspect object representation; `unsigned char` also supports normal arithmetic.

7. **[Code] Which assignments are valid?**

   ```cpp
   int value = 1;
   const int* p1 = &value;
   int* const p2 = &value;
   const int* const p3 = &value;
   ```

   **Answer.** `p1` may be reseated, for example `p1 = &other`, but `*p1 = 2` is ill-formed. `p2` cannot be reseated, but `*p2 = 2` is valid. `p3` can neither be reseated nor used to modify the integer. `value` itself is non-const, so direct modification and modification through `p2` remain valid; `const` on a pointer target restricts that access path, not necessarily the underlying object.

## 2.5. Implicit and explicit conversions

1. **[Basic] Which standard implicit conversions do you know?**

   **Answer.** Important categories include lvalue-to-rvalue, array-to-pointer, and function-to-pointer conversions; integral and floating promotions; numeric conversions; qualification conversions such as `T*` to `const T*`; null pointer conversions; pointer conversions along public inheritance or to `void*`; and boolean conversions. These combine in restricted standard conversion sequences used by initialization and overload resolution.

2. **[Basic] What is a converting constructor? How does `explicit` change its use?**

   **Answer.** A non-`explicit` constructor callable with one argument (possibly because later parameters have defaults) can define an implicit conversion to the class. Marking it `explicit` prevents its use in copy-initialization and most implicit conversion contexts, while direct initialization such as `T{x}` or `T(x)` remains allowed. Since C++20, `explicit(condition)` can make explicitness conditional.

3. **[Basic] How does a user-defined `operator T()` work, and when should it be `explicit`?**

   **Answer.** A conversion function is a non-static member written `operator T() const`; it lets an object convert to `T`. Make it `explicit` when automatic conversion could lose information, be costly, or participate unexpectedly in overloads and arithmetic. An `explicit operator bool()` is special in that it still works in contextual boolean positions such as `if`, `while`, and logical negation without enabling arbitrary integer conversions.

4. **[Deep dive] Why can a class with an implicit single-argument constructor create surprising overloads and temporaries?**

   **Answer.** Any compatible argument may silently create a temporary of the class, making overloads viable that the caller did not appear to request. This can select an unexpected overload, hide a unit/domain mistake, allocate resources, or introduce lifetime and performance costs. Value types intended as transparent conversions may allow it; domain types, owning types, and unit wrappers usually should be `explicit`.

5. **[Deep dive] How does the compiler choose the sequence standard conversion → user-defined conversion → standard conversion?**

   **Answer.** An implicit conversion sequence may contain an initial standard conversion sequence, at most one user-defined conversion (a converting constructor or conversion function), and a final standard conversion sequence. Overload resolution first identifies viable candidates, then ranks their sequences; it does not chain two user-defined conversions. During consideration of a converting constructor's own argument in some contexts, the exact permitted sequence is further restricted.

6. **[Code] Which lines compile if the constructor is `explicit`?**

   ```cpp
   class Seconds {
   public:
       explicit Seconds(int value);
   };

   void wait(Seconds);

   Seconds a(5);
   Seconds b{5};
   Seconds c = 5;
   wait(5);
   wait(Seconds{5});
   ```

   **Answer.** `a` and `b` compile because they use direct initialization. `c` fails because copy-initialization does not consider an explicit constructor as an implicit conversion. `wait(5)` fails for the same reason. `wait(Seconds{5})` compiles because the conversion is requested explicitly before the call.

## 2.6. C++ casts

1. **[Basic] When are `static_cast`, `dynamic_cast`, `const_cast`, and `reinterpret_cast` used?**

   **Answer.** `static_cast` expresses checked-at-compile-time conversions such as numeric conversion, explicit constructors, `void*` recovery, and hierarchy conversions whose runtime validity the programmer already knows. `dynamic_cast` performs RTTI-checked navigation in a polymorphic class hierarchy. `const_cast` changes cv-qualification. `reinterpret_cast` requests low-level representation-oriented conversions such as between certain pointer/integer types; it provides very few semantic safety guarantees.

2. **[Basic] Why is a C-style cast worse at expressing intent and harder to search for?**

   **Answer.** A C-style cast may attempt combinations equivalent to `const_cast`, `static_cast`, and `reinterpret_cast`, so reviewers cannot tell which dangerous capability was intended. Named casts are visually prominent, grep-friendly, and narrowly constrained. They turn a vague “force this conversion” into a specific claim that tools and reviewers can challenge.

3. **[Deep dive] What is required of the source type for a downcast using `dynamic_cast`?**

   **Answer.** For a runtime-checked downcast or cross-cast, the source must be a pointer or reference to a polymorphic class type—that is, a class with at least one virtual function—and the pointed/referred object must be within its lifetime. The target must be a pointer/reference to a complete class type (with specified exceptions such as `void*` targets). Access and unambiguous-public-base rules also matter.

4. **[Deep dive] How does a failed `dynamic_cast` to a pointer differ from one to a reference?**

   **Answer.** A failed pointer cast returns `nullptr`. A failed reference cast cannot return a null reference, so it throws `std::bad_cast`. This makes pointer form convenient for optional type tests and reference form suitable when failure is exceptional.

5. **[Deep dive] When is removing `const` with `const_cast` valid, and when does a subsequent write cause undefined behavior?**

   **Answer.** Removing qualification is allowed, and modifying through the result is valid only if the actual object was originally non-const. This is sometimes required for legacy APIs that incorrectly omit `const` but do not modify. If the underlying object was defined `const`, writing through the cast has undefined behavior, regardless of whether it happens to reside in writable memory.

6. **[Deep dive] Does `reinterpret_cast` guarantee that the result can be safely dereferenced? Discuss alignment, lifetime, and strict aliasing.**

   **Answer.** No. A pointer value may be produced while failing the target type's alignment requirement, not pointing to an object whose lifetime has begun, or violating the type-access/aliasing rules. Dereferencing then has undefined behavior. Use `std::memcpy` or C++20 `std::bit_cast` for representation conversion, properly aligned storage plus formal lifetime operations for object construction, and byte/character views for inspecting raw representation.

7. **[Code] Why is this code dangerous?**

   ```cpp
   const int value = 42;
   int* p = const_cast<int*>(&value);
   *p = 7;
   ```

   **Answer.** `value` is an actually const object. The cast itself is permitted, but modifying the object through `p` is undefined behavior. The compiler may place it in read-only storage or propagate the known constant `42`, so observing different values through `value` and `*p` is possible; the code cannot be repaired except by making the original object non-const or not writing.

## 2.7. Initialization forms, list-initialization, narrowing, and the most vexing parse

1. **[Basic] Compare default-, value-, direct-, copy-, and list-initialization.**

   **Answer.** `T x;` default-initializes: class default construction occurs, while a fundamental automatic object is indeterminate. `T x{};` value/list-initializes, commonly zero-initializing scalar state before relevant construction. `T x(args);` direct-initializes and considers explicit constructors. `T x = expr;` copy-initializes and excludes explicit constructors in the final conversion. Braced list-initialization (`T x{...}` or `T x = {...}`) has special overload rules and rejects narrowing; exact behavior also depends on whether `T` is an aggregate.

2. **[Basic] Which narrowing conversions does list-initialization reject?**

   **Answer.** It rejects floating-to-integer conversions; floating conversion to a lower-ranked type unless the expression is a suitable constant value under the applicable standard rules; integer/floating conversions whose value cannot be represented as required; and integer-to-floating conversions except allowed constant-expression cases. It also rejects integer conversions that cannot represent the constant value. The rule is checked at compile time and is stricter than ordinary assignment.

3. **[Deep dive] Why does the presence of a `std::initializer_list` constructor affect overload resolution for `{}`?**

   **Answer.** List-initialization of a class uses two phases. If the list is non-empty (or the class lacks an applicable default constructor for an empty list), constructors taking `std::initializer_list` are considered first and are strongly preferred. Only if no viable initializer-list constructor is found are other constructors considered. This is why adding such a constructor can change existing brace-initialization behavior.

4. **[Basic] What is the most vexing parse?**

   **Answer.** When a statement can be parsed as a declaration or an expression, C++ grammar may require the declaration interpretation. For example, `Widget w();` declares a function named `w` returning `Widget`; it does not create an object. Use `Widget w{};` for default construction.

5. **[Code] Explain each line.**

   ```cpp
   int a;
   int b{};
   int c = 3.14;
   int d{3.14};
   Widget w1();
   Widget w2{};
   ```

   **Answer.** Local automatic `a` is default-initialized and has an indeterminate value; reading it is invalid. `b` is value-initialized to zero. `c` is copy-initialized after converting `3.14` to `3`. `d` is ill-formed because braces reject the narrowing conversion. `w1` declares a no-argument function returning `Widget`. `w2` constructs a `Widget` using empty list-initialization, normally selecting its default constructor.

6. **[Code] Why do these vectors contain different values?**

   ```cpp
   std::vector<int> a(10, 2);
   std::vector<int> b{10, 2};
   ```

   **Answer.** Parentheses select the size/value constructor, so `a` contains ten elements, each equal to `2`. Braces prefer the `initializer_list<int>` constructor, so `b` contains two elements: `10` and `2`. This is a deliberate but sometimes surprising consequence of initializer-list priority.

## 2.8. `auto`, `decltype`, and structured bindings

1. **[Basic] How does `auto` deduce a type? What happens to top-level `const` and references?**

   **Answer.** Plain `auto` follows template argument deduction for a by-value parameter: references are not preserved and top-level cv-qualification is dropped, while low-level `const` remains (for example, a pointer to const stays a pointer to const). `auto&` and `auto&&` explicitly request reference deduction. Braced initializers have special rules: direct-list `auto x{1}` deduces `int`, while copy-list `auto x = {1}` deduces `std::initializer_list<int>` if possible.

2. **[Deep dive] How do `auto`, `auto&`, `const auto&`, and `auto&&` differ?**

   **Answer.** Plain `auto` creates a value, usually copying/moving and dropping top-level cv/ref. `auto&` binds only to an lvalue and preserves its constness through deduction. `const auto&` can bind to lvalues or temporaries and provides read-only access, extending a directly bound temporary's lifetime. In a deduced context, `auto&&` is a forwarding reference: it becomes an lvalue reference for lvalue initializers and an rvalue reference for rvalue initializers.

3. **[Basic] What are the rules for `decltype(name)` and `decltype((expression))`?**

   **Answer.** For an unparenthesized id-expression or member access, `decltype` yields the entity's declared type. Otherwise it reflects value category: an lvalue expression gives `T&`, an xvalue gives `T&&`, and a prvalue gives `T`. Parentheses therefore matter: if `x` is `const int`, `decltype(x)` is `const int`, while `decltype((x))` is `const int&` because `(x)` is an lvalue.

4. **[Deep dive] How does `decltype(auto)` differ from `auto` as a function return type?**

   **Answer.** `auto` return deduction uses by-value-style deduction and usually drops top-level references/cv. `decltype(auto)` applies `decltype` to the returned expression and can preserve references and value category. This makes it useful for forwarding accessors but dangerous if parentheses accidentally return a reference to a local object.

5. **[Basic] How do structured bindings work with arrays, tuple-like types, and classes?**

   **Answer.** A structured binding first creates a hidden binding object. Arrays bind by element. Tuple-like types use `std::tuple_size`, `std::tuple_element`, and `get<I>`. Otherwise, an eligible class binds to its accessible non-static data members under the language's restrictions. The introduced names are bindings, not ordinary reference variables, which explains some subtle `decltype` behavior.

6. **[Deep dive] When does a structured binding copy, and when does it refer to the source object?**

   **Answer.** `auto [a, b] = object;` initializes the hidden object by value, so it copies or moves the source and the names refer to subobjects of that copy. `auto& [a, b] = object;` binds the hidden object by lvalue reference; `const auto&` provides a read-only binding and can extend a temporary; `auto&&` follows forwarding-reference-like binding. Whether later writes affect the original follows that hidden object's binding.

7. **[Code] Determine the variable types.**

   ```cpp
   const int x = 42;
   const int& rx = x;

   auto a = rx;
   auto& b = rx;
   auto&& c = rx;
   decltype(x) d = 1;
   decltype((x)) e = x;
   ```

   **Answer.** `a` is `int`: plain `auto` drops the reference and top-level `const`. `b` is `const int&`. Because the initializer `rx` is an lvalue, forwarding-reference deduction makes `c` `const int&`. `d` is `const int`, because unparenthesized `x` yields its declared type. `e` is `const int&`, because `(x)` is an lvalue expression.

## 2.9. `constexpr`, `consteval`, and `constinit`

1. **[Basic] How does `const` differ from `constexpr`?**

   **Answer.** `const` prevents modification through that object/access path but does not by itself require compile-time initialization. `constexpr` declares an entity usable in constant evaluation when the other requirements are met; a `constexpr` object is const and must be constant-initialized. A `constexpr` function is eligible for compile-time evaluation but is not necessarily evaluated at compile time.

2. **[Basic] Can a `constexpr` function run at runtime? What determines this?**

   **Answer.** Yes. If called in a context that requires a constant expression and supplied suitable arguments, it must be successfully constant-evaluated. In an ordinary runtime context, the same function may execute at runtime; the optimizer may still fold it. Compile-time evaluation is driven by the context and evaluability, not merely by the keyword.

3. **[Basic] What does `consteval` guarantee?**

   **Answer.** It declares an immediate function. Every potentially evaluated call whose innermost non-block scope is not an immediate-function context must produce a constant expression; otherwise the program is ill-formed. This enforces compile-time evaluation at the call boundary, unlike `constexpr`.

4. **[Basic] What does `constinit` guarantee, and what does it not guarantee about `const`?**

   **Answer.** `constinit` requires a static- or thread-storage variable to have static initialization rather than deferred dynamic initialization. It does not make the variable immutable and does not make every later read a constant expression. For example, `constinit int counter = 0;` is mutable but initialized before dynamic initialization begins.

5. **[Deep dive] What are a constant expression and a literal type?**

   **Answer.** A constant expression is an expression that satisfies the standard's restrictions for compile-time evaluation—roughly, evaluation may not perform disallowed runtime-only operations or encounter undefined behavior. A literal type is a type whose objects can potentially participate in constant expressions; it has appropriate constexpr construction/destruction and literal-type subobjects. Being a literal type makes constant evaluation possible, not automatic.

6. **[Deep dive] How does `constinit` help avoid dynamic-initialization-order problems for global objects?**

   **Answer.** It makes the compiler reject an initializer that would require dynamic initialization. The object is therefore initialized during the static-initialization phase, before ordinary cross-translation-unit dynamic initialization whose order is often unspecified. It does not fix dependencies between objects that genuinely require dynamic initialization; function-local statics or redesigned dependencies may be needed there.

7. **[Code] Which declarations are valid, and when are their expressions evaluated?**

   ```cpp
   constexpr int square(int x) { return x * x; }
   consteval int checked(int x) { return x > 0 ? x : throw "bad"; }

   int n = runtime_value();
   int a = square(n);
   constexpr int b = square(4);
   int c = checked(4);
   ```

   **Answer.** `n` is runtime-initialized. `a` is valid and `square(n)` may run at runtime because the context does not require a constant expression. `b` is valid and must be evaluated at compile time. `c` is also valid: the `consteval` call must be evaluated at compile time even though `c` itself is a mutable runtime object initialized with the resulting value `4`. `checked(-1)` would be ill-formed because constant evaluation would execute the `throw`.

## 2.10. Init-statements in `if` and `switch`

1. **[Basic] What is the init-statement added to `if` and `switch` in C++17?**

   **Answer.** It is a declaration or expression placed before the condition and separated by a semicolon: `if (auto it = map.find(key); it != map.end())`. It performs setup while keeping the initialized name attached to the control statement.

2. **[Basic] What is the scope of a variable declared in an init-statement?**

   **Answer.** Its scope includes the condition and both the controlled statement and `else` branch, or all labels/statements of the associated `switch`. It ends after the complete `if`/`switch`, so the variable cannot leak into subsequent code.

3. **[Deep dive] How does this feature shorten the lifetime of a lock guard, iterator, or lookup result?**

   **Answer.** The helper exists only for the decision and its branches, so destruction occurs immediately after the statement rather than at the end of a larger surrounding block. For a lock guard, this precisely bounds the critical section; for an iterator or result, it prevents accidental later use and avoids name collisions.

4. **[Code] Rewrite this code with the smallest variable scope.**

   ```cpp
   auto it = cache.find(key);
   if (it != cache.end()) {
       return it->second;
   }
   ```

   **Answer.** Put the lookup in the init-statement:

   ```cpp
   if (auto it = cache.find(key); it != cache.end()) {
       return it->second;
   }
   ```

   `it` is available inside both branches if an `else` is added, and is destroyed after the complete `if`.

---

# 3. Expressions, functions, and lambdas

## 3.1. Operator precedence, value categories, and dangling pointers/references

1. **[Basic] Why know operator precedence and associativity if production code uses parentheses?**

   **Answer.** They determine how every unparenthesized expression is parsed and are essential when reviewing existing code, macros, compiler diagnostics, or overload expressions. Associativity decides grouping among operators at the same precedence; it does not determine runtime evaluation order. Parentheses should still be used when the intended grouping is not immediately obvious.

2. **[Code] How are `*p++`, `a && b || c`, `x = y = z`, and `a & b == 0` grouped?**

   **Answer.** They parse as `*(p++)`, `(a && b) || c`, `x = (y = z)`, and `a & (b == 0)`. Postfix increment binds more tightly than unary `*`; `&&` binds more tightly than `||`; assignment is right-associative; equality binds more tightly than bitwise AND. The last expression is a common bug—the likely intent is `(a & b) == 0`.

3. **[Basic] What are lvalues and rvalues in practical terms?**

   **Answer.** An lvalue expression identifies an object or function with stable identity and can normally appear on the left of assignment when non-const. “Rvalue” is the umbrella category for prvalues and xvalues: expressions representing pure values or objects whose resources may be reused. Value category belongs to an expression, not permanently to an object; a named variable is an lvalue expression even if its declared type is `T&&`.

4. **[Deep dive] Distinguish lvalue, xvalue, prvalue, and the combined glvalue category.**

   **Answer.** A glvalue identifies an object/function; it consists of lvalues and xvalues. An lvalue identifies an object not designated for resource reuse. An xvalue (“expiring value”), such as `std::move(x)`, still identifies an object but permits moving from it. A prvalue computes a value and, when needed, initializes/materializes an object. The combined rvalue category consists of prvalues and xvalues.

5. **[Basic] What are dangling pointers and references? Name common causes.**

   **Answer.** They retain an address or binding after the referred object's lifetime has ended. Causes include returning addresses/references to local variables, capturing locals by reference in an escaping lambda, keeping pointers/iterators across container reallocation or erasure, using a view of a destroyed string/buffer, deleting an object while aliases remain, and binding through a temporary without lifetime extension. Creating a dangling value is not always immediately undefined, but using it as if the object still existed generally is.

6. **[Deep dive] When does binding `const T&` extend a temporary's lifetime, and when does it not?**

   **Answer.** A temporary directly bound to a local `const T&` has its lifetime extended to that reference's scope; similar rules cover certain reference members and structured bindings with important context-specific details. Lifetime is not “passed on”: returning that reference, storing another reference obtained through it, or binding to a reference returned by a function does not further extend the original temporary. A temporary bound to a reference parameter lives only to the end of the full expression containing the call, so retaining the parameter is unsafe. A reference in a `new` initializer also does not make the temporary live as long as the allocated object.

7. **[Code] Find the lifetime problems.**

   ```cpp
   const std::string& name() {
       return std::string{"temporary"};
   }

   std::string_view prefix() {
       std::string value = "abcdef";
       return value.substr(0, 3);
   }
   ```

   **Answer.** `name()` returns a reference to a temporary destroyed as the return expression completes; callers receive a dangling reference. Return `std::string` by value. In `prefix()`, `value.substr` returns a temporary `std::string`; the returned `string_view` refers to that destroyed temporary (and `value` also dies on return). Return an owning `std::string`, or return a view only into storage whose lifetime is guaranteed by the API.

## 3.2. `sizeof`, `alignof`, and pointers to members

1. **[Basic] What does `sizeof` return and in which units? Is `sizeof(char) == 1` always true?**

   **Answer.** `sizeof` yields a compile-time `std::size_t` count of bytes occupied by a type/object, including padding and array elements. `sizeof(char)`, `sizeof(signed char)`, and `sizeof(unsigned char)` are always `1`. A C++ byte is the size of `char` and has at least 8 bits; it need not be an 8-bit octet.

2. **[Basic] Does `sizeof(expression)` evaluate the expression? What nuances exist?**

   **Answer.** Normally its operand is unevaluated, so `sizeof(i++)` does not increment `i`. The expression must still generally be well-formed and its type may trigger template instantiation or overload analysis. Arrays do not decay to pointers in `sizeof`. Standard C++ has no variable-length arrays; implementations supporting VLAs as extensions may evaluate a size expression, which is not portable C++ behavior.

3. **[Deep dive] Why is an empty class not size zero, and what affects class size?**

   **Answer.** Distinct complete objects must have distinct addresses, so an empty class normally has size at least one. Size can include data members, base subobjects, alignment padding, tail padding, and implementation machinery for virtual functions/inheritance. Empty base optimization and C++20 `[[no_unique_address]]` can allow empty subobjects to occupy no additional space when address-identity rules permit it. Layout is ABI- and implementation-dependent.

4. **[Basic] What does `alignof(T)` report, and how is alignment related to padding?**

   **Answer.** `alignof(T)` returns the alignment requirement of `T` in bytes: valid object addresses must satisfy it. Compilers insert padding between members so each is aligned, and tail padding so consecutive array elements are correctly aligned. Member ordering can therefore change `sizeof`, though one should not reorder when an external ABI/layout contract controls the type.

5. **[Deep dive] How does a pointer to a data member or member function differ from an ordinary pointer?**

   **Answer.** A pointer-to-member describes a member relative to an object of a particular class; it is not necessarily a raw address and can encode adjustments required by inheritance. It must be applied to an object using `.*` or to a pointer using `->*`. Its representation and size are ABI-dependent, and a member-function pointer is not convertible to an ordinary function pointer because non-static members need an object.

6. **[Code] How do you use these pointers to members?**

   ```cpp
   struct Device {
       int id;
       void reset(int mode);
   };

   int Device::* data_member = &Device::id;
   void (Device::* member_function)(int) = &Device::reset;
   ```

   **Answer.** Apply them to an instance or pointer. `std::invoke` is a generic alternative that handles member pointers and ordinary callables.

   ```cpp
   Device d{};
   Device* p = &d;

   d.*data_member = 7;
   p->*data_member = 8;
   (d.*member_function)(1);
   (p->*member_function)(2);

   std::invoke(member_function, d, 3);
   ```

## 3.3. Function signatures, overloading, and default arguments

1. **[Basic] What belongs to the signature of a free/member function? Is the return type included?**

   **Answer.** For ordinary overloading, the relevant signature includes the function name and parameter-type list; a non-static member's signature also distinguishes its class and cv/ref qualifiers. The return type is not included, and neither are parameter names or default arguments. Standard terminology has additional precise signature definitions for templates and special cases; `noexcept` has been part of the function type since C++17 but still cannot alone distinguish overloads.

2. **[Basic] Why can functions not be overloaded only by return type?**

   **Answer.** Overload resolution must choose a function from the call expression before—or sometimes without—knowing a destination type, and C++ does not generally use the expected return type to select an overload. Two declarations differing only in return type therefore conflict. Use different names, parameters, a tag, or a templated target type explicitly supplied by the caller.

3. **[Deep dive] Which stages does overload resolution perform?**

   **Answer.** Name lookup and argument-dependent lookup build the candidate set. The compiler adds applicable built-in, member, rewritten, or template candidates, substitutes/deduces templates and checks constraints, then removes non-viable candidates based on arity and convertible arguments. It ranks the implicit conversion sequence for each argument and applies tie-breakers such as non-template preference, template partial ordering, and constraint subsumption. If no unique best viable function remains, the call is ill-formed.

4. **[Basic] Where should default arguments be written: declaration or definition?**

   **Answer.** Put them in the declaration visible to callers, normally the public header. A default may be added by a later declaration in the same scope for parameters that still need one, but it cannot be redefined in the same scope. Repeating it on an out-of-line definition is usually an error and creates maintenance risk.

5. **[Deep dive] When and in which context is a default argument substituted? Is it virtual?**

   **Answer.** The expression is bound and checked at the declaration point, but evaluated each time a call omits that argument. Selection is based on the declaration found through the call's static type; default arguments are not virtual. Virtual dispatch may choose a derived implementation after the base declaration has already supplied the argument.

6. **[Code] What is called, and why?**

   ```cpp
   struct Base {
       virtual void print(int width = 10);
   };

   struct Derived : Base {
       void print(int width = 20) override;
   };

   Base* p = new Derived;
   p->print();
   ```

   **Answer.** The static type `Base*` supplies the default argument `10`; virtual dispatch then invokes `Derived::print(10)`. The derived default `20` would be used for a call whose static lookup finds `Derived::print`. Avoid different defaults on overrides; use a non-virtual wrapper or require the argument explicitly.

7. **[Code] Is this call ambiguous?**

   ```cpp
   void f(long);
   void f(double);
   f(1);
   ```

   **Answer.** Yes. Converting `int` to `long` and `int` to `double` are both standard conversion sequences of conversion rank; neither is better under the applicable tie-breakers. An explicit cast/literal suffix or an `f(int)` overload resolves it.

## 3.4. Returning values, references/pointers, NRVO, and copy elision

1. **[Basic] When should a function return a value, reference, raw pointer, or smart pointer?**

   **Answer.** Return by value for a new independent value—the default choice. Return `T&`/`const T&` for a non-null borrowed object whose lifetime clearly exceeds the use; use a raw pointer when a borrowed result may be absent. Return `std::unique_ptr<T>` to transfer exclusive ownership and `std::shared_ptr<T>` only when the API truly creates/shared ownership. Types such as `std::optional<T>`, references via `std::reference_wrapper`, iterators, or views may express other contracts more clearly.

2. **[Basic] What lifetime requirements apply when returning a reference or pointer?**

   **Answer.** The referred object must remain alive for every subsequent use, and the pointer/reference must not be invalidated by mutation, reallocation, destruction, or replacement. Never return a reference/address to an automatic local or a temporary. The API should communicate ownership, nullability, stability, and invalidation rules because the type alone may not express them.

3. **[Basic] What are RVO and NRVO?**

   **Answer.** Return Value Optimization constructs a returned class object directly in its destination instead of creating and copying/moving an intermediate. NRVO is the named form, where a local automatic object such as `return result;` is constructed into the caller's result storage. “RVO” also commonly refers to unnamed temporary cases.

4. **[Deep dive] Where is copy elision guaranteed since C++17, and where is NRVO still optional?**

   **Answer.** In common prvalue cases such as `return T{args};` from a function returning `T`, the prvalue initializes the result object directly; no source temporary exists, so copy/move constructors need not be available. Initializing a `T` from a same-type prvalue has analogous guaranteed elision. Returning a named local `return local;` is NRVO and remains permitted rather than mandatory; if not performed, implicit move may be attempted.

5. **[Deep dive] Why can `return std::move(local);` inhibit NRVO?**

   **Answer.** NRVO recognizes a specific named local operand. Wrapping it in `std::move` produces an xvalue expression instead, so that NRVO rule no longer applies and a move construction is normally required. Write `return local;`; the compiler may apply NRVO, and if it does not, return statements have special rules that can treat eligible locals as movable.

6. **[Code] Evaluate these interfaces and suggest clearer alternatives.**

   ```cpp
   const Widget& make_widget();
   Widget* find_widget(Id id);
   std::unique_ptr<Widget> create_widget();
   Widget get_widget();
   ```

   **Answer.** `make_widget` is suspicious: “make” implies a new value, while a reference implies borrowing and demands a documented stable owner; prefer `Widget make_widget()` unless returning a known long-lived cache. `find_widget` as a raw pointer reasonably means optional non-owning access, preferably with documented invalidation (or a reference-like optional abstraction). `create_widget` clearly transfers exclusive ownership and is appropriate for polymorphism/dynamic lifetime. `get_widget` returns an independent value and is often the simplest, fastest interface thanks to elision/move.

## 3.5. `noexcept`

1. **[Basic] What does the `noexcept` specifier mean?**

   **Answer.** `noexcept` or `noexcept(true)` declares that a function is non-throwing as part of its contract; `noexcept(false)` permits exceptions. It does not prevent code inside from throwing. It lets callers, generic libraries, optimizers, and traits reason about failure behavior.

2. **[Basic] What happens if an exception leaves a `noexcept` function?**

   **Answer.** `std::terminate()` is called; the exception cannot be caught by a caller outside that function. Destruction of fully constructed local objects before termination is not something portable code should rely on at that boundary. Therefore mark a function `noexcept` only when it can uphold the contract or deliberately terminate on failure.

3. **[Deep dive] Why should a move constructor often be `noexcept`, and how does that affect `std::vector`?**

   **Answer.** During reallocation, `vector` wants to preserve its strong exception guarantee. If moving an element could throw and copying is available, it commonly uses copying (`std::move_if_noexcept` logic), because a partially moved old buffer may be hard to roll back. A truthful `noexcept` move lets the container relocate efficiently. It must not be added if member moves can actually throw.

4. **[Deep dive] What are conditional `noexcept` and the `noexcept(expression)` operator?**

   **Answer.** A specification such as `noexcept(std::is_nothrow_move_constructible_v<T>)` makes the contract depend on a compile-time condition. The operator `noexcept(expr)` is an unevaluated boolean constant expression reporting whether `expr` is statically known not to throw. Generic wrappers use it to mirror the wrapped operation.

5. **[Deep dive] Is `noexcept` part of a function's type in modern C++?**

   **Answer.** Since C++17, non-throwing status is part of the function type, enabling conversions such as pointer-to-nonthrowing function to pointer-to-potentially-throwing function but not the reverse. It is not part of the function signature used to permit overloading, so two functions cannot differ only by `noexcept`.

6. **[Code] Express conditional `noexcept` correctly for this wrapper.**

   ```cpp
   template<class F, class... Args>
   decltype(auto) call(F&& f, Args&&... args) /* noexcept(?) */ {
       return std::forward<F>(f)(std::forward<Args>(args)...);
   }
   ```

   **Answer.** Mirror the exact invocation in an unevaluated `noexcept` expression; `std::invoke` also supports member pointers.

   ```cpp
   template<class F, class... Args>
   decltype(auto) call(F&& f, Args&&... args)
       noexcept(noexcept(std::invoke(std::forward<F>(f),
                                     std::forward<Args>(args)...))) {
       return std::invoke(std::forward<F>(f),
                          std::forward<Args>(args)...);
   }
   ```

## 3.6. Function pointers and `std::function`

1. **[Basic] How do you declare and call a function pointer?**

   **Answer.** For `bool handle(const Event&, void*)`, write `using Callback = bool (*)(const Event&, void*); Callback cb = &handle; bool ok = cb(event, context);`. The `&` and explicit dereference at the call are optional for ordinary functions/function pointers. A function pointer cannot directly store a capturing lambda or stateful function object.

2. **[Basic] Which callable objects can `std::function` store?**

   **Answer.** It can type-erase free functions, function pointers, capturing/noncapturing lambdas, bind expressions, and stateful function objects whose invocation matches its signature. In C++17/20, the stored target must be copy-constructible because `std::function` itself is copyable. Empty invocation throws `std::bad_function_call`.

3. **[Basic] Compare a callable template parameter, function pointer, and `std::function` in flexibility and cost.**

   **Answer.** A template accepts almost any callable with zero type-erasure overhead and strong inlining opportunities, but produces a different instantiation per type and must usually live in headers. A function pointer is small and cheap but supports only compatible stateless functions/lambdas and a fixed ABI-style signature. `std::function` offers one copyable runtime-polymorphic type and can own state, at the cost of indirect invocation, possible allocation, and weaker optimization.

4. **[Deep dive] What is type erasure, and how is it related to `std::function`?**

   **Answer.** Type erasure hides the concrete callable type behind a uniform runtime interface while retaining operations needed to invoke, copy, move, and destroy it. `std::function<R(Args...)>` stores an erased target and dispatches through implementation-managed function pointers/vtable-like machinery. Unlike inheritance, the target type need not derive from a common base.

5. **[Deep dive] When can `std::function` allocate dynamically? What is small-buffer optimization?**

   **Answer.** A target too large, too highly aligned, or otherwise unsuitable for the implementation's inline storage may be allocated dynamically. Small-buffer optimization stores eligible small callables directly inside the `std::function`, avoiding an allocation. The buffer size and exact eligibility are implementation details and must not be assumed as a portable performance guarantee.

6. **[Deep dive] Can `std::function` store a move-only callable in C++17/C++20? What alternatives exist?**

   **Answer.** Not directly: its target must be copy-constructible. Alternatives include a templated API, a custom move-only type-erased wrapper, storing shared state behind a copyable lambda, or a non-owning callable reference when lifetime is externally guaranteed. C++23 adds `std::move_only_function`, but it is outside a strict C++20 baseline.

7. **[Code] Declare a callback type for `bool(const Event&, void*)` and compare function-pointer and `std::function` APIs.**

   **Answer.** A C-style form is `using Callback = bool (*)(const Event&, void*); void set_callback(Callback, void* context);`. It is ABI-friendly and cheap; callers pass state separately. A C++ form is `using Callback = std::function<bool(const Event&)>; void set_callback(Callback);`, which can own captured state and is easier to compose, but may allocate and adds erased-call overhead. Choose based on boundary, ownership, and performance requirements.

## 3.7. Lambdas

1. **[Basic] What is a lambda at the type level? Do two textually identical lambdas have the same type?**

   **Answer.** Each lambda expression creates an unnamed, unique closure class type with a call operator and data members representing captures. Two separate lambda expressions have distinct types even if their tokens are identical. `auto` or templates preserve the exact closure type; type erasure such as `std::function` provides a common runtime type when needed.

2. **[Basic] Explain `[=]`, `[&]`, `[x]`, `[&x]`, and init-capture `[x = expression]`.**

   **Answer.** `[=]` implicitly captures odr-used automatic locals by value; `[&]` captures them by reference. `[x]` explicitly copies `x`, while `[&x]` stores reference-like access to the original. `[x = expression]` declares a new closure member initialized from the expression and is the way to rename, transform, or move a value, for example `[p = std::move(ptr)]`.

3. **[Basic] How does capturing `this` differ from capturing `*this`?**

   **Answer.** `[this]` captures the pointer, so member access operates on the original object and becomes invalid if that object dies. `[*this]` (C++17) captures a copy of the current object, so the closure owns independent state and can outlive the original, subject to the class being copyable. In a default by-copy capture, accessing members historically captures `this`, not an implicit deep copy of the object.

4. **[Basic] What does `mutable` do for a lambda with value captures?**

   **Answer.** A lambda's call operator is `const` by default, so its by-value capture members cannot normally be modified. `mutable` removes that implicit `const` from the call operator, allowing the closure's private copies to change across calls. It does not make a captured-by-reference original const or non-const; that follows the referred object.

5. **[Basic] What is a generic lambda with `auto` parameters?**

   **Answer.** It is a lambda whose call operator is a function template, for example `[](const auto& x) { return x.size(); }`. Each argument type instantiates an appropriate operator. C++20 also permits an explicit template parameter list on a lambda, enabling constraints and relationships between parameters.

6. **[Deep dive] Which lifetime rules matter when capturing locals or `this` by reference?**

   **Answer.** Reference capture does not extend an object's lifetime. The closure must not use a local after its scope ends or use `this` after the owning object is destroyed; this is especially important for callbacks, detached threads, and asynchronous work. Capture required values by value/move, use `weak_ptr` when observing shared-lifetime objects, or otherwise make the lifetime relationship explicit.

7. **[Deep dive] Can a noncapturing lambda convert to a function pointer?**

   **Answer.** Yes, a noncapturing lambda has a conversion to a pointer to a function with a compatible signature; unary `+` is sometimes used to force it. A capturing lambda needs closure state and cannot convert to an ordinary function pointer. Generic noncapturing lambdas can convert when a particular function-pointer target supplies parameter types and the instantiation is valid.

8. **[Code] Find the lifetime error.**

   ```cpp
   std::function<int()> make_counter() {
       int value = 0;
       return [&value]() mutable { return ++value; };
   }
   ```

   **Answer.** The returned closure holds a reference to local `value`, whose lifetime ends when `make_counter` returns. Calling it is undefined behavior. Capture the state by value: `return [value = 0]() mutable { return ++value; };`. Each copied `std::function` then normally has its own copied counter state; use shared ownership if copies must share one counter.

9. **[Code] How do the results differ?**

   ```cpp
   struct Worker {
       int value = 42;

       auto by_pointer() { return [this] { return value; }; }
       auto by_object()  { return [*this] { return value; }; }
   };
   ```

   **Answer.** The first closure reads the current `value` of the original `Worker`; changes are observed, but invocation after that worker dies is undefined. The second owns a snapshot copy made when the lambda is created; later changes to the original are not observed, and the closure can outlive it. Because its call operator is const by default, it also views that captured copy as const unless the lambda is `mutable`.

---

# 4. Classes, inheritance, and polymorphism

## 4.1. Encapsulation, invariants, `this`, and `friend`

1. **[Basic] What is encapsulation, and why is it more than declaring fields `private`?**

   **Answer.** Encapsulation groups state with the operations and rules that govern it, exposing a stable abstraction while hiding changeable representation and invalid transitions. Private fields help enforce the boundary, but a class with trivial setters for every field may still leak its representation and invariants. Good encapsulation makes correct use easy, invalid states hard or impossible, and implementation changes local.

2. **[Basic] What is a class invariant, and who must preserve it?**

   **Answer.** An invariant is a property that must hold whenever an object's public operations are entered or successfully return—for example `0 <= percentage <= 100` or `size_ <= capacity_`. Constructors establish it; every mutating member, assignment/swap operation, and trusted friend must preserve or restore it. During a private operation it may be temporarily broken if no observer can see the state and exception safety is maintained.

3. **[Basic] What is `this`, and in which functions is it available?**

   **Answer.** `this` is the implicit pointer to the object on which a non-static member function is executing. It is available in non-static member functions and certain member-related contexts, not in static member functions because they have no object. Constructors and destructors have `this`, but the object's dynamic-type behavior is restricted while construction/destruction is in progress.

4. **[Deep dive] What is the type of `this` in const and non-const member functions?**

   **Answer.** In a member of class `X`, it is a prvalue pointer of type `X*` in a non-const member and `const X*` in a `const` member (with analogous `volatile` combinations). The pointer itself is not a reseatable user variable; conceptually, the cv-qualification applies to the pointed-to object.

5. **[Basic] What does `friend` grant? Is friendship inherited or reciprocal?**

   **Answer.** A friend function or class may access the granting class's private and protected members. Friendship is neither inherited nor transitive, and it is not reciprocal unless separately declared. A friend declaration grants access; it does not make the friend a member.

6. **[Deep dive] When is `friend` justified, and when can it hide a poor responsibility boundary?**

   **Answer.** It is reasonable for tightly coupled operations that are logically part of the abstraction but must be non-members, such as symmetric operators, serialization adapters, factories, or carefully scoped test helpers. If many unrelated classes need friendship, or a friend manipulates representation without preserving invariants, the class likely exposes the wrong abstraction. Prefer a narrow public/private helper interface or moving the responsibility when that better expresses ownership.

7. **[Code] Redesign this type so an invalid object cannot be created.**

   ```cpp
   struct Percentage {
       int value;
   };
   ```

   **Answer.** Make storage private and validate construction. If failure is expected, use a factory returning `std::optional` or an error type; if it is exceptional, a throwing constructor is reasonable.

   ```cpp
   class Percentage {
   public:
       static std::optional<Percentage> make(int value) noexcept {
           if (value < 0 || value > 100) return std::nullopt;
           return Percentage(value);
       }

       int value() const noexcept { return value_; }

   private:
       explicit Percentage(int value) noexcept : value_(value) {}
       int value_;
   };
   ```

   Every public operation must continue to preserve the `0..100` invariant.

## 4.2. Constructors, destructor, `explicit`, `= default`, `= delete`, and Rules of 0/3/5

1. **[Basic] Which special member functions can the compiler generate?**

   **Answer.** The default constructor, destructor, copy constructor, copy-assignment operator, move constructor, and move-assignment operator are special member functions. The compiler may implicitly declare and define them when their individual conditions are met; a generated function can be defined as deleted if a base/member cannot perform the required operation. “Generated” never means that all six are always present.

2. **[Basic] State the Rule of Zero, Rule of Three, and Rule of Five.**

   **Answer.** Rule of Zero: compose RAII types so the class declares no resource-management special members. Rule of Three: if a pre-move-semantics class needs a custom destructor, copy constructor, or copy assignment, it likely needs all three. Rule of Five extends that reasoning to move construction and move assignment. These are design heuristics; explicitly deleting operations or having asymmetric semantics can be correct.

3. **[Deep dive] When do user declarations suppress implicit special members?**

   **Answer.** A move constructor/assignment is implicitly declared only when there is no user-declared copy constructor, move constructor, copy assignment, move assignment, or destructor. Declaring a move operation causes the corresponding implicitly declared copy operations to be deleted unless copies are explicitly supplied. A user-declared destructor suppresses implicit moves but historically does not by itself suppress copies (their implicit generation is deprecated in this situation). Each member can also become deleted because a base or field lacks that operation.

4. **[Basic] What are `= default` and `= delete` for?**

   **Answer.** `= default` explicitly requests the compiler-defined semantics and documents intent; it can restore a special member that another declaration would suppress. `= delete` declares a function but makes any selected use ill-formed, useful for prohibiting copying, dangerous conversions, or unwanted overloads. Deleted functions still participate in overload resolution, producing a precise error when selected.

5. **[Basic] When should a constructor be `explicit`? What about constructors with several parameters and default arguments?**

   **Answer.** Mark a constructor `explicit` when implicit conversion to the class would be surprising, lossy, expensive, or semantically wrong; this is the safe default for conversion-capable constructors. “Single-argument constructor” really means a constructor callable with one argument, so `T(int, int mode = 0)` can also convert and should be considered. Since C++11, `explicit` may be applied to constructors with any number of parameters, affecting copy-list initialization; C++20 adds conditional `explicit(bool)`.

6. **[Deep dive] How do user-declared, user-provided, and implicitly declared special members differ?**

   **Answer.** A member is user-declared when its declaration appears explicitly, including `= default` or `= delete`. It is user-provided when it is user-declared and not explicitly defaulted/deleted on its first declaration; an out-of-class `= default` is therefore user-provided. These distinctions affect triviality, implicit `constexpr`/`noexcept`, zero-initialization details, aggregate eligibility, and suppression of other special members. An implicitly declared function is introduced by the compiler and may later be implicitly defined or deleted.

7. **[Code] Which operations are available for this class, and why?**

   ```cpp
   struct File {
       File(const char* path);
       ~File();

       File(const File&) = delete;
       File& operator=(const File&) = delete;
   };
   ```

   **Answer.** Construction from `const char*` and destruction are available. Default construction is not implicitly declared because another constructor exists. Copy construction/assignment are explicitly deleted. Move construction/assignment are not implicitly declared because the destructor is user-declared (and copy operations are user-declared), so the type is neither copyable nor movable. If transfer is meaningful, implement or explicitly default move operations with correct handle-reset behavior and preferably `noexcept`.

8. **[Code] Implement or describe a Rule-of-Zero class that owns a dynamic buffer.**

   **Answer.** Store the bytes in an existing RAII value type so no custom special members are needed. `std::vector<std::byte>` provides ownership, size, copying, moving, and exception safety.

   ```cpp
   class Buffer {
   public:
       explicit Buffer(std::size_t size) : data_(size) {}

       std::byte* data() noexcept { return data_.data(); }
       const std::byte* data() const noexcept { return data_.data(); }
       std::size_t size() const noexcept { return data_.size(); }

   private:
       std::vector<std::byte> data_;
   };
   ```

   The compiler-generated copy/move/destructor now have the desired semantics.

## 4.3. Member initialization order and aggregate initialization

1. **[Basic] In what order are virtual bases, direct bases, and data members initialized?**

   **Answer.** For the most-derived object: virtual base classes are initialized first in depth-first left-to-right order; then direct non-virtual bases in their base-specifier-list order; then non-static data members in their declaration order; finally the constructor body runs. Destruction occurs in reverse. The most-derived constructor initializes virtual bases.

2. **[Basic] Does the order in a member-initializer list change actual initialization order?**

   **Answer.** No. Declaration order controls, regardless of initializer-list order. Write initializers in the same order as declarations so dependencies are visible and compilers do not warn. Relying on the textual initializer order can lead to reading an uninitialized member.

3. **[Code] What is wrong here?**

   ```cpp
   struct Buffer {
       Buffer(std::size_t size)
           : data_(new char[size_]), size_(size) {}

       char* data_;
       std::size_t size_;
   };
   ```

   **Answer.** `data_` is declared first, so it is initialized first and reads `size_` before `size_` has been initialized; the behavior is undefined/erroneous depending on the language version's terminology. Reorder the members so `size_` comes first or use the constructor parameter `size` directly. Better, replace the raw owning pointer with `std::vector<char>` or `std::unique_ptr<char[]>` to avoid manual Rule-of-Five work.

4. **[Basic] What are an aggregate type and aggregate initialization?**

   **Answer.** An aggregate is an array or eligible class with no disqualifying constructors, access control, virtual features, or bases/members under the rules of the selected standard. Aggregate initialization initializes its elements directly from a braced list in element order, recursively where appropriate. Missing elements are initialized from default member initializers or otherwise empty-list initialized.

5. **[Deep dive] How do constructors, access, bases, and virtual functions affect aggregate status in C++17 versus C++20?**

   **Answer.** In C++17 a class must have no user-provided, explicit, or inherited constructors; C++20 tightens this to no user-declared or inherited constructors, so even an explicitly defaulted constructor can disqualify it. Both require no private/protected non-static data members, no virtual functions, and no virtual base classes; base classes are allowed subject to version-specific restrictions, but private/protected direct bases disqualify the type. Because details changed across standards, use `std::is_aggregate_v<T>` when code must branch on the property.

6. **[Deep dive] What are C++20 designated initializers, and what restrictions do they have?**

   **Answer.** They initialize named direct data members of an aggregate, for example `Point p{.x = 1, .y = 2};`. Designators must name direct non-static members and appear in declaration order; C++ does not support C-style out-of-order, nested, or array-index designators. You cannot mix designated and ordinary clauses at the same brace level, and the target must remain an aggregate.

## 4.4. Inheritance and virtual inheritance

1. **[Basic] How do public, protected, and private inheritance change base-member accessibility?**

   **Answer.** Private base members are never directly accessible in the derived class. Under public inheritance, base public stays public and protected stays protected. Under protected inheritance, both public and protected base members become protected. Under private inheritance, both become private. Separately, conversions from derived to base are publicly accessible only for public inheritance to ordinary clients.

2. **[Basic] What meaning do public and private inheritance usually express?**

   **Answer.** Public inheritance expresses an substitutable “is-a” relationship: a `Derived` should satisfy the `Base` contract. Private inheritance is an implementation technique closer to “implemented in terms of”; clients cannot use the base interface/conversion. Composition is usually clearer for implementation reuse, while private inheritance is useful when protected access, overriding, or empty-base optimization is specifically needed.

3. **[Deep dive] What problems can multiple inheritance create?**

   **Answer.** It can create name ambiguities, repeated base subobjects, more complex construction/destruction, pointer adjustments, complicated casts, and fragile layout/ABI. Multiple implementation-bearing bases can couple invariants. Multiple inheritance of small pure interfaces is often manageable; design should still make ownership and override responsibilities unambiguous.

4. **[Basic] What is the diamond problem?**

   **Answer.** If `Left` and `Right` both derive from `Base`, and `Diamond` derives from both, an ordinary diamond contains two `Base` subobjects. Access/conversion to `Base` is ambiguous, and duplicated base state may diverge. Virtual inheritance can make the paths share one virtual `Base` subobject when one shared identity is intended.

5. **[Deep dive] How does virtual inheritance solve duplicate base subobjects, and who initializes the virtual base?**

   **Answer.** Declaring `Left : virtual Base` and `Right : virtual Base` makes a most-derived `Diamond` contain one shared `Base`. The most-derived constructor—not intermediate constructors—selects the virtual base's initializer. Initializer attempts in `Left`/`Right` apply only when that class itself is most-derived and are ignored for a `Diamond` construction.

6. **[Deep dive] How can multiple/virtual inheritance affect layout, pointer conversion, and access cost?**

   **Answer.** Base subobjects may reside at nonzero offsets, so converting pointers can adjust their values. Virtual bases require runtime-known offsets, commonly stored through vtable-related metadata, making object layout larger and some member access/casts more expensive. Exact layout, pointer-to-member representation, and ABI are implementation-specific; the standard guarantees semantics, not a vptr arrangement.

7. **[Code] How many `Base` subobjects does `Diamond` contain with and without virtual inheritance?**

   ```cpp
   struct Base {};
   struct Left  : Base {};
   struct Right : Base {};
   struct Diamond : Left, Right {};
   ```

   **Answer.** As written, it contains two: one inside `Left`, one inside `Right`, so `Diamond*` to `Base*` is ambiguous without selecting a path. If both intermediate edges are `virtual Base`, the most-derived `Diamond` contains one shared `Base`. Making only one edge virtual still leaves two base subobjects: one virtual and one non-virtual.

## 4.5. Polymorphism: `virtual`, `override`, `final`, pure virtual functions, and virtual destructors

1. **[Basic] What is runtime polymorphism, and what is required for a virtual call?**

   **Answer.** It selects an override according to an object's dynamic type at runtime while code uses a base pointer or reference. The function must be virtual in the base (it remains virtual in overrides), the call must not be explicitly qualified, and the object's relevant lifetime must be active. Passing by value can slice away the derived part.

2. **[Basic] What are `override` and `final` for?**

   **Answer.** `override` asks the compiler to verify that a virtual member actually overrides a base function, catching signature, cv/ref, and spelling mistakes. `final` on a virtual function prohibits further overriding; on a class it prohibits derivation. Neither changes dispatch cost by itself, though `final` may enable optimization.

3. **[Basic] What is a pure virtual function? Can it have a definition?**

   **Answer.** A declaration ending in `= 0` makes the function pure and normally makes the class abstract. It may still have an out-of-class definition, callable through qualified syntax; a pure virtual destructor must have a definition because derived destruction invokes it. Providing a body does not make the function non-pure.

4. **[Basic] When does a base class need a virtual destructor?**

   **Answer.** If an object may be deleted through a base pointer, the base destructor must be virtual (and accessible) so derived destruction occurs. A polymorphic interface commonly declares `virtual ~Base() = default;`. A base not intended for polymorphic deletion can instead use a protected non-virtual destructor to prevent clients from doing it accidentally.

5. **[Deep dive] What happens when a virtual function is called from a constructor or destructor?**

   **Answer.** Dispatch is limited to the class whose constructor/destructor is currently executing; more-derived overrides are not called because those parts are not yet constructed or have already been destroyed. Calling a pure virtual function in such a path can lead to undefined behavior (even if a definition exists, depending on how called). Do not rely on virtual dispatch to initialize or tear down derived state.

6. **[Deep dive] What are vptr and vtable, and what does the standard guarantee?**

   **Answer.** They are a common implementation: each polymorphic object contains one or more hidden pointers to tables of virtual functions and metadata. The standard does not require either representation. It guarantees observable virtual-dispatch, RTTI, casting, construction, and destruction semantics, leaving layout and dispatch mechanisms to the ABI/compiler.

7. **[Code] Find the problem.**

   ```cpp
   struct Base {
       ~Base() = default;
       virtual void run() = 0;
   };

   struct Derived : Base {
       ~Derived() { release_resource(); }
       void run() override;
   };

   Base* p = new Derived;
   delete p;
   ```

   **Answer.** `Base` is polymorphic but its destructor is non-virtual. Deleting a `Derived` through `Base*` has undefined behavior and may skip `Derived::~Derived`, leaking the resource. Declare `virtual ~Base() = default;`. Better yet, transfer ownership with `std::unique_ptr<Base>` after fixing the destructor.

## 4.6. `dynamic_cast` and object slicing

1. **[Basic] What is object slicing, and where does it occur?**

   **Answer.** Slicing occurs when a derived object is copied or moved into a base object by value. Only the base subobject is stored; derived fields and dynamic type are lost. It commonly happens in base-valued parameters, assignments, returns, and containers such as `std::vector<Base>`.

2. **[Code] Where is polymorphic behavior lost?**

   ```cpp
   void process(Base value);

   Derived d;
   Base b = d;
   process(d);
   ```

   **Answer.** Both `Base b = d;` and the by-value parameter in `process(d)` slice `d`. Virtual calls on `b` or `value` dispatch only as `Base` because those are complete base objects. Use `Base&`/`const Base&` or a pointer/smart pointer when polymorphic identity must be preserved.

3. **[Basic] When is `dynamic_cast` justified, and which design alternatives should be considered?**

   **Answer.** It is appropriate when navigating a genuinely polymorphic hierarchy and the target capability/type is optional—for example framework boundaries or heterogeneous object models. Frequent downcasts may indicate a missing virtual operation, visitor/double dispatch, variant-based closed set, capability interface, or data-oriented redesign. The best alternative depends on whether the set of types or operations is expected to grow.

4. **[Deep dive] Can `dynamic_cast` perform a cross-cast in multiple inheritance?**

   **Answer.** Yes. Given a pointer/reference to one polymorphic base subobject, `dynamic_cast` can find an unambiguous public sibling base within the same most-derived object. It uses runtime type information and performs any required pointer adjustment; failure follows the normal null/`std::bad_cast` rules.

5. **[Deep dive] What is required of the base type for `dynamic_cast` to work?**

   **Answer.** Runtime downcasts and cross-casts require a polymorphic source class—at least one virtual function—and an object within its lifetime. The target type must be complete where required, and the relationship must be public and unambiguous for a successful result. Upcasts to an unambiguous public base do not need RTTI and can be done implicitly.

6. **[Code] How do you avoid slicing in a polymorphic container?**

   ```cpp
   std::vector<Base> objects;
   objects.push_back(Derived{});
   ```

   **Answer.** Store owning polymorphic pointers, usually `std::unique_ptr<Base>`, and give `Base` a virtual destructor.

   ```cpp
   std::vector<std::unique_ptr<Base>> objects;
   objects.push_back(std::make_unique<Derived>());
   ```

   If value semantics are required, implement a virtual `clone()` and a value-wrapper, or use `std::variant` for a closed type set.

## 4.7. Operator overloading

1. **[Basic] Which operators can and cannot be overloaded? Can precedence, associativity, or arity change?**

   **Answer.** Most symbolic operators, assignment forms, `()`, `[]`, `->`, allocation/deallocation operators, conversions, and literals have overload mechanisms. Operators such as `.`, `.*`, `::`, `?:`, `sizeof`, `alignof`, and `typeid` cannot be overloaded. Overloading cannot invent a new token or change precedence, associativity, or operand count, and at least one operand must have class or enum type for ordinary overloaded operators.

2. **[Basic] Which operators must be non-static member functions?**

   **Answer.** In the C++17/20 baseline, `operator=`, `operator[]`, `operator()`, and `operator->` must be non-static members. Conversion functions must also be non-static members. Other operators may still be best expressed as members or non-members depending on symmetry and access; C++23 relaxes static-member rules for some call/subscript operators, but that is outside this guide's baseline.

3. **[Deep dive] What are typical copy/move assignment signatures, and what should they return?**

   **Answer.** Canonical forms are `T& operator=(const T&)` and `T& operator=(T&&) noexcept(/* truthful condition */)`. They return `*this` by non-const reference, enabling conventional chaining such as `a = b = c`. They should leave the object valid on self-assignment; resource-managing move assignment should also have a deliberate self-move policy.

4. **[Deep dive] How should `operator==`, ordering, and `<=>` be kept consistent?**

   **Answer.** Equality should be an equivalence relation, and ordering should agree with it: objects equivalent under the ordering should compare equal when that is the type's semantics. In C++20, defaulting `operator<=>` often generates member-wise ordering and can implicitly provide a matching defaulted `operator==`; relational expressions can be rewritten from `<=>`, while equality is based on `==`. For custom semantics, implement `==` and `<=>` from the same canonical state and choose the correct comparison category.

5. **[Basic] How should const and non-const `operator[]` overloads be designed?**

   **Answer.** Typically provide `T& operator[](size_type)` and `const T& operator[](size_type) const`, so mutation follows the constness of the container. Match the expected bounds contract: conventional `operator[]` is unchecked, while a separate `at()` throws on an invalid index. Proxy return types are possible for packed/lazy structures but should preserve intuitive read/write behavior.

6. **[Basic] What is `operator()` used for, and what is a function object?**

   **Answer.** Defining `operator()` makes an object callable. Such a function object can carry configuration or state, be overloaded/templated, and often inline efficiently through templates. Lambdas are compiler-generated function objects; comparators, hashers, predicates, and callbacks are common uses.

7. **[Deep dive] Why are stream `operator<<` and `operator>>` usually free functions returning the stream by reference?**

   **Answer.** The left operand is the stream, so making the operator a member of the user type would give operands the wrong order; modifying `std::ostream` is not an option. A free function can be a friend if private access is justified. Returning `std::ostream&`/`std::istream&` preserves stream state and enables chaining: `out << a << b`.

8. **[Deep dive] How are user-defined literals declared, and what naming restrictions apply?**

   **Answer.** A literal operator is declared at namespace scope, for example `constexpr Distance operator""_km(long double);`, using one of the permitted parameter forms (cooked, raw, character, string, or template forms). User-defined suffixes should begin with an underscore; suffixes without a leading underscore are reserved for the standard library, and identifiers containing double underscores or reserved underscore patterns must be avoided. Literal operators cannot be members and have language-defined parameter lists.

9. **[Code] What properties are expected from a correct `operator==`, and why are side effects dangerous?**

   **Answer.** It should represent a stable equivalence relation: reflexive, symmetric, and transitive, with repeated comparisons giving the same result while values are unchanged. It should normally be inexpensive, non-mutating, and consistent with hashing and ordering. Side effects break generic algorithms and containers that may compare any number of times or in unspecified orders, making results depend on implementation details rather than values.

---

# 5. Resource management and move semantics

## 5.1. RAII, `new`/`delete`, arrays, and placement new

1. **[Basic] What is RAII, and how does it connect a resource to object lifetime?**

   **Answer.** Resource Acquisition Is Initialization stores ownership in an object that acquires or receives the resource during construction and releases it in its destructor. Because automatic objects are destroyed on normal return and stack unwinding, cleanup becomes deterministic and independent of the exit path. The owning type should also define or delegate copy/move semantics so ownership cannot accidentally duplicate.

   ```mermaid
   flowchart LR
       A[construct owner] --> B[acquire resource]
       B --> C[use through valid object]
       C --> D{scope exits}
       D -->|normal return| E[destructor releases]
       D -->|exception| E
   ```

2. **[Basic] Which resources besides memory are well suited to RAII?**

   **Answer.** File descriptors/handles, sockets, mutex locks, database transactions, GUI/OS handles, mapped memory, temporary files, threads, and library contexts all fit. The release operation should normally be non-throwing. Scope guards generalize RAII to arbitrary rollback or completion actions.

3. **[Basic] How do `new`/`delete` differ from `malloc`/`free`?**

   **Answer.** A `new` expression obtains suitably aligned storage, constructs typed object(s), and normally throws `std::bad_alloc` on allocation failure; `delete` runs destructors and releases matching storage. `malloc` allocates raw bytes and returns `void*`/null without starting a class object's lifetime by construction; `free` releases bytes without calling destructors. Families must not be mixed. In modern C++, direct use of either is usually hidden inside RAII containers/allocators.

4. **[Basic] Why must memory allocated with `new[]` be released with `delete[]`?**

   **Answer.** Array new and delete form a matching allocation/deallocation protocol. The implementation may need hidden metadata to destroy the correct number of elements, and `delete[]` invokes element destructors in reverse order. Using scalar `delete`, `free`, or another allocator is undefined behavior, even if it appears to work for trivial elements.

5. **[Deep dive] What happens if the constructor of an object created by `new` throws?**

   **Answer.** Fully constructed bases and members are destroyed in reverse order; the object's own destructor does not run because its lifetime never completed. The `new` expression then calls the matching deallocation function for the storage it obtained and propagates the exception. This automatic cleanup can be defeated by unusual placement forms unless a matching placement delete is available for constructor failure.

6. **[Deep dive] What is placement new, and when is an explicit destructor call required afterward?**

   **Answer.** Placement new constructs an object in storage supplied by the caller: `T* p = ::new (address) T(args...);`. The placement form does not own or later release that storage. For a non-trivially destructible object whose lifetime is intentionally ended without an ordinary `delete`/scope owner, call `std::destroy_at(p)` (or `p->~T()`) before reusing/releasing the storage. The storage's own allocation mechanism must be released separately.

7. **[Deep dive] Which alignment and storage requirements apply to manual object lifetime management?**

   **Answer.** The region must be large enough, aligned for `T`, and remain allocated throughout the object's lifetime. Construction must formally begin a `T` lifetime, and code must obey aliasing and pointer-provenance/lifetime rules; reusing storage for a new object may require obtaining an appropriate pointer such as via `std::launder` in relevant C++17 cases. Prefer allocator APIs, `std::construct_at`/`destroy_at` where available, or established containers because these rules are subtle.

8. **[Code] What problems does this function have?**

   ```cpp
   void process() {
       Resource* resource = new Resource;
       resource->run();
       delete resource;
   }
   ```

   **Answer.** If `run()` throws, `delete` is skipped and the resource leaks. A bare owning pointer also obscures ownership and complicates future early returns. If dynamic allocation is unnecessary, use `Resource resource; resource.run();`. Otherwise use `auto resource = std::make_unique<Resource>(); resource->run();`; both guarantee destruction on every exit path.

## 5.2. `std::unique_ptr`

1. **[Basic] Which ownership model does `std::unique_ptr` express? Why is it movable but not copyable?**

   **Answer.** It represents exclusive ownership of an object through one handle. Copying would create two apparent owners and risk double deletion, so copy operations are deleted. Moving transfers the stored pointer/deleter to the destination and leaves the source valid but empty in the standard `unique_ptr` case.

2. **[Basic] Why should `std::make_unique` normally be used?**

   **Answer.** It constructs the object and immediately returns its owner without exposing a raw owning pointer, is concise, and avoids repeating the type. It also makes code exception-safe in complex expressions, especially under pre-C++17 argument-evaluation rules. Direct construction remains necessary for custom deleters, adopting some API-returned pointers, and certain specialized allocation schemes.

3. **[Basic] How do `get()`, `reset()`, and `release()` differ?**

   **Answer.** `get()` returns the stored non-owning raw pointer and changes nothing. `reset(p)` destroys the currently owned object, if any, then adopts `p`. `release()` gives up ownership and returns the raw pointer without deleting it; the caller must immediately transfer it to another owner or arrange deletion. Most leaks involving `unique_ptr` misuse start with an unjustified `release()`.

4. **[Deep dive] How does a custom deleter affect the type and size of `unique_ptr`?**

   **Answer.** The deleter is the second template argument, so `unique_ptr<T, D1>` and `unique_ptr<T, D2>` are different types. Stateful deleter data is stored with the pointer and can enlarge the object; stateless empty deleters are commonly compressed via empty-base/no-unique-address-style optimization, often leaving pointer-sized storage, but exact size is not guaranteed. Deleter move/copy/noexcept properties also affect the smart pointer's operations.

5. **[Basic] How should a `unique_ptr` be passed when a function takes ownership, merely uses the object, or may replace the owner?**

   **Answer.** Take `std::unique_ptr<T>` by value to consume ownership; callers use `std::move`. Take `T&`/`const T&` for a required borrowed object or `T*` for an optional borrow. Take `std::unique_ptr<T>&` only when the function is meant to reseat/reset the caller's owning handle. A `const std::unique_ptr<T>&` is appropriate only when the handle itself—not merely `T`—is part of the interface.

6. **[Deep dive] How does `unique_ptr<T>` differ from `unique_ptr<T[]>`?**

   **Answer.** The array specialization uses `delete[]`, provides `operator[]`, and does not provide scalar `operator*`/`operator->`. It does not store the array length, so the program must track it separately. Prefer `std::vector<T>` for dynamic arrays unless fixed ownership without resizing and separate size metadata are clearly appropriate.

7. **[Deep dive] Can `unique_ptr<Derived>` safely convert to `unique_ptr<Base>`? How does a virtual destructor matter?**

   **Answer.** A converting move is available when the pointer and deleter conversions are valid. With the default deleter, the destination deletes through `Base*`; if `Base` lacks a virtual destructor, deleting a `Derived` this way is undefined behavior. A virtual base destructor is the normal fix. A carefully preserved custom deleter can retain the concrete deletion operation, but the ownership contract must remain unambiguous.

8. **[Code] Where is the leak, and how should the code be rewritten?**

   ```cpp
   auto p = std::make_unique<Resource>();
   Resource* raw = p.release();
   raw->use();
   ```

   **Answer.** `release()` makes `p` empty and no owner later deletes `raw`; an exception from `use()` makes the leak immediate. If only borrowing is needed, call `p->use()` or use `Resource* raw = p.get();` while keeping `p` alive. Use `release()` only at an ownership-transfer boundary whose recipient explicitly adopts the pointer.

## 5.3. `std::shared_ptr`, `std::weak_ptr`, and the control block

1. **[Basic] Which ownership model does `std::shared_ptr` express, and when is it actually needed?**

   **Answer.** It represents shared lifetime ownership: the managed object is destroyed after the last strong owner releases it. Use it when several independent components genuinely must extend the same object's lifetime and no single owner is natural. It is not a default substitute for unclear ownership; prefer values or `unique_ptr` plus non-owning references when a hierarchy exists.

2. **[Basic] What is normally stored in a `shared_ptr` control block?**

   **Answer.** It holds strong and weak reference counts plus erased deletion/allocation machinery; it may also contain the object itself when created by `make_shared`. Each `shared_ptr` separately stores a pointer returned by `get()`, which may differ from the managed object address because aliasing constructors can share a control block while pointing at a subobject.

   ```mermaid
   flowchart LR
       S1[shared_ptr A] --> C[control block\nstrong + weak counts\ndeleter]
       S2[shared_ptr B] --> C
       W[weak_ptr] -. non-owning .-> C
       C --> O[managed object]
   ```

3. **[Deep dive] How does the strong count differ from the weak count?**

   **Answer.** Strong owners keep the managed object alive; when the strong count reaches zero, the object is destroyed. Weak references do not keep it alive, but the control block must remain until all weak references (and implementation bookkeeping) are gone. Thus object destruction and control-block deallocation are separate events.

4. **[Basic] How does `make_shared<T>()` differ from `shared_ptr<T>(new T)` in allocation count and memory lifetime?**

   **Answer.** `make_shared` commonly performs one allocation containing both control block and object, improving locality and exception safety; the raw-`new` form normally performs one allocation for `T` and another for the control block. With `make_shared`, surviving weak pointers can keep the combined allocation reserved after `T` is destroyed, even though `T`'s lifetime has ended. Separate allocation can release the object's memory at strong-count zero while retaining only the control block.

5. **[Basic] Why must two independent `shared_ptr`s not be created from the same raw pointer?**

   **Answer.** Each constructor creates a separate control block, so neither knows about the other. Each eventually invokes its deleter on the same object, causing double deletion and undefined behavior. Copy an existing `shared_ptr`, use `weak_ptr::lock`, or use `shared_from_this()` for an object already correctly owned.

6. **[Basic] How do ownership cycles arise, and how does `weak_ptr` break them?**

   **Answer.** A cycle occurs when objects own each other strongly, so every strong count remains nonzero even after external owners disappear. Replace non-owning/back edges—such as child-to-parent, observer, or cache links—with `weak_ptr`. This removes the edge from lifetime accounting while permitting a temporary strong owner to be acquired when the target still exists.

7. **[Basic] What does `weak_ptr::lock()` do, and why is checking `expired()` before use a race?**

   **Answer.** `lock()` atomically attempts to create a `shared_ptr` sharing ownership; it returns empty if the object has already expired. Between a separate `expired()` check and a later ownership attempt, another thread can release the last strong owner. Call `if (auto p = weak.lock()) { ... }` and use that retained owner.

8. **[Deep dive] What is `std::enable_shared_from_this` for? Why not return `shared_ptr(this)`?**

   **Answer.** It lets an object already managed by a `shared_ptr` obtain another owner sharing the existing control block through `shared_from_this()`. Constructing `shared_ptr(this)` creates an independent control block and causes double deletion. `shared_from_this()` requires that a suitable owning `shared_ptr` has initialized the internal weak link; otherwise it throws `std::bad_weak_ptr` (for the standard post-C++17 behavior).

9. **[Deep dive] What thread safety is guaranteed for separate `shared_ptr` instances sharing a control block, and what is not guaranteed?**

   **Answer.** Separate smart-pointer objects that share a control block may be copied/reset/destroyed concurrently; reference-count operations are synchronized. Concurrent unsynchronized writes to the same `shared_ptr` object are not safe unless using the appropriate atomic shared-pointer facilities. The pointed-to object receives no automatic synchronization at all—its own data races must be prevented separately.

10. **[Code] Find the problem.**

    ```cpp
    struct Node {
        std::shared_ptr<Node> parent;
        std::vector<std::shared_ptr<Node>> children;
    };
    ```

    **Answer.** Parent and children form strong cycles: a parent owns each child and each child owns the parent, so the graph leaks when external owners disappear. Let the parent own children and make the back-reference non-owning: `std::weak_ptr<Node> parent;`. Tree construction should establish nodes under shared ownership before assigning weak parent links.

## 5.4. Move semantics and perfect forwarding

1. **[Basic] What does `std::move` do? Does it move anything itself?**

   **Answer.** It is essentially a cast to an xvalue (`remove_reference_t<T>&&`), signaling that the source may be treated as expiring. No constructor, assignment, or bytes move until another operation consumes that expression. If the selected overload copies—or no operation is performed—nothing is transferred.

2. **[Basic] When is a move constructor selected rather than a copy constructor?**

   **Answer.** Overload resolution selects `T(T&&)` for a compatible non-const rvalue/xvalue when it is viable and better than `T(const T&)`. Lvalues normally select copy construction unless explicitly cast with `std::move`. Prvalue construction may be guaranteed-elided in C++17, so neither constructor runs. If move is missing, deleted, inaccessible, or incompatible (notably with `const`), copying may be selected or compilation may fail.

3. **[Deep dive] Why does moving from a `const` object usually copy?**

   **Answer.** `std::move(const T)` produces `const T&&`. Ordinary move constructors take `T&&` because moving usually modifies the source, so they cannot bind to a const rvalue. A copy constructor taking `const T&` can bind and is selected instead. Defining `T(const T&&)` rarely helps because it cannot steal state that requires mutating the source.

4. **[Basic] What does the standard guarantee about a moved-from object?**

   **Answer.** For standard-library types, unless a stronger operation-specific postcondition is stated, it is valid but in an unspecified state: its invariants hold, it can be destroyed or assigned to, and operations without extra preconditions may be called. It is not generally guaranteed empty. User-defined types must document and maintain an appropriate valid state.

5. **[Deep dive] Why does self-move assignment deserve attention, and what is a reasonable guarantee?**

   **Answer.** Generic code can accidentally execute `x = std::move(x)`. An implementation that destroys its resource and then reads it can corrupt state or double free. A robust move assignment should at least leave the object valid and destructible; preserving the original value is optional. A self-check, move-and-swap pattern, or carefully ordered resource exchange can provide that safely.

6. **[Basic] What is a forwarding reference, and how does it differ from an ordinary rvalue reference?**

   **Answer.** A forwarding reference is `T&&` where `T` is a cv-unqualified template parameter being deduced in that context, or `auto&&` in corresponding deduction. It binds to both lvalues and rvalues and records their category through deduction. `Widget&&`, `const T&&`, and `T&&` where `T` is already fixed are ordinary rvalue references and do not have that lvalue-binding behavior.

7. **[Deep dive] Explain reference-collapsing rules.**

   **Answer.** Forming references to references collapses as follows: `& + & -> &`, `& + && -> &`, `&& + & -> &`, and only `&& + && -> &&`. In short, an lvalue reference dominates. Thus when a forwarding reference receives an lvalue, `T` deduces as `U&`, and `T&&` collapses to `U&`.

8. **[Basic] What does `std::forward<T>` do, and why is it needed in a template wrapper?**

   **Answer.** It conditionally casts according to deduced `T`: an originally passed lvalue remains an lvalue, while an originally passed rvalue becomes an xvalue. A named parameter is always an lvalue expression even when its type is `T&&`, so forwarding is required to preserve the caller's value category for overload resolution.

9. **[Code] Why does the version without `std::forward` change behavior?**

   ```cpp
   template<class T>
   void wrapper(T&& value) {
       target(value);
   }
   ```

   **Answer.** The expression `value` is named and therefore an lvalue. `target` always sees an lvalue and may select a copy/`T&` overload even when the caller passed a temporary. Use `target(std::forward<T>(value));`. Forward a particular parameter only when handing it onward for consumption; repeated forwarding can expose an already-moved-from value.

10. **[Code] What is wrong or redundant here?**

    ```cpp
    Widget make() {
        Widget value;
        return std::move(value);
    }

    const Widget source;
    Widget target = std::move(source);
    ```

    **Answer.** `std::move(value)` commonly prevents NRVO; use `return value;`, allowing NRVO or the return statement's implicit-move rules. `std::move(source)` has type `const Widget&&`, which normally cannot bind to `Widget(Widget&&)`, so `target` is copied via `Widget(const Widget&)` if possible. The cast is misleading unless a meaningful const-rvalue overload exists.

---

# 6. Templates, SFINAE, and concepts

## 6.1. Function/class templates, parameters, specialization, and instantiation

1. **[Basic] How does a template differ from a function or class generated from it?**

   **Answer.** A template is a compile-time pattern plus parameter list; it is not itself an ordinary function or complete class object type. Instantiation substitutes specific arguments to create a specialization such as `vector<int>` or `max<int>`. Different specializations are distinct types/functions, and only needed portions may be instantiated.

2. **[Basic] Which type and non-type template parameters exist? Which values may be non-type arguments?**

   **Answer.** Type parameters use `class`/`typename`; template-template parameters accept templates; non-type template parameters carry compile-time values, for example `template<std::size_t N>`. C++17 permits categories including integral/enum values, pointers/references with restrictions, `nullptr_t`, and member pointers. C++20 generalizes them to structural types, including suitable literal class types whose public non-mutable subobjects are themselves structural. The argument must satisfy the relevant converted constant-expression and identity rules.

3. **[Basic] How are function-template parameters deduced? Which parameters cannot be deduced from context?**

   **Answer.** The compiler compares each function-parameter pattern with the corresponding argument type, applying rules based on value/reference form, cv-qualification, arrays, and forwarding references, then combines deductions consistently. A parameter appearing only in the return type cannot normally be deduced. Other non-deduced contexts include many nested-name specifiers, certain `decltype` expressions, and values embedded in expressions; those arguments must be explicit or inferred elsewhere.

4. **[Deep dive] What is a dependent name, and why are `typename` and `template` needed?**

   **Answer.** A dependent name's meaning depends on a template parameter and may be resolved only during instantiation. The parser cannot always know whether `T::value_type` is a type, so `typename T::value_type` disambiguates it. Similarly, after a dependent object/scope, `obj.template get<U>()` or `T::template rebind<U>` tells the parser that the following `<` starts template arguments. There are contexts where the language already assumes a type/template and the keyword is unnecessary.

5. **[Basic] How does implicit instantiation differ from explicit instantiation?**

   **Answer.** Implicit instantiation occurs when use requires a concrete specialization and no suitable explicit specialization already supplies it. An explicit instantiation definition such as `template class Vector<int>;` forces generation in a chosen translation unit. This can centralize code generation and reduce build work, provided the definition is visible there.

6. **[Deep dive] What are explicit instantiation declarations (`extern template`) and definitions for?**

   **Answer.** `extern template class Vector<int>;` tells a translation unit not to implicitly instantiate that specialization, expecting an explicit instantiation definition elsewhere. One `.cpp` then contains `template class Vector<int>;`. This can reduce duplicate compilation/code emission, but missing the definition creates link failures and the declarations/definitions must cover the members actually needed.

7. **[Basic] How does full specialization differ from partial specialization?**

   **Answer.** A full specialization supplies behavior for one complete argument list, such as `template<> struct Traits<int>`. A partial specialization describes a family that is more specialized than the primary, such as `Traits<T*>`. When several class/variable-template specializations match, partial ordering chooses the most specialized viable one.

8. **[Deep dive] Why are partial specializations allowed for class templates but not function templates? What is used instead?**

   **Answer.** Function templates already have overloading and template partial ordering, which provide the intended selection model and interact with conversions. Therefore function templates can be fully specialized but not partially specialized. Write another constrained/overloaded function template, often dispatching to a partially specialized class helper when state/type computation is needed.

9. **[Deep dive] Why are template definitions usually placed in headers?**

   **Answer.** The compiler normally needs the complete definition at each point where it implicitly instantiates a specialization; a declaration alone is insufficient. Headers make the definition reachable in every translation unit. An alternative is to keep it in a `.cpp` and explicitly instantiate a closed set of supported arguments, optionally paired with `extern template` declarations.

10. **[Code] Which overload or specialization is selected?**

    ```cpp
    template<class T> void f(T);
    template<class T> void f(T*);
    template<> void f<int>(int);

    int value = 0;
    f(value);
    f(&value);
    ```

    **Answer.** `f(value)` selects the primary `f(T)` with `T = int`, then uses its explicit `int` specialization. `f(&value)` selects the more specialized overload `f(T*)` with `T = int`; the explicit specialization shown belongs to the first template and does not apply. Function-template specializations do not participate as independent overload candidates—overload resolution chooses a primary template first.

## 6.2. Variadic templates and fold expressions

1. **[Basic] What are a parameter pack and a pack expansion?**

   **Answer.** A parameter pack represents zero or more template or function parameters, for example `class... Ts` or `Ts&&... args`. A pattern followed by `...` expands once per pack element, such as `f(std::forward<Ts>(args)...)`. All packs expanded together in one pattern must have compatible lengths.

2. **[Basic] How do you get a pack's element count with `sizeof...`?**

   **Answer.** `sizeof...(Ts)` counts template arguments and `sizeof...(args)` counts function-parameter pack elements. The result is a compile-time `std::size_t` and the pack is not expanded or evaluated.

3. **[Deep dive] How were variadic templates processed recursively before fold expressions?**

   **Answer.** A base overload handled an empty or single-element case, while a recursive overload processed the first argument and called itself with the remaining pack. This produced many instantiations and more complicated diagnostics. C++17 folds express many associative-style operations directly, though recursion or index-sequence expansion remains useful for nonuniform processing.

4. **[Basic] What unary/binary and left/right fold forms exist?**

   **Answer.** Unary left is `(... op pack)`, unary right is `(pack op ...)`, binary left is `(init op ... op pack)`, and binary right is `(pack op ... op init)`. Parentheses are part of the required syntax. Left/right matters for non-associative operators and for operand evaluation/overload behavior.

5. **[Deep dive] What happens when folding an empty pack, and which operators have identity values?**

   **Answer.** A binary fold over an empty pack yields its `init`. A unary fold over an empty pack is valid only for `&&` (identity `true`), `||` (identity `false`), and comma (identity `void()`). Other unary folds are ill-formed for an empty pack, so constrain against emptiness or use a binary fold with an explicit identity.

6. **[Code] Implement `all(...)`, returning logical AND of all arguments.**

   **Answer.** A unary fold is concise and gives `true` for an empty argument list.

   ```cpp
   template<class... Ts>
   constexpr bool all(Ts&&... values) {
       return (... && static_cast<bool>(std::forward<Ts>(values)));
   }
   ```

   `&&` retains left-to-right short-circuit evaluation.

7. **[Code] Explain the grouping order.**

   ```cpp
   (... - args)
   (args - ...)
   (init + ... + args)
   ```

   **Answer.** For arguments `a, b, c`, unary left `(... - args)` is `((a - b) - c)`. Unary right `(args - ...)` is `(a - (b - c))`. Binary left `(init + ... + args)` is `(((init + a) + b) + c)`. The distinct subtraction results demonstrate why fold direction matters.

## 6.3. SFINAE and `std::enable_if`

1. **[Basic] Expand SFINAE and explain its practical meaning.**

   **Answer.** “Substitution Failure Is Not An Error.” When substituting deduced template arguments into the immediate context of a function template produces certain invalid types or expressions, that candidate is removed from the overload set instead of causing a compilation error. This enabled pre-C++20 detection and conditional overloads, although concepts now express most such constraints more clearly.

2. **[Deep dive] What is the immediate context, and why is not every instantiation error a substitution failure?**

   **Answer.** It comprises types and expressions directly in the function type, template parameter declarations, and specified substitution contexts. If substitution succeeds there but later instantiating a function body, base, defaulted member, or another template triggers an error, that is generally a hard compilation error. SFINAE is deliberately limited; it is not a catch-all exception mechanism for arbitrary template compilation.

3. **[Basic] How does `std::enable_if` enable or remove an overload?**

   **Answer.** `std::enable_if<true, T>` contains a member type `type`; the false specialization does not. Referring to `std::enable_if_t<condition, T>` in an immediate substitution context succeeds only when the condition is true. If false during deduction, the candidate is discarded by SFINAE.

4. **[Deep dive] Where can `enable_if` be placed?**

   **Answer.** Common placements are the return type, an extra function parameter, a defaulted type template parameter, or a defaulted non-type template parameter. Return-type placement cannot distinguish constructors/destructors and may produce awkward diagnostics; a template parameter is often cleaner. The declarations must still be structurally distinct—default template arguments alone are not part of template equivalence.

5. **[Deep dive] What are the detection idiom and `std::void_t`?**

   **Answer.** They test whether dependent expressions/types are well-formed by mapping them to `void` during partial-specialization substitution. A primary trait yields false; a specialization using `std::void_t<decltype(std::declval<T&>().size())>` yields true when the expression exists. Concepts and requires-expressions are the C++20 replacement with clearer syntax and diagnostics.

6. **[Code] Restrict this function to integral types in C++17 style.**

   ```cpp
   template<class T>
   T twice(T value);
   ```

   **Answer.** Put `enable_if` in a non-type template parameter and normally strip cv if the interface can deduce it.

   ```cpp
   template<class T, std::enable_if_t<std::is_integral_v<T>, int> = 0>
   T twice(T value) {
       return value + value;
   }
   ```

   For by-value `T`, top-level cv is already dropped during deduction.

7. **[Code] Why can two overloads with default template arguments be treated as redeclarations of one function?**

   **Answer.** Default template arguments are not part of a function template's equivalence/signature. For example, two declarations shaped `template<class T, class = enable_if_t<cond>> void f(T);` differ only in defaults and therefore redeclare the same template instead of forming overloads. Make the declarations structurally distinct—often with different dependent non-type parameter types/values—or use C++20 constraints.

## 6.4. Concepts and `requires`

1. **[Basic] Which SFINAE problem do concepts solve?**

   **Answer.** Concepts make template requirements part of the declared interface rather than encoding them through substitution tricks. They provide earlier, clearer diagnostics, reusable named predicates, and language-defined ordering of constrained overloads. They do not eliminate templates or every instantiation error; function bodies can still be invalid for reasons not expressed by constraints.

2. **[Basic] What are a concept and a requires-clause?**

   **Answer.** A concept is a named compile-time boolean predicate over template arguments, declared with `template<...> concept Name = constraint-expression;`. A requires-clause attaches a constraint to a declaration, for example `template<class T> requires Sortable<T> void sort(T&);`, controlling viability and specialization ordering.

3. **[Deep dive] How does a requires-expression differ from a requires-clause?**

   **Answer.** A requires-expression, `requires(T t) { expressions...; }`, tests requirements and evaluates to `bool` without turning an invalid tested expression into a hard error in templated use. A requires-clause is the declaration-level `requires constraint` that constrains a template/function. A requires-expression is often used inside a concept, which is then named by a requires-clause.

4. **[Deep dive] Which requirement kinds can appear inside a requires-expression?**

   **Answer.** A simple requirement `t.begin();` checks that an expression is valid. A type requirement `typename T::value_type;` checks that a type name exists. A compound requirement `{ t.size() } noexcept -> std::convertible_to<std::size_t>;` can test validity, non-throwing status, and result-type constraint. A nested requirement `requires SomeConcept<T>;` checks another constraint expression.

5. **[Basic] Show three ways to constrain a template: requires-clause, constrained parameter, and abbreviated function template.**

   **Answer.** Given `template<class T> concept Number = std::integral<T> || std::floating_point<T>;`:

   ```cpp
   template<class T>
       requires Number<T>
   T square(T x);

   template<Number T>
   T cube(T x);

   auto magnitude(Number auto x);
   ```

   All constrain viability; choose the form that makes relationships among parameters easiest to read.

6. **[Deep dive] How does constraint subsumption select a more specialized overload?**

   **Answer.** Constraints are normalized into atomic constraints, and the compiler compares their logical structure. If one constraint subsumes another, the corresponding viable overload can be considered more constrained and preferred. This is not general theorem proving: re-spelling logically equivalent expressions can create distinct atomic constraints, so build refined concepts from named concepts rather than duplicating boolean formulas.

7. **[Deep dive] Can a concept verify semantic laws such as associativity?**

   **Answer.** It can verify well-formed expressions, types, conversions, `noexcept`, and explicit compile-time predicates. It cannot generally prove runtime laws such as associativity, ordering consistency, or “does not invalidate iterators.” Standard concepts often state semantic requirements that programs must honor; violating them can make generic-code behavior undefined or incorrect even though the syntactic concept evaluates true.

8. **[Code] Rewrite this `enable_if` constraint as a concept.**

   ```cpp
   template<class T,
            std::enable_if_t<std::is_integral_v<T>, int> = 0>
   T twice(T value);
   ```

   **Answer.** Use the standard `std::integral` concept from `<concepts>`:

   ```cpp
   template<std::integral T>
   T twice(T value) {
       return value + value;
   }
   ```

   An equivalent trailing form is `template<class T> T twice(T value) requires std::integral<T>;`.

9. **[Code] Write a concept for a type with `size()` whose result is convertible to `std::size_t`.**

   **Answer.** State exactly the expression and result conversion required:

   ```cpp
   template<class T>
   concept Sized = requires(const T& value) {
       { value.size() } -> std::convertible_to<std::size_t>;
   };
   ```

   Using `const T&` intentionally requires size inspection on a const object; use `T&` instead if mutation-only types should qualify.

---

# 7. Exceptions and safety guarantees

## 7.1. `throw`, `try`/`catch`, stack unwinding, and RAII

1. **[Basic] How is an exception thrown and caught? In which order are handlers examined?**

   **Answer.** `throw expression;` initializes an exception object and transfers control to the nearest active handler whose parameter matches. Handlers for a `try` block are tested in source order, so derived exception types must precede their bases and `catch (...)` must be last. If no handler matches, propagation continues outward after unwinding; if it escapes the initial thread function, `std::terminate` is called.

2. **[Basic] Why are exceptions normally caught by `const` reference?**

   **Answer.** A reference avoids copying the exception object and preserves its dynamic type, preventing slicing when catching through a base such as `std::exception`. `const` prevents accidental modification while still allowing virtual calls such as `what()`. Catch by non-const reference only when the handler intentionally modifies the exception before rethrowing or otherwise needs mutation.

3. **[Basic] What is stack unwinding, and which destructors run?**

   **Answer.** As exception propagation leaves scopes, fully constructed automatic objects are destroyed in reverse construction order. Partially constructed objects do not have their own destructor called, but their completed base/member subobjects are destroyed. Dynamically allocated resources are not automatically freed merely because a raw pointer is local; they need an RAII owner.

4. **[Basic] How does RAII make code exception-safe?**

   **Answer.** Every acquired resource is immediately placed in an owner whose destructor releases it. Stack unwinding then executes cleanup automatically whether control leaves by success, early return, or exception. This removes duplicated cleanup paths and makes compositional exception safety possible: if every member owns its resource correctly, the containing object's generated destruction usually does too.

5. **[Deep dive] What happens if a destructor throws while another exception is being handled?**

   **Answer.** If a second exception escapes a destructor during stack unwinding, two exceptions would be active with no propagation model, so `std::terminate()` is called. Destructors are implicitly non-throwing in common cases and resource-release destructors should uphold that contract. They should handle/log recoverable cleanup failures internally or expose an explicit pre-destruction operation that callers can check.

6. **[Deep dive] What does `std::uncaught_exceptions()` return, and where is it useful?**

   **Answer.** It returns the number of uncaught exception objects in the current thread. A scope guard can record the count on construction and compare it in its destructor to distinguish normal exit from unwinding, enabling commit-on-success or rollback-on-failure behavior. Such designs must still keep destructor actions non-throwing and account for nested exceptions.

7. **[Deep dive] How do `throw;` and `throw e;` differ when rethrowing?**

   **Answer.** Inside a handler, bare `throw;` rethrows the current exception object, preserving its dynamic type and original exception identity. `throw e;` evaluates `e` and throws a new exception object based on the expression's static type, which can slice a derived exception and adds a copy/move. Use bare `throw;` unless intentionally translating to a new exception, often with `std::throw_with_nested` for context.

8. **[Code] Which resources are released if `step2()` throws?**

   ```cpp
   void run() {
       auto file = std::make_unique<File>();
       Mutex mutex;
       mutex.lock();
       step1();
       step2();
       mutex.unlock();
   }
   ```

   **Answer.** `file` is destroyed during unwinding, so its `File` is released. `mutex`'s destructor runs, but the explicit `unlock()` is skipped; an ordinary mutex object does not provide scoped unlocking, and destroying a still-locked mutex is invalid or otherwise erroneous according to the mutex type's contract. The lock itself therefore is not safely released by this code. `step1`'s completed local RAII objects are also destroyed as its scope ended or unwound.

9. **[Code] Rewrite the example so cleanup does not depend on the exit path.**

   **Answer.** Use an RAII lock owner as well as the smart pointer; if the file can be a direct value, even the allocation can disappear.

   ```cpp
   void run() {
       auto file = std::make_unique<File>();
       std::lock_guard<Mutex> lock(mutex_);
       step1();
       step2();
   }
   ```

   If the original local `Mutex` was intentional, construct the guard from it; usually a mutex protects shared state and is therefore a longer-lived member rather than a new local mutex on every call.

## 7.2. Exception-safety guarantees

1. **[Basic] Describe the basic, strong, and nothrow guarantees.**

   **Answer.** The basic guarantee means that after failure, invariants hold and no resources leak, but observable state may have changed. The strong guarantee adds transactional behavior: on failure, observable state is unchanged. The nothrow guarantee means the operation will not emit an exception and completes its documented effect; it is essential for destructors and rollback primitives. “No guarantee” may leave even invariants/resource accounting compromised.

2. **[Basic] What does “valid but not necessarily unchanged state” mean?**

   **Answer.** The object's invariants still hold, it may be destroyed or assigned, and operations whose preconditions are satisfied remain legal, but its value can differ from the pre-call value. For example, a multi-step append might retain some appended elements after failure while the container remains internally consistent. The API should document any stronger, operation-specific postconditions.

3. **[Deep dive] Which operations are well suited to commit-or-rollback for the strong guarantee?**

   **Answer.** Configuration updates, container replacements, parsing/building a new representation, multi-field state changes, and database-like transactions are good candidates. Perform all potentially throwing work in temporary state, validate it, then commit with a non-throwing swap, pointer exchange, or small atomic state change. External irreversible effects require compensating actions or a different transaction protocol.

   ```mermaid
   flowchart LR
       A[old valid state] --> B[build temporary state]
       B -->|failure| A
       B -->|all checks pass| C[noexcept commit]
       C --> D[new valid state]
   ```

4. **[Deep dive] How does copy-and-swap provide the strong guarantee, and what are its costs?**

   **Answer.** Assignment takes or creates a copy in a temporary; all throwing copy/allocation work happens before the original changes. A non-throwing `swap` commits, and the temporary destroys the old state. Costs can include an extra allocation/copy, failure to reuse existing capacity, larger peak memory, and unnecessary work for self-assignment. A tailored two-phase update can be faster while preserving the same guarantee.

5. **[Deep dive] Why should destructors, `swap`, and resource-release operations usually be `noexcept`?**

   **Answer.** They are the mechanisms used during cleanup and rollback. If they throw while another exception is propagating, the program terminates; if `swap` can throw, copy-and-swap cannot guarantee an atomic commit. Failures from inherently fallible close/flush operations should be exposed through an explicit method before destruction, while the destructor performs best-effort non-throwing cleanup.

6. **[Deep dive] How do throwing element move constructors affect `std::vector` during reallocation?**

   **Answer.** To preserve the strong guarantee, `vector` generally moves when move construction is non-throwing or copying is unavailable, and copies when copying is available but moving may throw. If a type is move-only and its move can throw, an exception during relocation may leave already moved-from source elements; for relevant vector operations the guarantee can be weakened and effects may be unspecified under the standard's stated exceptions. Make honest move constructors `noexcept` when possible.

7. **[Code] Which guarantee does this function provide, and how can it be strengthened?**

   ```cpp
   void Account::transfer_to(Account& target, Money amount) {
       balance_ -= amount;
       target.add(amount); // may throw
   }
   ```

   **Answer.** If `target.add` throws, the source remains debited, so there is no strong guarantee; assuming all invariants still hold and no resources leak, it provides at most the basic guarantee and may violate the business invariant that money is conserved. Validate first and perform all throwing work on temporary account states, then commit both with non-throwing swaps. A manual rollback is acceptable only if restoring `balance_` cannot throw and covers every failure path; concurrency requires locking both accounts with a deadlock-safe strategy before validation/commit.

8. **[Code] Propose a strategy for testing exception safety in a class that performs several allocations.**

   **Answer.** Inject a deterministic allocator/resource that throws on the Nth allocation. For every `N` from the first allocation through successful completion, start from a known object snapshot, execute the operation, catch the injected exception, then verify invariants, observable state promised by the selected guarantee, live-object/resource counts, and continued usability/destruction. Run under AddressSanitizer/LeakSanitizer (or the platform equivalent), include copy/move/member-operation fault injection—not only allocation—and test success, self-assignment, and boundary sizes.

---

# Assessment usage notes

- For a short screening, select 12–15 **[Basic]** questions and 2–3 **[Code]** exercises from different sections.
- For a 60–90 minute interview, select 5–7 topics, begin with a basic question, and use the deeper questions according to the candidate's answers.
- Ask for consequences, not only rule names: correctness, ownership, lifetime, ABI, performance, and maintainability reveal actual understanding.
- In code-review exercises, evaluate the reasoning process and the candidate's ability to state assumptions, not only whether they recall exact standard wording.
- If a candidate does not remember a term, ask for an example or behavior prediction; terminology and understanding are related but not identical.
