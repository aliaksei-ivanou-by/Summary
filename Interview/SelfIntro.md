# Self Intro

## Self Intro 1 - Project-Based (5 Minutes)

Hi, my name is Aliaksei Ivanou. I am a C++ Software Engineer with more than six years of commercial C and C++ experience, and more than thirteen years across software engineering and R&D.

I can describe my background through the main types of projects I worked on. The first large area was R&D and image processing for satellite and UAV-related optical systems. The stack there was mainly MATLAB and internal data-processing tools. I worked on image stitching, stabilization, debayering, image fusion, compression and quality assessment. The main value of that work was not only writing scripts, but building algorithms that could improve real image data and still be practical under performance and hardware constraints.

The next project type was industrial metrology and 3D reconstruction. The stack was C++17, Qt and Point Cloud Library. I worked on a prototype that processed measurement and geometry data and connected algorithmic logic with a desktop application. This was a useful transition from research-style development into commercial C++ application development.

After that, I worked on a system for parsing and analyzing industrial sensor data in the oil and gas domain. C++ was used for the backend processing and business logic, PostgreSQL for data storage, and JavaScript for the frontend. I implemented requested features, maintained JavaScript modules connecting backend data flows with the web UI, fixed defects and refactored complicated code. I also optimized the PostgreSQL schema and queries, reducing search response time from about 1.5 seconds to 300 milliseconds.

Another important project type was legacy-system modernization. The stack was C, Java, Scala, custom protocols, Linux and cloud-based infrastructure. I worked mostly with C code: bug fixing, feature implementation, refactoring and production debugging. The difficult part was that the C layer was only one part of a larger platform, so before changing code I often had to understand surrounding services, logs, deployment behavior and runtime state.

My most recent project was an embedded SIP/IP desk phone platform. The stack included C++17, Qt 5 Widgets, Linphone SDK/liblinphone, embedded Linux built with Yocto, a mediasoup-based conferencing server in JavaScript/Node.js, and a Windows provisioning tool in C#/.NET. Linphone is the third-party VoIP/SIP library used by the phone for SIP registration, calls and media handling. The mediasoup server supported WebRTC video conferencing for the phone's C++ client. I worked mainly on the embedded Qt phone application: application architecture, user-facing telephony behavior, call and account state, contacts, phonebooks, device settings and debugging directly on physical phones.

On that SIP project, I also implemented project-specific primary/backup SIP server behavior in the customized Linphone/liblinphone telephony stack and connected it with the application and provisioning side. In simpler terms, this allowed the phone to recover when the main SIP server was unavailable and keep its runtime state and authentication behavior consistent. In addition, I finished and built out most of the practical functionality in the Windows Autoprovisioning Manager: device discovery, account setup, backup-server settings, HA1 authentication support, provisioning generation, saved project handling, network-based provisioning workflows, diagnostics, localization and installer packaging. HA1 is a SIP Digest password hash calculated from the username, SIP authentication realm and password, so it has to match the actual SIP server configuration.

From a working-style point of view, I usually bring the most value when the task is ambiguous at the beginning. I like to reproduce the exact problem, collect logs and state, understand which layer owns the behavior, and only then change code. I am also comfortable with incremental modernization: improving structure, tests or maintainability without turning the work into a risky rewrite.

So the common theme across my projects is complex system work. I am comfortable when the real problem is not isolated to one class or one ticket: old code, runtime behavior, protocols, configuration formats, deployment constraints and user-visible behavior all have to line up. I think my strongest value is careful debugging, practical modernization and C++ development in systems where reliability depends on understanding the whole context.

## Self Intro 2 - Company-Based (5 Minutes)

Hi, my name is Aliaksei Ivanou. I am a C++ Software Engineer with more than six years of commercial C and C++ experience, and more than thirteen years across software engineering and R&D.

I graduated from Belarusian State University, Faculty of Radiophysics and Computer Technologies, in 2012. My first long-term role was at PELENG in Minsk, where I worked for about seven and a half years as a design and research engineer. The stack was mainly MATLAB and internal data-processing tools. I worked on image-processing algorithms for satellite and UAV-related optical systems: image stitching, stabilization, debayering, image fusion, compression and quality assessment. This gave me a strong engineering foundation before I moved fully into commercial C and C++ development.

My first commercial C++ role after that transition was at RIFTEK from March 2020 to May 2020. The stack was C++17, Qt and Point Cloud Library. I worked on industrial metrology and 3D reconstruction software, mostly around a prototype that processed real measurement and geometry data. It was a short project, but it helped connect my earlier algorithm and imaging background with commercial C++ application development.

Then I worked at EPAM from May 2020 to August 2024. I worked on two main projects there. The first one was a system for parsing and analyzing industrial sensor data in the oil and gas domain, with a C++ backend, PostgreSQL and a JavaScript frontend. I implemented features, maintained frontend integration modules, fixed bugs and refactored existing code. I also optimized the PostgreSQL schema and queries, reducing search response time from about 1.5 seconds to 300 milliseconds. The second project was a digital library platform with C, Java, Scala, custom protocols, Linux and cloud-based infrastructure. I worked mostly as a C developer, implementing fixes and features and debugging issues where the root cause could be outside the C module itself.

