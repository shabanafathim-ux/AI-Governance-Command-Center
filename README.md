# AI Governance Command Center

**A reference implementation of an AI risk and compliance management system, built to the operational standard required by EU GDPR, the NIST AI Risk Management Framework, and ISO/IEC 42001.**

---

## One-line pitch

A working AI governance system that classifies AI use-case risk, scores vendor exposure, and tracks audit evidence against GDPR, NIST AI RMF, and ISO/IEC 42001 simultaneously — collapsing three separate compliance tracks into one.

---

## Why this exists

Most organizations deploying AI run GDPR, NIST AI RMF, and ISO/IEC 42001 as three disconnected compliance efforts — three teams, three evidence sets, three audit calendars. This project is a proof-of-concept for a single governance instrument that produces one piece of evidence and maps it automatically to all three frameworks' requirements.

It is built on real program-governance experience designing and operationalizing an ISO/IEC 42001-aligned AI Management System for two live clinical AI diagnostic platforms (Diabetic Retinopathy and Cervical Cancer Screening) at a government innovation council — reference cases in this repository are anonymized, generalized versions of that class of use case, not fictional placeholders.

## Status

🚧 In active development — built over a 5-day sprint. See `/docs/methodology` for the design rationale behind each module as it's built.

## Modules

| Module | Purpose | Status |
|---|---|---|
| `src/classifier` | AI use-case risk classifier — outputs risk tier + citation trail (GDPR article / NIST subcategory / ISO clause) | In progress |
| `src/vendor-risk` | Third-party AI vendor due-diligence scoring | Planned |
| `src/bias-tracker` | Bias & explainability evidence tracker per model | Planned |
| `src/incident-log` | AI incident logging mapped to ISO 42001 clause references | Planned |
| `src/dashboard` | Executive risk-heatmap and portfolio view | Planned |

## Governance foundation

The classification and scoring logic in this system is not arbitrary — it is built on a documented crosswalk mapping GDPR articles, NIST AI RMF functions/subcategories, and ISO/IEC 42001 clauses/Annex A controls to eight governance themes (accountability, risk assessment, data governance, bias/explainability, vendor risk, transparency, incident response, audit). See `/crosswalk/framework-crosswalk.md`.

## Reference cases

Sample AI use cases used to demonstrate the classifier are anonymized composites drawn from regulated-industry deployments (clinical diagnostics, financial services, public sector), not synthetic filler data. See `/data/reference-cases`.

## Author

Shabana Fathim — Senior Program Manager, AI Governance & Risk
ISO/IEC 42001 Lead Implementer | GARP Risk and AI (RAI) | PRINCE2 Agile Practitioner
[linkedin.com/in/shabana-fathim](https://linkedin.com/in/shabana-fathim)

## Disclaimer

This is a portfolio/demonstration project built to show applied AI governance capability. It is not a certified compliance tool and should not be used as a substitute for legal or regulatory advice.
