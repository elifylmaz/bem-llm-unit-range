# bem-llm-unit-range

Code and data for:

**Elif Yılmaz, İzzet Fatih Şentürk — "The Effect of Unit and Range
Information on Large Language Model-Based Building Energy
Parameterization"** (submitted to *Advanced Engineering Informatics*,
Special Issue: Application of Large Language Models in Energy
Engineering and Informatics).

This repository contains the full pipeline used to (i) extract
reference EnergyPlus parameters from ASHRAE 90.1-2019 prototype
buildings, (ii) derive leave-one-building-out (LOBO) plausible ranges,
(iii) prompt two LLMs (Llama-3.3-70B, gpt-oss-120b) under a 2x2
unit/range factorial design, (iv) inject and simulate the resulting
parameter vectors in EnergyPlus, and (v) compute all metrics and
statistical models reported in the paper.

## Repository structure

```
Sprint1-Data Acquisition and Environment Setup.ipynb
Sprint2-Reference Parameter Extraction and LOBO Range Computation.ipynb
Sprint3-LLM Parameter Generation.ipynb
Sprint4M-Main Experiment(S4M).ipynb
Sprint4S-Weather-File Stress Test (S4S).ipynb
Sprint5-Evaluation Metrics and Statistical Modeling.ipynb
Sprint6-Robustness Checks and Reproducibility.ipynb

data/
  idf_referans/                  Reference IDFs (5 tested buildings,
                                  Buffalo/5A) and the original ASHRAE
                                  90.1-2019 prototype archives (16
                                  buildings, all climates) used to
                                  build the LOBO pool. See
                                  THIRD_PARTY_DATA.md.
  epw_iklim/                     Weather files (5A/1A/7). See
                                  THIRD_PARTY_DATA.md.
  Energy+.idd                    EnergyPlus IDD schema (third-party,
                                  bundled for reference only). See
                                  THIRD_PARTY_DATA.md.
  environment_sprint*.json       Per-sprint reproducibility records
                                  (Python/package versions, EnergyPlus
                                  version, model/API settings).
  extraction_manifest.json       SHA-256 manifest of source files used
                                  in parameter extraction.

analiz/
  sprint2_lobo/                  LOBO range table, control-parameter
                                  file, buffer-sensitivity check,
                                  full-pool leave-one-out audit,
                                  pool-distribution skewness, manual
                                  extraction verification.
  sprint3_prompts/                Prompt templates (f1-f4_prompts.json),
                                  raw API call logs (f1-f4_calls.jsonl),
                                  Fahrenheit-diagnostic table,
                                  physical-bound violation log,
                                  parameter/seed debug mappings.
  sprint4_simulation/             (created by Sprint 4 notebooks)
  sprint5_metrics/                (created by Sprint 5 notebook)
  sprint6_robustness/             ANOVA/robustness outputs: per-model
                                  ANOVA, nonparametric checks, eta^2
                                  comparisons, seed-count sensitivity,
                                  reproducibility audit.
  protocol.json                   Master experimental protocol (seeds,
                                  climates, physical bounds, target/
                                  control parameters, model resolution
                                  log).

idf_llm/                          Injected IDF files, one per
                                   building x format x model x seed
                                   x climate combination (360 files).

sonuclar/                          Smoke-test EnergyPlus outputs
                                   (~28 preliminary runs used to
                                   validate the pipeline before the
                                   full batch was committed; richer
                                   per-run diagnostic file set:
                                   .audit, .bnd, .dxf, .rvaudit, sizing
                                   CSVs, etc.).

sonuclar 2/                        Full main-experiment (240 vectors)
                                   and weather-file-stress-test
                                   (120 vectors) EnergyPlus outputs
                                   referenced by the manuscript's
                                   Tables and Figures (reduced per-run
                                   file set: meter/table/CSV only, to
                                   keep repository size manageable).
```

## Requirements

- Python 3.12 (tested on 3.12.13); see `requirements.txt`.
- EnergyPlus 22.1.0 for Sprint 4 (main experiment and weather-file
  stress test), to match the native `Version,22.1;` tag of the
  reference IDFs. (Sprint 1's initial environment check downloads
  EnergyPlus 23.2.0 for a general availability check only; this is
  **not** the version used for the reported simulations.)
- A Groq API key (`GROQ_API_KEY` environment variable) for Sprint 3,
  to call `llama-3.3-70b-versatile` and `openai/gpt-oss-120b`.
  **Note:** `llama-3.3-70b-versatile` was announced as deprecated by
  Groq in June 2026, after this study's data collection; exact
  reproduction of the Llama-3.3-70B results may require an equivalent
  replacement model.
