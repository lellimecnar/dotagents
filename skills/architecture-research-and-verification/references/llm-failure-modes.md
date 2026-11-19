# LLM failure modes in architecture work

Model-specific error rates go out of date within months. The rates below are research findings as published,
there to show *why* each mitigation exists. Re-validate on the current model before treating them as current.

| Failure mode | Evidence | Mitigation |
|---|---|---|
| **Hallucinated packages and APIs** (opens the door to slopsquatting) | 576,000 samples from 16 models: at least 5.2% hallucinated packages for commercial models, 21.7% for open-source (USENIX Security 2025) https://usenix.org/conference/usenixsecurity25/presentation/spracklen . A frontier-model replication found 3.6–5.0%, checked against live registries https://arxiv.org/abs/2605.17062 | Verify every package, API, flag and version against the registry or official docs at task time. This always pays: the check is cheap and the downside is a supply-chain attack or a dead API |
| **Stale knowledge and version drift** | Version-specific API misuse is a distinct failure mode https://aclanthology.org/2025.naacl-long.348.pdf | Check the docs *for the version in use*; read changelogs and deprecation notes |
| **Self-correction without external feedback** | Models "struggle to self-correct… without external feedback, and at times, their performance even degrades" (ICLR 2024) https://arxiv.org/abs/2310.01798 . Survey: no prior work shows successful self-correction with feedback from prompted LLMs, except on exceptional tasks https://arxiv.org/abs/2406.01297 . Positive counter-result on open-ended quality https://arxiv.org/abs/2303.17651 | Verify with external signals: docs, tests, measurements, a second independent retrieval. Self-review still helps with format and completeness, but don't count it as evidence of correctness |
| **Can't self-verify plans** | Pair the model with external verifiers ("LLM-Modulo") https://arxiv.org/abs/2402.01817 | Use tools, tests, model checkers and humans as verifiers |
| **Unfactored checking** | Chain-of-Verification: draft → plan verification questions → answer each *independently of the draft* → revise; hallucinations dropped across tasks https://arxiv.org/abs/2309.11495 | Answer verification questions in isolation from the draft, by retrieval for anything factual |
| **Single-sample brittleness** | Self-consistency (sample several reasoning paths, take the majority) gave large gains on reasoning benchmarks https://arxiv.org/abs/2203.11171 | For a close judgement call, generate several independent analyses and compare. Disagreement is a signal to gather evidence |
| **Debate treated as a verifier** | Debate can improve factuality https://arxiv.org/abs/2305.14325 , but it is not reliably better than self-consistency and is sensitive to hyperparameters https://arxiv.org/abs/2311.17371 | Don't use debate as the default check. It helps mainly when participants hold genuinely different evidence or tools |
| **Sycophancy** | "A general behavior of state-of-the-art AI assistants", partly driven by human preference data https://arxiv.org/abs/2310.13548 . Rises with the user's expressed certainty. Converting the user's statement into a question first cut it more than telling the model "don't be sycophantic" https://arxiv.org/abs/2602.23971 | Restate the requester's preferred option as a neutral question; evaluate it as one option among several; disclose that it was the requester's preference |
| **Prompt-induced human-like biases** (anchoring, bandwagon, framing) | Making background axioms and best practices explicit before answering cut SE-dilemma bias sensitivity by 51% on average; chain-of-thought and self-debiasing gave no significant per-bias reduction https://arxiv.org/abs/2604.16756 ; see also https://arxiv.org/abs/2511.05766 | List the relevant principles and assumptions explicitly before evaluating options |
| **False reversals under pushback** | Agents "abandon correct work when a user challenges it". Writing a private claim checklist with confidence *before* reading feedback "nearly eliminates false reversals in patch review" https://arxiv.org/abs/2610.34654 | Before reading criticism, record your claims, evidence and confidence; change only what new evidence overturns |
| **Over-compliance / no stopping** | No coding agent stopped refactoring once the code was already clean https://arxiv.org/abs/2610.36346 | Set explicit stopping criteria (the research plan's stopping rule; the spike's exit criteria) |
| **Plausible but low-precision rationale** | Precision 0.267–0.278 against expert rationale; 1.59–3.24% of arguments potentially misleading https://arxiv.org/abs/2504.20781 | Prune generated arguments. Keep only those tied to a driver, a source or a measurement |
| **Not yet human-level at architecture decisions** | Zero-shot ADRs are "relevant and accurate" but "fall short of human-level" https://arxiv.org/abs/2403.01709 ; simple prompting "does not produce quality ADDs" https://arxiv.org/abs/2504.08207 ; rigorous testing of GenAI outputs is typically missing in the literature https://arxiv.org/abs/2503.13310 | Use the model as an option generator and drafter; let evidence, tools and the decision owner filter. Records stay **Proposed** until a human accepts them |
| **Stated confidence taken at face value** | Calibration is good in-format but generalises poorly to new tasks https://arxiv.org/abs/2207.05221 | Confidence bands must rest on cited evidence, not on how sure the output sounds |
| **Human evaluators' new biases** | Work shifts from generating solutions to evaluating them, bringing new bias categories (taxonomy of 15) https://arxiv.org/abs/2601.08045 | Reviewers check the citations and verification rungs, not the fluency |

## A minimal verification routine for an agent

1. Draft the recommendation.
2. Extract every factual claim and every volatile fact (packages, APIs, limits, costs, versions, maintenance status).
3. For each, write a verification question and answer it by retrieval from a primary source, without looking at
   the draft's wording.
4. Restate any requester preference as a neutral question and confirm it was evaluated like the other options.
5. Record claims, evidence and confidence before seeking review.
6. Revise. Move anything still unverified into "Could not verify", with the risk if it is wrong.

Sources: inline above.
