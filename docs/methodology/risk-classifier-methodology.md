# Risk Classifier — Methodology

**Module:** `src/classifier`
**Purpose:** Define the risk-tier taxonomy and decision logic the AI Governance Command Center uses to classify any AI use case, and show how each tier triggers specific obligations under GDPR, NIST AI RMF, and ISO/IEC 42001.

---

## 1. Why these four tiers

The classifier uses the **EU AI Act's risk taxonomy** (Unacceptable / High / Limited / Minimal) rather than a custom scale, for one deliberate reason: it's the most widely recognized regulatory risk taxonomy in the world right now, and reusing it means every output from this tool is immediately legible to a compliance professional, auditor, or hiring manager — no bespoke scale to explain.

| Tier | Definition |
|---|---|
| **Unacceptable** | AI practices banned outright — e.g. social scoring, manipulative/subliminal techniques causing harm, real-time biometric categorization in public spaces for law enforcement (with narrow exceptions), emotion inference in workplaces/schools |
| **High** | AI used in contexts with significant impact on health, safety, fundamental rights, or legal/economic standing — e.g. employment/HR decisions, credit scoring, healthcare diagnostics, law enforcement, education/exam scoring, critical infrastructure |
| **Limited** | AI with specific transparency obligations — e.g. chatbots, emotion-recognition systems, deepfakes/synthetic content — where the main obligation is disclosure that AI is involved |
| **Minimal** | Everything else — e.g. spam filters, inventory prediction, AI-enabled video games — no mandatory obligations beyond voluntary best practice |

---

## 2. Decision rules (what the classifier checks, in order)

The classifier evaluates a use-case description against these questions **in sequence** — the first "yes" determines the tier:

**Step 1 — Unacceptable check**
Does the use case involve any of:
- Social scoring of individuals by public authorities
- Manipulative techniques designed to materially distort behavior causing harm
- Real-time remote biometric identification in publicly accessible spaces for law enforcement (outside narrow legal exceptions)
- Inferring emotions in the workplace or educational settings
- Biometric categorization inferring race, political opinion, religion, sexual orientation

→ If yes: **Unacceptable.** Flag for immediate escalation; do not proceed to deployment planning.

**Step 2 — High-risk check**
Does the use case involve AI as a decision-making or decision-support component in:
- Employment (recruitment, promotion, termination, task allocation, performance monitoring)
- Access to essential private/public services (credit scoring, insurance eligibility, benefits)
- Healthcare (diagnosis, triage, treatment recommendation)
- Law enforcement, migration, or border control
- Education or vocational training (admissions, exam scoring)
- Critical infrastructure safety components
- Administration of justice or democratic processes

→ If yes: **High.** Full risk-treatment package required (see Section 3).

**Step 3 — Limited-risk check**
Does the use case involve:
- Direct interaction with individuals who should know they're talking to AI (chatbots, virtual assistants)
- Generation or manipulation of image/audio/video content (synthetic media, deepfakes)
- Emotion recognition or biometric categorization outside the banned/high-risk contexts above

→ If yes: **Limited.** Transparency/disclosure obligations apply.

**Step 4 — Default**
→ If none of the above: **Minimal.** Voluntary best-practice guidance only.

---

## 2a. Matching approach — and what testing changed about it

The rules in Section 2 above describe the *criteria*. This section documents *how* the implementation in `src/classifier/index.html` actually detects them in free-text input, because the first version was tested against edge cases and found to be too brittle — three real issues surfaced, and each one changed the design:

**Issue 1 — Fixed phrases missed paraphrased concepts.**
The first version looked for exact phrases like `"emotion recognition school"`. A real input like *"emotion recognition system deployed in classrooms"* didn't match, because it never contains that literal phrase — it fell through to a lower tier than the rules intended.
→ **Fix:** switched to **co-occurrence matching** — check for a concept word (e.g. `emotion`) appearing *anywhere* in the text alongside a context word (e.g. `classroom`, `school`, `workplace`), rather than requiring one fixed sentence.

