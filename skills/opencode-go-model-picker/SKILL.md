---
name: opencode-go-model-picker
description: Picks the best OpenCode Go model for each of OpenCode's 8 built-in agents (build, plan, general, explore, scout, compaction, title, summary). Use when the user asks what model to use in OpenCode Go, wants to re-evaluate their agent model assignments, or asks which model is cheapest or has the most usage/requests.
---

# OpenCode Go Agent Model Picker

OpenCode Go rotates its lineup and OpenCode adds/renames agents over time. Always re-fetch both sources below — don't answer from memory.

## Agent profiles

Each agent has a fixed selection profile. Use it to score models in Step 2.

| Agent       | Profile |
| ----------- | --- |
| build       | strong agentic multi-step + Kotlin coding ability |
| plan        | high reasoning depth |
| general     | agentic multi-step workflows |
| explore     | fast and cheap (frequently called, intelligence barely matters) |
| scout       | fast and cheap, strong code reading (inspecting external library source/docs) |
| compaction  | good reasoning, mid-level price (auto-runs on long context) |
| title       | fast and cheap (auto-runs on session events, output is short) |
| summary     | good reasoning, cheap price (auto-runs on session events) |

## Step 1 — Fetch both sources

Fetch the raw mdx for both files:

- Agents: `https://raw.githubusercontent.com/anomalyco/opencode/refs/heads/dev/packages/web/src/content/docs/agents.mdx` — confirms the 8 built-in agents and what each does.
- Models: `https://raw.githubusercontent.com/anomalyco/opencode/refs/heads/dev/packages/web/src/content/docs/go.mdx` — current model list, `requests per month` column, and other flags (peak/off-peak pricing, data retention, training-enabled).

Cross-check with a web search for `opencode.ai docs go models` if a number looks suspicious.

## Step 2 — Pick one model per agent

For each agent in the profile table, score every model on the relevant dimensions (see `references/benchmark-sources.md` for benchmarks to consult). Then apply the **hard filter**:

**Hard filter (applies to all 8 picks):** exclude any model whose `requests per month` figure is under **1000**. Don't pick a filtered-out model just because it benchmarks higher — a model that exhausts mid-month is worse than one that's slightly weaker but lasts the full window. If the filter eliminates every model for a given agent, fall back to the model with the highest `requests per month` among those excluded and flag it as **below the 1000-call/month threshold** in the Output.

Allow the same model to be picked for multiple agents — the user can consolidate in their config if they want.

If a model name doesn't ring a bell, check `references/benchmark-sources.md` for the underlying family mapping (e.g. `GLM-*` → Zhipu, `Kimi *` → Moonshot, `Qwen*` → Alibaba) and search benchmarks under the underlying family.

**Every number you cite must carry a URL.** If you can't paste the source URL, write "no verifiable data found" instead of a plausible-looking decimal.

## Step 3 — Output

```
## OpenCode Go agent model picks ([today's date])

- **build:** <model> — <one-liner: agentic/Kotlin evidence + req/month>
- **plan:** <model> — <one-liner: reasoning evidence + req/month>
- **general:** <model> — <one-liner: agentic evidence + req/month>
- **explore:** <model> — <one-liner: cheapest qualifying model + req/month>
- **scout:** <model> — <one-liner: code-reading + req/month>
- **compaction:** <model> — <one-liner: reasoning + price tier + req/month>
- **title:** <model> — <one-liner: cheapest qualifying model + req/month>
- **summary:** <model> — <one-liner: reasoning + price + req/month>

**Bonus/extra-usage flags seen:** <list, or "none observed">
**Below the 1000-call/month threshold:** <list of sub-threshold picks, or "none">
**Sources:** <every URL cited>
```

Re-run when the agent list or model lineup changes.
