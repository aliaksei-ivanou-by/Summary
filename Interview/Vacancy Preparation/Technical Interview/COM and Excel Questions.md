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

**The core idea in one sentence:** a COM interface is a pointer to a vtable with a fixed calling convention, so any language that can call through a function-pointer table can implement or consume it. That is why the same Excel object model is reachable from C++, C#, VBA and JavaScript.

**What COM adds on top of a C++ abstract class**, and the reason it is not just "an interface with `virtual`":

- **Identity by GUID, not by name.** Interfaces are named by IID and classes by CLSID, so there is no dependence on C++ name mangling, header layout or compiler version ([COM-005](#question-com-005)).
- **Versioning by addition.** An interface is immutable once shipped; a new version is a new IID, and `QueryInterface` is how a client discovers which one it got ([COM-003](#question-com-003)). This is what lets an add-in built years ago keep working.
- **Reference counting as the ownership contract**, because there is no shared runtime or garbage collector across language boundaries ([COM-004](#question-com-004)).
- **`HRESULT` instead of exceptions**, because exceptions do not cross an ABI boundary ([COM-009](#question-com-009)).
- **Location transparency.** The same interface works in-process, cross-apartment or cross-process, with COM inserting proxies where needed ([COM-020](#question-com-020)).

**The costs that come with it** are the reference-counting discipline, the registry/activation machinery, apartment rules that constrain threading, and the `VARIANT`/`BSTR` type system for anything Automation-compatible. For an Excel add-in these are not optional background details—they are the failure modes you will actually debug.

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

`IUnknown` is the whole COM contract in three methods. Every COM interface derives from it, so its vtable always starts with these slots:

```cpp
HRESULT QueryInterface(REFIID riid, void** ppv);  // discovery: "do you also support this?"
ULONG   AddRef();                                 // +1 reference
ULONG   Release();                                // -1; the object destroys itself at zero
```

**`QueryInterface` defines COM identity.** The rules are not optional and interviewers do ask for them: the answer must be *reflexive* (QI for the interface you already hold succeeds), *symmetric* (if A can reach B, B can reach A), *transitive* (if A reaches B and B reaches C, A reaches C), and *stable* (an interface that succeeded once must never later fail). On top of that, QI for `IID_IUnknown` must return the **same pointer value** from every interface on the object—that identical `IUnknown*` is how COM decides whether two interface pointers refer to one object. Comparing any other pair of interface pointers proves nothing, because multiple-inheritance and tear-off implementations legitimately return different addresses for different interfaces.

**`AddRef`/`Release` are per-interface-pointer bookkeeping, not per-object.** A successful `QueryInterface` has already called `AddRef` for you, so every successful QI needs a matching `Release` ([COM-004](#question-com-004)). The return value is a debugging aid only; it is not reliable for control flow.

**Aggregation** is where "the controlling `IUnknown`" matters: an aggregated inner object delegates its `IUnknown` calls to the outer object, so the client sees one identity. This is why a class factory takes a `pUnkOuter` parameter and must return `CLASS_E_NOAGGREGATION` if it does not support it ([COM-008](#question-com-008)).

In practice you should not be writing these calls by hand. `CComPtr`/`CComQIPtr` (ATL) or `_com_ptr_t` (`#import`) make acquisition and release RAII, which removes the single most common COM bug—an early return that skips a `Release`.

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

```cpp
IFoo* foo = nullptr;
HRESULT hr = obj->QueryInterface(IID_IFoo, reinterpret_cast<void**>(&foo));
if (SUCCEEDED(hr)) { /* foo is AddRef'd - you must Release it */ }
else if (hr == E_NOINTERFACE) { /* not supported - a normal answer, not an error */ }
```

**`E_NOINTERFACE` is an expected result, not a failure.** Code that treats any non-`S_OK` as a fatal error gets this wrong; QI is how you *ask*, so "no" is part of the protocol. On failure the implementation must also set `*ppv` to null.

**The four rules an implementation must satisfy** ([COM-002](#question-com-002)): reflexive, symmetric, transitive, and *stable over time*—an interface that succeeded once must never later fail, and one that failed must never later succeed. That stability is what allows a client to cache the answer.

**Identity:** QI for `IID_IUnknown` must return the same pointer from every interface of the object, and that is the only valid way to test whether two interface pointers name the same object.

**In C++ use `CComQIPtr`** rather than writing the cast by hand—it does the QI, holds the reference, and releases it, which removes the leak that a missing `Release` on an early return would cause.

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

Also common: **LIBID** for a type library and **APPID** for the security/activation settings of an out-of-process server.

These identifiers allow binary components to refer to interfaces/classes without relying on C++ names.

**Why 128 bits rather than a registered name:** a GUID can be generated offline with no central authority and still be unique, so two vendors can ship interfaces without coordinating. `guidgen`/`uuidgen`, `CoCreateGuid` or Visual Studio's Create GUID tool produce them; the registry form is `{6B29FC40-CA47-1067-B31D-00DD010662DA}`.

**In code they appear in three shapes** and the difference trips people up: `IID_IFoo` is a `const GUID` object, `__uuidof(IFoo)` is the compiler-attached UUID from `__declspec(uuid(...))` in a header or `#import`ed type library, and `CLSID_Foo` likewise for a class. Comparison is `IsEqualGUID` or `==`, never `memcmp` on a string form.

**Registration is where they matter operationally.** A CLSID has a key under `HKCR\CLSID\{...}` pointing at `InprocServer32` (the DLL path and `ThreadingModel`), plus a `ProgID` such as `Excel.Application` for the human-readable route via `CLSIDFromProgID`. A per-user add-in registers under `HKCU\Software\Classes` instead of `HKLM`, and on 64-bit Windows a 32-bit server lands under `Wow6432Node`—which is the usual reason an add-in "is installed" and Excel still cannot find it ([COM-032](#question-com-032)).

**Never reuse a GUID.** Changing an interface's methods or their order while keeping its IID is a silent ABI break: the client calls the old vtable slot and gets the new function.

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

```cpp
CComPtr<Excel::_Application> app;
HRESULT hr = app.CoCreateInstance(__uuidof(Excel::Application), nullptr, CLSCTX_LOCAL_SERVER);
```

**`CLSCTX` chooses where the object runs** and is not a formality: `CLSCTX_INPROC_SERVER` loads a DLL into your process, `CLSCTX_LOCAL_SERVER` launches or connects to an EXE on the same machine, and `CLSCTX_ALL` lets COM pick. Asking for `INPROC` on an EXE-only class fails with `REGDB_E_CLASSNOTREG`, which is the same error you get when the class simply is not registered—so check bitness and registry hive before assuming the code is wrong.

**What it does internally** is `CoGetClassObject` → `IClassFactory::CreateInstance` → `Release` the factory ([COM-008](#question-com-008)). Creating many objects of one class is cheaper through `CoGetClassObject` directly, because the activation lookup happens once.

**`CoInitializeEx` must have been called on the calling thread first**, or you get `CO_E_NOTINITIALIZED`—and the apartment you chose determines whether you receive a direct pointer or a proxy ([COM-018](#question-com-018)).

**It creates a *new* instance.** To attach to an Excel that is already running you want `GetActiveObject`/`GetObject` against the running-object table instead; `CoCreateInstance` on `Excel.Application` starts a second, invisible instance—which is the classic cause of a stray EXCEL.EXE left behind in Task Manager.

**Related errors worth recognising:** `E_NOINTERFACE` (class exists but does not implement the requested IID), `CLASS_E_NOAGGREGATION` (non-null `pUnkOuter` on a class that does not aggregate), `CO_E_SERVER_EXEC_FAILURE` (the EXE could not be launched, often a permissions or DCOM identity problem).

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

**Concretely, the difference is a direct vtable call versus an RPC.** In-process, once you have the pointer and you are in the right apartment, a method call costs about what a C++ virtual call costs. Out-of-process, every call is marshaled, context-switched and, for a local server, routed through LRPC—typically microseconds rather than nanoseconds, which is why chatty interfaces are a design error across that boundary ([COM-020](#question-com-020)).

**Other consequences that follow from the boundary:**

| | In-process (DLL) | Out-of-process (EXE) |
|---|---|---|
| Failure isolation | an access violation kills the host | the client survives and sees `RPC_E_DISCONNECTED` |
| Bitness | **must match the host exactly** | may differ; COM bridges it |
| Security context | the host's | its own identity, configurable via DCOM/APPID |
| Deployment | registry + DLL, no service | may need launch/activation permissions |
| Debugging | attach to the host | attach to the server, or use the surrogate |

**Bitness is the practical one for Office work.** A 32-bit Excel cannot load a 64-bit add-in DLL at all, so an add-in is normally built both ways or the installer picks. A DLL surrogate (`dllhost.exe`, via the `AppID`/`DllSurrogate` registry value) is the escape hatch: it hosts an in-process server out-of-process, which is how you isolate a crash-prone or mismatched component without rewriting it.

**Excel itself is a local server**, which is why an external automation client pays marshaling on every object-model call while a COM Add-In loaded inside Excel does not ([COM-023](#question-com-023))—the single biggest reason an in-process add-in outperforms an external driver on the same work.

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

A class factory is the object that knows how to construct instances of one CLSID. It is a separate object from the thing it creates, which is what allows COM to activate a class without the client ever linking against its implementation.

```cpp
HRESULT CreateInstance(IUnknown* pUnkOuter, REFIID riid, void** ppv);
HRESULT LockServer(BOOL fLock);
```

**`CoCreateInstance` is a convenience wrapper.** What it actually does is `CoGetClassObject` to obtain the factory, then `CreateInstance` on it, then `Release` the factory. Calling `CoGetClassObject` yourself is worth it when you create many instances of the same class—you pay the activation lookup once instead of per object.

**How the factory is found depends on the server type.** An in-process server exports `DllGetClassObject(rclsid, riid, ppv)`, and COM calls it after loading the DLL. A local (out-of-process) server starts, then calls `CoRegisterClassObject` for each CLSID it implements, publishing its live factories in the running-object table of class objects.

**`LockServer` and lifetime.** A DLL server is unloaded when `DllCanUnloadNow` returns `S_OK`, which it may only do when no objects *and* no server locks are outstanding. `LockServer(TRUE)` lets a client hold the server in memory while it holds no objects—useful to avoid repeatedly loading and unloading a heavy server.

**`pUnkOuter`** is the aggregation hook ([COM-002](#question-com-002)). If it is non-null and you do not support aggregation, the correct response is `CLASS_E_NOAGGREGATION`; if you do support it, the only interface you may return at that point is `IID_IUnknown`.

For an Excel COM Add-In you rarely write the factory yourself—ATL's object map generates it—but you do need to know it exists, because registration failures and `DllGetClassObject` returning `CLASS_E_CLASSNOTAVAILABLE` are exactly the symptoms of an add-in that Excel refuses to load ([COM-032](#question-com-032)).

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

**Layout:** a 4-byte length in *bytes* sits immediately before the character data, and the pointer you hold points at the characters, not at the length. So `SysStringLen(b)` is O(1), the string may contain embedded `\0`, and it is *also* null-terminated at the end—which is why passing a `BSTR` to a `const wchar_t*` parameter usually appears to work and then fails on the first string with an embedded null.

```cpp
BSTR b = SysAllocString(L"hello");   // allocated by the COM allocator
SysFreeString(b);                    // must be freed by it too
```

**Never `new`/`delete`, `free` or `LocalFree` a `BSTR`**, and never build one by casting a `wchar_t*`: the length prefix would not exist and `SysStringLen` would read whatever is in front of your buffer.

**The ownership rules at a call boundary** are the ones that leak in practice: a `[in]` `BSTR` belongs to the caller; an `[out]` or `[out, retval]` `BSTR` becomes the **caller's** responsibility to free; and a `BSTR` inside a `VARIANT` is freed by `VariantClear`, not separately. Every Excel property that returns text—`Range::get_Formula`, `Worksheet::get_Name`—hands you one to free.

**So use a wrapper.** `CComBSTR` (ATL) or `_bstr_t` (`#import`) make it RAII, and `_bstr_t` additionally converts to and from `char*`. Hand-written `SysFreeString` calls on every early-return path are exactly the code that leaks after the first exception.

**A null `BSTR` is legal** and means the empty string by convention, so `SysStringLen(nullptr)` returns 0 and `wcslen` on it crashes—another reason not to treat it as a plain pointer.

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

`SAFEARRAY` is self-describing: unlike a C array it carries its own element type, dimension count, and per-dimension lower bound and length, so it can cross a marshaling boundary without a separate length parameter.

```cpp
struct SAFEARRAYBOUND { ULONG cElements; LONG lLbound; };
```

**Three details bite people working with Excel specifically.**

*Lower bounds are not zero.* Excel returns arrays with `lLbound == 1` for both dimensions, so you must read the actual bounds with `SafeArrayGetLBound`/`SafeArrayGetUBound` rather than assuming 0-based indexing.

*Two-dimensional arrays are column-major.* `SafeArrayAccessData` gives you a flat pointer in which the first dimension varies fastest, so a row-major traversal over Excel data walks the buffer with a stride—bad for cache. If you are converting to a row-major native structure, iterate in the array's own order and transpose once.

*The element type may not be what you expect.* Excel gives you `VT_ARRAY | VT_VARIANT`, meaning every element is itself a `VARIANT` that can be `VT_R8`, `VT_BSTR`, `VT_BOOL`, `VT_ERROR` (for `#N/A` and friends) or `VT_EMPTY` for a blank cell. Assuming `VT_R8` everywhere is a crash waiting for the first text cell.

**Access and ownership.** `SafeArrayAccessData`/`SafeArrayUnaccessData` pin the data and must be paired—the lock count blocks destruction, so a missed unaccess leaks the array. Ownership follows the container: an array inside a `VARIANT` is freed by `VariantClear`, and you call `SafeArrayDestroy` only on an array you own outright. Never do both. If the data must outlive the COM call, copy it into native storage (`std::vector`) rather than holding the `SAFEARRAY` ([COM-027](#question-com-027)).

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

`IDispatch` adds four methods on top of `IUnknown`: `GetTypeInfoCount`, `GetTypeInfo`, `GetIDsOfNames` and `Invoke`. A client that has no compile-time knowledge of the interface can still call it—that is how VBA, JScript and every scripting host drive Office.

The two-step pattern is name → DISPID → call:

```cpp
OLECHAR* name = L"Value2";
DISPID dispid;
disp->GetIDsOfNames(IID_NULL, &name, 1, LOCALE_USER_DEFAULT, &dispid);
DISPPARAMS dp = {};
disp->Invoke(dispid, IID_NULL, LOCALE_USER_DEFAULT,
             DISPATCH_PROPERTYGET, &dp, &result, &excep, &argErr);
```

**The `DISPPARAMS` traps are the classic interview follow-up.** Positional arguments in `rgvarg` are in **reverse** order—`rgvarg[0]` is the *last* argument. A property put passes its value as a named argument with `DISPID_PROPERTYPUT`, and you must use `DISPATCH_PROPERTYPUTREF` instead of `DISPATCH_PROPERTYPUT` when assigning an object reference. `wFlags` distinguishes method call, property get and property put, and some Office members legitimately answer to more than one.

**Error reporting is two-layer.** `Invoke` returns an `HRESULT` for dispatch-level problems (`DISP_E_MEMBERNOTFOUND`, `DISP_E_TYPEMISMATCH`, `DISP_E_BADPARAMCOUNT`, with `argErr` naming the offending index), but a failure *inside* the called method comes back as `DISP_E_EXCEPTION` with the real message in `EXCEPINFO`—and those `BSTR`s must be freed ([COM-009](#question-com-009)).

**Cost and the dual-interface alternative.** Every call is a name lookup (cacheable), a `VARIANT` packing step and a runtime type check, so late binding is measurably slower than a vtable call. A *dual* interface derives from `IDispatch` but also exposes the methods as vtable slots, so a C++ client can early-bind while a script host still works ([COM-014](#question-com-014)). Against Excel, early binding through `#import`ed type libraries is the normal choice; late binding earns its keep when you must tolerate several Office versions with different type libraries.

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

**In C++ the two look completely different at the call site:**

```cpp
// early: a vtable call, checked at compile time
CComPtr<Excel::Range> r;
sheet->get_Range(CComVariant(L"A1:C3"), &r);

// late: name lookup then Invoke, checked at run time
DISPID id; OLECHAR* n = L"Range";
disp->GetIDsOfNames(IID_NULL, &n, 1, LOCALE_USER_DEFAULT, &id);
disp->Invoke(id, IID_NULL, LOCALE_USER_DEFAULT, DISPATCH_PROPERTYGET, &params, &out, &ex, &argErr);
```

Early binding comes from a type library—`#import "excel.exe"` or the MIDL-generated headers—which gives you IntelliSense, compile-time errors for a misspelled member, and a direct call. Late binding needs nothing at build time, so it survives a different Office version whose type library you did not compile against.

**The cost is real but often overstated**: the `GetIDsOfNames` lookup can be cached per DISPID, after which the remaining overhead is `VARIANT` packing and a runtime type check. Against a chatty loop over cells, both forms lose to a single bulk `Value2` transfer anyway ([COM-026](#question-com-026))—the binding style is a second-order effect next to the number of boundary crossings.

**Dual interfaces give you both.** An interface derived from `IDispatch` that also exposes its methods as vtable slots lets a C++ client early-bind while VBA and script hosts still work; most of the Office object model is dual, which is why `Excel::_Application` has a vtable at all ([COM-013](#question-com-013)).

**The practical rule for an add-in:** early-bind the members you use constantly, and keep a late-bound path for anything that exists only in newer Excel versions—asking `GetIDsOfNames` and handling `DISP_E_UNKNOWNNAME` is a cleaner version check than reading the application version number.

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

A thread joins an apartment when COM is initialized on that thread, and stays in it until `CoUninitialize`.

**An apartment is a synchronization boundary, not a thread.** Its purpose is to let a component declare "I am not thread-safe, protect me" or "I am, don't bother". The object's registered `ThreadingModel` and the caller's apartment together decide where the object is actually created and whether you get a raw pointer or a proxy.

| | STA | MTA |
|---|---|---|
| Threads per apartment | exactly one | many |
| Instances per process | many | at most one |
| Calls serialized by COM | yes | no |
| Message pump required | yes | no |
| Object must be thread-safe | no | yes |
| `CoInitializeEx` flag | `COINIT_APARTMENTTHREADED` | `COINIT_MULTITHREADED` |

There is also the **neutral apartment (NA)**, entered via `ThreadingModel=Neutral`: calls run on the caller's own thread with no switch, but with no serialization either—useful for thread-safe objects that want to avoid proxy overhead from both sides.

**The rules that follow:**

- An interface pointer is valid only inside the apartment that obtained it; crossing requires marshaling ([COM-019](#question-com-019)).
- A cross-apartment call is a proxy call with real cost and real reentrancy ([COM-020](#question-com-020), [COM-022](#question-com-022)).
- `CoInitializeEx` calls must be balanced with `CoUninitialize` on the same thread, and calling it twice with a *different* model on one thread returns `RPC_E_CHANGED_MODE`—a real error to handle, not to assert away, because some host or library may already have initialised the thread.

**For an Excel add-in this is not theoretical.** Excel's main thread is an STA and the object model is bound to it, so your worker threads live in the MTA and every object-model touch from them is marshaled back ([COM-021](#question-com-021)).

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

Each thread using COM must initialize it appropriately, and balance it with exactly one `CoUninitialize`.

**`CoInitialize(nullptr)` is just `CoInitializeEx(nullptr, COINIT_APARTMENTTHREADED)`.** New code should call `CoInitializeEx` so the apartment choice is explicit rather than inherited from a legacy default.

**Check the return value, and check it properly:**

| Return | Meaning |
|---|---|
| `S_OK` | this call initialised COM on the thread |
| `S_FALSE` | already initialised with the same model; the count was incremented |
| `RPC_E_CHANGED_MODE` | already initialised with a **different** model — your request was refused |

`S_FALSE` *succeeds*, so `SUCCEEDED(hr)` is true and you still owe a `CoUninitialize`. `RPC_E_CHANGED_MODE` does **not** succeed and you must **not** call `CoUninitialize`—doing so decrements someone else's count and tears COM down under them. Getting this wrong is a classic source of "COM stops working late in the session".

**Inside an Excel COM Add-In you normally do not call it at all on the main thread**: Excel has already initialised its STA and your `IDTExtensibility2::OnConnection` runs there ([COM-024](#question-com-024)). You call it on *your own* worker threads, choosing `COINIT_MULTITHREADED`, and uninitialise before the thread exits.

**Related:** `OleInitialize` is `CoInitializeEx` in STA mode plus the OLE subsystem (clipboard, drag-and-drop), which is what a UI thread wants; `CoInitializeSecurity` sets process-wide authentication defaults and can only be called once, before any interface is marshaled.

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

**How the serialization actually works:** an incoming cross-apartment call does not run on the caller's thread. COM posts a private window message to a hidden window it created for the apartment; the STA thread picks it up in its message loop and executes the call there. That is why a missing or blocked message pump stalls calls that appear to have nothing to do with windows ([COM-022](#question-com-022)).

**The consequence people miss is reentrancy.** Serialized does not mean atomic. While an STA thread is blocked inside an *outgoing* call, COM keeps pumping, so an incoming call can execute in the middle of your function—your own state can change under you between two statements. Guarding with a flag, or refusing reentrant work via `IMessageFilter`, is the standard defence.

**`IMessageFilter`** is the STA's flow-control hook: `HandleInComingCall` lets you reject or defer a call that arrives at a bad moment, and `RetryRejectedCall` is what Excel uses to tell a caller "I'm busy, retry"—which surfaces as `RPC_E_CALL_REJECTED` / `VBA_E_IGNORE`. An add-in that automates Excel while the user is editing a cell or a modal dialog is open must expect and retry these rather than treat them as fatal.

**Objects are thread-affine.** An STA object may only be touched on its own thread, and the object itself needs no internal locking against COM calls—but that guarantee vanishes for any of its state that non-COM code also touches from another thread.

**For Excel:** the main thread is an STA, the object model is bound to it, and the practical design is a queue from worker threads onto that thread rather than direct calls ([COM-021](#question-com-021)).

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

A thread joins the MTA with `CoInitializeEx(nullptr, COINIT_MULTITHREADED)`. There is at most **one MTA per process**, and every MTA thread shares it—so interface pointers move freely between MTA threads with no marshaling at all.

**No message pump is required**, which is the structural difference from an STA ([COM-017](#question-com-017)). Incoming calls are dispatched on RPC worker threads drawn from a pool, so two clients can be inside your object simultaneously and an object can be re-entered on a thread that never called `CoInitializeEx` itself. Everything the object touches must therefore be thread-safe: its own state, any cached interface pointers, and any library it calls into.

**Registration decides where an object actually lives.** The `ThreadingModel` value under the CLSID controls it: `Apartment` means the object is created in an STA regardless of the caller, `Free` means the MTA, `Both` means it is created in the caller's apartment, and a missing value means the legacy single-threaded apartment. So calling `CoCreateInstance` from an MTA thread on an `Apartment`-model object does **not** give you a direct pointer—COM spins up or picks an STA, creates the object there, and hands you a proxy.

**This is the trap for Excel work.** The Excel object model is STA-bound and single-threaded ([COM-021](#question-com-021)). Being in the MTA does not remove that constraint; it just means every call you make is marshaled to Excel's main STA, where it serializes behind Excel's own UI work. You gain the ability to do your own computation in parallel—you do not gain parallel access to the workbook. The workable design is: pull the data across once on the correct thread, compute in the MTA, push results back in one marshaled call.

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

**Why raw sharing is wrong even though the pointer looks valid**: the pointer will dereference fine and the call will execute on the *wrong thread*, bypassing the apartment's serialization entirely. There is no diagnostic—you get data races inside an object that was written assuming single-threaded access, appearing later as corruption or a hang. Between two MTA threads it is legal, because they share one apartment; anywhere else it is not.

**The two supported mechanisms:**

```cpp
// one-shot: source apartment
IStream* s = nullptr;
CoMarshalInterThreadInterfaceInStream(IID_IFoo, pFoo, &s);
// ...hand `s` to the other thread...
// target apartment - consumes the stream, gives you a proxy
CoGetInterfaceAndReleaseStream(s, IID_IFoo, reinterpret_cast<void**>(&pFoo));
```

```cpp
// repeated use, many threads: the Global Interface Table
CComPtr<IGlobalInterfaceTable> git;
git.CoCreateInstance(CLSID_StdGlobalInterfaceTable);
DWORD cookie;
git->RegisterInterfaceInGlobal(pFoo, IID_IFoo, &cookie);   // once
git->GetInterfaceFromGlobal(cookie, IID_IFoo, (void**)&p); // per thread, per use
git->RevokeInterfaceFromGlobal(cookie);                    // when done
```

The stream form is single-use: exactly one `CoGetInterfaceAndReleaseStream`, and if the handoff is abandoned you must `CoReleaseMarshalData` or the reference leaks. The GIT is the right tool when a background thread needs the same Excel interface repeatedly ([COM-021](#question-com-021)).

**The proxy is apartment-bound too**, so you cannot then pass the proxy to a third thread—each apartment needs its own. And a `CComPtr` copied into a lambda captured by a `std::thread` is precisely the accident this rule forbids: the pointer travels, the marshaling does not.

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

Marshaling is how COM keeps a method call's semantics intact when caller and callee are not in the same apartment. The client ends up holding a **proxy** that has the same vtable as the real interface; the proxy packages the arguments, the call is transported, and a **stub** on the other side unpacks them and makes the real call on the object's own thread.

**Standard vs custom.** Standard marshaling is generated for you from IDL—either a proxy/stub DLL built by MIDL, or type-library marshaling (`oleautomation`), which is why Automation is restricted to the `VARIANT`-compatible types ([COM-011](#question-com-011)). Custom marshaling means the object implements `IMarshal` and decides its own wire representation; that is how COM implements pass-by-value optimisations for things like `IStream` on shared memory.

**Getting a pointer across a thread boundary correctly.** You may not simply copy an interface pointer to another thread ([COM-019](#question-com-019)). The two supported routes are:

```cpp
// one-shot handoff
CoMarshalInterThreadInterfaceInStream(IID_IFoo, pFoo, &pStream);   // source thread
CoGetInterfaceAndReleaseStream(pStream, IID_IFoo, (void**)&pFoo);  // target thread

// repeated use from many threads
IGlobalInterfaceTable::RegisterInterfaceInGlobal / GetInterfaceFromGlobal
```

The GIT is the right tool when a background thread needs the same Excel interface repeatedly, because the stream form is consumed by a single `CoGetInterfaceAndReleaseStream`.

**What it costs.** Latency per call, because a cross-apartment call is a context switch at best and an RPC at worst—which is exactly why chatty loops over the Excel object model are catastrophic and a single bulk transfer is not ([COM-026](#question-com-026)). It also introduces **reentrancy**: while an STA thread waits inside a marshaled outbound call, it keeps pumping messages, so your own code can be re-entered before the call returns ([COM-022](#question-com-022)). And it adds failure modes a local call does not have—`RPC_E_DISCONNECTED`, `RPC_E_SERVERFAULT`, `CO_E_OBJNOTCONNECTED`—which must be handled rather than asserted away.

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

**The four failures, concretely.**

*Apartment violation*: sharing the interface pointer directly runs object-model code on a thread Excel never expected ([COM-019](#question-com-019)). Marshaling it correctly fixes the legality but not the serialization—calls still queue onto Excel's one thread.

*`RPC_E_CALL_REJECTED` / `VBA_E_IGNORE`*: Excel refuses calls while the user is editing a cell, a modal dialog is open, or a recalculation is running. This is normal and must be handled with a bounded retry (and an `IMessageFilter` if you also receive calls), not treated as a crash.

*Deadlock*: the worker blocks on a marshaled call into Excel's STA while Excel's thread is blocked waiting on something the worker holds. The STA keeps pumping, which makes this worse rather than better—the pump can deliver a call that re-enters your own code mid-update ([COM-022](#question-com-022)).

*Lifetime*: the workbook closes or Excel shuts down while work is in flight, so the marshaled pointer becomes `RPC_E_DISCONNECTED` or `CO_E_OBJNOTCONNECTED`. Shutdown has to cancel outstanding work and wait for it, not just release pointers ([COM-042](#question-com-042)).

**So the design is: one owner thread for all object-model access.** Workers compute on plain data and post results; the Excel-side thread drains the queue, coalesces, and writes one `Range` per flush ([COM-028](#question-com-028)). For a streaming feed, an RTD server is the sanctioned version of exactly this pattern—Excel itself pulls on its own thread at its own tempo ([COM-035](#question-com-035)).

**The one supported exception** is Excel's multithreaded recalculation, which may call thread-safe worksheet functions concurrently—but that applies to XLL/UDF code, not to the object model ([COM-030](#question-com-030)).

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

**The mechanism:** COM creates a hidden window for each STA, and a cross-apartment call is delivered as a message to that window. `DispatchMessage` is what actually invokes your method. No pump, no dispatch—the caller simply waits.

**What "stops pumping" means in practice**, and these are all things ordinary code does:

- a long synchronous computation on the STA thread;
- `WaitForSingleObject` / `join()` / a condition-variable wait on the STA thread;
- a lock held while another thread is inside a marshaled call to this apartment.

All three look like normal blocking and all three freeze incoming COM calls for their duration. If a worker thread is waiting on a call into the STA at the same time, that is a deadlock, and it will not time out.

**The correct wait on an STA thread** is one that keeps pumping:

```cpp
// pumps COM/window messages while waiting - safe on an STA
DWORD r = CoWaitForMultipleHandles(COWAIT_DEFAULT, INFINITE, 1, &hEvent, &index);
// MsgWaitForMultipleObjects + a manual pump is the lower-level equivalent
```

**But pumping is not free**, and this is the honest trade-off to state: pumping means reentrancy—your code can be re-entered before the wait returns ([COM-017](#question-com-017)). Both blocking and pumping are dangerous on an STA, which is why the real answer is to not wait on the STA thread at all: hand the work to a worker and let it post the result back.

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

**It runs in-process**, inside EXCEL.EXE, which is the whole performance argument for it: object-model calls are direct rather than marshaled across a process boundary ([COM-007](#question-com-007)). The flip side is that an access violation in your DLL takes Excel down with it, and the bitness must match the host exactly.

**How Excel finds and loads it**: a registry entry under `HKCU\Software\Microsoft\Office\Excel\Addins\<ProgID>` (or `HKLM` for all users) with `LoadBehavior`, `FriendlyName` and `Description`, plus the usual CLSID registration pointing at your DLL. `LoadBehavior = 3` means load at startup; Excel demotes it to `2` if the add-in throws during connection, which is why a once-working add-in silently stops loading ([COM-032](#question-com-032)).

**What it can do:** hook Excel's lifecycle and events, drive the object model, add ribbon UI via `IRibbonExtensibility`, and register worksheet functions—though for UDFs specifically an XLL through the C API is faster and supports multithreaded recalculation, so real products often ship both ([COM-029](#question-com-029), [COM-030](#question-com-030)).

**The obligations that come with being in-process:** run everything object-model-related on Excel's STA thread ([COM-021](#question-com-021)), never let an exception escape into Excel's call stack, release every interface pointer deterministically, and shut down cleanly on `OnDisconnection`—an add-in that leaves a thread running or a reference held is why EXCEL.EXE lingers in Task Manager after the window closes.

**The alternatives** are VSTO (managed, .NET runtime in-process, easier UI, heavier deployment) and Office.js (JavaScript, sandboxed, cross-platform, no native code and no direct object model) ([COM-029](#question-com-029)).

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

```cpp
void OnConnection(IDispatch* Application, ext_ConnectMode ConnectMode,
                  IDispatch* AddInInst, SAFEARRAY** custom);
void OnStartupComplete(SAFEARRAY** custom);
void OnDisconnection(ext_DisconnectMode RemoveMode, SAFEARRAY** custom);
void OnAddInsUpdate(SAFEARRAY** custom);
void OnBeginShutdown(SAFEARRAY** custom);
```

**`OnConnection` is where you capture the host.** The `Application` parameter is Excel's `IDispatch`—query it for `Excel::_Application` and hold it in a `CComPtr`. `ConnectMode` tells you *why* you were loaded (`ext_cm_AfterStartup` when the user enabled you from the add-ins dialog, `ext_cm_Startup` at launch), which matters because at `ext_cm_Startup` the object model is not fully ready yet.

**That is exactly what `OnStartupComplete` is for**: heavy initialisation, ribbon state, opening workbooks or touching the UI belongs here, not in `OnConnection`. Doing it too early is a common cause of failures that only reproduce on a cold start.

**`OnBeginShutdown` versus `OnDisconnection`.** `OnBeginShutdown` fires when Excel is closing and is your last chance to use the object model; by `OnDisconnection` with `ext_dm_HostShutdown` the host may already be tearing down, so object-model calls can fail. Stop your threads and cancel pending work in `OnBeginShutdown`, and release references in `OnDisconnection` ([COM-042](#question-com-042)).

**Never let an exception escape any of these.** Excel treats a failure during connection as a broken add-in and demotes `LoadBehavior` from 3 to 2, disabling you on the next launch with no visible error ([COM-032](#question-com-032)). Wrap each callback in a catch-all that logs and returns a failed `HRESULT` at most.

**It is not the whole story.** Ribbon UI comes from `IRibbonExtensibility`, custom task panes from `ICustomTaskPaneConsumer`, and real-time data from `IRtdServer` ([COM-035](#question-com-035))—`IDTExtensibility2` is only the lifecycle contract.

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

Around that spine sit the objects that real add-ins touch: `Names` (defined names), `ListObjects` (tables), `Charts`/`ChartObjects`, `Windows`/`Panes` for view state, and the event sources—`Application`, `Workbook` and `Worksheet` each raise their own events, which is where an add-in hooks `SheetChange`, `SheetSelectionChange` and `WorkbookBeforeClose`.

**`Range` is the one that matters for performance**, because it is the only place you can move a block of cells in a single crossing. `Value2` returns a `VARIANT` holding a 2-D `SAFEARRAY` for a multi-cell range and a plain scalar for one cell—the shape difference has to be handled explicitly ([COM-027](#question-com-027)). `Value` additionally applies Currency/Date conversion and `Text` returns what the cell *displays*, including `####` when the column is too narrow ([COM-034](#question-com-034)).

**Two habits that prevent most Excel add-in bugs.**

*Never rely on `ActiveWorkbook`/`ActiveSheet`/`Selection`.* They follow the user's focus, so an add-in that reads them races against whatever the user clicks. Qualify fully: `app->Worksheets->Item["Data"]->Range["A1:C100"]`.

*Release every intermediate object.* Each dot in that chain returns a separate reference-counted interface pointer, and in C++ nothing releases them for you—this is where `CComPtr` stops being a nicety. A leaked `Application` reference is the classic reason an invisible EXCEL.EXE stays in Task Manager after the host closes.

**Caching a `Range` is only safe while the geometry is stable.** Inserting or deleting rows, or the sheet being deleted, can leave a cached `Range` pointing somewhere else or failing outright, so cache the *address* and re-resolve, rather than holding the object across user edits ([COM-041](#question-com-041)).

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

**Break down what one `range.Cells(i,j).Value` actually costs**, and it is not one call: `Cells` is a property get that returns a new `Range` object (an `AddRef`ed interface pointer you must release), then `Value` is a second property get. So a naive loop is two or three cross-boundary operations plus an object allocation *per cell*.

If the client is out-of-process, each of those is a marshaled LRPC—microseconds. Even in-process inside a COM Add-In, each is a late- or early-bound Automation call through Excel's dispatch layer with `VARIANT` packing, plus the reference-counting traffic. A million cells at even 5 µs per operation is minutes; the same data as one `Value2` read is a single crossing and a memory copy.

```cpp
// bad: ~2N crossings and N temporary Range objects
for (long i = 1; i <= n; ++i) { CComVariant v; sheet->get_Cells(i,1,&cell); cell->get_Value2(&v); }

// good: one crossing, one SAFEARRAY
CComVariant block; range->get_Value2(&block);
```

**Excel-side costs compound it.** Each write can trigger recalculation, redraw and `Worksheet_Change` event handlers, so a per-cell write loop re-runs the dependency graph N times. Turning off `ScreenUpdating`, `EnableEvents` and setting `Calculation` to manual around a bulk operation removes that—and they must be restored in an RAII guard, because leaving Excel in manual calculation is a user-visible bug ([COM-033](#question-com-033)).

**So the rule is: minimise crossings, not instructions.** Read once into native memory, compute in C++, write once ([COM-027](#question-com-027), [COM-028](#question-com-028)). Micro-optimising the loop body is irrelevant next to the boundary count.

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

instead of one COM call per cell. Each per-cell access is a marshaled cross-apartment call ([COM-026](#question-com-026)); one bulk read is a single crossing regardless of size, so the difference on a 100 000-cell block is typically three orders of magnitude.

```cpp
CComVariant v;
range->get_Value2(&v);                         // one crossing

if (v.vt != (VT_ARRAY | VT_VARIANT)) { /* single cell: v is the scalar */ }

SAFEARRAY* sa = v.parray;
LONG r1, r2, c1, c2;
SafeArrayGetLBound(sa, 1, &r1); SafeArrayGetUBound(sa, 1, &r2);  // 1-based!
SafeArrayGetLBound(sa, 2, &c1); SafeArrayGetUBound(sa, 2, &c2);

VARIANT* data = nullptr;
SafeArrayAccessData(sa, reinterpret_cast<void**>(&data));
// column-major: element (row i, col j) is data[(j - c1) * (r2 - r1 + 1) + (i - r1)]
SafeArrayUnaccessData(sa);                     // CComVariant's dtor frees the array
```

**The checks that are not optional.** A one-cell range returns a scalar, not a 1×1 array. Bounds are 1-based. Every element is a `VARIANT` whose `vt` may be `VT_R8`, `VT_BSTR`, `VT_BOOL`, `VT_EMPTY` for a blank cell or `VT_ERROR` carrying `#N/A`/`#DIV/0!`—so decide up front whether an error cell is a skip, a NaN or a hard failure ([COM-012](#question-com-012)).

**Convert, then let go.** Copy into contiguous native storage (`std::vector<double>` plus a separate string table) and release the `VARIANT` before doing the real work, so you are not holding COM resources—or blocking Excel's thread—during computation.

**Chunk only when you must.** A `VARIANT` array of a full column is roughly 16 bytes per element before the string payloads, so a million rows is real memory; chunking by row bands also keeps the UI responsive and lets you report progress. Measure the chunk size—too small and you are back to paying per-crossing overhead. Also bound the range first: `UsedRange` or `SpecialCells(xlCellTypeLastCell)` rather than reading to row 1 048 576.

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

**One assignment writes a whole block.** Build a 2-D `SAFEARRAY` matching the target range exactly—same number of rows and columns—and assign it to `Value2` once:

```cpp
SAFEARRAYBOUND b[2] = { {rows, 1}, {cols, 1} };        // 1-based, like Excel
SAFEARRAY* sa = SafeArrayCreate(VT_VARIANT, 2, b);
// fill in column-major order, then:
CComVariant v; v.vt = VT_ARRAY | VT_VARIANT; v.parray = sa;
range->put_Value2(v);                                  // one crossing
```

A size mismatch does not error—Excel tiles or truncates—so resolve the target with `Resize(rows, cols)` from the top-left cell rather than trusting an address string.

**Wrap the write in a settings guard** ([COM-033](#question-com-033)):

```cpp
struct FastMode {                     // RAII: restore even on exception
    FastMode(Excel::_Application* a) : app(a) {
        app->get_Calculation(&calc); app->get_ScreenUpdating(&screen); app->get_EnableEvents(&events);
        app->put_Calculation(Excel::xlCalculationManual);
        app->put_ScreenUpdating(VARIANT_FALSE);
        app->put_EnableEvents(VARIANT_FALSE);
    }
    ~FastMode() { app->put_EnableEvents(events); app->put_ScreenUpdating(screen); app->put_Calculation(calc); }
};
```

Restoring is not optional: leaving calculation on manual silently breaks the user's workbook long after your code has finished.

**Coalescing is the part that matters for a live feed.** Keep a dirty map keyed by cell, overwrite in place as ticks arrive, and flush on a timer—say every 100–250 ms. Ten updates to one cell inside a window become one write, so the cost tracks the *screen refresh rate*, not the tick rate. That is also exactly what Excel's own RTD mechanism does with `ThrottleInterval`, which is the sanctioned route for streaming data and lets Excel pull on its own thread instead of you pushing onto its STA ([COM-035](#question-com-035)).

**Write only what is visible or referenced** where you can, and keep every write on the Excel thread ([COM-021](#question-com-021)).

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

