# Vacancy Preparation Template

> Purpose: the main interview-preparation document for one named vacancy. It preserves the vacancy, maps every important requirement to evidence or a declared gap, prepares a small set of stories and finishes with questions for the employer. Detailed technical questions and answers belong in the companion technical-interview file. Keep the result evergreen: do not add application status, interview dates or round-by-round logs. Delete prompts that do not apply.

## 01 Preparation Folder and Workflow

Create one folder per vacancy. Keep general technical banks outside vacancy folders and link them from the role-specific technical file. Keep the reusable templates at the root of `Vacancy Preparation/`, and copy both templates into the vacancy folder before adding role-specific material.

```text
Interview/
`-- Vacancy Preparation/
    |-- Technical Interview/
    |   |-- C++ Core Questions.md
    |   |-- JavaScript and Node.js Questions.md
    |   `-- COM and Excel Questions.md
    |-- Vacancy Preparation Template.md
    |-- Technical Interview Template.md
    `-- Vacancy Preparation. [Company] [Role]/
        |-- Vacancy Preparation. [Company] [Role].md
        `-- Vacancy Preparation. [Company] [Role] - Technical Interview.md
```

The main file owns the stable vacancy record, positioning, evidence map, stories, preparation priorities and employer questions. Shared banks own reusable topic answers. The vacancy technical file links those banks and owns only role-specific integration questions, product APIs and system-design scenarios. Do not maintain the same answer in more than one file.

For repository-local links whose filenames contain spaces or characters such as `+` and `,`, keep the exact relative path inside angle brackets, as in [Technical Interview Template](<./Technical Interview Template.md>). Do not percent-encode the filename; the repository preview may treat escapes such as `%2B` as literal filename text.

1. Create and name the vacancy folder, then copy and rename both templates.
2. Preserve the vacancy text, URL and relevant recruiter context before the posting changes or disappears.
3. Separate explicit requirements from your interpretation of what the team probably needs.
4. Classify each important requirement as strong evidence, adjacent evidence, refresh needed, learning gap or unknown.
5. Select three stories that cover most of the role; do not retell the entire career history.
6. Link the relevant stable IDs from shared banks, then prepare only cross-topic and vacancy-specific answers in the companion file.
7. Close the highest-risk knowledge gap with official reading and one small practical exercise.
8. Perform one consistency pass so the copied files are ready to use without maintaining a status timeline.

The document is ready when every important requirement has either evidence with an ownership boundary or a direct gap answer.

## 02 Vacancy Record

| Field | Details |
|---|---|
| Company | [Company] |
| Role | [Exact title] |
| Team / product | [Known context] |
| Location / work mode | [Location, remote/hybrid/on-site] |
| Employment / rate | [Contract type or range, when known] |
| Vacancy URL | [URL] |
| Recruiter / source | [Name and channel] |
| Interview format | [Stable format information relevant to preparation, or unknown] |

### Exact Vacancy Text