- These notebooks were developed and run on Google Colab (paths under
  `/content/drive/MyDrive/BEM-LLM`); adjust the `BASE` path at the top
  of each notebook if running locally or on a different platform.

## Run order

Run the notebooks in this order; each writes outputs consumed by the
next:

1. **Sprint 1** -- downloads/verifies EnergyPlus, weather files, and
   the ASHRAE 90.1-2019 prototype archives; writes
   `data/environment_sprint1.json`.
2. **Sprint 2** -- extracts reference parameters from the 16-building
   pool via comment-anchored parsing, computes LOBO ranges and the
   full-pool leave-one-out audit; writes `analiz/sprint2_lobo/*`.
3. **Sprint 3** -- builds the four prompt formats (F1-F4) and calls
   both LLMs via the Groq API; writes `analiz/sprint3_prompts/*`
   (prompts, raw calls, physical-bound/Fahrenheit diagnostics).
4. **Sprint 4M** -- injects parameters into the reference IDF for the
   main experiment (5A, all formats), runs the pre-simulation
   validation gate (physical-bound + injection round-trip checks),
   and runs EnergyPlus; writes `idf_llm/*` (5A subset) and
   `sonuclar 2/*` (5A subset).
5. **Sprint 4S** -- same as Sprint 4M but for the weather-file stress
   test (1A/7, format F4 only); writes the remaining `idf_llm/*` and
   `sonuclar 2/*` entries.
6. **Sprint 5** -- computes K1 (GSO), K2 (CV(RMSE)/NMBE/ASHRAE-14), K3
   (EYA/PFI), and the K4 Unit x Range ANOVA models (energy and
   parameter level), plus the baseline/random comparison; writes
   `analiz/sprint5_metrics/*`.
7. **Sprint 6** -- residual diagnostics, nonparametric sensitivity
   checks, within-model reproducibility, seed-count sensitivity, and
   the automated Reproducibility Audit; writes
   `analiz/sprint6_robustness/*`.

Approximate run times (single Colab session, no GPU required): Sprint
1 <5 min; Sprint 2 ~5-10 min; Sprint 3 ~15-20 min (network-bound, 300
API calls); Sprint 4M ~1-2 hours (213 EnergyPlus runs); Sprint 4S
~30-45 min (120 EnergyPlus runs); Sprint 5 <5 min; Sprint 6 <5 min.

## Which results reproduce which parts of the paper

- `analiz/sprint2_lobo/lobo_range_table.json`, `full_pool_loo_audit.csv`
  -> Section 3.3 (LOBO range derivation), Table showing 134/144
  (93.1%) full-pool leave-one-out coverage.
- `analiz/sprint3_prompts/fahrenheit_diagnostic_table.csv`,
  `bound_violation_log.csv` -> Section 5.1 (24/27 physical-bound
  eliminations, Fahrenheit-scale temperatures under F1).
- `analiz/sprint5_metrics/*` -> Tables for K1 (GSO), K2 (CV(RMSE)/NMBE,
  ASHRAE-14 compliance), K3 (EYA/PFI), K4 (Unit x Range ANOVA, both
  levels), and the baseline/random comparison (Section 5.6).
- `analiz/sprint6_robustness/*` -> Section 5.7 (residual diagnostics,
  Kruskal-Wallis/permutation checks, within-model ANOVA, seed-count
  sensitivity, raw-vs-log eta^2 comparison) and the Reproducibility
  Audit.

## Data availability and licensing

See [`THIRD_PARTY_DATA.md`](THIRD_PARTY_DATA.md) for the source and
license of the third-party ASHRAE/DOE prototype IDFs, EPW weather
files, and the EnergyPlus IDD schema. All code in this repository
(notebooks and any accompanying scripts) is released under the MIT
License (see [`LICENSE`](LICENSE)); this does not extend to the
third-party data described in `THIRD_PARTY_DATA.md`.

## Citation

If you use this code or data, please cite the paper (see
[`CITATION.cff`](CITATION.cff)).

## Known limitations

- The seed parameter is best-effort and does not guarantee bit-exact
  reproducibility across hardware/OS/library-version combinations.
- `llama-3.3-70b-versatile` was deprecated by Groq in June 2026;
  exact reproduction of the Llama-3.3-70B results requires either an
  archived/equivalent endpoint or accepting that results may differ
  under a replacement model.
- The two model families were run with asymmetric inference budgets
  (`gpt-oss-120b`: `max_tokens=800`, `reasoning_effort='low'`;
  `llama-3.3-70b-versatile`: `max_tokens=400`, `reasoning_effort`
  unspecified); see `data/environment_sprint3.json` and the paper's
  Discussion/Limitations for details.
- This is a controlled case study limited to five buildings, one main
  reference climate (Buffalo/5A), and two model families; results
  should not be assumed to generalize beyond this scope.
