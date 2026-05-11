---
name: stacked-diffs
description: Guidance for planning and using stacked diffs with Graphite. Use when creating a stack, splitting a large code change into reviewer-friendly diffs, deciding diff boundaries, preserving git move semantics, or explaining stacked-diff anti-patterns.
---

Stacked diffs are table stakes because writing code is easy, especially with LLMs. Reading and reviewing code remains hard. Stacked diffs help to structure changes in a reviewer-friendly way.

## How to think about stacked diffs

When creating a stacked diff, you should think about what you are trying to achieve. The goal of the whole stack is to achieve this goal. The goal of a single diff inside the stack is to achieve an atomic piece of progress towards the goal.

If possible, any change in a stack should be mergeable as-is. For example, when adding a new lint rule, the pattern should be fixed in the first diff and the lint rule added in the second diff. Why even separate diffs in this example? Because the reviewer can choose a different mode for the different diffs. "configuration-checking" mode for the lint rule and "scanning-similar-changes" mode for the code changes.

Each diff should have a clear single review purpose. The diff description meant for the reviewer should be 1-3 sentences. If not mergeable as-is, the description should explain why it isn't mergeable and how much of the stack must be merged instead.

Agents should plan the shape of their stack before starting the work. It may change over the course of the work, but an initial plan builds stronger stacks. The shape of the stack can be different from the implementation plan.

## Common patterns for stacked diffs

This is a non-exhaustive list of common stacked diffs patterns.

**Per domain piece**
Every diff in the stack applies the same change to a different domain. Especially useful in monorepo's when a pattern change needs to be applied to multiple packages/apps.

**Deploy order is import**
Stacked diffs can be merged separately. When multiple production deploys are necessary, we can structure these changes in a stack where the different diffs correspond to different deploys. E.a. when the order needs to be "merge diff1" -> "deploy diff1" -> "merge diff2" -> "deploy diff2" -> "merge diff3" -> "deploy diff3"

**Preparatory -> config change**
This is the lint rule & code change example from before.

**Refactor then reuse**
When a component or module is currently specific, but we need it in a new place. Diff 1 refactors the module into an abstracted version. Diff 2 then uses the abstracted version of the module in a new place.

**Preserving git move semantics**
When renaming a file, it is almost always best to do that in a separate diff. If a file has too many changes, git no longer sees it as a rename, breaking the semantic history for that file.

**Unit tests then refactor**
When a module needs to be refactored that has little unit tests, it benefits from first adding the unit tests. The unit test diff is then a good review unit to check the intent of the module.

**New features**
When creating new features, there are two broad patterns:

1. Separate diffs for prerequisite files necessary to add the full feature. The last diff then uses all the pieces to assemble the complete feature.
2. Create an incredibly small version of the feature, then build it out with the more complicated components. If the small version shouldn't be in production, it can be gated using a feature flag.

## Anti-patterns

**One big diff**
This is not reasonably reviewable.

**Splitting by file type instead of for review**
That goal of stacked diffs is to make reviewing as straightfoward as possible.

**Mixing different concepts in one diff**
For example, mixing a refactor with a feature change. These become hard to review, because a refactor requires a different thinking mode than adding a feature.

**A later diff fixing oversights in an earlier diff**
Instead, the earlier diff should be modified. Later diffs may extend earlier work, but should not fix mistakes that belong in the earlier diff.
