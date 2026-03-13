# continuity-recovery
Context survival and state recovery for OpenClaw. Restores operational continuity after truncation, compaction, or session resets using strict source hierarchies without hallucinating state.

Continuity Recovery
Restores working state when context windows compact or sessions reset. It prevents the agent from guessing its previous state or reading bloated chat transcripts.

Core Mechanics:

Pre-Loss Checkpoints (T3): Appends a compact state summary to the daily note before executing high-token operations likely to cause context loss.

Strict Source Priority: Recovers state using a rigid hierarchy: ground-truth files/code first, then daily notes, then topic memory. Transcripts are used only as a last resort.

Namespace Isolation: Prefers project-local files over global memory to ensure recovered context remains tightly scoped to the active task.
