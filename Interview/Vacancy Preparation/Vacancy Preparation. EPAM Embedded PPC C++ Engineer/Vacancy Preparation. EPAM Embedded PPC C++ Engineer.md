# EPAM Embedded PPC C++ Engineer - PPC Runtime Software

> Purpose: preparation for the embedded C++ request in the current EPAM recruiter package. This file owns PPC runtime implementation, integration, diagnostics, timing and target verification. The separate MATLAB/Simulink request has its own vacancy folder. The exact customer, official role title, location, target platform and meaning of PPC have not yet been confirmed.

> This file intentionally has no vacancy-specific technical-interview companion yet. The role description is still too broad to know whether the deep interview will focus on embedded C++, power-plant controls, real-time scheduling, model-based development or a specific controller platform. Reusable preparation is linked below; a companion file should be created only after the recruiter or engineering team supplies that context.

> Related current request: [EPAM MATLAB/Simulink Control Model Engineer](<../Vacancy Preparation. EPAM MATLAB Simulink Control Model Engineer/Vacancy Preparation. EPAM MATLAB Simulink Control Model Engineer.md>).

> Earlier unrelated EPAM request: [Senior C++ Developer - Excel COM Add-In](<../Vacancy Preparation. EPAM Senior C++ Developer, Excel COM Add-In/Vacancy Preparation. EPAM Senior C++ Developer, Excel COM Add-In.md>).

## 01 Vacancy Record

| Field | Details |
|---|---|
| Company | EPAM Systems |
| Working title | Embedded PPC C++ Engineer |
| Exact title | Not supplied |
| Primary scope | Implement, integrate and maintain embedded PPC software in C++ |
| Model-team interface | Understand intended control behavior and collaborate with the separate MATLAB/Simulink workstream |
| Customer / product | Not supplied |
| Location / work mode | Not supplied |
| Employment / rate | Not supplied |
| Vacancy URL | No matching public EPAM vacancy found from the supplied wording |
| Recruiter / source | EPAM recruiter-provided role text |
| Interview format | Unknown |

### Shared Recruiter Text

The supplied description contains both the embedded C++ request and the related MATLAB/Simulink request. It is preserved here in full, but this file prepares specifically for the C++ request.

#### Role Purpose

> Implement, integrate, and maintain embedded PPC software in C++ as the primary responsibility. Use working knowledge of MATLAB/Simulink control models to understand intended behavior and collaborate on the transition from modeled control logic to the running PPC software.

#### Responsibilities

> - Develop and maintain C++ software for PPC control functions and related embedded components.
> - Participate across the software lifecycle: clarify requirements, contribute to design and architecture, implement features, test changes, and support releases and maintenance.
> - Integrate control functionality with PPC runtime components, controller interfaces, measurements, feedback signals, and plant equipment as assigned.
> - Investigate functional and performance issues, including signal handling, scan-cycle behavior, latency, and timing constraints where they apply to the target system.
> - Review control-model behavior and interfaces with MATLAB/Simulink developers to resolve differences between model expectations and embedded implementation.
> - Implement maintainable, testable changes with appropriate diagnostics, fault handling, and error handling for the target environment.
> - Develop or extend unit and integration tests, and work with QA to verify behavior on supported hardware or test platforms.
> - Contribute to code reviews, requirements clarification, technical design, verification, validation, and release preparation.
> - Work with QA and DevOps specialists to make changes buildable, testable, and traceable through the delivery workflow.

### What Is Known and What Is Inferred

**Known from the request:**

- C++ is explicitly the primary responsibility.
- MATLAB/Simulink appears at the interface with the related model-engineering request.
- The work crosses model intent, embedded implementation, controller interfaces, measured signals, feedback, runtime timing and plant equipment.
- The engineer is expected to contribute across requirements, design, implementation, testing, release and maintenance.

**Inference requiring confirmation:**

