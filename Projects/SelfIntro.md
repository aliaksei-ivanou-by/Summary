# Self Intro

## Self Intro 1 - Project-Based (5 Minutes)

Hi, my name is Aliaksei Ivanou. I am a C++ Software Engineer with more than six years of commercial C and C++ experience, and more than thirteen years across software engineering and R&D.

I can describe my background through the main types of projects I worked on. The first large area was R&D and image processing for satellite and UAV-related optical systems. The stack there was mainly MATLAB and internal data-processing tools. I worked on image stitching, stabilization, debayering, image fusion, compression and quality assessment. The main value of that work was not only writing scripts, but building algorithms that could improve real image data and still be practical under performance and hardware constraints.

The next project type was industrial metrology and 3D reconstruction. The stack was C++17, Qt and Point Cloud Library. I worked on a prototype that processed measurement and geometry data and connected algorithmic logic with a desktop application. This was a useful transition from research-style development into commercial C++ application development.

After that, I worked on enterprise C++ software. One project was a corporate system in the oil and gas domain. The stack included modern C++, PostgreSQL and standard production tooling around builds, debugging and delivery. I implemented requested features, fixed defects, refactored complicated code, removed obsolete parts and worked on query and business-logic improvements. The main challenge was to improve maintainability while preserving expected behavior.

Another important project type was legacy-system modernization. The stack was C, Java, Scala, custom protocols, Linux and cloud-based infrastructure. I worked mostly with C code: bug fixing, feature implementation, refactoring and production debugging. The difficult part was that the C layer was only one part of a larger platform, so before changing code I often had to understand surrounding services, logs, deployment behavior and runtime state.

My most recent project was an embedded SIP/IP desk phone platform. The stack included C++17, Qt 5 Widgets, Linphone SDK/liblinphone, embedded Linux built with Yocto, and a Windows provisioning tool in C#/.NET. Linphone is the third-party VoIP/SIP library used by the phone for SIP registration, calls and media handling. I worked mainly on the embedded Qt phone application: application architecture, user-facing telephony behavior, call and account state, contacts, phonebooks, device settings and debugging directly on physical phones.

On that SIP project, I also implemented project-specific primary/backup SIP server behavior in the customized Linphone/liblinphone telephony stack and connected it with the application and provisioning side. In simpler terms, this allowed the phone to recover when the main SIP server was unavailable and keep its runtime state and authentication behavior consistent. In addition, I finished and built out most of the practical functionality in the Windows Autoprovisioning Manager: device discovery, account setup, backup-server settings, HA1 authentication support, provisioning generation, saved project handling, network-based provisioning workflows, diagnostics, localization and installer packaging. HA1 is a SIP Digest password hash calculated from the username, SIP authentication realm and password, so it has to match the actual SIP server configuration.

From a working-style point of view, I usually bring the most value when the task is ambiguous at the beginning. I like to reproduce the exact problem, collect logs and state, understand which layer owns the behavior, and only then change code. I am also comfortable with incremental modernization: improving structure, tests or maintainability without turning the work into a risky rewrite.

So the common theme across my projects is complex system work. I am comfortable when the real problem is not isolated to one class or one ticket: old code, runtime behavior, protocols, configuration formats, deployment constraints and user-visible behavior all have to line up. I think my strongest value is careful debugging, practical modernization and C++ development in systems where reliability depends on understanding the whole context.

## Self Intro 2 - Company-Based (5 Minutes)

Hi, my name is Aliaksei Ivanou. I am a C++ Software Engineer with more than six years of commercial C and C++ experience, and more than thirteen years across software engineering and R&D.

I graduated from Belarusian State University, Faculty of Radiophysics and Computer Technologies, in 2012. My first long-term role was at PELENG in Minsk, where I worked for about seven and a half years as a design and research engineer. The stack was mainly MATLAB and internal data-processing tools. I worked on image-processing algorithms for satellite and UAV-related optical systems: image stitching, stabilization, debayering, image fusion, compression and quality assessment. This gave me a strong engineering foundation before I moved fully into commercial C and C++ development.

My first commercial C++ role after that transition was at RIFTEK from March 2020 to May 2020. The stack was C++17, Qt and Point Cloud Library. I worked on industrial metrology and 3D reconstruction software, mostly around a prototype that processed real measurement and geometry data. It was a short project, but it helped connect my earlier algorithm and imaging background with commercial C++ application development.

Then I worked at EPAM from May 2020 to August 2024. I worked on two main projects there. The first one was a C++ corporate system for the oil and gas domain, with PostgreSQL and standard production development tooling around it. I implemented requested features, fixed bugs, refactored complicated code, removed obsolete code and worked on query and business-logic improvements. The second project was a large legacy platform with C, Java, Scala, custom protocols, Linux and cloud-based infrastructure. I worked mostly as a C developer, implementing fixes and features and debugging issues where the root cause could be outside the C module itself.

