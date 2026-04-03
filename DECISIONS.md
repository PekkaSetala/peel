# Peel — Design Decisions & Development Log

## What Peel is

A structured problem-solving skill for Claude with three modes:

- **Framing** — for undefined or wrong problems (First Principles + 80/20)
- **Bottleneck** — for slow systems or teams (Theory of Constraints)
- **Failure** — for things that broke (5 Whys) or might break (Pre-mortem)

Invoked with `/peel` or triggered automatically when Claude detects a diagnostic problem.

---

## Why it exists

The original motivation: not having to prompt "use 5 whys on this" or "apply theory of constraints" every time. The skill packages these frameworks so they fire automatically on the right type of problem, with a consistent output format that's easy to read.

---

## Version history

### v1 — Original

The starting point was a functional skill with solid framework choices and clear mode selection rules. It worked, but had several gaps identified during review.

### v2 — Current (revised)

Ten specific improvements were identified and implemented. Here's each decision and why it was made.

---

## Design decisions

### 1. Added the 🧅 Peel header

**Problem:** When Peel fired, the user couldn't tell whether they got a structured diagnosis or a regular Claude answer. The output looked like normal prose.

**Decision:** Every Peel response must open with `🧅 Peel → [Mode]: [reason]`. This is non-negotiable in the skill.

**Why it matters:** It's the primary signal that a framework is being applied. It also enables mode shift tracking mid-conversation (`🧅 Mode shift → [New Mode]: [reason]`). Without it, Peel's value is invisible.

**Testing note:** Haiku skipped the header on its first response but included it on subsequent ones. If this becomes a pattern, the skill may need stronger language forcing the header as the absolute first line.

### 2. Added "When NOT to use Peel"

**Problem:** The original skill had no exclusion criteria. It could trigger on simple questions like "how do I center a div" and feel heavy-handed.

**Decision:** Added explicit exclusion rules: skip Peel for how-to questions, obvious fixes, syntax lookups, and problems where the user knows what they want.

**Why it matters:** A skill that overfires loses trust. The user should feel like Peel activates when it's genuinely useful, not on every question.

### 3. Added severity calibration

**Problem:** A production outage and a slow CI pipeline both got the same depth of analysis. Peel felt disproportionate on small problems.

**Decision:** Three severity levels — critical (full treatment), moderate (standard depth), minor (light touch). Default to moderate when unsure.

**Why it matters:** Prevents Peel from producing a 500-word analysis of a minor annoyance. Scales effort to stakes.

### 4. Added information-gathering step

**Problem:** The original skill said "proceed without waiting for confirmation unless the problem is genuinely ambiguous" but gave no guidance on what to do when there wasn't enough information to diagnose.

**Decision:** Explicit step: if the problem is too thin, ask up to 3 targeted questions before proceeding.

**Why it matters:** Prevents bad diagnoses from insufficient context. Testing confirmed this works well — both Sonnet and Haiku correctly asked questions before committing when the prompt was vague.

### 5. Added correction handling

**Problem:** Peel is opinionated — it picks a mode and commits. The original skill had no guidance for when the user says "that's wrong."

**Decision:** Three-step recovery: don't defend reflexively, ask what the user is seeing, re-run with new information.

**Why it matters:** An opinionated tool needs a graceful reversal path. Without it, wrong diagnoses become arguments.

### 6. Added multi-turn support

**Problem:** The original skill assumed single-shot diagnosis. Real problems evolve — a Bottleneck investigation might reveal a Framing problem underneath.

**Decision:** Explicit mode shift syntax and guidance to carry forward context rather than restarting.

**Why it matters:** Problems don't respect frameworks. The skill needs to follow the problem wherever it goes.

### 7. Made 5 Whys flexible depth

**Problem:** The original prescribed five specific levels (technical → system → process → organizational → root cause). This forced depth even when the root cause was genuinely technical at layer 2.

**Decision:** "Ask why as many times as the problem requires." Stop at the real root cause, whether that's 2 layers or 5.

**Why it matters:** Forced depth produces padding, which directly contradicts the "never pad" rule. The goal is the root cause, not a fixed number of whys.

### 8. Removed "software development contexts"

**Problem:** The original description scoped Peel to software development, but the frameworks are completely general. Theory of Constraints works on any workflow. 5 Whys works on any failure.

**Decision:** Removed the software-specific framing. Examples still skew technical but the skill applies to any domain.

