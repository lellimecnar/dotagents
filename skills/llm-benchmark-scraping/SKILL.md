---
name: llm-benchmark-scraping
description: Use when comparing LLMs on public benchmarks. Fetch raw pages, parse.
metadata:
  version: "0.1.0"
  status: draft
  owner: maintainer
---

# LLM benchmark scraping

## When to use

You need hard numbers on model quality, speed or price from public leaderboards, and your page-extraction
tool is missing, search-only, or returns summaries instead of tables.

## Fetching

- Prefer the source’s public export, API or documented access method. When permitted, retrieve the public page with available tools and verify that it contains the expected data.
- Vendor model cards: fetch the raw README from the model hub
  (`huggingface.co/<org>/<model>/raw/main/README.md`) to get the table text, not a rendered summary.

## Optional public-source examples (verify the current access and format)

- **Artificial Analysis** model pages (`artificialanalysis.ai/models/<slug>`): headline numbers
  (intelligence index, time to first answer token, output speed, price) are in the visible text; per-eval
  scores sit in embedded JSON. Unescape `\"` first, then split the JSON into one segment per model at each
  `{"id":"<uuid>","slug":` boundary and read keys only inside that segment. A fixed-length window bleeds
  values from neighbouring comparison models; identical numbers repeating across different pages are the
  giveaway. Read the current key names from the page instead of assuming a list. Each page embeds many
  comparison models, so a few pages cover most of the field. Slugs are guessable but sometimes
  vendor-prefixed: grep `/models/[a-z0-9-]+` from an index page to find them.
- **Vals** (`vals.ai/benchmarks/<index>`): the ranked table renders as plain text
  `rank Name score% $cost latency`; the chart above it reads `score % Name`, so a name-then-number regex
  picks up the next row's score. Anchor on the rank.
- **EQ-Bench:** the data is CSV inside the page's JS files (for example `creative_writing.js`); find the
  backtick-delimited block and read its own header row (each file has its own).
- **Provider throughput trackers** (per-provider tok/s, 24 h average, time to first token) measure one
  provider; label them as such.

## Practitioner sentiment from accessible public sources

- Use aggregator pages that summarise recent community opinion by category, and your search tool's
  excerpts for individual posts.
- Hacker News item dates: `hacker-news.firebaseio.com/v0/item/<id>.json`. GitHub issues: the REST API
  (`/repos/<o>/<r>/issues/<n>`; unauthenticated search is heavily rate-limited). Discourse forums: append
  `.json` to `/t/<id>`.

## Reporting rules

- Indexes get rescaled between versions: never mix scores quoted in articles with current page values;
  cite the page and the date you read it.
- Aggregated speed is usually a median across providers (or the first-party API), not the speed your
  provider gives you. Say so.
- When a vendor claim and an independent measurement disagree, report both, with sources.
- Record each measurement with its source URL, version if available, and retrieval date in the task evidence.