In December 2024, I joined Innowise. My main recent project there ran from May 2025 to August 2026 and was an embedded SIP/IP desk phone platform for Cuman / Digital System Servis. The stack included C++17, Qt 5 Widgets, Linphone SDK/liblinphone, embedded Linux built with Yocto, and a Windows provisioning tool in C#/.NET. Linphone is the third-party VoIP/SIP library used by the phone for SIP registration, calls and media handling. This was a real embedded product, so the work included the device application, telephony behavior, embedded Linux integration, provisioning tooling, runtime configuration and debugging on physical phones.

On that project, my primary role was C++ development for the embedded Qt phone application. I worked with application architecture, user-facing telephony behavior, device settings, integration with the Linphone-based telephony stack, provisioning-related state and on-device debugging. I also implemented project-specific primary/backup SIP server behavior, so the phone could recover when the main SIP server was unavailable. Another large part of my work was the Windows Autoprovisioning Manager: I finished the tool and built out practical workflows for discovering, configuring and provisioning devices, including HA1 authentication support. HA1 is a SIP Digest password hash used by SIP servers to authenticate accounts without storing or distributing the plain password in the same form.

If I look at the progression across roles, it went from analytical R&D work to production C/C++ development, then to legacy modernization and finally to embedded product work. That path is useful because I am not focused only on one narrow layer. I can work with application code, old codebases, runtime behavior, protocols, configuration formats and debugging evidence from the real environment.

Across these companies, the common thread is that I work well in complex systems where behavior depends on several layers at once: C/C++ code, legacy architecture, protocols, configuration, runtime environment and sometimes real hardware. For my next role, I am looking for work where that experience is useful: embedded software, Qt/Linux, networking, modernization or products where careful debugging and compatibility matter.

## Bridges From Self Intro To Project Descriptions

Use these short bridges after either self-intro when the interviewer is ready to discuss projects in more detail.

### From Project-Based Self Intro

The most representative project from that overview is the embedded SIP desk phone platform. It combines several parts of my recent experience: C++ application development, embedded runtime behavior, protocol integration, provisioning and real-device debugging. I can start with a high-level overview of the product and then go into the parts I personally worked on.

Another good example is the legacy platform project. It is useful if you want to discuss old codebases, compatibility, debugging and careful modernization rather than embedded development specifically.

For the R&D side, I can also describe my earlier image-processing work, where the main challenge was turning mathematical and signal-processing ideas into practical software under quality and performance constraints.

### From Company-Based Self Intro

From the Innowise part of my CV, the best project to discuss is the embedded SIP desk phone platform. It was my most recent and most cross-layer project, and it shows how I work with C++, embedded Linux, product constraints and real hardware.

From the EPAM period, I can describe two different types of work. One project is better for modern C++ and enterprise application development. The other one is better for legacy C, cross-language debugging and long-lived production systems.

From the PELENG period, I can describe the R&D and image-processing work if the role needs analytical thinking, algorithmic background or hardware-adjacent engineering experience.

### General Project Opening

When I describe a project in detail, I usually structure it in the same way: first the product context, then the team and my role, then the main subsystems, then the specific parts I implemented, and finally the difficult engineering problems and what I learned from them. For the SIP phone project, that structure works especially well because the value of the work was in keeping several layers consistent, not just writing isolated features.

## Project Deep Dive 1 - Embedded SIP Desk Phone Platform (10 Minutes)

The project I would choose for a detailed discussion is the embedded SIP/IP desk phone platform that I worked on from May 2025 to August 2026. The customer was Cuman / Digital System Servis, and the product was a business desk phone for managed office telephony. In practice, that means a physical phone used in offices, reception areas, meeting rooms or dispatcher-like scenarios, where administrators need to configure many devices in a consistent way.

It was a full device software stack, not one standalone application. The main parts were the C++/Qt phone application on the device, Linphone SDK/liblinphone customized for the product, embedded Linux firmware, startup and runtime services, hardware integration, firmware packaging, a device configuration interface, and a Windows tool used to prepare and distribute phone configuration. Linphone is a third-party VoIP/SIP library that handles SIP accounts, registration, calls and media behavior. My primary area was the embedded Qt application, but in practice I worked across telephony behavior, generated configuration, firmware integration, Windows tooling, runtime setup and physical-device debugging.

The technology choices were mostly inherited. The phone application was already based on Qt 5 Widgets, the firmware was already built with Yocto, and the provisioning tool was already a Windows/.NET desktop tool. My task was not to replatform everything, but to make the product work reliably inside those constraints.

The first major subsystem was the embedded Qt phone application. It was the user-facing part of the phone, but also the coordination layer between user actions, telephony state, configuration files, Linux runtime state, network state and hardware. I worked on application structure, models and controllers for accounts, calls, contacts, phonebooks, settings and runtime state. I also worked on call screens, call controls, call lists, registration state and mapping asynchronous telephony events into stable UI behavior.

