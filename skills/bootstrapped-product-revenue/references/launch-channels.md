# Finding first paying users, launch channels, minimum viable monetization

Sources:
- [PG-DS] Paul Graham, "Do Things that Don't Scale": https://paulgraham.com/ds.html
- [HN] Hacker News, Show HN guidelines: https://news.ycombinator.com/showhn.html
- [PL] Stripe Payment Links: https://stripe.com/payments/payment-links
- [STR] Stripe pricing: https://stripe.com/pricing
- [SB] Stripe Atlas, "Business of SaaS": https://stripe.com/guides/atlas/business-of-saas

## Principles [PG-DS]
- Startups take off because founders make them take off; recruit users manually.
- Two reasons founders resist: shyness/preferring to code, and small absolute numbers seem pointless. Compound growth makes small numbers matter.
- Big launches are not the mechanism; Graham notes there may be an inverse correlation between launch magnitude and success (note 10).
- Early fragility is normal; don't judge a young product by an established one's standards.

## Minimum viable monetization (cheapest route to a first payment)
1. A hosted payment link: Stripe Payment Links create a payment page "in just a few clicks" and are shared as a link, "no code required" [PL]. Check the processor’s current fees and applicable terms [STR].
2. Manual delivery is acceptable at first: grant access by hand after payment (doing things that don't scale) [PG-DS].
3. In-app purchases only when the product lives in an app store and the store fee is acceptable (positioning-pricing.md).
4. Prefer card-required trials or paid-from-day-one over open free trials; card-required converts at ~40% baseline vs ~1% without [SB].
Opening a payment account and any paid tool needs the owner's go-ahead; prepare it, don't submit.

## Channel choice rules (working rules)
| Situation | First channel |
|---|---|
| Founder already in the segment's community | Direct written messages and posts there, then personal follow-up |
| Existing free users | Email them a paid offer; they are the warmest list |
| Developer/technical audience, product can be tried | Show HN (rules below) |
| No way to reach the segment manually | Rethink the segment or idea [PG-DS note 12] |
| Paid ads | Not before a working offer converts from manual outreach (spending rule) |

## Show HN rules [HN]
- For something you made that people can play with; must be something you worked on and will discuss.
- Off topic: blog posts, sign-up pages, newsletters, landing pages, fundraisers.
- Make it easy to try, ideally without signups or emails.
- Not for minor upgrades; title begins "Show HN".
- Don't ask friends to upvote or comment.
- "Don't post quickly-generated one-offs".
Implication: a waitlist landing page cannot be a Show HN.

## Landing / offer page checklist (working checklist built from the positioning and pricing references)
- Headline names the target customer and the value, against their current alternative (Dunford, positioning-pricing.md).
- One call to action: pay (or start a card-required trial). Not "join waitlist" unless explicitly testing interest only.
- Price visible; plans named for who they are for; free plan, if any, demoted.
- Refund promise in plain words (e.g. founding-member guarantee) to lower risk.
- How to reach the founder in writing.
- Measure: visits, clicks to pay, payments. Payments are the only success metric.

## Outreach message skeleton (Mom Test compatible)
"Hi [name], I'm working on [problem area] for [segment]. I'm not selling anything yet; I'm trying to learn how people handle [problem] today. Could I ask you three questions about the last time it came up? (Reply here is fine.)"
After learning: "Based on what you described, I've built [thing] that [value]. Founding price is $X/month, refund if it doesn't do [outcome] in 30 days. Here's the link: ..."
