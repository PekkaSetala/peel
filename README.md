# 🧅 Peel

A structured problem-solving skill for LLMs. Peel removes layers — assumptions, symptoms, proximate causes — until the real problem is exposed.

Three modes. One concrete next step. Every time.

---

## Modes

| Mode | Use when |
|---|---|
| **Framing** | The problem isn't defined yet, or you're not sure you're solving the right one |
| **Bottleneck** | Something is running but too slowly — a system, pipeline, team, or query |
| **Failure (Looking Back)** | Something broke and you need to know why |
| **Failure (Looking Forward)** | Something is about to ship and you're anxious about what could go wrong |

Peel picks the mode automatically based on how the problem is described. You can also invoke it explicitly with `/peel`.

---

## How it works

Peel applies established frameworks — First Principles, 80/20, Theory of Constraints, 5 Whys, Pre-mortem — mapped to the right mode. The output is a structured trace: each reasoning step labeled, whitespace-separated, independently readable and challengeable.

Not a consultant's deck. Not a brainstorm list. A diagnosis.

---

## Installation

### Claude.ai (Projects)

Add `SKILL.md` to your Claude Project's context. Peel will activate automatically on diagnostic problems, or on demand with `/peel`.

### Claude Code

```bash
mkdir -p ~/.claude/skills/peel
cp SKILL.md ~/.claude/skills/peel/SKILL.md
```

### Cursor / Windsurf / Copilot Chat

Paste the contents of `SKILL.md` into your workspace rules file:

- **Cursor**: `.cursor/rules` or `.cursorrules`
- **Windsurf**: `.windsurfrules`
- **GitHub Copilot**: `.github/copilot-instructions.md`

### ChatGPT / OpenAI Codex

Paste `SKILL.md` as the system prompt, or add it to your [Custom Instructions](https://help.openai.com/en/articles/8096356-custom-instructions-for-chatgpt) under "What would you like ChatGPT to know about you?"

### Any LLM with a system prompt

Paste the full contents of `SKILL.md` as the system prompt. Peel has no external dependencies — it's pure instruction.

---

## Usage

Invoke explicitly:

```
/peel our API latency doubled after last week's deploy and we don't know why
```

Or just describe the problem naturally — Peel will activate when it detects a diagnostic situation (symptoms without a clear cause, uncertainty about direction, recurring failures, pre-launch anxiety).

---

## Example output

```
🧅 Peel → Failure (Looking Back): A recurring production issue — tracing it to root cause.

WHAT BROKE
Payment processing failed 3 times this quarter, each time during peak load on Friday afternoons.

WHY? (LAYER 1 — TECHNICAL)
The payment gateway times out after 30s. Under peak load, response times exceed this threshold.

WHY? (LAYER 2 — SYSTEM)
The timeout value was set when traffic was 40% of current volume and was never revisited.

WHY? (LAYER 3 — PROCESS)
There's no review trigger for timeout configurations when traffic scales. They're set once and forgotten.

⚠ ROOT CAUSE
Configuration debt + no scaling review process. The timeout is a symptom; the missing review loop is the cause.

FIX
Establish a quarterly review of all timeout and threshold configurations tied to traffic metrics. Adjust the gateway timeout as a short-term fix, but that alone doesn't prevent the next instance.

→ Next action: Open a ticket to audit all service timeouts against current p95 traffic levels.
```

---

## Design notes

The full rationale behind every design decision — why structured trace over prose, why severity calibration, why the mode shift syntax — is in [`DECISIONS.md`](./DECISIONS.md).

---

## Files

```
peel/
├── SKILL.md        # The skill itself — install this
├── DECISIONS.md    # Design decisions and development log
└── README.md       # This file
```

---

## License

MIT
