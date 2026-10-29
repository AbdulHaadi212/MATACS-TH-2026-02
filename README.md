# MATACS — Associate Software Developer Assessment

**Candidate:** Abdul Haadi  
**Assessment reference:** MATACS-TH-2026-02  
**Time spent:** 30 Mins.

## Overview
This repository contains a design for an AI-assisted proposal triage workflow. AI extracts evidence from proposal documents; deterministic application code applies mandatory checks, computes weighted scores, and produces an explainable provisional ranking. A human reviewer makes the final award decision.

## Files
- [Process-flow diagram](diagram/process-flow.png)
- [Design write-up](WRITEUP.md)
- [AI prompts](PROMPTS.md)
- [Expected sample ranking](ranking.json)

## AI-use disclosure
I used ChatGPT to help structure and review this design, draft the prompts, and check the scoring approach against the assessment requirements. I reviewed the proposed ranking logic rather than accepting AI output blindly: for example, I did not treat the lowest-priced proposal as eligible because its Ontario electrical contractor licence was not stated for the bidder, and I treated the proposal's instruction to automated systems as untrusted document content rather than a scoring instruction. Before submitting, I will verify every file and replace the time-spent field with the actual time taken.

## Assumptions
- A mandatory requirement must be explicitly supported by proposal evidence; a subcontractor whose identity/licence is to be confirmed after award does not satisfy the bidder-level licence requirement.
- A proposal with a missing material weighted criterion is flagged and withheld from the final numeric ranking until a reviewer resolves the gap.
- The scores are illustrative and use the scoring rules documented in `WRITEUP.md`; the human reviewer retains award authority.