- `PPC` is now a **high-confidence working expansion of Power Plant Controller**, because the description combines plant equipment, measurements, feedback signals, grid-style control functions, periodic execution and HIL-like target verification. This remains a market-based inference rather than an EPAM-confirmed fact.
- The recruiter package spans two requests: embedded C++ implementation and MATLAB/Simulink model work. This file owns the C++ side and treats the model as an upstream behavioral contract.
- Scan-cycle language suggests periodic control execution, but the period, scheduler, operating system and hard/soft real-time classification are unknown.
- It is unknown whether C++ is handwritten from the model specification, generated with Embedded Coder, or a combination of generated control logic and handwritten runtime/integration code.

### Probable Product Domain and Control Path

Publicly documented roles and controller products with the same combination of PPC, C++, MATLAB/Simulink, measurements, plant equipment and scan-cycle concerns point to a renewable-energy **Power Plant Controller**. The likely product coordinates a wind, solar, battery or hybrid plant at the point of common coupling with the electrical grid.

This is the working architecture to use for preparation, not a statement about the undisclosed customer:

```text
Grid operator / SCADA / plant operator
        | active/reactive-power, voltage, frequency or power-factor reference
        v
PPC input acquisition and signal validation
        | measurements, timestamps, quality and availability
        v
Periodic control execution
        | controller state, limits, ramps, modes and fault logic
        v
Dispatch and equipment interfaces
        | per-asset setpoints
        v
WTG / PV inverter / BESS / STATCOM / capacitor or reactor bank
        |
        +---------------- measurements and feedback ----------------+
```

The PPC probably does not execute the fastest converter-level control loops. It is more likely a supervisory real-time controller that converts plant-level references and point-of-connection measurements into coordinated setpoints for multiple assets.

### Probable C++ Work

The embedded request most likely includes a subset of the following tasks:

- integrate control functions into a periodic PPC runtime rather than implement isolated calculations only;
- acquire measurements and external references, then validate range, units, timestamps, quality flags and freshness;
- maintain controller state across scan cycles, including initialization, mode changes, reset and bumpless recovery;
- apply limits, rate limiters, filtering, deadbands, saturation and equipment-availability constraints;
- distribute setpoints across turbines, inverters, storage and reactive-power equipment;
- handle missing or stale measurements, communication loss, unavailable equipment and partial-plant operation;
- separate the deterministic control path from asynchronous communication, configuration, logging and diagnostics;
- diagnose latency, jitter, missed deadlines, overruns, event ordering and model-versus-target differences;
- integrate handwritten C++ with generated or model-derived control components if the toolchain uses code generation;
- add host tests, runtime integration tests, regression scenarios and target or HIL verification;
- support configuration, release packaging, traceability and operational troubleshooting.

### Probable Stack - Confidence-Labeled

| Area | Probable technology or concept | Confidence and boundary |
|---|---|---|
| Runtime language | C++ | Confirmed as the primary responsibility; standard and restrictions unknown |
| Reference/control environment | MATLAB and Simulink | Confirmed at the team interface; exact ownership unknown |
| Target | Industrial embedded or real-time controller | High probability; processor, OS/RTOS and controller vendor unknown |
| Execution | Fixed-period tasks or scan cycles, possibly multiple rates | High probability from the supplied timing language; periods and deadline class unknown |
| Model integration | Handwritten implementation, generated C/C++, or a hybrid wrapper architecture | All remain possible until confirmed |
| Model tools | Stateflow, Simulink Coder or Embedded Coder | Plausible, not confirmed requirements |
| Verification | Model-in-the-loop, software-in-the-loop, processor-in-the-loop and hardware-in-the-loop | SIL/HIL are especially plausible; the actual stages and rigs are unknown |
| Grid/plant simulation | PSCAD, DIgSILENT PowerFactory, PSS/E or an internal plant simulator | Common in comparable PPC work, but none is confirmed for this request |
| Communications | Modbus, OPC UA, IEC 60870-5-104, IEC 61850 or customer-specific interfaces | Likely category, not a confirmed protocol list |
| Delivery | Git, code review, CI, automated regression, artifact and release traceability | Strongly implied by the supplied QA/DevOps responsibilities; products unknown |
| Diagnostics | Structured events, alarms, signal-quality state, cycle-time metrics and replayable traces | Probable operational need; implementation unknown |

### Control Functions Worth Recognizing

The C++ engineer may integrate these functions without being expected to design their control laws independently:

