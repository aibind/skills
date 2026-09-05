# Experimental Turbopack Chunking Controls

Turbopack chunking options in Next.js 16.3.0 or later configure how the compiler groups and splits client-side modules. Confirm the options supported by the installed version.

> [!IMPORTANT]
> The schema is experimental. Snapshot the installed types and defaults first, then derive each candidate from them. Documentation examples are not a substitute for the installed API.

## 0. Snapshot the Installed Schema (Required, Read-Only)

Before proposing any candidate delta:

1. Read the installed `turbopackChunking` block in `node_modules/next/dist/server/config-shared.d.ts` and record the exact keys and their types for the installed Next.js version.
2. If present, cross-check `node_modules/next/dist/docs/**/turbopackChunking.md` for intent/defaults.
3. If a key below is missing, renamed, or retyped in the installed version, adapt the delta to the installed shape or drop that candidate. Continue with other supported candidates.
4. Quote the installed signature (or its absence) when presenting candidates, so the delta is reviewable against the actual version.

## Form Configuration Deltas

Change one supported option relative to control. Preserve existing overrides and leave unrelated values absent so their installed defaults still apply. Test a combined candidate only when explicitly requested.

Use the exact installed type: a declared `RegExp[]` requires regular expressions, and a declared numeric `firstPageLoadPriority` requires a number. Do not substitute string globs or booleans. Read documented defaults when deciding whether a proposed value actually differs from control.

## Control Families

Use these descriptions to form hypotheses. Confirm presence, type, units, and defaults in the installed version before testing.

| Family | Option | What to test |
| :--- | :--- | :--- |
| Route priority | `priorityRoutes` | Favor client bundles for selected entry routes; check the cost on other routes |
| Route priority | `firstPageLoadPriority` | Change the weight of single-page loads relative to repeated navigation |
| Route priority | `priorityBoost` | Change how strongly the selected priority routes influence merging |
| Component chunks | `generateComponentChunks` | Emit the constituent parts of merged chunks so navigation can reuse already loaded code |
| Component chunks | `minComponentChunkSize` | Balance component reuse against requests for small component chunks |
| Size and merging | `minChunkSize` | Change merging of small chunks; measure request savings against extra downloaded code |
| Size and merging | `maxMergeChunkSize` | Change the threshold above which an existing chunk is excluded from further merging |
| Size and merging | `maxChunkCountPerGroup` | Change the per-group chunk limit; measure requests and navigation reuse |
| Size and merging | `requestCost` | Change the assumed cost of a request in the merging calculation |

Chunking thresholds can describe uncompressed, unminified code. Keep those units separate from emitted file sizes and browser transfer sizes in the report.