A typical example is call handling. When a user starts a call, transfers it, switches to video or enters a conference flow, the visible result depends on several things at the same time: the UI state, selected account, Linphone SDK state, SIP signaling, media negotiation, generated configuration and sometimes network conditions. My work was to make those transitions predictable from the user point of view and easier to reason about in code.

I also worked on contacts, phonebooks, call history, programmable keys and device settings. For contacts, the work included local and remote phonebook behavior, import/export flows, refresh behavior, conflict handling and linkage with call history. For programmable keys, the difficult part was keeping configured key targets, subscription state, server notifications and UI indicators consistent. For settings, I worked around network, display, audio, camera, account, certificate and provisioning-related screens and models. Some parts also touched hardware-related behavior on the target device.

The second major subsystem was customized Linphone SDK/liblinphone integration. One of my strongest implementation areas was primary/backup SIP server behavior. In simple terms, the phone needed to recover if the main SIP server became unavailable, switch to a backup server when required, and later return to the main server when it was available again. This was not just adding one backup-server field. The product had to track which server was active, when a switch happened, when recovery should be attempted, and which authentication data belonged to the currently active server.

That work touched the Linphone SDK layer, the Qt application and the provisioning tool. On the SDK side, it affected account configuration, registration state, active-server state and the API used by the application. On the application side, the account models and UI had to show the same state the phone was actually using. On the provisioning side, generated configuration had to include primary and backup server data in a format the device could apply. A registration failure after switching to backup could be caused by generated config, wrong active-server state, wrong authentication data, routing, transport settings, server behavior or network conditions, so debugging required checking the full path.

The third subsystem was the Windows Autoprovisioning Manager. This was a desktop tool for installers and IT administrators. A foundation already existed, but I finished it and built out most of the practical functionality: device discovery, account setup, backup-server settings, contacts, programmable keys, network settings, configuration generation, archives, network-based delivery of generated files, device querying, diagnostics, localization and installer packaging.

I also worked on migrating and modernizing that tool from an old .NET Framework setup to .NET 8 for Windows. The main problem was saved project compatibility. The UI itself did not need to be redesigned, but saved projects could restore incomplete state when fields were added or changed. That is dangerous for this type of tool because the user may not notice the missing value immediately. The problem can appear later on the phone as a broken account, broken backup-server setting, wrong contact data or incorrect provisioning output.

My fix was to make project save/load more explicit. Instead of relying mainly on generic UI-control serialization, the tool used a clear project-state model, saved it as structured data, protected it in the project file, and restored the UI through explicit gather/restore logic. I also had to keep generated configuration stable: account settings, backup-server fields, HA1 password-hash mode, file names, archive layout and per-device output. HA1 is a SIP Digest hash calculated from username, authentication realm and password; if one of those values is wrong, the generated configuration can look valid but the phone may fail to register. The current internal project scope moved to ten SIP accounts, but I would not present that as a public product specification.

The fourth subsystem was embedded Linux and firmware integration. Yocto was the inherited build system, and it was suitable because the firmware image had to include the board-support stack, Qt runtime, Linphone SDK, media libraries, configuration files, certificates, fonts, sounds, scripts, services and model-specific files. My contribution was using and adapting that platform for application, SDK, runtime-service and image integration: recipes, installed file layouts, service definitions, startup order, packaging and validation on target devices.

Some defects looked like application bugs but were actually runtime or firmware-composition problems. For example, the application could start before a required service was ready, a file could be missing from the image, a path could differ from the development setup, a media device could appear later than expected, or a network setting could apply in the UI but not survive reboot. These cases required reading target logs, checking installed files and services, validating startup order and testing the complete firmware behavior on the phone.

Testing and debugging were mostly scenario-based. I used focused automated tests where they made sense for local logic, but the final validation required physical devices. A typical investigation started from a QA or customer report with logs, firmware version, device model, SIP server configuration and provisioning state. Then I recreated the scenario, built the relevant component or firmware image, deployed it to the phone, reproduced the issue, collected logs and generated configuration, and narrowed the problem down to the failing layer.

The most difficult bugs were cross-layer. One example was registration working on the primary SIP server but failing after switching to backup. Another example was a saved project opening successfully but restoring incomplete state, which later looked like a phone-side configuration bug. Programmable-key issues were similar: the final visible state depended on configuration, account state, server notifications and UI model updates.

So the value of the project was that it required system-level engineering, not isolated feature work. My strongest contributions were the embedded Qt application and state-model work, project-specific primary/backup SIP server behavior, major Autoprovisioning Manager functionality, and debugging across SIP behavior, provisioning, embedded Linux, firmware packaging and real hardware.
