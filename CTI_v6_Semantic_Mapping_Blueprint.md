# CTI v6 — Semantic SAP → TFL → ADaM Mapping Blueprint

**Purpose**  
This document defines a step-by-step, end-to-end blueprint for CTI v6 focused on **semantic interpretation**, not text extraction. The objective is to correctly map **SAP intent** to **TFL shells** and **ADaM specifications** in order to deterministically produce:

- The *true* list of analyses/outputs to be built
- Required ADaM domains
- Required ADaM variables and flags
- Required derivations and filters
- Clear implementation gaps before programming begins

---

## STEP 1 — Use the TFL Shell as the System Anchor

**Action**  
- Ingest the TFL shell (tables/figures/listings) as the primary source of truth.
- Parse each shell structurally (type, title, section hierarchy).
- Ignore output numbering; retain semantic titles.

**Rationale (Biostat)**  
The TFL shell defines what *must* be produced. SAP text explains intent but is not authoritative for output existence.

**Output**  
Raw list of shell-defined outputs.

---

## STEP 2 — Canonicalize Shell Outputs into Semantic Analysis Outputs

**Action**  
- Normalize shell titles into semantic units.
- Collapse cosmetic variants (mean vs median, SD vs CI, panels).

**Example**  
"Mean (SD) Plasma Concentration vs Time"  
"Median (Q1, Q3) Plasma Concentration vs Time"  
→ Single semantic output

**Rationale (Biostat)**  
Biostatisticians reason in analyses, not display variants.

**Output**  
A deduplicated set of **Analysis Outputs**.

---

## STEP 3 — Create an Analysis Signature for Each Output (Core Object)

Each Analysis Output is reduced to a canonical **Analysis Signature**:

- Outcome concept
- Target population
- Time basis
- Summary measures
- Stratification variables
- Analysis class (PK, Safety, ECG, etc.)

**Why this matters**  
This is the smallest unit that maps unambiguously to ADaM.

**Output**  
A biostatistically meaningful list of unique analysis signatures.

---

## STEP 4 — Use SAP Text to Annotate (Not Create) Analysis Signatures

**Action**  
SAP is used to enrich analysis signatures with:
- Population definitions
- Derivation logic
- Modeling language
- Censoring or exclusion rules

**Explicit Non-Goals**  
- Do NOT create outputs from SAP prose
- Do NOT treat SAP narrative as authoritative for analysis existence

**Output**  
Analysis signatures with annotated intent and assumptions.

---

## STEP 5 — Resolve Each Analysis Signature to ADaM Domains

**Action**  
- Infer the primary ADaM domain for each analysis.
- Add supporting domains as needed.
- Validate against the ADaM specification.

**Example**  
Outcome: Plasma concentration → ADPC (primary), ADSL (supporting)

**Output**  
Deterministic domain requirements for each analysis.

---

## STEP 6 — Resolve Required ADaM Variables

For each analysis, identify:
- Outcome variables (AVAL, AVALC, PARAM, PARAMCD)
- Time variables (ADT, ADY, AVISIT, AVISITN)
- Population/analysis flags (PKFL, SAFFL, ANLxxFL)
- Stratification variables

**Rule**  
Variables must be confirmed from the ADaM specification.

**Output**  
Exact variable dependency list per analysis.

---

## STEP 7 — Infer Filters, Flags, and Derivations

**Action**  
- Determine population filters
- Identify required analysis flags
- Derive baseline, change-from-baseline, normalization rules

**Inputs**  
SAP text + shell annotations + ADaM derivation definitions

**Output**  
Executable filter and derivation logic.

---

## STEP 8 — Validate Against ADaM Spec (Gap Detection)

For each analysis:
- Confirm required datasets exist
- Confirm required variables exist
- Flag default or inferred flags
- Identify missing derivations or ambiguous rules

**Output**  
Programmer-ready gap list.

---

## STEP 9 — Generate Output-to-ADaM Mapping Artifacts

Generate structured artifacts:
- Analysis → domain mapping
- Analysis → variable requirement table
- Variable → derivation dependencies
- Shell output → analysis signature link

Exportable formats:
- Excel
- JSON
- SAS metadata

---

## STEP 10 — Assign Confidence and Verification Status

Confidence dimensions:
- Extraction confidence
- Spec confirmation status
- Statistical completeness

Usage labels:
- Programmer-ready
- Needs biostat confirmation
- Not implementable as specified

---

## STEP 11 — Final Product: Analysis Execution Plan

The final v6 deliverable is:

> A deterministic, semantically validated **analysis execution plan** answering:
> "What analyses will be built, using which ADaM data, with what logic?"

This mirrors real biostatistical and programming workflows.

---

## One-Line Summary

**CTI v6 semantically interprets analysis intent by anchoring on TFL shells and mapping each analysis deterministically to ADaM domains, variables, flags, and derivations — SAP text is annotation, not the output.**
