# Shopiepie — AI-Powered Social Commerce Concierge

> An AI agent that turns an Instagram DM into a complete, personalised commerce journey — from identity verification to checkout — without the shopper ever leaving the conversation.

**Live Prototype:** [https://shopiepie.lovable.app/](https://shopiepie.lovable.app/)
Loom Video explanation: https://www.loom.com/share/8d998af8bfec456f98f7aba8e6246b54
---

## Setup 


open https://shopiepie.lovable.app/  # Works in any modern browser, zero dependencies
```

No build step, no API keys, no server. The prototype is a hostel url.
---

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                     Shopiepie Chat Interface                     │
│                   (Instagram DM-style mobile UI)                │
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

### The 9-Step Journey

| Step | Moment | What Happens | Design Decision |
|------|--------|-------------|-----------------|
| 1 | **Phone Verification** | +91 phone input with auto-formatting → 4-digit OTP with error/success states | Lightweight identity — no app install, no social login SDK |
| 2 | **User Recognition** | Profile loaded silently. Header shows Gold tier + ₹480 balance. System divider confirms identity | Silent context loading — the intelligence should feel ambient, not announced |
| 3 | **Intent Selection** | 4 chips: Red Top, Black Jeans, Other (→ text input), Upload Screenshot (→ gallery) | Structured intent reduces ambiguity. "Other" auto-focuses the keyboard for free-text |
| 4 | **Product Discovery** | 5 scrollable product cards with inline SVG illustrations. "View more" loads 5 additional unique cards | SVG silhouettes never fail to render (no broken image URLs). Cards are 152px — three visible at once |
| 5 | **Price Comparison** | Product price across Myntra, Ajio, Amazon, Flipkart, Nykaa. Best price highlighted with coupon + cashback tags | Cross-merchant comparison is GoKwik's network moat made tangible |
| 6 | **Rewards Application** | Gold tier gradient card → ₹480 deducted → final price updated in real-time | "Skip" option respected without guilt. Earn-forward messaging in checkout |
| 7 | **Payment Selection** | UPI (inline input), PhonePe, GPay, Paytm, Card (number + expiry + CVV expansion). CTA disabled until valid | Each method has its own expansion state. Validation is per-method |
| 8 | **Order Success** | Animated checkmark, order ID (SPI-XXXXXX), summary, earn-forward note, 2 CTAs with real ecommerce URLs | "You'll earn ₹X on this order" closes the loyalty flywheel loop |
| 9 | **V2 Proactive Tease** | 3s after success: "I'll ping you when your favourite brands drop something matching your taste" | Makes the reactive→proactive v2 vision tangible inside v1 |

---

## Design Decisions (and why they matter)

### 1. Single-file HTML, zero dependencies
The brief says "runs in ≤3 commands." A single HTML file runs in zero commands. No Node, no Python, no API keys. The evaluator opens the file and the demo starts. This is a prototype — speed-to-evaluate beats architecture elegance.

### 2. Inline SVG product illustrations instead of remote images
Every product card uses a hand-crafted SVG silhouette (dress, jeans, top, blazer, hoodie, etc.) with the product's brand color as a gradient fill. This was a deliberate choice over Unsplash/CDN URLs because:
- Images from CDNs fail silently in offline/sandboxed environments
- Broken image placeholders destroy the "concierge" illusion
- SVG silhouettes look intentional and designed, not like missing assets

### 3. Phone + OTP instead of Instagram handle lookup
The original brief suggests "social handle or lightweight OTP." I chose OTP because:
- It's a real identity primitive GoKwik already has
- It demonstrates cold-start handling (new users can verify too)
- It avoids the "how did this bot know my data?" trust problem
- The OTP flow has 4 distinct UX states (empty, active, error with shake animation, success with green transition) — demonstrating component-level design thinking

### 4. Intent chips with structured routing
Instead of a free-text-only input, the 4 chips (Red Top, Black Jeans, Other, Upload Screenshot) create structured pathways. This matters because:
- Pre-defined intents → instant results (no LLM latency)
- "Other" auto-focuses the text input (keyboard-first mobile UX)
- "Upload Screenshot" triggers native gallery picker
- The agent routes to the same product UI regardless of input method — consistent experience

### 5. 5 merchants, not 3
The brief says "across the merchant network." Showing 5 real merchants (Myntra, Ajio, Amazon, Flipkart, Nykaa Fashion) with per-merchant coupons and cashback percentages makes the network intelligence concrete. The "BEST" badge and green highlight make the optimal choice unmissable.

### 6. Payment method expansion states
Each of the 5 payment methods has its own UX:
- **UPI:** inline text input for UPI ID with regex validation
- **PhonePe/GPay/Paytm:** single-tap with "you'll confirm in the app" feedback
- **Card:** 3-field expansion (number with auto-formatting, MM/YY, CVV)
- The "Tap to complete purchase" CTA stays disabled until a valid method is configured — no false-positive taps

### 7. Earn-forward loyalty messaging
After checkout, the success card shows "You'll earn ₹X rewards on this order · next time's on us." This single line demonstrates the loyalty flywheel: spend → earn → return → spend. It's the entire GoKwik network thesis in one sentence.

---

## Mock Data Schema

### Shopper Profile
```javascript
{
  name: 'Priya',
  city: 'Bengaluru',
  tier: 'Gold',           // Silver | Gold | Platinum
  rewards: 480,            // ₹ balance
  last_order_days: 12,
  preferred_brands: ['Zara', 'Urbanic', 'Snitch', 'H&M'],
}
```

### Product
```javascript
{
  id: 'rt1',
  brand: 'Zara',
  name: 'Ribbed Red Knit Top',
  icon: ICON_TOP,          // Inline SVG silhouette
  color: '#dc2626',        // Brand gradient fill
  price: 1499,             // Best network price
  mrp: 1999,               // Maximum retail price
  cashback: 8,             // % via network
  best: 3,                 // Index of best merchant (0-4)
}
```

### Price Grid (generated per product)
```javascript
// 5 merchants: Myntra, Ajio, Amazon, Flipkart, Nykaa Fashion
[
  { merchant: 'Myntra',     price: 1544 },
  { merchant: 'Ajio',       price: 1649 },
  { merchant: 'Amazon',     price: 1604 },
  { merchant: 'Flipkart',   price: 1499, coupon: 'ELITE10', cashback: 8 },  // BEST
  { merchant: 'Nykaa Fashion', price: 1664 },
]
```

### Catalog Structure
- **30 products** across 3 intent categories
- **Red Top:** 10 products (Zara, H&M, Snitch, Urbanic, Vero Moda, Only, Forever 21, Mango, AND, W)
- **Black Jeans:** 10 products (Levi's, H&M, Zara, Pepe, Jack & Jones, Diesel, Wrangler, Lee, Calvin Klein, Tommy Hilfiger)
- **Custom/Other:** 10 products (Uniqlo, COS, Zara, H&M, Mango, Snitch, Urbanic, AND, Vero Moda, Bewakoof)

---

## Edge Cases Handled

| Case | What Happens |
|------|-------------|
| Wrong OTP (any code ≠ 1234) | Shake animation + red borders + "Incorrect code — try again" |
| OTP resend | Boxes clear, green "✓ New code sent" feedback |
| "Other" intent selected | Text input auto-focuses with cursor, placeholder shows examples |
| "Upload Screenshot" selected | Native gallery picker opens, image preview shown in chat |
| "View more" clicked | 5 additional unique products load, shelf scrolls to show them |
| All 10 products shown | "View more" becomes "All options shown" (disabled) |
| App-based payment selected | "Selected · you'll confirm in the app" green text feedback |
| Invalid UPI ID entered | CTA remains disabled (regex: `[\w.\-]{2,}@[a-zA-Z]{2,}`) |
| Incomplete card details | CTA remains disabled until 16 digits + MM/YY + 3 CVV |
| Reset (↻ button) | Full state reset, fresh conversation from phone verification |

---

## File Structure
```
shopiepie/
├── shopiepie.html          # Complete prototype (74KB, self-contained)
├── README.md              # This file
├── PRODUCT_THINKING.md    # 1-page product thinking note (deliverable)
└── LOOM_SCRIPT.md         # Word-for-word recording script (deliverable)
```

---

## Tech Stack
- **Frontend:** Vanilla HTML/CSS/JS (no framework — maximum portability)
- **Design system:** Custom CSS variables, 8px grid, Instagram-native dark palette
- **Product visuals:** Inline SVG silhouettes with dynamic gradient fills
- **State management:** Single `state` object with 9-step progression
- **Zero external dependencies** — runs offline, no API keys required

---

## What I'd Build in V2

See the full argument in  Summary:

1. **Real LLM integration** — Claude Sonnet for vision (image → style tags → catalog match) and personalised concierge copy
2. **Proactive DMs** — Agent initiates conversations when preferred brands drop new collections
3. **Cross-merchant preference graph** — Every session feeds a unified preference vector across the GoKwik network
4. **Group chat mode** — Friends styling together, agent mediates, checkout for each participant

---

*Submission for: GoKwik PM Hiring Assignment · Shopiepie · April 2026*
