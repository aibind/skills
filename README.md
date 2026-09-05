# skills

[![skills.sh](https://skills.sh/b/aibind/skills)](https://skills.sh/aibind/skills)
[![Next.js](https://img.shields.io/badge/Next.js-16.3%2B-black)](https://nextjs.org/)

Agent skills for Next.js performance engineering.

```sh
npx skills add aibind/skills
```

## Prerequisites

- Next.js > 16.3.0 with App Router (`app/`) and Turbopack production builds (`next build` passes).
- A servable production target (`next start` or custom prod server) and a browser harness for measurements. `agent-browser` is recommended, but it is optional. Any suitable browser harness can be used.

## Available Skills

- **[next-chunking-optimizer](./next-chunking-optimizer/SKILL.md)** — Empirical, non-destructive Turbopack chunk tuning for Next.js 16.3+ App Router apps. Measures cold loads, warm transitions, and reports median/range deltas before applying changes.

  ```sh
  npx skills add aibind/skills --skill next-chunking-optimizer
  ```