In December 2024, I joined Innowise. My main recent project there ran from May 2025 to August 2026 and was an embedded SIP/IP desk phone platform for Cuman / Digital System Servis. The stack included C++17, Qt 5 Widgets, Linphone SDK/liblinphone, embedded Linux built with Yocto, a mediasoup-based conferencing server in JavaScript/Node.js, and a Windows provisioning tool in C#/.NET. Linphone is the third-party VoIP/SIP library used by the phone for SIP registration, calls and media handling. The JavaScript server supported WebRTC video conferencing with the C++ phone client. This was a real embedded product, so the work included the device application, telephony behavior, embedded Linux integration, provisioning tooling, runtime configuration and debugging on physical phones.

On that project, my primary role was C++ development for the embedded Qt phone application. I worked with application architecture, user-facing telephony behavior, device settings, integration with the Linphone-based telephony stack, provisioning-related state and on-device debugging. I also implemented project-specific primary/backup SIP server behavior, so the phone could recover when the main SIP server was unavailable. Another large part of my work was the Windows Autoprovisioning Manager: I finished the tool and built out practical workflows for discovering, configuring and provisioning devices, including HA1 authentication support. HA1 is a SIP Digest password hash used by SIP servers to authenticate accounts without storing or distributing the plain password in the same form.

If I look at the progression across roles, it went from analytical R&D work to production C/C++ development, then to legacy modernization and finally to embedded product work. That path is useful because I am not focused only on one narrow layer. I can work with application code, old codebases, runtime behavior, protocols, configuration formats and debugging evidence from the real environment.

Across these companies, the common thread is that I work well in complex systems where behavior depends on several layers at once: C/C++ code, legacy architecture, protocols, configuration, runtime environment and sometimes real hardware. For my next role, I am looking for work where that experience is useful: embedded software, Qt/Linux, networking, modernization or products where careful debugging and compatibility matter.

## Self Intro 3 - C++ & JavaScript / Excel Add-In Position

Hi, my name is Aliaksei Ivanou. I am a **C++ Software Engineer** with more than **six years** of commercial C and C++ experience, plus an earlier background in engineering and Research & Development. My focus is **data processing**, application **reliability** and **responsive user interfaces**.

I previously worked at **EPAM** for over four years. The project most relevant to this role involved parsing and analyzing **industrial sensor data** for the **oil and gas industry**. The **backend was C++**, with PostgreSQL, and **JavaScript** was used on the **frontend**. I extended backend features, maintained frontend integration and improved search-related database queries. My work supported the workflow from processing incoming measurements to **searching and reviewing the results** in the web interface. I also fixed defects and **refactored existing code**, keeping the processing and frontend behavior compatible as new features were added.

Most recently, at **Innowise**, I worked on a **C++17 and Qt** application for an **IP desk phone** on embedded Linux. I **refactored call handling** to separate user actions, application state and telephony-library integration. I also implemented automatic **backup-server registration** and **return to the primary server**. This allowed the phone to **restore registration** when the main server became unavailable. Alongside call handling, I worked on account state, contacts and settings, which had to reflect the phone's actual runtime configuration.

For **WebRTC video conferencing** on the same phone, I contributed to part of the **initial server implementation** in **JavaScript/Node.js** and worked on **integrating the C++ client** with the mediasoup-based conferencing service. My client work covered joining conferences, handling participant and media changes, and updating the UI. This involved **multithreading and asynchronous events**, with attention to keeping **application state consistent** during those transitions. When a conference behaved incorrectly, I had to follow the interaction between client state, signaling and media events to locate the failing component.

Another part of that product was a **Windows provisioning application** in **C# and .NET**. I finished and extended workflows for **discovering devices**, configuring accounts and **generating provisioning files**. I also made **saved configurations restore consistently**, so administrators could reopen an existing setup, adjust it and reuse it when preparing phone settings. Diagnostics helped investigate setup issues, and **installer packaging** made the tool ready for administrators to deploy.

My other EPAM project was a **digital library platform** with a **legacy C core** and Java/Scala services. I implemented features and **fixed integration issues**, using Linux logs, **gdb and core dumps** to **trace failures across components** and restore expected behavior. A fix in the C layer had to remain compatible with the surrounding services and existing application workflows.

Earlier, at **RIFTEK**, I developed a **C++/Qt prototype** for **industrial 3D reconstruction**, bringing measurement-data processing and reconstruction algorithms into a desktop application.

Before that, at **PELENG**, I developed **MATLAB algorithms** for **satellite and UAV imagery**, including stitching, stabilization, image fusion and compression. This work supported preparing sensor images for further analysis, balancing useful **image quality** with processing and **hardware constraints**.

Across these projects, I worked with **distributed teams**, reviewed code and helped with **onboarding and mentoring**. I also built **unit and integration tests**. On the phone project, I used QA reports to **reproduce failures**, investigate the cause and **validate fixes on physical devices**, checking the user-visible behavior as well as the local code change.

This opportunity at EPAM interests me because it brings together **C++, JavaScript, performance** and **user-facing software**. **Office COM Add-In** development would be new to me. I would bring practical experience in **optimizing data workflows**, debugging across components and **delivering tested changes**.

### Project Points For Follow-Up

I have worked on projects in oil and gas, telecommunications, digital libraries, industrial metrology and aerospace.

