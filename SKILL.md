---
name: compactable
description: >-
  Use when a long session hits a lull, a task boundary, or context feels heavy with stale tool output, and the agent must judge whether running /compact now would genuinely help the session rather than just being technically resumable via files. Also use when explicitly asked "should we compact" or "/compactable".
---

# Compactable

## Overview

Being resumable via files is not the same as compacting being a good idea right now. Files preserve facts; compacting destroys whatever reasoning, nuance, or in-flight decisions never made it into a file. This skill judges timing, not resumability, and — if it recommends compacting — externalizes what would otherwise be lost first.

**Core principle:** a compact recommendation needs a reason to compact NOW, not just an absence of reasons not to.

## When to Use

- Proactively, at a natural lull: a task/milestone just finished, a subagent/workflow just returned, or the conversation is between steps with no active back-and-forth.
- On explicit request ("/compactable", "should we compact now?", "is this a good time to compact?").

Do not run this mid-reasoning, mid-tool-call-chain, or while a decision is actively being worked out in-context — that's the condition this skill checks FOR, not a pause point.

## Decision Criteria

Score all four. Compact only when the stale/boundary/economics signals are positive AND the in-flight signal is clear.

| Factor | Favors compacting NOW | Favors NOT YET |
|---|---|---|
| **Tool-output staleness** | Context is dominated by large reads/search results/logs no longer needed for what's next | Recent tool output will likely be re-referenced in the next few turns |
| **In-flight reasoning** | No live multi-step reasoning, partial decision, or nuance that exists only in-context (not yet in a file/plan/memory) | There's a chain of reasoning or a partially-formed decision that hasn't been written down anywhere — compacting would silently drop it even though *other* things are file-resumable |
| **Task boundary** | A milestone/subtask just completed; the next chunk of work is logically independent | Currently mid-task; the next step depends on details only visible in-context |
| **Token economics** | Compacting now plausibly reduces total future tokens spent (heavy stale context isn't worth re-paying to keep) | Compacting now is premature — you'd likely re-derive/re-fetch the same context again soon, paying the cost twice |

The **in-flight reasoning** check is the one that most often gets skipped — "it's all resumable via files" is not the same question as "is anything live only in my head right now." If yes, that's not automatically a blocker: capture it in the continuity note below, then compact.

## Continuity Note (required before recommending COMPACT NOW)

A compact summary reliably keeps facts you wrote down; it's less reliable at preserving *which skills/workflows were active and why*. Before advising compaction, write a short continuity note so nothing gets silently dropped:

1. Which skill(s) or workflow(s) are currently active or mid-execution, and at what step.
2. Any pending next action that isn't yet reflected in a plan/todo/file.
3. Any in-flight decision or nuance flagged by the in-flight-reasoning check above.

Write it to a scratch file (e.g. the session scratchpad) or, if the harness has no scratchpad for this session, state it plainly in your response so it lands in the transcript the compactor summarizes. Skip this step only when the in-flight-reasoning check found nothing live to lose.

## Output

Always give a verdict, not just analysis:

```
Verdict: COMPACT NOW | NOT YET
Reason: <one line, naming the deciding factor(s)>
[if COMPACT NOW: continuity note written to <path> / stated above]
Next: run /compact yourself — this skill can't invoke it directly.
```

**This skill never runs `/compact` itself.** There is no tool to invoke a slash command from inside a skill; `/compact` is a user/CLI-level action (or triggered automatically by the harness near context limits). The verdict is a recommendation for you to act on.

## Common Mistakes

| Mistake | Fix |
|---|---|
| Treating "resumable via files" as sufficient reason to compact | Resumability is a floor, not the criterion. Check in-flight reasoning separately. |
| Recommending compact mid-task because context is large | Size alone isn't the signal — staleness and task boundary are. A large context of *currently relevant* material is not compactable. |
| Skipping the continuity note because "it's all in files anyway" | The note is specifically for what ISN'T in files yet — active skill/workflow state and undocumented in-flight decisions. |
| Assuming the skill can run `/compact` | It can't. Always end with "run /compact yourself." |