**Issue 2 — Pluralization broke exact substring matches.**
A phrase-based rule for `"score citizens"` didn't match the input *"scores citizens"* — the extra "s" from pluralization broke a literal substring check.
→ **Fix:** switched to **word-stem matching** (e.g. `scor` catches score / scores / scoring / scored) combined with co-occurrence, so grammatical variation doesn't break detection.

**Issue 3 — Broadening a rule to fix Issue 2 introduced false positives.**
Once "employee" was added as a general employment-risk keyword, an unrelated internal support chatbot ("answers *employee* questions about IT and HR policies") was incorrectly flagged as a High-risk employment use case, even though it makes no employment decision.
→ **Fix:** split employment detection into (a) strong, unambiguous direct terms (`hiring`, `recruit`, `termination`) that trigger High risk on their own, and (b) softer terms (`employee`, `workforce`, `staff`) that only trigger High risk in **co-occurrence** with an actual decision-context word (`rank`, `score`, `evaluate`, `monitor`, `promote`, `screen`) — so mentioning employees isn't enough; the AI has to be making or supporting a decision about them.

**The general principle this established:** every keyword-based rule in this classifier is now checked against at least one deliberate false-positive case before being considered final — a rule is not "done" when it catches the case it was written for; it's done when it doesn't *also* catch cases it shouldn't. The test suite covering this (12 edge cases + 3 reference-case regressions, all passing) lives alongside the module and should be re-run any time a keyword list changes.

---

## 3. What each tier triggers (cross-framework obligations)

This is where the classifier's output becomes useful — each tier auto-populates the specific controls required, pulled directly from `/crosswalk/framework-crosswalk.md`:

| Tier | GDPR obligations triggered | NIST AI RMF functions activated | ISO/IEC 42001 controls activated |
|---|---|---|---|
| **Unacceptable** | Immediate processing halt review; Art. 5 lawfulness re-assessment | GOVERN 1 (policy escalation) | A.2 (AI Policy exception review) |
| **High** | Art. 35 DPIA mandatory; Art. 22 automated-decision safeguards; Art. 13/14 transparency | GOVERN 1–6 (full); MAP 1–5; MEASURE 1–4; MANAGE 1–4 (full lifecycle) | A.3, A.5 (AISIA), A.6, A.7, A.8, A.10 (full lifecycle controls) |
| **Limited** | Art. 13/14 transparency notice | GOVERN 4; MAP 5.1 | A.8 (Information for Interested Parties) |
| **Minimal** | Standard Art. 5/6 lawful-basis check only | GOVERN 1 (baseline policy) | A.2 (baseline policy alignment) |

---

## 4. Output format

For any use case submitted, the classifier returns:

```
Risk Tier: [Unacceptable / High / Limited / Minimal]
Reasoning: [1–3 sentences citing the specific trigger from Section 2]
Required Actions:
  - GDPR: [specific article(s)]
  - NIST AI RMF: [specific function/subcategory]
  - ISO/IEC 42001: [specific clause/Annex A control]
```

This citation-trail format is the core design decision of this module: **no output without a traceable regulatory reference.** A risk tier with no citation is not defensible in an actual governance context, and this project is built to the standard of one that would be.

---

## 5. Known limitations (stated deliberately)

- This is a rules-based first pass, not a substitute for legal review — genuinely ambiguous use cases should always be escalated to human review, and the tool should say so explicitly rather than force a confident answer.
- Co-occurrence and stem matching (Section 2a) made detection more robust than the original fixed-phrase approach, but it is still keyword-based, not semantic — a use case that describes a genuinely high-risk scenario using none of the anticipated vocabulary (e.g. an unfamiliar euphemism, or a non-English input) will under-classify to Minimal rather than raise a flag. A production version would pair this rules engine with a human review step for low-confidence or borderline cases, rather than relying on keywords alone.
- The EU AI Act's Annex III high-risk list is illustrative here, not reproduced in full — a production system would need the complete, current annex text kept in sync with amendments.
- This taxonomy is EU-centric by design (for legibility); a fuller build would layer in jurisdiction-specific variants (e.g. UAE National AI Strategy 2031, US state-level AI laws) as a secondary tag rather than replacing the primary tier.

---

*Next: this logic gets implemented in `src/classifier` as the actual application code, with the crosswalk lookup wired in programmatically.*