1. **Oil and gas: sensor data analysis at EPAM.** I worked on a system for parsing, processing and analyzing industrial sensor data, with a **C++ backend**, PostgreSQL and a **JavaScript frontend**. I extended backend features, maintained frontend integration, fixed defects and **improved database queries and schema**. My changes supported the workflow from processing incoming measurements to **searching and reviewing the results** through the web interface. The focus was keeping data processing, database access and user-facing behavior consistent as the system evolved.

2. **Telecommunications: embedded SIP/IP desk phone platform at Innowise.** I worked on the **C++17/Qt** application on embedded Linux, separating call controls, UI state and SDK integration. I implemented **primary/backup SIP server behavior** so the phone could restore registration through a backup server and return to the primary server when it recovered. For **WebRTC conferencing**, I contributed to the **initial JavaScript/Node.js server code** and integrated the C++ client with the conferencing service. My client work covered joining conferences, participant and media changes, and consistent UI updates. The server was later reworked, apparently using mediasoup-demo; my server contribution was to the **early implementation**. I used Qt threads, background tasks and queued signals, and validated asynchronous behavior with **QtTest and physical-device scenarios**.

3. **Telecommunications: Windows Autoprovisioning Manager, part of the same platform.** I finished and extended a **C#/WinForms** application and helped migrate it to **.NET 8**. Administrators could discover devices, configure accounts, **generate and distribute provisioning files**, and use diagnostics to investigate setup problems. I also implemented authentication support, localization and installer packaging. By making **project save/load** explicit through a structured state model, I enabled **saved configurations to restore consistently** and be reused when preparing device settings.

4. **Digital libraries: legacy platform modernization at EPAM.** I maintained **C89 modules** in a system that also included Java, Scala and custom protocols. I implemented requested features, refactored existing code and **fixed integration defects** between the C core and surrounding services. The goal was to keep existing library workflows working as components changed and **restore expected behavior** when production issues occurred. I traced failures across Linux systems hosted on AWS and Azure using **logs, gdb and core dumps**.

5. **Industrial metrology: 3D reconstruction prototype at RIFTEK.** I developed an **R&D prototype** using **C++17, Qt and Point Cloud Library**. The prototype combined measurement and geometry processing with reconstruction algorithms in a desktop application.

6. **Aerospace: satellite and UAV image processing at PELENG.** Across several optical-system projects, I developed **MATLAB algorithms** for image stitching, stabilization, debayering, image fusion, compression and quality assessment. These algorithms supported **preparing sensor imagery for further analysis**: combining images, reducing image motion, reconstructing color and compressing image data. The work involved balancing usable **image quality** with processing and **hardware constraints**.

### Relevance To This Position

The strongest match is connecting **C++ processing** with user-facing functionality and **JavaScript components** while handling **asynchronous behavior reliably**. My examples include making processed sensor data available through a web interface, restoring phone registration after server failures, supporting WebRTC conferences and making saved device configurations reusable. These relate to the add-in's need to **process incoming data**, **keep application state consistent** and **remain usable as updates arrive**.

### Vacancy Coverage (Preparation Notes)

Use this table to choose examples for follow-up questions. It is not part of the opening answer.

| Requirement | Evidence to discuss | Scope of the claim |
| --- | --- | --- |
| **Senior C++ development and maintenance** | C++14/17 at EPAM; C++17/Qt architecture and SDK changes at Innowise; C++/Qt/PCL at RIFTEK. | Six-plus years is **combined commercial C/C++ experience**, including the legacy C project. |
| **JavaScript and Node.js** | Personal JavaScript frontend work at EPAM; partial implementation of the initial Node.js conferencing server; C++ phone-client integration. | Hands-on server-side work was an **early, partial contribution**. The server was later reworked, apparently using mediasoup-demo; this does not imply ownership of the later implementation. |
| **Performance, substantial data workloads and scalability** | Database query/schema improvements supporting sensor-data search; numerical processing of images and point clouds. | Explain the affected workflow, the change and the resulting behavior. Specific dataset sizes, update rates and scaling limits are not established by these examples. |
| **Multithreading, asynchronous updates and responsiveness** | Qt threads, QtConcurrent/background tasks, queued connections, SIP/WebRTC callbacks and recovery. | These demonstrate concurrency and runtime-state handling. They do not establish a specific high-frequency financial-data rate. |
| **Windows usability and stability** | C#/.NET provisioning tool, background operations, configuration, diagnostics and consistent project save/load. | Windows desktop product experience; Office-hosted C++ integration is a separate area. |
| **Debugging, profiling and quality assurance** | gdb/gdbserver, logs, core dumps, QtTest unit/integration tests, and regression scenarios on physical phones. | Strong debugging and testing examples. **Detailed CPU/memory profiling needs a separate case**; debugging tools alone do not demonstrate profiling expertise. |
| **Requirements, stakeholders and distributed teamwork** | EPAM international teams; QA/customer bug reports, reproduction and validation; code reviews, task planning and mentoring. | Prepare one example of clarifying expected behavior and explaining a fix's user impact. Direct client presentations or business ownership are not established by these examples. |
| **Git and CI/CD** | Git/GitLab, build and packaging work; Jenkins and GitLab CI listed in the CV. | Distinguish using build infrastructure from designing pipelines. Phone validation included developer-driven builds and physical-device testing. |
| **Excel COM Add-Ins and Office APIs** | Transferable C++, Windows, concurrency and integration experience. | Direct Office COM Add-In experience is the **main role-specific gap**. The vacancy specifically asks for it; general C++ tenure alone does not demonstrate it. |
| **Finance, VBA and Office.js** | Numerical and industrial data-processing background. | The current experience does not establish these nice-to-have areas; keep them separate from the demonstrated skills. |
| **English B2+** | The current CV lists **English B2**. | Demonstrate technical communication in the conversation; do not silently change the stated level to B2+. |

