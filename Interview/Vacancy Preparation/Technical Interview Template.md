# Technical Interview Template

> Purpose: the technical companion for one named vacancy. Keep only cross-topic integration, product-specific APIs and architecture scenarios here. Link reusable C++, JavaScript/Node.js, COM/Excel or other general question banks instead of copying their answers. Keep vacancy analysis, career evidence and behavioral stories in the main vacancy-preparation file.

## 01 File Record and Use

| Field | Details |
|---|---|
| Company | [Company] |
| Role | [Exact title] |
| Main preparation | [Add a relative link to the main vacancy-preparation file.] |
| Interview format | [Discussion, live coding, system design, take-home or unknown] |

Use the question IDs from the main file so priorities stay synchronized. Every answer should begin with a direct 30-60 second bullet summary and then provide the detail needed for likely follow-ups. Material learned specifically for this interview must not be presented as production experience.

A short answer should normally contain two to four self-contained bullets:

- **Definition/conclusion** — answer the exact question in the first bullet.
- **Mechanism or decision rule** — explain how it works or when to choose it.
- **Important nuance** — state the main exception, failure mode or tradeoff.
- **Practical action** — include only when the question implies a design or diagnostic decision.

Keep these bullets concise enough to say naturally, but do not omit a correctness-changing caveat. Put code, detailed mechanics, alternatives and secondary edge cases under `Details and nuances` so the spoken answer stays short without making the written answer superficial.

For the link to the main preparation, use the exact relative filename inside angle brackets, as in [Vacancy Preparation Template](<./Vacancy Preparation Template.md>). This keeps spaces, `+` and punctuation compatible with the repository preview.

## 02 Shared Question Banks

| Bank | Relative link | Relevant sections / IDs |
|---|---|---|
| C++ | [C++ Core Questions](<./Technical Interview/C++ Core Questions.md>) | [Select stable IDs instead of copying answers.] |
| JavaScript / Node.js | [JavaScript and Node.js Questions](<./Technical Interview/JavaScript and Node.js Questions.md>) | [Select stable IDs.] |
| COM / Excel | [COM and Excel Questions](<./Technical Interview/COM and Excel Questions.md>) | [Select stable IDs.] |

These links are correct for the reusable template at `Vacancy Preparation/`. After copying the template one level deeper into a named vacancy folder, use `../Technical Interview/...`. Keep exact relative paths inside angle brackets.

## Question Index

- [ROLE-001 - Question](#question-role-001)
- [ROLE-002 - Question](#question-role-002)

Use native Markdown headings such as `Question ROLE-001` as jump targets; do not use raw HTML anchors. Keep the return link between that heading and the question text so the question remains visible below the preview's fixed header after a jump.

## 03 Coverage and Priority

| ID | Topic | Why likely | Priority | Confidence |
|---|---|---|---|---|
| ROLE-001 | [Topic or question] | [Vacancy requirement, recruiter signal or likely task] | [High / medium / low] | [Strong / refresh / learn / unknown] |

## 04 Questions and Answers

### Question ROLE-001

[↑ Back to question index](#question-index)

#### Question ROLE-001 — [Question]

**Short answer**

- [Definition or direct conclusion.]
- [Mechanism or selection rule.]
- [Important caveat, failure mode or tradeoff.]

**Details and nuances**

[Explain the mechanism, tradeoff and relevant failure mode.]

**Example or evidence boundary**

[Use a real project example when supported. Otherwise say explicitly that this is prepared knowledge, a toy exercise or a hypothetical design.]

**Likely follow-ups**

- [Follow-up question and the point it tests.]
- [Alternative or edge case the interviewer may introduce.]

**Reference / version boundary**

- [Primary source and the standard/product version to which the claim applies.]

[↑ Back to question index](#question-index)

### Question ROLE-002

[↑ Back to question index](#question-index)

#### Question ROLE-002 — [Question]

**Short answer**

- [Definition or direct conclusion.]
- [Mechanism or selection rule.]
- [Important caveat, failure mode or tradeoff.]

**Details and nuances**

[Explanation, tradeoff and failure mode.]

**Example or evidence boundary**

[Example or explicit boundary.]

**Likely follow-ups**

- [Follow-up.]

**Reference / version boundary**

- [Item or none.]

[↑ Back to question index](#question-index)

## 05 Live-Coding and Diagnostic Exercises

### Exercise 1 - [Title]

- **Prompt:** [Realistic task derived from the vacancy.]
- **Clarifying questions:** [Inputs, constraints, error behavior and scale.]
- **Approach:** [Data structures, algorithm and complexity.]
- **Implementation risks:** [Lifetime, overflow, invalidation, concurrency or API misuse.]
- **Tests:** [Normal, boundary and failure cases.]
- **Verification:** [Complexity, correctness invariant and test evidence.]

## 06 System-Design Answers

### SD-01 - [Prompt]

Cover:

1. Requirements, scale and failure semantics.
2. Components and ownership boundaries.
3. Data flow, state and concurrency.
4. Error handling, retry, cancellation and shutdown.
5. Compatibility, security and deployment.
6. Observability and performance measurement.
7. Unit, integration and end-to-end validation.

### Decisions and Tradeoffs

| Decision | Chosen option | Alternative | Why | Failure / revisit signal |
|---|---|---|---|---|
| [Decision] | [Choice] | [Alternative] | [Reason] | [What would invalidate it] |

## 07 Rapid Review

- [Concept that must be explained without notes.]
- [Common trap or misconception.]
- [Role-specific API, protocol or platform behavior.]
- [One debugging sequence.]
- [One performance answer that begins with measurement.]
- [One honest answer about the largest technical gap.]

## 08 Sources

Prefer standards, official documentation and primary vendor material. Record sources for version-specific or unfamiliar claims; do not turn the answer file into copied documentation.

| Question | Claim | Primary source | Standard / product scope |
|---|---|---|---|
| ROLE-001 | [Claim] | [Primary source] | [Version or stable contract] |
