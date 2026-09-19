# marketing-engineering skill

An AI coding agent skill that turns the lessons from Profound University's
[Marketing Engineering](https://university.tryprofound.com/courses/marketing-engineering-concepts)
course into a working method the agent follows on every marketing build.

## Why this skill exists

I finished the Marketing Engineering course and wanted its lessons applied to every
marketing engineering project I build, not just remembered from one read-through.

Without it, agents build marketing automations straight from the request:

- they automate the symptom someone described instead of the real problem,
- they wire every step through an LLM, including the math that should be code,
- they fire an alert on every change until nobody reads the channel,
- and when output is weak they keep rewording the prompt, when the real problem
  may be the data or a step doing too much.

The course gives a repeatable answer to each of those. Packaging it as a skill means
coding agents pick it up whenever the task is a marketing agent or pipeline:
competitor monitoring, lead routing, AEO/SEO visibility, brand or coverage
monitoring, reporting and outreach. The same discovery, design and quality bar then
applies every time.

## What's inside

- `SKILL.md` has eight steps, each with a "done when" check: discovery, breaking the
  workflow down, prioritising (frequency × effort × impact), data inventory,
  designing with five building blocks, building each step, tiered alerts, and
  iterating when output is weak.
- `references/course-notes.md` has the published notes for all 15 lessons,
  grouped by module. The agent reads it when a step needs more detail. Video
  transcripts are not included.

## Install

```bash
git clone https://github.com/salmanbareesh/marketing-engineering-skill ~/.claude/skills/marketing-engineering

git clone https://github.com/salmanbareesh/marketing-engineering-skill ~/.codex/skills/marketing-engineering
```

## Credit

Course content © Profound. This repo is a personal study aid built from their
lesson notes.
