# munera

Git-native markdown task management for AI coding agents.

## The problem

An AI coding agent working on a serious project produces more ambient state than a chat window can hold. Every piece of work has intent, an approach, a running checklist, and a trail of small decisions. When the session ends, that state has to land somewhere — otherwise the next session starts from chat-log archaeology.

Existing tools either live outside the repository (GitHub Issues, Linear, Jira — external, networked, not structured for LLMs to read directly), or bring heavy runtime (databases, MCP servers, embedding indices). For many projects, neither trade-off is right. What you actually want is *a directory of markdown in the repo* — tight enough that the agent can navigate it, loose enough that humans edit it without ceremony.

munera is that directory, and the conventions that make it work.

## Shape

```
myrepo/
  munera/
    plan.md
    open/
      042-add-oauth-login/
        design.md          # what & why
        plan.md            # how
        steps.md           # do
        implementation.md  # decisions taken in flight
    closed/
      041-setup-ci/
        design.md
        plan.md
        steps.md
        implementation.md
  src/
  ...
```

- `plan.md` is the orchestration layer — open tasks, in work order.
- Each task is a directory. Inside, four files capture the conversation about the work: *design* (what and why), *plan* (how), *steps* (a checklist), *implementation* (decisions recorded as they're taken).
- State is where a task directory lives: `open/` or `closed/`.
- Transitions are `git mv`.
- Evolution is in-place editing.
- History is git log.

No server. No CLI. No index.

## Install

munera has no runtime to install. There are two steps:

1. **Create the directory structure** in your repository — `munera/plan.md` and the `open/` and `closed/` subdirectories.

2. **Give your agent the spec.** Copy the contents of either [`MUNERA-PROSE.md`](MUNERA-PROSE.md) (plain English) or [`MUNERA-LAMBDA.md`](MUNERA-LAMBDA.md) (compact lambda notation) into your project's agent context file — typically `AGENTS.md`, `CLAUDE.md`, or equivalent. This is how the agent learns the protocol. Without it, the directory is just files.

Choose prose or lambda based on your agent and your preference. Both are normatively equivalent.

## Process is yours

munera defines mechanics, not process. The spec describes the shape of the directory, what each file means, and what `open/` vs `closed/` imply — nothing about how humans and agents collaborate around those artefacts. That's deliberate: you're free to impose whatever process fits your team, your trust level with the agent, or your review pipeline. PR-gated, fully autonomous, human-driven with the agent as executor, propose-and-confirm — all work against the same files.

## A working default

If you want somewhere to start, this pattern works well:

- The human and the agent co-author the task files the way two people co-author a spec — the conversation lands on disk as it happens.
- At session start, the agent reads `munera/plan.md` to orient. The human designates which task is in focus. The agent opens that task's files.
- The agent works through `steps.md`, appending to `implementation.md` as decisions surface in flight.
- For three kinds of actions — creating a task, moving a task between `open/` and `closed/`, or substantially rewriting `design.md` or `plan.md` — one party proposes, the other confirms, the agent executes. Routine edits don't need ceremony.
- When work is done, the agent proposes closure. The human confirms. The agent `git mv`'s it to `closed/`.

Adopt, adapt, or ignore.

## Non-goals

- **Memory and knowledge.** Memory scoped to a task lives in its `implementation.md`; memory that spans tasks is out of scope. Pair with a memory protocol such as [mementum](https://github.com/michaelwhitford/mementum) for cross-session knowledge.
- **Search and indexing.** Use `rg`, `grep`, and your editor.
- **Prioritization engines.** Ordering is whatever `plan.md` says.
- **Multi-repo coordination.** One munera per repo.
- **Runtime tooling.** No CLI, no server, no daemon.

## Spec

See [MUNERA.md](MUNERA.md) for the normative rules.