> [Paste or link to the preserved vacancy text. Keep the employer's wording separate from your interpretation.]

### Hiring Hypothesis

- **Problem the team is hiring to solve:** [What probably matters in the first months.]
- **Three strongest signals:** [Requirement 1], [requirement 2], [requirement 3].
- **Main risk in my profile:** [The gap likely to decide the interview.]
- **Why this role is worth pursuing:** [Specific product, engineering or career reason.]
- **Unknowns that can change the decision:** [Scope, stack, seniority, location, compensation or work authorization.]

## 03 Positioning and Spoken Answers

### Positioning Sentence

[One sentence: professional identity + closest evidence + honest gap or differentiator.]

### 30-Second Introduction

[Approximately 60-90 words: current identity, closest project, strongest relevant capability and one explicit gap only if it is central to the role.]

### 90-Second Introduction

[Approximately 170-230 words: two relevant projects, personal contribution, one result or engineering theme, and why the role follows logically. Do not recite every employer.]

### Why This Company and Role

[Two concrete reasons tied to the product/team and one reason tied to your experience. Avoid generic growth language.]

### Main Gap Answer

[State the gap directly. Separate adjacent evidence from direct experience. Name the concepts or exercise used to close it. Do not apologize or imply production experience that did not happen.]

### Closing Summary

[Two sentences: the value you bring now and what you expect to learn or confirm.]

### Do Not Claim

- [Skill, ownership, result or scale not supported by evidence.]
- [Adjacent technology that must not be presented as direct experience.]
- [Confidential detail that must not be disclosed.]

## 04 Requirement-to-Evidence Map

First copy the employer's requirement faithfully, then add your evidence. A gap is a valid entry; an inflated claim is not.

| Priority | Exact requirement | Classification | Evidence / project | Personal action and result | Boundary / gap | Story |
|---|---|---|---|---|---|---|
| Must | [Requirement] | [Strong / adjacent / refresh / gap / unknown] | [Project] | [What I did and what changed] | [What this does not prove] | [Story name] |
| Must | [Requirement] | [Classification] | [Evidence] | [Action/result] | [Boundary] | [Story] |
| Nice | [Requirement] | [Classification] | [Evidence] | [Action/result] | [Boundary] | [Story] |

### Coverage Review

- **Strongest match:** [Requirement and why.]
- **Transferable but indirect:** [Requirement and adjacent evidence.]
- **Main gap:** [Requirement and direct answer.]
- **Unverified vacancy assumption:** [What must be asked rather than assumed.]
- **Reason to stop the process, if confirmed:** [A genuine mismatch or constraint.]

## 05 Story Kit

Prepare three primary stories and one backup. Each primary story should cover several vacancy signals and take about two minutes before follow-up questions.

### Story 1 - [Title]

- **Requirements covered:** [Skills or behaviors.]
- **Context:** [Product, users and constraint in two sentences.]
- **My responsibility:** [Personal ownership, distinct from team ownership.]
- **Actions:** [Three or four technically specific actions.]
- **Result:** [Observable behavior, delivered outcome or supported metric.]
- **Validation:** [Tests, measurements, review or production evidence.]
- **Tradeoff / lesson:** [One decision and its consequence.]
- **Boundary:** [What another person/team owned or what remains unknown.]
- **Likely follow-up:** [Question that probes depth.]

### Story 2 - [Title]

- **Requirements covered:** [Skills or behaviors.]
- **Context:** [Context.]
- **My responsibility:** [Ownership.]
- **Actions:** [Actions.]
- **Result:** [Result.]
- **Validation:** [Validation.]
- **Tradeoff / lesson:** [Tradeoff.]
- **Boundary:** [Boundary.]
- **Likely follow-up:** [Question.]

### Story 3 - [Title]

- **Requirements covered:** [Skills or behaviors.]
- **Context:** [Context.]
- **My responsibility:** [Ownership.]
- **Actions:** [Actions.]
- **Result:** [Result.]
- **Validation:** [Validation.]
- **Tradeoff / lesson:** [Tradeoff.]
- **Boundary:** [Boundary.]
- **Likely follow-up:** [Question.]

### Backup Story - [Title]

[Use for a secondary requirement such as a failure, disagreement, legacy system, mentoring, delivery incident or unfamiliar domain.]

## 06 Technical Preparation

Keep the role-specific priorities and study plan here. Put complete answers in `Vacancy Preparation. [Company] [Role] - Technical Interview.md` and add a relative link to it after creating the vacancy folder.

- **Technical questions and answers:** [Add the relative link to the companion file.]
- **Boundary:** this file says what to prepare and why; the companion file contains how to answer it.

### Topic Matrix

| Topic | Why the role needs it | Current depth | What to review | Technical question IDs | Proof of readiness |
|---|---|---|---|---|---|
| [Topic] | [Vacancy signal or likely task] | [Strong / refresh / learn / unknown] | [Specific concepts] | [TQ-01, TQ-02] | [Story, explanation, code or exercise] |

### Architecture to Clarify

- [Which runtime, framework, protocol or deployment model is actually used?]
- [Where are the ownership and process boundaries?]
- [What is synchronous, asynchronous, local or remote?]
- [Which compatibility and security constraints are mandatory?]

### Fundamentals to Explain Without Notes

- [Concept and the failure it prevents.]
- [Concept and the design tradeoff it creates.]
- [Concept and one example from past work.]

### Likely Technical Questions

- **TQ-01:** [Question derived directly from a must-have requirement; answer it in the companion file.]
- **TQ-02:** [Question about debugging or failure behavior.]
- **TQ-03:** [Question about performance and measurement.]
- **TQ-04:** [Question about concurrency, lifetime or state.]
- **TQ-05:** [Question about testing and delivery.]
- **TQ-06:** [Question about a technology gap.]

### System-Design Prompt

[Write one realistic design prompt based on the product.]

Cover:

1. Requirements, scale and failure semantics.
2. Components and ownership boundaries.
3. Data flow, state and concurrency.
4. Error handling, retry, cancellation and shutdown.
5. Compatibility, security and deployment.
6. Observability and performance measurement.
7. Unit, integration and end-to-end validation.

### Practical Exercise

- **Goal:** [Smallest exercise that attacks the largest gap.]
- **Time box:** [A few focused hours, not a new portfolio project.]
- **Deliverable:** [Code, benchmark, diagram or written diagnosis.]
- **Questions it should answer:** [What you expect to learn.]
- **Honest interview wording:** "I built this as preparation; it is not production experience."

### Official Reading

- [Primary vendor documentation or standard.]
- [Relevant API or platform reference.]
- [Performance, deployment or troubleshooting guide.]

## 07 Questions for the Employer

### Recruiter

- [Is the apparent must-have truly required on day one?]
- [What are the interview stages and evaluation areas?]
- [What are the location, contract, compensation and start constraints?]

### Hiring Manager / Engineering Team

- [What problem should this hire solve in the first three months?]
- [Which component and decisions would I own?]
- [What is the current architecture and largest technical constraint?]
- [How are correctness, performance and reliability measured?]
- [What is automated, and what still requires manual validation?]
- [Why is the position open?]

## 08 Production-Ready Standard

The completed preparation should satisfy these stable conditions; do not turn this section into a progress tracker or interview timeline.

- Every must-have has evidence or a direct gap answer.
- Every claim has a project source or a clearly labeled preparation source.
- Personal ownership is separate from team or product capability.
- Results are observable; unsupported numbers have been removed.
- The introduction is relevant to this role, not a full career chronology.
- Three stories cover most likely behavioral and technical follow-ups.
- The technical companion is linked, prioritized and contains spoken answers rather than copied reference material.
- Questions test the assumptions that could change the decision.
- Confidential names, code, credentials, endpoints and customer data stay private.

## 09 Sources

### Repository Sources

- [Project document, CV, code sample or other stable evidence.]

### Public Sources

- [Official product page, vendor documentation or standard.]

| Claim | Source | Disclosure |
|---|---|---|
| [Vacancy or project fact] | [Stable source] | [Public / safe internal / confidential] |
