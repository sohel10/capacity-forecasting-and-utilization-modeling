# Cost Driver Modeling & Capacity Impact Analysis (SAS / GLM)

## Overview
This project builds a statistical cost model using publicly available U.S. hospital data (CMS Medicare facility reporting).  
The goal is to identify which operational factors are most strongly associated with higher cost per inpatient stay — so leadership can understand where money is leaking and what levers can be adjusted.

This same approach applies outside healthcare:
- Which distribution centers are most expensive per shipment?
- Which plants are high cost per unit even after adjusting for complexity?
- Which regions require intervention vs are just handling harder work?

In other words: we’re not just predicting cost — we’re explaining *why* cost is high.

---

## Business Question
Why do some facilities have a higher adjusted cost per admission than others, even when they treat similar “complexity” of cases?

We model cost-per-discharge as a function of:
- Staffing/training intensity (teaching hospital vs non-teaching)
- Cost-to-charge ratio (operational efficiency proxy)
- Case mix index (how complex/expensive the patients are)
- Payment/penalty adjustments (value-based performance programs)
- Rural/low-volume adjustments (throughput / scale penalty)

This is directly analogous to:
- “Why is warehouse A more expensive per order than warehouse B?”
- “Why is call center site 3 costlier per call, after adjusting for call difficulty?”

---

## Dataset
- Source: U.S. Centers for Medicare & Medicaid Services (CMS) hospital payment/cost reporting
- File used: `ipsf_inp_2025_04_01.sas7bdat`
- Level: hospital / facility
- Target variable: `caseMixAdjustedCostPerDischarge_`  
  (a standardized cost-per-stay metric that accounts for patient complexity)

Note: The raw CMS file is public, but not redistributed here.

---

## Modeling Approach
All modeling is done in SAS using generalized linear models (GLM).

### 1. Feature Engineering
- **CaseMixIndex**  
  Measures clinical complexity. Higher means sicker / harder cases.
- **internsToBedsRatio**  
  Used to flag teaching hospitals (more trainees, more overhead).
- **operatingCostToChargeRatio**  
  Proxy for cost discipline and efficiency.
- **vbpAdjustment**  
  “Value-based purchasing” incentive/penalty applied to hospitals based on quality.
- **lowVolumeAdjustmentFactor**  
  Adjustment for small / rural facilities that operate below scale.

### 2. Modeling
We fit a GLM in SAS to explain `caseMixAdjustedCostPerDischarge_` using those inputs.

Why GLM?
- Cost is skewed (not normal), so simple linear regression can be misleading.
- GLM with log link / Gamma-like variance handles cost data more realistically.
- Coefficients are directly interpretable: “holding other factors constant, X is associated with Y% higher cost per case.”

### 3. Interpretability
The model output ranks cost drivers:
- Which factors are structural (can’t change easily)?
- Which are operational (you can improve them)?
- Which penalties/adjustments are hurting margin?

This is the part executives actually care about.

---

## Deliverables in this repo
- `hospital_model_with_ods.sas`  
  - Loads CMS facility data  
  - Cleans and engineers variables  
  - Runs GLM in SAS  
  - Exports model output using ODS (SAS reporting system)

- `hospital_model_output.pdf`  
  - Example of interpretable model output for leadership:
  - driver magnitudes
  - which hospitals are “above expected cost”

- `README.md`  
  - High-level summary (this file)

---

## Disclaimer
This project uses publicly available data and is for demonstration / portfolio purposes.  
No internal, restricted, or patient-level data is included.


