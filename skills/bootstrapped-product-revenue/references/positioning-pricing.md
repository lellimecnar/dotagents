# Positioning and pricing

Sources:
- [AD] April Dunford, "A Quickstart Guide to Positioning": https://www.aprildunford.com/post/a-quickstart-guide-to-positioning
- [SP] Patrick McKenzie, "Pricing low-touch SaaS", Stripe Atlas guide: https://stripe.com/guides/atlas/saas-pricing
- [KZ] Patrick McKenzie, "Doubling SaaS Revenue By Changing The Pricing Model": https://www.kalzumeus.com/2012/08/13/doubling-saas-revenue/
- [SB] Stripe Atlas, "Business of SaaS": https://stripe.com/guides/atlas/business-of-saas
- [PG-DS] Paul Graham, "Do Things that Don't Scale": https://paulgraham.com/ds.html
- [STR] Stripe pricing: https://stripe.com/pricing
- [APL] Apple App Store Small Business Program: https://developer.apple.com/app-store/small-business-program/
- [GP] Google Play service fees: https://support.google.com/googleplay/android-developer/answer/112622

## Positioning: five components, worked in order [AD]
1. **Competitive alternatives**: "What would a customer do if your offering didn't exist?" Often "do nothing", a spreadsheet, a manual process. Dunford: in enterprise software "we typically lose 25% of deals to 'no decision.'"
2. **Differentiated capabilities**: what you have that the alternatives do not.
3. **Value**: for each capability ask "so what for customers?"
4. **Target customers**: who cares a lot about that value, and what characteristics make them care.
5. **Market category**: the frame that makes the value obvious.
Positioning is not messaging, a tagline or a vision [AD]. Dunford calls the fill-in-the-blanks positioning statement "pointless" because each component depends on the others [AD].

One-paragraph worksheet: "Instead of [alternative], [target customer] uses [product] because [capability] lets them [value]. It's a [category]."

## Pricing rules
| Rule | Source |
|---|---|
| Most technical founders misprice (too low) at the start; virtually no one gets pricing right first try, so test | [KZ] |
| Price is the easiest lever: replacing a small number with a bigger one | [SB] |
| Use 3-4 tiers loosely segmented by usage, named for who they are for, instead of linear per-unit or configurable pricing | [KZ] |
| Tier names should let a buyer self-select instantly (e.g. Hobbyist / Active Trader / Financial Professional, not Lite/Standard/Premium) | [SP] |
| Charge more, generally; higher prices price out "pathological" low-end customers who need heavy hand-holding | [SP][KZ] |
| Demote a free plan to text beneath the paid columns | [SP] |
| Don't offer "Unlimited" at a fixed low price; it anchors custom work too low | [SP] |
| $X.99 pricing communicates cheapness in B2B/prosumer; McKenzie has not seen $49 vs $49.99 vs $50 matter | [SP] |
| Reduce decision fatigue: default the buyer into the outcome option; consider outcome/success pricing | [SP] |
| Test packaging changes (what is in each plan), not just +/-$10 | [KZ] |
| Raising prices 25% "can result in 'accidentally' decreasing churn by 20%" via customer mix | [SB] |
| Annual plans bring up-front cash and lower churn | [SB] |
| Typical low-touch price points: ~$10/month B2C, $20-$500/month B2B | [SB] |

**Where sources pull differently:** McKenzie says charge more and price out low-end customers [SP][KZ]; Graham says that when choosing between the segment that signs up quickest and the one that pays most, pick the quickest because they are the early adopters [PG-DS, note 8]. Rule followed here: target the early-adopter segment, but still charge a real price from the first offer, because the goal is proof of payment.

## Fees (compare at decision time)
Payment and app-store fees change by date, market, program and product type, so none are written
here. Before choosing where to take payment, open each channel's official fee page, compute the
net per sale for the actual price and plan type (one-off, subscription, international buyer), and
quote the figures with the check date.

| Channel | Where the current fee lives | Watch for |
|---|---|---|
| Card processor (e.g. Stripe) | the processor's own pricing page [STR] | per-transaction % + fixed fee; international and currency-conversion add-ons |
| Apple App Store | Apple's developer program / small business program pages [APL] | reduced-rate program eligibility |
| Google Play | Play Console service-fee help page [GP] | separate service and billing fees; regional rules; alternative billing and external links |

Method: compare store fees against a hosted card checkout for the same plan; a store's
distribution can be worth its fee, but say what the fee costs at the expected volume.

## Pricing worksheet for a first offer
1. Alternative the customer uses now and what it costs them (money + hours x their hourly value).
2. Price = a fraction of that cost, rounded to a clean number. Never derived from your hosting cost.
3. One paid plan (or two), monthly and annual. Optional founding-member price with a written refund promise.
4. Payment collected by a hosted link (launch-channels.md).