### Short Follow-Up Answers

**What was your JavaScript and Node.js experience?**

C++ was my main focus, and I also wrote **JavaScript in two contexts**. At EPAM, I **maintained frontend modules** connected to a C++ backend for sensor-data analysis. On the SIP phone project, I contributed to the **initial JavaScript/Node.js server code** for WebRTC video conferencing and worked on the **C++ client's integration** with the conferencing service.

The server was later reworked, and **my understanding** is that the later version used mediasoup-demo. My server-side contribution was to the **early implementation**; my main responsibility on the project remained the **C++ phone application and its conferencing integration**.

**How does your experience relate to Excel COM Add-Ins?**

My relevant experience is in **C++ data processing**, performance optimization, **asynchronous application behavior** and debugging across components. My Windows desktop work was in **C# and .NET**. I have **not developed Excel COM Add-Ins**, so I would need to learn the Office-specific integration and execution model.

**Have you handled the spreadsheet workloads described in this role?**

I have not developed formula-heavy Excel add-ins. My closest examples are **sensor-data analysis**, **image and geometry processing**, and **asynchronous telephony applications**. At EPAM, I worked on the processing, search and frontend integration used to review sensor data. On the phone project, I handled changing call and conference state while **keeping the UI consistent**. Those are the related workflows I can describe in detail.

**How would you investigate a performance problem?**

I would first **reproduce a representative workload** and **measure the user-visible delay**. Then I would separate processing time, database or network waits, and UI work to **locate the bottleneck**. I would change the part supported by the measurements, **repeat the same workload** and **check correctness** as well as response time. My concrete optimization example is the PostgreSQL search; my production-debugging examples involve logs, gdb and core dumps.

**How do you approach testing and delivery?**

On the phone project, I built **QtTest unit and integration tests** for asynchronous application behavior. I also **reproduced reported failures on physical devices** and repeated the relevant scenarios after a fix. My delivery work included component builds, firmware integration and **Windows installer packaging**. Final device validation involved manual steps as well as automation.

**Why are you interested in returning to EPAM?**

I spent **over four years at EPAM** working on production C/C++ systems in international teams. Since then, I have added experience with **application architecture**, **asynchronous product behavior** and **Windows tooling**. This role interests me because it brings those skills together with **data processing and JavaScript integration**, with a clear focus on performance and the user experience.

### Examples To Prepare

- **One personal JavaScript change:** prepare a frontend example from EPAM and identify the part of the initial conferencing server you implemented. Describe the purpose, code and validation you remember; distinguish that early contribution from the later rewrite. The use of mediasoup-demo in the rewrite is your current understanding, not a claim that you implemented that version.
- **The sensor-data workflow:** what input the system handled, what you changed in the backend or database, how the frontend used the results, and which user task the change supported. Explain how you checked that processing, search and presentation worked together.
- **One concurrency bug:** the event sequence, which component or thread owned the state, your fix and the regression scenario. Server recovery or conference participant changes are useful candidates.
- **One communication example:** an incomplete QA/customer report, how expected behavior was clarified, and how the fix and validation were explained to the team.
- **CV consistency:** the PDF calls the EPAM oil-and-gas project "Energy ERP". Explain its sensor-data parsing and analysis purpose consistently as the same project, using the clarified description and keeping the customer unnamed.

### Questions For The Recruiter

- Is **direct Excel COM Add-In experience essential from day one**, or is the team open to a senior C++ engineer with the related experience I described?
- How is the work divided between **C++, browser JavaScript and Node.js**, and where does Node.js run in this product?
- What is the **current priority**: formula/recalculation performance, incoming data updates, stability or new user-facing features?

## Self Intro 3 - C++ и JavaScript / Excel Add-In

Здравствуйте, меня зовут Алексей Иванов. Я **C++-разработчик** с опытом коммерческой разработки на C и C++ **более шести лет**. До этого я занимался инженерной работой, исследованиями и разработками. Мои основные направления: **обработка данных**, **надежность** приложений и **отзывчивые пользовательские интерфейсы**.

Ранее я больше четырех лет работал в **EPAM**. Наиболее близкий к этой вакансии проект был связан с парсингом и анализом **данных промышленных датчиков** для **нефтегазовой отрасли**. **Бэкенд был на C++**, с использованием PostgreSQL, а **JavaScript** применялся на **фронтенде**. Я расширял функциональность бэкенда, поддерживал интеграцию с фронтендом и улучшал запросы к базе данных, связанные с поиском. Моя работа охватывала путь от обработки входящих измерений до **поиска и просмотра результатов** в веб-интерфейсе. Я также исправлял ошибки и **проводил рефакторинг существующего кода**, сохраняя согласованность обработки данных и поведения фронтенда при добавлении новых функций.

