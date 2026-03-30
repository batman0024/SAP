# Scaling CTI v6 Across Studies

## Core Principle

**Logic must be global. Knowledge must be configurable. Evidence must be study-specific.**

Scaling CTI v6 across studies is not a compute problem; it is a **semantic governance and architecture problem**. If designed correctly, the same Python logic supports 1 study or 1,000 studies without modification.

---

## 1. Separate Global Logic from Study Context

### What Must Remain Global (Invariant)
These elements are *never customized per study*:

- AnalysisSignature definition
- ADaM domain resolution logic (CDISC-aligned)
- Variable role taxonomy (outcome, time, flag, stratifier)
- Validation rules (what makes an analysis implementable)
- Gap severity classification
- Confidence computation framework

These live in **core Python modules** and apply uniformly to all studies.

---

### What Is Study-Specific (Inputs Only)
Each study contributes data—not logic:

- TFL shell
- SAP document
- ADaM specification
- Study metadata (protocol ID, phase, design)
- Optional sponsor configuration profile

Python consumes these as **inputs** without altering core behavior.

---

## 2. Introduce a StudyContext Object (Critical)

All processing occurs within a structured `StudyContext`, which contains:

- Study / protocol identifier
- Phase (drives expectation rules)
- Therapeutic area (optional)
- Parsed TFL shell metadata
- Parsed SAP text with references
- Parsed ADaM specification
- Sponsor configuration (optional)

### Why This Matters
- No logic needs to “know” which study it is processing
- Reprocessing becomes deterministic
- Cross-study comparison becomes possible

Python always operates on a StudyContext, never loose inputs.

---

## 3. Treat AnalysisSignature as the Cross-Study Key

Every semantic analysis is represented by a **canonical AnalysisSignature** that is:

- Hashable
- Deterministic
- Independent of study numbering

Example signature:

- Outcome: Plasma concentration
- Population: PK
- Time: Nominal sampling time
- Summary: Mean / SD

This identical signature may appear across multiple studies and phases.

### Result
Python can:
- Compare analyses across studies
- Detect reuse
- Detect divergence in intent or specification

---

## 4. Maintain a Global, Read-Only Knowledge Layer

Scaling requires shared understanding without shared assumptions.

### Global Knowledge Includes

- Synonym maps ("Plasma conc." → "Plasma concentration")
- Outcome concept ontology
- ADaM role mappings
- Sponsor-agnostic CDISC interpretations

### Key Rules
- Stored as versioned JSON / YAML
- Loaded at runtime
- Never modified by individual studies

Python reads from this layer; it does not learn per study.

---

## 5. Support Sponsor Profiles Without Forking Logic

Sponsor differences are handled via **configuration overlays**, not code branches.

Examples:
- Alternative flag naming conventions
- Sponsor-specific domain usage nuances

### Design Rule

- No `if sponsor == X` logic
- Core logic remains unchanged
- Configuration drives precedence and naming resolution

---

## 6. Validation Is Study-Specific but Comparable

Each study produces its own:

- Analysis gap list
- Confidence profile
- Execution plan

But all use the **same validation logic**, enabling comparisons such as:

- Which studies have the most blocking gaps
- Which gaps recur across programs

This is real scalability.

---

## 7. Cache and Reuse Analysis Semantics

To scale efficiently:

- Canonical analysis signatures are cached
- When new studies are processed:
  - Known patterns resolve immediately
  - Novel patterns are highlighted

Clinical programs repeat analyses; Python leverages this structurally, not statistically.

---

## 8. Version Everything (Non-Negotiable)

To scale safely, you must version:

- Global rules
- Knowledge maps
- Sponsor configurations
- Study execution plans

### Why
- Old studies must remain reproducible
- Interpretation must be traceable to rule versions

Python enforces this by associating version metadata with every output artifact.

---

## 9. Horizontal Scaling by Study Isolation

Python processing is **study-isolated**:

- Each StudyContext is independent
- No shared mutable state
- Easy parallelization (batch or CI-style runs)

This enables processing dozens or hundreds of studies without cross-contamination.

---

## 10. Cross-Study Analytics Is a Secondary Layer

Once studies are processed, Python can optionally:

- Compare analysis coverage across programs
- Identify common ADaM gaps
- Detect SAP-to-shell drift over time

This layer is **observational only** and never feeds back into study-level logic.

---

## Mental Model

Think of CTI v6 as a compiler:

- TFL shell → source language
- SAP → annotations / comments
- ADaM spec → type system
- AnalysisSignature → abstract syntax tree
- Validation → compile-time checks
- Execution plan → compiled output

Each study is an independent compilation unit.

---

## Final Takeaway

CTI v6 scales across studies because it does **not learn per study**.

It applies the same semantic compiler to different inputs, producing independent, comparable, and reproducible execution plans—exactly how safe clinical systems must scale.
