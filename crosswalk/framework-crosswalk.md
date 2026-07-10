# Cross-Mapped Framework: GDPR × NIST AI RMF × ISO/IEC 42001

**Purpose:** Core reference artifact for the AI Governance Command Center project. Maps overlapping obligations across the EU's GDPR, NIST's AI Risk Management Framework, and ISO/IEC 42001, so a single control or piece of evidence can satisfy multiple frameworks at once — this is the "single control, triple compliance" logic the tool's Risk Classifier module runs on.

---

## How to read this

- **GDPR** = legal obligation (EU regulation, binding where personal data is processed)
- **NIST AI RMF** = voluntary risk-management process, organized into 4 functions: **Govern, Map, Measure, Manage** (each with categories/subcategories, e.g. GOVERN 1–6)
- **ISO/IEC 42001** = certifiable management-system standard; main clauses **4–10** (context, leadership, planning, support, operation, evaluation, improvement) plus **Annex A** (38 controls across 9 objective groups, A.2–A.10)

Each row below is a governance *theme* — the same underlying control activity, expressed in three different vocabularies.

---

## 1. Governance Structure & Accountability

| Framework | Reference | Requirement |
|---|---|---|
| GDPR | Art. 5(2), Art. 24 | Accountability principle — must demonstrate compliance, not just achieve it |
| NIST AI RMF | GOVERN 1, GOVERN 2 | Org-wide AI risk policies; accountability structures, defined roles for who approves high-risk use cases |
| ISO 42001 | Clause 5 (Leadership), A.2 (AI Policy), A.3 (Internal Organization) | Top management commitment; documented AI policy; defined roles/responsibilities across the AI lifecycle |

**Single control that satisfies all three:** an AI Governance Charter naming a risk owner per AI system, an approval workflow for high-risk use cases, and a policy document referenced in onboarding.

---

## 2. Risk Assessment & Impact Analysis

| Framework | Reference | Requirement |
|---|---|---|
| GDPR | Art. 35 (DPIA), Art. 36 (prior consultation) | Data Protection Impact Assessment mandatory where processing is "likely to result in high risk" |
| NIST AI RMF | MAP 1–5 | Context-setting, system categorization, benefit/cost mapping, characterizing impacts on individuals/groups/society |
| ISO 42001 | A.5 (Impact Assessment — AI System Impact Assessment, AISIA), Clause 6.1 (actions to address risks/opportunities) | Formal AI system impact assessment before deployment; documented risk treatment plan |

**Single control:** a combined AI System Impact Assessment template that produces both the GDPR-required DPIA output and the ISO/NIST risk register entry from one intake form.

---

## 3. Data Governance & Lawful Basis

| Framework | Reference | Requirement |
|---|---|---|
| GDPR | Art. 5(1), Art. 6, Art. 9 | Lawfulness, purpose limitation, data minimization; special category data restrictions |
| NIST AI RMF | MAP 4, MEASURE 2.x | Mapping data/third-party components; measuring data quality, representativeness, bias sources |
| ISO 42001 | A.7 (Data for AI Systems) | Data quality, provenance, and governance controls across the AI lifecycle |

**Single control:** a data lineage and lawful-basis register per training/inference dataset, reused as evidence for both the DPIA and the ISO data-governance control.

---

## 4. Bias, Fairness & Explainability

| Framework | Reference | Requirement |
|---|---|---|
| GDPR | Art. 22, Art. 13(2)(f)/14(2)(g) | Right not to be subject to solely automated decisions with legal/significant effect; right to "meaningful information about the logic involved" |
| NIST AI RMF | MEASURE 2.11, MEASURE 3 | Bias testing and benchmarking; explainability and interpretability measurement |
| ISO 42001 | A.6 (AI System Life Cycle — includes design/verification controls), A.8 (Information for Interested Parties) | Bias evaluation checkpoints built into system lifecycle; documented, communicable model behavior |

**Single control:** a bias-testing and explainability checklist completed per model release, whose output doubles as the Art. 22 "meaningful information" disclosure and the ISO lifecycle evidence.

