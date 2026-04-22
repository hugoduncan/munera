# munera

## Protocol

Munera is a protocol, not an implementation. It is git-native, markdown-based, and designed as a task protocol for AI coding agents. The spec covers mechanics only — it does not cover process. Process is for the user to define.

---

## Repository Layout

The `munera/` directory lives at the repository root.

- It **must** contain a file named `munera/plan.md`.
- It **should** contain directories named `munera/open/` and `munera/closed/`.

Unknown files found in `munera/` must be preserved. Agents must not parse them or delete them.

---

## Tasks

A task is a directory located under either `munera/open/` or `munera/closed/`. State is location — a task's position in the directory tree is its entire state. Transitioning between states is a `git mv` between `open/` and `closed/`, and must also remove the task's reference from `munera/plan.md`.

---

## Task IDs

Task directory names take the form `NNN-slug`:

- `NNN` is a non-negative integer, zero-padded to at least three digits.
- `slug` is kebab-case: lowercase letters, digits, and hyphens.

IDs are allocated by taking the maximum existing `NNN` across both `open/` and `closed/`, then adding one. Concurrent branches may produce collisions; resolve them by renaming one of the colliding directories. Never merge task contents.

---

## Task Directory Contents

Before execution begins, a task directory must contain all four of the following files:

| File | Purpose |
|---|---|
| `design.md` | What and why — goal, context, constraints, acceptance criteria. |
| `plan.md` | How — approach, decisions, risks. Written and decided before execution begins. |
| `steps.md` | Do — a working checklist. Updated continuously during execution. |
| `implementation.md` | Decisions and discoveries recorded in flight. Append-only. The task's local memory. |

Additional `.md` files are permitted and must be preserved by agents.

---

## Temporal Split

Each file serves a different moment in a task's life:

- **`design.md`** stabilises early and rarely changes after agreement.
- **`plan.md`** is set before execution; it changes only when the approach itself changes.
- **`steps.md`** is the active surface during execution, updated continuously as the checklist is worked.
- **`implementation.md`** captures in-flight decisions, discoveries, and trade-offs. Entries should be self-identifying — tagged with a date, a commit SHA, or a reference to the step they belong to — so their context remains recoverable later.

---

## File Format

Task files are plain markdown. No frontmatter. No required section headings. Munera names the files; what goes inside them is up to the humans and agents who write them.

---

## `steps.md` Convention

`steps.md` follows a weak convention:

- Checklist items use standard markdown syntax: `- [ ]` for open, `- [x]` for done.
- Items may carry indented prose, links, or nested sub-items beneath them.
- The common pattern is to tick an item and leave a short note below it — a commit SHA, a decision, or a snag.

---

## `munera/plan.md`

`munera/plan.md` is the orchestration file. Its format is open — a bulleted list, a narrative, a table, or a mix — but its role is to hold an ordered list of references to tasks in `open/`, in intended work order.

`open/` is the canonical record of active tasks. `plan.md` curates how and when those tasks should be worked; it may reorder, group, or omit. A task may exist in `open/` without appearing in `plan.md` — it remains open, just unordered.

An agent beginning work should read `plan.md` to orient itself. This is a recommendation, not a rule.

---

## State

A task is either `open/` or `closed/`. There is no in-progress state. There is no blocked state.

- Progress within a task lives in `steps.md`.
- Obstacles are recorded as prose inside task files.

`closed/` holds both completed and abandoned tasks. The spec makes no distinction between those outcomes. The reason for closure, if recorded, lives inside the task.

---

## Evolution

Task files evolve by being edited in place. There are no amendment files, revision markers, or changelogs. History is git's ordinary diff and log.

Task directories must not contain subtask directories. Task contents must not be merged across tasks. Tasks do not split, merge, or nest. When scope drifts beyond the original design, the correct mechanic is to close the existing task and open a new one.

---

## Orientation

When handling any general orientation request, read `munera/plan.md`. It is the task bootloader — the entry point for understanding what work is currently open and in what order it should be approached.

If [mementum](https://github.com/michaelwhitford/mementum) is present in the context, run its orientation step first. Munera's orientation follows after.
