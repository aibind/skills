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
| **Driver** | `agent-browser` if installed (see `agent-browser skills get core`), else available harness; user's preference wins |
| **Target Journeys** | Primary route (e.g. `/dashboard`), warm transition (e.g. `/dashboard` → `/settings`), guardrails (e.g. `/`) |
| **Environment** | Auth state, viewport (desktop/mobile), network profile (e.g. unthrottled / fast 4G) |
| **Sample Size** | Planned valid runs per candidate (default $N \ge 3$; $N = 1$–$2$ allowed only for user-requested scout checks) |

---

## 2. Results Log Template

Use the full table for final reports ($N \ge 3$). Use the scout variant for quick $N = 1$–$2$ checks; label scout results as low-confidence and do not present them as verdicts.

Full report — copy and fill this table for reporting experiment outcomes:

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
