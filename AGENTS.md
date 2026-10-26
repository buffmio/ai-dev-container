# AGENTS.md

Field notes for getting language models to write code that does not need to be
rewritten. Treat these rules as operating instructions for every coding task in
this repository.

## 1. Protect Core Configuration

**Do not modify or delete configuration files or devcontainer setup without permission.**

Never edit or delete `AGENTS.md`, the `.devcontainer/` directory, or any files
in `.devcontainer/` unless explicitly requested by the user.

## 2. Match the User's Language

**Use the same language as the user for all conversation, explanations, and generated text.**

Keep code identifiers, commands, and standard technical terms in English when
appropriate, but conduct all discussions, plans, and summaries in the user's
language.

## 3. Read Before You Write

**Before editing, read the code you are about to touch.**

Follow imports, existing helpers, adjacent tests, and local conventions. Do not
skim just enough to make a plausible change.

When you cannot find a pattern, say so and ask instead of guessing.

## 4. Think Before You Code

**State what you are about to do before you type.**

Name the assumptions, the path you chose, and the tradeoffs you rejected.

If something is confusing, stop and clarify. A visible pause is better than
confident code that only works in the easiest case.

## 5. Prefer Simplicity

**Write the smallest change that solves the problem in front of you.**

Avoid premature abstractions, speculative extensibility, and broad frameworks
that are not required by the current task. Hardcode only when the value truly
belongs to the current behavior and there is no real evidence it should vary.

If an abstraction exists only because "we might need it later," remove it.

## 6. Make Surgical Changes

**Keep diffs as small as the task allows.**

Do not reformat, rename, restructure, or clean up unrelated code while solving
a local problem.

Every changed line should have a reason tied to the request. If a line is only
there because you happened to be nearby, revert it.

## 7. Verify Behavior

**The gap between code that looks correct and code that works is testing.**

For bug fixes, reproduce the failing behavior first, then fix it, then verify
the fix. Add or update tests for behavior that can break. Test outcomes that
users depend on, not incidental implementation details.

If a behavior is hard to test, treat that as design feedback.

## 8. Work From a Clear Goal

**Every task needs a success criterion before implementation starts.**

Define what "done" means in observable terms.

For anything larger than a tiny edit, make a short plan first so the user can
catch a wrong direction early.

## 9. Debug Systematically

**When something breaks, investigate before changing code.**

Read the full error, inspect the stack trace, reproduce the issue, and isolate
the cause one step at a time.

Do not paper over symptoms with null checks, retries, broad catches, or default
values unless you have proven that behavior is correct.

## 10. Treat Dependencies as Permanent

**Every dependency is code the project does not control.**

Before adding one, check whether the project, standard library, or existing
tooling already solves the problem well enough. If a dependency is still the
right choice, explain why in the change.

## 11. Communicate the Work

**Say what changed and why.**

Keep the user informed about uncertainty, blocked items, and verification
results.

"I think this should work" is not enough. Prefer evidence: tests run, behavior
observed, files checked, or constraints confirmed.

## 12. Watch for Common Failure Modes

**Avoid these patterns:**

- Kitchen sink changes: restructuring unrelated code while solving one
  issue.
- Wrong abstraction: extracting too early, then forcing the code to fit.
- Optimistic path: handling only the happy path and ignoring errors or edge
  cases.
- Runaway refactor: expanding the scope because adjacent code looks
  imperfect.

When you notice one of these patterns, stop, narrow the work, and return to the
task's actual success criterion.
