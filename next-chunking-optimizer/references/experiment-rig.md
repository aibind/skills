# Experiment Rig & Log Template

Use this template to record baseline conditions and candidate outcomes.

## 1. Rig Checklist

| Item | Details |
| :--- | :--- |
| **Target App** | Selected app directory and package name, with the request, context, or single-app assumption supporting the choice; ask only when multiple plausible targets remain |
| **Considered / Rejected Apps** | Every Next.js candidate found plus non-Next packages checked, each with path, Next.js version or exclusion reason (e.g. blume docs, worker), and why it was not selected |
| **Control Config Snapshot** | Pre-existing `experimental.turbopackChunking` block in the target app (or its absence); this is the restore target |
| **Working Directory** | Exact `cwd` or `pnpm --filter <pkg>` used for build/serve/test commands |
| **Next.js Version** | Extracted from the target app's `package.json` / lockfile (expected >16.3.0; if not met, follow the Eligibility fallback in `SKILL.md`) |
| **Bundler Command** | Exact production build command for the target app (target: Turbopack; if Webpack/Rspack, follow the Eligibility fallback in `SKILL.md`) |
| **Serve Command** | Exact production serve command for the target app (use its declared script; e.g. `next start` vs `opennextjs-cloudflare preview`) |
| **Router** | App Router verified (target app's `app/` directory present) |
| **Serving Endpoint** | Fresh port / isolated host (avoid stale cache / running processes) |
| **Driver** | Browser and tool versions; `agent-browser` if installed (see `agent-browser --help` and `agent-browser skills get core`), else available harness; user's preference wins |
| **Target Journeys** | Primary route (e.g. `/dashboard`), warm transition (e.g. `/dashboard` → `/settings`), guardrails (e.g. `/`) |
| **Environment** | Auth state, viewport (desktop/mobile), network profile (e.g. unthrottled / fast 4G) |
| **Sample Size** | Planned valid runs per candidate (default $N \ge 3$; $N = 1$–$2$ allowed only for user-requested scout checks) |
| **Measurement Method** | Readiness markers, paint collection window, cache reset, browser warm-up if any, and separate prefetch/post-click windows; keep these fixed across variants |
| **Evidence** | Paths to the report, exact candidate deltas, raw samples, and excluded setup attempts with reasons |

---

## 2. Measurement Preflight

Complete one setup pass through each selected journey before collecting the baseline:

- Confirm the selected app's production artifact is served at the measured URL. For authenticated routes, verify login works at that host and reaches the intended page. Save and reuse the same auth state without warming the app's asset cache for cold samples.
- Inspect current links and visible content with the browser before defining selectors. Use public DOM state or a safe interaction for the usable marker, not private React fields. State when the marker is a proxy for full usability.
- Register paint observers before navigation. Check that the document is visible, expected cold FCP/LCP entries exist, and the usable marker corresponds to visible content. Missing paint events, login redirects, or selector failures are invalid samples, not zero timings. Keep slow but valid samples.
- Exercise real links for warm journeys. Measure from the browser click event, verify the destination marker and absence of a new document navigation, and collect prefetch traffic separately from post-click traffic. Zero post-click requests alone does not establish zero transfer for the journey.

Keep setup attempts outside the declared sample count. If selectors, markers, cache handling, browser flags, or timing collection change, archive the old setup with its reason and repeat affected control and candidate measurements using the fixed method. Report unresolved measurement limits instead of selecting a winner from invalid or mixed samples. Local unthrottled desktop results do not establish mobile production performance.

## 3. Decision Summary

Show this compact comparison in the response. Include control and every tested variant. Use the actual primary metric and the cold-load guardrail most relevant to the decision. Show medians and observed min–max ranges; include absolute or percentage changes where useful.

```markdown
<Recommendation and the main reason.>

N = <valid runs> per variant and journey. <Viewport and network conditions.>
Timing and byte values are medians [min–max].

| Variant | Primary timing | Cold JS transfer | Guardrails | Valid / Attempted |
| :--- | :--- | :--- | :--- | :---: |
| Control | <value [range]> | <value [range]> | Baseline | <n / n> |
| Variant 1: <change> | <value [range]; delta> | <value [range]; delta> | <pass / regression / inconclusive> | <n / n> |

Current state: control restored; no candidate retained.
<Exact proposed configuration diff, if recommending a change.>
[Full measurements and reproduction details](<report path>)
```

Follow Step 4 of [SKILL.md](../SKILL.md) for the final approval request. Keeping control is a valid outcome. Do not describe an unchanged pre-existing option as a newly implemented or independently proven improvement.

## 4. Detailed Results Log

Save the full table in the linked report for experiments with $N \ge 3$. Include every selected journey and decision metric, with exact configuration deltas and failed-attempt reasons. Keep setup failures separate and explain which fixed method the valid/attempted counts cover. Use the scout variant for user-requested $N = 1$–$2$ checks; label them low-confidence and do not present them as verdicts.

Full report — copy and fill this table in the measurement artifact:

```markdown
### Experiment Summary

- **Target App**: <directory + package name, and how it was chosen>
- **Environment**: Next.js <version>, App Router, Turbopack
- **Viewport & Network**: <e.g., Desktop 1440x900, Fast 4G>
- **Sample Size**: N = <count> valid runs per candidate

| Candidate | Journey | Metric | Valid / Attempted | Median | Range (Min–Max) | Control Delta | Verdict |
| :--- | :--- | :--- | :---: | :---: | :---: | :---: | :--- |
| Control | Primary (Cold) | LCP | 5 / 5 | 1.12s | 1.05s – 1.18s | baseline | Baseline |
| Control | Primary (Cold) | JS Transfer | 5 / 5 | 340 KB | 338 KB – 342 KB | baseline | Baseline |
| Candidate 1 | Primary (Cold) | LCP | 5 / 5 | 0.98s | 0.94s – 1.02s | -140ms (-12.5%) | Win |
| Candidate 1 | Guardrail (Warm)| Nav Time | 5 / 5 | 120ms | 110ms – 135ms | +5ms (neutral) | Pass |
```

Scout report — for user-requested quick checks only:

```markdown
### Scout Check (low-confidence, N = 1–2)

- **Environment**: Next.js <version>, App Router, Turbopack
- **Sample Size**: N = <1–2> scout runs per candidate

| Candidate | Journey | Median | Control Delta |
| :--- | :--- | :--- | :--- |
| Candidate 1 | Primary (Cold) | <e.g. LCP 0.98s> | <e.g. -140ms> |
```