- active-power control and curtailment;
- reactive-power, voltage and power-factor control;
- frequency response and droop behavior;
- ramp-rate limiting and dispatch distribution;
- equipment capability and priority allocation;
- fault ride-through or post-fault recovery coordination;
- plant operating modes, degraded modes and safe fallback;
- coordination of wind turbines, PV, BESS, STATCOM and switched compensation equipment.

Interview boundary: recognize the purpose, inputs, outputs, states and failure modes of these functions, but do not claim prior grid-control ownership.

## 02 Hiring Hypothesis and Decision

- **Problem the team is hiring to solve:** maintain and extend embedded C++ that executes PPC control behavior, while reducing mismatches between the reference control model and the deployed runtime.
- **Three strongest signals:** production C++; integration across signals and runtime components; verification of model-versus-target behavior.
- **Main risk in my profile:** no direct PPC control-system production experience and no confirmed target platform.
- **Strongest differentiator:** more than seven years of MATLAB algorithm work, including a real MATLAB-to-onboard-software handover boundary, followed by commercial C++ and recent embedded Linux product development.
- **Why the role is interesting:** hands-on embedded C++ combined with controller interfaces, measured signals, timing, fault behavior and hardware-oriented verification.
- **Current decision:** interested and relevant enough to proceed. Clarify the PPC domain, target runtime and ownership boundary with the separate model team.

## 03 Boundary Between the Two Current Requests

### This File - Embedded PPC C++

This is the primary workstream and the closer match:

- production C++ design, implementation and maintenance;
- integration with runtime components and external interfaces;
- signal validity, state ownership, timing and latency diagnosis;
- diagnostics, error handling, fault behavior and recovery;
- unit, integration and target-platform testing;
- code review, release support and cross-functional delivery.

Closest evidence: the embedded SIP phone platform, followed by the EPAM industrial sensor-data system and the integrated library platform.

### Sibling File - MATLAB/Simulink Model Work

The separate [MATLAB/Simulink preparation](<../Vacancy Preparation. EPAM MATLAB Simulink Control Model Engineer/Vacancy Preparation. EPAM MATLAB Simulink Control Model Engineer.md>) owns model development, model validation and model-to-code questions. The C++ request still needs a clear interface with that workstream:

- input, output, state and parameter definitions;
- units, scaling, sample rates, validity and timestamp semantics;
- initialization, reset, safe-state and fault behavior;
- expected numeric and timing tolerances;
- repeatable model-versus-runtime comparison.

My historical MATLAB experience is useful evidence for collaborating across this boundary, but direct Simulink preparation belongs in the sibling request.

## 04 Positioning and Spoken Answers

### Positioning Sentence

C++ engineer with recent embedded Linux product experience and an earlier seven-year MATLAB aerospace-algorithm background, including verification across the boundary from a MATLAB reference implementation to onboard software; new to Simulink and PPC control systems but interested in both sides of the role.

### 30-Second Introduction

I am a C++ engineer with recent experience on an embedded Linux product, working across application logic, asynchronous runtime state, diagnostics, target integration and physical-device testing. Earlier in my career I spent more than seven years developing and validating aerospace algorithms in MATLAB and working with their transition to onboard software. I have not used Simulink or PPC control software directly, but the combination of embedded C++ and model-to-runtime integration is relevant and interesting to me.

### 90-Second Introduction

My recent project was an embedded SIP desk-phone platform built with C++17 and Qt on embedded Linux. I worked on application architecture, asynchronous SDK integration, failover behavior, diagnostics, Yocto image integration and debugging on physical ARM64 devices. That gives me direct experience with production C++, runtime state, cross-component interfaces, error recovery, testing and the difference between desktop behavior and the real target.

Earlier, I spent more than seven years developing image-processing algorithms for satellite and UAV optical systems, mainly in MATLAB. The important connection to this role is not only MATLAB syntax. An algorithm proven in MATLAB still had to be specified, handed over and verified against the implementation running in onboard software, where precision, memory and timing constraints were different. I also worked with calibrated sensor interfaces and validated results using reference and flight data.

I have not used Simulink or developed Power Plant Controller functions in production, so I would need to learn that product and control domain. However, C++ is described as the primary responsibility, and I already have the MATLAB, algorithm, embedded integration and verification foundations needed to approach the model-to-software boundary responsibly. Both parts of the role are interesting to me.