Мой последний проект в **Innowise** был связан с приложением на **C++17 и Qt** для **настольного IP-телефона** под embedded Linux. Я **провел рефакторинг обработки вызовов**, разделив действия пользователя, состояние приложения и взаимодействие с библиотекой телефонии. Также я реализовал автоматическую **регистрацию на резервном сервере** и **возврат к основному серверу**. Это позволяло телефону **восстанавливать регистрацию**, когда основной сервер становился недоступен. Помимо обработки вызовов, я работал с состоянием учетных записей, контактами и настройками, которые должны были соответствовать действующей конфигурации телефона.

Для **видеоконференций на WebRTC** на этом же телефоне я участвовал в написании части **первоначальной реализации сервера** на **JavaScript/Node.js** и занимался **интеграцией C++-клиента** с сервисом конференций на базе mediasoup. На клиентской стороне я работал с подключением к конференциям, изменениями участников и медиасостояния, а также обновлением интерфейса. Это включало **многопоточность и асинхронные события**: при таких переходах было важно сохранять **согласованность состояния приложения**. Если конференция работала неправильно, мне приходилось прослеживать взаимодействие состояния клиента, сигнализации и медиасобытий, чтобы найти компонент, в котором возникала проблема.

Другой частью продукта было **Windows-приложение для подготовки конфигураций устройств** на **C# и .NET**. Я доработал и расширил сценарии **обнаружения устройств**, настройки учетных записей и **генерации файлов конфигурации**. Также я обеспечил **корректное восстановление сохраненных конфигураций**, чтобы администраторы могли открыть существующий проект, изменить его и повторно использовать при подготовке настроек телефонов. Диагностика помогала разбирать проблемы настройки, а **подготовка установщика** позволяла администраторам развертывать приложение.

Другой мой проект в EPAM был **платформой цифровой библиотеки** с **legacy-ядром на C** и сервисами на Java/Scala. Я реализовывал функциональность и **исправлял проблемы интеграции**, используя журналы Linux, **gdb и дампы памяти**, чтобы **прослеживать сбои между компонентами** и восстанавливать ожидаемое поведение системы. Изменения в C-части должны были сохранять совместимость с окружающими сервисами и существующими сценариями работы приложения.

Еще раньше, в **RIFTEK**, я разрабатывал **прототип на C++/Qt** для **промышленной 3D-реконструкции**, объединяя обработку измерений и алгоритмы реконструкции в настольном приложении.

До этого, в **PELENG**, я разрабатывал **алгоритмы на MATLAB** для **спутниковых изображений и изображений с БПЛА**, включая сшивку, стабилизацию, совмещение и сжатие. Эта работа помогала готовить изображения с датчиков к дальнейшему анализу, учитывая **качество изображений**, вычислительные затраты и **аппаратные ограничения**.

На этих проектах я работал в **распределенных командах**, проводил ревью кода и помогал с **адаптацией и наставничеством коллег**. Я также разрабатывал **модульные и интеграционные тесты**. На проекте телефона я использовал отчеты QA, чтобы **воспроизводить ошибки**, находить их причины и **проверять исправления на физических устройствах**. При этом я проверял как локальное изменение кода, так и поведение, которое видит пользователь.

Эта позиция в EPAM интересна мне сочетанием **C++, JavaScript, производительности** и **пользовательских приложений**. Разработка **Office COM Add-In** была бы для меня новым направлением. При этом я могу применить свой практический опыт **оптимизации обработки данных**, отладки взаимодействия компонентов и **выпуска проверенных изменений**.

### Проекты для уточняющих вопросов

Я работал над проектами в нефтегазовой отрасли, телекоммуникациях, цифровых библиотеках, промышленной метрологии и аэрокосмической сфере.

1. **Нефтегазовая отрасль: анализ данных датчиков в EPAM.** Я работал над системой парсинга, обработки и анализа данных промышленных датчиков с **бэкендом на C++**, PostgreSQL и **фронтендом на JavaScript**. Я расширял функциональность бэкенда, поддерживал интеграцию с фронтендом, исправлял ошибки и **улучшал запросы и схему базы данных**. Мои изменения поддерживали сценарий от обработки входящих измерений до **поиска и просмотра результатов** через веб-интерфейс. Основная задача заключалась в том, чтобы обработка данных, доступ к базе и пользовательское поведение оставались согласованными по мере развития системы.

2. **Телекоммуникации: платформа настольного SIP/IP-телефона в Innowise.** Я работал над приложением на **C++17/Qt** под embedded Linux, разделяя управление вызовами, состояние интерфейса и интеграцию с SDK. Я реализовал **логику основного и резервного SIP-серверов**, чтобы телефон мог восстанавливать регистрацию через резервный сервер и возвращаться к основному после восстановления его доступности. Для **WebRTC-конференций** я участвовал в написании **первоначального серверного кода на JavaScript/Node.js** и интегрировал C++-клиент с сервисом конференций. На клиенте я работал с подключением к конференциям, изменениями участников и медиасостояния, а также согласованным обновлением интерфейса. Позже сервер переработали, предположительно с использованием mediasoup-demo; мой серверный вклад относился к **ранней реализации**. Я использовал потоки Qt, фоновые задачи и сигналы через очередь, а асинхронное поведение проверял с помощью **QtTest и сценариев на физических устройствах**.

