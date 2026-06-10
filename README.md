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
