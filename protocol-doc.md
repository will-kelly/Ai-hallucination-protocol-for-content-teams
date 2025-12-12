# AI Hallucination Review Protocol for Technical Content

## Purpose

This protocol defines how to **detect, flag, and correct AI hallucinations** in technical content before publication. It ensures all AI-assisted output is:

* Factually accurate
* Traceable to a system of record
* Reviewed and approved by a human subject-matter expert (SME)

**No AI-assisted technical content is published without verification.**

---

## Scope

This protocol applies to:

* Technical documentation
* READMEs and repositories
* API references and schemas
* Runbooks and architecture docs
* Support macros and knowledge-base articles

It covers content **generated or edited using AI tools**, including (but not limited to):

* Custom GPTs
* IDE copilots
* Knowledge-base AI assistants
* Generative writing tools

---

## Key definitions

**Hallucination**
A statement presented as fact that:

* Cannot be verified against a system of record, or
* Contradicts the system of record

**System of record (SoR)**
The authoritative source of truth, such as:

* Source code
* API specifications (OpenAPI / GraphQL)
* Schemas
* Release notes
* Maintained runbooks or design docs

---

## Roles and responsibilities

### Content author (human)

* Declares AI assistance
* Provides source bundle
* Flags uncertainty
* Resolves issues or escalates

### Technical reviewer (SME)

* Verifies facts against the SoR
* Approves or blocks publication
* Owns final accuracy decision

### Editor or governance reviewer

* Confirms labeling and metadata
* Checks citations and risk level
* Ensures auditability

### AI tool owner

* Maintains prompts and retrieval sources
* Reviews hallucination patterns
* Tunes guardrails and workflows

### Compliance or security (as needed)

* Reviews incidents with regulatory or customer impact

---

## Allowed, restricted, and banned AI usage

### Allowed

* Outlining and scaffolding
* Rewriting for clarity
* Summarization
* Code comment expansion
* Extracting fields from schemas

### Restricted (SME required)

* API references
* Upgrade instructions
* Security configuration
* Version-specific behavior

### Banned

* Inventing APIs or endpoints
* Fabricating benchmarks
* Claiming roadmap items
* Creating customer quotes or names

---

## Hallucination detection signals

### Human review signals

* Unknown APIs, flags, or features
* Exact defaults or limits without citations
* Version inconsistencies
* Security or compliance claims without sources
* Terminology inconsistent with glossary

### Automated signals

* Text not present in retrieval context
* Code identifiers not found in repo
* Schema or API mismatches
* Broken or misleading links
* Glossary violations
* Low embedding similarity between claim and citation
* References to future dates or releases

---

## Severity classification

| Severity          | Description                                             | Action             |
| ----------------- | ------------------------------------------------------- | ------------------ |
| **P0 – Critical** | Incorrect commands, APIs, configs, or security settings | Block merge        |
| **P1 – High**     | Version drift, wrong defaults, misleading architecture  | SME escalation     |
| **P2 – Medium**   | Vague or uncited claims                                 | Fix before publish |
| **P3 – Low**      | Style or clarity issues                                 | Editorial backlog  |

---

## Review workflow

### 1. Intake

* Author marks content as **AI-assisted**
* Source bundle attached (code, specs, tickets, releases)

### 2. Automated pre-checks

* Schema and API linting
* Repo identifier validation
* Glossary diff
* Link validation
* Retrieval mismatch detection

### 3. Editorial screening

* Scan for confident specifics
* Check citations
* Escalate P0 or P1 issues

### 4. SME verification

* Validate every factual claim against SoR
* Mark claims as:

  * ✅ Verified
  * ⚠️ Unclear
  * ❌ Incorrect

### 5. Correction

* Remove or rewrite incorrect statements
* Add citations
* Document limitations or uncertainty

### 6. Approval

* Editor validates metadata
* SME approves technical accuracy
* Merge blocked without both approvals

### 7. Post-merge learning

* Log hallucination incident
* Update prompts, retrieval sources, or tooling

---

## Required metadata (front matter or page properties)

```yaml
ai_generated: true | partial
sources:
  - link-to-code-or-spec
verified_by:
  - sme_handle
  - editor_handle
review_date: YYYY-MM-DD
risk_level: P0 | P1 | P2 | P3
model: model-name-and-version
retrieval_context: repo / branch / commit / snapshot
```

---

## Audit trail requirements

* Prompt and retrieval context retained for 90–180 days
* Reviewer comments preserved in PR or page history
* Failed checks logged
* Hallucination log maintained with:

  * Date
  * File
  * Severity
  * Failure mode
  * Root cause
  * Fix
  * Model and prompt version

---

## Escalation and SLAs

| Severity    | SLA                                     |
| ----------- | --------------------------------------- |
| **P0**      | Fix within 24 hours and incident review |
| **P1**      | Fix within 3 business days              |
| **P2 / P3** | Next documentation sprint               |

If hallucinated content is published:

* Add a public correction note
* Update changelog or page history

---

## Platform implementation notes

### GitHub and docs-as-code

* CI enforces schema, link, and glossary checks
* CODEOWNERS required for reference content
* Failing checks block merges

### Confluence or Notion

* Page properties store metadata
* Approval workflow required for publish
* “Verified” status enforced

### Static site generators

* Front-matter schema validation
* Build fails on missing metadata or dead citations

---

## Training and enablement

* Train reviewers on common hallucination patterns
* Require evidence-first edits
* Review hallucination logs monthly
* Continuously tune prompts and retrieval sources

---

## Author preflight checklist

* [ ] AI assistance declared
* [ ] Source bundle attached
* [ ] All specifics cited
* [ ] Link and glossary checks passed

---

## Editor screening checklist

* [ ] No uncited defaults or limits
* [ ] Version alignment verified
* [ ] Metadata complete
* [ ] P0 or P1 escalated

---

## SME verification checklist

* [ ] All claims verified against SoR
* [ ] Examples tested
* [ ] Edge cases and constraints documented
* [ ] Approval recorded

---

## Hallucination issue template

```yaml
title: "[Hallucination] <short description>"
labels:
  - hallucination
  - docs
  - severity:P1
assignees:
  - sme_handle
body:
  observed_text: "<quoted content>"
  expected_truth: "<verified statement>"
  system_of_record_links:
    - "<code or spec link>"
  failure_mode:
    - invented_entity | version_drift | wrong_default | schema_mismatch | other
  impact:
    - breaks_build | misleads_users | internal_confusion
  reproduction: "<how detected>"
  fix: "<corrected content>"
  model_prompt_version: "<model + prompt commit>"
```

---

## Review flow (quick reference)

```
AI-assisted draft
      |
      v
Automated checks (schema / links / glossary)
      |
      v
Editorial screening
      |
      v
SME verification
      |
      v
Corrections + citations
      |
      v
Editor + SME approval
      |
      v
Publish → log → tune AI
```

---

## Enforcement rules

* AI-assisted content **must declare provenance**
* Automated checks **must pass**
* SME approval **is mandatory**
* Prompts and retrieval snapshots **must be retained**
* Public corrections **are required for shipped hallucinations**

---

If you want, the next clean step would be to:

* Extract this into `/docs/policy/` + `/docs/sop/`
* Add a GitHub Actions gate that enforces the metadata block
* Create a companion `HALLUCINATION_LOG.md` template

No rush. The structure’s doing the work already.
