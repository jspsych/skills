# jsPsych Skills

A collection of [Agent Skills](https://agentskills.io) for building experiments with [jsPsych](https://www.jspsych.org). These skills give AI coding agents (Claude Code, Cursor, Copilot, and others) the conventions and best practices for jsPsych development, loaded automatically when relevant.

## Skills

| Skill | What it helps with |
| --- | --- |
| [`jspsych-plugin-builder`](skills/jspsych-plugin-builder/SKILL.md) | Creating jsPsych plugins (v8+) — when to build vs. reuse, initialization, structure, testing, and documentation. |
| [`jspsych-timeline-builder`](skills/jspsych-timeline-builder/SKILL.md) | Creating shareable, parameterized timelines (reusable experiment components) for the [jspsych-timelines](https://github.com/jspsych/jspsych-timelines) repository. |

## Install

### Claude Code

```text
/plugin marketplace add jspsych/skills
/plugin install jspsych@jspsych-skills
```

This installs the `jspsych` plugin, which bundles both skills. Claude loads a skill automatically when your request matches its description, or you can invoke one directly with `/jspsych-plugin-builder` or `/jspsych-timeline-builder`.

### Other agents (Cursor, Copilot, Cline, Windsurf, and 70+ more)

Using the cross-agent [`skills`](https://github.com/vercel-labs/skills) CLI:

```bash
npx skills add jspsych/skills
```

### Manual

Copy any folder under [`skills/`](skills/) into your agent's skills directory (for Claude Code, that's `~/.claude/skills/` for personal use or `.claude/skills/` in a project).

## Repository layout

```text
skills/
├── .claude-plugin/
│   └── marketplace.json   # Claude Code plugin marketplace manifest
└── skills/
    ├── jspsych-plugin-builder/
    │   └── SKILL.md
    └── jspsych-timeline-builder/
        └── SKILL.md
```

Each skill is a self-contained folder with a `SKILL.md` (YAML frontmatter + instructions), following the [Agent Skills](https://agentskills.io) open standard. Skills may also include supporting files (reference docs, examples, scripts) referenced from `SKILL.md`.

## Contributing

To add a skill:

1. Create `skills/<skill-name>/SKILL.md` with `name` and `description` frontmatter. Put the key use case first in the description — agents use it to decide when to load the skill. Keep `SKILL.md` under ~500 lines and move long reference material into supporting files in the same folder.
2. Add the skill's path to the `skills` array in [`.claude-plugin/marketplace.json`](.claude-plugin/marketplace.json).
3. Add a row to the Skills table above.

## License

[MIT](LICENSE)
