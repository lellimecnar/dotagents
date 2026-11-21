# Unit economics basics

Sources:
- [SB] Stripe Atlas, "Business of SaaS" (Patrick McKenzie): https://stripe.com/guides/atlas/business-of-saas
- [PG-G] Paul Graham, "Startup = Growth": https://paulgraham.com/growth.html
- [PG-DS] Paul Graham, "Do Things that Don't Scale": https://paulgraham.com/ds.html

## The fundamental equation [SB]
revenue = Σ (acquisition × conversion × ARPU) / churn
- Acquisition: prospects reached. Conversion: % who become paying customers.
- ARPU: average revenue per account per period.
- Churn: % of customers in a period who stop paying (200 paying in January, 190 of them pay in February = 5%).
- Expected lifetime ≈ 1 / churn. Example from the source: 5% monthly churn → 20-month lifetime; at $30/month → $600 lifetime revenue per customer.
- Improvements multiply: +10% acquisition and +10% conversion = +21%.
- Plateau: customers at plateau = acquisition × conversion / churn. If those cannot improve, growth stops.
- SaaS COGS is often under 5-10% of marginal revenue, so the cost that matters is CAC (marketing and sales spend per new customer).

## Sales model [SB]
- Low-touch: buyers purchase without sustained one-to-one contact; website, email, free trial. Price points cluster ~$10/month B2C, $20-$500/month B2B (ACV ~$100-$5K). Key metric: MRR.
- High-touch: sales-led; SMB ACV $6K-$15K, enterprise six figures+. Key metric: ARR.
- Running both on one product rarely works; usually one strangles the other.

## Low-touch benchmarks (use only once there are paying users) [SB]
| Metric | Poor | Baseline | Good |
|---|---|---|---|
| Trial→paid, no card required | substantially below 1% | ~1% | 2%+ (extremely good) |
| Trial→paid, card required | substantially below 40% | 40% | 60% |
| Monthly customer churn | 10%+ (existential threat) / 7% (low-hanging fruit or hard market) | ~5% where you expect to start | 2% (very sticky) |
Requiring a card up front generally increases net new paying customers [SB]. Selling to prosumers/freelancers structurally raises churn [SB]. Better marketing can lower visitor→trial conversion because it attracts poorer-fit visitors [SB].

## Growth expectations: the sources differ by funding model
| Context | Expectation | Source |
|---|---|---|
| YC-funded startup | 5-7% a week is good; 10% exceptional; 1% means you haven't figured it out. Best metric: revenue; next best: active users | [PG-G] |
| Bootstrapped SaaS | Often ~18 months before profitable enough to pay founders reasonable wages; then 10-20% year-over-year growth can be a very happy outcome | [SB] |
| VC-scaled SaaS | "3, 3, 2, 2, 2" from >$1M ARR | [SB] |
Followed here: Stripe's bootstrapped expectations for planning income timelines, and Graham's weekly measurement for feedback. Do not tell a solo founder that slower-than-YC growth is failure.

## Early-stage numbers to track weekly (before benchmarks apply)
Conversations held, commitments asked, commitments given, payments, MRR, churned payers, reason for each churn.
Compound growth from small numbers is real: 100 users growing 10%/week becomes ~14,000 after a year [PG-DS].