### Interest Answer for the C++ Request

Yes, I am interested in the embedded C++ request. It is relevant to my recent experience with C++ on an embedded Linux product, runtime integration, diagnostics, fault recovery, testing and physical-device validation. I would be happy to clarify the PPC domain, target environment and the interface with the MATLAB/Simulink team.

### Model-Team Interface Answer

I can collaborate with a model team because I have worked at the boundary between a MATLAB reference algorithm and onboard implementation: defining expected behavior, using reference inputs and outputs and investigating differences introduced by target constraints. I would not claim Simulink production experience; this C++ request should define the model as a testable behavioral and interface contract.

### Why Return to EPAM

I spent three years at EPAM working on production C and C++ systems, progressed from junior to middle grade and relocated internally to EPAM Poland while continuing on the same customer project. Since leaving, I have added embedded Linux, Qt product architecture, on-device diagnosis and technical-lead responsibilities. Returning for a role that combines current C++ strengths with my earlier MATLAB background would be a coherent next step rather than a change of direction.

### Do Not Claim

- Direct production experience with Simulink, Stateflow, Simulink Coder or Embedded Coder.
- Direct ownership of a Power Plant Controller, grid-control functions or plant commissioning.
- Control-theory design of active/reactive power, voltage, frequency, droop or dispatch functions.
- Hard-real-time or safety-certification evidence unless the customer confirms the environment and the relevant personal experience is established.
- That MATLAB algorithms were personally implemented in flight code; the flight-software group owned the onboard implementation.
- That `PPC` definitely means Power Plant Controller until EPAM confirms it.

## 05 Requirement-to-Evidence Map

| Priority | Exact requirement | Classification | Evidence / project | Personal action and result | Boundary / gap |
|---|---|---|---|---|---|
| Must | Develop and maintain C++ software for PPC control functions | Adjacent-strong | Embedded SIP platform; EPAM industrial and library systems | Delivered C++ features, defect fixes, architecture changes and maintenance in production products | No PPC control-function ownership |
| Must | Embedded components and target environment | Strong | Embedded SIP desk-phone platform | Integrated C++/Qt with SDK, Yocto image, Linux services, media/network hardware and ARM64 devices | Embedded Linux application/platform integration, not bare-metal controller firmware |
| Must | Full software lifecycle | Strong | All commercial projects | Requirements clarification, design, implementation, review, testing, release support and maintenance | Formal lifecycle and standards depend on the customer |
| Must | Controller interfaces, measurements and feedback signals | Adjacent | Satellite/UAV sensor geometry; industrial sensor-data system; embedded runtime events | Traced measured values and asynchronous state across component boundaries; calibrated IMU-to-camera relationship | No plant controller or closed-loop control ownership |
| Must | Signal handling and scan-cycle behavior | Adjacent / learn | Embedded asynchronous state and timing diagnosis; MATLAB signal/image processing | Diagnosed event ordering, stale state and target timing; worked with sampled data and reference results | No confirmed periodic PPC scheduler or scan-cycle implementation |
| Must | Latency and timing constraints | Adjacent-strong | Embedded media/runtime work; onboard algorithm budget | Investigated timing-sensitive behavior and designed algorithms within a fixed onboard compute budget | Exact PPC deadlines and determinism requirements unknown |
| Must | Review MATLAB/Simulink model behavior | Split | Satellite/UAV MATLAB work | Designed and validated algorithms in MATLAB and compared target results with reference behavior | MATLAB requires refresh; Simulink is new |
| Must | Transition modeled logic to running software | Strong adjacent | MATLAB-to-flight-software handover | Specified algorithm behavior and verified the onboard result against reference data | Did not own the flight-code implementation or automatic code generation |
| Must | Diagnostics, fault handling and error handling | Strong | Embedded SIP platform | Implemented registration watchdog and failover/failback behavior; added diagnostics and validated recovery on devices | Telephony domain rather than plant control |
| Must | Unit and integration tests; QA cooperation | Strong | Qt Test, MSTest, GoogleTest; EPAM external QA | Added focused tests, reproduced target scenarios and supplied validation evidence to QA | Hardware-test automation depth varies by project |
| Must | Buildable, testable and traceable delivery | Strong | Yocto/BitBake, Jenkins, GitLab CI, Bitbucket/Git workflows | Integrated changes into established builds, review and deployment workflows | DevOps platform administration was not the primary responsibility |
| Context | Plant equipment and PPC domain | Gap / unknown | No direct evidence | Transferable hardware, sensor and industrial-data work | Product, plant type and control functions must be learned |

