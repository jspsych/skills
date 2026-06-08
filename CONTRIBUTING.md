# Contributing

Thanks for your interest in contributing a skill for working with jsPsych! This repository collects [Agent Skills](https://agentskills.io) that teach AI coding agents (Claude Code, Cursor, Copilot, and others) how to do jsPsych-related work well. Contributions are welcome via pull request.

## What makes a good skill here

A skill should capture **reusable, non-obvious knowledge or a repeatable procedure** for jsPsych development — the kind of thing you'd otherwise paste into a chat every time. Good candidates:

- Conventions and best practices for a jsPsych task (building plugins, writing timelines, data handling, deployment).
- Multi-step procedures an agent should follow consistently.
- Domain knowledge that helps an agent make the right call (e.g. when to build a plugin vs. reuse one).

Out of scope: general programming advice not specific to jsPsych, or one-off instructions with no reuse value.

Before proposing a new skill, check that an existing skill doesn't already cover the area — extending one is often better than adding another.

## Skill format

Each skill is a self-contained folder under [`skills/`](skills/) containing a `SKILL.md` file, following the [Agent Skills](https://agentskills.io) open standard:

```text
skills/
└── my-skill/
    ├── SKILL.md          # required: frontmatter + instructions
    ├── reference.md      # optional: detailed docs, loaded only when needed
    └── examples/         # optional: example files, scripts, assets
```

`SKILL.md` has YAML frontmatter followed by markdown instructions:

```markdown
---
name: my-skill
description: One or two sentences on what this skill does and when to use it. Put the key use case first — agents read this to decide when to load the skill.
---

# My Skill

Instructions the agent follows when this skill is active...
```

### Frontmatter

- **`name`** (required) — lowercase, hyphen-separated. Should match the folder name.
- **`description`** (required) — what the skill does *and when to use it*. This is the single most important field: agents match a user's request against it to decide whether to load the skill. Lead with the trigger/use case. The description is truncated at 1,536 characters in listings, so keep it tight.

### Writing guidance

- **Keep `SKILL.md` under ~500 lines.** Move long reference material (API tables, large examples) into separate files in the skill folder and link to them from `SKILL.md` so the agent loads them only when needed.
- **State what to do, not why.** Once loaded, a skill's body stays in the agent's context, so every line has a recurring cost. Be concise and imperative.
- **Target jsPsych v8+** unless a skill is explicitly about older versions; note the version assumption if it matters.
- **Prefer pointing at canonical sources** (jspsych.org docs, the jspsych and jspsych-contrib repos) over duplicating content that may drift.

## Adding a skill (checklist)

1. Create `skills/<skill-name>/SKILL.md` with `name` and `description` frontmatter and your instructions.
2. Add any supporting files in the same folder and reference them from `SKILL.md`.
3. Register the skill in [`.claude-plugin/marketplace.json`](.claude-plugin/marketplace.json) by adding its path to the `skills` array of the `jspsych` plugin:
   ```json
   "skills": [
     "./skills/jspsych-plugin-builder",
     "./skills/jspsych-timeline-builder",
     "./skills/<skill-name>"
   ]
   ```
4. Add a row to the **Skills** table in [README.md](README.md).
5. Open a pull request describing what the skill does and the kind of request it should trigger on.

## Testing locally

You can load this repo as a local marketplace in Claude Code before opening a PR:

```text
/plugin marketplace add ~/Documents/GitHub/skills
/plugin install jspsych@skills
```

Then try prompts that should trigger your skill and confirm the agent loads it and behaves as intended. You can also invoke it directly with `/<skill-name>`.

For other agents, the cross-tool [`skills`](https://github.com/vercel-labs/skills) CLI can install from a local path:

```bash
npx skills add ~/Documents/GitHub/skills
```

## Versioning

This repository uses **commit-based versioning** — there is no version number to bump. Once a pull request is merged, the change reaches users on their next `/plugin update` (Claude Code) or `npx skills add` (other agents). Practically:

- You don't need to edit any version field when adding or updating a skill.
- The `jspsych` plugin entry in [`.claude-plugin/marketplace.json`](.claude-plugin/marketplace.json) intentionally has **no `version` field**, so Claude Code falls back to the git commit SHA and treats every merged commit as a new version. (Note: the `metadata.version` in that file is the marketplace's own version, not the plugin's.)
- A per-skill version is **optional and informational only**. The [Agent Skills standard](https://agentskills.io/specification) has no first-class `version` field; if you want to record one, put it under `metadata` in the skill's `SKILL.md` frontmatter — nothing reads or acts on it:
  ```yaml
  metadata:
    version: "1.0"
  ```

If this repo later moves to explicit, documented releases, we'd add a `version` to the plugin entry, bump it per release following [semantic versioning](https://semver.org), and maintain a `CHANGELOG.md`. Until then, keep changes small and reviewable so continuous delivery stays safe.

## Pull request review

PRs are reviewed for:

- **Fit** — is this reusable jsPsych knowledge that belongs here?
- **Description quality** — will agents reliably trigger the skill at the right time, without over-triggering?
- **Accuracy** — instructions reflect current jsPsych (v8+) practice.
- **Conciseness** — `SKILL.md` stays focused; bulky reference material is split out.
- **Registration** — the skill is added to `marketplace.json` and the README table.

By contributing, you agree that your contribution is licensed under the repository's [MIT License](LICENSE).
