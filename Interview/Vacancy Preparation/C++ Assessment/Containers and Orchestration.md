# Containers and Orchestration Assessment — Answer Guide

This handbook covers Docker/container fundamentals, production packaging of C++ applications, runtime debugging, security, and Kubernetes basics. Every question has a model answer. Strong answers distinguish image build time, container runtime, kernel isolation, and orchestration behavior.

Labels:

- **[Basic]** — expected foundations.
- **[Deep dive]** — kernel, packaging, security, or lifecycle nuance.
- **[Code]** — Dockerfile/command/debugging exercise.
- **[Design]** — production deployment choice.

## Contents

1. [Container and image fundamentals](#1-container-and-image-fundamentals)
2. [Building C++ container images](#2-building-c-container-images)
3. [Runtime networking, storage, resources, and security](#3-runtime-networking-storage-resources-and-security)
4. [Debugging and operating native applications in containers](#4-debugging-and-operating-native-applications-in-containers)
5. [Kubernetes fundamentals](#5-kubernetes-fundamentals)

---

# 1. Container and image fundamentals

1. **[Basic] What is the difference between an image, a container, and a layer?**

   **Answer.** An image is an immutable, content-addressed filesystem/configuration template. Its filesystem is assembled from ordered read-only layers. A container is a runtime process environment created from an image plus a writable layer and runtime configuration such as namespaces, cgroups, mounts, network, and credentials. Stopping/removing a container does not remove the image, and data in its writable layer is not durable infrastructure by default.

2. **[Deep dive] What is a Linux container at the kernel level?**

   **Answer.** It is one or more ordinary host-kernel processes whose view/access is isolated with namespaces (PID, mount, network, user, IPC, UTS, cgroup, time where supported), resource-governed with cgroups, constrained by credentials/capabilities/seccomp/LSMs, and given a layered root filesystem. Containers share the host kernel; there is no mandatory guest kernel or hardware virtualization boundary.

3. **[Basic] How does a container differ from a virtual machine?**

   **Answer.** A VM virtualizes hardware and runs its own guest kernel, generally providing a stronger isolation boundary and supporting a different OS kernel at greater startup/resource cost. A container isolates processes sharing the host kernel and packages user-space dependencies compactly. Both can be combined: container workloads commonly run inside VMs. The correct security boundary depends on threat model and runtime hardening, not only terminology.

4. **[Deep dive] How do image layers and the writable container layer behave?**

   **Answer.** Snapshot/union filesystems present layers as one tree using copy-on-write. Modifying an image file creates a copy in the writable layer; deleting a lower-layer file creates a whiteout, but bytes remain in the original image layer. Therefore copying a secret and deleting it in a later Dockerfile instruction does not remove it from image history. Large write-heavy data belongs on appropriate volumes/storage, not the copy-on-write layer.

5. **[Basic] What is a container registry?**

   **Answer.** It stores and distributes image manifests/configuration/layers addressed by digest and referenced by tags. A tag such as `latest` is mutable; a digest identifies immutable content. Authenticate/authorize pushes and pulls, scan/sign artifacts, define retention, and deploy by digest or a controlled immutable tag policy. Registry availability and credentials are part of deployment reliability.

6. **[Basic] How do `ENTRYPOINT` and `CMD` differ in a Dockerfile?**

   **Answer.** `ENTRYPOINT` defines the executable; `CMD` supplies default arguments or, without an entrypoint, the default command. Runtime command arguments commonly replace `CMD` while preserving `ENTRYPOINT`; an explicit entrypoint override replaces it. Prefer exec-form JSON (`["/app/server","--foreground"]`) so the process receives signals directly and arguments are not interpreted by an implicit shell.

7. **[Deep dive] What is PID 1 special behavior inside a container?**

   **Answer.** The container's main process is PID 1 in its PID namespace. It must reap orphaned children and has special default signal behavior on Linux; a shell wrapper that fails to `exec` can absorb termination signals and leave the real app running until forced kill. A well-designed C++ service can run directly as PID 1 and handle/reap appropriately, or use a minimal init process when it spawns children.

8. **[Basic] What happens when the main container process exits?**

   **Answer.** The container stops and its exit code becomes container status. Other processes in the container do not turn it into a permanent VM. A restart policy/orchestrator may create/restart it, but in-memory and writable-layer state may be lost. The process should run in the foreground, emit diagnostics before exiting, and return meaningful exit codes rather than daemonizing inside the container.

9. **[Deep dive] Are image layers the same as Dockerfile instructions?**

   **Answer.** Filesystem-changing build instructions commonly produce snapshots/layers, while metadata instructions may change image configuration without a filesystem diff. Modern builders optimize/cache representation and multi-stage images retain only selected final-stage ancestry. The useful model is immutable content-addressed build results, not “one full copy per line.” Inspect the built manifest/history rather than relying on an oversimplified implementation assumption.

---

# 2. Building C++ container images

1. **[Basic] What does a multi-stage build provide for C++?**

   **Answer.** A builder stage contains compilers, headers, package tools, and tests; a runtime stage copies only the executable and required runtime assets/libraries. This reduces image size and attack surface and prevents source/build credentials from entering the final stage. It does not automatically discover dynamic dependencies or licenses—those must be copied/installed deliberately and tested in the clean runtime stage.

2. **[Code] What is a reasonable multi-stage Dockerfile shape?**

   **Answer.** Pin compatible base images, copy dependency manifests/build scripts before frequently changing source to reuse cache, configure/build/test in the builder, then install into a staging prefix and copy that prefix into a minimal runtime with a non-root user. Exact distro packages and build flags belong to the project.

   ```dockerfile
   FROM build-image@sha256:... AS build
   WORKDIR /src
   COPY CMakeLists.txt cmake/ ./
   COPY include/ include/
   COPY src/ src/
   RUN cmake -S . -B out -G Ninja -DCMAKE_BUILD_TYPE=Release \
    && cmake --build out \
    && ctest --test-dir out --output-on-failure \
    && cmake --install out --prefix /stage

   FROM runtime-image@sha256:...
   COPY --from=build /stage/ /usr/local/
   USER 10001:10001
   ENTRYPOINT ["/usr/local/bin/service"]
   ```

3. **[Deep dive] How should Dockerfile instructions be ordered for caching?**

   **Answer.** Put stable dependency/toolchain descriptors before frequently changing application sources, so dependency restoration remains cached when only source changes. Keep build context small with `.dockerignore`. Combine steps only when they form one cache/integrity unit; excessive combining hurts readability/selective reuse. A cache hit is safe only if all effective inputs—base digest, compiler, flags, dependency locks, generator scripts, source—are represented.

4. **[Deep dive] What are BuildKit cache and secret mounts for?**

   **Answer.** Cache mounts persist compiler/package-cache data across builds without copying it into the image layer. Secret/SSH mounts expose credentials to one build step without embedding them in the layer or Dockerfile arguments. Build logs/commands can still leak values if misused. Scope and audit remote caches because they may contain proprietary intermediates and can become a poisoning boundary.

5. **[Basic] Why is `.dockerignore` important?**

   **Answer.** It excludes irrelevant/sensitive files from the build context sent to the builder, improving transfer/cache performance and preventing accidental `COPY` of `.git`, local builds, credentials, dumps, and large test data. It is not a complete secret-control mechanism: explicitly mounted/copied inputs and build arguments still need review. Validate the effective context in CI.

6. **[Code] How do you determine a C++ binary's runtime-library dependencies?**

   **Answer.** Inspect the exact final artifact with platform tools such as `readelf -d`, `objdump -p`, or `ldd` in a trusted environment, and understand loader paths/sonames; on Windows use appropriate PE dependency tools. Run the final image in a clean environment. Do not copy whatever `ldd` prints blindly from an untrusted binary because loader behavior may execute code in some contexts, and license/security policy may require installing packages rather than copying individual files.

7. **[Deep dive] Static or dynamic linking for a containerized C++ application?**

   **Answer.** Static linking simplifies runtime dependency packaging and can enable very small images, but increases binary size, may complicate glibc/NSS/DNS/plugin behavior and licensing, and requires rebuilding for library security fixes. Dynamic linking uses shared runtime packages and loader behavior, easing some updates/plugins but creates ABI/version dependencies. Decide per library/platform and test networking, locales, certificates, time zones, and plugins—not just startup.

8. **[Deep dive] Why can a binary built on one distribution fail in another runtime image?**

   **Answer.** It may require newer glibc/libstdc++ symbol versions, a different C library such as musl, missing loader/sonames, incompatible CPU instructions, absent CA/locales/time-zone files, or different filesystem assumptions. Build against the oldest compatible runtime ABI or use matching build/runtime families, pin toolchains, inspect symbols, and test the final image on supported architecture. “Linux binary” is not one universal ABI contract.

9. **[Design] Distroless, scratch, or a minimal distribution image?**

   **Answer.** `scratch` contains nothing except copied files and works for truly self-contained binaries/assets. Distroless provides selected runtime libraries/certificates without a shell/package manager. A minimal distribution offers familiar package updates and diagnostic tools but larger attack surface. Choose based on runtime dependencies, incident-debug model, policy, and update process; a debug variant/ephemeral tools can preserve operability without shipping tools in production.

10. **[Deep dive] How should multi-architecture images be built and tested?**

    **Answer.** Publish an OCI index/manifest list mapping one tag/digest set to architecture-specific images. Cross-compilation needs correct toolchain/sysroot/dependencies; emulation can build/test but may be slow and hide architecture-specific concurrency/alignment/performance issues. Run native tests on each supported architecture, avoid accidental `-march=native`, and record platform/toolchain provenance per image.

11. **[Design] How should compiler optimization and symbols be packaged?**

    **Answer.** Build production optimization and deterministic build IDs; separate debug information into matching symbol artifacts and strip only the distributed runtime copy according to platform tooling. Retain symbols securely for every released digest and verify symbolization from a captured dump. Do not solve image size by discarding the only usable crash evidence or by deploying a different binary than the one tested.

12. **[Deep dive] How do reproducible builds relate to container images?**

    **Answer.** A pinned container builder controls many tool inputs but timestamps, mutable package repositories/tags, host kernel behavior, nondeterministic compilers/archives, and downloaded content still affect output. Pin base digests and dependency locks/checksums, remove nondeterministic metadata, generate provenance/SBOM, and compare outputs. A reproducible application binary can still be wrapped in non-reproducible image metadata unless both layers are controlled.

---

# 3. Runtime networking, storage, resources, and security

1. **[Basic] How do bind mounts, named volumes, and tmpfs mounts differ?**

   **Answer.** A bind mount exposes a chosen host path, coupling permissions/layout/security to that host. A named volume is managed by the container platform and suited to persistent application data under its backup/driver lifecycle. A tmpfs mount is memory-backed and ephemeral, useful for transient sensitive/high-speed data within resource limits. Mounting over an image path hides its original contents for that container.

2. **[Basic] What does publishing a container port do?**

   **Answer.** It configures host/runtime networking to forward or expose a host address/port to a container port. `EXPOSE` in a Dockerfile is documentation/metadata and does not itself publish a port. Binding the host to `127.0.0.1` limits direct reachability compared with `0.0.0.0`, subject to platform/firewall. The application often must listen on the container interface (`0.0.0.0`/appropriate address), not container loopback only.

3. **[Deep dive] How does container DNS/service discovery work?**

   **Answer.** The runtime supplies network-namespace interfaces/routes and typically an internal DNS resolver that maps service/container names according to network scope. Container IPs are ephemeral and should not be persisted as identity. DNS caching, search domains, IPv4/IPv6, and `/etc/resolv.conf` behavior affect C++ resolvers; define retry/TTL/timeouts and diagnose from the same network namespace.

4. **[Design] How should application configuration enter a container?**

   **Answer.** Keep the image environment-neutral and supply validated configuration through command arguments, environment variables, mounted config files, or an orchestration configuration API. Environment variables are simple but stringly typed and can be exposed in diagnostics; mounted files support structured data and rotation patterns. Define precedence, schema, defaults, reload/restart behavior, and never silently accept misspelled critical options.

5. **[Deep dive] How should secrets be supplied?**

   **Answer.** Use the platform's secret mechanism or an external secret manager with least-privilege identity, preferably mounting/fetching short-lived credentials rather than baking them into images or Git. Prevent leakage in environment dumps, process arguments, logs, metrics, core dumps, and child processes. Rotation must be designed: re-read safely, reconnect, or roll instances. Kubernetes Secret objects are encoding/storage abstractions, not automatically encrypted end-to-end without cluster configuration.

6. **[Basic] What do CPU and memory limits do?**

   **Answer.** Cgroups account and constrain resources. CPU quota generally throttles execution rather than reserving a physical core; CPU shares/weights arbitrate contention. A memory limit bounds charged memory and can lead to allocation failure, reclaim, or OOM kill depending on kernel/runtime behavior. C++ code sees host/container metrics differently across library/kernel versions, so size thread pools/caches from explicit configuration and cgroup-aware measurements.

7. **[Deep dive] What happens during an OOM kill?**

   **Answer.** When a cgroup/system cannot satisfy memory policy, the kernel selects a process to kill; it receives no catchable cleanup opportunity like SIGTERM. The container may exit with a runtime-specific status/reason and be restarted. Inspect cgroup/container/kernel events, peak working set, allocator retention, page cache, and limits. Add headroom and backpressure; relying on catching `std::bad_alloc` does not cover OOM kill.

8. **[Deep dive] How should termination signals be handled?**

   **Answer.** The runtime/orchestrator sends a configurable graceful signal (commonly SIGTERM), waits a grace period, then force-kills remaining processes. Use exec-form entrypoint/`exec` wrappers so the C++ process receives it. The signal handler must remain async-signal-safe—notify normal control flow—then stop admission, cancel/drain within deadline, flush safe state, and join. Test shutdown under load and keep grace periods aligned with application deadlines.

9. **[Design] Why run as non-root and drop capabilities?**

   **Answer.** Container root can have substantial power within namespaces and becomes more dangerous with vulnerable kernels, mounts, sockets, or excessive capabilities. Use a fixed unprivileged UID/GID, writable directories with correct ownership, drop all capabilities then add only required ones, apply seccomp/AppArmor/SELinux, avoid privileged mode/host namespaces, and use read-only root filesystems where practical. User namespaces/rootless runtimes further reduce host impact but have feature constraints.

10. **[Deep dive] What is the danger of mounting the container-engine socket?**

    **Answer.** Access to a Docker/containerd control socket often permits creating privileged containers, mounting host paths, or reading secrets—effectively host/root-level control. Do not mount it merely to inspect siblings. Use a narrowly scoped proxy/API, orchestrator-native metadata, or dedicated controlled builder; isolate CI builders and treat daemon credentials as highly privileged.

11. **[Basic] How should a containerized application log?**

    **Answer.** Write structured operational logs to stdout/stderr so the runtime collects and routes them; include timestamp policy, severity, operation/request IDs, build version, and actionable context without secrets. Do not depend on unbounded files inside the writable layer. The platform needs rotation/backpressure/retention; application logging must be rate-limited/bounded so an error storm does not exhaust CPU, network, or storage.

12. **[Deep dive] Does a container have its own clock?**

    **Answer.** Containers normally share the host kernel clock, although time namespaces can alter some views on supported systems. Host time synchronization, timezone files/environment, and monotonic versus wall-clock APIs still matter. Use `steady_clock` for durations/deadlines and explicit UTC/time-zone handling for timestamps. Do not fix time bugs by assuming the image's `/etc/localtime` defines an independent clock.

---

# 4. Debugging and operating native applications in containers

1. **[Basic] What is the initial diagnostic sequence for a stopped/restarting container?**

   **Answer.** Inspect status, exit code/reason, restart count, logs including the previous instance, runtime configuration/mounts/environment (redacting secrets), health events, and resource/OOM data. Identify the exact image digest and command. Reproduce with the same configuration when safe. An orchestrator restart loop can erase ephemeral evidence, so collect it before repeatedly restarting.

2. **[Basic] What do `logs`, `inspect`, `exec`, and `cp`-style operations provide?**

   **Answer.** Logs retrieve captured stdout/stderr; inspect returns configured/runtime metadata; exec starts another process in a running container's namespaces; copy transfers files across the boundary. Exec cannot enter a stopped container and a minimal image may have no shell/tools. These commands can expose secrets or perturb production, so use scoped read-only diagnostics and platform audit controls.

3. **[Code] How do you debug a C++ process in a minimal container?**

   **Answer.** Prefer a separate debug image/ephemeral debug container containing matching `gdb`, symbols, and tools, sharing the target's process namespace as supported. Attach permission requires ptrace capability/seccomp/Yama policy and should be temporary—not permanent privileged mode. Alternatively capture a core/hang dump and analyze offline with exact binary, libraries, root filesystem, and symbols. Attaching pauses/changes timing.

4. **[Deep dive] What is required for useful core dumps in containers?**

   **Answer.** Configure process `RLIMIT_CORE`, kernel core pattern/collector, writable/storage paths, and orchestrator permissions; know that kernel settings may be host-wide. Preserve exact executable, shared libraries, build ID, architecture, maps, and separate debug files. Dumps contain memory secrets and can be large, so encrypt/control/expire them. Test crash collection before an incident.

5. **[Deep dive] Why might `strace`, `perf`, or packet capture fail in a container?**

   **Answer.** Seccomp, capabilities, ptrace policy, procfs visibility, kernel perf settings, namespace boundaries, and host permissions restrict them. Adding `SYS_PTRACE`, `PERFMON`, `NET_ADMIN`, or privileged mode expands attack surface and may still not provide host-wide context. Grant minimal temporary diagnostic permissions in a controlled environment, or use node/eBPF/observability tooling designed for the platform.

6. **[Code] How do you diagnose a missing shared library at startup?**

   **Answer.** Read the loader error, inspect `DT_NEEDED`, interpreter, runpath, architecture, and symbol versions with `readelf`/`objdump`; check filesystem paths/permissions and loader cache/config inside the final image. `ldd` can be a quick diagnostic for trusted binaries but is not the full loader contract. Verify whether the file exists but exports an incompatible version, and fix packaging/build ABI rather than setting a broad unsafe `LD_LIBRARY_PATH` blindly.

7. **[Deep dive] How do you diagnose a container that works locally but fails in production?**

   **Answer.** Compare image digest/architecture, command/user, environment/config/secrets, mounts/permissions, cgroup limits, kernel/runtime/security policy, network/DNS/TLS, CPU features, and deployment probes. Local bind mounts may hide missing image files; local root/privileged execution may mask permission problems. Produce a configuration/digest diff and reproduce under production-like constraints instead of rebuilding an unidentifiable “same tag.”

8. **[Design] How should image vulnerability findings be handled?**

   **Answer.** Map a finding to the exact installed/embedded component and fix availability, assess reachability/exposure and severity, then rebuild from patched inputs—even if the application code is unchanged. Scanners have false positives/negatives and may miss statically linked or manually copied libraries; combine SBOM/provenance, vendor advisories, binary inspection, and policy. Pinning an old digest aids reproducibility but requires deliberate update automation.

9. **[Deep dive] What makes production container observability different from host-only monitoring?**

   **Answer.** Instances are ephemeral and identities/dimensions change; cgroup resource accounting differs from host totals; network/storage layers add boundaries; restarts lose local state. Emit application metrics/traces/logs with stable service/version and orchestrator metadata while controlling cardinality. Correlate container OOM/throttling/restarts with request behavior and node pressure; CPU “100%” must be interpreted relative to quotas/cores.

---

# 5. Kubernetes fundamentals

1. **[Basic] What are a Pod, Deployment, ReplicaSet, and Service?**

   **Answer.** A Pod is the scheduling/lifecycle unit containing one or more tightly coupled containers sharing network and selected volumes. A ReplicaSet maintains a desired pod count. A Deployment manages ReplicaSets and rolling declarative updates for stateless workloads. A Service provides stable virtual discovery/load balancing to selected ready pods. Pods are replaceable; their names/IPs are not durable identity.

2. **[Basic] What are ConfigMaps and Secrets?**

   **Answer.** They store non-confidential and confidential configuration objects that pods can consume as environment values or mounted files. Updates propagate differently by consumption method and applications may require reload/restart. Secret values are commonly base64-encoded in API representation, not inherently encrypted; cluster encryption, RBAC, external secret management, and audit are required. Avoid broad namespace read access.

3. **[Basic] Distinguish liveness, readiness, and startup probes.**

   **Answer.** Startup indicates a slow-starting application has initialized and suppresses other probes until success. Readiness controls whether the pod receives Service traffic. Liveness decides whether Kubernetes should restart a stuck container. A dependency outage should often make an instance unready but not trigger endless liveness restarts. Probe endpoints must be cheap, bounded, and represent meaningful internal progress.

4. **[Deep dive] What are resource requests and limits?**

   **Answer.** Requests guide scheduling and contribute to guaranteed capacity; limits constrain use according to resource semantics. CPU above a limit is throttled; memory above a limit can produce OOM kill. Request/limit combinations determine QoS class and eviction priority. Set them from measured working sets and load tests, then observe throttling/OOM/latency; overly low limits cause instability while absent requests allow unsafe packing.

5. **[Design] How does a rolling update work?**

   **Answer.** A Deployment creates a new ReplicaSet/pods and gradually scales it up while scaling the old down, constrained by surge/unavailable settings and readiness. During the rollout, versions coexist, so APIs, messages, databases, and configuration must be compatible. Progress deadlines and health metrics should halt failure; Deployment rollback restores a prior pod template but cannot reverse external data effects.

6. **[Deep dive] What happens during pod termination?**

   **Answer.** The pod is marked terminating/endpoints become unready through control-plane propagation, optional pre-stop hooks run, and the container receives its stop signal. After `terminationGracePeriodSeconds`, remaining processes are killed. There are races with in-flight/load-balancer traffic, so applications must stop admission/readiness, handle SIGTERM, drain within a bounded deadline, and make requests retry/idempotency safe. Pre-stop consumes the same grace budget.

7. **[Design] Deployment, StatefulSet, DaemonSet, Job, or CronJob?**

   **Answer.** Deployment fits replaceable stateless replicas. StatefulSet provides ordered stable identities and per-replica volume claims for stateful systems but does not create database correctness/backup. DaemonSet runs a pod per selected node for agents. Job runs work to completion; CronJob schedules Jobs with concurrency/catch-up policies. Select by lifecycle/identity, not because one controller sounds more powerful.

8. **[Basic] How do services discover each other?**

   **Answer.** Kubernetes DNS resolves Service names to a virtual/endpoint mechanism; the Service selects ready pods by labels. Use namespace-qualified names when needed and configure client timeouts, connection refresh, and retries because endpoints change. Headless Services return pod endpoints for client-side/stateful discovery. Do not store pod IPs as durable identities.

9. **[Deep dive] How do persistent volumes and claims relate?**

   **Answer.** A PersistentVolume represents provisioned storage; a PersistentVolumeClaim requests capacity/access characteristics and binds/provisions a volume through a StorageClass. Pod mounts then use the claim. Access modes, topology, filesystem/block semantics, expansion, snapshots, reclaim policy, backup, and multi-attach behavior depend on the driver. Persistence of a volume is not equivalent to application-consistent backup.

10. **[Design] How should a failing Kubernetes workload be diagnosed?**

    **Answer.** Inspect desired versus current controller state, pod events/status/restarts, current and previous logs, probe failures, exit/OOM reasons, resources, configuration/secret mounts, endpoints, DNS/network policies, node conditions, and exact image digest. Use `describe`-style events plus metrics/traces and an ephemeral debug container when appropriate. Do not delete/restart first and erase evidence; first determine whether failure is application, configuration, dependency, scheduler, node, or policy.

11. **[Deep dive] What does horizontal pod autoscaling need to work safely?**

    **Answer.** It needs meaningful per-pod/external metrics, resource requests for utilization-based metrics, bounded min/max, stabilization/cooldowns, and an application that can actually scale horizontally. Scaling reacts after load and new pods take time, so queues/backpressure/headroom remain necessary. Stateful bottlenecks or downstream limits can worsen under added replicas. Load test the control loop and scaling signal.

12. **[Design] What security controls matter for a C++ workload on Kubernetes?**

    **Answer.** Use a dedicated minimally privileged service account/RBAC, non-root user, dropped capabilities, seccomp profile, read-only root filesystem, controlled volumes, network policies, image digest/signature/admission policy, protected secrets, resource limits, and patched minimal images. Avoid privileged/host PID/network/path mounts. Native memory-safety defects make containment valuable, but cluster hardening cannot replace fixing UB and validating untrusted input.

13. **[Deep dive] Why can a sidecar affect shutdown and resource accounting?**

    **Answer.** Containers in a pod share lifecycle/network and compete within pod scheduling/resource configuration; proxies/agents can add latency, memory/CPU, connection draining, and termination ordering requirements. The application may exit while a sidecar retains connections or vice versa. Measure whole-pod behavior, define startup/readiness/shutdown coordination, and do not assume the sidecar is operationally free.

---

# Assessment usage notes

- Ask candidates to trace a binary from builder toolchain through image layers, loader dependencies, runtime limits, termination, and crash diagnostics.
- Reward explicit kernel/runtime/orchestrator boundaries; a container is neither merely a tar file nor a lightweight VM.
- For Kubernetes, focus on lifecycle and failure behavior rather than memorizing every object type.
- Security answers should combine least privilege, immutable provenance, patched dependencies, secret hygiene, and memory-safe C++ practices.
