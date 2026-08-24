# Benchmark sources to check

Not exhaustive — new leaderboards appear and old ones go stale, so treat this as a starting
point for the web search in Step 2, not a checklist to satisfy mechanically.

## General coding
- Aider polyglot leaderboard (aider.chat) — edit-success rate across languages
- LiveBench (coding category) — resists benchmark contamination via fresh problems
- BigCodeBench
- LMArena / Chatbot Arena — "Coding" category leaderboard

## Kotlin / JVM specific
- **[Android Bench](https://developer.android.com/bench) — check this first.** Google's own
  benchmark for Android-specific development tasks (100 test cases grounded in Android best
  practices), reported as a leaderboard with Score (%), a CI range, avg latency, and avg cost
  per full run — averaged over 5 runs per model, so the CI range is a genuine statistical
  reliability figure, not a single noisy sample. Its leaderboard already covers several vendor
  families that OpenCode Go hosts (Qwen, Kimi, DeepSeek, GLM, MiniMax, Gemma, Mimo), plus every
  major closed model, so it's usually possible to place an OpenCode Go model's underlying family
  directly on this leaderboard even when the exact OpenCode-labeled version isn't listed. Also
  check `/bench/archive` if the model you're after isn't on the current leaderboard (it may have
  aged off) and `/bench/methodology` if you need to judge whether a score is comparable to
  another benchmark's.
- Per-language breakdowns on the Aider polyglot leaderboard, if Kotlin is included
- Any Kotlin-specific eval mentioned in recent model release blog posts or papers
  (JetBrains has published Kotlin-specific benchmarks in the past — worth searching by name)
- If nothing Kotlin/Android-specific turns up even after checking Android Bench, fall back to
  Java/general JVM-language results as the closest proxy, and say so explicitly rather than
  silently substituting general coding scores

## Reasoning
- GPQA Diamond
- MMLU-Pro
- ARC-AGI (if the model has a reported score — most don't)
- Vendor's own reported reasoning benchmark, cross-checked against at least one independent
  source since vendor-reported numbers alone are not reliable

## Agentic / multi-step tool-use
- SWE-bench (Verified) — real GitHub issue resolution
- Terminal-Bench
- Any "agentic coding" leaderboard or blog comparison that specifically tests multi-turn
  tool-calling loops rather than single-shot generation

## Mapping OpenCode Go labels to underlying models
OpenCode Go often uses its own naming for hosted versions of known open models. When exact-name
benchmark coverage is thin, search the underlying family/vendor instead:
- `GLM-*` → Zhipu AI
- `Kimi *` → Moonshot AI
- `Qwen*` → Alibaba
- `DeepSeek *` → DeepSeek
- `Grok *` → xAI
- `MiniMax *` → MiniMax
- `Hy*` → likely Tencent Hunyuan (verify — naming is not confirmed)
- `MiMo-*` → likely Xiaomi (verify — naming is not confirmed)
- `GPT *` → OpenAI, if OpenCode is hosting an OpenAI model under this program

This mapping can go stale as OpenCode adds models under new names — if a prefix isn't listed
here, search the exact name first; if that turns up nothing, try dropping version numbers/suffixes
and searching again before concluding there's no benchmark data.
