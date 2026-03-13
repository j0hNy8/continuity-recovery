---
name: continuity-recovery
description: "Recover session state after context compaction, quota-pruning, truncation, or session boundary. Use when: (1) tool output shows [compacted], [truncated], or [Memory Pruned for Quota Protection], (2) a new session continues multi-step work from a prior session, (3) context-loss symptoms suggest silent cache/TTL pruning, or (4) critical volatile state risks being lost before a long execution or response. NOT for: routine memory writeback (use session-writeback), correction capture (use self-calibration), normal session startup (handled by AGENTS.md), or platform memoryFlush that already persists state."
---

# Continuity Recovery

Recover working state from canonical sources when context is lost or at risk. Do not reconstruct from guesses.

## Triggers

### T1 — Explicit Context-Loss Marker
Tool output contains a literal context-loss marker such as `[compacted: ...]`, `[truncated: ...]`, or `[Memory Pruned for Quota Protection]`.

### T2 — Cross-Session Continuation
A new session must resume multi-step work started in a prior session **and** the prior session's state is not already loaded via AGENTS.md startup.

### T3 — Silent Pruning Suspected
Fire when context clearly looks partially missing even without an explicit marker: missing recent tool results, vanished working assumptions, or abrupt loss of active-task state consistent with cache/TTL pruning.

### T4 — Pre-Loss Checkpoint
Fire **only** when all three conditions hold:
1. A tool call or execution sequence is about to run that will likely cause compaction (large output, many steps).
2. Volatile state exists that is not yet persisted anywhere (decisions, intermediate results, partial plans).
3. That state cannot be recovered from files, code, or existing memory after compaction.

If any condition is false, skip. Do not checkpoint "just in case."

## Recovery Scope

Prefer the narrowest relevant source scope.
- Recover project-specific state from project files first.
- Recover cross-project durable rules from topic memory.
- Use session narrative only when operational sources are insufficient.
- Do **not** recover from broad transcript history when a project-local file already contains stronger state.

## Recovery Procedure (T1, T2 & T3)

### Step 1 — Identify Gaps
Review available context. Determine: what was the task, what stage was reached, what decisions were made, what is missing.

### Step 2 — Recover by Task Type

**Code / file edits:** Read the target files directly. File state is ground truth. Check git status/diff if the edit may be partial.

**Ops / infrastructure:** Read relevant configs, service state, or command output. Check daily note for recent decisions.

**Research / analysis:** Daily note for conclusions reached. Topic files for durable findings. Source URLs if bookmarked.

**Multi-step workflow:** Daily note for last checkpoint. Then source files for current state.

### Step 3 — Source Priority (all task types)
1. Source files, configs, code — ground truth
2. Daily note (`memory/YYYY-MM-DD.md`) — recent session state
3. Topic files (`memory/*.md`) — durable facts
4. Session transcripts — **last resort only** (see Transcript Rules below)

### Step 4 — Targeted Reads Only
Use offset/limit, grep, head/tail. Do not reload entire files speculatively.

### Step 5 — Resume from Last Verified State
Resume from the last checkpoint that can be confirmed from sources, not from where the narrative left off. If a fact cannot be recovered, state the gap and ask.

## Pre-Loss Checkpoint (T4)

Write a **single compact block** appended to the daily note:

```md
- HH:MM checkpoint: [task summary in one line]
  - decisions: [bullets]
  - next: [remaining steps]
```

Rules:
- One checkpoint per task, not per step. Overwrite the previous checkpoint for the same task if still in the daily note's last few lines.
- Do not capture tool output that remains in files, obvious context, or state already in memory.
- Use this only for volatile operational state that would otherwise be lost; do not duplicate ordinary session-writeback or platform memoryFlush behavior.
- Follow session-writeback daily note rules (append-only, compact, factual).
- If the task completes normally, the checkpoint is superseded by proper writeback — it is temporary operational state.

## Transcript Rules

Session transcripts (`memory/sessions/`) are expensive, noisy, and may contain stale reasoning.

- **Use only when** source files + daily note + topic files cannot recover the needed state.
- **Search narrowly:** grep for task-specific keywords, do not scan full transcripts.
- **Extract facts only:** ignore reasoning, hedging, and abandoned approaches in transcripts.
- **Never treat transcript content as authoritative** if it conflicts with current file state.

## Constraints

- This skill does not replace session-writeback (end-of-task distillation), self-calibration (correction capture), or platform memoryFlush/persistence flows.
- Use pre-loss checkpoints only when continuity risk is real and recovery from canonical sources would be materially worse.
- Do not create buffer files, shadow memory, or dedicated checkpoint files outside the daily note.
- Do not use recovery as license for unbounded file scanning.
- Checkpoints are temporary. They do not count as durable memory.
