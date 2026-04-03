# Contributing to Peel

Peel is a prompt-based skill, not a codebase. Contributing means proposing changes to how an LLM is instructed to think — which requires a different standard of evidence than a typical software PR. An instruction change that looks reasonable on paper can perform badly in practice, and vice versa.

The bar for a contribution is: **test it, document it, show your work.**

---

## What can be contributed

**Improvements to existing modes**
Tightening instructions, fixing output format drift, improving mode selection rules, making the skill more robust on smaller models.

**New platform installation instructions**
If you've verified Peel works on a platform not listed in the README, add the install path.

**Testing results**
Run Peel on a real problem, document what happened — what mode fired, whether the output was useful, where it fell short. Testing reports are valuable contributions even without a code change.

**Bug reports**
If Peel consistently misbehaves on a certain class of problem or model, open an issue with a reproducible example.

**New modes**
High bar — see below.

---

## How to propose a change

### For small changes (wording, formatting, installation instructions)

Open a PR directly. In the PR description, explain what problem the change solves and whether you tested it.

### For substantive changes (mode logic, output format, selection rules)

1. Open an issue first and describe what you want to change and why
2. Get rough agreement before investing in a full PR
3. Include test results in the PR (see Testing below)

### For new modes

Open an issue before writing anything. New modes need a clear answer to:
- What class of problem does this mode address that the existing three don't?
- Which framework(s) does it apply, and why are they the right fit?
- What's the output format?

Peel's value is in being opinionated and focused. A new mode that duplicates existing coverage or adds vague "general thinking" frameworks will be declined.

---

## Testing standard

Changes to `SKILL.md` must be tested against at least one real problem — not a toy example, a problem where the answer isn't already obvious.

A test report should include:

- **Model tested**: e.g. Claude Sonnet 4.5, GPT-4o
- **Problem description**: what you gave it
- **Expected behavior**: what mode should fire, what the output should look like
- **Actual behavior**: what actually happened, verbatim or summarized
- **Pass/fail and why**

If the change is meant to fix a specific failure, include a before/after comparison.

You don't need to test every model. Test on what you have access to. If you only tested on one model, say so — that's still useful information.

---

## What belongs in DECISIONS.md

`DECISIONS.md` is the design log for Peel. If your PR makes a non-obvious design decision — something where a reasonable person might wonder "why did they do it this way?" — add an entry explaining the reasoning.

Format loosely follows the existing entries: what problem it solves, what was decided, why.

---

## Style

`SKILL.md` instructions should be:

- **Direct and unambiguous.** The model executing these instructions may be smaller or less capable than the one you tested on. Prefer explicit rules over judgment calls where possible.
- **Short.** Every sentence in the skill costs context window. If an instruction can be cut without losing meaning, cut it.
- **Testable.** If you can't write a test that would fail if the instruction were removed, question whether it's pulling its weight.

---

## Issues

Use issues for:
- Reporting consistent misbehavior with a reproducible example
- Proposing substantive changes before investing in a PR
- Documenting test results even without a proposed fix

Label suggestions: `bug`, `model-behavior`, `new-mode`, `platform`, `testing`.

---

## What won't be accepted

- Changes that haven't been tested
- New modes without a clear gap analysis
- Instructions that are vague or rely on "use good judgment" where a rule is possible
- Padding — adding content that increases length without improving behavior
