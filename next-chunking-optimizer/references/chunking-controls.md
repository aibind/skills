# Experimental Turbopack Chunking Controls

Turbopack chunking options in Next.js (>16.3.0) configure how the compiler groups, prioritizes, and splits client-side module graphs.

> [!IMPORTANT]
> The schema is experimental and drifts between versions (e.g. `string[]` → `RegExp[]` has occurred). Never copy any example verbatim. Snapshot the installed schema first (see §0) and derive every candidate delta from it.

## 0. Snapshot the Installed Schema (Required, Read-Only)

Before proposing any candidate delta:

1. Read the installed `turbopackChunking` block in `node_modules/next/dist/server/config-shared.d.ts` and record the exact keys and their types for the installed Next.js version.
2. If present, cross-check `node_modules/next/dist/docs/**/turbopackChunking.md` for intent/defaults.
3. If a key from §1–§3 below is missing, renamed, or retyped in the installed version, the installed types win: adapt the delta to the installed shape or drop that candidate — never guess the old shape.
4. Quote the installed signature (or its absence) when presenting candidates, so the delta is reviewable against the actual version.

## Configuration Shape (Illustrative Only — Do Not Copy)

```ts
// next.config.ts
import type { NextConfig } from 'next'

const nextConfig: NextConfig = {
  experimental: {
    turbopackChunking: {
      // Prioritize client code for critical route patterns
      priorityRoutes: ['/', '/dashboard/**'],
      firstPageLoadPriority: true,
      priorityBoost: 2,

      // App Router component chunking for repeated navigations
      generateComponentChunks: true,

      // Chunk size & grouping thresholds (bytes)
      minChunkSize: 20_000,
      maxMergeChunkSize: 100_000,
      maxChunkCountPerGroup: 25,
    },
  },
}

export default nextConfig
```

Shape reference only — test one family at a time per `SKILL.md` unless the user explicitly requests a combined candidate. Types below are intents, not contracts: confirm each key and type against the §0 snapshot for the installed version.

## Control Families (Confirm Each Against §0)

### 1. Route Priority
- **`priorityRoutes`**: Route patterns prioritized during initial chunk compilation. Type has drifted across versions (observed as string globs and as `RegExp[]`) — use exactly what the installed types declare.
- **`firstPageLoadPriority`**: Boosts chunking priority for the first initial page load graph. Confirm presence, type, and default in the installed version; drop if absent.
- **`priorityBoost`**: Relative weighting factor biasing chunk boundaries toward prioritized routes. Confirm presence, type, and default in the installed version; drop if absent.

### 2. Component Chunks
- **`generateComponentChunks`**: Generates granular chunks for shared client components in App Router. Ideal for dashboards where multiple views share a common component graph, enabling warm-navigation cache hits. *Not supported in Pages Router.* Confirm presence and type in the installed version.

### 3. Size and Merging Economics
- **`minChunkSize`**: Minimum chunk size before merging into adjacent chunks (prevents excessive micro-requests). Confirm unit (bytes) and type in the installed version.
- **`maxMergeChunkSize`**: Ceiling for merged chunk size (prevents forming blocking mega-chunks). Confirm unit (bytes) and type in the installed version.
- **`maxChunkCountPerGroup`**: Upper limit on parallel chunks loaded per route group. Confirm presence and type in the installed version.
- **`requestCost`**: Weight representing HTTP request overhead in the chunking cost model. Confirm presence and type in the installed version.
