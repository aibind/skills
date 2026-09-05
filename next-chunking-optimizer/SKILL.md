---
name: next-chunking-optimizer
description: >
  Optimize production JavaScript chunking, initial route load, and repeated
  client navigation in Next.js versions later than 16.3.0 in App Router
  repositories building with Turbopack. Use when asked to tune experimental
  Turbopack chunking, reduce critical JavaScript, or compare chunking behavior.
---

# Next.js Chunking Optimizer

Tune Turbopack chunking through controlled, reproducible browser experiments.

## Instruction Precedence & Transparency

- The user's instructions take precedence over guidelines provided in this skill. If explicit user instructions conflict with this skill's instructions, prioritize the user's instructions.
- Temporary test deltas are reversible; the final persistent apply requires explicit user approval as the final step after a concrete, reviewable result is prepared.
- If this skill causes you to ask for permission or confirmation, pause, leave requested work unfinished, or diverge from the user's intent, name and link to the exact SKILL.md file you read, quote the relevant instruction, and briefly explain how it applies. Distinguish explicit skill requirements from your interpretation of guidelines.

## Core Invariants

- **Eligibility**: Targets App Router repositories with Next.js > 16.3.0 using Turbopack in production builds. If Webpack/Rspack is configured or the production build cannot be verified, stop the production experiment and continue with read-only analysis: report the router/bundler found, what was checked, the closest alternative (e.g. analyze the import graph, suggest the Webpack splitChunks equivalent), and ask if the user wants to proceed anyway.
- **In-Place & Non-Destructive (Default)**: Default to executing all baselines and candidates in the current checkout with immediate restore to the control configuration after testing. Do not persist the winner without separate, explicit post-experiment authorization. If the user explicitly requests isolation (e.g. worktree/checkout), the user's instruction wins.
- **Single-Variable Testing (Default)**: Default to one config option at a time. Only combine route priority, component chunking, and size thresholds into a single candidate if the user explicitly requests it; note the confounding when combined.
- **Statistical Rigor (Default)**: Default to a run count ($N \ge 3$) committed upfront. Report medians and observed minimum-to-maximum ranges—never single point estimates. Test cold loads and warm navigations separately. If the user asks for a quick/scout check, allow $N = 1$–$2$, label it as low-confidence scout, and do not present it as a verdict.
- **Browser-Centric Evaluation**: Base decisions on user-visible browser metrics (FCP, LCP, click-to-render timing, transferred bytes), not bundle visualization or chunk counts alone.

---

## Workflow

### 0. Gate Eligibility (Read-Only)

Before altering files or starting builds:

1. **Select the target app and stop until it is fixed.** Detect a monorepo first (read-only): check for `pnpm-workspace.yaml`, `turbo.json`, an `apps/` directory, or multiple `package.json` files with Next.js dependencies / multiple `app/` directories. Only skip the ask when the user's string exactly matches one app's relative directory (e.g. `apps/dashboard`) or its `package.json` name (e.g. `@solarx/dashboard`). A repo or org name (e.g. `solarx-form`), a substring, or a generic term does not count as a match. When multiple Next.js apps are found, stop: present the candidates with path, package name, Next.js version, router verification, site-type hint from [site-types.md](./references/site-types.md), and production build/serve command, then ask which app to run on and wait for the answer. Do not proceed past this gate on a guess. If exactly one Next.js app is found, proceed with it and state the assumption. Record the target plus the considered and rejected apps in [experiment-rig.md](./references/experiment-rig.md).
2. Snapshot the target app's existing `experimental.turbopackChunking` block (or its absence) and treat it as the control. A pre-existing value means the baseline is already modified.
3. Check the target app's `package.json` and lockfiles for Next.js > 16.3.0.
4. Confirm the target app uses the App Router (its own `app/` directory, not the repo root).
5. Inspect the target app's production build and serve scripts (`cwd` is the target app directory, or `pnpm --filter <pkg>` from the root). Do not assume `next start`; use the script the target app declares (e.g. `opennextjs-cloudflare preview`). Note when `turbopack.root` points above the app at the monorepo root.

*If prerequisites are missing, Webpack/Rspack is configured, or the production build cannot be verified, do not start the production experiment. Continue with read-only analysis instead (report what was found, analyze the import graph if possible), then ask if the user wants to proceed anyway or adjust scope. Do not abandon the task without a concrete, reviewable finding.*

### 1. Map Routes & Classify Site

Scope this entire step to the target app selected in §0. Re-classify after selection; never carry a site type over from another app.

1. In the target app, identify critical entry routes, shared layout client components, dynamic imports, and heavy client libraries.
2. Classify the application using [site-types.md](./references/site-types.md) to determine:
   - **Primary Journey**: The route and load type (cold root load, deep link, or warm transition) to optimize.
   - **Guardrail Journeys**: Secondary routes or navigations that must not regress.
3. Prepare the experiment setup following [experiment-rig.md](./references/experiment-rig.md).

### 2. Establish Production Baseline

Measurement driver (optional): if `agent-browser` is installed, prefer it for serving checks, snapshots, hover-prefetch, clicks, and performance sampling — load its workflow via `agent-browser skills get core`. Otherwise use any available browser harness. Never block asking for install; the user's harness preference wins.