---

## 5. Third-Party & Vendor AI Risk

| Framework | Reference | Requirement |
|---|---|---|
| GDPR | Art. 28 (processors), Art. 44–49 (transfers) | Data Processing Agreements with any AI vendor acting as processor; safeguards for cross-border data transfer |
| NIST AI RMF | GOVERN 6, MAP 4 | Third-party AI risk governance; mapping risks from vendor/supply-chain components |
| ISO 42001 | A.10 (Third-Party and Supplier Relationships) | Vendor due diligence, contractual AI-specific risk clauses, supplier monitoring |

**Single control:** your existing EOI/RFP vendor due-diligence questionnaire, extended with GDPR processor-clause checks and NIST/ISO supplier-risk scoring — this is a direct extension of your K-DISC vendor governance work.

---

## 6. Transparency & Individual Rights

| Framework | Reference | Requirement |
|---|---|---|
| GDPR | Art. 12–14 (transparency), Art. 15–20 (access, rectification, erasure, portability) | Clear notice of AI processing; mechanisms for individuals to exercise data rights |
| NIST AI RMF | GOVERN 4, MAP 5.1 | Transparency and documentation practices; understanding impacts on individuals |
| ISO 42001 | A.8 (Information for Interested Parties) | Documented communication to affected stakeholders about AI system use and limitations |

**Single control:** a public-facing AI use disclosure + an internal rights-request handling workflow that logs into the same audit trail used for ISO evidence.

---

## 7. Security, Monitoring & Incident Response

| Framework | Reference | Requirement |
|---|---|---|
| GDPR | Art. 32 (security), Art. 33–34 (breach notification — 72 hrs) | Technical/organizational security measures; mandatory breach notification |
| NIST AI RMF | MANAGE 2, MANAGE 4 | Risk treatment and incident response; monitoring for emergent risk |
| ISO 42001 | A.6 (lifecycle monitoring), Clause 10.1/10.2 (nonconformity, corrective action) | AI incident logging tied to clause references; continual improvement loop |

**Single control:** an AI Incident Log (already scoped in your project) that captures severity, root cause, and remediation — satisfying breach-notification timing evidence, NIST MANAGE outcomes, and ISO nonconformity records simultaneously.

---

## 8. Continuous Monitoring & Audit

| Framework | Reference | Requirement |
|---|---|---|
| GDPR | Art. 5(2), Art. 24(1) | Ongoing demonstration of compliance |
| NIST AI RMF | GOVERN 1.5, MEASURE 4 | Regular policy review; ongoing monitoring of AI system performance against defined metrics |
| ISO 42001 | Clause 9 (Performance Evaluation), Clause 9.2 (Internal Audit) | Internal audit program; management review cycles |

**Single control:** a recurring audit calendar (quarterly, aligned to your Power BI dashboard cadence) that produces one evidence pack reused across all three frameworks' review cycles.

---

## Why this crosswalk matters for your project

Most organizations run GDPR, NIST AI RMF, and ISO 42001 as **three separate compliance tracks** with three sets of evidence, three teams, and three audit calendars — expensive and duplicative. Your **AI Governance Command Center**'s Risk Classifier module should be built to output *all three* framework references for a single use case in one pass. That "one control, three frameworks" logic is the differentiator to lead with in interviews — it's exactly the kind of efficiency argument a CISO or Chief AI Governance Officer wants to hear from a candidate.

### Suggested build order for the tool
1. Encode this table as a structured lookup (JSON/dict: theme → GDPR article, NIST subcategory, ISO clause/control)
2. When a user logs an AI use case, the LLM classifies its risk tier **and** pulls the matching row(s) from this crosswalk automatically
3. Output becomes a single "Compliance Coverage Report" per AI system — this is your demo's most impressive screen

---

*Note: This crosswalk is a practitioner reference built for portfolio/demo purposes, not legal advice. Always validate against the current published texts of GDPR, NIST AI RMF 1.0 (and its Generative AI Profile, NIST AI 600-1), and ISO/IEC 42001:2023 before use in a real compliance program.*
