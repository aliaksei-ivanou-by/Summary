# Technical Interview Template

> Purpose: the technical companion for one named vacancy. It contains likely technical questions, concise ready-to-say answers, deeper follow-ups, examples and system-design practice. Keep vacancy analysis, career evidence, behavioral stories and the interview log in the main vacancy-preparation file.

## 01 File Record and Use

| Field | Details |
|---|---|
| Company | [Company] |
| Role | [Exact title] |
| Main preparation | [Add a relative link to the main vacancy-preparation file.] |
| Interview format | [Discussion, live coding, system design, take-home or unknown] |
| Interview date | [YYYY-MM-DD or unknown] |
| Last reviewed | [YYYY-MM-DD] |

Use the question IDs from the main file so priorities stay synchronized. Answers should normally begin with a direct 30-90 second response; add depth only for likely follow-ups. Material learned specifically for this interview must not be presented as production experience.

## 02 Coverage and Priority

| ID | Topic | Why likely | Priority | Confidence | Rehearsed |
|---|---|---|---|---|---|
| TQ-01 | [Topic or question] | [Vacancy requirement, recruiter signal or likely task] | [High / medium / low] | [Strong / refresh / learn / unknown] | [ ] |

## 03 Questions and Answers

### TQ-01 - [Question]

**Short answer**

[Give the conclusion first. Keep this version short enough to say naturally without notes.]

**Deeper explanation**

[Explain the mechanism, tradeoff and relevant failure mode.]

**Example or evidence boundary**

[Use a real project example when supported. Otherwise say explicitly that this is prepared knowledge, a toy exercise or a hypothetical design.]

**Likely follow-ups**

- [Follow-up question and the point it tests.]
- [Alternative or edge case the interviewer may introduce.]

**Needs verification**

- [Fact, API detail or version-specific behavior to verify in a primary source.]

### TQ-02 - [Question]

**Short answer**

[Answer.]

**Deeper explanation**

[Explanation, tradeoff and failure mode.]

**Example or evidence boundary**

[Example or explicit boundary.]

**Likely follow-ups**

- [Follow-up.]

**Needs verification**

- [Item or none.]

## 04 Live-Coding and Diagnostic Exercises

### Exercise 1 - [Title]

- **Prompt:** [Realistic task derived from the vacancy.]
- **Clarifying questions:** [Inputs, constraints, error behavior and scale.]
- **Approach:** [Data structures, algorithm and complexity.]
- **Implementation risks:** [Lifetime, overflow, invalidation, concurrency or API misuse.]
- **Tests:** [Normal, boundary and failure cases.]
- **Retrospective:** [What was slow, unclear or incorrect during rehearsal.]

## 05 System-Design Answers

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

## 06 Rapid Review

- [Concept that must be explained without notes.]
- [Common trap or misconception.]
- [Role-specific API, protocol or platform behavior.]
- [One debugging sequence.]
- [One performance answer that begins with measurement.]
- [One honest answer about the largest technical gap.]

## 07 Sources

Prefer standards, official documentation and primary vendor material. Record sources for version-specific or unfamiliar claims; do not turn the answer file into copied documentation.

| Question | Claim checked | Source | Checked |
|---|---|---|---|
| TQ-01 | [Claim] | [Primary source] | [YYYY-MM-DD] |

## 08 Review Notes

### [YYYY-MM-DD] - [Practice session or interview round]

- **Questions practiced or asked:** [IDs and new questions.]
- **Answers that were weak:** [IDs and why.]
- **Corrections made:** [What changed.]
- **Add to main interview log:** [New role facts, follow-up or decision only.]
