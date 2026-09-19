---
name: marketing-engineering
description: >-
  Profound's Marketing Engineering method for turning a recurring marketing workflow into an
  agent or automation. Use when scoping, designing, building, or debugging any marketing
  agent/pipeline (competitor monitoring, lead routing, AEO/SEO visibility, brand or coverage
  monitoring, reporting, outreach); when deciding what to automate; when writing prompts or
  structured outputs for an LLM step; when setting alert thresholds; or when agent output
  quality stalls.
---

# Marketing Engineering

Source: Profound University course "Marketing Engineering" (4 modules, 15 lessons).
Full lesson notes: [`references/course-notes.md`](references/course-notes.md) — read the matching
section when a step below needs more than its summary.

Core stance: automate the **lights-on** steps so human energy goes to **judgment** steps first.
Measured on marketing KPIs (conversion, traffic, pipeline, cost per lead), not uptime. Two halves
of the job: accelerate the existing playbook, then invent capabilities that did not exist.

"Node" below means one step of the system: a workflow-builder node, or a function / script stage
when building in code. The method is the same.

## Steps

Run in order. Each step ends on its completion criterion.

### 1. Discovery — find the real problem

- Before talking to the stakeholder: write what you think the problem is, and three ways you might be wrong.
- Separate **symptom** ("report takes two days") from **problem** ("leadership decides on stale competitive data"). Build for the problem. First principles: what is actually needed, by whom, when?
- Existing workflow: anchor on the **last real run** ("take me back to the last time — what did you open first?"), stay quiet after asking, decompose every vague verb ("review", "compare", "clean up"), probe the three gaps (before it starts / after it's sent / when it went sideways), two passes: breadth then depth.
- No workflow yet: look for data nobody uses, oversight nobody does, things people assume are impossible. Think horizontally — one team's problem is usually a category.
- Adoption bar: must be 10x better and 2x easier than the current habit. Enthusiasm does not predict adoption.

Done when: the problem is stated as a need (not a symptom), with the definition of "done", tools involved, and how quality is judged.

### 2. Decompose — see it as a system

- List every step with input, output, lights-on vs judgment, and decision points.
- Real step count is 3–4x what people describe. Fewer than 5 steps = still the brochure version. Aim for 10–15.
- Flag judgment steps explicitly — they become human-in-the-loop checkpoints.

Done when: 10–15 steps, each labeled with input, output, and lights-on/judgment.

### 3. Prioritize — Frequency × Effort × Impact

- Frequency: daily/continuous high; weekly worth considering; less than weekly low at start; one-off deprioritize.
- Effort: high-freq + medium-effort ships first; high-freq + high-effort after quick wins; low-freq + high-effort deprioritize.
- Impact: pain eliminated × how often felt; how many people/teams benefit; closeness to revenue metrics.
- Validate every request: "How often are you actually doing this today?" A game-changing pitch with no recurring use is a red flag.

Done when: each idea sits in one bucket — **build first**, **build later**, or **wrong target**.

### 4. Data inventory — where the data lives

- For each tool: specific data points (not "SEO data" but "keywords, positions, clicks by page"), and its channel: **API**, **web scrape**, or **knowledge base read**.
- Live data says what is there; live + stored says what **changed** — the change is almost always what is worth knowing.
- **Change detection pattern:** gather current → read last run's snapshot → LLM compares → overwrite snapshot. The baseline slides forward each run (a single sheet cell is enough).

Done when: every input the system needs has a named source and access channel.

### 5. Design before you build — five blocks

Start from **delivery** (who sees it, where, what format) and work backwards. Classify every step:

| Block | Test |
|---|---|
| Retrieval | get current state of X from *our* systems |
| Research | find X from an *external* source not yet in our systems |
| Reasoning | LLM synthesizes, scores, drafts, judges |
| Logic | branch on what reasoning found (placed right after reasoning) |
| Delivery | send to a person or system (Slack, email, sheet, CMS, webhook) |

Retrieval and research fail differently — do not conflate them. Sketch the whole flow before building (course example: 22-node agent built ad hoc took two weeks and broke; designed first, 30 minutes and stable).

Standard skeleton most agents fit: trigger → 3–4 scoped inputs → data collection (parallel sources allowed) → LLM judgment node returning a boolean + detail fields → conditional → action path (LLM formats report → deliver) / all-clear path (code returns "nothing to report").

Done when: every step is mapped to a block and the delivery format is fixed.

### 6. Build each node

**Code or LLM?** Needs judgment → LLM. Needs to be exact → code. Always code: math, filtering, sorting/ranking, formatting (dates, Slack markdown, @mentions), de-duplication. Code usually sits at the start (clean API responses, compute diffs, filter to significant changes) and the end (build final message). Test generated code on 3–4 items you can verify by hand.

**APIs:** every call = endpoint + parameters (required vs optional) + authentication. Pull reads, push writes back (push makes it operational, not just reporting). 429 rate limit → wait and retry; 404 wrong endpoint; 401 bad/expired key. Read the example response for data shape.

**Prompts** (context → instruction → output):
- Level 1 one-liner is chat-only. Level 2 applies full anatomy. Level 3 chains specialized prompts when one prompt does several jobs (detect, analyze, format).
- Constrain by what not to do ("Do not interpret. Do not summarize what stayed the same.").
- Null case: "If no significant changes, output exactly: No changes detected."
- Permit uncertainty: "If intent cannot be determined, output exactly: Ambiguous."
- Feed real data for every fact; the model's own knowledge is not a source.
- **3 AM test:** runs tonight unwatched — do you trust it? "Probably" / "depends" = gaps.

**Structured outputs:** design fields for what the **next node** needs, not what the LLM analyzes. Boolean → go/no-go. Number → math/thresholds (a real number, not digits in a sentence). Text → a label from categories defined upfront. List → loop/count.

**Variable names:** `source_thing` (`crm_first_name`, `scrape_article_summary`, `llm_email_draft`, `pricing_last_week`, `pricing_diff`). Rename every auto-name. Read-aloud test: a stranger knows what's inside.

Done when: every node has a code/LLM decision, prompts pass the 3 AM test, every LLM output feeding another node is structured, and no default variable names remain.

### 7. Conditionals — the trust layer

Binary "if changed, alert" buries a 40% price spike under typo alerts; people stop reading within weeks. Use tiers, each with trigger condition, channel, and context amount:

1. **Silence** — within normal range
2. **Log to digest** — real, not urgent
3. **Channel summary** — notable and timely for the team
4. **Direct alert to lead** — immediate, full context attached

Set thresholds from domain knowledge ("what would I do if I saw this at 3 a.m.?"), not technical defaults. Every noisy alert drains the **trust account**; an agent at zero isn't broken, it's ignored.

Done when: every finding type maps to exactly one tier with a written threshold.

### 8. Iterate when output is weak

- Write the quality target for the node first.
- Loop: pick the one node most responsible → change **one** thing (prompt, model, input format) → rerun with identical inputs → compare side by side → keep or revert.
- After two failed prompt changes, look upstream. Diagnose:
  - misreads data that is present → **prompt** problem
  - ignores data stripped before the node → **data** problem (fix the cleaning step, not the prompt)
  - weak despite good prompt and data → **structure** problem (split the node)

Done when: the node meets its written quality target on the same inputs.

## Explaining the value

To non-technical stakeholders, hit three pillars: **capacity recovered** (recurring manual work runs itself), **quality ingrained** (consistency independent of who ran it), **new capabilities unlocked** (things infeasible by hand now exist).
