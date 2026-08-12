# ECHOBURN — Agent Failure Series #22

> **Agent reports ✅ $99.99 · Bank statement shows -$399.96**

![Series](https://img.shields.io/badge/Agent%20Failure%20Series-%2322-f85149?style=flat-square)
![Status](https://img.shields.io/badge/status-live-3fb950?style=flat-square)
![Tech](https://img.shields.io/badge/built%20with-vanilla%20HTML-58a6ff?style=flat-square)

---

## What Is This

A single-file interactive demo showing **idempotency blindness** — the silent failure mode where an AI agent retries a timed-out tool call without an idempotency key, causing the same action to execute multiple times on the provider side.

The agent dashboard stays green. The Stripe ledger fills up.

## The Failure Mode

```
Agent → stripe_charge($99.99) → TIMEOUT (waiting 30s)
Agent → stripe_charge($99.99) → TIMEOUT (attempt 2, no idempotency key)
Agent → stripe_charge($99.99) → TIMEOUT (attempt 3)
Agent → stripe_charge($99.99) → SUCCESS ✅

Agent says: "Payment processed: $99.99"
Stripe says: 4 charges × $99.99 = $399.96
```

The original charge didn't fail — it just didn't respond in time. Every retry created a new charge. The customer is billed 4× before anyone notices.

## The Fix

Toggle the **Idempotency Key** switch in the demo. With a key:

```
stripe_charge($99.99, idempotency_key="order-CX4421-1723519200")
```

Stripe deduplicates all retries against the same key and returns the cached result. One charge. Always.

## Three Scenarios

| Scenario | Provider | Duplicate Effect |
|----------|----------|------------------|
| Payment Charge | Stripe | Customer charged N× |
| Welcome Email | SendGrid | Customer spammed N× |
| Inventory Deduct | Inventory API | Stock decremented N× |

All three show the same pattern — different consequences.

## What's Inside

```
index.html          Single-file interactive demo (760 lines)
README.md           This file
```

## How to Run

**Live demo (recommended):**  
👉 [https://rlasaf12.github.io/echoburn/](https://rlasaf12.github.io/echoburn/)

**Local:**
```bash
open index.html
# or
python3 -m http.server 8080
```

No dependencies. No build step. No API keys.

## Controls

| Control | What It Does |
|---------|-------------|
| Idempotency Key toggle | ON = provider deduplicates · OFF = full overcharge |
| Retry limit | 2, 3, or 4 retries before final success |
| Scenario | Switch between Payment / Email / Inventory |
| Run | Start the animated scenario |
| Reset | Clear all panels |

## Part of the Agent Failure Series

| # | Name | Failure Mode | Live |
|---|------|-------------|------|
| 19 | ORPHANCALL | Fire-and-forget with no confirmation | [↗](https://rlasaf12.github.io/orphancall/) |
| 20 | BLEEDTHROUGH | Context leaking across agent turns | [↗](https://rlasaf12.github.io/bleedthrough/) |
| 21 | TOOLROT | Cached tool schema diverging from reality | [↗](https://rlasaf12.github.io/toolrot/) |
| **22** | **ECHOBURN** | **Idempotency blindness — duplicate side-effects** | **← you are here** |

Built by [Harel Asaf](https://harelasaf.com) · Part of nightly autonomous prototype work

