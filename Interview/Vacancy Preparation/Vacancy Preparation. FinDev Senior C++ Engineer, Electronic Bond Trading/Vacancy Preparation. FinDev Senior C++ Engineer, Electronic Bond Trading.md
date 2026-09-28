# FinDev Senior C++ Engineer - Electronic Corporate Bond Trading

> Purpose: preparation for FinDev's Senior C++ Engineer opening on a distributed, real-time electronic corporate bond trading platform. It contains ready-to-say introductions, the exact role signals, an evidence map, technical preparation, interview stories and questions for the team. Career claims must remain consistent with the project documents in `Interview/`.

> Russian version of this file: [Vacancy Preparation. FinDev Senior C++ Engineer, Electronic Bond Trading - RU](<./Vacancy Preparation. FinDev Senior C++ Engineer, Electronic Bond Trading - RU.md>). Both files carry the same content and must not drift apart.

> Technical interview questions and prepared answers: [Technical Interview Q&A](<./Vacancy Preparation. FinDev Senior C++ Engineer, Electronic Bond Trading - Technical Interview.md>).

> Reusable topic banks: [C++ Core](<../Technical Interview/C++ Core Questions.md>) · [C Language](<../Technical Interview/C Language Questions.md>) · [Databases and SQL](<../Technical Interview/Databases and SQL Questions.md>) · [Linux and Shell](<../Technical Interview/Linux and Shell Questions.md>) · [Build Systems](<../Technical Interview/Build Systems Questions.md>) · [Testing](<../Technical Interview/Testing Questions.md>) · [Containers and Orchestration](<../Technical Interview/Containers and Orchestration Questions.md>) · [Git](<../Technical Interview/Git Questions.md>).

## 01 Role Snapshot and Positioning

### Vacancy Record

