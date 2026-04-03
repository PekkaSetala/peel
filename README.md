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

1. Open your Project → **Settings** → **Knowledge**
2. Upload `SKILL.md`

Peel will activate automatically on diagnostic problems, or on demand with `/peel`.

### Claude Code

```bash
mkdir -p ~/.claude/skills/peel
curl -o ~/.claude/skills/peel/SKILL.md https://raw.githubusercontent.com/PekkaSetala/peel/main/SKILL.md
```

### Cursor

Paste the contents of `SKILL.md` into `.cursor/rules/peel.md` (current) or `.cursorrules` (legacy).

### Windsurf / Copilot Chat

Paste the contents of `SKILL.md` into your workspace rules file:

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

### Framing

```
🧅 Peel → Framing: The problem as stated is a symptom — finding the right question before answering it.

ASSUMPTIONS
"We need more features to grow" → Unproven. Feature volume and growth have no
reliable correlation. Most products fail from poor retention, not thin feature sets.

"Users aren't converting because of the product" → Possibly false. It could be
messaging, pricing, wrong audience, or a positioning problem invisible inside the product.

"We're not growing" → Needs definition. Absolute user count? Revenue? Activation rate?
These have different causes and different fixes.

BARE FACTS
Growth is flat despite shipping. You don't yet know where in the funnel value is
being lost — acquisition, activation, retention, or referral. Until that's known,
everything is guesswork.

LEVERAGE POINT
The 20%: find out where users drop off for the first time. Not why they churn after months —
where they fail to get value in the first session. That's the moment the product either
earns its place or loses it, and it's almost always the last thing teams investigate.

THE REAL PROBLEM
You're shipping to a leaky bucket without knowing where the holes are.
More features fill the bucket faster; they don't fix the holes.

→ Next action: Map activation — what does a user have to do to reach their first
"this is useful" moment, and what percentage of new signups actually get there?
```

---

### Bottleneck

```
🧅 Peel → Bottleneck: The pipeline has volume but deals aren't moving — locating the constraint.

THE FLOW
Leads generated → Qualified → Demo booked → Demo delivered → Proposal sent
→ Legal/procurement → Closed

⚠ THE CONSTRAINT
Legal/procurement. Deals regularly sit here for 4–8 weeks with no movement.
Every other stage takes days. This one takes months.

EXPLOIT IT NOW
Assign someone to own deals in legal — not to rush them, but to maintain internal
champion engagement while procurement runs its process. Champions go cold when
left alone. A weekly "anything I can unblock?" keeps the deal alive.

IF THAT'S NOT ENOUGH
Get a standard order form and MSA pre-approved by your own legal so you're
never the bottleneck. When a customer says "send us your paper," you can.

→ Next action: Pull every deal stuck in legal right now and find the last date
the champion was contacted. Anything over 10 days needs a touchpoint today.
```

---

### Failure (Looking Back)

```
🧅 Peel → Failure (Looking Back): Six months of steady churn with no known cause — tracing the causal chain.

WHAT BROKE
Customer churn has increased steadily for 6 months. No single spike, no obvious trigger.
Customers cite "budget" or "not using it enough" — answers that explain nothing.

WHY? (LAYER 1 — SYMPTOM)
Customers are disengaging weeks before they cancel. By the time they churn,
the decision is already made.

WHY? (LAYER 2 — PROCESS)
There's no touchpoint between onboarding and renewal. Quiet customers —
the highest churn risk — are invisible until they cancel.

WHY? (LAYER 3 — OWNERSHIP)
Customer success is measured on renewals, not on proactive engagement.
The team responds to problems; it doesn't go looking for them.

WHY? (LAYER 4 — INCENTIVES)
Leadership tracks churn rate as an outcome. Nobody is accountable for
leading indicators — login frequency, feature adoption, support volume —
that predict churn weeks in advance.

WHY? (LAYER 5 — STRUCTURE)
Acquisition has a funnel, metrics, and owners. Retention has a number.
The organizational weight is entirely front-loaded; the back end runs on hope.

⚠ ROOT CAUSE
Churn is measured too late to act on. The signal exists in product usage data
but it's nobody's job to watch it. By the time a customer appears in the churn
number, the window to intervene closed weeks earlier.

FIX
Define 2-3 leading indicators from existing data (core feature usage, login gaps,
support frequency). Assign ownership. Make proactive outreach a metric.

→ Next action: Pull the last 20 churned accounts and find the last date each one
used the product's core feature. That gap — disengagement to cancellation —
is your intervention window. Right now you don't know how wide it is.
```

---

### Failure (Looking Forward)

```
🧅 Peel → Failure (Looking Forward): A pricing change is high-leverage and hard to reverse — pressure-testing it before it ships.

WHAT'S SHIPPING
New usage-based pricing launches in 3 weeks, replacing the flat monthly tier.
Success = no net revenue loss in month one, positive NPS delta by month three.

⚠ 1. EXISTING CUSTOMERS FEEL PUNISHED
Likelihood: high | Impact: high
Power users who chose you for predictable costs will see bills spike immediately.
They'll churn not because the product got worse but because the deal changed.
Fix now: Grandfather existing customers on current pricing for 6 months minimum.
Announce it before launch, not after the first invoice.

⚠ 2. USAGE IS HARD TO PREDICT, SO SALES CYCLES STALL
Likelihood: high | Impact: medium
Prospects can't budget for what they can't forecast. "It depends on usage"
is a procurement blocker, not a selling point.
Fix now: Build a usage estimator into the pricing page. Give prospects a number
before they have to ask.

⚠ 3. SUPPORT VOLUME SPIKES ON FIRST BILLING CYCLE
Likelihood: medium | Impact: medium
First invoice under new pricing will surprise people regardless of communication.
"Why is my bill $340 this month?" is coming.
Fix now: Prepare a billing explainer and train support before launch,
not after the tickets arrive.

→ Next action: Identify the top 20 accounts by current usage volume and model
what their bill looks like under the new pricing. If any of them are in for a
shock, call them before launch — not after.
```

---

## Files

```
peel/
├── SKILL.md        # The skill itself — install this
└── README.md       # This file
```

---

## License

MIT
