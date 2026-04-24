# Shopiepie — AI-Powered Social Commerce Concierge

> An AI agent that turns an Instagram DM into a complete, personalised commerce journey — from identity verification to checkout — without the shopper ever leaving the conversation.

**Live Prototype:** https://shopiepie.lovable.app/

---

## Setup (≤ 3 commands)

```bash
git clone https://github.com/<your-username>/shopie.git
cd shopie
open curateai.html   # Works in any modern browser, zero dependencies
```

No build step, no API keys, no server. The prototype is a single self-contained HTML file (74KB).

---

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                    Shopiepie Chat Interface                     │
│                  (Instagram DM-style mobile UI)                │
├──────────┬──────────┬──────────┬──────────┬──────────┬──────────┤
│ Step 1   │ Step 2   │ Step 3   │ Step 4   │ Step 5-6 │ Step 7-9 │
│ Phone +  │ User     │ Intent   │ Product  │ Price +  │ Payment  │
│ OTP      │ Recog.   │ Select   │ Results  │ Rewards  │ + Order  │
├──────────┴──────────┴──────────┴──────────┴──────────┴──────────┤
│                     State Machine (9 steps)                     │
│              state.step: welcome → phone → otp →                │
│         intent → results → compare → rewards → payment → success│
├─────────────────────────────────────────────────────────────────┤
│                         Data Layer                              │
│  ┌──────────────┐  ┌───────────────┐  ┌──────────────────────┐  │
│  │ PROFILE      │  │ CATALOG       │  │ PRICE GRID           │  │
│  │ name, tier,  │  │ 30 products   │  │ 5 merchants per SKU  │  │
│  │ rewards,     │  │ 3 categories  │  │ best price flagged   │  │
│  │ brands       │  │ 10 brands     │  │ coupons + cashback   │  │
│  └──────────────┘  └───────────────┘  └──────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
```

---

## What I'd Build in V2

1. Real AI recommendations  
2. Proactive shopping alerts  
3. Cross-merchant user graph  
4. Social/group shopping  

---

*Submission for: GoKwik PM Hiring Assignment · Shopiepie · April 2026*
