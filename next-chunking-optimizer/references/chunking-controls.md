# Experimental Turbopack Chunking Controls

Turbopack chunking options in Next.js 16.3.0 or later configure how the compiler groups and splits client-side modules. Confirm the options supported by the installed version.

> [!IMPORTANT]
> The schema is experimental. Derive each candidate from the target app's installed package, including types, value limits, defaults, and full option paths. Neither this reference nor the latest online documentation defines the API for every Next.js version.

## 0. Snapshot the Installed Schema (Required, Read-Only)

Before proposing any candidate delta:

1. Resolve the Next.js package from the target app, not an unrelated workspace package. Start with `dist/server/config-shared.d.ts` and record the installed version, full option paths, and exact types for the chunking controls.
2. Read the corresponding runtime validation, usually `dist/server/config-schema.js`, for constraints that TypeScript cannot express, such as numeric bounds. If these files move, locate their equivalents inside that installed package rather than assuming the feature disappeared.
3. Cross-check bundled documentation and implementation for meanings, units, and defaults. If external documentation is needed, use official sources matching the installed release; latest or canary documentation is not evidence for a different version.
4. If a control is missing, renamed, moved, or retyped, use an equivalent only when the installed package confirms its meaning and accepted shape. Otherwise skip that candidate and continue with supported controls. Do not emit both old and new keys or guess a conversion. If no candidate can be verified, report the experiment incomplete.
5. Record the installed signatures, constraints, source paths, and any unresolved defaults in the report. Resolve defaults relevant to whether a candidate differs from control before testing it. Repeat this inspection if the installed version changes; a version number alone does not establish option support.

## Form Configuration Deltas

Change one supported option relative to control. Preserve existing overrides and leave unrelated values absent so their installed defaults still apply. Test a combined candidate only when explicitly requested.

Use only the installed representation. For example, if `priorityRoutes` is declared as `RegExp[]`, supply regular expressions, not string globs. If `firstPageLoadPriority` is numeric and its runtime validator limits it to 0–1, supply a number within that interval, not a boolean. These are conditional examples, not types or limits to impose on every release.

Validate the candidate against the installed runtime schema when available, then confirm acceptance in the required production build. Unknown-option, ignored-option, or invalid-value warnings invalidate the candidate even if the build exits successfully. Restore control and correct or skip that candidate before collecting measurements. Do not suppress validation or cast away type errors to force an option through.

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
