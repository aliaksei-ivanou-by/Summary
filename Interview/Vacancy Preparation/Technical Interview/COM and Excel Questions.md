# COM and Excel Technical Interview Questions and Answers

> Reusable COM, apartment-threading and Excel Automation question bank. Prepared knowledge must not be presented as past production experience.

# Question Index

Questions use stable topic-specific IDs. Every answer begins with a short bullet summary and keeps details, examples and edge cases below it.

## COM Fundamentals (COM-001–COM-014)

|  |  |  |
|---|---|---|
| [COM-001. What is COM?](#question-com-001) | [COM-002. What is `IUnknown`?](#question-com-002) | [COM-003. What does `QueryInterface` do?](#question-com-003) |
| [COM-004. What do `AddRef` and `Release` do?](#question-com-004) | [COM-005. What is a GUID / IID / CLSID?](#question-com-005) | [COM-006. What is `CoCreateInstance`?](#question-com-006) |
| [COM-007. In-process vs out-of-process COM server?](#question-com-007) | [COM-008. What is a COM class factory?](#question-com-008) | [COM-009. What is `HRESULT`?](#question-com-009) |
| [COM-010. What is `BSTR`?](#question-com-010) | [COM-011. What is `VARIANT`?](#question-com-011) | [COM-012. What is `SAFEARRAY`?](#question-com-012) |
| [COM-013. What is `IDispatch`?](#question-com-013) | [COM-014. Early binding vs late binding?](#question-com-014) |  |

## COM Apartments and Threading (COM-015–COM-022)

|  |  |  |
|---|---|---|
| [COM-015. What is a COM apartment?](#question-com-015) | [COM-016. `CoInitialize` vs `CoInitializeEx`?](#question-com-016) | [COM-017. What is STA?](#question-com-017) |
| [COM-018. What is MTA?](#question-com-018) | [COM-019. Can you pass a COM interface pointer directly to another thread?](#question-com-019) | [COM-020. What is COM marshaling?](#question-com-020) |
| [COM-021. Why can using Excel COM objects from worker threads be problematic?](#question-com-021) | [COM-022. Why does an STA usually need a message pump?](#question-com-022) |  |

## Excel Add-In and Office Integration (COM-023–COM-028)

|  |  |  |
|---|---|---|
| [COM-023. What is a COM Add-In?](#question-com-023) | [COM-024. What is `IDTExtensibility2`?](#question-com-024) | [COM-025. What are important Excel Object Model objects?](#question-com-025) |
| [COM-026. Why is reading cells one-by-one through COM slow?](#question-com-026) | [COM-027. How would you efficiently read a large Excel range?](#question-com-027) | [COM-028. How would you update many Excel cells efficiently?](#question-com-028) |

## Add-In Technologies (COM-029–COM-032)

|  |  |  |
|---|---|---|
| [COM-029. COM Add-In, XLL, VSTO and Office.js: how do they differ and when is each used?](#question-com-029) | [COM-030. What is an XLL, and what does the Excel C API give you that Automation does not?](#question-com-030) | [COM-031. What is VSTO, and what does it add and require?](#question-com-031) |
| [COM-032. How is a COM Add-In registered and loaded, and what does `LoadBehavior` control?](#question-com-032) |  |  |

## Excel Performance Controls and Real-Time Data (COM-033–COM-036)

|  |  |  |
|---|---|---|
| [COM-033. Which `Application` settings speed up bulk Excel work, and why must they be restored?](#question-com-033) | [COM-034. `Value` vs `Value2` vs `Text`?](#question-com-034) | [COM-035. What is an RTD server, and how does Excel drive it?](#question-com-035) |
| [COM-036. What is an asynchronous UDF, and how does it differ from RTD?](#question-com-036) |  |  |

## Office.js and Client-Side Scripting (COM-037–COM-040)

|  |  |  |
|---|---|---|
| [COM-037. What is Office.js, and where does it run?](#question-com-037) | [COM-038. How do `load()` and `context.sync()` work, and why is batching essential?](#question-com-038) | [COM-039. How does Office.js differ from COM Automation for the same task?](#question-com-039) |
| [COM-040. What are Office.js custom functions, and how does streaming work?](#question-com-040) |  |  |

## Staleness, Teardown and Testing (COM-041–COM-042)

|  |  |  |
|---|---|---|
| [COM-041. How do you keep a background computation from using stale workbook state?](#question-com-041) | [COM-042. How would you test update bursts, workbook closure and shutdown races?](#question-com-042) |  |

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

---

# 4. Add-In Technologies

## Question COM-029

[↑ Back to question index](#question-index)

### Question COM-029 — COM Add-In, XLL, VSTO and Office.js: how do they differ and when is each used?

**Short answer**

- A **COM Add-In** implements `IDTExtensibility2`, is registered in the registry and drives Excel through the Automation object model; a native **XLL** links against the Excel C API and is the fastest path for worksheet functions; **VSTO** is a managed .NET add-in with designer support and a runtime dependency; **Office.js** is a sandboxed JavaScript add-in described by a manifest.
- The first three are Windows-desktop-only and bitness-specific; Office.js is the only one that also runs on Excel for the web, Mac and iPad.
- Pick by the primary job: worksheet-function throughput → XLL; deep native desktop integration → COM Add-In; managed, UI-heavy desktop → VSTO; cross-platform reach or store distribution → Office.js.

**Details and nuances**

| | COM Add-In | XLL | VSTO | Office.js |
|---|---|---|---|---|
| Interface | `IDTExtensibility2` + Automation | Excel C API (`xlcall.h`) | .NET + Automation interop | JavaScript API + manifest |
| Language | C++/ATL, C#, any COM-capable | C/C++ | .NET | JavaScript/TypeScript |
| Primary strength | Full object-model access from native code | Worksheet functions with lowest overhead | Ribbon/task-pane tooling, managed code | Reach and sandboxing |
| Worksheet functions | Automation UDFs, single-threaded | Native UDFs, can be registered thread-safe and asynchronous | Managed UDFs, awkward | Custom functions, asynchronous |
| Platforms | Windows desktop | Windows desktop | Windows desktop | Windows, Mac, web, iPad |
| Deployment | Registry + installer | File + installer | ClickOnce / MSI + VSTO runtime | Manifest: sideload, central deployment, AppSource |
| Bitness | Must match Excel | Must match Excel | Must match Excel | Not applicable |

They are not exclusive. A common shape is an XLL for the calculation-critical functions plus a COM Add-In or task pane for the UI, sharing one native core.

**Example or evidence boundary**

Prepared knowledge. I have not shipped any of the four; this is the comparison I would use to place a product before discussing its design.

[↑ Back to question index](#question-index)

---

## Question COM-030

[↑ Back to question index](#question-index)

### Question COM-030 — What is an XLL, and what does the Excel C API give you that Automation does not?

**Short answer**

- An XLL is a native DLL that Excel loads directly; it exports `xlAutoOpen`/`xlAutoClose`, registers its functions with `xlfRegister` and exchanges data as `XLOPER12` rather than through `IDispatch`.
- It removes the Automation dispatch layer, so it is the lowest-overhead way to add worksheet functions, and it can register functions as thread-safe so Excel's multithreaded recalculation may call them on several worker threads.
- The cost is a raw C interface: manual memory conventions, no type safety and a straightforward path to crashing the host process, plus a build that must match Excel's bitness.

**Details and nuances**

The registration string passed to `xlfRegister` carries type markers that change what Excel may do with the function, including a marker for thread safety and, since Excel 2010, one for asynchronous execution. Getting those markers wrong is a classic source of instability: a function declared thread-safe that touches shared mutable state will fail intermittently under multithreaded recalculation and look like a data bug rather than a concurrency bug.

Memory ownership is explicit. Excel and the XLL both allocate `XLOPER12` values, and the side that allocated is the side that frees; `xlFree` and the `xlbitDLLFree` flag exist for exactly that handover.

**Example or evidence boundary**

Prepared knowledge, not production experience. The transferable part is real: a C ABI with explicit ownership rules and a host process that dies on a mistake is the same discipline as the C89 work on the library platform.

[↑ Back to question index](#question-index)

---

## Question COM-031

[↑ Back to question index](#question-index)

### Question COM-031 — What is VSTO, and what does it add and require?

**Short answer**

- VSTO is the managed .NET model for Office extensions: application-level add-ins that live with Excel, or document-level customizations bound to one workbook.
- It gives designer-based Ribbon customization, custom task panes and the .NET library surface, at the cost of the VSTO runtime on every client and a trust/deployment story through ClickOnce or an installer.
- Underneath it still goes through COM interop, so every object-model batching rule applies unchanged, plus garbage-collection and interop-lifetime concerns of its own.

**Details and nuances**

The managed layer hides `AddRef`/`Release` but does not remove them: interop wrappers hold COM references, and objects released only at collection time can keep Excel alive after the user closes it. That is the well-known "Excel stays in Task Manager" symptom, and the fix is the same discipline as in native code - do not build long chains of temporary object-model references, and release deliberately.

**Example or evidence boundary**

Prepared knowledge. My managed Windows experience is the C#/.NET 8 WinForms provisioning tool, which is desktop delivery rather than Office integration.

[↑ Back to question index](#question-index)

---

## Question COM-032

[↑ Back to question index](#question-index)

### Question COM-032 — How is a COM Add-In registered and loaded, and what does `LoadBehavior` control?

**Short answer**

- The class is registered as an ordinary COM server (CLSID and ProgID under `HKCR`), and Excel discovers it from `Software\Microsoft\Office\Excel\Addins\<ProgID>` under `HKCU` or `HKLM`.
- That key carries `FriendlyName`, `Description` and `LoadBehavior`, which decides whether the add-in loads at startup or on demand.
- Bitness must match: a 32-bit in-process server cannot load into 64-bit Excel, and per-machine registration on a 64-bit system involves the `Wow6432Node` view.

**Details and nuances**

The detail worth knowing is the failure mode. If the add-in throws during `OnConnection`, Excel demotes `LoadBehavior` and moves the add-in to its disabled list. The visible symptom is "it worked yesterday and today it is simply not there", with no error. Diagnosis is to read the current `LoadBehavior` value and check the disabled items, then fix the startup path - not to reinstall.

`HKCU` registration is per-user and needs no elevation; `HKLM` is per-machine and does. For an add-in that must be present for every user of a machine, that choice is made at packaging time, not at development time.

**Example or evidence boundary**

Prepared knowledge. The adjacent real experience is installer packaging and per-user versus per-machine state in the Windows provisioning tool.

[↑ Back to question index](#question-index)

---

# 5. Excel Performance Controls and Real-Time Data

## Question COM-033

[↑ Back to question index](#question-index)

### Question COM-033 — Which `Application` settings speed up bulk Excel work, and why must they be restored?

**Short answer**

- `Application.Calculation = xlCalculationManual`, `Application.ScreenUpdating = False` and `Application.EnableEvents = False` remove the three costs that dominate a large write: recalculation per change, redraw per change and event handlers per change.
- These are global application state, not scoped to your add-in, so leaving them set breaks Excel for the user and silently disables every other add-in's event handlers.
- Save the previous values, restore them in a destructor or `finally`, and restore what was there rather than assuming the defaults - another add-in may already have changed them.

**Details and nuances**

`Application.DisplayAlerts` and the worksheet's `DisplayPageBreaks` belong to the same family. The order matters: switch calculation to manual before writing, write, then calculate explicitly and restore.

The interview point is not the list, it is the guarantee. An exception between "set" and "restore" leaves the user with an Excel that looks frozen and stops reacting to events, and no message explaining why. In C++ this is a plain RAII guard whose destructor restores the captured values; in managed or scripted code it is `try`/`finally`. Relying on Excel to reset `ScreenUpdating` when a macro ends is not a substitute, because an add-in is not a macro.

```cpp
class ExcelStateGuard {           // sketch, not production code
public:
    explicit ExcelStateGuard(ExcelApp& app) : app_(app),
        calc_(app.Calculation()), screen_(app.ScreenUpdating()), events_(app.EnableEvents()) {
        app_.SetCalculation(xlCalculationManual);
        app_.SetScreenUpdating(false);
        app_.SetEnableEvents(false);
    }
    ~ExcelStateGuard() {           // restores what was there, not the defaults
        app_.SetEnableEvents(events_);
        app_.SetScreenUpdating(screen_);
        app_.SetCalculation(calc_);
    }
private:
    ExcelApp& app_;
    long calc_; bool screen_; bool events_;
};
```

**Example or evidence boundary**

Prepared knowledge for the Excel specifics. The pattern itself is one I use: scoped restoration of state captured on entry, so an early return or a thrown exception cannot leave a subsystem in a mode the user did not choose.

[↑ Back to question index](#question-index)

---

## Question COM-034

[↑ Back to question index](#question-index)

### Question COM-034 — `Value` vs `Value2` vs `Text`?

**Short answer**

- `Value2` is the fastest and the right default for data: it does not use the Currency or Date variant types, so a date arrives as the underlying serial number.
- `Value` may return Currency and Date variants, which costs conversion on every element and is only worth it when you actually want those types.
- `Text` returns the formatted string as displayed, so it depends on column width and can come back as `#####`; it is for reading what the user sees, never for reading data.

**Details and nuances**

For a multi-cell range, `Value2` returns a `VARIANT` holding a two-dimensional `SAFEARRAY` of `VARIANT`, indexed from 1. A single-cell range returns a scalar rather than a 1×1 array, which is the shape bug that appears the first time a user selects one cell - check the shape before indexing.

If dates matter, convert the serial number yourself rather than paying for `Value` across the whole block: the conversion is cheap in native code and expensive across the Automation boundary.

**Example or evidence boundary**

Prepared knowledge.

[↑ Back to question index](#question-index)

---

## Question COM-035

[↑ Back to question index](#question-index)

### Question COM-035 — What is an RTD server, and how does Excel drive it?

**Short answer**

- An RTD server is a COM object implementing `IRtdServer` that feeds continuously changing values into cells through the worksheet function `=RTD("ProgID", server, topic...)`; it is Excel's built-in mechanism for live data.
- The direction is the important part: your feed thread updates internal state and calls `IRTDUpdateEvent::UpdateNotify()` to signal that something changed; **Excel then pulls** by calling `RefreshData` on its own thread, at its own pace, and you return only the topics that actually changed.
- `Application.RTD.ThrottleInterval` sets how often Excel is willing to pull, in milliseconds, so the display rate is decoupled from the feed rate by design rather than by your own timer.

**Details and nuances**

The interface is small: `ServerStart`, `ConnectData`, `RefreshData`, `DisconnectData`, `Heartbeat`, `ServerTerminate`. `ConnectData` is where a cell subscribes to a topic and `DisconnectData` where it unsubscribes, so topic lifetime is driven by the workbook, not by you.

This is the canonical answer to "20,000 updates per second and Excel needs ten refreshes per second": you do not push 20,000 times, you coalesce into a latest-value-per-topic store, call `UpdateNotify` when the store changes, and let the throttle interval decide the refresh rate. The architectural reasoning - bounded state, coalescing, one batched handover to the Excel thread - is the same as any other producer/consumer rate mismatch; RTD is the platform's name for it.

Limits worth knowing: `RefreshData` runs on Excel's thread and must return quickly, so all work belongs on your side of the boundary; scaling is per topic, so tens of thousands of distinct topics is its own problem; and because it is COM, the apartment and bitness rules of every other answer apply.

**Example or evidence boundary**

Prepared knowledge for RTD itself. The pattern is one I have implemented: on the phone platform, asynchronous SDK events arrived faster than the UI needed, and the fix was the same shape - coalesce into owned state, signal, and let the consumer take a consistent snapshot rather than pushing every event through to the interface.

[↑ Back to question index](#question-index)

---

## Question COM-036

[↑ Back to question index](#question-index)

### Question COM-036 — What is an asynchronous UDF, and how does it differ from RTD?

**Short answer**

- An asynchronous UDF is a worksheet function that returns immediately with a handle instead of a value; Excel leaves the cell pending, your code computes off Excel's thread and delivers the result through that handle when it is ready.
- The difference from RTD is what drives it: an asynchronous UDF is **pull-driven by recalculation** and produces one result per recalculation, while RTD is **signal-driven** and keeps updating a cell without any recalculation at all.
- Use an asynchronous UDF for a single slow computation - a remote call, a heavy model - and RTD for a value that changes continuously on its own.

**Details and nuances**

There is a third, separate mechanism that is easy to conflate: a **thread-safe** UDF. Registering a function as thread-safe lets Excel call it on several worker threads during multithreaded recalculation. That is parallelism within one recalculation, not asynchrony, and it carries the usual requirement - no shared mutable state, no object-model access.

So the three are orthogonal answers to three different problems: thread-safe for CPU-bound functions that can run in parallel, asynchronous for long latency, RTD for continuous change.

**Example or evidence boundary**

Prepared knowledge.

[↑ Back to question index](#question-index)

---

# 6. Office.js and Client-Side Scripting

## Question COM-037

[↑ Back to question index](#question-index)

### Question COM-037 — What is Office.js, and where does it run?

**Short answer**

- Office.js is the JavaScript/TypeScript API for Office Add-ins: the add-in is described by a manifest and its code runs inside an embedded browser view hosted by Excel, not in Excel's own process space.
- Because it is a sandboxed web application, it runs on Excel for Windows, Mac, the web and iPad - it is the only extension model that reaches all of them.
- The sandbox is also the limitation: no registry, no arbitrary native libraries, no synchronous object-model access, and an API surface narrower than COM Automation.

**Details and nuances**

Distribution is by manifest rather than by registry: sideloading for development, centralized deployment through the Microsoft 365 admin centre for an organization, or AppSource for public listing. For an engineer coming from COM, that is the change with the largest practical consequence - there is no per-machine install step and no bitness question.

When a vacancy says "JavaScript, particularly for client-side scripting" next to Excel, this is almost certainly what it means, and it is worth confirming early, because the answer decides whether the JavaScript in question is Office.js inside a task pane, a Node.js service behind the add-in, or both.

**Example or evidence boundary**

Prepared knowledge, plus a small hands-on exercise done during preparation. My JavaScript production work is backend and web-facing integration, not Office.

[↑ Back to question index](#question-index)

---

## Question COM-038

[↑ Back to question index](#question-index)

### Question COM-038 — How do `load()` and `context.sync()` work, and why is batching essential?

**Short answer**

- Office.js hands you proxy objects, not data. `load()` queues a request for named properties and queued writes go into the same batch; nothing crosses to Excel until `await context.sync()` performs one round-trip and populates the proxies.
- The whole performance model is therefore the number of `sync()` calls, not the number of statements: queue every read and write you need, then synchronize once.
- A `sync()` inside a loop is the exact Office.js equivalent of per-cell COM access, and it is the single most common mistake in this API.

**Details and nuances**

```javascript
await Excel.run(async (context) => {
    const sheet = context.workbook.worksheets.getActiveWorksheet();
    const range = sheet.getRange("A1:D10000");
    range.load("values");              // queued, nothing has happened yet
    await context.sync();              // one round-trip

    const rows = range.values;         // now populated
    const out = rows.map(r => [r[0] * 2]);

    sheet.getRange("F1:F10000").values = out;  // queued
    await context.sync();              // one more round-trip
});
```

Two syncs for ten thousand rows. The naive version calls `getCell(i, j)` and `sync()` per cell and is slower by orders of magnitude, for the same reason per-cell `Value2` access is slow in COM: the cost is the boundary crossing, not the arithmetic.

Load only the properties you need - `load("values")`, not `load()` - because loading everything on a large range transfers formatting and formula metadata you are about to discard. Objects that must survive across syncs need `context.trackedObjects.add`, and releasing them with `untrack` matters in long-running task panes.

**Example or evidence boundary**

Prepared knowledge, verified in a Script Lab exercise during preparation: I wrote the batched and the per-cell versions of the same read and compared them. That is a study artifact, not production experience.

[↑ Back to question index](#question-index)

---

## Question COM-039

[↑ Back to question index](#question-index)

### Question COM-039 — How does Office.js differ from COM Automation for the same task?

**Short answer**

- COM is synchronous, in-process and Windows-desktop-only, with apartment and bitness rules and the full object model; Office.js is asynchronous, sandboxed and cross-platform, with a narrower API and a per-`sync()` round-trip cost.
- The optimization discipline is identical under both: minimize boundary crossings. In COM you batch to reduce interop calls; in Office.js you batch to reduce `sync()` calls.
- For raw throughput the order is XLL, then COM Add-In, then Office.js; for reach and deployment simplicity the order reverses.

**Details and nuances**

The differences that change a design rather than a line of code:

| | COM Automation | Office.js |
|---|---|---|
| Call model | Synchronous, immediate | Queued, flushed by `sync()` |
| Threading | STA/apartment rules, marshaling | Single JavaScript runtime, promises |
| Failure isolation | A crash takes Excel with it | Sandboxed; the add-in fails alone |
| Long work | Worker thread, marshal back to the STA | Web worker or a backend service |
| Install | Registry, bitness, elevation | Manifest, no bitness |

**Example or evidence boundary**

Prepared knowledge.

[↑ Back to question index](#question-index)

---

## Question COM-040

[↑ Back to question index](#question-index)

### Question COM-040 — What are Office.js custom functions, and how does streaming work?

**Short answer**

- Custom functions are worksheet functions written in JavaScript, declared with a `@customfunction` annotation and run in their own JavaScript runtime rather than in the task pane.
- A streaming custom function receives an invocation object and calls `setResult` repeatedly, so one cell keeps receiving new values without any recalculation - the Office.js counterpart of an RTD server.
- Cancellation is explicit: `invocation.onCanceled` fires when the cell no longer needs the value, and a streaming function that ignores it leaks a subscription for the life of the session.

**Details and nuances**

```javascript
/**
 * Streams a live value into the cell.
 * @customfunction
 * @streaming
 */
function liveValue(symbol, invocation) {
    const timer = setInterval(() => invocation.setResult(read(symbol)), 1000);
    invocation.onCanceled = () => clearInterval(timer);
}
```

The design question is the same one RTD answers: the source may change far faster than the cell needs to display, so coalesce on your side and emit at a chosen rate rather than on every change.

Streaming custom functions work on the web and Mac, where RTD does not, which is usually the reason to choose them over RTD when a product is not Windows-only.

**Example or evidence boundary**

Prepared knowledge.

[↑ Back to question index](#question-index)

---

# 7. Staleness, Teardown and Testing

## Question COM-041

[↑ Back to question index](#question-index)

### Question COM-041 — How do you keep a background computation from using stale workbook state?

**Short answer**

- A background job works from a snapshot. By the time it finishes the user may have edited cells, inserted rows, switched workbooks or closed the book, so a completed result is a *claim about a past state* and must be validated before it is applied.
- Attach a monotonically increasing generation counter to the input state, bump it on every change that invalidates the snapshot, carry it with the job, and discard any result whose generation no longer matches.
- Never hold a COM interface pointer across the wait: capture plain data, and re-resolve the target `Range` on the Excel thread when applying, because an insert or delete can move or invalidate it.

**Details and nuances**

"Stale" hides three different failures, and they need three different answers:

| What went stale | How it shows | What handles it |
|---|---|---|
| The input data changed | The result is arithmetically correct for data nobody has any more | Generation counter compared at apply time |
| The target moved | Rows were inserted; the saved `Range` now points at the wrong cells | Re-resolve on the Excel thread, or address by named range rather than by a held pointer |
| The host is gone | Workbook closed, or the add-in is shutting down, while a job is in flight | The completion path must be harmless, not merely unlikely |

The generation check is small and does the most work:

```cpp
// Bumped on Worksheet_Change, on workbook switch, on explicit invalidation.
std::atomic<std::uint64_t> generation_{0};

struct Job {
    std::uint64_t generation;   // captured when the job was created
    Input         data;         // a copy, never a live COM pointer
};

// On the Excel thread, when a result comes back:
void apply(const Result& r) {
    if (r.generation != generation_.load()) return;   // computed for a past world
    writeBack(r);
}
```

Two nuances worth saying out loud. **Cancellation and staleness are not the same thing**: cancellation is proactive, when we already know the work is pointless, and staleness is detected at the end, when we could not have known. Do both - cancel what you can, validate always - because cancellation is an optimization and validation is the correctness guarantee.

And **out-of-order completion is free to handle** once the generation exists: with a worker pool, results arrive in an order nobody controls, so applying only results whose generation is at least the last applied one gives last-writer-wins without any extra machinery.

For shutdown specifically, the completion callback has to remain safe after the thing it would update is gone. The usual shapes are a weak reference to the applier, or a shutdown flag set under the same lock the applier checks - so that "the result arrived after teardown" is an ordinary early return rather than a crash in a destructor.

**Example or evidence boundary**

The Excel specifics are prepared knowledge. The pattern is production experience: on the phone platform, asynchronous SDK registration and call events routinely completed against state the application had already moved past, and the fix was the same - the application owned the current state, the event carried what it was computed from, and anything that no longer matched was dropped rather than applied.

[↑ Back to question index](#question-index)

---

## Question COM-042

[↑ Back to question index](#question-index)

### Question COM-042 — How would you test update bursts, workbook closure and shutdown races?

**Short answer**

- Put an interface at the Excel boundary so almost everything is testable without Office: a fake sink records the batches it was given, and burst behavior, coalescing, ordering and staleness become ordinary unit tests that run in CI.
- Drive time yourself. A scheduler that takes a clock as a dependency lets a test advance time deterministically; a test that calls `sleep` is flaky, and a flaky test is one everybody learns to ignore.
- Keep a small, slow, Office-hosted suite for what only Excel can prove - registration and load, apartment behavior, real recalculation, and close-while-busy - and say plainly that it is small on purpose.

**Details and nuances**

The layering is what makes this answerable at all:

```text
feed  →  coalescing store  →  scheduler (injected clock)  →  ISink  →  real Excel sink
                         everything left of ISink is testable in a plain binary
```

**Bursts become properties, not timings.** Push 20,000 updates across 50 keys, advance the clock by one tick, and assert the fake sink received at most one write per key and each carries the last value pushed. That is deterministic and says something true about the design; measuring wall-clock throughput in a unit test says almost nothing.

**Closure and shutdown are the ones that actually find bugs.** The shape that works: start a job, trigger workbook close or add-in teardown while it is in flight, then assert that no callback touches a destroyed object and no COM pointer is used after its final release. Run it in a loop under a sanitizer, or under Application Verifier on Windows, because a single pass usually passes by luck. The same loop with a generation bump instead of a close covers [COM-041](#question-com-041).

**Be explicit about what cannot be unit tested**, because claiming otherwise is the answer that gets caught: registration and bitness, the real recalculation engine, and genuine reentrancy where Excel calls back into the add-in during a call. Those need a hosted suite. It runs on a schedule rather than per commit, it is a handful of scenarios rather than a matrix, and one of them should be the add-in throwing during load so the disabled-add-in path is seen at least once by someone rather than first by a user.

**Example or evidence boundary**

The Excel-hosted part is prepared knowledge. What is production experience is the shape of the argument: on the phone platform the unit and integration tests ran headless on the target device precisely because hardware-dependent behavior cannot be proven anywhere else, and everything that did not need the device was tested where it was fast to run.

[↑ Back to question index](#question-index)

