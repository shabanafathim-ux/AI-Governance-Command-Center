# AI Governance Command Center

**A reference implementation of an AI risk and compliance management system, built to the operational standard required by EU GDPR, the NIST AI Risk Management Framework, and ISO/IEC 42001.**

---

## Live demo

All five modules are built, tested, and publicly viewable — no download required:

| Module | Live demo |
|---|---|
| Risk Classifier + Asset/Risk Registers | [Open →](https://shabanafathim-ux.github.io/AI-Governance-Command-Center/src/classifier/) |
| Vendor Risk Scoring | [Open →](https://shabanafathim-ux.github.io/AI-Governance-Command-Center/src/vendor-risk/) |
| Bias & Explainability Tracker | [Open →](https://shabanafathim-ux.github.io/AI-Governance-Command-Center/src/bias-tracker/) |
| Incident Log | [Open →](https://shabanafathim-ux.github.io/AI-Governance-Command-Center/src/incident-log/) |
| Executive Dashboard | [Open →](https://shabanafathim-ux.github.io/AI-Governance-Command-Center/src/dashboard/) |

---

## One-line pitch

A working AI governance system that classifies AI use-case risk, scores vendor exposure, tracks bias/explainability evidence, logs incidents with breach-notification timing, and reports portfolio status — against GDPR, NIST AI RMF, and ISO/IEC 42001 simultaneously, collapsing three separate compliance tracks into one.

---

## Why this exists

Most organizations deploying AI run GDPR, NIST AI RMF, and ISO/IEC 42001 as three disconnected compliance efforts — three teams, three evidence sets, three audit calendars. This project is a proof-of-concept for a single governance instrument that produces one piece of evidence and maps it automatically to all three frameworks' requirements.

It is built on real program-governance experience designing and operationalizing an ISO/IEC 42001-aligned AI Management System for two live clinical AI diagnostic platforms (Diabetic Retinopathy and Cervical Cancer Screening) at a government innovation council — reference cases in this repository are anonymized, generalized versions of that class of use case, not fictional placeholders.

## Status

✅ **All five core modules complete** — built over a 5-day sprint, each tested against edge cases (and, in the Risk Classifier's case, against a real ambiguous scenario that caught and led to fixing two genuine classification bugs) before being shipped. See `/docs/methodology` for the design rationale behind each module.

## Modules

| Module | Purpose | Status |
|---|---|---|
| `src/classifier` | AI use-case risk classifier — outputs risk tier + citation trail (GDPR article / NIST subcategory / ISO clause). Includes auto-populated AI Asset Register and Risk Register. | ✅ Complete |
| `src/vendor-risk` | Weighted third-party AI vendor due-diligence scoring across 6 governance categories, with red-flag detection and a Vendor Register. | ✅ Complete |
| `src/bias-tracker` | Bias & fairness / explainability evidence checklist per model, with a permanent timestamped Audit Trail log. | ✅ Complete |
| `src/incident-log` | AI incident severity triage (impact type × personal-data involvement) with a live GDPR Art. 33 breach-notification countdown, mapped to ISO 42001 clauses. | ✅ Complete |
| `src/dashboard` | Executive portfolio view — risk-tier distribution, vendor risk bands, open incidents, evidence-completion gauge, and an "Attention Needed" panel. Uses illustrative sample data (see note in-module). | ✅ Complete |

## Governance foundation

The classification and scoring logic in this system is not arbitrary — it is built on a documented crosswalk mapping GDPR articles, NIST AI RMF functions/subcategories, and ISO/IEC 42001 clauses/Annex A controls to eight governance themes (accountability, risk assessment, data governance, bias/explainability, vendor risk, transparency, incident response, audit). See `/crosswalk/framework-crosswalk.md`.

## Reference cases

Sample AI use cases used to demonstrate the classifier are anonymized composites drawn from regulated-industry deployments (clinical diagnostics, financial services, public sector), not synthetic filler data. See `/data/reference-cases`.

## Design notes worth reading

- **Rules-based, not LLM-based, by deliberate choice.** The Risk Classifier and Vendor Scoring engines use transparent, auditable keyword/co-occurrence logic rather than an opaque model call — in a governance context, being able to trace *why* a classification fired matters more than a marginally smarter black box. See `/docs/methodology`.
- **Tested against real scenarios, not just synthetic edge cases.** The classifier's methodology doc documents three real bugs found through testing (fixed-phrase brittleness, pluralization gaps, and an overlapping-vocabulary bug where "screening" meant something different in a healthcare vs. education context) and how each was fixed.
- **Evidence completion ≠ model quality.** The Bias & Explainability Tracker deliberately measures whether evidence exists and was reviewed, not whether a model is actually fair — an honest scope boundary stated directly in the tool's own output.

## Author

Shabana Fathim — Senior Program Manager, AI Governance & Risk
ISO/IEC 42001 Lead Implementer | GARP Risk and AI (RAI) | PRINCE2 Agile Practitioner
[linkedin.com/in/shabana-fathim](https://linkedin.com/in/shabana-fathim)

## Disclaimer

This is a portfolio/demonstration project built to show applied AI governance capability. It is not a certified compliance tool and should not be used as a substitute for legal or regulatory advice.
