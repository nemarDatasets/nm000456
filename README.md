[![DOI](https://img.shields.io/badge/DOI-10.82901%2Fnemar.nm000456-blue)](https://doi.org/10.82901/nemar.nm000456)

# Human-AI collaboration for co-managing epilepsy in real time (scalp EEG + SEEG)

BIDS version of Pennsieve Discover dataset 696 version 2, "Data for Human-AI Collaboration for Co-Managing Epilepsy in Real Time"
(Shi, Goldblum, Ojemann, Aguila, Nix, Walsh, Lavelle, Davis, Sinha, Conrad, Litt; doi:10.26275/xbu9-ulpn; licence CC-BY-NC-SA-4.0,
kept unchanged). Paper: Goldblum Z, Shi H, Xu Z, et al., "An Implantable Device that Converses with Patients and Learns to Co-Manage
Epilepsy", medRxiv 2026, doi:10.64898/2026.01.26.26344234. Ethics: University of Pennsylvania IRB protocol #849276.

**Task / paradigm.** Inpatient prototype linking scalp and intracranial EEG to a conversational AI agent on the patient's smartphone during continuous video-EEG monitoring in the Penn Level 4 EMU; 13 patients; seizure detection retrained with patient/clinician feedback (Goldblum, Shi et al., medRxiv 2026, doi:10.64898/2026.01.26.26344234, abstract and Methods). Deposit: one EDF per recording day (raw_data/edf/PennEPIxxxxx/PennEPIxxxxx_dayNN.edf, 69 files), annotation CSVs (seizure_events, patient_events, sleep, medications, databricks_events) and model outputs (deposit readme.md).

**Modality.** participants.tsv recording_type: 11 of 13 patients are **Scalp** EEG; only 2 are SEEG (PID007 = PennEPI00202, PID013 = PennEPI02154) (deposit files/participants.tsv). The atlas modality "ECoG+SEEG" should read "scalp EEG + SEEG (2 of 13)".

**Recording system / filters.** Processing: bandpass 0.5-100 Hz (scalp) and 0.5-250 Hz (iEEG), notch 60 and 120 Hz; CAR or bipolar re-referencing per algorithm (paper Methods "Cloud data processing"). Acquisition amplifier and sampling rate not stated (n/a; read from EDF headers).

**Localisation method.** None in the deposit or paper: only clinical lobe-level epilepsy localization and laterality per patient (participants.tsv Localization/Laterality). PennEPI00202 and PennEPI02154 (the 2 SEEG patients) are not among the 104 public PennEPI single-subject releases, so no public electrode localisation exists for them.

## Contents

- 13 subjects, `sub-<PennEPI id>`. 11 scalp-EEG patients (`eeg/`) and 2 stereo-EEG patients (`ieeg/`: sub-PennEPI00202, sub-PennEPI02154), kept
  together as one dataset as deposited.
- One EDF per deposit recording day: `sub-<id>_task-monitoring_run-<NN>_<eeg|ieeg>.edf`, run NN = deposit day number (file `dayNN`);
  69 recordings. Scalp: 46 signal channels at 256 Hz (10-20 + Fpz/Pz/A1/A2, F9/F10/T9/T10/P9/P10, LOC/ROC, EKG, X11-X18, DC1-4,
  OSAT, PR). SEEG: 1024 Hz depth contacts (L*), plus ECG, DC, TRIG, OSAT/PR/Pleth and unused inputs (C99-C128, typed MISC).
  Channel types are inferred from labels; references, amplifier and hardware filters are not stated by the source (n/a).
- `events.tsv` per run from the deposit annotation CSVs: seizures (seizure_events), patient push-button events (patient_events),
  sleep-stage onsets (sleep), medication administrations (medications) and patient annotations through the human-AI platform
  (databricks_events). Onsets are seconds from the EDF start; events outside the span of any EDF stay only in sourcedata/annots.
- `derivatives/humanai-seizure-detection/`: the deposit's `intermediate_data` (CITL day-by-day predictions, fine-tuning runs,
  metrics) - model outputs, not raw data.
- `sourcedata/`: `annots/` (deposit annotation CSVs, dates scrubbed as below), `pennsieve/` (deposit readme, changelog, manifest,
  participants table and sidecar), `edf_provenance.tsv` (deposit path -> BIDS path, sha256 of the data records of both files),
  `edf_discontinuities.tsv` (EDF+D record gaps).

## Conversion and privacy review (2026-10-08)

- The EDFs are the deposit files with a header-only edit. Every header was scanned: patient field `X X X X`, recording field
  `Startdate DD-JAN-2001 X X X`, no names or hospital numbers in any header or channel field, and no text in any EDF+ annotation
  of any data record. The deposit had already shifted all dates to January 2001 with DD = deposit day; following the
  release rule (keep year and month, day -> 01) the day was set to 01 in the startdate field and in the `Startdate`
  recording subfield of 58 files (the other 11 files already had day 01 and are byte-identical to the deposit).
  The data records are unchanged: sha256 of the data region of each written file equals that of the deposit file
  (sourcedata/edf_provenance.tsv). The original headers of the edited files (only difference: the day number, which is kept as
  the run label / scans.tsv deposit_day) are not redistributed; the unchanged deposit files remain at the DOI above.
- Annotation CSVs and model outputs: every timestamp column has its day set to 01, and the deposit day is kept in an added
  `<column>_deposit_day` column, so relative timing is preserved. One free-text medication note in participants.tsv contained a
  month/day calendar date; its day was set to 01.
- PennEPI identifiers are research identifiers of the Penn epilepsy registry, used as subject labels as in the deposit.
- Some scalp files are EDF+D (discontinuous; scans.tsv edf_subtype). RecordingDuration is the wall-clock span; gaps are listed
  in sourcedata/edf_discontinuities.tsv.

## Overlap with other public data

Not in NEMAR, OpenNeuro, DANDI or EEGDash (searched 2026-10-07/08 by DOI, title, authors and PennEPI ids). Of the 13 patients,
only PennEPI00037 also has a public PennEPI single-subject release (Pennsieve 613, doi:10.26275/zm8u-bo15), which holds a different,
intracranial recording; the scalp EEG here is not part of it. The two SEEG patients (PennEPI00202, PennEPI02154) have no PennEPI
release, and no electrode coordinates or contact localisation are public for them (participants.tsv gives the clinical
lobe-level localisation and laterality only).

## Electrodes

The two SEEG subjects have `electrodes.tsv` (contact names from the EDF labels, `group` = shaft prefix, x/y/z = n/a) and
`coordsystem.json` (space Other, unknown): no electrode localisation is public for them. events.tsv `event_text` holds the
source free text; `response_onset` is the patient's platform response time in seconds from the EDF start.
