# Site-Type Strategies

Classify routes, layouts, and journeys before forming hypotheses. A product can have multiple modes; segment the experiment by route family when needed.

| Type | Primary Metric | Starting Hypothesis | Watch Out For |
| :--- | :--- | :--- | :--- |
| **Marketing / Content** | Cold root & landing LCP, critical JS transfer | Prioritize acquisition routes (`/`, `/pricing`); minimize above-the-fold client code | Over-prioritizing `/` when paid/organic traffic lands on deep links |
| **Documentation** | Cold deep link LCP, shared navigation reuse | Keep layout/navigation chunks shared and cached; keep doc-body code route-local | Home-page priority rules that do not benefit deep-linked docs |
| **Ecommerce** | Category/Product LCP, cart interaction | Prioritize browse journeys without leaking checkout/cart code into initial view | Monolithic shared chunks bloating the landing page |
| **Dashboard / SaaS** | Authenticated cold entry + warm client navigation | Enable `generateComponentChunks: true` for repeated navigation reuse | Regressing cold entry LCP with too many tiny component requests |
| **Workflow / Internal** | Task completion flow, mutation-to-screen transitions | Prioritize high-frequency route transitions and shared interactive components | Optimizing anonymous entry routes that active users bypass |
| **Hybrid** | Segmented by public vs. authenticated route families | Separate priority rules for public landing vs. authenticated app spaces | Applying a single global chunking rule that degrades one mode |
