# munera

munera is a git-native markdown task-management protocol for AI coding agents. This document specifies mechanics: the layout of files, what each one means, what's permitted and what isn't. It deliberately does not specify process — who writes what, who reviews, how transitions happen. Process is the user's to impose.

Most rules are plain imperatives; a few load-bearing ones use RFC 2119 keywords (**MUST**, **SHOULD**, **MAY**) for emphasis and appear in blockquotes.

---

## 1. The directory

A munera-enabled repository has a directory named `munera/` at its root.

> `munera/` **MUST** contain a file named `plan.md`.
> `munera/` **SHOULD** contain directories named `open/` and `closed/`.

Other files and directories are permitted. Agents leave unknown files alone — don't parse them, don't delete them.

---

## 2. A task is a directory

Each task is a directory under `open/` (active) or `closed/` (completed or abandoned). Transitioning between states is literally a `git mv` between those two parents. State is location.

Task directory names combine a sequential number and a human slug, in the form `NNN-slug`:

- `NNN` is a non-negative integer, zero-padded to at least three digits.
- `slug` is kebab-case: lowercase letters, digits, and hyphens.

IDs are allocated by convention: use `max(existing NNN) + 1` across `open/` and `closed/` combined. Concurrent branches may produce duplicates. Rename one of the colliding directories — don't attempt to merge task contents.

Inside each task directory, four files form the canonical record of a human-agent conversation about that piece of work:

> A task directory **MUST** contain all four files before execution begins:
>
> - `design.md` — what and why (goal, context, constraints, acceptance).
> - `plan.md` — how (approach, decisions, risks) — decided before execution.
> - `steps.md` — do (a working checklist).
> - `implementation.md` — decisions and discoveries recorded during execution; the task's local memory.

A task directory may also contain other `.md` files (notes, transcripts, artefacts). Agents preserve them.

The four-file split isn't decorative. It reflects how work unfolds in time: agree on *what*, then on *how*, then *execute*, leaving a trail of what was actually decided along the way. Each file is useful at a different moment and churns at a different rate — design stabilises early, plan is set before implementation begins, steps update continuously as the checklist is worked, and implementation grows append-only as discoveries surface.

`plan.md` and `implementation.md` both record decisions, but at different stages. `plan.md` is the decided approach *going in*, and changes when the approach itself changes. `implementation.md` is the record of decisions *taken in flight* — the small choices, discoveries, and trade-offs that surfaced during execution and are worth preserving for future reference. Entries in `implementation.md` should be self-identifying (a date, a commit SHA, or a reference to the step they belong to) so their context is recoverable later.

---

## 3. Files are plain markdown

No frontmatter. No required section headings. munera names the files; what goes in them is up to the humans and agents who write them.

`steps.md` has a weak convention, because it is the working surface during execution: a markdown checklist using standard `- [ ]` and `- [x]`. Checklist items may carry indented prose, links, or nested sub-items beneath them — the common pattern is to tick an item and leave a short note (a commit SHA, a decision, a snag) below it.

---

## 4. `plan.md` orders the work

`munera/plan.md` is the orchestration file. Its format is deliberately open — a bulleted list, a narrative, a table, or a mix — but its weight is an ordered list of references to tasks in `open/`, in intended work order.

`open/` is canonical. `plan.md` curates *how* and *when* open tasks should be worked; it may reorder, group, or omit. A task directory may exist without a reference in `plan.md` — such a task is unordered, but still open.

An agent beginning work in a munera repository should read `plan.md` to orient itself before acting. This is a recommendation, not a rule — the spec imposes no session-entry obligation.

---

## 5. State is location

There is no `in-progress`. There is no `blocked`. A task is in `open/` or it is in `closed/`. Progress within a task lives in `steps.md`; obstacles live in task files as prose.

`closed/` holds both completed and abandoned tasks. The spec makes no distinction between those outcomes; the reason, if recorded, lives inside the task.

---

## 6. Evolution

Task files evolve by being edited. The spec defines no amendment files, revision markers, or changelogs — git's ordinary diff and log are the history.

> Task directories **MUST NOT** contain subtask directories.
> Task contents **MUST NOT** be merged across tasks.

Tasks do not split, merge, or nest. When scope drifts beyond the original design, the available mechanic is to close the existing task and create a new one.