### Coverage Review

- **Strongest match:** production C++ across recent embedded Linux and earlier industrial systems.
- **Most valuable adjacent evidence:** extensive MATLAB algorithm work plus a real reference-model-to-onboard-implementation boundary.
- **Main gap:** Simulink and the exact PPC control domain.
- **Risk-reducing fact:** the supplied role explicitly makes C++ primary and asks for working knowledge, not principal ownership, of MATLAB/Simulink models.
- **Unverified assumption:** PPC means Power Plant Controller and the system belongs to renewable or grid-control equipment.
- **Decision-changing question:** whether independent Simulink control-model development is mandatory from the first day.

## 06 Story Kit

### Story 1 - MATLAB Reference Algorithm to Onboard Software

- **Requirements covered:** MATLAB, model intent, target constraints, verification and cross-team collaboration.
- **Context:** aerospace image-processing algorithms were designed and verified in MATLAB, while another group owned the software running on flight hardware.
- **My responsibility:** design the algorithm, define its expected behavior and verify that the onboard result matched the reference within the relevant criteria.
- **Actions:** used reference and real imagery, inspected intermediate stages, documented assumptions and compared the onboard result with the MATLAB behavior; treated precision, memory, arithmetic and timing as explicit implementation differences.
- **Result:** algorithms could be transferred into the onboard workflow with a reference against which implementation differences were investigated.
- **Validation:** reference datasets, real imagery and flight or in-orbit evidence depending on the instrument.
- **Boundary:** I did not implement the flight-software version and did not use Simulink.
- **Likely follow-up:** how would you distinguish an algorithm mismatch from numeric precision, scheduling or interface differences?

### Story 2 - Embedded Registration Failover and Recovery

- **Requirements covered:** C++, embedded runtime, fault handling, diagnostics, state transitions and target testing.
- **Context:** an embedded SIP phone had to maintain service across primary and backup server failures and later return safely to the preferred server.
- **My responsibility:** customized SDK and application-side integration around watchdog, failover/failback state, authentication information and diagnostics.
- **Actions:** made the transitions explicit, traced asynchronous callbacks and timers, preserved ownership across SDK/application boundaries and validated failure and recovery on physical devices.
- **Result:** predictable recovery behavior with evidence explaining why a transition occurred.
- **Validation:** logs, repeatable server-failure scenarios and target-device testing.
- **Boundary:** this is not a plant controller, but it is direct evidence for embedded state, fault handling and timing-sensitive integration.
- **Likely follow-up:** how did you avoid stale callbacks or an oscillation between primary and backup states?

### Story 3 - IMU-to-Camera Calibration and Interface Error

- **Requirements covered:** measurements, signals, subsystem interfaces, model-versus-physical behavior and validation.
- **Context:** the camera and navigation board behaved correctly independently, but calculated image coordinates were wrong because of their physical alignment.
- **My responsibility:** implement frame-centre coordinate computation and calibrate the IMU-to-camera geometry.
- **Actions:** identified the relationship between the subsystems as the likely owner, estimated the actual alignment rather than trusting nominal geometry and incorporated the correction into the coordinate calculation.
- **Result:** frame coordinates aligned with known ground positions.
- **Validation:** comparison with known ground coordinates and flight data.
- **Boundary:** sensor calibration and geometry, not feedback-control design.
- **Likely follow-up:** why can two correct components still produce an incorrect system result?

### Backup Story - Industrial Sensor Data Through C++, Storage and QA

