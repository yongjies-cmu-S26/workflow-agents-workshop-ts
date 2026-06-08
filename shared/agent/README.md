# @workshop/agent

The **constant core** of the workshop — the code-review agent and its runtime,
imported unchanged by all three patterns (naive, worker, workflow). Nothing here
knows about Render; the substrate decides how the agent is invoked.

The review pipeline:

```
prepareDiff → filterDiff → [ security ‖ performance ‖ ux? ] → judge
```

## Key exports

- `runReview(prUrl, options)` — the whole pipeline; returns a verdict, findings,
  and token usage. Emits progress via an `onEvent` callback (used by the worker).
- `defineAgent(def)` → an `Agent` with an in-process `.run(input, ctx)`.
- The agents: `securityReviewer`, `performanceReviewer`, `uxReviewer`, `judge`,
  plus `REVIEWERS`, `selectReviewers()`, and `hasFrontendFiles()`.
- `prepareDiff(pr)` — GitHub PR URL → per-file patches (public repos, no auth).
- `filterDiff(patches, { breakGlass })` — drop noise files before review.
- `parseDecision(text)` — judge output → `{ verdict, reason, findings }`.
- `resolveClient` / `resolveModelSpec` / `MODEL_TIERS` — provider-agnostic model
  client over `fetch` (Anthropic + OpenAI), with a deterministic **mock** client
  when no API key is set (or `AGENT_MODEL=mock`).
- Tools: `defineTool`, `defineMcpSource`, `TOOL_REGISTRY`, `registerTool`,
  `resolveTools` — import-and-register local tools and MCP sources.

## Layout

```
src/
  review.ts        runReview + parseDecision (the orchestration)
  agents.ts        agent definitions + reviewer selection
  agent.ts         defineAgent() → in-process .run()
  loop.ts          the provider-agnostic LLM loop
  model.ts         Anthropic/OpenAI adapters + MockClient
  model-tiers.ts   small/medium/large → concrete models
  prepareDiff.ts   GitHub PR → Patch[]
  filterDiff.ts    noise filtering + break-glass
  tool.ts / tools.ts   tool + MCP registry
  types.ts         shared contracts
  index.ts         public surface
```

No API key needed — the mock model lets the full pipeline run offline.
