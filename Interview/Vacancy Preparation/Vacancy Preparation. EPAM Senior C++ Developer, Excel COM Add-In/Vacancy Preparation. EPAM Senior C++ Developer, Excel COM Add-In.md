# EPAM Senior C++ Developer - C++, JavaScript and Excel COM Add-In

> Purpose: preparation for EPAM's Senior C++ Developer opening involving C++, JavaScript/Node.js and an Excel COM Add-In. It contains ready-to-say introductions, the exact role signals, an evidence map, technical preparation, interview stories and questions for the team. Career claims must remain consistent with the project documents in `Interview/`.

> Technical interview questions and prepared answers: [Technical Interview Q&A](<./Vacancy Preparation. EPAM Senior C++ Developer, Excel COM Add-In - Technical Interview.md>).

> Reusable topic banks: [C++ Core](<../Technical Interview/C++ Core Questions.md>) · [JavaScript and Node.js](<../Technical Interview/JavaScript and Node.js Questions.md>) · [COM and Excel](<../Technical Interview/COM and Excel Questions.md>).

## 01 Role Snapshot and Positioning

### Vacancy Record

| Field | Details |
|---|---|
| Company | EPAM Systems |
| Position | Senior C++ Developer |
| Location / mode | Remote within Poland; the vacancy text also describes a hybrid-by-design model |
| Product focus | High-performance Microsoft Office COM Add-In for Excel |
| Official vacancy | [EPAM Careers](https://careers.epam.com/en/vacancy/senior-c-developer-blt5iuooygrtf5anb25_en) |
| Original LinkedIn posting | [Job 4461911643](https://www.linkedin.com/jobs/view/4461911643) |

The vacancy is not a generic C++ role. Its center is existing Excel integration: Windows COM Add-In development, JavaScript for user interaction, Office APIs, 50k+ formulas or real-time streams, profiling, performance tuning and high-frequency updates. Direct Excel/COM experience is therefore the largest selection risk, not a minor nice-to-have.

Two details need clarification during the interview. The description says JavaScript "particularly for client-side scripting" while the responsibility names Node.js, so the actual JavaScript runtime and ownership are unclear. It also groups five years of C++ with COM Add-In maintenance in one requirement; ask whether five years of COM itself is required or whether five years of C++ plus strong adjacent integration experience is acceptable.

| Area | Position for this vacancy |
|---|---|
| Strongest match | Production C/C++, data processing, inherited-code maintenance, asynchronous state, cross-component debugging and user-facing behavior |
| Supporting match | Occasional JavaScript at EPAM, an early Node.js conferencing-server contribution, Windows desktop tooling in C#/.NET, tests and distributed delivery |
| Main gap | No commercial Excel COM Add-In, Office object-model, VBA or Office.js experience |
| Interview strategy | Lead with C++ and end-to-end debugging; show that the Office gap is understood precisely and has a concrete learning plan |
| Decision to clarify | Whether direct COM Add-In experience is a day-one requirement or the team is open to adjacent native Windows experience |

### Positioning Sentence

I am a C++ engineer who has worked on data-processing and asynchronous products across native, JavaScript, persistence and UI boundaries; Excel COM is the new domain, not a skill I will pretend to have already used in production.

### Do Not Position As

- An experienced Excel, COM, VBA or Office.js developer.
- A JavaScript specialist: JavaScript was a secondary contribution area.
- A finance-domain engineer.
- An expert in CPU or memory profiling. The prepared example and the sentence that states the boundary are in section 05; use them instead of improvising.
- The owner of the later mediasoup server implementation.

## 02 Spoken Answer Kit

### 30-Second Introduction

Hi, my name is Aliaksei Ivanou. I am a C++ Software Engineer with more than six years in C and C++, five in commercial employment. My closest match is an EPAM sensor-data product: C++17 and MySQL with JavaScript-based user-facing functionality. Recent embedded Qt work adds asynchronous state handling, testing and cross-component debugging. I have not built an Excel COM Add-In; the transferable foundation is native C++, data processing, Windows tooling and reliable integration across language and runtime boundaries.

### 90-Second Introduction

Hi, my name is Aliaksei Ivanou. I am a C++ Software Engineer with more than six years of C and C++, five of them in commercial employment, plus an earlier R&D background in numerical and image processing.

At EPAM I worked on a system that parsed and analyzed industrial sensor data. The backend was C++17, results were stored in MySQL, and JavaScript provided user-facing functionality inside the product. My main work was C++ feature development, defect correction and safe simplification of inherited code. I also changed JavaScript when a complete feature or fix crossed the native and user-facing boundary.

More recently, I developed a C++17/Qt application for an embedded SIP phone and personally implemented the Linphone SDK registration watchdog and primary/backup failover/failback logic behind it. That involved asynchronous SDK events, application and UI state, background work, tests and debugging on physical devices. I also contributed to early JavaScript/Node.js conferencing-server code and integrated the C++ client with the conferencing path. On the same product I substantially extended a Windows provisioning tool in C#/.NET, including explicit project save/load and installer packaging.

The direct gap is Excel COM Add-In development: I have not done it commercially. I would bring production C++, data-flow debugging, asynchronous application design and tested Windows delivery, while learning the Excel object model and COM-specific lifecycle rather than overstating adjacent experience.

### Main Self-Introduction for the Technical Interview

Use this version for "Tell me about yourself" when the interviewer leaves the length open. It is written to be spoken in about three minutes.

Hi, thank you for meeting with me. My name is Aliaksei Ivanou. I am a C++ Software Engineer with more than six years of C and C++ experience, five in commercial employment, plus an earlier R&D background. My strongest areas are native applications, data processing, cross-component debugging and reliable asynchronous state.

I worked at EPAM from September 2021 to August 2024. The assignment closest to this position was an industrial sensor-data system. C++17 parsed and analyzed incoming measurements, MySQL stored the results, and JavaScript provided user-facing functionality. I mainly developed C++ features, corrected defects and simplified inherited code. I also changed JavaScript when a task crossed the native and user-facing boundary. Debugging meant reproducing the problem and tracing the affected value through processing, persistence and presentation before deciding where the correction belonged.

My most recent project was an embedded SIP desk-phone platform built with C++17, Qt and embedded Linux. I worked on application architecture, telephony behavior, account and call state, provisioning and physical-device debugging. One representative task was implementing the customized Linphone SDK registration watchdog and primary/backup failover/failback behavior, then integrating it into the Qt application. The challenge was keeping SDK state, authentication data, persisted configuration and the UI consistent while asynchronous registration events arrived. I used Qt threads, background work, queued signals, focused Qt Test coverage and repeatable device scenarios.

My JavaScript and Node.js experience is a secondary area. At EPAM I made occasional JavaScript changes for complete C++ and user-facing workflows. On the phone project I contributed to the early Node.js conferencing-server implementation and integrated the C++ client with the WebRTC path. C++ remained my primary responsibility.

On the same platform I substantially extended a Windows provisioning application in C# and .NET. I implemented device and configuration workflows, explicit project save/load, diagnostics and installer packaging. That is relevant Windows desktop experience, although it was not an Office-hosted application.

For this vacancy, my strongest match is production C++, asynchronous state, data processing, Windows delivery and cross-language debugging. The clear gap is direct Excel COM Add-In development: I have not done it in production. I am preparing specifically around COM lifetime and errors, STA threading and marshaling, Excel's object and calculation models, bulk range transfer, registration and x86/x64 deployment. The role interests me because it combines engineering problems I know well with a concrete new domain, and because I would return to EPAM with broader product and architecture experience than when I left. I would be happy to go deeper into either the sensor-data flow or the asynchronous phone example.

### Clarifying the Experience Figures Already Sent

The recruiter received `C++ 6 years`, `Async 2 years`, `JavaScript 4 years`, `Performance 4 years` and `TypeScript - no experience`. Do not volunteer these secondary-area year counts in the introduction. If asked, explain what the estimates represented instead of implying continuous full-time specialization.

| Figure sent | Accurate spoken clarification |
|---|---|
| C++ - 6 years | "I have more than six years of C and C++ practice, including five years in commercial employment. The remaining period was full-time independent C++ work and EPAM's mentoring programme before I joined EPAM." |
| Async - 2 years | "That was an approximate span rather than two years spent exclusively on concurrency. My strongest concentrated experience is the recent embedded project: Qt threads and background work, queued events, SIP/WebRTC callbacks, async Windows operations and shutdown/state coordination." |
| JavaScript - 4 years | "That described project exposure across the period, not four years as a primary JavaScript developer. My direct work was occasional JavaScript changes on the EPAM sensor-data product and an early Node.js conferencing-server contribution; C++ remained my main responsibility." |
| Performance - 4 years | "That described work in performance- and resource-sensitive systems, not four years as a dedicated profiling specialist. My evidence is numerical image and point-cloud processing, sensor-data workflows and an embedded product. I would discuss a measurement-first approach, but I do not claim Excel performance experience." |
| TypeScript - none | "Correct. I have JavaScript and Node.js exposure, but no professional TypeScript experience." |

### If the Interviewer Stops You Early

Finish the current sentence and use this close: "The closest match is my C++ sensor-data work and recent asynchronous Qt product work. Direct Excel COM development is new to me, and I am happy to explain either the transferable experience or how I am preparing for that gap."

### Direct Answer About the Excel COM Gap

I have not developed an Excel COM Add-In. I understand that this is more specific than general C++ or Windows experience: I need the add-in lifecycle, the Excel object model, COM ownership and error conventions, apartment threading, registration and Office deployment behavior. My related experience is native C++, asynchronous state, data-processing workflows and Windows tooling. I can explain those examples in depth, but I would present Excel COM as an active learning area, not previous production experience.

### Why Return to EPAM

I spent three years at EPAM working on production C and C++ systems in international teams. Since then, I have added embedded product work, application architecture, asynchronous runtime behavior and Windows tooling. This role is interesting because it combines the parts I already know well - C++, data processing, reliability and cross-language integration - with a concrete new domain in Excel integration.

## 03 Requirement-to-Evidence Map

Use this table to choose evidence, not as a script to recite. `Adjacent` means the engineering skill transfers, but the vacancy's named environment is new.

| Vacancy requirement | Fit | Evidence to discuss | Boundary of the claim | Best source/story |
|---|---|---|---|---|
| 5+ years of C++, particularly maintaining COM Add-Ins | Adjacent | C++17 sensor-data product; C++17/Qt phone application; Linphone SDK watchdog/failover implementation; C++17/Qt/PCL prototype | More than six years of C/C++ practice and five in commercial employment, but none in a COM Add-In | Oil and gas cross-layer change; SIP server recovery |
| JavaScript for client-side Excel interaction; Node.js in the responsibility | Adjacent | Occasional JavaScript at EPAM; contribution to early Node.js conferencing-server code; C++ client integration | No JavaScript inside Excel, no Office.js and no ownership of the later server rewrite | Sensor-data workflow; early conferencing path |
| Microsoft Office APIs and Excel COM Add-In architecture | Gap | Transferable C++, Windows, concurrency and integration background; current technical study | No direct production evidence | Direct gap answer plus learning exercise |
| Large datasets, 50k+ formulas and/or real-time processing | Adjacent | Industrial sensor processing, image algorithms and point-cloud reconstruction | No supported claim for formula-heavy workbooks, high-frequency financial streams, update rate or dataset size | Sensor data; PELENG; RIFTEK |
| Performance, scalability and responsiveness under substantial workloads | Adjacent | Resource-constrained image processing, point-cloud pipeline work, embedded UI/runtime behavior and measurement-first debugging approach | No prepared production metric proving a specific speedup or Excel bottleneck analysis | PELENG/RIFTEK decision; performance investigation answer |
| Profiling, debugging and performance tuning | Partial | Logs, gdb/gdbserver, core dumps, Valgrind in the project toolchain, Qt Test, MSTest and physical-device scenarios; onboard compression designed against a fixed computation budget | Strong debugging and measurement-first optimization against a stated budget; no production case of running a sampling profiler against a live application - say so | Onboard compression budget; library crash; phone regression scenario |
| Asynchronous programming and multithreading for real-time updates | Strong transferable | Qt threads, Qt Concurrent/background work, queued signals, SIP/WebRTC callbacks and async Windows operations | Demonstrates concurrency and state consistency, not Excel-specific threading or market-data rates | SIP registration or conference-state sequence |
| Testing and quality assurance | Strong transferable | Qt Test, MSTest, developer scenarios, separate QA teams and validation on physical devices | Full on-device regression was not automated | Phone regression scenario |
| Stakeholder communication and distributed delivery | Strong | QA/customer reports, international EPAM teams, code review, task planning, mentoring and technical-lead responsibilities | Frequent external-client presentation or business ownership is not established | Incomplete report to reproducible scenario |
| Git and CI/CD | Partial to strong | Daily Git/GitLab use, Jenkins/GitLab CI in delivery projects, builds and packaging | Used and troubleshot delivery infrastructure; did not own the complete CI/CD platform design | Build/package issue |
| English B2+ | Match | B2 graded at Streamline; working language throughout EPAM and Innowise | Keep the stated level at B2 and demonstrate it in the interview | Interview itself |
| Finance, VBA, real-time trading integration and Office.js | Gap / nice-to-have | Numerical and industrial data-processing background only | No demonstrated professional experience in these named areas | State plainly |

## 04 Three Stories to Prepare

Each story should take two minutes and follow context, personal responsibility, action, result and lesson. Add exact numbers only when a project document supports them.

### Story 1 - C++ and JavaScript Across One Data Flow

- **Context:** an established EPAM product processed industrial sensor measurements in C++17, stored results in MySQL and exposed them through JavaScript-based workflows.
- **Responsibility:** implement a feature or correct a defect that crossed the native and user-facing layers.
- **Action to explain:** reproduce with representative input, trace one value through parsing, calculation, persistence and JavaScript presentation, then change the layer that owned the incorrect behavior.
- **Result to prepare:** the exact behavior restored and how developer and QA validation covered the whole path.
- **Boundary:** JavaScript was occasional work; do not invent a framework, dataset size or performance number.

### Story 2 - Asynchronous State and Recovery

- **Context:** SIP registration state depended on a customized SDK, primary and backup servers, authentication context, Qt models, provisioning and the real network.
- **Responsibility:** implement the customized Linphone SDK registration watchdog and primary/backup failover/failback state machine, integrate it into the application and provisioning paths, and add the backup-domain authentication change.
- **Action to explain:** why the state machine belonged in the SDK, what event sequence exposed the problem, how the Qt application consumed SDK-owned state, and how stale UI or configuration was avoided.
- **Result to prepare:** the implemented SDK behavior, merged application integration and repeatable physical-device recovery scenario.
- **Boundary:** the project-specific SDK state machine and its integration were my work; unrelated upstream Linphone code and the rest of the project fork were outside my ownership.

### Story 3 - Durable Windows Application State

- **Context:** administrators needed to save a provisioning project, reopen it, adjust it and generate phone configuration again.
- **Responsibility:** make project save/load explicit and consistent in the C#/.NET WinForms tool.
- **Action to explain:** gather UI state into a structured model, serialize deliberately, restore defaults and fields predictably, and keep format evolution reviewable.
- **Result to prepare:** a saved setup could be reopened and reused rather than reconstructed manually.
- **Boundary:** this is Windows desktop delivery, not Office integration.

### Backup Story - Cross-Component Production Debugging

Use the integrated library platform when the interviewer wants legacy C, Linux, gdb/core dumps or a failure whose visible component was not the component at fault.

## 05 Technical Preparation

This section sets the role-specific priorities. Reusable answers live in the [C++](<../Technical Interview/C++ Core Questions.md>), [JavaScript/Node.js](<../Technical Interview/JavaScript and Node.js Questions.md>) and [COM/Excel](<../Technical Interview/COM and Excel Questions.md>) banks; cross-stack and architecture answers live in the [vacancy-specific technical companion](<./Vacancy Preparation. EPAM Senior C++ Developer, Excel COM Add-In - Technical Interview.md>). Prepared knowledge must not be described as past production experience.

| Topic | Current position | Preparation priority |
|---|---|---|
| Modern C++ and native debugging | Strongest evidence; refresh interview fundamentals | Medium |
| Concurrency and asynchronous state | Strong Qt/product evidence; learn COM-specific constraints | High |
| JavaScript and Node.js | Adjacent hands-on evidence; refresh language and runtime fundamentals | High |
| COM | No production evidence | Highest |
| Excel object model and calculation | No production evidence | Highest |
| Performance analysis | Transferable debugging/data experience; avoid claiming unsupported profiling depth | High |

### The Performance and Profiling Answer

The vacancy names profiling, debugging and performance tuning as a requirement, so this needs one chosen example and one honest boundary, decided before the interview rather than improvised in it.

**The example to use: onboard image compression at PELENG.** The constraint was not "make it faster", it was a fixed computation budget on hardware that could not be updated after launch, with image quality as the thing being traded. I implemented lossy compression reaching a 3-4x ratio inside that budget, then improved delivered image quality at the same ratio and the same budget by predicting image type and adapting quantization to it. The measurement was against the budget and against a resolution criterion that I later automated, because "how well can you resolve detail in this image" was an expert judgement until it was turned into a defined, repeatable measurement.

Why this one rather than a debugging story: it is the only example where the constraint was numeric and stated in advance, the tradeoff was explicit, and the improvement is expressed as "same cost, better result" rather than as an unverifiable speedup.

**The boundary to state, in one sentence.** My performance work has been measurement-first optimization against a stated budget, plus native debugging with gdb, core dumps and Valgrind in the project toolchain. I have not run a sampling profiler against a live production application, and I would not describe myself as a profiling specialist.

**Then turn it into method, which is what the question is really testing.** For an Excel add-in I would not start by optimizing anything. I would separate the latency into its parts - data arrival, native processing, the COM transfer itself, workbook recalculation and rendering - measure each, and only then decide where the work belongs. That order matters here specifically, because the intuitive answer ("the calculation is slow") and the usual real answer ("we are making fifty thousand boundary crossings") point at different code.

### First Clarify the Product Architecture

"Excel add-in" can mean several materially different technologies. Ask which one the product actually uses.

| Technology | What to know for the conversation |
|---|---|
| Native COM Add-In | Windows-only component loaded by Office, commonly using the Office extensibility lifecycle and Excel's COM object model; deployment, registration, bitness and host stability matter |
| VSTO Add-In | Managed .NET Framework solution layered over Office COM integration; related to COM but not the same as a native C++ add-in |
| Office Add-in / Office.js | Web application plus a manifest, running in a browser/webview sandbox and using asynchronous JavaScript APIs; designed for multiple Office platforms |
| XLL | Native C/C++ Excel extension mainly used for high-performance worksheet functions through the Excel C API; it has a different lifecycle and call constraints from a COM Add-In |

Prepared answers for all four, and for the mechanisms each one offers for live data, are in the COM/Excel bank: [COM-029](<../Technical Interview/COM and Excel Questions.md#question-com-029>) compares the technologies, [COM-030](<../Technical Interview/COM and Excel Questions.md#question-com-030>) to [COM-032](<../Technical Interview/COM and Excel Questions.md#question-com-032>) cover XLL, VSTO and registration/`LoadBehavior`, [COM-033](<../Technical Interview/COM and Excel Questions.md#question-com-033>) to [COM-036](<../Technical Interview/COM and Excel Questions.md#question-com-036>) cover the calculation/events/screen-updating controls, `Value2`, RTD and asynchronous UDFs, and [COM-037](<../Technical Interview/COM and Excel Questions.md#question-com-037>) to [COM-040](<../Technical Interview/COM and Excel Questions.md#question-com-040>) cover Office.js, `context.sync()` and streaming custom functions.

Do not assume the JavaScript part is Office.js. It could be an embedded web UI, a browser task pane, a Node.js service, build tooling or a separate application component.

### C++ and COM Checklist

- Rule of Zero/Five, value categories, move semantics, copy elision and when `noexcept` affects container behavior.
- Ownership with values, references, `unique_ptr`, `shared_ptr` and `weak_ptr`; avoid reference cycles and make lifetime visible in APIs.
- Polymorphism, virtual destructors, object slicing and the cost of crossing DLL or ABI boundaries.
- Container complexity and iterator/reference invalidation; use non-owning views such as `std::span` or `std::string_view` only while their source remains alive.
- Data races versus logical races and deadlocks; mutex/condition-variable discipline, atomics, cancellation and thread-safe shutdown.
- Exception guarantees, error propagation and RAII cleanup during partial construction or early return.
- `IUnknown`, interface identity, `QueryInterface`, reference counting, `AddRef` and `Release`.
- `HRESULT` and `SUCCEEDED`/`FAILED`; never allow C++ exceptions to cross a COM ABI boundary.
- COM data types and ownership: `BSTR`, `VARIANT`, `SAFEARRAY`, type libraries, `IDispatch` and early versus late binding.
- RAII wrappers for interface pointers, strings, arrays and registration handles; clear ownership at every boundary.
- `CoInitializeEx`, STA versus MTA, message pumping, marshaling, callbacks and reentrancy.
- Excel object-model calls should respect the owning apartment. Background workers should operate on copied native data and hand completion back through a controlled boundary rather than using an Excel proxy arbitrarily.
- In-process DLL lifetime, class factories, CLSID/ProgID, registry discovery, load/unload behavior and 32/64-bit compatibility.
- Host resilience: fast startup, bounded shutdown, logging, cleanup after partial initialization and no unhandled failure that can take down Excel.

### Excel Checklist

- Object hierarchy and lifetime: `Application`, `Workbook`, `Worksheet`, `Range`, events and workbook close/shutdown behavior.
- Minimize cross-process or automation-boundary calls. Read and write rectangular ranges in bulk rather than one cell at a time.
- Understand automatic, manual, full and partial calculation; smart recalculation; dependency chains; volatile functions; and the difference between workbook, worksheet and range calculation.
- Treat calculation mode, event enablement and screen updating as host-wide state. If code changes them, restore the original values on every exit path.
- Measure a representative workbook before optimizing. Separate native processing time, COM transfer time, recalculation time and UI/update time.
- Plan for user edits, workbook closure, cancellation, stale asynchronous results, repeated events and reentrant callbacks.
- Know the deployment questions: supported Excel versions, x86/x64, per-user or per-machine registration, signing, installer/update path and behavior when Office disables a failing add-in.

### JavaScript and Node.js Checklist

- Primitive versus object behavior, equality/coercion, lexical scope and closures.
- `this`, prototypes/classes, modules and the lifetime of captured state in callbacks.
- Event loop, call stack, task and microtask ordering, promises and `async`/`await` error propagation.
- Promise composition, partial failure and the difference between sequential and concurrent asynchronous work.
- Cancellation and timeouts, cleanup of listeners/resources, duplicate events and stale responses.
- Backpressure, batching and coalescing when updates arrive faster than Excel or the UI can consume them.
- Node.js CPU work versus I/O work; when a worker thread or native C++ component is justified.
- Contract between JavaScript and C++: schema, units, timestamps, null/error representation, versioning and ownership of validation.
- Browser JavaScript versus Node.js versus Office.js. Prepare for the one the team confirms instead of revising all three equally.

### System-Design Prompt to Rehearse

Design a path that receives changing external data, processes it in C++, exposes status through JavaScript and updates an Excel workbook without freezing the user interface.

Cover these points in order:

1. Define the data contract, update semantics and ownership of calculation.
2. Copy incoming data into native structures and keep Excel/COM objects on the correct apartment boundary.
3. Batch or coalesce updates and apply backpressure instead of issuing a cell call for every value.
4. Separate background computation from workbook updates; make cancellation and workbook closure explicit.
5. Reject stale results using version or generation identifiers.
6. Measure queue delay, C++ processing, boundary transfer, Excel recalculation and visible latency separately.
7. Test correctness under rapid updates, errors, cancellation, workbook close/reopen and add-in shutdown.

### Likely Technical Questions and Where the Answer Is

Every question below has a prepared answer. The point of the table is that nothing on this list has to be improvised, and that the review before the interview is a list of IDs rather than a re-read of everything.

`C` = [C++ Core](<../Technical Interview/C++ Core Questions.md>), `COM` = [COM and Excel](<../Technical Interview/COM and Excel Questions.md>), `JS` = [JavaScript and Node.js](<../Technical Interview/JavaScript and Node.js Questions.md>), `ROLE` = [the technical companion](<./Vacancy Preparation. EPAM Senior C++ Developer, Excel COM Add-In - Technical Interview.md>).

| Likely question | Prepared answer |
|---|---|
| What happens when `AddRef`/`Release` ownership is wrong, and how would RAII prevent it? | COM-004, with the `ComPtr` sketch; CPP-029 to CPP-038 for ownership generally |
| Why can an STA deadlock when its thread blocks without pumping messages? | COM-022, COM-021, COM-019; ROLE-009 for the worker/UI variant |
| How would an exception inside native code be translated to a stable COM error? | COM-009, including the `catch` at the ABI boundary; ROLE-003 for the C++/JS direction |
| How would you move a large two-dimensional dataset between C++ and Excel efficiently? | COM-027, COM-028, COM-034 |
| Why is per-cell automation slow, and how would you prove where the time goes? | COM-026 for why; "The Performance and Profiling Answer" above for how to prove it, naming the five latency components |
| How would you keep background computation from using stale workbook state? | COM-041 - generation counter, the three kinds of staleness, out-of-order completion |
| What is the difference between a COM Add-In, an XLL and an Office.js add-in? | COM-029 for the comparison; COM-030, COM-031, COM-037 for each one |
| How would you investigate an add-in that loads on one machine but not another? | COM-032 - `LoadBehavior`, demotion on a failing `OnConnection`, `HKCU` vs `HKLM`, bitness |
| How do promise microtasks and timer/I/O callbacks differ in JavaScript ordering? | JS-007, JS-008, JS-009, JS-010; JS-012 for the Node.js phases |
| When would Node.js worker threads help, and when would batching be the better fix? | JS-014, JS-015; ROLE-004 and ROLE-008 for the C++ side of the same decision |
| What contract would you put between a C++ calculation engine and a JavaScript UI? | ROLE-001, ROLE-002, ROLE-003 |
| How would you test update bursts, workbook closure and shutdown races? | COM-042 - fake sink, injected clock, what cannot be unit tested and needs a hosted suite |

Three more that are likely for this vacancy specifically and are also covered: how twenty thousand updates per second reach a ten-hertz display (ROLE-006, naming RTD, then COM-035 and COM-036); which `Application` settings speed up a bulk write and why they must be restored (COM-033); and what `context.sync()` does and why one call per loop iteration is the Office.js version of per-cell COM (COM-038).

If a live-coding stage is confirmed, the coded forms of two of these are in [Live Coding Scaffold](<../Technical Interview/Live Coding Scaffold.md>).

## 06 Practical Preparation Plan

### Minimum Useful Exercise

Build a small learning prototype if the interview schedule allows. It is a study artifact, not commercial experience.

**With only a few days, do the Office.js version first and treat the native add-in as optional.** Script Lab installs into Excel from AppSource in minutes and runs Office.js snippets without any project setup, so ninety minutes is enough to read a range with `range.load` and `context.sync()`, write a block back, then build a deliberately naive version that synchronizes inside the loop and compare the two on the same sheet. That is a real, honest observation to bring, it maps directly onto the vacancy's "JavaScript for client-side scripting" requirement, and it cannot fail to produce something. A native COM Add-In built from scratch - project setup, registration, bitness, debugging into a host process - is the more impressive artifact and a realistic way to spend an entire weekend and arrive with nothing; attempt it only after the Office.js exercise is done. Write down what surprised you either way: the observations are the deliverable, not the demo.

1. Confirm whether the vacancy uses a native COM Add-In, XLL, VSTO or Office.js.
2. For native COM, create the smallest loadable add-in supported by the chosen project setup, log connect/disconnect, and verify x86/x64 registration behavior.
3. Add one command that reads a rectangular range in one operation, copies it to native structures, performs a simple calculation and writes one result block back.
4. Benchmark bulk transfer against a deliberately naive per-cell path on the same workbook.
5. Move only the pure calculation to a worker, then return the result through the host-safe boundary with cancellation and workbook-close handling.
6. Write down what failed during registration, loading, shutdown and debugging; those observations are more useful in an interview than a polished demo with no explanation.

If the product actually uses Office.js, replace the native prototype with a Script Lab or task-pane exercise that loads ranges, batches operations, calls `context.sync()` deliberately and handles errors and cancellation.

### Five Preparation Sessions

| Session | Output |
|---|---|
| 1. Vacancy and positioning | Preserve the vacancy text; confirm architecture; rehearse the 30- and 90-second answers |
| 2. C++ and COM | Explain ownership, HRESULTs, apartments, marshaling, reentrancy, registration and bitness without notes |
| 3. Excel and performance | Explain the object model, bulk range access, calculation modes and a measurement plan |
| 4. JavaScript and design | Review async ordering, cancellation/backpressure and rehearse the update-pipeline design |
| 5. Evidence and mock interview | Deliver three stories, answer the gap directly and record weak follow-up questions for one final review |

**Compressed to three evenings when the interview is days away, not weeks.** Evening one is the Script Lab exercise above, because it is the only item that converts a gap into something sayable and it is the one thing that cannot be done from reading. Evening two is the technology comparison, the Excel performance controls, RTD and asynchronous UDFs, and Office.js - sessions 2 to 4 collapsed onto the bank questions rather than onto sources. If the recruiter confirms a live-coding stage, add one pass through [Live Coding Scaffold](<../Technical Interview/Live Coding Scaffold.md>) on evening two: type the project once so the build system is not something to recall while being watched, and rehearse exercise two aloud, since it is the coded form of ROLE-006. Evening three is spoken delivery only: the three-minute introduction, the three stories and the gap answer, aloud, in English, against a timer, plus two decisions that take fifteen minutes each - the salary range to state, and confirming with the recruiter which stage this is and whether it includes live coding. The morning of the interview is for the readiness check and the questions list, not for new material.

### Ready-for-Interview Check

- The exact add-in technology and JavaScript runtime are known, or prepared as explicit questions.
- The 30-second answer is under 90 words and the 90-second answer is not expanded into a career chronology.
- Three stories each contain a personal action, observable result and honest ownership boundary.
- COM and Excel knowledge is described as preparation, not as production history.
- One performance answer starts with measurement and names the separate latency components.
- One concurrency answer includes event order, state owner, cancellation/shutdown and validation.
- The main gap can be stated in one calm sentence without apologizing or changing the subject.

## 07 Questions for the Interviews

### Recruiter

- Is direct Excel COM Add-In experience essential from day one, or is the team open to a C++ engineer with adjacent Windows and integration experience?
- What proportion of the role is C++, JavaScript/Node.js and Excel-specific work?
- Is this maintenance of an existing product, modernization work or a new component?
- Which interview stages are technical, and will any stage include live coding or an Excel/COM design exercise?

### Engineering Team

- Is the Excel integration a native COM Add-In, XLL, VSTO solution, Office.js add-in or a combination?
- Where does JavaScript run in the product: browser/webview, Office.js task pane, Node.js service or another host?
- What currently dominates user-visible latency: data arrival, native processing, COM transfer, workbook recalculation or rendering?
- How are asynchronous updates scheduled onto the Excel/COM thread, and how are shutdown and workbook closure handled?
- Which Excel versions and x86/x64 combinations are supported, and how is the add-in registered, signed and updated?
- What automated coverage exists outside Excel, and what requires an Office-hosted integration test?
- What would a successful first three months look like for someone joining without previous Excel COM product experience?

## 08 References

### Repository Sources

- [2021. Oil and Gas Corporate System](<../../2021. Oil and Gas Corporate System.md>) - primary C++/JavaScript evidence.
- [2025-2026. Embedded SIP Desk Phone Platform](<../../2025-2026. Embedded SIP Desk Phone Platform.md>) - asynchronous C++/Qt, Windows tooling, testing and ownership boundaries.
- [2022-2024. Integrated Library System](<../../2022-2024. Integrated Library System.md>) - legacy C and cross-component debugging.
- [2020. Industrial 3D Scanning and Metrology Software](<../../2020. Industrial 3D Scanning and Metrology Software.md>) and [2012-2020. Satellite and UAV Optical Imaging Software](<../../2012-2020. Satellite and UAV Optical Imaging Software.md>) - numerical and data-processing background.

### Official Technical Reading

- [Processes, Threads, and Apartments](https://learn.microsoft.com/en-us/windows/win32/com/processes--threads--and-apartments)
- [Excel performance: Improving calculation performance](https://learn.microsoft.com/en-us/office/vba/excel/concepts/excel-performance/excel-improving-calculation-performance)
- [Build Excel add-ins with Office Add-ins](https://learn.microsoft.com/en-us/office/dev/add-ins/excel/excel-add-ins-overview)
- [Developing Excel XLLs](https://learn.microsoft.com/en-us/office/client-developer/excel/developing-excel-xlls)
- [Programming with the C API in Excel](https://learn.microsoft.com/en-us/office/client-developer/excel/programming-with-the-c-api-in-excel)