- **Requirements covered:** C++ maintenance, measured data, full-path debugging, independent QA and release delivery.
- **Context:** an EPAM C++17 system processed industrial sensor data, stored results in MySQL and exposed them through JavaScript workflows.
- **My responsibility:** implement requested functionality, fix defects, simplify inherited C++ and support external QA.
- **Actions:** traced values from input through C++ processing, persistence and presentation, corrected the owning layer and provided reproducible scenarios to QA.
- **Result:** coherent end-to-end behavior and focused modernization within the existing architecture.
- **Boundary:** industrial data processing rather than embedded control execution.

## 07 Technical Preparation - Pending Product Clarification

There is deliberately no role-specific technical Q&A file yet. Use the existing banks for reusable material and create the companion only when the product context is known.

### Shared Banks

- [C++ Core Questions](<../Technical Interview/C++ Core Questions.md>) - ownership, concurrency, atomics, timing, performance, error handling and API design.
- [C Language Questions](<../Technical Interview/C Language Questions.md>) - volatile, memory layout, interfaces, errors and low-level debugging.
- [Testing Questions](<../Technical Interview/Testing Questions.md>) - unit, integration, embedded, concurrency, time and legacy-code testing.
- [Build Systems Questions](<../Technical Interview/Build Systems Questions.md>) - CMake, cross-compilation, dependencies and reproducible builds.
- [Linux and Shell Questions](<../Technical Interview/Linux and Shell Questions.md>) - processes, signals, target diagnosis and services if the runtime is Linux-based.
- [Git Questions](<../Technical Interview/Git Questions.md>) - review workflow, bisect, safe undo and traceable delivery.

### Priority Topics After Clarification

| Topic | Current depth | Why it matters | Next action |
|---|---|---|---|
| Production C++ and ownership | Strong | Primary responsibility | Review role-relevant C++ bank sections after standard/platform is known |
| MATLAB | Refresh | Direct historical experience but not recent | Reopen representative scripts and refresh current workspace, functions, data types and tests |
| Simulink fundamentals | Learn | Explicit requirement | Build one small discrete controller model only after expected depth is confirmed |
| Model-to-code interface | Adjacent | Central boundary in the request | Clarify manual implementation versus code generation; then study the exact workflow |
| Periodic scan cycle | Learn/adjacent | Named performance concern | Confirm cycle period, scheduler, overrun behavior and hard/soft deadline |
| Signal contract | Strong adjacent | Measurements and feedback cross interfaces | Prepare units, range, validity, timestamp, sampling rate and stale-data checklist |
| Control theory / plant behavior | Learn | May be essential if PPC is a Power Plant Controller | Ask which functions and how much control design the C++ engineer owns |
| SIL/PIL/HIL and target tests | Adjacent | Likely verification boundary | Confirm available test rigs, model equivalence rules and tolerances |
| Industrial communication | Learn/adjacent | PPC exchanges references, measurements and commands with SCADA and plant assets | Confirm protocols, update rates, timeout rules and ownership of reconnect behavior |
| Numeric behavior | Adjacent | Model and target can differ through precision, saturation, discretization and execution order | Prepare floating-point tolerance, fixed-point awareness and boundary-case tests |
| Grid-control vocabulary | Learn | Needed to communicate with model and plant engineers | Learn the role of P, Q, V, f, power factor, ramp limits, droop and curtailment without claiming control-design experience |

### Likely Questions to Prepare Later

- How is one PPC scan cycle scheduled, and what happens on an overrun?
- How are input measurements validated, timestamped and marked stale?
- How are setpoints, feedback, internal state and output commands represented at the C++ boundary?
- How is model behavior compared with the embedded implementation, including numeric and timing tolerances?
- Is code generated from Simulink, handwritten from the model, or wrapped around generated components?
- Which work belongs in the deterministic control path and which may run asynchronously?
- How are safe state, degraded mode, watchdog, reset and recovery defined?
- What can be unit-tested on the host, what requires SIL/PIL, and what requires HIL or plant equipment?
- How are communication timeouts, stale measurements and temporarily unavailable assets represented in the controller state?
- How are rate transitions handled when measurements, control functions and equipment interfaces run at different frequencies?
- How are integrator state, saturation and anti-windup behavior preserved across mode changes, reset and failover?

### Smallest Useful Exercise

Do not build a speculative PPC implementation before the platform is known. If Simulink is confirmed as an interview topic, create one preparation artifact:

