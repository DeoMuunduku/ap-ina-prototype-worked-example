# ap-ina-bugada-prototype-worked-example
Reproducible worked example for AP-InA: code, config, seeds, and generated audit artefacts (traces, provenance, metrics)
# AP-InA Worked Example (BugAda / Bugzilla-Jira incidents)

This repository provides a small, reproducible prototype that instantiates the **AP-InA** minimal criteria
(**R–A–U–H–T–P–O–I/N**) on an incident dashboard setting, using BugAda-style Bugzilla/Jira tickets as incident cards.

The goal is to show **how AP-InA can be integrated into an information system** and what **audit artefacts** are produced
(allowlists, update records, traces, provenance logs, and outcome metrics).

---

## What this prototype does (high-level)

We treat each bug report as an **incident card** described only by **early / available-at-creation** fields (anti-leakage).
From these cards, we:
1. Build **clean incident cards** + **anti-leakage allowlist** + QC reports.
   
<img width="2004" height="1006" alt="image" src="https://github.com/user-attachments/assets/d6dbaac4-6807-4eac-ad88-ec21a062a8cf" />

3. Generate **silver labels** (deterministic rules) to operationalize the candidate meanings **H**.
4. Create **DEV / HOLDOUT-H** splits.
5. Calibrate a **gating threshold (τ)** to target a given abstention rate (e.g., 80%), then run the gate to produce:
   <img width="567" height="455" alt="fig_abstention_vs_tau" src="https://github.com/user-attachments/assets/71ddd30e-9448-41ba-93ba-e510af7c3dcf" />

   - `eligibility_audit.csv`
   - per-incident **trace logs** (`traces/`)
     <img width="2250" height="1048" alt="image" src="https://github.com/user-attachments/assets/19fd624c-465c-4e9b-a96b-ebd5c319919c" />

   - per-incident **provenance logs** (`prov/`)
  
7. Evaluate gate behaviour against **GOLD labels** (coverage and abstention metrics).

---

## Repository structure (suggested)

```text
scripts/
  01_prepare_cards_antileakage.py
  02_generate_silver_labels.py
  03_make_splits_dev_holdout.py
  04_calibrate_tau_and_run_gate.py
  05_evaluate_gate_vs_gold.py
assets/                 # optional: example figures for the paper
docs/                   # optional: datacards, notes
