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

## 2a. Matching approach, round one — co-occurrence and stemming

The rules above describe the *criteria*. This section and the next document *how* the implementation in `src/classifier/index.html` actually detects them in free-text input — because testing repeatedly found the first, simpler version too brittle. Each fix changed the design in a specific way.

**Issue 1 — Fixed phrases missed paraphrased concepts.**
The first version looked for exact phrases like `"emotion recognition school"`. A real input like *"emotion recognition system deployed in classrooms"* didn't match, because it never contains that literal phrase.
→ **Fix:** **co-occurrence matching** — a concept word (e.g. `emotion`) appearing anywhere alongside a context word (e.g. `classroom`, `school`, `workplace`), rather than one fixed sentence.

**Issue 2 — Pluralization broke exact substring matches.**
`"score citizens"` didn't match `"scores citizens"` — the extra "s" broke a literal substring check.
→ **Fix:** **word-stem matching** (e.g. `scor` catches score / scores / scoring / scored) combined with co-occurrence.

**Issue 3 — Broadening a rule to fix Issue 2 introduced a false positive.**
Adding "employee" as a general employment keyword caused an unrelated support chatbot ("answers *employee* questions about IT and HR policies") to be flagged High, even though it makes no employment decision.
→ **Fix:** split employment detection into strong, unambiguous direct terms (`hiring`, `recruit`, `termination`) versus softer terms (`employee`, `workforce`, `staff`) that only trigger in **co-occurrence** with an actual decision-context word (`rank`, `score`, `evaluate`, `monitor`, `promote`, `screen`).

---

## 2b. Matching approach, round two — negation blindness

A later, more demanding real-world test exposed a deeper problem that co-occurrence and stemming alone don't solve: **keyword matching cannot see negation.** A sentence can contain exactly the right trigger words while *explicitly denying* that the AI does the thing those words describe — and a substring-matching classifier has no way to tell the difference. This surfaced as three separate real bugs, found through one realistic test description (an internal HR policy assistant chatbot), not synthetic edge cases:

**Bug 1 — "Decisions remain with humans" read as AI decision-making.**
*"All final employment and HR decisions **remain with** authorized HR personnel."* contains both "employment" and "decision" — enough to fire the employment co-occurrence rule — even though the sentence is stating the AI has **no** decision role at all.
→ **Fix:** `hasHumanDecisionDisclaimer()` — checks for a disclaiming phrase (`remain with`, `rests with`, `is made by`, `approved by`, etc.) co-occurring with a human-role word (`personnel`, `manager`, `staff`, `human`). If found, the trigger is suppressed.

**Bug 2 — A negated verb list still matched a bare keyword.**
*"The system **does not** evaluate employee performance, monitor behaviour, rank employees, recommend **promotions**... or make employment decisions."* — this sentence is a list of things the AI explicitly does **not** do. But the word "promotion" is a substring of "promotions," and the *strong keyword* rule (`hiring`, `recruit`, `promotion`, `termination`...) had **no guard at all** — it fired immediately, before the negation was ever considered.
→ **Fix:** `hasDoesNotDisclaimer()` — checks for a "does not / doesn't / is not authorized to" construction anywhere in the text.

**Bug 3 — A third, different unguarded rule caught a third false positive.**
The same test description also contains *"...the system may retrieve relevant information... about their own leave balance or **benefits eligibility**."* — just looking a number up, not deciding it. But this matched the *essential-service* rule's bare keyword `'benefits eligib'`, a rule that had never been touched by the first two fixes.
→ **Root cause identified:** patching one rule at a time was whack-a-mole — negation blindness is a property of *every* keyword-based rule, not a one-off. **Fix:** a single combined guard (`hasNegationGuard()`, checking both disclaimer patterns) is now applied to **every rule in the High-risk tier**, not just the one it happened to be discovered on.

**The general principle this established (revised):** a keyword rule is not "done" when it correctly catches the case it was written for and survives a handful of hand-picked false-positive checks. It is only trustworthy once a *structural* class of failure — here, negation — has been checked against **every** rule that could plausibly exhibit it, not just the rule where it was first noticed. The test suite (15+ cases, including the full real-world HR assistant description that triggered this round of fixes, plus regression checks confirming genuine High-risk cases still fire correctly) lives alongside the module and should be re-run whenever any keyword list changes.

---

## 3. What each tier triggers (cross-framework obligations)

Each tier auto-populates the specific controls required, pulled directly from `/crosswalk/framework-crosswalk.md`:

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

This citation-trail format is the core design decision of this module: **no output without a traceable regulatory reference.**

---

## 5. Known limitations (stated deliberately)

- This is a rules-based first pass, not a substitute for legal review — genuinely ambiguous use cases should always be escalated to human review, and the tool should say so explicitly rather than force a confident answer.
- Co-occurrence, stemming, and the negation guards (Sections 2a, 2b) made detection substantially more robust than the original fixed-phrase approach, but it remains keyword-based, not semantic. More complex negation constructions than "does not X" or "remains with Y" (double negatives, conditional or hypothetical framing, sarcasm) are not guaranteed to be caught. A production version would pair this rules engine with a human review step for low-confidence or borderline cases rather than relying on keywords alone.
- The EU AI Act's Annex III high-risk list is illustrative here, not reproduced in full — a production system would need the complete, current annex text kept in sync with amendments.
- This taxonomy is EU-centric by design (for legibility); a fuller build would layer in jurisdiction-specific variants (e.g. UAE National AI Strategy 2031, US state-level AI laws) as a secondary tag rather than replacing the primary tier.

---

*This module has now been through three rounds of real-world testing and fixes (documented in 2a and 2b above), each one making the underlying logic more robust rather than just patching the one failing case in isolation.*
