# COM and Excel Technical Interview Questions and Answers

> Reusable COM, apartment-threading and Excel Automation question bank. Prepared knowledge must not be presented as past production experience.

# Question Index

Questions use stable topic-specific IDs. Every answer begins with a short bullet summary and keeps details, examples and edge cases below it.

## COM Fundamentals (COM-001–COM-014)

- [COM-001. What is COM?](#question-com-001)
- [COM-002. What is `IUnknown`?](#question-com-002)
- [COM-003. What does `QueryInterface` do?](#question-com-003)
- [COM-004. What do `AddRef` and `Release` do?](#question-com-004)
- [COM-005. What is a GUID / IID / CLSID?](#question-com-005)
- [COM-006. What is `CoCreateInstance`?](#question-com-006)
- [COM-007. In-process vs out-of-process COM server?](#question-com-007)
- [COM-008. What is a COM class factory?](#question-com-008)
- [COM-009. What is `HRESULT`?](#question-com-009)
- [COM-010. What is `BSTR`?](#question-com-010)
- [COM-011. What is `VARIANT`?](#question-com-011)
- [COM-012. What is `SAFEARRAY`?](#question-com-012)
- [COM-013. What is `IDispatch`?](#question-com-013)
- [COM-014. Early binding vs late binding?](#question-com-014)

## COM Apartments and Threading (COM-015–COM-022)

- [COM-015. What is a COM apartment?](#question-com-015)
- [COM-016. `CoInitialize` vs `CoInitializeEx`?](#question-com-016)
- [COM-017. What is STA?](#question-com-017)
- [COM-018. What is MTA?](#question-com-018)
- [COM-019. Can you pass a COM interface pointer directly to another thread?](#question-com-019)
- [COM-020. What is COM marshaling?](#question-com-020)
- [COM-021. Why can using Excel COM objects from worker threads be problematic?](#question-com-021)
- [COM-022. Why does an STA usually need a message pump?](#question-com-022)

## Excel Add-In and Office Integration (COM-023–COM-028)

- [COM-023. What is a COM Add-In?](#question-com-023)
- [COM-024. What is `IDTExtensibility2`?](#question-com-024)
- [COM-025. What are important Excel Object Model objects?](#question-com-025)
- [COM-026. Why is reading cells one-by-one through COM slow?](#question-com-026)
- [COM-027. How would you efficiently read a large Excel range?](#question-com-027)
- [COM-028. How would you update many Excel cells efficiently?](#question-com-028)

---

# 1. COM Fundamentals

## Question COM-001

[↑ Back to question index](#question-index)

### Question COM-001 — What is COM?

**Short answer**

- COM is Microsoft's binary component model: clients use stable interfaces identified by GUIDs rather than concrete C++ class layouts.
- Core contracts include `IUnknown` interface discovery, reference-counted lifetime, HRESULT error reporting and marshaling across apartments/processes.
- It enables in-process and out-of-process language-neutral components, but callers must obey registration/activation, ABI, ownership and threading rules.

**Details and nuances**

COM is Microsoft's binary component model for interoperable software components.

It defines:

- binary interface conventions
- interface discovery
- lifetime management
- activation
- marshaling between threads/processes

It allows components compiled with different languages/tools to interact through stable ABI-level interfaces.

[↑ Back to question index](#question-index)

---

## Question COM-002

[↑ Back to question index](#question-index)

### Question COM-002 — What is `IUnknown`?

**Short answer**

- `IUnknown` is the root COM interface with `QueryInterface`, `AddRef` and `Release`.
- It defines interface discovery and reference-counted ownership; a successful interface acquisition returns a reference the caller must release.
- All interfaces on one COM identity must agree on the controlling `IUnknown`, while aggregation can make the implementation details subtler.

**Details and nuances**

The fundamental COM interface.

It exposes:

```cpp
QueryInterface
AddRef
Release
```

Every COM interface ultimately derives from the `IUnknown` contract.

[↑ Back to question index](#question-index)

---

## Question COM-003

[↑ Back to question index](#question-index)

### Question COM-003 — What does `QueryInterface` do?

**Short answer**

- It asks an object for an interface by IID without exposing the concrete implementation.
- Success returns the requested pointer with an added reference; failure normally returns `E_NOINTERFACE` and no usable pointer.
- The supported interface set should be stable and obey identity/reflexivity/symmetry/transitivity expectations, so capability discovery remains reliable.

**Details and nuances**

It asks a COM object whether it supports a specific interface identified by IID.

If supported, it returns an interface pointer and increments its reference count.

It enables interface discovery without depending on concrete object types.

[↑ Back to question index](#question-index)

---

## Question COM-004

[↑ Back to question index](#question-index)

### Question COM-004 — What do `AddRef` and `Release` do?

**Short answer**

- Each owned interface reference is balanced: `AddRef` acquires another ownership claim and `Release` relinquishes one; the object normally deletes itself at zero.
- Copying a pointer, returning it, or storing it across a scope requires the correct ownership convention; raw pointer assignment alone does not increment the count.
- Prefer RAII wrappers such as `wil::com_ptr`, WRL `ComPtr` or `_com_ptr_t`; reference counts do not collect cycles or make object methods thread-safe.

**Details and nuances**

They implement COM reference counting.

- `AddRef()` increments reference count
- `Release()` decrements it
- object normally destroys itself when the count reaches zero

Correct ownership discipline is essential.

Prefer a COM smart pointer so every acquired interface reference is released on all exits:

```cpp
#include <wrl/client.h>

Microsoft::WRL::ComPtr<IUnknown> object;

HRESULT hr = CoCreateInstance(
    someClassId,
    nullptr,
    CLSCTX_INPROC_SERVER,
    IID_PPV_ARGS(object.GetAddressOf()));

if (FAILED(hr)) {
    return hr;
}

// object.ReleaseAndGetAddressOf() is useful for an out-parameter that replaces
// an existing pointer. GetAddressOf() does not release an existing reference.
```

Raw-pointer code must document whether a parameter is borrowed, transferred or returned with an extra reference. `QueryInterface` and successful COM out-parameters normally return an owned reference that the caller must eventually release.

[↑ Back to question index](#question-index)

---

## Question COM-005

[↑ Back to question index](#question-index)

### Question COM-005 — What is a GUID / IID / CLSID?

**Short answer**

- A GUID is a 128-bit identifier; an IID names an interface contract and a CLSID names a creatable COM class.
- COM uses these stable identifiers across languages, modules and registration instead of compiler-specific C++ names.
- Changing an interface incompatibly requires a new IID; implementation revisions behind an unchanged binary contract do not.

**Details and nuances**

A GUID is a globally unique identifier.

Common COM uses:

- **IID** — identifies an interface
- **CLSID** — identifies a COM class

These identifiers allow binary components to refer to interfaces/classes without relying on C++ names.

[↑ Back to question index](#question-index)

---

## Question COM-006

[↑ Back to question index](#question-index)

### Question COM-006 — What is `CoCreateInstance`?

**Short answer**

- `CoCreateInstance` is a convenience activation API: given a CLSID, class context and IID, COM locates a server, creates an object and returns the requested interface.
- The calling thread must initialize COM, check the `HRESULT` and own/release the returned reference.
- Activation may load a DLL or communicate with an EXE/service; registration, bitness and security/context settings can therefore affect failure.

**Details and nuances**

It asks COM to create an instance of a registered COM class.

Inputs include:

- CLSID
- activation context
- requested IID

COM locates and activates the corresponding server and returns the requested interface.

[↑ Back to question index](#question-index)

---

## Question COM-007

[↑ Back to question index](#question-index)

### Question COM-007 — In-process vs out-of-process COM server?

**Short answer**

- An in-process server is a DLL loaded into the client: calls are cheap, but ABI/bitness must match and faults corrupt or terminate the host.
- An out-of-process server is typically an EXE: process isolation and independent lifetime improve robustness, but calls require marshaling/IPC and cost more.
- Choose from latency, isolation, deployment, privilege, architecture compatibility and recovery requirements—not interface syntax alone.

**Details and nuances**

**In-process server**

Usually a DLL loaded into the client process.

Pros:

- lower call overhead

Cons:

- crash can take down host process

**Out-of-process server**

Usually an EXE.

Pros:

- isolation

Cons:

- marshaling and IPC overhead

[↑ Back to question index](#question-index)

---

## Question COM-008

[↑ Back to question index](#question-index)

### Question COM-008 — What is a COM class factory?

**Short answer**

- A class factory is the activation object, normally implementing `IClassFactory::CreateInstance` to construct a CLSID's objects.
- In-process COM obtains it through `DllGetClassObject`; local servers register factories while running.
- `LockServer` can influence unload/lifetime, and aggregation support must be handled explicitly during creation.

**Details and nuances**

A class factory creates COM objects.

The standard interface is `IClassFactory`.

COM may obtain a class factory and ask it to create instances of a requested COM class.

[↑ Back to question index](#question-index)

---

## Question COM-009

[↑ Back to question index](#question-index)

### Question COM-009 — What is `HRESULT`?

**Short answer**

- `HRESULT` is a structured 32-bit status value containing severity/facility/code information; test with `SUCCEEDED`/`FAILED`, not only `== S_OK`.
- `S_FALSE` is success with a distinct result, while failures may carry richer COM error information.
- At ABI boundaries translate exceptions/system errors to stable HRESULTs, initialize out parameters defensively and never let C++ exceptions escape.

**Details and nuances**

A 32-bit status code commonly returned by COM APIs.

Use helpers/macros such as:

```cpp
SUCCEEDED(hr)
FAILED(hr)
```

A negative severity bit generally indicates failure.

At a COM boundary, translate C++ failures into stable `HRESULT` values and never let exceptions escape across the ABI:

```cpp
HRESULT Component::Calculate(Result* result) noexcept {
    if (result == nullptr) {
        return E_POINTER;
    }

    try {
        *result = calculateImpl();
        return S_OK;
    } catch (const std::bad_alloc&) {
        return E_OUTOFMEMORY;
    } catch (...) {
        return E_FAIL;
    }
}
```

Also distinguish `S_OK` from `S_FALSE`: both satisfy `SUCCEEDED(hr)`, but `S_FALSE` may communicate a meaningful non-error result.

[↑ Back to question index](#question-index)

---

## Question COM-010

[↑ Back to question index](#question-index)

### Question COM-010 — What is `BSTR`?

**Short answer**

- `BSTR` is an Automation string with a length prefix and UTF-16-compatible character storage allocated by the Automation allocator.
- It can contain embedded nulls, so use `SysStringLen`/the recorded length rather than C-string scanning.
- Own it with `SysAllocString*`/`SysFreeString` or an RAII wrapper and follow `[in]`/`[out]` ownership rules; never free it with `delete`.

**Details and nuances**

A COM string type used by Automation.

It is length-prefixed and normally allocated/freed with COM/OLE functions such as:

```cpp
SysAllocString
SysFreeString
```

It is not just a raw null-terminated `wchar_t*`.

[↑ Back to question index](#question-index)

---

## Question COM-011

[↑ Back to question index](#question-index)

### Question COM-011 — What is `VARIANT`?

**Short answer**

- `VARIANT` is a tagged union for Automation values; `vt` determines which union member and ownership rules are active.
- Initialize and clear it correctly (`VariantInit`/`VariantClear` or RAII), and use `VariantChangeType` only when coercion is intended and checked.
- Handle `VT_EMPTY`, `VT_NULL`, `VT_ERROR`, `VT_BYREF`, interface references and array flags explicitly; a mismatched tag/member can corrupt memory.

**Details and nuances**

A tagged union used by COM Automation to represent values of different runtime types.

It can hold values such as:

- integers
- doubles
- BSTR
- COM interface pointers
- arrays
- empty/null states

Its lifetime must be managed correctly, commonly with `VariantInit` and `VariantClear`.

```cpp
#include <OleAuto.h>

VARIANT value;
VariantInit(&value);

value.vt = VT_BSTR;
value.bstrVal = SysAllocString(L"Excel value");
if (value.bstrVal == nullptr) {
    // handle allocation failure before using value
}

// Pass value to Automation APIs while it is alive.
VariantClear(&value); // frees the BSTR according to the active tag
```

The `vt` tag and active union member must always agree. In production C++, prefer a proven RAII wrapper such as `_variant_t`, `wil::unique_variant` or an equivalent project-standard type.

[↑ Back to question index](#question-index)

---

## Question COM-012

[↑ Back to question index](#question-index)

### Question COM-012 — What is `SAFEARRAY`?

**Short answer**

- `SAFEARRAY` is a self-describing COM array carrying element type, dimensions, lower bounds and lengths.
- Access through the SafeArray APIs or lock/unlock data with strict cleanup; indices need not start at zero and multidimensional layout must be interpreted correctly.
- Ownership depends on the containing parameter/`VARIANT`; destroy only arrays you own and deep-copy when a boundary requires independent lifetime.

**Details and nuances**

A COM-managed array representation that stores metadata such as:

- element type
- dimensions
- bounds

Frequently used together with `VARIANT` for Automation and Excel range data.

[↑ Back to question index](#question-index)

---

## Question COM-013

[↑ Back to question index](#question-index)

### Question COM-013 — What is `IDispatch`?

**Short answer**

- `IDispatch` is the Automation late-binding interface: resolve names to DISPIDs, then call properties/methods through `Invoke` with `VARIANT` arguments.
- It enables scripting and version-flexible clients but moves type checking and argument conversion to runtime and adds dispatch overhead.
- Correct calls must handle invocation flags, reversed positional arguments, named property-put arguments, HRESULTs and `EXCEPINFO`.

**Details and nuances**

`IDispatch` supports late-bound Automation.

A client can:

- resolve method/property names to DISPIDs
- invoke methods/properties dynamically

It is widely used in Office Automation.

[↑ Back to question index](#question-index)

---

## Question COM-014

[↑ Back to question index](#question-index)

### Question COM-014 — Early binding vs late binding?

**Short answer**

- Early binding compiles against a known vtable/type-library contract, giving type checking, discoverability and lower invocation overhead.
- Late binding uses names/DISPIDs and `IDispatch::Invoke`, improving dynamic compatibility but deferring errors and conversions to runtime.
- Both still obey COM lifetime/apartment rules; choose based on version tolerance, deployment and client-language needs, and cache DISPIDs in hot late-bound paths.

**Details and nuances**

**Early binding**

Compiler knows interface/type information.

Pros:

- type safety
- faster calls
- compile-time checking

**Late binding**

Typically through `IDispatch`.

Pros:

- dynamic flexibility

Cons:

- runtime lookup
- weaker type checking
- more overhead

[↑ Back to question index](#question-index)

---

# 2. COM Apartments and Threading

## Question COM-015

[↑ Back to question index](#question-index)

### Question COM-015 — What is a COM apartment?

**Short answer**

- An apartment is COM's concurrency boundary associating initialized threads and objects with a calling/synchronization model.
- STA objects receive calls serially on their apartment thread; MTA objects may receive concurrent calls. Crossing boundaries normally requires marshaling/proxies.
- Every COM-using thread initializes its apartment once, respects interface agility and keeps the required message pump/lifetime alive.

**Details and nuances**

An apartment defines COM's threading and synchronization model for objects and threads.

Common models:

- STA — Single-Threaded Apartment
- MTA — Multi-Threaded Apartment

A thread joins an apartment when COM is initialized on that thread.

[↑ Back to question index](#question-index)

---

## Question COM-016

[↑ Back to question index](#question-index)

### Question COM-016 — `CoInitialize` vs `CoInitializeEx`?

**Short answer**

- `CoInitialize` is effectively `CoInitializeEx(..., COINIT_APARTMENTTHREADED)`; `CoInitializeEx` explicitly selects STA or MTA and related options.
- Each thread calls it before COM use and balances every successful `S_OK` or `S_FALSE` with `CoUninitialize`.
- Trying to change an already-chosen model returns `RPC_E_CHANGED_MODE`; initialization and cleanup must therefore be designed per thread, not globally.

**Details and nuances**

`CoInitialize` initializes COM in STA mode.

`CoInitializeEx` lets you choose apartment model explicitly, for example:

```cpp
CoInitializeEx(nullptr, COINIT_APARTMENTTHREADED);
```

or:

```cpp
CoInitializeEx(nullptr, COINIT_MULTITHREADED);
```

Each thread using COM must initialize it appropriately.

[↑ Back to question index](#question-index)

---

## Question COM-017

[↑ Back to question index](#question-index)

### Question COM-017 — What is STA?

**Short answer**

- A single-threaded apartment has exactly one thread; apartment-bound object calls are dispatched serially on that thread.
- Cross-apartment callers receive proxies, and the STA thread normally needs a message loop to dispatch calls and avoid starvation/deadlock.
- STA means serialized entry, not “no reentrancy”: outgoing calls and message pumping can allow callbacks, so invariants still need care.

**Details and nuances**

In an STA, COM ensures calls into apartment-bound objects are serialized onto the apartment's owning thread.

STA often relies on a Windows message loop for call dispatch.

Office applications commonly expose automation objects associated with STA behavior.

[↑ Back to question index](#question-index)

---

## Question COM-018

[↑ Back to question index](#question-index)

### Question COM-018 — What is MTA?

**Short answer**

- The multithreaded apartment can contain multiple threads, and objects there may be called concurrently on different threads.
- Components must provide their own synchronization and must not assume thread affinity.
- MTA reduces STA dispatch constraints but does not make STA-bound interfaces such as much of Office Automation freely callable; marshaling rules still apply.

**Details and nuances**

In an MTA, multiple threads can receive COM calls concurrently.

Objects used there must be designed for concurrency.

MTA avoids some STA dispatch constraints but requires thread-safe components.

[↑ Back to question index](#question-index)

---

## Question COM-019

[↑ Back to question index](#question-index)

### Question COM-019 — Can you pass a COM interface pointer directly to another thread?

**Short answer**

- Not in general: an interface pointer is valid under its apartment/agility contract, and copying its raw bits to another apartment can bypass required proxies.
- Marshal it with standard mechanisms such as `CoMarshalInterThreadInterfaceInStream`/`CoGetInterfaceAndReleaseStream` or the Global Interface Table.
- Direct reuse is valid only for interfaces known to be agile/free-threaded under their documented contract; lifetime still requires an owned reference.

**Details and nuances**

Not safely in the general case.

COM interface pointers are subject to apartment rules.

When crossing apartment boundaries, the interface may need to be marshaled so COM can provide an appropriate proxy.

[↑ Back to question index](#question-index)

---

## Question COM-020

[↑ Back to question index](#question-index)

### Question COM-020 — What is COM marshaling?

**Short answer**

- Marshaling packages an interface reference and call data so COM can represent it safely across apartment or process boundaries.
- The receiving side normally gets a proxy; COM routes calls to the object's apartment/server through registered type information, standard or custom marshalers.
- It preserves interface semantics but adds latency, serialization, reentrancy and failure modes—so reduce chatty boundary crossings.

**Details and nuances**

Marshaling converts an interface reference into a representation usable across apartment or process boundaries.

COM may create proxies/stubs so calls can safely cross those boundaries.

[↑ Back to question index](#question-index)

---

## Question COM-021

[↑ Back to question index](#question-index)

### Question COM-021 — Why can using Excel COM objects from worker threads be problematic?

**Short answer**

- Excel's Automation object model is effectively UI/STA-affine and not designed as a freely concurrent object graph.
- Worker calls can require marshaling back to Excel, block while Excel is busy, trigger retries/reentrancy or deadlock with UI waits and application locks.
- Keep COM access on one owning Excel/UI thread; copy plain data to workers for computation and return batched results for bulk range updates.

**Details and nuances**

Excel's object model is heavily tied to its main/UI apartment.

Calling it from arbitrary worker threads can cause:

- marshaling overhead
- reentrancy issues
- blocking
- invalid apartment access patterns
- difficult deadlocks

A common architecture is:

```text
worker threads
    ↓
queue / batch
    ↓
Excel/UI/COM thread
```

[↑ Back to question index](#question-index)

---

## Question COM-022

[↑ Back to question index](#question-index)

### Question COM-022 — Why does an STA usually need a message pump?

**Short answer**

- COM often delivers cross-apartment calls to an STA through its message queue, so the owner must dispatch messages.
- If that thread blocks without pumping, incoming calls and completion callbacks can stall, creating hangs or circular waits.
- Use UI/message-aware waits and avoid synchronous dependency cycles; remember that pumping introduces reentrancy and protect invariants accordingly.

**Details and nuances**

COM may dispatch cross-apartment calls through Windows messages.

If the STA thread stops pumping messages, COM calls can stall or deadlock.

This is particularly important for UI applications and Office automation.

[↑ Back to question index](#question-index)

---

# 3. Excel Add-In and Office Integration

## Question COM-023

[↑ Back to question index](#question-index)

### Question COM-023 — What is a COM Add-In?

**Short answer**

- A COM Add-In is a registered COM component loaded/activated by an Office host to extend UI and react to application lifecycle/events.
- It runs in or closely coupled to the host, so bitness, registration, trust, threading, reference lifetime and fault isolation are critical.
- Keep callbacks fast, detach/release everything during shutdown and move CPU-heavy work away from the Office thread without moving Excel objects themselves.

**Details and nuances**

A COM Add-In is a COM component loaded by an Office application.

It can integrate with Excel lifecycle and object model.

Historically, Office COM Add-Ins often use interfaces such as `IDTExtensibility2`.

[↑ Back to question index](#question-index)

---

## Question COM-024

[↑ Back to question index](#question-index)

### Question COM-024 — What is `IDTExtensibility2`?

**Short answer**

- `IDTExtensibility2` is the classic Office COM add-in lifecycle interface with connection, startup, add/remove and disconnection callbacks.
- `OnConnection` supplies the host application object and connection mode; `OnDisconnection` is where events, UI hooks and retained COM references must be released.
- Treat callbacks as host-controlled boundaries: validate state, return promptly, translate failures and make partial startup/duplicate shutdown safe.

**Details and nuances**

A classic Office extensibility interface used for add-in lifecycle callbacks such as:

- connection
- startup complete
- disconnection
- add-in updates

The exact architecture depends on the add-in technology used.

[↑ Back to question index](#question-index)

---

## Question COM-025

[↑ Back to question index](#question-index)

### Question COM-025 — What are important Excel Object Model objects?

**Short answer**

- The common hierarchy is `Application → Workbooks → Workbook → Worksheets → Worksheet → Range`, with charts, names, tables and events around it.
- `Range` is the performance-critical bulk data boundary; use `Value2` for arrays of values and avoid long chains of per-cell properties.
- Cache only what has a clear lifetime, release COM wrappers deterministically and fully qualify objects to the intended workbook/sheet.

**Details and nuances**

Common hierarchy:

```text
Application
  ↓
Workbooks
  ↓
Workbook
  ↓
Worksheets
  ↓
Worksheet
  ↓
Range
```

`Range` is especially important for reading and writing cell blocks efficiently.

[↑ Back to question index](#question-index)

---

## Question COM-026

[↑ Back to question index](#question-index)

### Question COM-026 — Why is reading cells one-by-one through COM slow?

**Short answer**

- Every property access crosses a COM/Automation boundary with dispatch, type conversion, reference-counting and possible apartment marshaling overhead.
- Tens of thousands of tiny calls dominate the actual data work and can repeatedly involve Excel's UI/calculation state.
- Read or write a rectangular range in one `Value2` operation, process the returned 2-D data locally and minimize object-model round trips.

**Details and nuances**

Every COM property/method call has overhead.

Doing:

```text
1,000,000 cells
×
1 COM call per cell
```

can be dramatically slower than fetching one large `Range`.

The usual optimization is batching.

[↑ Back to question index](#question-index)

---

## Question COM-027

[↑ Back to question index](#question-index)

### Question COM-027 — How would you efficiently read a large Excel range?

**Short answer**

- Resolve the target `Range` once and fetch its `Value2` once; for multiple cells Excel normally returns a two-dimensional `SAFEARRAY` inside a `VARIANT`.
- Validate scalar-vs-array shape, bounds and element variants, then copy/convert to native contiguous data and release COM access before heavy work.
- Chunk only when memory, responsiveness or Excel limits require it; benchmark chunk size and keep all Excel access on the correct apartment.

**Details and nuances**

Prefer:

```text
one Range request
↓
VARIANT / SAFEARRAY
↓
process locally in C++
```

instead of one COM call per cell.

This reduces boundary crossings drastically.

[↑ Back to question index](#question-index)

---

## Question COM-028

[↑ Back to question index](#question-index)

### Question COM-028 — How would you update many Excel cells efficiently?

**Short answer**

- Coalesce changes, build a 2-D bulk value array and assign whole ranges with as few COM calls as possible.
- Throttle UI refresh and, when appropriate, temporarily adjust calculation, events and screen updating—but always restore the prior state with RAII/finally-style cleanup.
- Perform computation off-thread on plain data, then marshal only the final batch to the Excel-owning thread; never hold application locks during COM calls.

**Details and nuances**

Use:

- coalescing
- batching
- range writes
- reduced update frequency
- minimal COM round-trips
- avoid unnecessary recalculation/redraw when possible

For real-time feeds, intermediate values often do not need to be displayed individually.

[↑ Back to question index](#question-index)
