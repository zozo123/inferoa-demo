# Inferoa — inference flight recorder

An interactive, single-file demo of the ideas in [Announcing Inferoa](https://inferoa.agentic-in.ai/blog/announcing-inferoa/):
an **inference-native, tokenmaxxing agent harness** for long-horizon coding work, built on the vLLM stack.

**Live demo:** https://zozo123.github.io/inferoa-demo/

## What it shows

Press **RUN** and watch an 8-turn agent loop ("fix the failing auth test") spend tokens through two harnesses at once:

- **Context window** — cached prefix vs. fresh input vs. output, turn by turn
- **Semantic router** — each step routed to self-hosted vLLM or a frontier model, with the reason
- **Cost race** — cumulative spend: naive harness (full resend, raw tool dumps, frontier-only) vs. Inferoa

## What it is (and isn't)

This is a **client-side simulation** — no model is called. The mechanics are real; the rates are
parameterized from the results reported in the announcement:

| Lever | Reported result |
|---|---|
| Prefix-cache discipline | 90.0% cached-token discount |
| CodeGraph context | 80.8% context reduction |
| RTK tool records | 61.4% tool-output reduction |

Pricing in the sim is illustrative ($/Mtok: frontier 3.00 in / 0.30 cached / 15.00 out; self-hosted 0.10 in / 0.30 out).
With those parameters the simulated task lands at **~21.6× cheaper** with an **87% cache-hit rate**.

## Receipts — the real runs

The simulation is for legibility; these executed for real in isolated [islo.dev](https://islo.dev) sandboxes:

- **Real agent PR:** [inferoa-receipts#1](https://github.com/zozo123/inferoa-receipts/pull/1) — an agent in a sandbox was handed a genuinely failing test (tz-naive vs tz-aware datetimes; the task prompt named the suspected cause), produced the fix, re-ran pytest to green, and opened a PR containing the verbatim before/after output. It proves the sandboxed execute-verify-publish workflow, not unguided diagnosis.
- **Real Inferoa → real vLLM:** `inferoa@0.11.0` (npm) in one sandbox drove `vLLM v0.22.1` (Qwen2.5-0.5B, CPU, `enable_prefix_caching=True`) in another, wired through a public `islo share` URL. Inferoa's event log records `provider_id: vllm:openai_compatible:https://….share.islo.dev/v1` with 16,829-token prompts and stable prompt/tool-schema hashes.
- **Real prefix-cache metrics:** vLLM's Prometheus counters after the run — `prefix_cache_hits_total 1,611,008` / `prefix_cache_queries_total 1,647,574` = **97.8% measured hit rate** (the announcement claims 90%).
- **Honest limits:** a 0.5B CPU model demonstrates the mechanics, not frontier coding ability.

## Run locally

It's one `index.html`. Open it, or:

```
python3 -m http.server 8080
```

## The real thing

```
npm install -g inferoa
inferoa setup
inferoa
```

---

*Unofficial demo. Stack credits: vLLM Engine · vLLM Semantic Router · vLLM Omni · CodeGraph · RTK.*