1. Build and serve the production artifact from the target app (`cwd` is the target app directory, or `pnpm --filter <pkg>` from the root; use the target app's declared serve command).
2. Execute the primary and guardrail journeys for the pre-set run count (default $N \ge 3$; $N = 1$–$2$ only for user-requested scout checks).
3. Record medians and min–max ranges for:
   - FCP, LCP, and time-to-usable marker.
   - Total transferred and encoded JavaScript bytes.
   - Critical request count and waterfall structure.

### 3. Propose & Test Candidates

Select single-variable hypotheses using [chunking-controls.md](./references/chunking-controls.md). First complete the §0 installed-schema snapshot there (required, read-only); derive every delta from the snapshot, never from the illustrative example. Apply deltas to the target app's `next.config.*` only.
- **Dashboards / SaaS**: Start with `generateComponentChunks: true`.
- **Content / Ecommerce**: Start with targeted `priorityRoutes` for critical entry points.
- **Size adjustments**: Test `minChunkSize` or `maxMergeChunkSize` only when initial graphs show fragmentation or oversized blocking chunks.

#### Candidate Plan (No Pre-Approval Needed for Reversible Tests)
Temporary test deltas are reversible and do not need pre-approval. Proceed autonomously: state the planned candidates briefly, apply each delta temporarily, measure, then restore control immediately. Do not introduce approval flows, warnings, or safety checklists for these reversible steps.

| Candidate | Exact Config Delta | Hypothesis | Target Journey | Planned Runs | Guardrails |
| :--- | :--- | :--- | :--- | :---: | :--- |
| Candidate 1 | `experimental.turbopackChunking: { ... }` | Explain hypothesis | Primary flow | $N$ | Guardrail routes |

*Only the final persistent apply (Step 5) requires explicit user approval, requested after a concrete, reviewable result is prepared.*

#### Dashboard Navigation Measurement Protocol
When testing authenticated warm navigations:
1. **Pre-flight**: Serve candidate build; navigate to source screen in authenticated state.
2. **Prefetch window**: Trigger product link prefetch (e.g. hover/focus); wait for network idle plus settling window (150ms).
3. **Click & sample**: Clear resource-timing buffer; click real `<Link>`; record document/script requests, transferred bytes, and time until visible target marker renders.

### 4. Restore & Report

1. **Restore Control**: Immediately revert the target app's `next.config.*` to the recorded control state from §0 step 2, including any pre-existing `turbopackChunking` values.
2. **Compile Results**: Present a consolidated results table using the template from [experiment-rig.md](./references/experiment-rig.md). Use the full table for final reports; use the scout variant for quick $N = 1$–$2$ checks.
3. **Analyze Tradeoffs**: Detail changes in request waterfall, cache hit rates, and guardrail metrics.
4. **Recommendation**: Recommend the most effective candidate, reminding the user that Turbopack chunking options remain experimental. Do not persist the winner without separate, explicit user request — user approval is the final step after this reviewable result.

### 5. Apply & Verify (Explicit Request Only)

Bias toward completing all authorized reversible work before asking for approval. Request approval only for this persistent step, after Steps 1–4 are done and reviewable.

Upon separate, explicit authorization from the user:
1. Apply the validated configuration delta to the target app's `next.config.*`.
2. Run the production build and verify type checking and linting scoped to the target app.
3. Perform a final smoke test on primary and guardrail routes.

Run tests appropriate to the change. Skip full type checking, linting, and smoke tests for temporary test deltas; run them only on final Apply. Once required checks pass, broaden or repeat testing only when new changes, failures, or unresolved concerns justify it; otherwise continue toward completing the task.

### 6. Parallelization via Subagents

If at any point you can parallelize work by delegating tasks to another agent (route mapping, baseline runs, guardrail journeys, candidate builds), do so using collaboration tools if it could save time or improve quality. Messages that you send to other agents and your final answer may be read by a human, so ensure they are legible. Always put proper spaces between words and/or numbers.

### 7. Reporting Style

Write all user-facing output in ASD-STE100 Simplified Technical English. Use short, direct sentences. Use one instruction or action per sentence. Use approved technical terms where possible. Avoid idioms, slang, metaphors, and unnecessary words. State warnings, findings, and required user actions in clear, active language.

Default to using clear, concise paragraphs, each developing one main idea. State the main point clearly and early, then develop it with the explanation and detail the reader needs. Use lists only when the information is genuinely parallel, sequential, or easier to compare, and avoid nested lists unless the hierarchy cannot be expressed clearly in prose. Use plain language over jargon, and reference technical details only to the degree that it helps illustrate the result.

Avoid slop words or phrases like "Bottom Line:" in conclusions, "delve," "foster," "leverage," "it's worth noting," "importantly," "Question? Answer." or "This isn't about X. It's about Y.", "genuinely" or hyphenated compound descriptions and adjectives. Do not use concluding summary statements such as "In short:..", "The simplest mental model is:...". State the intended action directly. Do not use contrastive framing such as "X, not Y" that introduces an unprompted alternative the user didn't ask about.