1. A small discrete-time feedback model with a setpoint, measurement, saturation, stale-input flag and fixed sample time.
2. A matching handwritten C++ step function with explicit input, state and output structures.
3. Reference input vectors and a comparison of model and C++ outputs with stated tolerances.
4. Tests for saturation, invalid input, state reset, missed cycle and recovery.

Interview wording: “I built this to refresh MATLAB and learn the Simulink-to-C++ boundary; it is preparation, not production PPC experience.”

## 08 Questions for EPAM

### Recruiter

- Is this one customer request, and what is the exact role title?
- What does `PPC` stand for in this project?
- What are the location, work mode, contract and start-date constraints?
- Is direct Simulink production experience mandatory, or is MATLAB experience plus willingness to learn acceptable?
- Which interview stages are planned, and is there live coding or a control/model discussion?

### Engineering Team

- What plant or equipment does the PPC control, and which control functions belong to this team?
- What are the target processor, operating system or RTOS, compiler and C++ standard?
- Is control logic handwritten in C++, generated from Simulink, or divided between generated and handwritten components?
- Does the C++ engineer read and review models, modify them, or independently design control behavior?
- What is the scan period, what jitter or overrun is acceptable and what is the safe failure behavior?
- How are measurements, timestamps, validity, setpoints, feedback and actuator commands represented?
- Which diagnostics are available on a deployed controller, and can failing scenarios be replayed?
- What SIL, PIL, HIL, bench or plant test environments exist?
- Which standards, coding rules and traceability requirements apply?
- What would the first three months of successful delivery look like?

## 09 Public Context Found During Preparation

No public EPAM posting matching the supplied wording was found. The following sources explain the likely technology boundary but do not establish facts about this customer:

- [MathWorks: Code Generation by Using Embedded Coder](https://www.mathworks.com/help/ecoder/gs/code-generation-workflows-with-embedded-coder.html) - C/C++ generation from MATLAB or Simulink algorithms, target customization and verification.
- [MathWorks: Code Interface Configuration](https://www.mathworks.com/help/ecoder/platform-and-interface-configuration.html) - aligning generated functions, data and services with an existing target architecture.
- [MathWorks: SIL, PIL and HIL Tests](https://www.mathworks.com/help/sltest/sil-pil-and-hil-tests.html) - back-to-back comparison between model, generated code and target execution.
- [Central Power Research Institute: HIL Testing Facility for PPC Controllers](https://cpri.res.in/index.php/en/content/hil-testing-facility-ppc-controllers) - public evidence that Power Plant Controller verification commonly includes repeatable HIL checks of active/reactive power, voltage, frequency and related plant behavior.
- [Bachmann: Power Plant and Grid Control](https://www.bachmann.info/en/system-overview/grid-measurement-protection-and-control/power-plant-and-grid-control) - a vendor-neutral example of a PPC receiving a plant-level reference, using point-of-connection measurements and distributing setpoints to generating units.
- [Bachmann: M-Target for Simulink](https://www.bachmann.info/en/products/m-target-for-simulink) - a public example of Simulink control logic being generated, installed and executed on an industrial real-time controller while exchanging live process values.

These sources support preparation topics only. They must not be quoted as the EPAM customer's architecture, toolchain or domain.

## 10 Repository Sources

- [2025-2026. Embedded SIP Desk Phone Platform](<../../2025-2026. Embedded SIP Desk Phone Platform.md>) - embedded C++17/Qt, Yocto, target integration, diagnostics, failover and physical-device validation.
- [2012-2020. Satellite and UAV Optical Imaging Software](<../../2012-2020. Satellite and UAV Optical Imaging Software.md>) - extensive MATLAB work, sensor interfaces, calibration, onboard constraints and MATLAB-to-flight-software verification.
- [2021. Oil and Gas Corporate System](<../../2021. Oil and Gas Corporate System.md>) - C++17 industrial sensor-data processing, cross-layer diagnosis and external QA.
- [2022-2024. Integrated Library System](<../../2022-2024. Integrated Library System.md>) - legacy C/C++, Linux, review, CI/deployment and long-lived product maintenance.
- [Recruiter Brief](<../../Recruiter Brief.md>) - current location, availability, work authorization and condensed positioning.
