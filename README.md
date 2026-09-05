# Agent Skills

[![skills.sh](https://skills.sh/b/aibind/skills)](https://skills.sh/aibind/skills)

Reusable skills for coding agents, starting with Next.js performance optimization.

## Quick start

Install from this collection:

```sh
npx skills add aibind/skills
```

## Available skills

| Skill | Purpose |
| --- | --- |
| [Next.js Chunking Optimizer](./next-chunking-optimizer/SKILL.md) | Measure and tune JavaScript loading and page navigation in Next.js |

### Next.js Chunking Optimizer

Tests production builds in a browser, compares results, and applies measured improvements when requested.

Requirements:

- Next.js > 16.3.0 with App Router and Turbopack production builds.
- A working production build and a command to serve it locally.
- A browser tool for measurements. `agent-browser` is recommended; other browser tools can be used.

## Updates

Update all installed skills regularly for the latest fixes and guidance:

```sh
npx skills update
```

See the [skills.sh update documentation](https://www.skills.sh/docs/packs#update-a-pack) for details, including how to update a single skill.
