# Behavioral Questions

> Purpose: prepared answers to the non-technical questions that appear in almost every senior interview. Reusable across vacancies; the vacancy-specific stories live in each vacancy's own preparation file. Every answer here is grounded in a real project and carries the same ownership boundaries as the project documents.

Two rules that apply to all of them.

**Answer the question asked, then stop.** The most common failure in this part of an interview is not a weak story, it is a good story that runs four minutes and answers a question nobody asked. Ninety seconds is usually right; the interviewer will ask for more if they want more.

**Never make the other person the villain.** A story where a colleague was wrong and I was right says more about me than about them, whatever actually happened. The version that lands describes a disagreement between two defensible positions and what resolved it.

## Question Index

- [BEH-001. Tell me about a technical decision you would defend.](#question-beh-001)
- [BEH-002. Tell me about a time you were wrong.](#question-beh-002)
- [BEH-003. Tell me about a disagreement with a colleague or a customer.](#question-beh-003)
- [BEH-004. What is the hardest bug you have debugged?](#question-beh-004)
- [BEH-005. How do you work with people in other time zones?](#question-beh-005)
- [BEH-006. Tell me about mentoring or giving difficult feedback.](#question-beh-006)
- [BEH-007. What is your biggest weakness?](#question-beh-007)
- [BEH-008. How do you handle a deadline you are not going to make?](#question-beh-008)
- [BEH-009. Why should we hire you for this role?](#question-beh-009)
- [BEH-010. What are your salary expectations?](#question-beh-010)

---

## Question BEH-001

### BEH-001 — Tell me about a technical decision you would defend.

"On the Windows provisioning tool I had to add save and load for a configuration project. The obvious implementation is to serialise the UI control tree generically - about twenty lines, works the first day. I deliberately didn't, and I'd defend that.

A generic dump makes the file format an accident of the UI layout. Rename a control, reorder a panel, and old files break in a way nobody can review, because the format was never written down. So I built explicit project-state objects instead: gather deliberately, restore deliberately.

The cost is real and I'd state it before anyone asks - every new field needs mapping work, so the cheap path is genuinely cheaper on day one. What it buys is that fields, defaults and schema growth show up in a diff, so adding a field becomes a reviewable change rather than a hope. For a format that outlives the code that wrote it, I'll pay that."

**Why this one works:** it names the cheaper alternative and the cost of the choice, which is what separates a decision from a preference.

## Question BEH-002

### BEH-002 — Tell me about a time you were wrong.

"On the SIP phone platform, the requirement was primary/backup server support. My first approach put the switching logic at application level, and I was wrong about it. I reverted it.

The reason it was wrong is worth more than the fact. The SDK owns registration, so it is the only layer that sees the transitions in order. At application level I was reconstructing state I could only observe second-hand and always slightly late - so the application's view and the real registration state could disagree, and that disagreement is exactly the bug the feature was supposed to prevent.

So I moved it: implemented the watchdog and the failover/failback state machine in the SDK, added a backup-domain field to AuthInfo so authentication survived the switch, and made the application consume SDK-owned state instead of maintaining a copy. That rule - one owner for the state, everyone else reads it - is what stopped the UI and the persisted configuration drifting apart.

What I took from it is that when I find myself reconstructing another layer's state, that is the signal I've put the logic in the wrong layer."

**Why this one works:** a reverted approach is a real admission, and the lesson is a rule rather than a sentiment.

## Question BEH-003

### BEH-003 — Tell me about a disagreement with a colleague or a customer.

Use the inherited-code version, which is a disagreement about method rather than about a person.

"On the EPAM sensor-data product I was simplifying inherited logic - conditions that could no longer be true, branches kept for a configuration that no longer existed, helpers duplicated with small differences. There was pressure to move faster on it, and the disagreement was about how much you have to understand before you delete.

My position was that a simplification only has value if it is provably behaviour-preserving, which in practice means understanding why the dead branch was written before removing it - because sometimes it is not dead, and the cost of being wrong lands on a customer rather than on me.

What resolved it wasn't winning an argument. It was making the work visible: keeping each change narrow enough to review on its own, so a reviewer could agree or disagree about one deletion instead of about my judgement in general. That also made the disagreement cheap, because we were then arguing about a specific branch, which is a question with an answer."

**If pushed for a disagreement with a person rather than a practice:** the honest answer is that in outsourcing the disagreements I have had were about scope and ownership rather than personal, and describe one of those - do not invent a conflict.

## Question BEH-004

### BEH-004 — What is the hardest bug you have debugged?

"An airborne optical system computed the geographic coordinates of each frame centre, and the coordinates were wrong. The camera tested correctly. The navigation board tested correctly. Both subsystems were fine.

That is what made it hard: nothing in either subsystem's own output could reveal the fault, because the error wasn't inside either of them. It lived in the relationship between them - a small misalignment between the camera's optical axis and the inertial unit's reference frame. A fraction of a degree, invisible on a bench, and a large displacement on the ground at operating altitude.

The fix was to stop trusting the nominal alignment values, calibrate the actual alignment, and build that correction into the coordinate computation - then validate against known ground positions rather than against internal consistency, because internal consistency was exactly what had been lying.

The habit I kept is to treat interfaces as the first suspect rather than the last. When two components each pass their own tests and the system is still wrong, the defect is usually in what they assume about each other, and no amount of testing either one alone will find it."

**Why this one works:** it is a class of bug, not an anecdote, and the closing habit transfers to any integration-heavy role.

## Question BEH-005

### BEH-005 — How do you work with people in other time zones?

"On the EPAM sensor-data project the team was five C++ engineers where I was, QA in India, and the customer in the United States. The practical consequence is that a question asked at the end of my day is answered at the end of theirs. An ambiguous report costs a full day instead of five minutes.

So I changed how I wrote them. Reproduction steps, representative input, and expected behaviour written for someone I cannot interrupt - the test being whether a person with no context could follow it without asking me anything. Before sending, I'd read it back and look for any sentence that assumed something only I knew.

It is not a communication skill so much as an engineering one: you are writing a deterministic procedure, and the reader is the machine that runs it."

## Question BEH-006

### BEH-006 — Tell me about mentoring or giving difficult feedback.

"At Innowise I'm the technical lead of an engineering sub-group that grew from four to six, alongside roughly 80% hands-on delivery, so the lead part is code review, task planning, onboarding, mentoring and interview support rather than management.

Most of the difficult feedback happens in code review, and the thing I've had to learn is to separate 'this is wrong' from 'this is not how I would have done it'. The second one is not feedback, it is preference, and mixing them makes a reviewer easy to ignore. So I try to say which one I'm giving, and for the second kind, say it once and let it go.

Where it's actually a defect, the useful form is the failure case, not the verdict: not 'this is unsafe' but 'if two of these arrive while the first is still in flight, this is the state you end up with'. That turns it into a technical conversation with an answer, and the person can check it themselves."

**Boundary:** technical lead of a sub-group, not a line manager; no formal performance-review responsibility.

## Question BEH-007

### BEH-007 — What is your biggest weakness?

Answer with a real one that is specific and bounded, and say what you do about it. Avoid the disguised-strength form.

"Two honest ones, and I'd rather name them than have them found.

The first is domain-specific: I have not built an Excel COM add-in, and I'd present that as an active learning area rather than adjacent experience. I know precisely what I'm missing - the add-in lifecycle, the object model, registration and deployment behaviour - which is not the same as knowing it.

The second is a real pattern: I have a strong pull toward getting a design right before shipping it, and on a short assignment that is not always the correct trade. On a three-month EPAM assignment I had to accept that there was no time for architectural replacement and that the value was in focused, reviewable changes inside the existing structure. I still have to make that call consciously rather than by instinct."

## Question BEH-008

### BEH-008 — How do you handle a deadline you are not going to make?

"Say it early, and say it with a number and a choice rather than with an apology.

Early, because the value of the information decays fast - a warning three weeks out lets someone re-plan, the same warning three days out is just bad news. With a number, because 'it's going to slip' is not actionable and 'the integration path is two weeks, not one, and here is what I found' is. And with a choice, because the person I'm telling usually has options I don't: cut a case, ship without a piece, move a date, add a reviewer.

What I try not to do is absorb it quietly and hope. That converts a schedule problem, which someone can manage, into a surprise, which nobody can."

## Question BEH-009

### BEH-009 — Why should we hire you for this role?

Keep it to three sentences and do not oversell the gap.

"Because the parts of this role that are hard for most candidates are the parts I have actually done: production C and C++ in inherited enterprise code, asynchronous state that has to stay consistent across layers, and debugging that crosses a language or process boundary - which is most of what an Excel add-in with a JavaScript surface and a native core is made of.

The Excel and COM domain is new to me, and I would rather be the person who says that clearly and has a specific plan than the person who discovers it in the first sprint.

And I've worked at EPAM for three years, in distributed international teams, so there is no adjustment period on how the work is organised."

## Question BEH-010

### BEH-010 — What are your salary expectations?

**Decide the numbers before the call, not during it.** Fill these in from current posted ranges for the role and city - No Fluff Jobs and Just Join IT publish real ranges by contract type - and do not go into the conversation without them.

| | Figure | Note |
|---|---|---|
| Target, UoP gross monthly | | The number to state |
| Range to give if asked for a range | | Keep the bottom of it a number you would actually accept |
| Walk-away | | Never said out loud; it exists so the answer is a decision, not a reaction |
| B2B equivalent, if relevant | | Roughly the UoP figure plus the employer-side cost, minus what you take on |

The phrasing when asked first:

"Based on what I'm seeing for senior C++ roles in Warsaw, I'm looking at [range] gross on a standard employment contract. I'd rather be in the right role than optimise the number, so if the fit is good I'm open to discussing it - but that's the range I'm working from."

If they ask what you earn now, you are not obliged to answer, and in Poland it is normal not to: "I'd rather talk about the range for this role than about my current contract - they're structured differently."