3. **Телекоммуникации: Windows Autoprovisioning Manager, часть той же платформы.** Я доработал и расширил приложение на **C#/WinForms** и участвовал в его переносе на **.NET 8**. Администраторы могли обнаруживать устройства, настраивать учетные записи, **генерировать и доставлять файлы конфигурации**, а также использовать диагностику для разбора проблем настройки. Я также реализовал поддержку аутентификации, локализацию и подготовку установщика. Сделав **сохранение и загрузку проектов** явными через структурированную модель состояния, я обеспечил **корректное восстановление сохраненных конфигураций** и их повторное использование при подготовке настроек устройств.

4. **Цифровые библиотеки: модернизация legacy-платформы в EPAM.** Я поддерживал **модули на C89** в системе, где также использовались Java, Scala и собственные протоколы. Я реализовывал запрошенную функциональность, проводил рефакторинг и **исправлял ошибки интеграции** между C-ядром и окружающими сервисами. Цель состояла в том, чтобы сохранять работоспособность существующих сценариев при изменении компонентов и **восстанавливать ожидаемое поведение** при возникновении проблем в рабочей среде. Я прослеживал сбои в Linux-системах, размещенных в AWS и Azure, используя **журналы, gdb и дампы памяти**.

5. **Промышленная метрология: прототип 3D-реконструкции в RIFTEK.** Я разработал **исследовательский прототип** с использованием **C++17, Qt и Point Cloud Library**. Прототип объединял обработку измерений и геометрических данных с алгоритмами реконструкции в настольном приложении.

6. **Аэрокосмическая сфера: обработка спутниковых изображений и изображений с БПЛА в PELENG.** В нескольких проектах оптических систем я разрабатывал **алгоритмы на MATLAB** для сшивки, стабилизации, дебайеризации, совмещения, сжатия и оценки качества изображений. Эти алгоритмы обеспечивали **подготовку изображений с датчиков к дальнейшему анализу**: объединение изображений, уменьшение их смещения, восстановление цвета и сжатие данных. В работе требовалось учитывать **качество изображений**, вычислительные затраты и **аппаратные ограничения**.

### Связь с этой позицией

Наиболее близкий к вакансии опыт связан с объединением **обработки на C++**, пользовательской функциональности и **компонентов на JavaScript** при **надежной обработке асинхронных событий**. Мои примеры: предоставление обработанных данных датчиков через веб-интерфейс, восстановление регистрации телефона после сбоев серверов, поддержка WebRTC-конференций и повторное использование сохраненных конфигураций устройств. Это связано с задачами надстройки: **обрабатывать входящие данные**, **сохранять согласованность состояния приложения** и **оставаться удобной в работе при поступлении обновлений**.

### Соответствие вакансии: заметки для подготовки

Эта таблица помогает выбрать примеры для уточняющих вопросов. Она не является частью вступительного рассказа.

| Требование | Какие примеры использовать | Что именно подтверждает опыт |
| --- | --- | --- |
| **Разработка и сопровождение на C++ уровня Senior** | C++14/17 в EPAM; архитектура приложения на C++17/Qt и изменения SDK в Innowise; C++/Qt/PCL в RIFTEK. | Более шести лет относятся к **суммарному коммерческому опыту на C и C++**, включая legacy-проект на C. |
| **JavaScript и Node.js** | Личная работа с JavaScript-фронтендом в EPAM; частичная реализация первоначального сервера конференций на Node.js; интеграция C++-клиента телефона. | Практический серверный опыт относится к **части ранней реализации**. Позже сервер переработали, предположительно с использованием mediasoup-demo; это не означает авторство последующей реализации. |
| **Производительность, значительные объемы данных и масштабируемость** | Улучшения запросов и схемы БД для поиска данных датчиков; численная обработка изображений и облаков точек. | Объяснить затронутый сценарий, внесенное изменение и полученное поведение. Эти примеры не подтверждают конкретные объемы данных, частоту обновлений и пределы масштабирования. |
| **Многопоточность, асинхронные обновления и отзывчивость** | Потоки Qt, QtConcurrent и фоновые задачи, соединения через очередь, SIP/WebRTC-колбэки и восстановление после сбоев. | Это примеры параллельной работы и управления состоянием во время выполнения. Они не подтверждают конкретную скорость обработки высокочастотных финансовых данных. |
| **Удобство и стабильность Windows-приложений** | Инструмент конфигурирования на C#/.NET, фоновые операции, настройки, диагностика и корректное сохранение/восстановление проектов. | Опыт разработки настольного Windows-продукта; интеграция C++-компонента внутрь Office остается отдельной областью. |
| **Отладка, профилирование и обеспечение качества** | gdb/gdbserver, журналы, дампы памяти, модульные и интеграционные тесты QtTest, регрессионные сценарии на физических телефонах. | Есть конкретные примеры отладки и тестирования. **Для подробного профилирования CPU и памяти нужен отдельный пример**; сами инструменты отладки не подтверждают опыт профилирования. |
| **Требования, взаимодействие с участниками проекта и распределенная команда** | Международные команды EPAM; отчеты QA и заказчика об ошибках, воспроизведение и проверка исправлений; ревью кода, планирование задач и наставничество. | Подготовить пример уточнения ожидаемого поведения и объяснения влияния исправления на пользователя. Эти примеры сами по себе не подтверждают проведение клиентских презентаций или ответственность за бизнес-решения. |
| **Git и CI/CD** | Git/GitLab, сборка и подготовка пакетов; Jenkins и GitLab CI указаны в CV. | Разделять использование инфраструктуры сборки и проектирование пайплайнов. Проверка телефонов включала сборки, выполняемые разработчиком, и тестирование на физических устройствах. |
| **Excel COM Add-Ins и Office API** | Применимый опыт C++, Windows, многопоточности и интеграции компонентов. | Отсутствие прямого опыта Office COM Add-In является **главным пробелом относительно этой роли**. Вакансия прямо его запрашивает; общий стаж C++ сам по себе этот опыт не подтверждает. |
| **Финансы, VBA и Office.js** | Опыт численной обработки и работы с промышленными данными. | Текущий опыт не подтверждает эти дополнительные направления; их стоит отделять от продемонстрированных навыков. |
| **Английский B2+** | В текущем CV указан **английский B2**. | Показать умение обсуждать технические вопросы на интервью; не менять заявленный уровень на B2+ без оснований. |

