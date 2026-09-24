# Testing Technical Interview Questions and Answers

> Reusable bank covering testing theory, C++ test frameworks, GoogleTest and GoogleMock mechanics, and the questions about process that appear alongside them. Test-related answers tied to one product live in that vacancy's own file; the shared C++ ownership and concurrency answers stay in [C++ Core Questions](<./C++ Core Questions.md>).

**Honest scope for my own claims.** Production experience: GoogleTest with CTest, QtTest run headless on an embedded target, MSTest on a .NET tool, and working alongside separate QA organisations. GoogleMock specifically is prepared knowledge rather than something I have used in production - say that rather than implying otherwise.

# Question Index

## Theory and Levels (TEST-001–TEST-005)

|  |  |  |
|---|---|---|
| [TEST-001. Verification vs validation?](#question-test-001) | [TEST-002. Unit, integration, system and regression testing - what is the actual difference?](#question-test-002) | [TEST-003. Black-box, white-box and gray-box?](#question-test-003) |
| [TEST-004. What belongs in the test pyramid, and what goes wrong when it inverts?](#question-test-004) | [TEST-005. What makes a test good, and what makes one worse than nothing?](#question-test-005) |  |

## C++ Frameworks (TEST-006–TEST-011)

|  |  |  |
|---|---|---|
| [TEST-006. Which C++ test frameworks do you know, and how do you choose?](#question-test-006) | [TEST-007. How do GoogleTest fixtures work, and what must not go in the constructor?](#question-test-007) | [TEST-008. `ASSERT_*` vs `EXPECT_*`?](#question-test-008) |
| [TEST-009. What are parameterized and typed tests?](#question-test-009) | [TEST-010. What is a mock, and how does it differ from a stub or a fake?](#question-test-010) | [TEST-011. How do you declare expectations in GoogleMock?](#question-test-011) |

## Hard Things to Test (TEST-012–TEST-016)

|  |  |  |
|---|---|---|
| [TEST-012. How do you test multithreaded code?](#question-test-012) | [TEST-013. How do you test code that depends on time?](#question-test-013) | [TEST-014. How do you test embedded or hardware-dependent code?](#question-test-014) |
| [TEST-015. How would you introduce tests into a legacy C or C++ codebase with none?](#question-test-015) | [TEST-016. How do tests fit into CI/CD, and what runs where?](#question-test-016) |  |

# 1. Theory and Levels

## Question TEST-001

[↑ Back to question index](#question-index)

### Question TEST-001 — Verification vs validation?

**Short answer**

- Verification asks "did we build the product right" - does it match the specification. Code review, unit tests and static analysis are verification.
- Validation asks "did we build the right product" - does it solve the user's actual problem. Acceptance testing and user feedback are validation.
- A system can pass every verification activity and still fail validation, and that failure is the more expensive one because it is discovered last.

**Details and nuances**

The distinction is worth more than its definition when you are the developer, because verification is the part you control and validation is the part that requires talking to someone. The practical version is: my tests prove the code does what I understood the requirement to mean; they cannot prove I understood the requirement.

That is exactly why a defect report that reads "this number is wrong" deserves a question about what number the user expected before any code is opened - the mismatch is as likely to be in the specification as in the implementation.

[↑ Back to question index](#question-index)

---

## Question TEST-002

[↑ Back to question index](#question-index)

### Question TEST-002 — Unit, integration, system and regression testing - what is the actual difference?

**Short answer**

- Unit tests exercise one component with its collaborators replaced; integration tests exercise the seams between components; system tests exercise the whole product the way it will be deployed.
- Regression testing is not a level at all - it is a purpose. Any of the three becomes regression testing when it is re-run to prove that a change broke nothing.
- The useful distinction is not size but what a failure tells you: a unit failure names the function, a system failure only names the symptom.

**Details and nuances**

That last point is the one worth making in an interview, because it explains why the levels exist rather than restating them. The value of a test is the diagnostic precision of its failure. A unit test that fails points at fifty lines. A system test that fails points at the product. Both are worth having; they cost different amounts to write and pay out differently when they go red.

Integration tests are where most of the disagreement lives, because "integration" means anything from two classes to the whole stack against real infrastructure. When asked, define which you mean before answering.

[↑ Back to question index](#question-index)

---

## Question TEST-003

[↑ Back to question index](#question-index)

### Question TEST-003 — Black-box, white-box and gray-box?

**Short answer**

- Black-box tests against the specification with no knowledge of the implementation; white-box tests with the code in view, targeting branches, paths and boundary conditions; gray-box sits between, using partial internal knowledge such as the database schema or the log format.
- With access to the source, white-box is more efficient at finding defects, because you can see which branch nobody has ever exercised.
- The trap is that white-box tests written from the implementation encode the implementation, so they pass through a rewrite that changes behaviour and fail on a refactor that changes nothing.

**Details and nuances**

The resolution is to derive the cases white-box and write the assertions black-box: use the code to decide *which* inputs matter - the boundary, the empty case, the branch nobody hits - and then assert on the observable contract rather than on internals. That keeps the coverage and loses the brittleness.

**The techniques each one gives you** are worth naming, because "black-box" on its own sounds like "testing without thinking":

*Black-box*: equivalence partitioning (one representative per class of input), boundary-value analysis (the off-by-one lives at `0`, `1`, `n-1`, `n`, and at type limits), decision tables for combinations of conditions, state-transition testing, and pairwise selection when the parameter space is too large to enumerate.

*White-box*: coverage-directed testing. Statement coverage is the weak form; **branch** coverage is the useful minimum; MC/DC is what safety-critical standards require. The value is diagnostic, not a target — coverage tells you what is definitely **untested**, and a number above 80% tells you almost nothing about whether the assertions are any good. Mutation testing is the honest measure: change the code deliberately and see whether any test notices.

*Gray-box*: knowing the schema, the wire format, the cache policy or the log lines lets you assert on an internal effect without depending on internal structure — verifying that a retry actually happened, or that a cache was populated, from the outside.

**Where each fits in practice:** unit tests are usually white- or gray-box because you own the code; integration and end-to-end tests are black-box because they must survive refactoring of everything underneath; acceptance tests are black-box by definition, since they express what the customer asked for.

**The point that matters most for this codebase's kind of work:** coverage tools do not see concurrency, and neither branch coverage nor boundary analysis finds a race or a lock-order inversion. Those need a different family of techniques entirely — stress and soak runs, deterministic scheduling, and sanitizers.

[↑ Back to question index](#question-index)

---

## Question TEST-004

[↑ Back to question index](#question-index)

### Question TEST-004 — What belongs in the test pyramid, and what goes wrong when it inverts?

**Short answer**

- Many fast unit tests at the base, fewer integration tests above, few end-to-end tests at the top - because cost and run time rise with each level while diagnostic precision falls.
- An inverted pyramid - mostly end-to-end - gives a suite that is slow, flaky and uninformative: it tells you something is broken hours later and does not say what.
- The fix is not deleting the end-to-end tests but adding the lower levels, so the slow ones become confirmation rather than the only signal.

**Details and nuances**

Flakiness is the real cost and it compounds. A suite that fails 5% of the time for environmental reasons trains everyone to re-run it, and once a red build is routinely re-run rather than investigated, the suite has stopped working regardless of how much it covers.

That is also the argument for running whatever can run without the full environment in the fast job: not purity, but keeping the signal trustworthy.

[↑ Back to question index](#question-index)

---

## Question TEST-005

[↑ Back to question index](#question-index)

### Question TEST-005 — What makes a test good, and what makes one worse than nothing?

**Short answer**

- A good test is deterministic, independent of other tests, fast enough that nobody avoids running it, and specific enough that its name plus its failure message tell you what broke.
- It asserts one behaviour, and it fails for exactly one reason.
- A test is worse than nothing when it is flaky, when it asserts the implementation instead of the contract, or when it is written to raise coverage - all three cost maintenance and none of them find defects.

**Details and nuances**

Coverage deserves its own sentence, because it is the metric most often misused: coverage measures which lines ran, not which behaviours were checked. A test that calls every function and asserts nothing reports excellent coverage. Treat it as a way of finding code nothing has touched - which is genuinely useful - rather than as a quality target, because the moment it becomes a target people write tests that move it.

The naming point is practical: `TEST(ColumnStats, BlanksAreSkippedNotCountedAsZero)` tells a reader what broke from the CI summary alone; `TEST(ColumnStats, Test3)` requires opening the file.

**Example or evidence boundary**

Production experience: QtTest coverage for asynchronous SIP, media and networking workflows on the embedded platform, run headless on the target device.

[↑ Back to question index](#question-index)

---

# 2. C++ Frameworks

## Question TEST-006

[↑ Back to question index](#question-index)

### Question TEST-006 — Which C++ test frameworks do you know, and how do you choose?

**Short answer**

- GoogleTest is the default for most projects: fixtures, parameterized and typed tests, good failure messages, and GoogleMock in the same package. Catch2 is header-only with a lighter syntax and BDD-style sections. Boost.Test suits projects already on Boost. CppUTest and similar are built for embedded, where the standard library may be constrained.
- Framework-native runners matter when the code is framework-shaped: QtTest understands the Qt event loop and signals, which a generic framework does not.
- Choose by what the project already uses and by what it must run on - a test binary that cannot run on the target is not a test.

**Details and nuances**

The integration with CTest is worth naming separately from the framework: `enable_testing()` plus `gtest_discover_tests` makes each test its own CTest entry, so `ctest --output-on-failure` reports them individually and CI can shard them. Without discovery the whole binary is one pass/fail.

**Example or evidence boundary**

Production experience: GoogleTest wired into CTest on my own C++20 application, QtTest on the embedded platform, and MSTest on the C#/.NET provisioning tool. Catch2 and CppUTest I know but have not shipped with.

[↑ Back to question index](#question-index)

---

## Question TEST-007

[↑ Back to question index](#question-index)

### Question TEST-007 — How do GoogleTest fixtures work, and what must not go in the constructor?

**Short answer**

- A fixture is a class deriving from `::testing::Test`; `TEST_F` creates a **fresh instance for every test**, so state never leaks between tests - that isolation is the whole point.
- `SetUp()` and `TearDown()` run per test; `SetUpTestSuite()` and `TearDownTestSuite()` run once for the whole suite and are for genuinely expensive shared setup.
- Do not put assertions in the constructor - `ASSERT_*` returns from the enclosing function and cannot return from a constructor; do not throw from the destructor; use `SetUp` and `TearDown` for anything that can fail.

**Details and nuances**

```cpp
class SessionTest : public ::testing::Test {
protected:
    void SetUp() override    { session_ = makeSession(); }   // may assert
    void TearDown() override { session_.reset(); }           // may fail safely
    std::unique_ptr<Session> session_;
};

TEST_F(SessionTest, StartsDisconnected) { EXPECT_FALSE(session_->connected()); }
```

Order per test: constructor, `SetUp`, the test body, `TearDown`, destructor. The reason to prefer `SetUp` over the constructor is not style - it is that the constructor cannot report a failure through the framework, so a failed setup there shows up as a confusing crash instead of a failed test.

The shared-suite variants are a trap worth naming: anything they set up is shared, so a test that mutates it breaks the next one and the breakage depends on execution order. Use them for something read-only and expensive, and nothing else.

[↑ Back to question index](#question-index)

---

## Question TEST-008

[↑ Back to question index](#question-index)

### Question TEST-008 — `ASSERT_*` vs `EXPECT_*`?

**Short answer**

- `ASSERT_*` aborts the current test function on failure; `EXPECT_*` records the failure and continues.
- Use `ASSERT` when continuing would be meaningless or unsafe - a null pointer you are about to dereference, a size you are about to index with - and `EXPECT` everywhere else, because one run then reports every broken expectation rather than only the first.
- `ASSERT` returns from the function it is written in, so it does not work in a helper that returns a value, and it does not abort the enclosing test when used inside a subroutine that returns `void` without the caller checking.

**Details and nuances**

That last point is the one that catches people. In a helper function, `ASSERT_EQ` returns from *the helper*, and the test carries on into the state the assert was meant to prevent. The fixes are to check `::testing::Test::HasFatalFailure()` after calling the helper, or to wrap the call in `ASSERT_NO_FATAL_FAILURE(...)`.

Useful beyond the basics: `ASSERT_THROW`, `ASSERT_NO_THROW`, `EXPECT_DEATH` for death tests, `EXPECT_NEAR` for floating point (never `EXPECT_EQ` on a double), and `SCOPED_TRACE` to attach context so a failure inside a loop says which iteration.

[↑ Back to question index](#question-index)

---

## Question TEST-009

[↑ Back to question index](#question-index)

### Question TEST-009 — What are parameterized and typed tests?

**Short answer**

- A parameterized test runs the same body against many values: derive from `TestWithParam<T>`, write `TEST_P`, and register the values with `INSTANTIATE_TEST_SUITE_P`.
- A typed test runs the same body against many types - the way to check that every implementation of an interface, or every container, satisfies the same contract.
- The point of both is that each case is reported separately, so a failure names the value or type that broke rather than the loop.

**Details and nuances**

```cpp
class ParsePrice : public ::testing::TestWithParam<std::pair<std::string, double>> {};

TEST_P(ParsePrice, RoundTrips) {
    const auto& [text, expected] = GetParam();
    EXPECT_DOUBLE_EQ(parse(text), expected);
}

INSTANTIATE_TEST_SUITE_P(Valid, ParsePrice,
    ::testing::Values(std::pair{"1.00", 1.0}, std::pair{"0", 0.0}));
```

Generators: `Values`, `ValuesIn` for a container, `Range`, `Bool`, and `Combine` for the cartesian product - which is how a matrix of options gets covered without writing the matrix by hand.

This is the mechanism behind data-driven testing: the test logic is written once and the cases live in data, so adding a case is adding a row rather than writing code. When the cases come from a file, keep the file next to the test and make a malformed row a test failure rather than a silent skip.

[↑ Back to question index](#question-index)

---

## Question TEST-010

[↑ Back to question index](#question-index)

### Question TEST-010 — What is a mock, and how does it differ from a stub or a fake?

**Short answer**

- A **stub** returns canned answers so the code under test can proceed; a **fake** is a working but simplified implementation, such as an in-memory store; a **mock** additionally records and verifies the interaction - which methods were called, how often, with what.
- The distinction that matters is what you assert on: stubs and fakes support asserting on the *result*, mocks support asserting on the *conversation*.
- Prefer asserting on results. A test that verifies the exact sequence of calls is coupled to the implementation, so it fails on a refactor that changed nothing observable.

**Details and nuances**

Mocks earn their place where the interaction *is* the behaviour and there is no result to inspect: that a retry happened exactly three times, that a resource was released on the error path, that a notification was sent once and not twice. For everything else, a fake gives a less brittle test.

The structural prerequisite in C++ is a seam: mocking requires an interface - a base class with virtual functions, or a template parameter - so code written against a concrete type cannot be mocked without being changed first. That is usually the real obstacle in legacy code, and it is why [TEST-015](#question-test-015) starts where it does.

**Example or evidence boundary**

Prepared knowledge for GoogleMock specifically. What is production experience is the design side: building seams so a component can be exercised without the thing behind it, which is what made headless on-target testing of SIP flows possible at all.

[↑ Back to question index](#question-index)

---

## Question TEST-011

[↑ Back to question index](#question-index)

### Question TEST-011 — How do you declare expectations in GoogleMock?

**Short answer**

- Declare mock methods with `MOCK_METHOD(ReturnType, name, (args), (const, override));` - the modern form; the numbered `MOCK_METHOD2` macros are obsolete.
- Set expectations with `EXPECT_CALL(mock, method(matcher)).Times(n).WillOnce(Return(v))`, and note that expectations are declared **before** the code under test runs and verified when the mock is destroyed.
- Matchers describe the arguments (`_`, `Eq`, `Gt`, `HasSubstr`, `AllOf`, `Not`), cardinality describes how many calls (`Times`, `AtLeast`, `AtMost`, `Between`, `AnyNumber`), and actions describe what happens (`Return`, `ReturnRef`, `Throw`, `Invoke`).

**Details and nuances**

```cpp
class MockClock : public IClock {
public:
    MOCK_METHOD(std::chrono::steady_clock::time_point, now, (), (const, override));
};

TEST(Watchdog, RetriesThreeTimesThenGivesUp) {
    MockRegistrar reg;
    EXPECT_CALL(reg, attempt(_)).Times(3).WillRepeatedly(Return(false));
    Watchdog(reg).run();            // verified when reg is destroyed
}
```

Two behaviours that surprise people. A call that matches no expectation is an "uninteresting call" - a warning, not a failure - unless the mock is a `StrictMock`, where it fails; a `NiceMock` silences the warning. And matching is **last declared, first matched**, so a general expectation written after a specific one shadows it.

`Times` is inferred when omitted: `WillOnce` implies once, `WillRepeatedly` implies any number. Stating it explicitly when the count is the point of the test makes the intent readable.

[↑ Back to question index](#question-index)

---

# 3. Hard Things to Test

## Question TEST-012

[↑ Back to question index](#question-index)

### Question TEST-012 — How do you test multithreaded code?

**Short answer**

- Most of it by not testing it concurrently: push the logic into pure functions and the shared state behind one owner, so the part that can be wrong is testable single-threaded and the concurrent part is small enough to reason about.
- For what remains, use tools rather than hope - ThreadSanitizer or Helgrind detect races the test did not happen to hit, which a passing test does not.
- Make the schedule controllable where you can: inject the clock, drive the threads from the test rather than sleeping, and run the race-prone case in a loop so a one-in-fifty interleaving actually appears.

**Details and nuances**

The honest framing is that a concurrency test can demonstrate the presence of a race but almost never its absence, because the scheduler is not under your control and a test that passed a thousand times can fail on different hardware. That is why the sanitizer matters: it reasons about the happens-before relation rather than about whether the bad interleaving occurred this time.

`sleep` as synchronisation deserves naming as an anti-pattern: it makes the test slow when the value is large and flaky when it is small, and it is wrong at both ends. Use a condition variable, a future, or a latch the test controls.

**Example or evidence boundary**

Production experience: QtTest coverage for asynchronous SIP and media workflows, where the events arrive from an SDK on its own threads, and the test's job was to drive a reproducible sequence rather than to hope for one. Valgrind was in the project toolchain; TSan specifically is prepared knowledge.

[↑ Back to question index](#question-index)

---

## Question TEST-013

[↑ Back to question index](#question-index)

### Question TEST-013 — How do you test code that depends on time?

**Short answer**

- Never call `now()` directly in the code under test. Take a clock as a dependency - an interface, or a template parameter - and inject a fake one in tests.
- Then timeouts, retries, expiry and throttling become ordinary deterministic tests: advance the fake clock and assert, with no waiting and no flakiness.
- The same argument applies to anything else ambient: random numbers, the filesystem, the environment, the network.

**Details and nuances**

```cpp
struct FakeClock {
    std::chrono::steady_clock::time_point t{};
    auto now() const { return t; }
    void advance(std::chrono::milliseconds d) { t += d; }
};

TEST(Watchdog, FiresAfterTimeout) {
    FakeClock clock;
    Watchdog<FakeClock> w(clock, std::chrono::seconds(30));
    clock.advance(std::chrono::seconds(29));
    EXPECT_FALSE(w.expired());
    clock.advance(std::chrono::seconds(2));
    EXPECT_TRUE(w.expired());
}
```

A thirty-second timeout becomes a microsecond test, and the boundary either side of it is checkable, which a real-time test could never do.

The design consequence is worth stating out loud because interviewers are listening for it: making time injectable is a testability requirement that improves the production code too, since the dependency on the clock becomes visible in the type rather than hidden in a function body.

[↑ Back to question index](#question-index)

---

## Question TEST-014

[↑ Back to question index](#question-index)

### Question TEST-014 — How do you test embedded or hardware-dependent code?

**Short answer**

- Split it: everything above the hardware boundary is ordinary code and is tested on the development machine, fast, in CI; only what genuinely touches the device runs on the device.
- Put an interface at that boundary so the logic can be exercised against a fake, and keep the real implementation thin enough that little logic hides in it.
- Then run a smaller suite on the target itself, headless, because cross-compilation, timing, alignment and resource limits differ from the host - and those differences are exactly what host tests cannot see.

**Details and nuances**

The reason the on-target part cannot be skipped is that the interesting failures live in the differences: a different toolchain, different alignment behaviour, a slower CPU that turns a latent race into a real one, and memory limits that make an allocation fail where it never would on a workstation. A test suite that only ever ran on the build machine has proven the logic and not the product.

Practically: deploy the test binary with the firmware, run it over SSH or through the target's own runner, and collect the result in CI. Keep that suite small - it is slow and it needs hardware - and let the host suite carry the volume.

**Example or evidence boundary**

Production experience: QtTest unit and integration coverage running headless directly on the embedded ARM64 target, so hardware-dependent behaviour was verified where it actually runs, alongside scenario validation on the physical phones.

[↑ Back to question index](#question-index)

---

## Question TEST-015

[↑ Back to question index](#question-index)

### Question TEST-015 — How would you introduce tests into a legacy C or C++ codebase with none?

**Short answer**

- Not by aiming at coverage. Start where a defect was just found or a change is about to be made - write the test that would have caught it, fix it, and keep going. The suite grows along the path of actual risk.
- The obstacle is almost never the framework, it is the absence of seams: code calls concrete types, touches globals and does I/O inline, so there is nothing to substitute. Introducing a seam is the first refactor, and it must be behaviour-preserving.
- Characterisation tests come before refactoring: write tests that record what the code *currently* does, including behaviour that looks wrong, so the refactor has something to check against.

**Details and nuances**

The order matters and is the part interviewers are testing: you cannot safely refactor untested code, and you often cannot test it without refactoring. Characterisation tests break that deadlock - they assert current behaviour rather than correct behaviour, which is enough to make the next change safe.

Cheap early wins: pure functions that already exist and simply were never called from a test; a build target for the test binary so running tests is one command; and CI running that binary from day one, because a suite nobody runs decays within weeks.

On C89 specifically, the seam is a function pointer or a linker-level substitution rather than a virtual function, and the `#include` of a `.c` file into the test translation unit - ugly but standard practice - is sometimes the only way to reach a `static` function.

**Example or evidence boundary**

Adjacent experience: two years in a decades-old C89 platform under strict backward-compatibility constraints, where simplifying inherited logic had to be provably behaviour-preserving. I did not lead a test-introduction programme there; the discipline described is the one I worked to.

[↑ Back to question index](#question-index)

---

## Question TEST-016

[↑ Back to question index](#question-index)

### Question TEST-016 — How do tests fit into CI/CD, and what runs where?

**Short answer**

- Layer by cost: unit tests on every commit for fast feedback, integration tests on merge, the slow and environment-dependent suites nightly, and a smoke check after deployment.
- The rule that makes it work is that the fast job must stay fast and trustworthy - if it takes twenty minutes or fails randomly, people stop reading it and the whole pipeline is decoration.
- A failing pipeline has to block something. A red build that everyone merges past is worse than no build, because it costs the same and signals nothing.

**Details and nuances**

Practically, on a C++ project: build and unit tests per commit; a sanitizer build (ASan, then TSan as a separate job since they cannot be combined) on a schedule; static analysis such as Clang-Tidy or Cppcheck as its own job so a style finding does not block a functional one; and artefacts plus test reports published so a failure can be diagnosed without reproducing it locally.

Flaky tests need an explicit policy - quarantine and fix, with a deadline - because the alternative is that the team develops a habit of re-running, and that habit does not distinguish flaky from real.

**Example or evidence boundary**

Production experience: Jenkins building on AWS Linux with nightly and on-demand deployment to separate developer and QA machines on the library platform, and GitLab CI on the embedded platform. I used and troubleshot this infrastructure; I did not own the pipeline design.

[↑ Back to question index](#question-index)
