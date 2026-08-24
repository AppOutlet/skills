# Benchmark sources to check

Not exhaustive — treat as a starting point for the Step 2 web search.

## General coding
- Aider polyglot, LiveBench (coding), BigCodeBench, LMArena Coding

## Kotlin / Android
- **[Android Bench](https://developer.android.com/bench) — check first.** Google's Android dev benchmark; covers most OpenCode Go vendor families (Qwen, Kimi, DeepSeek, GLM, etc.) so underlying-family scores usually exist even when the exact OpenCode label doesn't. 5 runs/model with a CI range. Also check `/bench/archive` (older results) and `/bench/methodology` (comparability).
- Per-language breakdowns on the Aider polyglot leaderboard
- Kotlin-specific evals from recent release notes / papers (JetBrains has published some)
- If nothing Kotlin/Android-specific turns up, fall back to Java/general JVM-language results as the closest proxy and say so explicitly.

## Reasoning
- GPQA Diamond, MMLU-Pro
- ARC-AGI (rarely reported)
- Vendor-reported reasoning benchmarks (cross-check against an independent source)

## Agentic / multi-step tool-use
- SWE-bench (Verified), Terminal-Bench
- Any "agentic coding" leaderboard or write-up testing multi-turn tool-calling loops

## Mapping OpenCode Go labels to underlying models

OpenCode Go often uses its own labels for hosted versions of known open models. When exact-name coverage is thin, search the underlying family:

- `GLM-*` → Zhipu AI
- `Kimi *` → Moonshot AI
- `Qwen*` → Alibaba
- `DeepSeek *` → DeepSeek
- `Grok *` → xAI
- `MiniMax *` → MiniMax
- `Hy*` → likely Tencent Hunyuan (verify)
- `MiMo-*` → likely Xiaomi (verify)
- `GPT *` → OpenAI, if OpenCode is hosting one under this program

This mapping can go stale. If a prefix isn't listed, search the exact name first; if that turns up nothing, try dropping version numbers/suffixes before concluding there's no data.
