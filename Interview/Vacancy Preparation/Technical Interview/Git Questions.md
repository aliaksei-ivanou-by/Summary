# Git Technical Interview Questions and Answers

> Reusable bank for the version-control questions that appear in almost every interview and that nobody prepares for, because everyone assumes they already know Git. The gap is usually not the commands but being able to say what a command does to the graph.

**Scope of my own claims.** Daily Git and GitLab use across every commercial project, Bitbucket on the library platform, and Jenkins and GitLab CI builds from it. That is user-level fluency, not repository administration or CI platform design.

# Question Index

## The Model (GIT-001–GIT-004)

|  |  |  |
|---|---|---|
| [GIT-001. What is a commit, actually?](#question-git-001) | [GIT-002. What is the difference between the working tree, the index and HEAD?](#question-git-002) | [GIT-003. What is a branch?](#question-git-003) |
| [GIT-004. Merge vs rebase?](#question-git-004) |  |  |

## Everyday Operations (GIT-005–GIT-008)

|  |  |  |
|---|---|---|
| [GIT-005. How do you resolve a merge conflict?](#question-git-005) | [GIT-006. `reset --soft`, `--mixed`, `--hard` and `revert` - which undoes what?](#question-git-006) | [GIT-007. What are `cherry-pick` and `stash` for?](#question-git-007) |
| [GIT-008. You committed a secret. What now?](#question-git-008) |  |  |

## Working With Others (GIT-009–GIT-010)

|  |  |  |
|---|---|---|
| [GIT-009. Which branching strategy, and why?](#question-git-009) | [GIT-010. How do you find the commit that broke something?](#question-git-010) |  |

# 1. The Model

## Question GIT-001

[↑ Back to question index](#question-index)

### Question GIT-001 — What is a commit, actually?

**Short answer**

- A commit is an immutable object holding a complete snapshot of the tree, plus author, message and the hashes of its parents - not a diff. Diffs are computed between commits when you ask for one.
- Its identity is the SHA-1 (or SHA-256) of that content including the parent hashes, which is why changing anything about a commit - or anything in its history - produces a different commit.
- The history is therefore a directed acyclic graph of snapshots, and almost every confusing Git behaviour becomes obvious once you picture the graph instead of a list of changes.

**Details and nuances**

The immutability point is the one that answers a whole family of questions. `rebase`, `commit --amend` and `filter-repo` do not modify commits - they cannot - they create new ones and move the branch pointer. The old commits still exist, unreferenced, until garbage collection, which is exactly why `git reflog` can recover work that appears lost.

A merge commit is simply a commit with two parents. That is the entire difference, and it is why a merge preserves both histories while a rebase, which replays changes onto a new base, does not.

[↑ Back to question index](#question-index)

---

## Question GIT-002

[↑ Back to question index](#question-index)

### Question GIT-002 — What is the difference between the working tree, the index and HEAD?

**Short answer**

- The working tree is the files on disk; the index (staging area) is what the next commit will contain; HEAD is the commit the current branch points at.
- `git add` moves changes from the working tree into the index; `git commit` turns the index into a new commit and advances HEAD.
- The three-way split is what makes it possible to commit part of your changes - `git add -p` stages selected hunks, so one messy afternoon becomes several reviewable commits.

**Details and nuances**

The commands that move between the three are worth being able to name without hesitating, because the question is usually a proxy for whether you understand the model:

| Goal | Command |
|---|---|
| Working tree → index | `git add <path>` |
| Index → working tree (unstage) | `git restore --staged <path>` |
| Discard working-tree change | `git restore <path>` |
| See working tree vs index | `git diff` |
| See index vs HEAD | `git diff --staged` |

That last pair explains the most common confusion: plain `git diff` shows nothing after you staged everything, which looks like the changes vanished.

[↑ Back to question index](#question-index)

---

## Question GIT-003

[↑ Back to question index](#question-index)

### Question GIT-003 — What is a branch?

**Short answer**

- A movable pointer to one commit - a file containing a hash. That is all, which is why creating and deleting branches is instant and why they cost nothing.
- Committing moves the pointer forward; HEAD normally points at a branch, and "detached HEAD" simply means HEAD points directly at a commit instead.
- A remote-tracking branch such as `origin/main` is a local record of where that branch was on the remote at the last fetch, not a live view of the remote.

**Details and nuances**

That last point explains the everyday confusion between `fetch` and `pull`: `fetch` updates the remote-tracking pointers and touches nothing else, while `pull` is `fetch` followed by `merge` (or `rebase` with `--rebase`). Someone surprised by an unexpected merge commit usually ran `pull` when they meant `fetch`.

Detached HEAD is not an error state - it is what you are in after `git checkout <sha>` - but commits made there are referenced by nothing, so they disappear from view when you check out a branch. `git reflog` and then a branch created at that hash is the recovery.

[↑ Back to question index](#question-index)

---

## Question GIT-004

[↑ Back to question index](#question-index)

### Question GIT-004 — Merge vs rebase?

**Short answer**

- Merge creates one new commit with two parents, preserving exactly what happened; rebase replays your commits onto a new base, producing new commits with new hashes and a linear history.
- Rebase gives a history that reads as a straight line, at the cost of rewriting it - so never rebase commits that other people have already pulled.
- The workable rule: rebase your own unpushed work to tidy it before review, merge to integrate shared branches.

**Details and nuances**

The reason "never rebase public history" is a rule rather than a preference: your rebase gives every replayed commit a new hash, so anyone who already has the old ones now has two copies of the same changes and a merge that resolves them badly. Recovering that for a whole team is expensive and entirely avoidable.

`git pull --rebase` is the everyday application - it replays your local commits on top of what was fetched rather than adding a merge commit for every sync, which is how the noisy "Merge branch 'main' into main" commits disappear.

Interactive rebase (`git rebase -i`) is the tool for squashing, reordering and rewording before a review. Worth saying that you use it for that, because a reviewer reading five coherent commits is faster and more accurate than one reading thirty.

[↑ Back to question index](#question-index)

---

# 2. Everyday Operations

## Question GIT-005

[↑ Back to question index](#question-index)

### Question GIT-005 — How do you resolve a merge conflict?

**Short answer**

- A conflict happens when both sides changed overlapping lines and Git will not guess; it marks the region with `<<<<<<<`, `=======`, `>>>>>>>` and stops.
- Resolve by editing the file to what it should actually be - not by picking a side mechanically - then `git add` the file to mark it resolved, then `git commit` (or `git rebase --continue`).
- `git merge --abort` or `git rebase --abort` returns to the state before you started, which is the right move whenever the conflict turns out to be bigger than expected.

**Details and nuances**

Two things worth mentioning because they show judgement rather than mechanics. First, the conflict markers show text, but the correct resolution is about intent: two changes can be textually adjacent and semantically fine, or textually distant and semantically incompatible - which Git cannot see at all. After resolving anything non-trivial, build and run the tests, because a clean merge is not a correct one.

Second, conflicts are a symptom with a cause: long-lived branches and large changes. Merging from the main line frequently turns one enormous conflict into several trivial ones.

`git checkout --ours` and `--theirs` are available when one side is genuinely right - typically for generated files - and the naming inverts during a rebase, which catches everyone at least once.

[↑ Back to question index](#question-index)

---

## Question GIT-006

[↑ Back to question index](#question-index)

### Question GIT-006 — `reset --soft`, `--mixed`, `--hard` and `revert` - which undoes what?

**Short answer**

- `reset` moves the branch pointer backwards: `--soft` keeps the index and working tree, `--mixed` (the default) keeps the working tree and clears the index, `--hard` discards both.
- `revert` does the opposite of rewriting: it creates a *new* commit that undoes an earlier one, leaving history intact.
- The rule is the same as for rebase: `reset` on local work, `revert` on anything already pushed - because reverting is the only undo that does not rewrite what others have.

**Details and nuances**

| Command | Branch pointer | Index | Working tree | Use when |
|---|---|---|---|---|
| `reset --soft HEAD~1` | back one | kept | kept | Re-commit the same changes differently |
| `reset --mixed HEAD~1` | back one | cleared | kept | Re-stage selectively |
| `reset --hard HEAD~1` | back one | cleared | **lost** | You want the changes gone |
| `revert <sha>` | forward one | - | - | The commit is already public |

`--hard` is the only one that destroys uncommitted work, and it is the only Git command that routinely loses something permanently - committed work is recoverable through `git reflog` for the default ninety days, uncommitted work is not recoverable at all.

`git commit --amend` belongs in the same family: it replaces the last commit rather than adding one, which is fine locally and a history rewrite once pushed.

[↑ Back to question index](#question-index)

---

## Question GIT-007

[↑ Back to question index](#question-index)

### Question GIT-007 — What are `cherry-pick` and `stash` for?

**Short answer**

- `cherry-pick <sha>` applies the change introduced by one commit onto the current branch as a new commit - the tool for taking a single fix from one branch without taking everything around it.
- `stash` sets aside uncommitted work so the tree is clean, then `stash pop` brings it back - for when something urgent arrives mid-change.
- Both are convenient and both are signs worth noticing: frequent cherry-picking usually means the branching model does not match how fixes actually flow.

**Details and nuances**

The cherry-pick case that matters in a release process is a hotfix: the fix is made on the release branch and cherry-picked to the main line, or the reverse. The thing to watch is that the same change now exists as two commits with different hashes, so a later merge between those branches can conflict with itself - which is why the commit that was picked should be recorded in the message.

`stash` caveats worth knowing: it does not include untracked files unless you pass `-u`, and a stash that sits for a week becomes a mystery. `git stash list` and a message (`git stash push -m`) cost nothing.

[↑ Back to question index](#question-index)

---

## Question GIT-008

[↑ Back to question index](#question-index)

### Question GIT-008 — You committed a secret. What now?

**Short answer**

- Treat it as leaked and rotate the credential first. Everything else is cleanup; the key or password must be assumed compromised the moment it was pushed.
- Then remove it from history - `git filter-repo`, or BFG - and force-push, because deleting the file in a new commit leaves it fully readable in every earlier commit.
- Then prevent the repeat: `.gitignore` for the file, a secret-scanning hook or CI check, and configuration read from the environment rather than committed.

**Details and nuances**

The order is the whole answer. People reach for the history rewrite first, which takes time, during which the credential is still valid and still public. Rotation is minutes and removes the actual risk.

Force-pushing a rewritten history is disruptive - everyone's clones are now wrong and must reset to the new history - so it needs announcing rather than doing quietly. And on a public host, assume that forks, caches and anything that mirrored the repository still have the old objects; the rewrite reduces exposure, it does not undo it.

**Example or evidence boundary**

This is a live item in my own public repository: an old CV file containing a home address and date of birth was deleted from the working tree but remains in history and still needs `filter-repo` and a force-push. I would rather describe it accurately than pretend the repository is clean.

[↑ Back to question index](#question-index)

---

# 3. Working With Others

## Question GIT-009

[↑ Back to question index](#question-index)

### Question GIT-009 — Which branching strategy, and why?

**Short answer**

- Trunk-based: everyone integrates into one main line through short-lived branches, with feature flags for anything incomplete. It suits frequent releases and keeps merges small.
- GitFlow: long-lived `develop` and `release` branches with separate hotfix branches. It suits versioned products with supported releases and parallel maintenance.
- The choice follows release cadence, not taste: continuous delivery pushes toward trunk-based, a product shipping twice a year with supported old versions needs the release branches GitFlow provides.

**Details and nuances**

The cost of long-lived branches is worth naming because it is the argument that actually decides it: integration pain grows faster than linearly with branch age, and a two-week branch is not twice the trouble of a one-week branch. That is the reason short-lived branches win wherever they are possible.

Where they are not possible - a platform released roughly twice a year, with fixes needed on versions already at customers - the release branch is not ceremony, it is the only place a fix for last year's release can live.

**Example or evidence boundary**

Production experience: the library platform released approximately twice a year with every change requiring approval from added reviewers on both the delivery and customer sides, which is a very different rhythm from continuous delivery and changes how much risk a single merge is allowed to carry.

[↑ Back to question index](#question-index)

---

## Question GIT-010

[↑ Back to question index](#question-index)

### Question GIT-010 — How do you find the commit that broke something?

**Short answer**

- `git bisect` - mark a known-good and a known-bad commit and Git binary-searches between them, so a thousand commits take about ten tests rather than a thousand.
- It can be fully automated: `git bisect run ./test.sh`, where the script exits zero for good and non-zero for bad, and Git finds the commit unattended.
- Before that, `git log -S'text'` (the pickaxe) finds commits that added or removed a specific string, and `git blame` plus `git log -p <file>` answers "why is this line here".

**Details and nuances**

Bisect has a prerequisite that people forget: every commit in the range must build, or the test script cannot distinguish broken-by-the-bug from broken-by-the-build. Exit code 125 tells bisect to skip a commit it cannot evaluate, which is the escape hatch.

The related point worth making about commit hygiene: bisect works well exactly in proportion to how small and self-contained the commits are. A history of thirty focused commits localises a defect to a few lines; a history of three enormous merges localises it to a week of work. That is a concrete, non-aesthetic argument for the commit discipline that interactive rebase supports.

`git blame` deserves one caveat: it shows who last touched a line, which after a reformatting commit is whoever ran the formatter. `git log --follow` and `blame -w` (ignore whitespace) get past that.

[↑ Back to question index](#question-index)
