# Data for Human-AI Collaboration for Co-Managing Epilepsy in Real Time


This dataset supports human-in-the-loop (HITL / CITL) seizure detection model evaluation and patient-specific fine-tuning. Patient folders use **PennEPI** identifiers (e.g. `PennEPI02012`). 


## Top-level layout

| Path | Status | Description |
|------|--------|-------------|
| `participants.tsv` / `participants.json` | **Present** | Participants table and column sidecar at dataset root |
| `raw_data/` | **Present** | Source EDFs and event CSVs under `edf/` and `events/` (per PennEPI ID) |
| `intermediate_data/` | **Present** | Derived analysis products: CITL day predictions, fine-tune runs, aggregate metrics |

```
<dataset root>/
├── participants.tsv
├── participants.json
├── raw_data/
│   ├── edf/
│   └── events/
└── intermediate_data/
    ├── citl_results/
    └── results/
```



---

## `participants.tsv` / `participants.json`

Cohort-level clinical and recording summary. Includes fields such as:

- `PID`, `PennEPIID`  
- `Sex`, `Race`, `Ethnicity`, `recording_type`  
- Stay / enrollment lengths  
- Seizure counts and average duration  
- Annotation and survey-related flags  
- Laterality, localization, seizure-type notes, medications  

`participants.json` documents column descriptions for the TSV.

---

## `raw_data/`

Source electrophysiology and annotation timelines for all 13 subjects, organized as parallel `edf/` and `annots/` trees.

```
raw_data/
├── edf/
│   └── PennEPI*****/
│       ├── PennEPI*****_day01.edf
│       ├── PennEPI*****_day02.edf
│       └── …
└── annots/
    └── PennEPI*****/
        ├── seizure_events.csv
        ├── patient_events.csv
        ├── sleep.csv
        ├── medications.csv
        └── databricks_events.csv
```

| Item | Description |
|------|-------------|
| `edf/PennEPI*****/` | Continuous de-identified recordings for that subject |
| `PennEPI*****_dayNN.edf` | One EDF per hospital / recording day (`day01`, `day02`, …) |
| `annots/PennEPI*****/` | Annotation CSVs for the same subject |
| `seizure_events.csv` | Seizure timelines (original columns, e.g. `start` / `stop`) |
| `patient_events.csv` | Patient self-reported seizures or clinical events|
| `sleep.csv` | Sleep-stage annotations |
| `medications.csv` | Medication administration timeline |
| `databricks_events.csv` | Patient annotations for seizures through the human-AI platform |

Same 13 PennEPI IDs appear under both `edf/` and `annots/`.

---

## `intermediate_data/`

Processed outputs from baseline and fine-tuned seizure detection models. Intended for secondary analysis without re-reading the full continuous EEG streams.

### `citl_results/`

Day-by-day clinician-in-the-loop (CITL) evaluation for a **subset** of patients.


```
citl_results/
├── baseline_metrics_all_patients.csv
├── finetuned_metrics_all_patients.csv
├── improvement_metrics_all_patients.csv
├── summary_statistics_all_metrics.csv
└── PennEPI*****/
    ├── day1/
    │   ├── predictions_day1_before_day1.parquet
    │   ├── predictions_day1_after_day1.parquet
    │   ├── predictions_day1_after_day2.parquet
    │   └── …
    ├── day2/
    │   └── …
    └── summary/
        └── evaluation_metrics.csv
```

**Cohort-level CSVs** summarize baseline vs. fine-tuned performance and improvement across patients.

| Item | Description |
|------|-------------|
| `dayN/` | Predictions for evaluation day *N* |
| `predictions_dayN_before_dayK.parquet` | Model outputs on day *N* **before** fine-tuning that uses feedback through day *K* |
| `predictions_dayN_after_dayK.parquet` | Model outputs on day *N* **after** fine-tuning that uses feedback through day *K* |
| `summary/evaluation_metrics.csv` | Per-patient metric summary across days |

Parquet prediction tables generally include timestamps / windows and model scores or labels for continuous evaluation along the hospital stay.

### `results/`

Batch fine-tuning and baseline evaluation outputs (non–day-oriented) for **all 13** subjects.

```
results/
└── PennEPI*****/
    ├── base_model/                    # or onset_base_model / related baseline name
    │   ├── metrics/
    │   ├── pred/
    │   └── prob/
    └── PennEPI*****_<base>_<hparams>_<update_rule>/
        ├── metrics/                   # thresholded metric tables (CSV)
        ├── pred/                      # discrete predictions (CSV)
        └── prob/                      # continuous scores / probabilities (CSV)
```