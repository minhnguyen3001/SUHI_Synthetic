# Leveraging Community Health Worker (CHW) Notes to Improve Emergency Department (ED) Readmission Classification

Research project presented at the Chicago Area Undergraduate Research Symposium (CAURS), April 19, 2025, Robert H. Lurie Medical Research Center, Chicago, IL.

## Overview

The Centers for Medicare and Medicaid Services treat 30 day Emergency Department (ED) readmissions as a key measure of care quality. The Sinai Urban Health Institute (SUHI) trains Community Health Workers (CHWs), drawn from the same Chicago neighborhoods as the patients they serve, to follow up with patients after discharge and connect them with resources that can help prevent avoidable readmissions.

Past work has shown that CHW and Social Determinants of Health (SDoH) data improve predictive models of 30 day ED readmission. The free text notes that CHWs write during these visits have not been used in that work, mainly because they are sparse, inconsistently structured, and unlabeled.

This project focuses on a subset of CHW notes called "resource notes," which document specific needs such as food or transportation. The goal is to enrich the larger set of CHW notes with meaningful tags derived from these resource notes, with an goal toward improving 30 day ED readmission prediction down the line.

## Background

* CHWs serve as trusted liaisons between the hospital and patients in under resourced Chicago neighborhoods.
* Resource notes are a subset of CHW notes that flag a specific, named need (food, transportation, housing, insurance, and so on) rather than general contact information.
* Food and transportation needs are the most heavily represented categories in the current dataset, so the analysis to date concentrates on those two.

## What is in this repository

| File | Description |
|---|---|
| `CAURS_2025_Nguyen_Minh.docx` | Full research abstract submitted to CAURS 2025. |
| `Minh_Nguyen_CAURS_2025_Poster_Adjusted.pptx` | Research poster presented at CAURS 2025, covering background, methodology, results, and future work. |
| `Notes_Counter.ipynb` | Groups SDoH resource columns by category, identifies which patients have resource notes on file, computes resource note frequency tables, calculates readmission rate by resource category, and applies Yule's Q to test the association between receiving a given resource and 30 day readmission. |
| `SyntheticDataCreate.ipynb` | Exploratory notebook for generating synthetic CHW resource notes (food and transportation) with a language model to help offset the sparsity of real labeled notes, plus a lexical diversity check (Yule's Q on word frequency) and a food/transportation need cross tabulation. |

## Methodology

1. Categorical resource fields recorded by CHWs (for example `food_res`, `food_nores`, `trans_res`, `trans_nores`) are converted to binary indicators of whether a patient has a given resource need on record.
2. Patients are grouped by `record_id` to determine, per resource category, whether any resource note exists and whether the patient was readmitted within 30 days (`day_readmit`).
3. Resource frequency tables and readmission rates are computed for each SDoH category, with particular attention to food and transportation.
4. Yule's Q is used to measure the strength and direction of the association between having a resource note in a given category and 30 day readmission.
5. `SyntheticDataCreate.ipynb` prototypes an approach to generate additional synthetic resource notes from a small set of real examples, in order to test how the Yule's Q estimate behaves as sample size grows.

## Key findings so far

* Of 2,177 patients in the dataset, 441 had at least one resource note on file from a CHW.
* Food and transportation needs are the most common resource categories represented.
* For food resource notes specifically, readmission rate was about 23 percent (21 of 91) among patients with a food resource note, compared to a much higher rate among patients without one, producing a Yule's Q of -1.0 in this subset. That value reflects a small, imbalanced sample (only 3 patients had a "food, no resource" note) and should be read as a preliminary signal rather than a stable effect size.
* Overall, the pattern across categories suggests that patients who had an identified resource need addressed tended to have lower readmission rates than those whose need was identified but not met.

## Future work

* Generate synthetic resource notes to study how the number of available notes affects the stability of the Yule's Q estimate.
* Train Doc2Vec and Support Vector Machine (SVM) models on labeled resource notes to automatically identify SDoH categories in the larger, unstructured set of contact notes.
* Investigate whether adding contact note features alongside existing tabular features improves 30 day ED readmission prediction.

## Authors

Minh Nguyen, Ankita Mishra, Navika Maglani, Charmi Patel, Daniela Raicu, Kelly McCabe, Jacob Furst, and Roselyne Tchoua

College of Computing and Digital Media, DePaul University, Chicago, IL
Sinai Urban Health Institute, Chicago, IL

## Acknowledgments

This work was partially supported by the Motorola Solutions Foundation Scholars Program and the Sinai Urban Health Institute.

## Contact

Minh Nguyen: mnguye93@depaul.edu
PI, Roselyne Tchoua: rtchoua@depaul.edu

## A note on data and credentials

The underlying patient level CHW and SDoH dataset is not included in this repository, since it contains sensitive health information. The notebooks here are shared to document the analysis approach.

Before pushing this repository publicly, remove the hardcoded Hugging Face access token currently in `SyntheticDataCreate.ipynb` (the `login(token="hf_...")` call) and revoke that token from your Hugging Face account settings. If the notebook has already been committed anywhere with that token in it, treat the token as compromised and generate a new one, since old commits can still expose it even after you edit the file. Going forward, read tokens from an environment variable or a local `.env` file that is listed in `.gitignore` instead of pasting them into notebook cells.