**Why it matters:** Artificially narrowing the audience limits usefulness. The career pivot test ("need to pivot from support engineer to AI engineer") confirmed Framing mode works well on non-software problems.

### 9. Replaced prose output with structured trace format

**Problem:** The original skill said "narrative over report" and instructed Claude to weave analysis into flowing prose. This made outputs hard to scan, especially for someone with dyslexia.

**Decision:** Each mode now has a labeled output template with clear section headers (ASSUMPTIONS, BARE FACTS, LEVERAGE POINT, THE FLOW, THE CONSTRAINT, etc.), whitespace between blocks, and a maximum of 3-4 sentences per section.

**Why this was the biggest change:** The original "narrative over report" instruction was actively working against readability. The structured trace format makes each reasoning step independently visible and challengeable. You can scan just the headers and the next action and get 80% of the value.

**This was the core design insight of the revision:** Peel's differentiator isn't better frameworks (Claude can apply these without a skill). It's decomposed, auditable reasoning — a diagnosis the user can see working and argue with piece by piece.

### 10. Trimmed description to 813 characters

**Problem:** The SKILL.md description field has a 1024 character limit. The initial revision was 1043 characters.

**Decision:** Cut filler phrases, tightened wording. Landed at 813 characters with room to spare.

---

## Testing results

### Framing mode (Sonnet) — "My team keeps shipping features but our metrics aren't moving"

- Without Peel: decent brainstorm list, asks follow-up question, no clear diagnosis
- With Peel v1 (prose format): sharp diagnosis, good assumptions, but delivered as dense paragraphs
- With Peel v2 (structured trace): same quality diagnosis, scannable format, each section independently readable

The v1 → v2 comparison was the clearest validation that the format change matters.

### Framing mode (Sonnet) — "Need to pivot from support engineer to AI engineer"

- Correctly identified as Framing
- Good assumptions (challenged "AI engineer = ML", reframed support engineering experience as relevant)
- Specific, actionable next step
- Confirmed the skill works on non-software problems

### Bottleneck mode (Sonnet) — "We have 6 engineers but it feels like we ship at the pace of 2"

- Correctly identified as Bottleneck
- Asked targeted questions before diagnosing (correct behavior — not enough info to map the flow)
- Questions were specific and useful, not generic

### Failure mode (Haiku) — "Third payment processing outage this quarter"

- First response: asked good questions but **missed the Peel header**
- Second response (after follow-up): header appeared, chose Framing instead of Failure (Looking Back)
- Output format was correct — labels, short sections, bare facts were actually bare
- Diagnosis was reasonable but mode selection was debatable

**Haiku observations:** Follows the format, asks questions when it should, but judgment calls are softer than Sonnet's. The header may need stronger enforcement language for smaller models. Mode selection was less precise — it chose the safer/more abstract mode rather than committing to the specific one.

---

## Known limitations & future considerations

**Auto-triggering is inconsistent.** The skill doesn't trigger reliably on problem descriptions alone. `/peel` is the reliable invocation method. This is a platform-level limitation more than a skill design issue.

**Haiku header compliance.** Haiku skipped the header on its first response in testing. May need "THE VERY FIRST LINE must be the Peel header, no exceptions" if this persists.

**Haiku mode selection.** Haiku chose Framing for a problem that was arguably Failure (Looking Back). The mode selection rules may need to be more prescriptive for smaller models — less "use judgment" and more "if X then Y."

**BARE FACTS section drift.** In testing, Claude sometimes put analysis into the BARE FACTS section instead of raw facts. The skill says "what remains after stripping the assumptions" but models tend to interpret this loosely. Could add: "BARE FACTS contains only observable, unchallengeable statements. No interpretation."

**Untested modes.** Bottleneck was tested only up to the question-asking stage (no full structured trace output). Failure (Looking Forward / Pre-mortem) was not tested at all. Both should be exercised on real problems.

**Cross-platform sync.** Skills don't sync between Claude.ai and Claude Code. To use Peel in Claude Code, manually install at `~/.claude/skills/peel/SKILL.md`.

**Model-size positioning.** Larger models benefit from Peel's auditability and structured output. Smaller models (Haiku) benefit more fundamentally — the structure compensates for weaker reasoning by enforcing step-by-step discipline. Worth exploring whether the skill needs a lightweight mode for smaller models, or whether the structure alone is sufficient.
