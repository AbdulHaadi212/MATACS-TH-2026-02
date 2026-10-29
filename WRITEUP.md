# Design write-up

## 1. AI vs. rules
Use ordinary application code for file-type/size checks, access control, category selection, mandatory pass/fail checks, weight validation, score calculation, sorting, and audit logging. These operations need predictable outcomes and should not depend on a language model. Use AI to extract structured facts from varied proposal layouts and map supporting passages to admin-defined criteria. Require each extracted fact to include a quote and page reference; use `unknown` rather than guessing. A rules layer validates the result before it affects scoring.

The criteria and weights come from the admin configuration, never from hard-coded category-specific logic. Validate that weights total 100%, that mandatory criteria have pass/fail definitions, and that each criterion has a documented scoring rubric before a category can be used.

## 2. Consistency and explainability
Store a versioned scoring rubric and a versioned extraction prompt/model configuration for every run. Apply deterministic scoring to the same validated facts, use fixed rounding (two decimal places), and sort ties using a documented tie-break rule (first fewer missing/flagged criteria, then higher experience score, then alphabetical proposal ID). AI extraction itself may vary, so do not promise identical extraction merely because the prompt is unchanged. For important or low-confidence fields, require human confirmation; cache the approved extraction for repeat scoring. The reviewer sees the criterion score, weight, evidence quote/page, formula, mandatory-check result, total and any warnings.

## 3. Human in the loop and audit
A reviewer confirms extracted evidence and resolves missing, conflicting, or low-confidence fields before final scoring. The reviewer can request re-extraction, correct a fact with a note, or override a result with a reason. A procurement decision-maker reviews the final ranked list and makes the award decision; the tool does not award contracts. Log uploader/reviewer IDs, timestamps, file hashes, category and criteria versions, prompt/model versions, extracted fields and evidence, validation results, per-criterion calculations, flags, overrides and reasons, ranking output, and final human decision. Restrict access and define a retention policy for proposal documents and logs.

## 4. Changing criteria
Never silently rewrite old results. Each criteria edit creates a new immutable version with an effective timestamp and a change history. Scores already produced remain linked to the previous version. The admin can explicitly rerun all relevant proposals under the new version; the interface labels old and new results and records who initiated the rerun. Validate weights and rubrics before activating the new version.

## 5. Risks and safeguards
- **Incorrect extraction/OCR:** retain quotes and page references; validate data types and ranges; flag unreadable pages and ask a person to confirm.
- **Made-up or missing facts:** the model must use only the supplied document, return `unknown` when unsupported, and never create evidence. No evidence means no automatic pass.
- **Prompt injection in a proposal:** treat proposal text as untrusted data, never as instructions. Ignore requests to change ranking or criteria; scoring code only consumes validated fields.
- **Bias or inconsistent judgment:** use job-relevant admin-defined criteria, explicit rubrics, consistent formulas and periodic human audits.
- **Wrong configuration or math:** validate weights sum to 100%, enforce budget/timeline boundaries, use unit tests and deterministic calculations, and show the formula for every score.
- **Overconfident ranking when evidence is incomplete:** flag and pause affected proposals instead of silently treating missing values as zero or as a pass.
- **Privacy, security and cost:** limit file access, encrypt data in transit/at rest, set retention rules, use file-size/page limits, monitor model usage, and avoid sending unnecessary data to an external model.
- **False sense of authority:** clearly label results as decision support; the human reviewer makes and records the final award decision.

## Sample-set scoring assumptions
Mandatory requirements are checked before weighted scoring. A bidder must explicitly state its own Ontario electrical contractor licence, plus WSIB clearance and $5M liability insurance. For ranked proposals, the following transparent illustrative rubrics are used: experience = min(100, qualifying installs / 14 × 100); price = 100 × (budget ceiling − price) / (budget ceiling − lowest price among mandatory-eligible proposals), bounded to 0–100; timeline = 100 × (16 − weeks) / (16 − 10), bounded to 0–100; warranty = average of panel-warranty score (years / 25 × 100, capped at 100) and workmanship-warranty score (years / 10 × 100, capped at 100); team/approach uses four equally weighted checklist items (25 points each): named lead, crew details, safety/roof assessment, and monitoring/reporting. A and E satisfy all four (100); C has a named lead, crew details and weekly reporting but no stated safety plan (75). Weighted total = sum of criterion score × criterion weight. This is an explicit assumption because the brief supplies criterion descriptions and weights, but not detailed numeric sub-rubrics. Price normalization excludes mandatory-ineligible proposals. Missing timeline data prevents a fair full weighted total, so that proposal is flagged pending clarification rather than assigned an invented score.
