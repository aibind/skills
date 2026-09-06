# Install skills for your coding agent

Skills give your coding agent reusable instructions for development tasks. This collection starts with measuring and tuning JavaScript loading in Next.js apps.

[![skills.sh](https://skills.sh/b/aibind/skills)](https://skills.sh/aibind/skills)

## Install from this collection

Use the [skills command-line tool](https://www.skills.sh/docs/cli) to install skills from this repository:

```sh
npx skills add aibind/skills
```

## Measure Next.js loading and navigation

Use [next-chunking-optimizer](./next-chunking-optimizer/SKILL.md) to test how splitting JavaScript into files affects page loading and navigation.

Before you start, you need:

- Next.js 16.3.0 or later with App Router and Turbopack production builds that support the chunking options you want to test
- Commands to build your app for production and serve it locally
- A browser tool for measurements, preferably `agent-browser`

The skill checks your Next.js version first. Below 16.3.0, it stops before analysis or experiments, including when you request analysis only.

Your agent compares the current configuration with temporary chunking settings using production builds and browser measurements. It restores your starting configuration and presents every experiment in a comparison table.

Review the results and exact configuration change before approving a setting to keep. If the measurements show no clear benefit, the agent recommends keeping your current configuration.

## Update installed skills

Fetch the latest versions of all your installed skills:

```sh
npx skills update
```

See the [skills update documentation](https://www.skills.sh/docs/packs#update-a-pack) to update a single skill.
