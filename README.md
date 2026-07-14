# Mac-Bid Total Cost Calculator

A single self-contained HTML file (`macbid-fixed.html`) that estimates the real, all-in cost of bidding on a Mac-Bid item and grades whether it's actually a good deal — before you commit.

No build step, no dependencies. Open the `.html` file in any browser.

## Inputs

| Field | Purpose |
|---|---|
| **Retail Value ($)** | What the item sells for new/full price. Used as the baseline for grading and for the condition-based suggested price. |
| **Condition** | Open box (75% off), Open box (70% off), Like-new (66% off). Sets a **suggested starting-bid cap** for that condition tier — see below. |
| **Current Bid Amount ($)** | Your current/planned bid. Drives *Total Cost for Your Bid*. |
| **I Only Want to Pay ($)** | Your target all-in budget. Drives *Max Bid to Hit Your Target*. Auto-fills from the condition's suggested price until you edit it yourself (see "Auto-fill" below). |

All three of Retail Value, Bid, and "I Only Want to Pay" work **independently** — you can fill in just one to get a quick answer without the others. For example, entering only a Bid Amount still shows you Total Cost; entering only "I Only Want to Pay" still shows you the Max Bid. Nothing gets cleared just because another field is empty.

## Outputs

| Field | Meaning |
|---|---|
| **Suggested Price for Condition** | `Retail Value × (1 − discount)` for the selected condition. A personal bidding cap/reference — it does **not** feed into Deal Status. |
| **Amount Saved** | `Retail Value − Total Cost`. |
| **Total Cost for Your Bid** | What you'd actually pay if you won at your current bid, fees included (formula below). |
| **Max Bid to Hit Your Target** | The highest bid you can place and still land at or under "I Only Want to Pay" once fees are added. |
| **Deal Status** | A Great / Good / Fair / Bad grade based purely on `Total Cost ÷ Retail Value` (see below). |

## Key concepts

### 1. Total cost formula

Mac-Bid (like most liquidation auction sites) adds a buyer's premium and sales tax on top of your winning bid, plus a flat handling fee:
premium = bid × 15%
subtotal = bid + $3.00 handling fee + premium
totalCost = subtotal × (1 + 9.1% tax)


These constants (`handlingFee`, `premiumRate`, `taxRate`) live at the top of the `<script>` block — adjust them if Mac-Bid's fee structure changes.

### 2. Suggested Price ≠ Deal Status (they're independent)

- **Suggested Price** is a simple bidding *cap/reference*: "for an open-box item at 75% off, I shouldn't bid more than $X." It's driven entirely by the Condition radio buttons and Retail Value.
- **Deal Status** is the actual verdict on the deal, and only looks at `Total Cost ÷ Retail Value`. It ignores the Condition selection entirely.

This is intentional: the Condition/Suggested Price is a personal guideline that informs your bidding strategy but doesn't control or override the objective total-cost grade. You can bid above the suggested price and still get graded "Good deal" if fees + tax still land you well under retail — see the walkthrough example below.

**Example:** Retail = $200, Condition = Open box 75% off → Suggested Price = $50. You bid $70 anyway (over the suggested cap). Total Cost = $91.10 (with fees/tax), which is 45.5% of retail → still grades as **"Good deal"** (falls in the 40–50% band), even though you exceeded the condition's suggested price.

### 3. Deal Status tiers

Grading is based on `Total Cost ÷ Retail Value` — the all-in price you actually pay, as a percentage of what the item costs new:

| Total cost as % of retail | Grade |
|---|---|
| ≤ 40% | 🟢 Great deal |
| 40–50% | 🟢 Good deal |
| 50–65% | 🟡 Fair deal |
| > 65% | 🔴 Bad deal |

**Why these cutoffs:** liquidation/returns auctions carry real risk (no warranty, no returns, possible missing parts/cosmetic damage), so the discount needs to be meaningful to be worth it. 50% off all-in is a reasonable floor for "good" — below that, after paying the 15% premium and 9.1% tax on top of your bid, you're not saving enough over retail (or an open-box deal at a regular retailer with a warranty) to justify the risk.

Note these tiers are based on the *total cost including fees*, not the raw bid — a raw bid of, say, 44% of retail already becomes ~50%+ of retail once premium and tax are added, so the effective bar for a "Good" bid (in raw-bid terms) is noticeably lower than 50%.

If Deal Status shows **"Bid Not Set"** or **"Add Retail Value to Check"**, it just means one of those two required inputs is missing — the other calculator fields (Total Cost, Max Bid) still work fine without them.

### 4. Auto-fill behavior for "I Only Want to Pay"

When you enter a Retail Value or change Condition, "I Only Want to Pay" auto-fills with the Suggested Price — but only until you manually edit that field yourself. Once you've typed your own number in, the tool stops overwriting it, even if you later change Retail Value or Condition. This lets you use the suggested price as a quick starting point without it fighting you if you want a different target.

## Bonus: Future Time Calculator

A small unrelated utility at the bottom of the page — enter hours/minutes and it shows what the local date/time will be that far from now. Useful for figuring out when an auction actually closes relative to "now."

## Customizing

All the tunable numbers live near the top of the `<script>` block:

- `handlingFee`, `premiumRate`, `taxRate` — Mac-Bid's fee structure
- `getDiscount()` — the % off for each Condition option
- The `costRatio` thresholds (`0.40`, `0.50`, `0.65`) inside `calculate()` — the Deal Status tier cutoffs

Edit the values directly and reload the page; no build step required.