### Короткие ответы на уточняющие вопросы

**Какой у вас опыт JavaScript и Node.js?**

C++ был моим основным направлением, при этом я писал **JavaScript в двух контекстах**. В EPAM я **поддерживал модули фронтенда**, связанные с C++-бэкендом для анализа данных датчиков. На проекте SIP-телефона я участвовал в написании **первоначального серверного кода на JavaScript/Node.js** для WebRTC-видеоконференций и работал над **интеграцией C++-клиента** с сервисом конференций.

Позже сервер переработали, и, **насколько я понимаю**, последующая версия использовала mediasoup-demo. Мой серверный вклад относился к **ранней реализации**; основной зоной ответственности на проекте оставалось **C++-приложение телефона и его интеграция с конференциями**.

**Как ваш опыт связан с Excel COM Add-Ins?**

Мой применимый опыт включает **обработку данных на C++**, оптимизацию производительности, **асинхронную работу приложений** и отладку взаимодействия компонентов. Настольные Windows-приложения я разрабатывал на **C# и .NET**. Я **не разрабатывал Excel COM Add-Ins**, поэтому мне потребуется освоить особенности интеграции с Office и его модель выполнения.

**Работали ли вы с нагрузками на электронные таблицы, описанными в вакансии?**

Я не разрабатывал надстройки Excel для таблиц с большим количеством формул. Наиболее близкие примеры из моего опыта: **анализ данных датчиков**, **обработка изображений и геометрических данных**, а также **асинхронные приложения телефонии**. В EPAM я работал с обработкой, поиском и интеграцией фронтенда для просмотра данных датчиков. На проекте телефона я обрабатывал изменения состояния вызовов и конференций, **сохраняя согласованность интерфейса**. Эти связанные сценарии я могу описать подробно.

**Как бы вы исследовали проблему производительности?**

Сначала я бы **воспроизвел характерную нагрузку** и **измерил задержку, которую видит пользователь**. Затем отдельно рассмотрел бы время обработки, ожидание базы данных или сети и работу интерфейса, чтобы **найти узкое место**. Я бы изменил тот участок, на который указывают измерения, **повторил ту же нагрузку** и **проверил корректность**, а также время отклика. Мой конкретный пример оптимизации связан с поиском в PostgreSQL; примеры отладки в рабочей среде включают журналы, gdb и дампы памяти.

**Как вы подходите к тестированию и выпуску изменений?**

На проекте телефона я разрабатывал **модульные и интеграционные тесты на QtTest** для асинхронного поведения приложения. Я также **воспроизводил описанные ошибки на физических устройствах** и повторял соответствующие сценарии после исправления. В подготовку к выпуску входили сборки компонентов, интеграция в прошивку и **подготовка Windows-установщика**. Итоговая проверка устройства включала как автоматизацию, так и ручные действия.

**Почему вы заинтересованы в возвращении в EPAM?**

Я **больше четырех лет работал в EPAM** над промышленными C/C++-системами в международных командах. После этого я получил дополнительный опыт в **архитектуре приложений**, **асинхронной работе продукта** и **Windows-инструментах**. Эта позиция интересна мне тем, что объединяет эти навыки с **обработкой данных и интеграцией JavaScript**, с акцентом на производительность и пользовательский опыт.

### Примеры для подготовки

- **Личное изменение на JavaScript:** подготовить пример с фронтенда EPAM и определить часть первоначального сервера конференций, которую ты реализовал. Описать назначение, код и проверку, которые помнишь; отделить этот ранний вклад от последующей переработки. Использование mediasoup-demo в переработанной версии является твоим текущим пониманием, а не утверждением о твоем авторстве этой версии.
- **Сценарий работы с данными датчиков:** какие входные данные обрабатывала система, что ты изменил в бэкенде или базе, как фронтенд использовал результаты и какую пользовательскую задачу поддерживало изменение. Объяснить, как проверялось совместное поведение обработки, поиска и представления данных.
- **Одна ошибка многопоточности:** последовательность событий, компонент или поток, которому принадлежало состояние, твое исправление и регрессионный сценарий. Можно взять восстановление после сбоя сервера или изменения участников конференции.
- **Один пример коммуникации:** неполный отчет QA или заказчика, уточнение ожидаемого поведения и объяснение исправления и его проверки команде.
- **Согласованность с CV:** в PDF нефтегазовый проект EPAM назван "Energy ERP". Объяснять его назначение через парсинг и анализ данных датчиков как один и тот же проект, используя уточненное описание и не называя заказчика.