| Field | Details |
|---|---|
| Company | FinDev |
| Position | Senior C++ Engineer |
| Team | Distributed across the USA, the UK and Poland |
| Location / mode | Georgia, Poland, Portugal, Cyprus or Spain; remote or hybrid |
| Product focus | Large-scale distributed real-time electronic trading platform for corporate bonds |
| Component | The core list-management service, plus the integration services bridging the platform to internal and external trading systems |
| Stack named | C++20 preferred (C++17 acceptable), Linux, microservices on a shared service framework with schema-driven code generation using FlatBuffers and Protobuf, relational databases and SQL |
| Official vacancy | [FinDev careers](https://fin.dev/career/open-positions/c-engineer-tw-gc-4331161) |
| Relocation | Assistance offered for Poland or Cyprus, including visa sponsorship, flights and the first month's accommodation |

### Exact Vacancy Text

> FinDev is looking for a Senior C++ Developer to join a distributed team, based across the USA, the UK, and Poland, building technology for a large-scale, distributed, real-time electronic trading platform.
>
> The team develops platform for global electronic trading of corporate bonds, supporting a range of trading protocols and workflows. You'll work on core backend services supporting the full lifecycle of trade negotiation and execution, as well as integration services connecting the platform with internal and external trading systems.
>
> **Locations:** Georgia · Poland · Portugal · Cyprus · Spain
>
> **What you will do**
>
> - Enhance and maintain the core list-management service at the center of our electronic corporate bond trading platform, owning the full lifecycle of trade negotiation and execution across a growing set of trading protocols
> - Build and extend the integration services that bridge our platform to internal and external trading systems
> - Work in C++ on Linux in a microservices architecture built on a shared service framework with schema-driven code generation (FlatBuffers and Protobuf)
> - Contribute during all phases of the project lifecycle
> - Work with business counterparts to provide technical solutions to client needs
> - Work with QA and support teams to address issues that arise during development, testing, and in production
>
> **Required**
>
> - 8+ years of commercial C++ development for production services; C++20 preferred, strong C++17 acceptable
> - Strong modern C++ fundamentals, including STL, templates and generic programming, RAII and object lifetimes, design patterns, and core concurrency concepts. Comfort with template-heavy C++ code is important
> - Experience building, debugging, and profiling Linux services in production
> - Ability to select appropriate data structures and algorithms for correct, efficient business logic
> - Experience with microservices or service integrations in distributed systems
> - Working knowledge of relational databases and SQL.
>
> **Nice to have**
>
> - Experience with electronic trading, order-management workflows, or fixed-income markets.
> - Background in low-latency or high-throughput systems, network protocols, or pub/sub architectures.
> - Familiarity with Protobuf, FlatBuffers, Redis, or similar infrastructure technologies.
> - Experience with Python, shell scripting, CMake/Conan, or AI-assisted development tools.
>
> **Benefits:** flexible remote or hybrid setup; health insurance in Poland and Cyprus, 50% coverage for spouses and children; 24 days of paid vacation; 10 days of paid sick leave; 50% reimbursement for professional training, education and conferences; relocation package for Poland or Cyprus, including flight tickets, one month of accommodation for the employee and official family, visa sponsorship, entry permits and residence permits.

### What This Vacancy Is Actually About

Three readings of the text drive the whole preparation, and each is worth stating in the interview because each one is falsifiable.

**This is not a low-latency role.** The required list contains no latency target, no lock-free requirement, no kernel bypass, no busy-wait or NUMA language. Low latency and high throughput appear once, under nice-to-have. What the required list actually asks for is deep C++ fundamentals, comfort in template-heavy code, production Linux debugging, sound data-structure choices, distributed service integration and SQL. That is a correctness-and-evolvability role in a system where mistakes cost money, not a race for nanoseconds. Prepare accordingly, and confirm it with one question rather than assuming it.

**The daily material is generated code and schema evolution.** "A shared service framework with schema-driven code generation (FlatBuffers and Protobuf)" means service boundaries are defined in schema files, the C++ types are generated from them, and the framework around them is template-heavy - which is why the requirement calls out generic programming twice. The recurring engineering problem in such a codebase is not writing a message; it is changing one without breaking a deployed service that was compiled against the old definition.

**"List management" is the domain core, not plumbing.** In corporate bonds a *list* is a set of bonds a client wants to trade together, quoted line by line. A service that owns "the full lifecycle of trade negotiation and execution across a growing set of trading protocols" is therefore a stateful domain service: per-line negotiation state, protocol-specific rules, timeouts, amendments, cancellations, partial execution, and a growing set of protocols pushing on the same model. The phrase "a growing set" is the real signal - the design question is how the model absorbs a new protocol without a rewrite.

| Area | Position for this vacancy |
|---|---|
| Strongest match | Production Linux C and C++ services, debugging across process and language boundaries, long-lived inherited code, relational data correctness, distributed teams across several time zones |
| Supporting match | C++17 enterprise development, C++20 in personal work, asynchronous state machines and recovery, custom inter-service protocols, PostgreSQL and MySQL, CMake, containers and CI |
| Main gap | Five years of commercial C++ against a stated 8+; no fixed-income, order-management or trading-venue experience; no production Protobuf or FlatBuffers |
| Interview strategy | Lead with production Linux service work and cross-boundary diagnosis; give the years number plainly and early; treat schema evolution as the bridge between what the role does daily and what I have actually done |
| Decision to clarify | Whether the eight-year figure is a hard filter or a proxy for production ownership, and whether the role is latency-critical or correctness-critical |

### Positioning Sentence

I am a C++ engineer whose strongest work has been inside long-lived production systems on Linux, where a symptom in one service usually belongs to another one; fixed income is a new domain and my commercial C++ is five years rather than eight, and I would rather state both plainly than let them come out later.

### Do Not Position As

- An engineer with eight years of commercial C++. The number is five, with more than six years of practice in total.
- A trading, fixed-income or market-data engineer. There is no experience in any of them.
- A low-latency specialist. There is no production evidence of microsecond-level work, and the prepared knowledge of batching, coalescing, backpressure and lock-free structures must be presented as prepared knowledge.
- A Protobuf or FlatBuffers user. The adjacent evidence is custom protocols and configuration formats, and a designed-for-change persisted format - not these libraries.
- A template-metaprogramming specialist. Comfortable reading and writing templates, not the author of a heavily metaprogrammed framework.
- A senior by title. The formal grade is middle; describe the scope instead.
- An owner of database schema design or query tuning. The relational work was application-level integration.

## 02 Spoken Answer Kit

### 30-Second Introduction

Hi, my name is Aliaksei Ivanou. I am a C++ Software Engineer with more than six years in C and C++, five of them in commercial employment. My closest match to this role is two years on a decades-old production platform where a C core sat behind Java and Scala services on Linux in AWS, and most of the work was following a failure across those boundaries to whichever component actually owned it. Before that, C++17 and MySQL on an enterprise data product. Fixed income is a domain I have not worked in.

### 90-Second Introduction

Hi, my name is Aliaksei Ivanou. I am a C++ Software Engineer with more than six years of C and C++, five of them in commercial employment, plus an earlier R&D background in numerical and image processing.

The project closest to this role ran for two years at EPAM: an integrated library platform serving a vendor with nine thousand-plus library customers. The core domain logic was C89; around it were Java data services, a Java desktop client and Scala API services, with PostgreSQL underneath and custom inter-service protocols between them, all on Linux in AWS. My primary responsibility was the C core, but the work that mattered was diagnostic: a user saw something wrong in Java or through the REST API, and the cause was in C, in a protocol payload, or in runtime state. I worked log-first across the request path, used gdb and core dumps on the AWS hosts for native failures, and put the fix where the behaviour was actually owned. Releases were twice a year, so a defect that shipped had months to affect customers - that shapes how carefully you change code.

Before that I spent three months on a C++17 enterprise system processing industrial sensor data into MySQL, and my most recent project was an embedded C++17/Qt platform where I implemented a registration watchdog and primary/backup failover.

Two things I would put on the table early: my commercial C++ is five years, not eight, and I have not worked in fixed income or electronic trading.

### Main Self-Introduction for the Technical Interview

Use this version for "Tell me about yourself" when the interviewer leaves the length open. It is written to be spoken in about three minutes.

Hi, my name is Aliaksei Ivanou. I am a C++ Software Engineer with more than six years of C and C++, five of them in commercial employment, and fourteen years across software engineering and R&D.

I can describe my background through the main types of projects I worked on. The first large area was R&D and image processing for satellite and airborne optical systems. The stack there was mainly MATLAB and internal data-processing tools. I worked on image stitching, stabilization, debayering, image fusion, compression and quality assessment. The main value of that work was not only writing scripts, but building algorithms that could improve real image data and still be practical under performance and hardware constraints.

The next project type was industrial metrology and 3D reconstruction. The stack was C++17, Qt and Point Cloud Library. I worked on a prototype that processed measurement and geometry data and connected algorithmic logic with a desktop application. This was a useful transition from research-style development into commercial C++ application development.

After that, I worked on a system for parsing and analyzing industrial sensor data in the oil and gas domain. C++17 was used for backend processing and business logic, MySQL for data storage, and JavaScript for user-facing functionality integrated into the product codebase. I implemented requested features, maintained the JavaScript integration, fixed defects, simplified complicated code and removed obsolete components.

Another important project type was legacy-system modernization. The stack was C, Java, Scala, custom protocols, Linux and cloud-based infrastructure. I worked mostly with C code: bug fixing, feature implementation, refactoring and production debugging. The difficult part was that the C layer was only one part of a larger platform, so before changing code I often had to understand surrounding services, logs, deployment behavior and runtime state.

My most recent project was an embedded SIP/IP desk phone platform. The stack included C++17, Qt 5 Widgets, Linphone SDK/liblinphone, embedded Linux built with Yocto, a mediasoup-based conferencing server in JavaScript/Node.js, and a Windows provisioning tool in C#/.NET. For reference, Linphone is the third-party VoIP/SIP library used by the phone for SIP registration, calls and media handling. The mediasoup server supported WebRTC video conferencing for the phone's C++ client. I worked mainly on the embedded Qt phone application: application architecture, user-facing telephony behavior, call and account state, contacts, phonebooks, device settings and debugging directly on physical phones.

On that SIP project, I personally implemented the registration watchdog and primary/backup failover/failback logic in the customized Linphone/liblinphone stack, integrated its APIs into the application and provisioning paths, and added backup-domain authentication data to SDK AuthInfo. In simpler terms, this allowed the phone to recover when the main SIP server was unavailable while keeping runtime state and authentication behavior consistent. In addition, I finished and built out most of the practical functionality in the Windows Autoprovisioning Manager: device discovery, account setup, backup-server settings, HA1 authentication support, provisioning generation, saved project handling, network-based provisioning workflows, diagnostics, localization and installer packaging. For reference, HA1 is a SIP Digest password hash calculated from the username, SIP authentication realm and password, so it has to match the actual SIP server configuration. I implemented and debugged the HA1 and realm functionality.

From a working-style point of view, I usually bring the most value when the task is ambiguous at the beginning. I like to reproduce the exact problem, collect logs and state, understand which layer owns the behavior, and only then change code. I am also comfortable with incremental modernization: improving structure, tests or maintainability without turning the work into a risky rewrite.

So the common theme across my projects is complex system work. I am comfortable when the real problem is not isolated to one class or one ticket: old code, runtime behavior, protocols, configuration formats, deployment constraints and user-visible behavior all have to line up. I think my strongest value is careful debugging, practical modernization and C++ development in systems where reliability depends on understanding the whole context.

### If the Interviewer Stops You Early

Finish the current sentence and use this close: "The closest match is the two years on the library platform - a C core inside a multi-language distributed system on Linux, where most of the work was cross-component diagnosis. Trading is a new domain for me, and I am happy to go into either the transferable experience or what I have prepared."

### Direct Answer About the Years Requirement

This is the first hard filter in the posting, so it needs an answer that is delivered calmly and without negotiation.

Five years of commercial C++ employment - RIFTEK, EPAM and Innowise. More than six years of practice in total, with sixteen months of full-time independent C++ work and EPAM's mentoring programme and laboratory in between. Fourteen years in software engineering and R&D altogether, counted from 2012; the earlier part was algorithm and image-processing R&D in MATLAB, so I do not count it as C++.

Then say what the difference is supposed to buy, and show it:

"I understand what eight years is usually a proxy for - having owned production code long enough to have been wrong in it and to have lived with the consequences. What I can point to is two years inside a platform older than my career, where releases were twice a year, every change needed approval from reviewers on two sides, and a defect that shipped stayed in front of nine thousand-plus library customers until the next release. That taught me more about production ownership than a third employer would have. If the eight years is a hard threshold rather than a proxy, I would rather you apply it now than after four rounds."

Do not offer the R&D years to close the gap, do not round five up, and do not add the independent period into the commercial figure. The number is the number; the argument is about what it represents.

### Direct Answer About the Trading-Domain Gap

I have not worked in fixed income, electronic trading or order management. What I have prepared is the shape of the domain rather than a claim to know it: that corporate bonds trade over the counter rather than on a central order book, that request-for-quote is the dominant institutional protocol, that a list is a set of bonds quoted line by line while a portfolio trade is priced as one package, and that the negotiation lifecycle is essentially a state machine per line with timeouts, amendments and cancellations.

What I would bring is the engineering side of that: modelling a lifecycle whose states are defined by a business, keeping two services from disagreeing about which state a thing is in, and adding a new protocol to a model that already serves several. The domain vocabulary I would expect to spend the first month learning, and I would say so rather than nodding along.

### Why This Company and Role

Three reasons, all specific.

The product is a system where correctness across service boundaries is the whole problem, and that is the work I have consistently been most useful at. The library platform, the sensor-data product and the SIP phone were all cases where a symptom in one layer belonged to another; a trade negotiation lifecycle spread across a list-management service and a set of integration services is the same shape with money attached.

The team is distributed across the USA, the UK and Poland. I worked for two years in exactly that configuration at EPAM - a US customer, QA in India, engineers in Poland - and I know what it costs: a question asked at the end of my day is answered at the end of theirs, so an ambiguous report costs a day rather than five minutes. I write reproduction steps for someone I cannot interrupt.

And the logistics are already solved rather than hypothetical. I hold a Polish national D visa and worked legally in Warsaw for two years, so Poland is a return rather than a first move.

## 03 Requirement-to-Evidence Map

Use this table to choose evidence, not as a script to recite. `Adjacent` means the engineering skill transfers, but the vacancy's named environment is new.

| Vacancy requirement | Fit | Evidence to discuss | Boundary of the claim | Best source/story |
|---|---|---|---|---|
| 8+ years of commercial C++ for production services | Gap | Five years commercial - RIFTEK, EPAM, Innowise; more than six years of practice; fourteen years in engineering and R&D | The number is five, and no framing changes it | Direct answer above |
| C++20 preferred, strong C++17 acceptable | Match on C++17, partial on C++20 | C++17 across the oil-and-gas, metrology and SIP projects; C++20 in the independent desktop application; prepared on concepts, ranges, `<=>`, `jthread`, `span` and coroutines | C++20 has not been the language of a commercial codebase I worked in | Story 3; [CPP-114](<../Technical Interview/C++ Core Questions.md#question-cpp-114>) |
| Modern C++ fundamentals: STL, RAII, object lifetimes, design patterns, concurrency | Strong | Ownership and lifetime work across every C++ project; asynchronous state on the SIP platform; the C++ bank covers the interview surface | Fundamentals are strong; deep template metaprogramming is not the strongest area | Story 2 |
| Templates, generic programming, comfort in template-heavy code | Partial | Everyday STL and template use; prepared on instantiation, specialization, SFINAE vs concepts, variadics and fold expressions, `if constexpr`, CTAD | I have read and written templates, not maintained a heavily metaprogrammed framework - say this rather than let a follow-up find it | [CPP-111](<../Technical Interview/C++ Core Questions.md#question-cpp-111>) to [CPP-113](<../Technical Interview/C++ Core Questions.md#question-cpp-113>), [CPP-192](<../Technical Interview/C++ Core Questions.md#question-cpp-192>) |
| Building, debugging and profiling Linux services in production | Strong | Two years on a C core inside a Linux platform on AWS: log correlation across the request path, gdb and core dumps over SSH, Valgrind in the toolchain, Jenkins builds on AWS Linux | Strong debugging and measurement-first optimization; no production case of running a sampling profiler against a live service - say so | Story 1 |
| Selecting data structures and algorithms for correct, efficient business logic | Strong | Image-processing and point-cloud algorithm work; container and complexity choices in C++ projects; the algorithms section of the C++ bank | Algorithmic background is real; competitive-programming speed is not the claim | PELENG compression decision |
| Microservices or service integrations in distributed systems | Adjacent to strong | A platform of C, Java and Scala services with custom inter-service protocols and a REST API, on AWS; I added a Scala API request, exposed it through Swagger and connected it to C-side processing | Integration across services in a distributed platform - not ownership of the service topology, deployment platform or orchestration design | Story 1 |
| Relational databases and SQL | Match at application level | PostgreSQL on the library platform, MySQL on the sensor-data product, PostgreSQL in an earlier satellite-parameter database with a Rails web interface | Application-level integration and correctness; not schema design ownership or query-plan tuning | Story 3 |
| Electronic trading, order management, fixed income | Gap | None | No experience in the domain at all | Direct answer above |
| Low-latency or high-throughput systems, network protocols, pub/sub | Adjacent | Onboard compression designed against a fixed computation budget; SIP and WebRTC integration; custom inter-service protocols; prepared on batching, coalescing, throttling, backpressure, tail latency and lock-free structures | The prepared material is preparation. The one real example is a stated budget on flight hardware, not a service under load | PELENG budget; [CPP-071](<../Technical Interview/C++ Core Questions.md#question-cpp-071>) to [CPP-077](<../Technical Interview/C++ Core Questions.md#question-cpp-077>) |
| Protobuf, FlatBuffers, Redis or similar | Gap with a strong bridge | Custom inter-service protocols and payload debugging; a provisioning format deliberately designed for reviewable evolution rather than generic serialization | I have not used Protobuf, FlatBuffers or Redis in production | Backup story |
| Python, shell scripting, CMake/Conan, AI-assisted tooling | Partial | CMake across C++ projects including the published C++20 application; shell work throughout Linux service debugging; Python used occasionally rather than as a primary language; Conan not used | Do not claim Conan or production Python | [BLD-005](<../Technical Interview/Build Systems Questions.md#question-bld-005>), [BLD-009](<../Technical Interview/Build Systems Questions.md#question-bld-009>) |
| Working with business counterparts, QA and support | Strong | Business analyst and Product Owner on the library platform; a separate QA organisation in India on the sensor-data product; customer developers as direct collaborators and reviewers | Regular direct client presentation or business ownership is not established | Story 1 |
| English as the working language across the USA, the UK and Poland | Match | Professional working English since 2021 across distributed teams in the US, India and Europe; Streamline certificates through B1, B2 course exam 60.5/100 below the 70/100 threshold | State the level as B2 and demonstrate it; do not claim a B2 certificate | The interview itself |

## 04 Three Stories, Written Out

Non-technical questions outside these - a decision you would defend, a time you were wrong, a disagreement, the hardest bug, mentoring, weakness, deadlines, why you, and salary - are answered in [Behavioral Questions](<../Behavioral Questions.md>).

Each is about two minutes spoken. They are written to be said, not read - the wording is the deliverable, because a story assembled live from bullet points arrives as a list of facts instead of as a story. Rehearse aloud; adjust the wording to sound like you, but keep the ownership boundaries exactly as they are.

### Story 1 - Following a Failure Across Four Runtimes

*Use for: production Linux debugging, distributed service integration, legacy code, joining a large system, working with QA and business counterparts. This is the primary story for this vacancy.*

"For two years at EPAM I worked on an integrated library platform - the operational software a library runs on - for a vendor whose product serves over nine thousand libraries worldwide.

The architecture is what makes it worth describing. It is not one application. There is a C89 core carrying the domain behaviour, Java data services mediating database access, a Java desktop client used by library staff, and Scala API services exposing functionality through a documented REST API. PostgreSQL holds most of the operational data. The components talk partly over custom inter-service protocols. So one user-visible operation crosses several processes and several language runtimes before it produces a result.

My primary responsibility was the C core, shared with two other C developers. But the part I would emphasise is diagnostic, because the symptom almost never appeared where the cause lived. A user saw something wrong in the Java client or through the REST API, and the root cause was in C, in a data service, in a protocol payload, or in runtime state.

So the method was log-first: correlate logs across the request path, add focused logging where the existing evidence was not enough, and only then decide which component owned the fix. For native failures - crashes, invalid memory access - I worked over SSH with gdb and core dumps on the AWS Linux machines, because compilation and realistic validation happened there rather than locally.

The defects were genuinely varied. One was a time-handling bug that would have broken behaviour at the upcoming US daylight-saving transition; the cause was an increment placed at the wrong point in a C function's control flow. Another class was fixed-size character arrays with length or initialisation problems, which surfaced as truncated text in one place and as crashes from uninitialised memory in another. Another was ideographic search: the C-side matching applied word-oriented boundaries to Japanese text, which is searched in character and syllable units rather than whitespace-delimited words, so the fix had to restore correct Japanese matching without changing the existing Chinese and Korean behaviour.

I also crossed the language boundaries when a task required it - I added an API request in Scala, exposed it through Swagger and connected it to established C-side processing, which meant keeping request mapping, return values and error semantics coherent across two runtimes and the documentation at once.

Over the two years I delivered at least twelve features and a considerably larger number of defect fixes. The thing I actually took from it is that releases were twice a year: a defect that shipped stayed in front of those libraries for months. That is why every change needed approval from every reviewer on both sides, and why I got into the habit of writing down not just what I changed but what I had checked would still behave the same way."

**Boundary if pressed:** I was one of two or three C developers doing the group's feature work; I did not lead development. The Java and Scala contributions were task-driven, not ownership of those services. The database work was application-level, not schema design or tuning.

### Story 2 - Asynchronous State and Recovery

*Use for: concurrency, state ownership, failover and reliability, and for "tell me about something hard you built".*

"On the embedded SIP desk-phone platform I implemented the registration watchdog and the primary/backup failover and failback logic in the customised Linphone SDK, then integrated it through the Qt application and the provisioning path.

The requirement sounds small - support a backup SIP server - and it isn't. Storing two addresses is the easy part. The system has to know which server is currently active, keep registration and authentication context coherent across a switch, update the application models so the UI isn't showing a server the phone is no longer registered to, and persist and generate configuration that stays compatible with what's already deployed.

My first approach was to do the switching at application level. That was wrong, and I reverted it. The state belonged in the SDK, because the SDK owns registration and it was the only place that could see the transitions in order - at application level I was reconstructing state I could only observe second-hand and always slightly late.

So I implemented the watchdog and the failover/failback state machine in the SDK, added a backup-domain field to the SDK's AuthInfo so authentication survived the switch, and had the application consume SDK-owned state rather than maintain its own copy. That last rule is what stopped the UI and the persisted configuration drifting apart.

The result is that a deployed phone keeps working when the primary server becomes unavailable and returns to it automatically once it recovers, and the recovery is reproducible as a scenario on a physical device rather than something we hoped about. The application integration was merged.

The reason I bring this to a trading platform conversation is the rule I ended up with: exactly one component owns a piece of state, and everything else observes it. Two components that each maintain their own copy will disagree, and the disagreement shows up as a user-visible inconsistency long before anyone finds the race."

**Boundary if pressed:** the watchdog, the failover/failback logic and the AuthInfo change were mine; unrelated upstream Linphone code and the rest of the project fork were not.

### Story 3 - One Value in Five Forms

*Use for: C++ with a relational database, data correctness across a lifecycle, joining a codebase quickly, working with remote QA.*

"At EPAM I worked on a system that processed industrial sensor data for a Fortune 500 oilfield-services company. C++17 parsed and analysed incoming measurements, results went into MySQL, and a JavaScript layer inside the product exposed them through the user-facing workflows. It ran in Azure. Five C++ engineers at EPAM, a separate QA organisation in India, and the customer in the United States.

The thing that shaped the engineering is that one measurement existed in five forms at once: raw input, parsed C++ state, a calculated value, a database record, and something a user looked at. A defect could originate at any of those points and surface far from its cause.

So a typical task started as 'this number is wrong'. My first job was getting representative input that reproduced it. Then I followed the value: what the parser built, what the calculation did with it, what was actually written to MySQL, what the user-facing layer displayed. Each stage looks innocent in isolation. The fix goes at the layer that actually owned the wrong behaviour, which is regularly not the layer where it was noticed.

Part of the work was simplifying inherited logic - conditions that could no longer be true, branches kept for a configuration that no longer existed, helpers duplicated with small differences. The rule I worked to is that a simplification has to be provably behaviour-preserving before it is worth anything, which in practice means understanding why the dead branch was written before deleting it, because sometimes it is not dead.

In three months I delivered at least four customer-requested features plus defect fixes and cleanup. The relevance here is that a trade moving through negotiation and execution is the same problem with higher stakes: the same logical thing exists as an inbound message, as service state, as a persisted row and as something a trader sees, and the engineering is keeping those from disagreeing."

**Boundary if pressed:** the MySQL work was application-level integration, not schema design or tuning. Three months is a short assignment and I would not claim architectural ownership. Do not invent a dataset size or a performance number.

### Backup Story - A Persisted Format Designed to Change

*Use this when the conversation reaches Protobuf, FlatBuffers or schema evolution, because it is the honest bridge from what I have done to what this role does daily.*

"On the SIP platform I extended the Windows provisioning tool - C# and WinForms - that administrators use to configure phones in bulk. One thing it couldn't do was save a project: you set up a configuration, and next time you reconstructed it by hand.

The obvious implementation is to serialise the control tree generically. It's about twenty lines and it works on the first day. I deliberately didn't do that, and this is the decision I'd defend: a generic dump makes the file format an accident of the UI layout. Rename a control, reorder a panel, and old files break in ways nobody can review, because the format was never written down anywhere.

Instead I built explicit project-state objects - gather the state into them deliberately, restore from them deliberately. The cost is real and I'd state it up front: every new field needs mapping work, so the cheap path is genuinely cheaper on day one. What you buy is that fields, defaults and schema growth are all visible in a diff, so adding a field is a reviewable change rather than a hope.

The save/load flow shipped in the tool's 2.x line. I also migrated the project from .NET Framework 4.8 to .NET 8 while I was in there.

What I take from it to a schema-driven codebase is that this is the problem those tools solve properly and by default: an explicit, versioned, reviewable definition, with compatibility rules that are enforced rather than remembered. I arrived at a hand-rolled version of the same principle because I had to; I have not used Protobuf or FlatBuffers in production."

**Boundary if pressed:** this is Windows desktop delivery in C#, not C++ service work, and the format was mine rather than an interface between teams.

## 05 Technical Preparation

Reusable answers live in the shared banks; cross-stack and architecture answers live in the [vacancy-specific technical companion](<./Vacancy Preparation. FinDev Senior C++ Engineer, Electronic Bond Trading - Technical Interview.md>). Prepared knowledge must not be described as past production experience.

| Topic | Current position | Preparation priority |
|---|---|---|
| Modern C++ fundamentals, STL, ownership, lifetimes | Strongest evidence; refresh the interview surface | Medium |
| Templates and generic programming | Everyday use, not framework authorship; the stated requirement makes this a likely screen | Highest |
| C++20 specifically | Personal-project use; refresh concepts, ranges, `<=>`, `jthread`, `span`, `format` | High |
| Linux production debugging and profiling | Strong evidence; refresh `perf`, and rehearse the boundary about sampling profilers | Medium |
| Concurrency and memory model | Strong product evidence; refresh atomics, ordering and lock-free at interview depth | High |
| Distributed services, protocols, pub/sub | Adjacent evidence; prepare the vocabulary and the failure modes | High |
| Schema-driven serialization: Protobuf, FlatBuffers | No evidence; the cheapest gap to close before the interview | Highest |
| SQL and relational reasoning | Application-level evidence; refresh joins, indexes, plans and isolation | Medium |
| Corporate bond trading domain | No evidence; learn the shape, not the jargon | High |
| Algorithms and data-structure selection | Strong background; refresh the standard interview set | Medium |

### Domain Primer

This is prepared knowledge from public sources, and it should be introduced that way: "here is what I read to understand the shape of the problem" rather than "here is what I know about the market".

Corporate bonds do not trade like equities. There is no single central order book; the market is over the counter and dealer-intermediated, liquidity is fragmented across a very large number of individual instruments identified by ISIN or CUSIP, and most of those instruments do not trade on any given day. That fragmentation is why the protocols exist at all.

**Request-for-quote (RFQ)** is the dominant institutional protocol: a client asks a chosen set of dealers for a price on a specific bond and size, the dealers respond, and the client executes against one of the responses. The variations are about who is asked and who can see what - a broad "blast" to many dealers versus a targeted selection, disclosed versus anonymous, and **all-to-all** models where buy-side firms can respond as well as dealers.

**List trading** is RFQ applied to many bonds at once: the client sends a list, and each line is quoted individually. **Portfolio trading** is the alternative, where a whole basket is negotiated as a single package price with one counterparty - which reduces information leakage and lets illiquid bonds be priced alongside liquid ones. Those two are genuinely different products, not two names for one thing, and a service that manages lists has to model both if the platform offers both.

The lifecycle behind all of it is inquiry and negotiation, quote, execution, then allocation, confirmation and settlement downstream. **FIX** is the standard messaging protocol for order and execution flow between such a platform and external order-management and execution-management systems, which makes it the most likely thing behind the phrase "integration services connecting the platform with internal and external trading systems" - worth asking rather than assuming.

The engineering translation, which is the part to actually talk about: a list is a collection of line items, each with its own negotiation state, its own timers, and rules that differ by protocol. The interesting questions are how a new protocol is added without special-casing it through the whole model, how partial execution and amendment are represented, what happens to in-flight state when a service is redeployed, and how two services are prevented from disagreeing about whether a line is still live.

### The Performance and Profiling Answer

The vacancy names profiling as a requirement, so this needs one chosen example and one honest boundary, decided before the interview rather than improvised in it.

**The example to use: onboard image compression at PELENG.** The constraint was not "make it faster", it was a fixed computation budget on hardware that could not be updated after launch, with image quality as the thing being traded. I implemented lossy compression reaching a 3-4x ratio inside that budget, then improved delivered image quality at the same ratio and the same budget by predicting image type and adapting quantization to it. The measurement was against the budget and against a resolution criterion that I later automated, because "how well can you resolve detail in this image" was an expert judgement until it was turned into a defined, repeatable measurement.

Why this one: it is the only example where the constraint was numeric and stated in advance, the tradeoff was explicit, and the improvement is expressed as "same cost, better result" rather than as an unverifiable speedup.

**The boundary to state, in one sentence.** My performance work has been measurement-first optimization against a stated budget, plus native debugging with gdb, core dumps and Valgrind in the project toolchain. I have not run a sampling profiler against a live production service, and I would not describe myself as a profiling specialist.

**Then turn it into method, which is what the question is really testing.** For a service in this platform I would start by naming the metric and the percentile, because the tail is what a trader experiences and the mean hides it. Then separate the latency into its parts - inbound deserialization, the service's own processing, any database or cache round trip, downstream service calls, and serialization out - and measure each before deciding where the work belongs. The reason that order matters is that the intuitive answer and the real answer usually point at different code: in a schema-driven microservice architecture the time is frequently in the number of boundary crossings and in allocation, not in the business logic everyone is looking at.

### C++ Checklist

The requirement names templates and generic programming explicitly and twice, so this is the section to over-prepare.

- Value categories, move semantics, `noexcept` moves and why containers copy without them; copy elision and guaranteed elision.
- Forwarding references, reference collapsing and `std::forward`; why a forwarding-reference constructor hijacks copies.
- Ownership: `unique_ptr`, `shared_ptr`, `weak_ptr`, control blocks, cycles, and passing smart pointers by the contract rather than by habit.
- Template instantiation, specialization, partial specialization and why function templates cannot be partially specialized; two-phase lookup and dependent names.
- Variadic templates, fold expressions, parameter-pack forwarding.
- SFINAE, `enable_if`, tag dispatch - and how concepts and `requires` replace them; what a concept buys in error messages.
- `if constexpr` versus specialization; type traits; CTAD and deduction guides.
- CRTP and static polymorphism versus virtual dispatch versus `std::variant` and `std::visit` - and the cost of each.
- Exception guarantees, RAII under partial construction, and `expected`-style error handling versus exceptions versus error codes.
- ABI and API compatibility, PImpl, inline and ODR - the reason a shared framework header is a liability if it changes.
- Concurrency: data race versus race condition, mutexes and the ordering they provide, condition-variable predicates, atomics and memory order, false sharing, lock-free progress guarantees and the ABA problem.
- Containers and complexity, iterator and reference invalidation, and when a sorted `vector` beats a node-based container.
- Allocation cost, reserve, small-string optimization, arenas and `pmr`.
- C++20 in production: concepts, ranges, `<=>`, designated initializers, `span`, `jthread` and `stop_token`, `format`, and an honest position on coroutines.

### Linux Production Services Checklist

- Where to look first when a service misbehaves: logs, `journalctl`, resource limits, file descriptors, disk, then the process itself.
- Diagnosing a hung process: `ps`/`top`, `/proc/<pid>/status` and `wchan`, `strace`, `gdb -p` and `thread apply all bt`.
- Core dumps: enabling them, `coredumpctl`, matching symbols, and reading `.ecxr`-equivalent state in gdb.
- Sanitizers versus Valgrind: what each catches and what each costs; why ASan and TSan belong in CI rather than in production.
- `perf top`, `perf record`/`report`, `perf stat` counters, and flame graphs - describe the tool honestly as something prepared rather than production-worn.
- Memory growth versus fragmentation, RSS versus heap, and how to tell a leak from an allocator pattern.
- systemd units, restart policy, and what a restart hides.
- The shell discipline that makes a diagnostic script safe: `set -euo pipefail`, quoting, and not parsing `ls`.

### Distributed Services and Serialization Checklist

- Protobuf: wire format basics, field numbers as the contract, `optional` and presence, reserved fields and tags, backward and forward compatibility rules, `oneof`, and what changing a field type actually breaks.
- FlatBuffers: access without parsing, the vtable-per-object layout, why it suits read-heavy and latency-sensitive paths, and its evolution rules - append-only fields, `deprecated`, and why struct layout is fixed.
- The choice between them, stated as a tradeoff rather than a preference: parse cost and access pattern versus message size, mutability and ecosystem.
- Versioning a service interface: additive change, the two-phase deploy, and never reusing a retired field number.
- Idempotency, at-least-once delivery and deduplication keys; why "exactly once" is a property of the consumer, not the transport.
- Ordering: per-key ordering versus global ordering, and what a sequence number buys you.
- Timeouts, retries with backoff and jitter, and why a retry without idempotency is a correctness bug.
- Pub/sub versus request/response versus an event log; fan-out, slow consumers and backpressure.
- Health, readiness, graceful shutdown and draining in-flight work - and what happens to state a service is holding when it is redeployed.
- Observability: structured logs with a correlation identifier, metrics with percentiles, and tracing across service hops.

### SQL Checklist

- Join types and the one people get wrong; `NULL` semantics in predicates, aggregates and joins.
- Index structure and cost, composite index column order, covering indexes, and the cases where an index will not be used.
- Reading a query plan, and the difference between a slow query and a slow system.
- The N+1 pattern as it appears from a service rather than an ORM.
- ACID in practice, isolation levels and the anomalies each permits, and database deadlocks and how lock ordering avoids them.
- Transactions across a service boundary: why a distributed transaction is usually the wrong answer and what replaces it.

### System-Design Prompt to Rehearse

Design the service that owns a list of bonds through negotiation and execution, with several trading protocols, external systems on both sides, and no tolerance for two services disagreeing about whether a line is still live.

Cover these points in order:

1. The domain model: list, line item, the states a line moves through, and what is protocol-specific versus common.
2. Where state lives, who owns it, and how everything else observes it rather than copying it.
3. The message contract: schema definitions, versioning rules, and how a new protocol is added without special-casing it through the model.
4. Idempotency, ordering and deduplication on both the inbound and the outbound side.
5. Timeouts, amendments, cancellations and partial execution as first-class states rather than error paths.
6. Persistence and recovery: what survives a restart, how in-flight negotiations are resumed, and how a deploy is done without losing live state.
7. Observability: the correlation identifier that follows one line item across every service, and the metrics that would show a protocol behaving differently from the others.
8. Testing: what can be unit-tested, what needs a fake counterparty, and what only an integrated environment can prove.

### Likely Technical Questions and Where the Answer Is

Every question below has a prepared answer. The point of the table is that nothing on this list has to be improvised, and that the review before the interview is a list of IDs rather than a re-read of everything.

`CPP` = [C++ Core](<../Technical Interview/C++ Core Questions.md>), `C` = [C Language](<../Technical Interview/C Language Questions.md>), `DB` = [Databases and SQL](<../Technical Interview/Databases and SQL Questions.md>), `NIX` = [Linux and Shell](<../Technical Interview/Linux and Shell Questions.md>), `BLD` = [Build Systems](<../Technical Interview/Build Systems Questions.md>), `TEST` = [Testing](<../Technical Interview/Testing Questions.md>), `CNT` = [Containers and Orchestration](<../Technical Interview/Containers and Orchestration Questions.md>), `ROLE` = [the technical companion](<./Vacancy Preparation. FinDev Senior C++ Engineer, Electronic Bond Trading - Technical Interview.md>).

| Likely question | Prepared answer |
|---|---|
| Explain SFINAE, and what concepts changed | CPP-113, with CPP-111 and CPP-112 for instantiation and packs |
| When does `if constexpr` replace a specialization? | CPP-192, CPP-193 for CTAD |
| Which C++20 features would you actually use here? | CPP-114; CPP-190 for `<=>`, CPP-126 for `jthread`, CPP-051 for `span` |
| Why must a move constructor be `noexcept`? | CPP-007, then CPP-039 for what `vector` does without it |
| How do you pass a `shared_ptr`, and when should you not use one at all? | CPP-031, CPP-032, CPP-038 |
| Exceptions, error codes or `std::expected` in a service? | CPP-146; CPP-091 for the guarantees |
| How do you change a public interface without breaking a deployed binary? | CPP-147, CPP-095, CPP-093 |
| Race condition versus data race - and which one do your tools find? | CPP-053, CPP-052 |
| What does a mutex give you besides mutual exclusion? | CPP-054, CPP-065, CPP-066 |
| What does lock-free actually mean, and when is a mutex faster? | CPP-182, CPP-184, CPP-061 |
| Why does adding cores not add throughput? | CPP-068, then CPP-071 for how to establish that |
| `map` or `unordered_map` - and when neither? | CPP-045, CPP-046, CPP-044, CPP-115 |
| Design a producer-consumer queue with shutdown | CPP-069, CPP-070; CPP-183 for the lock-free variant |
| A service is slow in production. Walk me through it | CPP-071, CPP-072, CPP-073; NIX-014, NIX-008; ROLE-006 |
| It leaks over days but not in staging | CPP-085; C-020 for the tool choice; ROLE-009 |
| Read this core dump / find this crash | C-019, CPP-084 |
| What does a slow query look like from the service side? | DB-007, DB-005, DB-006, DB-008 |
| Which isolation level, and which anomaly does it permit? | DB-009, DB-010, DB-011 |
| Protobuf or FlatBuffers, and why? | ROLE-001 |
| How do you evolve a schema without breaking a deployed service? | ROLE-002, with ROLE-012 for the deploy |
| Where does the state of a negotiation live? | ROLE-003, ROLE-004 |
| What makes a trading message idempotent? | ROLE-005 |
| Design the integration to an external venue | ROLE-007, ROLE-011 |
| How do you test a service that depends on other services? | ROLE-008; TEST-010, TEST-012, TEST-013 |
| What does a shared service framework cost you? | ROLE-010 |
| How is the project built and dependencies managed? | BLD-005, BLD-006, BLD-009 |
| How do you debug a C++ process inside a container? | CNT-008, CNT-006 |

If a live-coding stage is confirmed, work through [Live Coding Scaffold](<../Technical Interview/Live Coding Scaffold.md>) once so the build system is muscle memory rather than something to recall while being watched.

## 06 Practical Preparation Plan

### Minimum Useful Exercise

The largest closable gap is schema-driven serialization, and it is closable in an evening. Everything else on the gap list - eight years, fixed income - is not closable at all, so this is where preparation time earns the most.

1. Define one small `.proto` describing something from the domain: a list, its line items, a quote, an execution. Generate the C++ and write a round trip.
2. Now evolve it the way a real service would: add a field, retire another, and change a type. Serialize with version one, deserialize with version two and the reverse. Write down exactly what survived, what silently changed, and what broke. The reserved-field rule stops being a documentation item once you have watched a reused field number produce plausible garbage.
3. Define the same message in a FlatBuffers schema, access a field without parsing, and compare both the code and the access pattern. Note what FlatBuffers makes easy and what it makes rigid.
4. Write the one paragraph you would say in the interview: when you would choose each, based on what you observed rather than on what the documentation claims.

Time-box it to one evening. The deliverable is the written observations, not the code - "I built this as preparation; it is not production experience" is the exact wording to use.

If there is a second evening, spend it on templates rather than on more serialization: write a small generic function with a concept constraint, then the same thing with `enable_if`, and be able to explain what the error messages do differently. That is the requirement the posting states twice.

### Preparation Sessions

| Session | Output |
|---|---|
| 1. Vacancy and positioning | Confirm the architecture questions; rehearse the 30- and 90-second answers and the years answer until it is calm rather than defensive |
| 2. Templates and modern C++ | Explain instantiation, specialization, SFINAE versus concepts, `if constexpr` and CTAD without notes; state the C++20 position honestly |
| 3. Serialization and distributed failure modes | The Protobuf/FlatBuffers exercise; idempotency, ordering, retries, backpressure and graceful shutdown |
| 4. Linux, SQL and debugging | One end-to-end diagnostic narrative; joins, indexes, plans and isolation levels |
| 5. Evidence and mock interview | Deliver three stories, answer both gaps directly, and record weak follow-ups for one final review |

**Compressed to two evenings when the interview is close.** Evening one is the serialization exercise plus the templates refresher, because those are the two things the posting names that cannot be answered from career evidence. Evening two is spoken delivery only: the three-minute introduction, the three stories, the years answer and the domain answer, aloud, in English, against a timer - plus two decisions that take fifteen minutes each: the salary range to state, and confirming with the recruiter which stage this is and whether it includes live coding. The morning of the interview is for the readiness check and the questions list, not for new material.

### Ready-for-Interview Check

- The years answer can be delivered in under thirty seconds, without apology and without rounding.
- The domain answer distinguishes list trading from portfolio trading and RFQ from all-to-all, and is introduced as preparation.
- Three stories each contain a personal action, an observable result and an honest ownership boundary.
- The templates answer goes beyond "I use the STL" - one concrete example of a constraint, a specialization or `if constexpr` solving a real problem.
- Protobuf and FlatBuffers can be compared from something observed, not from documentation.
- One performance answer starts with a metric and a percentile and names the separate latency components.
- One debugging narrative runs from symptom to owning component across a service boundary.
- The two gaps can each be stated in one calm sentence without changing the subject.

## 07 Questions for the Interviews

### Recruiter

- Is the process run directly by FinDev, or through a partner or vendor?
- The posting asks for eight years of commercial C++. Mine is five, with more than six years of practice overall - is that figure a hard filter, or is the team assessing production ownership more broadly?
- Which interview stages are technical, and does any stage include live coding or a system-design exercise?
- Which of the listed locations applies to this opening in practice, and is the Poland option Warsaw-based or fully remote?
- Is direct fixed-income or electronic-trading experience expected from day one?

### Engineering Team

- Is this service latency-critical in the microsecond sense, or is the binding constraint correctness and evolvability across protocols?
- What does "a growing set of trading protocols" look like from inside the code - how much of a new protocol is configuration, and how much is new code in the list model?
- Where does the negotiation state actually live: in the service's memory, in a database, in a cache such as Redis, or in an event log?
- Both FlatBuffers and Protobuf are named. What decides which one a given interface uses, and how are schema changes rolled out across services?
- What do the integration services speak to external systems - FIX, a vendor API, something proprietary?
- How is a deploy done when a service is holding live trading state?
- What does the shared service framework give you, and where does it get in the way?
- What is the test story: what can be tested without a counterparty, and what needs an integrated environment?
- What currently causes the most production incidents on this service?
- What would a successful first three months look like for someone joining without previous trading-domain experience?

## 08 References

### Repository Sources

- [2022-2024. Integrated Library System](<../../2022-2024. Integrated Library System.md>) - the primary evidence: production Linux C, distributed multi-language services, cross-component diagnosis.
- [2021. Oil and Gas Corporate System](<../../2021. Oil and Gas Corporate System.md>) - C++17 with a relational database and correctness across layers.
- [2025-2026. Embedded SIP Desk Phone Platform](<../../2025-2026. Embedded SIP Desk Phone Platform.md>) - asynchronous state, failover, state ownership and Windows tooling.
- [2020-2021. Independent C++ Engineering Projects](<../../2020-2021. Independent C++ Engineering Projects.md>) - the C++20 application, CMake, GoogleTest and SQLite.
- [2012-2020. Satellite and UAV Optical Imaging Software](<../../2012-2020. Satellite and UAV Optical Imaging Software.md>) - algorithms and design against a fixed computation budget.

### Official Technical Reading

- [Protocol Buffers: updating a message type](https://protobuf.dev/programming-guides/proto3/#updating)
- [FlatBuffers: schema evolution and writing a schema](https://flatbuffers.dev/evolution/)
- [C++ reference: concepts library](https://en.cppreference.com/w/cpp/concepts)
- [perf: Linux profiling with performance counters](https://perfwiki.github.io/main/)
- [PostgreSQL: using EXPLAIN](https://www.postgresql.org/docs/current/using-explain.html)

### Domain Reading

- [Tradeweb: request-for-quote trading for corporate bonds](https://www.tradeweb.com/our-markets/institutional/credit/request-for-quote/)
- [Tradeweb: portfolio trading in corporate bonds](https://www.tradeweb.com/newsroom/media-center/insights/blog/portfolio-trading-an-innovative-solution-for-corporate-bond-trading/)
- [MarketAxess: portfolio trading](https://www.marketaxess.com/trade/portfolio-trading)
- [SEC FIMSAC: definition of electronic trading in fixed income](https://www.sec.gov/spotlight/fixed-income-advisory-committee/fimsac-preliminary-recommendation-re-definition-of-electronic-trading.pdf)
