# Containers and Orchestration Technical Interview Questions and Answers

> Reusable bank for Docker and Kubernetes as they come up in a C++ role: building and shipping a native binary in a container, debugging inside one, and the Kubernetes objects a developer is expected to name. Build-system detail lives in [Build Systems Questions](<./Build Systems Questions.md>); process, filesystem and log questions live in [Linux and Shell Questions](<./Linux and Shell Questions.md>).

**Honest scope for my own claims.** Docker appears in project toolchains I have worked in; I have not owned a containerised delivery pipeline, and I have no production Kubernetes experience. Everything here is prepared knowledge unless a boundary note says otherwise - present it that way rather than letting fluency imply ownership.

# Question Index

## Docker Fundamentals (CNT-001–CNT-005)

- [CNT-001. Image vs container vs layer?](#question-cnt-001)
- [CNT-002. What is a container actually, at the kernel level?](#question-cnt-002)
- [CNT-003. `ENTRYPOINT` vs `CMD`?](#question-cnt-003)
- [CNT-004. How does layer caching work, and how do you order a Dockerfile for it?](#question-cnt-004)
- [CNT-005. Bind mounts vs volumes?](#question-cnt-005)

## Docker for C++ (CNT-006–CNT-009)

- [CNT-006. What does a multi-stage build give a C++ project?](#question-cnt-006)
- [CNT-007. How do you get a small and still-working C++ image?](#question-cnt-007)
- [CNT-008. How do you debug a C++ application inside a container?](#question-cnt-008)
- [CNT-009. How should a containerised application log and be configured?](#question-cnt-009)

## Kubernetes (CNT-010–CNT-012)

- [CNT-010. Pod, Deployment, Service, ConfigMap, Secret - what is each for?](#question-cnt-010)
- [CNT-011. Liveness, readiness and startup probes?](#question-cnt-011)
- [CNT-012. How does a rolling update work, and how do you roll back?](#question-cnt-012)

---

# 1. Docker Fundamentals

## Question CNT-001

[↑ Back to question index](#question-index)

### Question CNT-001 — Image vs container vs layer?

**Short answer**

- An image is a read-only template: a stack of filesystem layers plus metadata such as the entrypoint and environment. It does not run.
- A container is a running (or stopped) instance of an image with a thin writable layer on top; anything written there is lost when the container is removed unless it went to a volume.
- Each Dockerfile instruction produces a layer, layers are content-addressed and shared between images, which is why pulling a second image based on the same base is nearly free.

**Details and nuances**

The consequence that catches people is that a layer is immutable, so deleting a file in a later layer does not reclaim its space - the data is still in the earlier layer and still in the image. `RUN apt-get install ... && rm -rf /var/lib/apt/lists/*` has to be one instruction for exactly this reason; splitting it into two leaves the cache in the image.

The same property is a security issue: a secret copied in and deleted later is still extractable from the image history, which is why build secrets need `--secret` or a multi-stage boundary rather than a `COPY` followed by a `RUN rm`.

[↑ Back to question index](#question-index)

---

## Question CNT-002

[↑ Back to question index](#question-index)

### Question CNT-002 — What is a container actually, at the kernel level?

**Short answer**

- A normal Linux process with a restricted view: **namespaces** isolate what it can see (PIDs, mounts, network, users, hostname, IPC) and **cgroups** limit what it can consume (CPU, memory, I/O).
- There is no virtual machine and no guest kernel - the container shares the host kernel, which is why containers start in milliseconds and why a Linux container cannot run on a Windows kernel without a Linux VM underneath.
- Capabilities and seccomp then reduce what the process may ask the kernel to do, which is the part that matters when debugging tools stop working inside a container.

**Details and nuances**

Two practical consequences follow directly. Because the kernel is shared, a container is a weaker isolation boundary than a VM - a kernel vulnerability is a host vulnerability - which is the reason for non-root users, dropped capabilities and image scanning.

And because `ptrace` is blocked by the default seccomp profile, `gdb` and `strace` do not work inside a container until it is run with `--cap-add=SYS_PTRACE` and a relaxed profile. That is [CNT-008](#question-cnt-008), and it is a question with a specific answer rather than a vague one.

[↑ Back to question index](#question-index)

---

## Question CNT-003

[↑ Back to question index](#question-index)

### Question CNT-003 — `ENTRYPOINT` vs `CMD`?

**Short answer**

- `ENTRYPOINT` is the command the container always runs; `CMD` supplies default arguments that anything after `docker run image` replaces.
- With `ENTRYPOINT ["./app"]` and `CMD ["--help"]`, running the image with `--version` runs `./app --version`. With only `CMD`, the whole command is replaced instead.
- Use the exec form - a JSON array - not the shell form, because the shell form wraps the process in `/bin/sh -c`, which becomes PID 1 and does not forward signals to your program.

**Details and nuances**

The signal point is the one that matters in production: PID 1 in a container does not get the default signal handling an ordinary process gets, so a `SIGTERM` sent on `docker stop` or by Kubernetes reaches `sh`, which ignores it, and ten seconds later the container is killed hard. The application never ran its shutdown path.

Fixes: exec form so the application is PID 1 and handles `SIGTERM` itself, or an init such as `--init` or `tini` when the process needs to reap children as well.

[↑ Back to question index](#question-index)

---

## Question CNT-004

[↑ Back to question index](#question-index)

### Question CNT-004 — How does layer caching work, and how do you order a Dockerfile for it?

**Short answer**

- Each instruction's result is cached, and the cache is valid only while that instruction and everything before it are unchanged - so one changed line invalidates every layer after it.
- Therefore order from least to most volatile: base image, then system packages, then dependency manifests and dependency installation, then the source code, then the build.
- The single highest-value move on a C++ project is copying the dependency description alone, resolving dependencies, and only then copying the sources.

**Details and nuances**

```dockerfile
FROM ubuntu:22.04
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential cmake ninja-build \
    && rm -rf /var/lib/apt/lists/*      # one layer, cache removed in it

COPY vcpkg.json /app/                   # dependency description only
WORKDIR /app
RUN vcpkg install                       # cached until vcpkg.json changes

COPY . /app                             # volatile: invalidates from here down
RUN cmake -S . -B build -G Ninja && cmake --build build
```

`COPY . /app` placed early is the classic mistake: every source edit invalidates the package installation, and a thirty-second build becomes a ten-minute one.

`.dockerignore` belongs in the same answer - without it, `COPY . .` sends the build directory, the `.git` history and any local artefacts into the build context, which is both slow and a way to leak things into an image.

[↑ Back to question index](#question-index)

---

## Question CNT-005

[↑ Back to question index](#question-index)

### Question CNT-005 — Bind mounts vs volumes?

**Short answer**

- A bind mount maps a host path into the container, so the host filesystem is the source of truth - ideal in development, where you want to edit code locally and see it inside immediately.
- A volume is managed by Docker, lives outside any container's lifetime, and is the right answer for data that must survive the container - a database, uploads, state.
- Neither belongs in a production image's source path: in production the image is the source of truth and the code is `COPY`ed in, so the container runs standalone.

**Details and nuances**

The development-versus-production distinction is the point of the question. A bind mount in production means the machine must have the right files in the right place, which is precisely the reproducibility the container was adopted to get rid of.

One practical trap: a bind mount over a directory hides whatever the image had there. Mounting a source tree over `/app` in a Node or C++ project can shadow dependencies installed during the build, which produces a confusing "it worked in the image and not in the container".

[↑ Back to question index](#question-index)

---

# 2. Docker for C++

## Question CNT-006

[↑ Back to question index](#question-index)

### Question CNT-006 — What does a multi-stage build give a C++ project?

**Short answer**

- One Dockerfile with several `FROM` stages: the first has the compiler, headers and build tools; the last copies only the produced binary and its runtime dependencies.
- For a compiled language the saving is large - a toolchain image is hundreds of megabytes and the artefact is a few - and the security saving matters as much, since a compiler in production is attack surface with no purpose.
- `COPY --from=builder /app/myapp /usr/local/bin/` is the whole mechanism.

**Details and nuances**

```dockerfile
FROM ubuntu:22.04 AS builder
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential cmake ninja-build && rm -rf /var/lib/apt/lists/*
COPY . /src
RUN cmake -S /src -B /build -G Ninja -DCMAKE_BUILD_TYPE=Release \
 && cmake --build /build

FROM debian:bookworm-slim
COPY --from=builder /build/myapp /usr/local/bin/myapp
USER 1000:1000
ENTRYPOINT ["/usr/local/bin/myapp"]
```

The C++-specific catch is dynamic linking: the binary still needs its shared libraries, and the runtime image may not have them. `ldd` on the artefact tells you what is missing; the options are to install those runtime packages in the final stage, link statically, or keep the base images compatible so the ABI matches. Building on Ubuntu and running on Alpine is the classic failure, because Alpine uses musl rather than glibc and the binary simply will not start.

A second stage can also run the tests - build, test, then a third stage that ships only what passed.

[↑ Back to question index](#question-index)

---

## Question CNT-007

[↑ Back to question index](#question-index)

### Question CNT-007 — How do you get a small and still-working C++ image?

**Short answer**

- Multi-stage build first, since that is where most of the size is; then a slim runtime base - `debian:bookworm-slim` or a distroless image - and `--no-install-recommends` with the package cache removed in the same layer.
- Alpine is smaller but uses musl, so a glibc-built binary will not run and anything relying on glibc behaviour needs testing rather than assuming.
- `FROM scratch` works only for a fully static binary, which for C++ means static libstdc++ and libgcc and accepting the constraints that brings.

**Details and nuances**

The honest trade-off, which is what an interviewer is listening for: shrinking the image is not free. Distroless has no shell, so `docker exec` for a look around is unavailable and diagnosis has to come from logs and from a debug variant of the image. Static linking removes the runtime-dependency problem and gives up the ability to patch a vulnerable library without rebuilding.

The pragmatic default for most C++ services is a slim glibc base matching the build image, plus a separate debug image with the tools in it, rather than chasing the smallest possible number.

[↑ Back to question index](#question-index)

---

## Question CNT-008

[↑ Back to question index](#question-index)

### Question CNT-008 — How do you debug a C++ application inside a container?

**Short answer**

- Build with `-g` and keep an unstripped binary; ship stripped if you must, but keep the symbols somewhere matched to the build.
- `gdb` and `strace` need `ptrace`, which the default seccomp profile blocks: run with `--cap-add=SYS_PTRACE --security-opt seccomp=unconfined`, or attach from the host with `--pid=host`.
- For a crash, get the core out: the host's `/proc/sys/kernel/core_pattern` governs where cores go - it is a host setting, not a container one - and `ulimit -c unlimited` must apply to the process.

**Details and nuances**

The `core_pattern` point is the one that wastes hours, because it looks like a container setting and is not: the kernel is shared, so the pattern the host has configured is what applies, and `RUN ulimit -c unlimited` in a Dockerfile does nothing at all - it affects the build step's shell, not the eventual runtime.

The practical alternatives are gdbserver inside the container with the debugger outside, or a separate debug image with the tools installed that runs the same binary. Either way, the binary analysed and the binary that crashed must be the same build, exactly as in [C-019](<./C Language Questions.md#question-c-019>).

**Example or evidence boundary**

Prepared knowledge for the container specifics. The underlying practice - `gdb`, core dumps, matching symbols to the build, remote debugging onto a target - is production experience from the library platform and the embedded platform.

[↑ Back to question index](#question-index)

---

## Question CNT-009

[↑ Back to question index](#question-index)

### Question CNT-009 — How should a containerised application log and be configured?

**Short answer**

- Log to stdout and stderr, unbuffered or line-buffered, and let the platform collect it - `docker logs` and `kubectl logs` read exactly that, and writing to a file inside a container puts the logs where nobody will look and fills the writable layer.
- Configure through environment variables and mounted files, not baked into the image, so one image runs in every environment - that is the twelve-factor rule and it is what makes promotion between environments meaningful.
- Secrets come from the platform's secret mechanism, never from the image and never from a `docker history`-visible build argument.

**Details and nuances**

The C++ detail worth adding: stdout is block-buffered when it is not a terminal, so logs appear in delayed chunks or vanish entirely when the process crashes. Flush deliberately, use a logging library configured to flush at the right level, or set the stream unbuffered at startup.

Structured logging - one JSON object per line - costs little and makes the difference between logs that can be queried and logs that can only be read, which matters as soon as there is more than one replica.

[↑ Back to question index](#question-index)

---

# 3. Kubernetes

## Question CNT-010

[↑ Back to question index](#question-index)

### Question CNT-010 — Pod, Deployment, Service, ConfigMap, Secret - what is each for?

**Short answer**

- A **Pod** is the smallest schedulable unit: one or more containers sharing a network namespace and volumes. Pods are disposable - they are replaced, not repaired.
- A **Deployment** declares the desired number of Pods from a template and reconciles reality toward it, which is what gives you rolling updates, rollback and scaling.
- A **Service** gives a stable name and virtual IP in front of a changing set of Pods; **ConfigMap** and **Secret** supply configuration and credentials as environment variables or mounted files.

**Details and nuances**

The idea underneath all of it is declarative reconciliation: you describe the desired state, a controller continuously works to make the actual state match. That is why deleting a Pod managed by a Deployment simply produces a new Pod, and why `kubectl apply` of a file is the normal way to change anything.

Worth knowing that a Secret is base64-encoded, not encrypted, unless encryption at rest is configured - so "we use Secrets" is not by itself a security answer.

**Example or evidence boundary**

Prepared knowledge. I have no production Kubernetes experience and would say so rather than describe a deployment I did not run.

[↑ Back to question index](#question-index)

---

## Question CNT-011

[↑ Back to question index](#question-index)

### Question CNT-011 — Liveness, readiness and startup probes?

**Short answer**

- **Readiness** decides whether the Pod receives traffic: failing it removes the Pod from the Service without restarting anything.
- **Liveness** decides whether the container is restarted: failing it kills and restarts the container, so it is for "wedged beyond recovery", not for "busy".
- **Startup** suspends the other two until the application has finished starting, which is how a slow-starting process avoids being killed repeatedly before it is ever ready.

**Details and nuances**

The classic misconfiguration is a liveness probe that checks a dependency: the database goes down, every replica fails liveness, and Kubernetes restarts all of them - turning a degraded system into an outage, and restarting the one component that was not broken. Liveness should test the process itself; dependency health belongs in readiness.

The second is a liveness timeout shorter than a legitimate slow path under load, which produces a restart loop that looks like a crash and is actually a timeout.

[↑ Back to question index](#question-index)

---

## Question CNT-012

[↑ Back to question index](#question-index)

### Question CNT-012 — How does a rolling update work, and how do you roll back?

**Short answer**

- Changing the Pod template makes the Deployment create a new ReplicaSet and shift Pods across gradually, bounded by `maxSurge` (how many extra may exist) and `maxUnavailable` (how many may be missing) - so the service stays up throughout.
- New Pods only take traffic once their readiness probe passes, which is what makes the update safe rather than merely gradual.
- `kubectl rollout undo deployment/<name>` reverts to the previous ReplicaSet; `kubectl rollout status` watches one in progress.

**Details and nuances**

Two requirements that the mechanism assumes and does not enforce. Both versions run simultaneously during the update, so they must tolerate each other - which for a schema change means the migration has to be backward compatible, applied before the new code, rather than shipped with it.

And a rolling update with `maxUnavailable: 0` still needs enough capacity for the surge, so a cluster at its limit will stall mid-update.

Blue/green is the alternative when simultaneity is unacceptable: two complete environments, the Service switched from one to the other, at the cost of double the resources and a less granular rollback.

**Example or evidence boundary**

Prepared knowledge. The adjacent production experience is release discipline rather than orchestration: on the library platform, product releases roughly twice a year with automated nightly deployment to developer and QA machines, where backward compatibility was a hard constraint.

[↑ Back to question index](#question-index)
