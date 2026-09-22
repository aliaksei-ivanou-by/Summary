# Live-Coding Scaffold and Worked Exercises

> Purpose: a C++17 project that builds and tests in one command, plus two exercises written in it. Reusable across vacancies. The point is not to memorize the code - it is to have typed it once, so the live-coding round is spent on the problem rather than on remembering `target_link_libraries` syntax.

Verified: configures with CMake 3.28, builds clean under `-Wall -Wextra -Wpedantic` with GCC 13, and all seven tests pass. The offline variant compiles with a single `g++` command.

## 01 Before Writing Any Code

Sixty seconds of questions, every time. Interviewers are grading this part as much as the code, and skipping it is the most common way a good engineer looks junior.

- **Input shape and ownership.** Where does the data come from, who owns it, may I copy it, is it already in memory?
- **Size.** Hundreds, or hundreds of millions? It decides whether the answer is a `vector` or a streaming pass.
- **Error behavior.** Malformed input: throw, return an error, or skip the element? Ask - do not pick silently.
- **Concurrency.** Single-threaded unless told otherwise. Confirm it rather than assuming either way.
- **What "done" means.** A function with tests, or a runnable program?

Then say the approach out loud with its complexity before typing: *"a single pass over the block, O(rows x columns) time and O(columns) extra space - does that match what you had in mind?"* A wrong plan costs thirty seconds to correct now and ten minutes to unwind later.

## 02 The Scaffold

Five files. Out-of-source build, header-only core so there is no library plumbing to debug, GoogleTest fetched by CMake.

```
live_coding/
  CMakeLists.txt
  sheet.hpp
  coalesce.hpp
  tests.cpp
  standalone.cpp      # offline fallback, not part of the CMake build
```

Build and run:

```bash
cmake -S . -B build -DCMAKE_BUILD_TYPE=Debug
cmake --build build -j
cd build && ctest --output-on-failure
```

### CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.14)
project(live_coding LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# Header-only "library" target: carries the include path and the standard to
# everything that links it, so nothing downstream repeats those settings.
add_library(core INTERFACE)
target_include_directories(core INTERFACE ${CMAKE_CURRENT_SOURCE_DIR})
target_compile_features(core INTERFACE cxx_std_17)

enable_testing()

include(FetchContent)
FetchContent_Declare(googletest
    GIT_REPOSITORY https://github.com/google/googletest.git
    GIT_TAG        v1.14.0
)
set(gtest_force_shared_crt ON CACHE BOOL "" FORCE)   # needed on MSVC
FetchContent_MakeAvailable(googletest)

add_executable(tests tests.cpp)
target_link_libraries(tests PRIVATE core GTest::gtest_main)

if(MSVC)
    target_compile_options(tests PRIVATE /W4 /permissive-)
else()
    target_compile_options(tests PRIVATE -Wall -Wextra -Wpedantic)
endif()

include(GoogleTest)
gtest_discover_tests(tests)
```

**What to say if asked about any of it.** `add_library(core INTERFACE)` makes the include path and the language standard properties of a target rather than global state, so anything that links `core` inherits them and nothing downstream repeats them - that is the whole point of target-based CMake. `PRIVATE` on `target_link_libraries` says the dependency is an implementation detail and must not leak to whatever links `tests`; `PUBLIC` would propagate it. `gtest_force_shared_crt` exists because mismatched runtime libraries are the classic MSVC link failure when consuming GoogleTest. `gtest_discover_tests` asks the built binary which tests it contains, so `ctest` reports each one separately instead of one pass/fail for the whole executable.

**If the machine has no network,** `FetchContent` cannot reach GitHub. Do not spend the round fighting it: say so, and fall back to `standalone.cpp`, which needs nothing but a compiler. Having that fallback ready is itself a good signal.

### sheet.hpp - a spreadsheet block as native data

```cpp
#pragma once
#include <cstddef>
#include <optional>
#include <stdexcept>
#include <vector>

namespace sheet {

// A block as it arrives from a spreadsheet: row-major, blanks are empty.
using Cell  = std::optional<double>;
using Block = std::vector<std::vector<Cell>>;

struct ColumnStats {
    std::size_t count = 0;   // non-blank cells
    double      sum   = 0.0;
    std::optional<double> mean;   // empty when the column has no numbers
};

// Rejects a ragged block: a spreadsheet range is always rectangular, so a
// ragged input means the caller built it wrong, not that the sheet was odd.
inline std::size_t columnCount(const Block& b) {
    if (b.empty()) return 0;
    const std::size_t width = b.front().size();
    for (const auto& row : b) {
        if (row.size() != width) throw std::invalid_argument("ragged block");
    }
    return width;
}

inline std::vector<ColumnStats> columnStats(const Block& b) {
    const std::size_t width = columnCount(b);
    std::vector<ColumnStats> out(width);
    for (const auto& row : b) {
        for (std::size_t c = 0; c < width; ++c) {
            if (const Cell& cell = row[c]; cell.has_value()) {
                ++out[c].count;
                out[c].sum += *cell;
            }
        }
    }
    for (auto& s : out) {
        if (s.count > 0) s.mean = s.sum / static_cast<double>(s.count);
    }
    return out;
}

}  // namespace sheet
```

### coalesce.hpp - a latest-value-per-key store

```cpp
#pragma once
#include <algorithm>
#include <cstddef>
#include <mutex>
#include <string>
#include <unordered_map>
#include <unordered_set>
#include <utility>
#include <vector>

namespace feed {

// Latest-value-per-key store. Producers call update() at the feed rate; the
// consumer calls drainChanged() at the display rate and gets only what changed
// since its previous drain. Obsolete ticks are overwritten, never queued, so
// memory is bounded by the number of keys rather than by the update rate.
class CoalescingStore {
public:
    void update(std::string key, double value) {
        std::lock_guard<std::mutex> lock(m_);
        latest_[key] = value;
        dirty_.insert(std::move(key));
    }

    // Sorted by key so the output is deterministic: tests stay simple and a
    // consumer writing contiguous rows gets a stable order.
    std::vector<std::pair<std::string, double>> drainChanged() {
        std::lock_guard<std::mutex> lock(m_);
        std::vector<std::pair<std::string, double>> out;
        out.reserve(dirty_.size());
        for (const auto& key : dirty_) out.emplace_back(key, latest_.at(key));
        dirty_.clear();
        std::sort(out.begin(), out.end(),
                  [](const auto& a, const auto& b) { return a.first < b.first; });
        return out;
    }

    std::size_t size() const {
        std::lock_guard<std::mutex> lock(m_);
        return latest_.size();
    }

private:
    mutable std::mutex m_;
    std::unordered_map<std::string, double> latest_;
    std::unordered_set<std::string>        dirty_;
};

}  // namespace feed
```

### tests.cpp

```cpp
#include <gtest/gtest.h>

#include "coalesce.hpp"
#include "sheet.hpp"

using sheet::Block;
using sheet::Cell;

TEST(ColumnStats, EmptyBlockHasNoColumns) {
    EXPECT_TRUE(sheet::columnStats(Block{}).empty());
}

TEST(ColumnStats, SingleCellIsStillARectangularBlock) {
    // The shape trap: a one-cell selection must be wrapped as 1x1 by the
    // caller, not passed through as a scalar.
    const Block b{{Cell{42.0}}};
    const auto s = sheet::columnStats(b);
    ASSERT_EQ(s.size(), 1u);
    EXPECT_EQ(s[0].count, 1u);
    EXPECT_DOUBLE_EQ(s[0].sum, 42.0);
    ASSERT_TRUE(s[0].mean.has_value());
    EXPECT_DOUBLE_EQ(*s[0].mean, 42.0);
}

TEST(ColumnStats, BlanksAreSkippedNotCountedAsZero) {
    const Block b{
        {Cell{1.0},    Cell{},    Cell{10.0}},
        {Cell{},       Cell{},    Cell{20.0}},
        {Cell{3.0},    Cell{},    Cell{}    },
    };
    const auto s = sheet::columnStats(b);
    ASSERT_EQ(s.size(), 3u);

    EXPECT_EQ(s[0].count, 2u);
    EXPECT_DOUBLE_EQ(*s[0].mean, 2.0);          // not 4/3

    EXPECT_EQ(s[1].count, 0u);
    EXPECT_FALSE(s[1].mean.has_value());        // no numbers, not a mean of 0

    EXPECT_EQ(s[2].count, 2u);
    EXPECT_DOUBLE_EQ(*s[2].mean, 15.0);
}

TEST(ColumnStats, RaggedBlockIsRejected) {
    const Block b{{Cell{1.0}, Cell{2.0}}, {Cell{3.0}}};
    EXPECT_THROW(sheet::columnStats(b), std::invalid_argument);
}

TEST(CoalescingStore, RepeatedUpdatesCollapseToOneEntry) {
    feed::CoalescingStore store;
    store.update("AAPL", 1.0);
    store.update("AAPL", 2.0);
    store.update("AAPL", 3.0);

    const auto batch = store.drainChanged();
    ASSERT_EQ(batch.size(), 1u);                // three ticks, one write
    EXPECT_EQ(batch[0].first, "AAPL");
    EXPECT_DOUBLE_EQ(batch[0].second, 3.0);     // the latest value wins
    EXPECT_EQ(store.size(), 1u);
}

TEST(CoalescingStore, DrainWithNoChangesIsEmpty) {
    feed::CoalescingStore store;
    store.update("MSFT", 1.0);
    ASSERT_EQ(store.drainChanged().size(), 1u);
    EXPECT_TRUE(store.drainChanged().empty());  // nothing changed since
    EXPECT_EQ(store.size(), 1u);                // but the value is still held
}

TEST(CoalescingStore, OutputIsSortedByKey) {
    feed::CoalescingStore store;
    store.update("ZZZ", 1.0);
    store.update("AAA", 2.0);
    store.update("MMM", 3.0);

    const auto batch = store.drainChanged();
    ASSERT_EQ(batch.size(), 3u);
    EXPECT_EQ(batch[0].first, "AAA");
    EXPECT_EQ(batch[1].first, "MMM");
    EXPECT_EQ(batch[2].first, "ZZZ");
}
```

### standalone.cpp - the offline fallback

```cpp
// Offline fallback: no GoogleTest, no network, one translation unit.
//   g++ -std=c++17 -Wall -Wextra -o standalone standalone.cpp && ./standalone
#include <cmath>
#include <cstdio>
#include <string>
#include "coalesce.hpp"
#include "sheet.hpp"

static int failures = 0;
#define CHECK(cond)                                                            \
    do {                                                                       \
        if (!(cond)) {                                                         \
            std::printf("FAIL %s:%d  %s\n", __FILE__, __LINE__, #cond);        \
            ++failures;                                                        \
        }                                                                      \
    } while (false)

int main() {
    using sheet::Block;
    using sheet::Cell;

    const Block b{{Cell{1.0}, Cell{}}, {Cell{}, Cell{}}, {Cell{3.0}, Cell{}}};
    const auto s = sheet::columnStats(b);
    CHECK(s.size() == 2u);
    CHECK(s[0].count == 2u);
    CHECK(std::fabs(*s[0].mean - 2.0) < 1e-12);
    CHECK(!s[1].mean.has_value());

    feed::CoalescingStore store;
    store.update("AAPL", 1.0);
    store.update("AAPL", 3.0);
    const auto batch = store.drainChanged();
    CHECK(batch.size() == 1u);
    CHECK(std::fabs(batch[0].second - 3.0) < 1e-12);
    CHECK(store.drainChanged().empty());

    std::printf(failures == 0 ? "all checks passed\n" : "%d failure(s)\n", failures);
    return failures == 0 ? 0 : 1;
}
```

## 03 Exercise 1 - A Range Block Into Native Data

**Prompt shape.** "You have read a rectangular range out of Excel in one call. Compute a per-column summary."

This is the code equivalent of [COM-027](<./COM and Excel Questions.md#question-com-027>) and [COM-034](<./COM and Excel Questions.md#question-com-034>): one bulk read, then all the work locally on plain data. Modelling the block as `vector<vector<optional<double>>>` is the design decision to defend - a blank cell is *absent*, not zero, and `optional` makes that impossible to forget. In the real add-in the same block arrives as a two-dimensional `SAFEARRAY` of `VARIANT` indexed from 1, and converting it once at the boundary is exactly what keeps the rest of the code free of COM.

**Three traps, each encoded as a test rather than mentioned in passing:**

- **Blanks are not zeros.** A column of `1, blank, 3` has mean 2, not 4/3. This is the bug interviewers actually look for.
- **A column with no numbers has no mean.** Returning 0 would be a silent lie; `optional<double>` returns nothing and forces the caller to decide.
- **A single cell is still a block.** Selecting one cell gives a scalar rather than a 1x1 array on the COM side, so the caller must wrap it. The test pins the contract.

**What to say about complexity and next steps.** One pass, O(rows x columns) time, O(columns) space; the blocking cost in production is the single boundary crossing, not the arithmetic. With more time: stream row by row so a large range never lands in memory twice, and carry Excel error values as their own state rather than folding them into "blank", because `#N/A` and an empty cell mean different things to the user.

## 04 Exercise 2 - Coalescing a Fast Feed for a Slow Consumer

**Prompt shape.** "Twenty thousand updates per second arrive; the display needs ten refreshes per second."

This is [ROLE-006](<../Vacancy Preparation. EPAM Senior C++ Developer, Excel COM Add-In/Vacancy Preparation. EPAM Senior C++ Developer, Excel COM Add-In - Technical Interview.md#question-role-006>) in code, so if the round is live coding rather than discussion, this is the likely shape. Name the mechanism first - in Excel this is what an RTD server does ([COM-035](<./COM and Excel Questions.md#question-com-035>)) - then write the part that is yours: the store the server would sit on top of.

**The one idea that matters.** A queue of every tick is the wrong data structure, because 19,990 of every 20,000 entries are obsolete before anyone reads them. A map from key to latest value plus a dirty set is bounded by the number of keys, not by the update rate, and drops obsolete values for free. Say that sentence out loud; it is the whole answer.

**Decisions to defend:**

- **Sorted output.** Deterministic batches make tests simple and give a consumer writing contiguous rows a stable order. The cost is `O(k log k)` per drain on changed keys only.
- **One mutex.** Correct and obvious at this scale, and I would not replace it before measuring. Under contention the next steps are sharding by key hash or moving to a single-consumer design - not reaching for lock-free code because it sounds faster ([ROLE-007](<../Vacancy Preparation. EPAM Senior C++ Developer, Excel COM Add-In/Vacancy Preparation. EPAM Senior C++ Developer, Excel COM Add-In - Technical Interview.md#question-role-007>)).
- **Drain, not push.** The consumer asks when it is ready. That is what decouples the rates, and it is the same direction RTD uses.

**What is deliberately missing, and say so before being asked:** no backpressure policy, because nothing here can block; no stale-value indication, so a feed that stops looks identical to one that is quiet; no timer, because the 10 Hz scheduler belongs to the consumer, not the store.

## 05 When You Do Not Know

Say it once, plainly, then show the method: *"I have not used that API. Here is how I would find out, and here is the equivalent I have used."* Then keep coding. Interviewers discount a confident wrong answer far more heavily than an honest gap, and the round is testing how you work, not what you have memorized.
