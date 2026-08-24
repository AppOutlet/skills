---
name: opencode-go-model-picker
description: Picks the best OpenCode Go model pair for plan/build agent modes, plus the cheapest model for trivial tasks, by fetching the live model list and usage limits from opencode.ai/docs/go and researching current benchmarks (coding, Kotlin, reasoning, agentic tool-use). Use this whenever the user asks "what model should I use in OpenCode Go", wants to re-evaluate their OpenCode Go plan/build models, asks which OpenCode Go model is cheapest or has the most usage/requests, or mentions OpenCode Go usage limits, model rotation, or picking models for planning vs building. OpenCode Go's roster and limits change periodically, so always re-run the live research in this skill rather than answering from memory of a past model list.
---

# OpenCode Go Model Picker

OpenCode Go rotates its available models and usage limits over time, and the underlying
models are often unfamiliar rebrands of known model families. Never answer from memory —
always do the live fetch and live research below, even if you think you already know the
current lineup.

## Step 1 — Discover the current models and limits

Fetch `https://opencode.ai/docs/go` (try both a literal-transcription prompt and a
structured-extraction prompt — the two can disagree, since fetch tools summarize through a
small model). Cross-check with a quick web search for `opencode.ai docs go models` if the two
fetches disagree on model names or limit numbers, or if a number looks suspicious (e.g.
wildly different request counts for the same model across sources).

Capture, per model:
- **Exact model name as displayed.** Look carefully for extra text appended directly to the
  name itself (not just a separate notes column) — this is how OpenCode flags a model that
  currently gets a bonus/extra usage grant (e.g. a multiplier or "free" tag suffixed to the
  name). Quote this annotation verbatim; don't paraphrase it away.
- **Usage limits.** OpenCode Go's limits are denominated in dollar value per window (5-hour /
  weekly / monthly), not fixed request counts — the page may also show per-model request-count
  estimates derived from that $ value and the model's per-request cost. Capture both forms if
  present, and note which one you're using later so the final recommendation is traceable.
- Any other flag that affects real-world usability: peak/off-peak pricing, data-retention
  notes, "training enabled" (prompts may be used for training), limited-time-only status, etc.

If the page lists a model whose name doesn't ring a bell, don't skip it — OpenCode Go names
are frequently its own label for a hosted version of a known open model (e.g. a "GLM-*" name
is Zhipu, "Kimi *" is Moonshot, "Qwen*" is Alibaba, "DeepSeek *" is DeepSeek, "Grok *" is xAI,
"MiniMax *" is MiniMax). Keep the underlying family in mind for Step 2 — benchmark coverage
under the exact OpenCode label is often thin, but coverage of the underlying model/version is
usually not.

## Step 2 — Rank the models

For every model discovered in Step 1, search the web for evidence on three dimensions. See
`references/benchmark-sources.md` for a starting list of leaderboards/benchmarks worth
checking — treat it as a starting point, not a fixed list, since new benchmarks appear
constantly and old ones go stale.

1. **Coding ability** — general coding benchmarks first, then check
   [Android Bench](https://developer.android.com/bench) for Android/Kotlin-specific evidence
   (Google's own benchmark of 100 Android dev tasks, averaged over 5 runs per model with a CI
   range) — its leaderboard already covers several of the vendor families OpenCode Go hosts, so
   it's usually the strongest available proxy for this user's actual work, not just a
   long-shot search. The user is an Android/Kotlin developer, so weight Android Bench /
   Kotlin-JVM evidence higher than a model's score on languages irrelevant to that work.
2. **Reasoning** — general reasoning/knowledge benchmarks, and specifically evidence of how
   the model performs on multi-step problem decomposition, not just raw knowledge recall.
3. **Agentic / multi-step tool-use** — benchmarks or write-ups that specifically test acting
   over many steps with tools (running commands, editing files, iterating on test failures),
   since this is a different skill from single-shot code generation.

Where you can't find a benchmark under the exact OpenCode label, search the underlying model
family/version instead (see Step 1). Note explicitly in your findings when a ranking is based
on the underlying model rather than a benchmark of the OpenCode-hosted version specifically,
since hosting can affect latency/quantization even when the weights are the same.

**Every specific number you cite must carry the URL you found it on.** A benchmark score is
only as good as your ability to point at where it came from — if you can't name the URL, you
did not actually read it, and a model under memory pressure will confabulate a plausible-looking
decimal rather than admit that. This applies doubly to any comparison against a *different*
vendor's model (e.g. "beats Claude Opus X.Y's Z score") — those comparison figures are the
easiest thing to fabricate convincingly, and citing a model name that turns out not to exist is
a strong tell that the whole number was invented. Before writing a ranking line, ask: could I
paste the exact URL this number came from? If not, don't write the number — write "no
verifiable data found" instead. A ranking built from three "no data" lines is more useful than
one built from five confident-sounding but unsourced ones.

Produce a best → worst ranking per dimension, with a one-line reason per model citing what you
found (a specific score plus its source URL, a specific benchmark name, or "no data found" if
genuinely nothing turned up — don't invent a ranking for a model with no evidence, just note
the gap).

## Step 3 — Choose the plan/build pair and the cheapest model

Combine the Step 2 rankings with the Step 1 limits to choose three models:

- **Plan model** — the strongest on reasoning (and coding/Kotlin as a tiebreaker). This model
  is used for codebase investigation, weighing approaches, and design — fewer, higher-value
  calls per task, so it's fine to spend down a tighter usage limit here.
- **Build model** — the strongest on agentic/multi-step tool-use (coding ability as a
  tiebreaker over raw reasoning, since building is mostly execution). This model gets called
  repeatedly through a build/test/iterate loop, so weigh its usage limit heavily — do not pick
  the most capable model here if it has a limit so tight it'll be exhausted mid-task. If the
  top-ranked agentic model has a noticeably thin limit and a close second has a much larger
  one, prefer the second and say so explicitly.
- **Cheapest model** — whichever model has the largest usage allowance / lowest effective cost
  per request, full stop, regardless of how it ranked in Step 2. This is for trivial work (file
  exploration, simple lookups) where intelligence barely matters and you want to conserve the
  Plan/Build models' limits.

Don't default to the single highest-ranked model on the leaderboard without checking its
limit — a model that's marginally better but 10x more usage-constrained is usually the wrong
pick for Build in particular, since it'll get exhausted first.

## Output

Give a direct recommendation, not a hedge. Structure it as:

```
## OpenCode Go recommendation ([today's date])

**Plan:** <model name>
<1-3 sentences: why on reasoning/coding, referencing what you found in Step 2>

**Build:** <model name>
<1-3 sentences: why on agentic/tool-use, and the limit tradeoff you weighed>

**Cheapest (trivial tasks):** <model name>
<1 sentence: the limit/cost figure that makes it the cheapest>

**Bonus/extra-usage flags seen:** <list any model-name annotations from Step 1, or "none observed">

**Sources:** <every URL you cited a specific number from, as markdown links>
```

Close with a one-line reminder that this reflects today's snapshot of OpenCode Go's model
list and limits, and should be re-run next time the lineup or limits look different (rather
than trusted indefinitely).

If the recommendation ends up resting on "no verifiable data found" for most models — say
because the exact search terms didn't surface anything — say so plainly rather than quietly
padding the gap with confident-sounding prose. A thin but honest recommendation is the point;
a well-written but fabricated one defeats the purpose of the skill.
