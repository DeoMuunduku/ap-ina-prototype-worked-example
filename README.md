# AP-InA Worked Example – BugAda (Bugzilla / Jira Incidents)

This repository contains the companion material for the worked example of **AP-InA** presented in:

> **Towards Traceable Meaning in Information Systems**  
> D. Munduku, E. Negre – accepted at **ICIM 2026**, University of Oxford (March 27–29, 2026).  
> To appear in Springer CCIS.

We instantiate the AP-InA minimal audit contract (**R–A–U–H–T–P–O–I/N**) on a BugAda-style dataset where each
Bugzilla/Jira issue is treated as an **incident card** on a “red-light” dashboard.

---

## 1. Repository contents (top level)

- `.gitignore` – standard Git ignore rules.
- `LICENSE` – license for this repository.
- `README.md` – this file.
- `ap_ina_bugada_prototype.ipynb` – original notebook used to prototype the full pipeline.
## 6. Literature Review – PRISMA Flow

As announced in the paper, we provide here the PRISMA diagram of the systematic
literature review (1990–2025).



![Uploading prisma 2.drawio.png…]()

Additional materials (search queries, screening forms, codebook, Cohen’s κ scripts,
and raw exports) will be added progressively in a dedicated subfolder of the repository.

<img width="1567" height="841" alt="prisma" src="https://github.com/user-attachments/assets/899c3d80-caff-4416-b18d-e4c52fdc0740" />
### 1.1. Scripts (AP-InA pipeline)

These scripts implement the steps described in the paper:

- `step1_prepare_cards_clean_py.py`  
  Prepare cleaned incident cards from the raw BugAda input, perform basic QC, and build the
  feature allowlist (anti-leakage).


- `step2_generate_silver_labels_py.ipynb`  
  Generate **silver labels** (rule-based) used to operationalise the candidate meanings **H**
  described in the worked example.

- `step3_make_splits_dev_holdouth_py.py`  
  Deterministic split into **DEV** and **HOLDOUT-H** folds.

- `step4_calibrate_tau_and_run_protocol_py.py`  
  Sweep over thresholds τ, choose a target abstention rate, run the AP-InA protocol, and
  generate traces, provenance logs and audit files.

- `step5_eval_gate_vs_gold.py`  
  Evaluate the gate against GOLD labels (coverage and abstention metrics) and export summary
  reports.

---

## 2. ZIP archives and audit artefacts

### 2.1. `bugada_cards_clean-*.zip`

Contains the **prepared incident cards** and data documentation:

- cleaned BugAda incident cards;
- basic statistics and QC reports;
- feature allowlist (fields kept for the early, anti-leakage setting).

This corresponds to the “clean incident cards + allowlist” part of the paper.

---

### 2.2. `final_bugada_H_tau045-*.zip`

This archive contains the **full AP-InA run** for the selected operating point (e.g. τ = 0.45).
It is the main artefact that instantiates the minimal contract **R–A–U–H–T–P–O–I/N**.

Inside this ZIP you will find (naming may vary slightly, but the structure is):

- `eligibility_audit.csv`  
  Aggregated table used in the paper to report eligibility, abstention rates, and gate behaviour.

- `traces/`  
  One JSON file per incident (e.g. `BUGS-10954.json`), each containing:
  - episode identifier and timestamp,
  - input features (early fields only),
  - context (team, policy version, parameters),
  - candidate scores and decision,
  - everything needed to **replay the decision from logs alone**.

- `prov/`  
  Provenance logs recording:
  - resource updates (from–to version),
  - responsible unit / approver,
  - timestamps and policy versions.

- `resources/` (or equivalent)  
  Versioned resources used by the interpretive schema:
  - feature allowlist,
  - keyword table,
  - gate policy (e.g. `gate_policy_v1.0.json` / `v1.1.json`).

- additional summary files (e.g. `metrics.json`, `gate_outputs.csv`, figures) used in the
  **Discussion** section of the paper.

Together, these artefacts show how AP-InA can be integrated into an information system and how
the audit trail is materialised.

---

### 2.3. `final_bugada_report-*.zip`

Contains the **reporting artefacts** used for analysis and figures in the paper:

- coverage and abstention metrics;
- stability / drift indicators;
- figures (histograms, sweep curves, dashboards).

These are derived from the traces and audit tables and used to support the discussion.

---

## 3. How to run the pipeline

A minimal way to replay the main steps is:

```bash
# Python 3.10+ recommended
# Install dependencies according to the imports in the notebook / scripts
# (pandas, numpy, scikit-learn, matplotlib, etc.)

python step1_prepare_cards_clean_py.py
# optionally inspect intermediate outputs or the notebook

# silver labels (run inside the notebook)
# open: step2_generate_silver_labels_py.ipynb

python step3_make_splits_dev_holdouth_py.py
python step4_calibrate_tau_and_run_protocol_py.py
python step5_eval_gate_vs_gold.py


4. Example trace and provenance (for reviewers)

<img width="2860" height="1650" alt="image" src="https://github.com/user-attachments/assets/f86ee560-d21a-4e7e-b9d0-cdd30f6793be" />
{
  "episode_id": "BUGS-10954",
  "ts": "1999-07-30T22:55:51Z",
  "input": {
    "title": "[BUGS] Settings UI RESOLVED",
    "component": "Settings UI",
    "severity": "medium",
    "desc_len": 46,
    "has_keyword_crash": false,
    "security_flag": false
  },
  "context": {
    "team": "ops-L1",
    "policy": "policy@tau=0.45",
    "policy_decl": "T=0.2; tau=0.45; delta=0.15"
  },
  "branch_scores": {
    "m": { "release_regression": 0.0, "infra_instability": 0.0, "...": 0.0 },
    "c": { "release_regression": 0.0, "infra_instability": 0.0, "...": 0.0 }
  }
}

<img width="2860" height="1650" alt="image" src="https://github.com/user-attachments/assets/f86ee560-d21a-4e7e-b9d0-cdd30f6793be" />

A typical provenance record from prov/:
{
  "resource": "gate_policy",
  "from_version": "v1.0",
  "to_version": "v1.1",
  "changed_by": "ops-L1",
  "timestamp": "2025-03-20T14:33:11Z",
  "approval": {
    "role": "supervisor",
    "approved": true
  }
}
---
A typical provenance record from prov/:

{
  "resource": "gate_policy",
  "from_version": "v1.0",
  "to_version": "v1.1",
  "changed_by": "ops-L1",
  "timestamp": "2025-03-20T14:33:11Z",
  "approval": {
    "role": "supervisor",
    "approved": true
  }
}
5. Contact

For questions about the code or artefacts, please contact:

Déo Munduku – deo.munduku@lamsade.dauphine.fr