### Вопросы рекрутеру

- **Обязателен ли прямой опыт Excel COM Add-In с первого дня**, или команда готова рассматривать senior C++-разработчика с описанным смежным опытом?
- Как распределена работа между **C++, браузерным JavaScript и Node.js**, и где в этом продукте выполняется Node.js?
- Какой сейчас **основной приоритет**: производительность формул и пересчета, обновления входящих данных, стабильность или новая пользовательская функциональность?

## Bridges From Self Intro To Project Descriptions

Use these short bridges after any self-intro when the interviewer is ready to discuss projects in more detail.

### From Project-Based Self Intro

The most representative project from that overview is the embedded SIP desk phone platform. It combines several parts of my recent experience: C++ application development, embedded runtime behavior, protocol integration, provisioning and real-device debugging. I can start with a high-level overview of the product and then go into the parts I personally worked on.

Another good example is the legacy platform project. It is useful if you want to discuss old codebases, compatibility, debugging and careful modernization rather than embedded development specifically.

For the R&D side, I can also describe my earlier image-processing work, where the main challenge was turning mathematical and signal-processing ideas into practical software under quality and performance constraints.

### From Company-Based Self Intro

From the Innowise part of my CV, the best project to discuss is the embedded SIP desk phone platform. It was my most recent and most cross-layer project, and it shows how I work with C++, embedded Linux, product constraints and real hardware.

From the EPAM period, I can describe two different types of work. One project is better for modern C++ and enterprise application development. The other one is better for legacy C, cross-language debugging and long-lived production systems.

From the PELENG period, I can describe the R&D and image-processing work if the role needs analytical thinking, algorithmic background or hardware-adjacent engineering experience.

### From Role-Targeted Self Intro

I can start with the EPAM sensor-data project and explain the C++ backend, the JavaScript frontend integration and the database changes that reduced search response time.

For the C++ and Node.js connection, I can describe my contribution to the initial WebRTC conferencing server and the phone client's integration with the conferencing service. For reliability, I can also walk through the SIP server recovery work.

### General Project Opening

When I describe a project in detail, I usually structure it in the same way: first the product context, then the team and my role, then the main subsystems, then the specific parts I implemented, and finally the difficult engineering problems and what I learned from them. For the SIP phone project, that structure works especially well because the value of the work was in keeping several layers consistent, not just writing isolated features.

## Project Deep Dive 1 - Embedded SIP Desk Phone Platform (10 Minutes)

The project I would choose for a detailed discussion is the embedded SIP/IP desk phone platform that I worked on from May 2025 to August 2026. The customer was Cuman / Digital System Servis, and the product was a business desk phone for managed office telephony. In practice, that means a physical phone used in offices, reception areas, meeting rooms or dispatcher-like scenarios, where administrators need to configure many devices in a consistent way.

It was a full device software stack, not one standalone application. The main parts were the C++/Qt phone application on the device, Linphone SDK/liblinphone customized for the product, embedded Linux firmware, startup and runtime services, hardware integration, firmware packaging, a device configuration interface, and a Windows tool used to prepare and distribute phone configuration. Linphone is a third-party VoIP/SIP library that handles SIP accounts, registration, calls and media behavior. My primary area was the embedded Qt application, but in practice I worked across telephony behavior, generated configuration, firmware integration, Windows tooling, runtime setup and physical-device debugging.

The technology choices were mostly inherited. The phone application was already based on Qt 5 Widgets, the firmware was already built with Yocto, and the provisioning tool was already a Windows/.NET desktop tool. My task was not to replatform everything, but to make the product work reliably inside those constraints.

The first major subsystem was the embedded Qt phone application. It was the user-facing part of the phone, but also the coordination layer between user actions, telephony state, configuration files, Linux runtime state, network state and hardware. I worked on application structure, models and controllers for accounts, calls, contacts, phonebooks, settings and runtime state. I also worked on call screens, call controls, call lists, registration state and mapping asynchronous telephony events into stable UI behavior.

A typical example is call handling. When a user starts a call, transfers it, switches to video or enters a conference flow, the visible result depends on several things at the same time: the UI state, selected account, Linphone SDK state, SIP signaling, media negotiation, generated configuration and sometimes network conditions. My work was to make those transitions predictable from the user point of view and easier to reason about in code.

I also worked on contacts, phonebooks, call history, programmable keys and device settings. For contacts, the work included local and remote phonebook behavior, import/export flows, refresh behavior, conflict handling and linkage with call history. For programmable keys, the difficult part was keeping configured key targets, subscription state, server notifications and UI indicators consistent. For settings, I worked around network, display, audio, camera, account, certificate and provisioning-related screens and models. Some parts also touched hardware-related behavior on the target device.

The WebRTC video-conferencing path connected the native C++/Qt phone client to a mediasoup-based server application written in JavaScript and running on Node.js. I contributed to the initial server code. The server was later reworked, and my understanding is that the later version used mediasoup-demo. My server-side contribution was to the early implementation. My phone-side integration work included joining conferences, handling participant and media events, updating application state and debugging conference behavior on the device. This gave me experience with both early JavaScript server development and C++ client integration.

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
