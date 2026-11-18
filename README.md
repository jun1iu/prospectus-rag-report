# Prospectus retrieval: what improved the answers

A small experiment in answering questions about IPO prospectuses with quoted evidence, and saying when the documents do not support an answer.

## The question

An IPO prospectus can run hundreds of pages. Finding the right passage is often harder than writing a fluent answer. Would adding keyword search or a second ranking step help?

## Experiment design

The document collection covers the final public SEC prospectuses for Figma, Reddit and CoreWeave. Documents were split into sections, then smaller passages that stay inside each section.

Three retrieval approaches were compared:

1. **Vector search:** find passages with similar meaning to the question.
2. **Hybrid search:** combine meaning-based matches with keyword matches using reciprocal rank fusion.
3. **Reranking:** retrieve a larger candidate set, then score those passages again and pass the best six to the answer step.

The harder evaluation set contains 36 questions, including cross-filing questions and questions the filings cannot answer. Retrieval was measured separately from the answers. An automated evaluator judged answers against reference answers; this is not an independent human audit.

## Reported results

| Approach | Evidence found, out of 33 answerable questions | Answers marked correct, out of 36 |
|---|---:|---:|
| Vector search | 26 logged; approximately 28 after evidence corrections | 33 |
| Hybrid search | 25 | 33 |
| Reranking, 15 candidates to 6 | 30 | 35 |

These are the original report's figures. The baseline retrieval score is provisional because accepted evidence wording changed after the baseline run. A rerun with identical references is needed for an exact comparison. The 35/36 result describes this small evaluation set, not accuracy on arbitrary prospectus questions.

## Why the approaches behaved differently

- **The first test was too easy.** An initial 22-question set could not distinguish the approaches, so harder questions were added.
- **Evidence matching can mislead.** Some retrieved passages contained the right fact in different wording. Accepting alternative evidence phrases changed how retrieval misses were counted.
- **The hybrid conclusion is not settled.** Several logged misses contain valid evidence in different wording. Exact-phrase scoring can penalize a useful result, so the logged hit counts do not establish that hybrid retrieval was worse.
- **Candidate order matters.** The reranking result suggests that some useful passages were already among the candidates but outside the final six.

These are observations about this implementation and dataset, rather than general conclusions that hybrid search is worse or reranking always helps.

## Audit findings

One three-company revenue-growth comparison was marked correct even though the answer explicitly lacked evidence for one company. Naming the eventual winner is not enough to demonstrate a supported comparison. The logged 35/36 therefore should not be read as 35 fully verified, evidence-complete answers.

Several retrieval misses contain the right fact in alternative prose or tables. Earlier runs also lack saved candidate IDs, preventing a complete retrospective rescore. Proposed adjusted totals are estimates, not replacement measurements. Freeze the scoring rules and rerun before claiming an exact retrieval improvement.

## Improvements to test next

1. Freeze and version the questions, accepted evidence and settings; rerun all approaches against the same references.
2. Inspect failures question by question, including cases where an answer was marked correct without a retrieval hit.
3. Measure citation support and correct abstention separately from answer correctness.
4. Compare a larger candidate pool while recording latency and cost alongside quality.
5. Evaluate on held-out questions and additional filings before drawing broader conclusions.

## Publication scope

This repository contains only the case study, reported aggregate results and experimental rationale. Application source, raw evaluation logs, credentials, configuration and private infrastructure details are excluded.

The evaluation source material is public filing content available through [SEC EDGAR](https://www.sec.gov/edgar/search/).
