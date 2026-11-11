# Phase 3 - Priority 3 (Experiment 4): Operational Cost of Stale Decisions & Telemetry Refresh Policy (12 Benchmark Hardware Jobs, 0 Auxiliary Jobs)

## 1. Executive Summary & Principal Comparative Benchmark Tables

This report documents the completed hardware execution of **Priority 3 - Experiment 4** from [`phase3_submission_manifest_v3.json`](../manifests/phase3_submission_manifest_v3.json) (`SHA-256 = 86968d1471af9228dd1902b0266569ac95b143e237027718cffaa383ab03cfba`) and pre-submission amendments `AMEND-P3-002` and `AMEND-P3-003` in [`phase3_amendment_log_v3_1.json`](../manifests/phase3_amendment_log_v3_1.json) (`SHA-256 = d2303277634adb9e1433d6f7a9e34f60d8cb7d6ad37ba1c6b5de1baeab08e692`):

- **Sample Size & Hardware Scope:** `N = 12` standalone benchmark QPU jobs (6 IBM Quantum jobs across `ibm_kingston` and `ibm_fez` + 6 IQM Resonance jobs across `emerald` and `garnet`; **0 warm-up or troubleshooting jobs** required in Experiment 4), executing **192 compiled PUBs** (96 IBM at 1,024 shots/PUB + 96 IQM at 512 shots/PUB) and **147,456 physical shots** (98,304 IBM + 49,152 IQM) across **24 disjoint logical workloads** (12 Window 1 Dev workloads `W_P3_Dev_069..080` + 12 Blind Holdout workloads `W_P3_ModHold_037..048` across Windows 2 and 3), for a total settled spend of **39.00 s IBM** and **24.75 cr IQM**.
- **Strict Chronological Isolation Verified:**
  1. **Window 1 Development (`Cohort Dev-80`: `W_P3_Dev_069..080`, 12 workloads, 4 benchmark jobs):** Completed at `2026-11-10T02:45:23+00:00` ([`phase3_exp4_part_a_w1_dev_results.json`](../data/phase3_exp4_part_a_w1_dev_results.json)) with zero Holdout access.
  2. **Refresh Policy Lock (Prior to Either Holdout Window):** Calibrated and froze [`phase3_locked_refresh_policy.json`](../data/phase3_locked_refresh_policy.json) at `2026-11-10T02:47:19.377917+00:00` (`SHA-256 = c8fa9126a5daa6304160e82e3b800db586e0a92ed327fb9cc7395803563ad5b0`) prior to generating or compiling `W_P3_ModHold_037..048`.
  3. **Holdout Windows 2 & 3 (`Cohort Module-Holdout-60`: `W_P3_ModHold_037..048`, 12 untouched workloads, 8 benchmark jobs):** Pre-execution prospective `C_Triggered_Refresh_Policy` choices frozen at `2026-11-10T02:56:06+00:00` ([`phase3_exp4_holdout_preexecution_frozen_choices.json`](../data/phase3_exp4_holdout_preexecution_frozen_choices.json), `SHA-256 = 6f12782938ce2147398b4faaa9f680af11718e620b9c0a0be9b85b8f22fe22cb`), completed at `2026-11-10T02:58:46+00:00` ([`phase3_exp4_part_b_holdout_results.json`](../data/phase3_exp4_part_b_holdout_results.json) and [`phase3_exp4_complete_results.json`](../data/phase3_exp4_complete_results.json)).

> [!IMPORTANT]
> **Metric & Interpretive Scope Conventions:**
> - **Classical Bhattacharyya Distribution Overlap** is F_B = Σ_x √(P_ideal(x) · P_meas(x)) (labeled `"State Fidelity"` in some Studio UI views), and **Squared Hellinger Fidelity** is F_H = F_B².
> - Per `AMEND-P3-003`, `C1` (`Top-4(G_hist)` at `T0`), `C2` (isomorphic remap on `G_live`, zero resynthesis), and `C3` (`C2` warm-start + resynthesis across `Top-4(G_live) U {S_0, S_C2}`) execute randomized within shared primary-QPU jobs (`ibm_kingston` / `emerald`), while `C4` executes in separate standalone alternate-QPU jobs (`ibm_fez` / `garnet`). Consequently, **`C3` vs `C1` measures the combined refresh-and-expanded-search policy**, whereas **`C3` vs `C2` isolates the incremental value of full resynthesis beyond placement-only refresh**.

### Table 1A: Blind Holdout (Windows 2 & 3 Combined: `W_P3_ModHold_037..048`, `N = 12` Workloads) - IBM Quantum (4 Jobs: `dbp8g6gqtr0c738dueug`, `dbp8g7k4148s739u4gt0`, `dbp8g8gou4as73ct6g2g`, `dbp8g9c4148s739u4h20`; 48 PUBs, 49,152 Shots)

| Refresh Strategy Arm | Executed QPU(s) | Candidates Changed vs `C1` | Qualifying Accepted (↑) | Target Attainment `%` (↑) | `% Delta` F_B vs Best Fixed (`C3`) (↑) | F_B *(mean / median)* (↑) | F_H = F_B² *(mean / median)* (↑) | `TVD` *(mean / median)* (↓) | Shannon Entropy `bits` *(mean / median)* | `2Q` Gates *(mean)* (↓) | Compiled Depth *(mean)* (↓) | Live CPU Effort `s/workload` *(mean / median)* (↓) |
| :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **`C1_Keep_Original_Stale`** | `ibm_kingston` | 0 / 12 (0 vs `C2`) | **10 / 12** | **83.33%** | -0.63% | 0.9215 / 0.9236 | 0.8531 / 0.8530 | 0.1893 / **0.1753** | 3.5899 / 3.2071 | 54.50 | 98.17 | **0.0000 / 0.0000** |
| **`C2_Update_Placement_Only`** | `ibm_kingston` | 9 / 12 (0 resynth.) | **10 / 12** | **83.33%** | -0.46% | 0.9230 / 0.9293 | 0.8556 / 0.8636 | 0.1918 / 0.1787 | 3.6215 / **3.1253** | 54.50 | 98.17 | 1.8059 / 0.9994 |
| **`C3_Recompile_Fresh_Same_QPU`** | `ibm_kingston` | 10 / 12 (3 / 12 vs `C2`) | **10 / 12** | **83.33%** | **0.00%** | **0.9273** `/` **0.9305** | **0.8629** `/` **0.8657** | **0.1823** `/ 0.1786` | 3.5772 / 3.1339 | **54.17** | **97.42** | 7.4049 / 5.9810 |
| **`C4_Switch_QPU_Live`** | `ibm_fez` | 12 / 12 (alt. QPU) | 9 / 12 | 75.00% | -0.91% | 0.9188 / 0.9290 | 0.8489 / 0.8630 | 0.2025 / 0.1771 | 3.7220 / 3.7001 | 55.58 | 104.00 | 2.9335 / 2.8060 |
| **`C_Triggered_Refresh_Policy` (Locked Overlay: `12 C1`)** | `ibm_kingston` | 0 / 12 (12 kept `C1`) | **10 / 12** | **83.33%** | -0.63% | 0.9215 / 0.9236 | 0.8531 / 0.8530 | 0.1893 / **0.1753** | 3.5899 / 3.2071 | 54.50 | 98.17 | 1.8059 / 0.9994 (75.6% CPU saved vs `C3`) |

### Table 1B: Blind Holdout (Windows 2 & 3 Combined: `W_P3_ModHold_037..048`, `N = 12` Workloads) - IQM Resonance (4 Jobs: `01a1c363-f3ae-777d-9057-be1a49bd973d`, `01a1c364-03ce-75e0-9943-37004ef7e062`, `01a1c363-dcb1-70a0-bb81-bece74494c55`, `01a1c363-e609-702f-9389-418328ddefb3`; 48 PUBs, 24,576 Shots)

| Refresh Strategy Arm | Executed QPU(s) | Candidates Changed vs `C1` | Qualifying Accepted (↑) | Target Attainment `%` (↑) | `% Delta` F_B vs Best Fixed (`C3`) (↑) | F_B *(mean / median)* (↑) | F_H = F_B² *(mean / median)* (↑) | `TVD` *(mean / median)* (↓) | Shannon Entropy `bits` *(mean / median)* | `2Q` Gates *(mean)* (↓) | Compiled Depth *(mean)* (↓) | Live CPU Effort `s/workload` *(mean / median)* (↓) |
| :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **`C1_Keep_Original_Stale`** | `emerald` | 0 / 12 (0 vs `C2`) | 5 / 12 | 41.67% | -1.83% | 0.8485 / 0.8642 | 0.7350 / 0.7471 | 0.3232 / 0.3248 | 4.1618 / 4.0417 | 48.33 | 71.17 | **0.0000 / 0.0000** |
| **`C2_Update_Placement_Only`** | `emerald` | 10 / 12 (0 resynth.) | **7 / 12** | **58.33%** | -0.03% | 0.8641 / 0.8765 | 0.7591 / 0.7687 | 0.2966 / 0.2680 | 3.9577 / 3.5813 | 48.33 | 71.17 | 0.1675 / 0.0833 (94.3% CPU saved vs `C3`) |
| **`C3_Recompile_Fresh_Same_QPU`** | `emerald` | 10 / 12 (3 / 12 vs `C2`) | **7 / 12** | **58.33%** | 0.00% | 0.8644 / **0.8841** | 0.7618 / **0.7819** | **0.2909** `/ 0.2701` | 3.9400 / 3.5799 | 48.17 | **69.83** | 2.9546 / 3.2112 |
| **`C4_Switch_QPU_Live`** | `garnet` (`20Q`) | 12 / 12 (alt. QPU) | 5 / 12 | 41.67% | -6.28% | 0.8100 / 0.8477 | 0.6842 / 0.7188 | 0.3731 / 0.3243 | 4.4657 / 4.4154 | 49.67 | 85.08 | 1.4421 / 1.4268 |
| **`C_Triggered_Refresh_Policy` (Locked: `5 C1, 5 C2, 2 C3`)** | `emerald` | 7 / 12 (5 kept `C1`) | 6 / 12 | 50.00% | **+0.16%** | **0.8658** `/ 0.8812` | **0.7621** `/ 0.7767` | 0.2938 / **0.2543** | 3.9898 / 3.9071 | **48.08** | 70.00 | 1.9993 / 2.3261 (32.3% CPU saved vs `C3`) |

---

## 2. Window 1 Development Split Tables (`Cohort Dev-80`: `W_P3_Dev_069..080`, `N = 12` Workloads, 4 Jobs)

### Table 2A: Window 1 Development - IBM Quantum (2 Jobs: `dbp7l344148s739u2t8g` on `ibm_kingston` + `dbp7l45t7fqc7388t810` on `ibm_fez`; 48 PUBs, 49,152 Shots)

| Refresh Strategy Arm | Executed QPU | Candidates Changed vs `C1` | Qualifying Accepted (↑) | Target Attainment `%` (↑) | `% Delta` F_B vs Best Fixed (`C1`) (↑) | F_B *(mean / median)* (↑) | F_H = F_B² *(mean / median)* (↑) | `TVD` *(mean / median)* (↓) | Shannon Entropy `bits` *(mean / median)* | `2Q` Gates *(mean)* (↓) | Compiled Depth *(mean)* (↓) | Live CPU Effort `s/workload` *(mean / median)* (↓) |
| :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **`C1_Keep_Original_Stale`** | `ibm_kingston` | 0 / 12 (0 vs `C2`) | **11 / 12** | **91.67%** | **0.00%** | **0.9244** `/ 0.9138` | **0.8570** `/ 0.8351` | **0.1920** `/ 0.2060` | 3.6168 / 2.9171 | 56.17 | **99.92** | **0.0000 / 0.0000** |
| **`C2_Update_Placement_Only`** | `ibm_kingston` | 11 / 12 (0 resynth.) | 10 / 12 | 83.33% | -0.62% | 0.9187 / 0.9134 | 0.8471 / 0.8344 | 0.2089 / 0.2067 | 3.6881 / 3.1846 | 56.17 | **99.92** | 2.5443 / 1.9994 |
| **`C3_Recompile_Fresh_Same_QPU`** | `ibm_kingston` | 11 / 12 (4 / 12 vs `C2`) | 10 / 12 | 83.33% | -0.84% | 0.9166 / 0.9107 | 0.8439 / 0.8295 | 0.2106 / 0.2008 | 3.7139 / 3.3501 | **56.00** | **99.92** | 8.1461 / 7.9388 |
| **`C4_Switch_QPU_Live`** | `ibm_fez` | 12 / 12 (alt. QPU) | 10 / 12 | 83.33% | -0.89% | 0.9162 / **0.9213** | 0.8438 / **0.8489** | 0.2088 / **0.1896** | 3.7319 / 3.4537 | 56.25 | 105.08 | 2.9413 / 2.6534 |

### Table 2B: Window 1 Development - IQM Resonance (2 Jobs: `01a1c32e-d91b-703a-99bf-7b47889019c6` on `emerald` + `01a1c32f-33ad-74d5-bf61-8bd1d0bd8dda` on `garnet`; 48 PUBs, 24,576 Shots)

| Refresh Strategy Arm | Executed QPU | Candidates Changed vs `C1` | Qualifying Accepted (↑) | Target Attainment `%` (↑) | `% Delta` F_B vs Best Fixed (`C3`) (↑) | F_B *(mean / median)* (↑) | F_H = F_B² *(mean / median)* (↑) | `TVD` *(mean / median)* (↓) | Shannon Entropy `bits` *(mean / median)* | `2Q` Gates *(mean)* (↓) | Compiled Depth *(mean)* (↓) | Live CPU Effort `s/workload` *(mean / median)* (↓) |
| :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **`C1_Keep_Original_Stale`** | `emerald` | 0 / 12 (0 vs `C2`) | **7 / 12** | **58.33%** | -1.98% | 0.8334 / 0.8802 | 0.7199 / 0.7748 | 0.3363 / 0.2692 | 4.2010 / 4.6011 | 50.83 | **78.00** | **0.0000 / 0.0000** |
| **`C2_Update_Placement_Only`** | `emerald` | 11 / 12 (0 resynth.) | **7 / 12** | **58.33%** | -0.14% | 0.8491 / **0.8933** | **0.7412** `/` **0.7980** | **0.3150** `/` **0.2585** | 4.0784 / 4.0365 | 50.83 | **78.00** | 0.1864 / 0.1406 (93.7% CPU saved vs `C3`) |
| **`C3_Recompile_Fresh_Same_QPU`** | `emerald` | 11 / 12 (5 / 12 vs `C2`) | **7 / 12** | **58.33%** | **0.00%** | **0.8503** `/ 0.8716` | 0.7392 / 0.7604 | 0.3158 / 0.2865 | 4.1103 / 4.0004 | 50.00 | 78.58 | 2.9770 / 3.0817 |
| **`C4_Switch_QPU_Live`** | `garnet` (`20Q`) | 12 / 12 (alt. QPU) | 5 / 12 | 41.67% | -7.95% | 0.7827 / 0.8247 | 0.6444 / 0.6808 | 0.4235 / 0.3532 | 4.7625 / 4.8624 | **48.58** | 86.25 | 1.4847 / 1.3782 |

---

## 3. Job-Level Queue Delays, Execution Times, Calibration Hashes & Billing Ledger (12 Jobs)

### Table 3A: Complete 12-Job Hardware Execution, Queue Delay & Billing Ledger (0 Warm-Up / 0 Troubleshooting Jobs)

| Window & Split | Provider | Job Role & Arms | QPU | Hardware Job ID / UUID | PUBs | Physical Shots | Queue / Client Wait `s` (↓) | Active Execution `s` (↓) | Settled Billed Spend (↓) | Live Calibration Timestamp & `SHA-256` Prefix |
| :--- | :---: | :--- | :--- | :--- | :---: | :---: | :---: | :---: | :---: | :--- |
| **Window 1 (`Dev`)** | IBM | Primary (`C1, C2, C3`) | `ibm_kingston` | `dbp7l344148s739u2t8g` | 36 | 36,864 | 1.79 s | 10.77 s | 12.00 s | 2026-11-09 22:27:08-03:00 (`3cea25fe200e...`) |
| **Window 1 (`Dev`)** | IBM | Alternate (`C4`) | `ibm_fez` | `dbp7l45t7fqc7388t810` | 12 | 12,288 | 1.33 s | 4.32 s | 5.00 s | 2026-11-09 22:33:18-03:00 (`8ab1e12eb51b...`) |
| **Window 1 (`Dev`)** | IQM | Primary (`C1, C2, C3`) | `emerald` | `01a1c32e-d91b-703a-99bf-7b47889019c6` | 36 | 18,432 | 18.15 s | 12.60 s | 10.50 cr | `2026-11-09 05:13:09Z` (`4e3acc2f2fd9...`) |
| **Window 1 (`Dev`)** | IQM | Alternate (`C4`) | `garnet` | `01a1c32f-33ad-74d5-bf61-8bd1d0bd8dda` | 12 | 6,144 | **2,698.49 s** (45.0m) | 1.91 s | 2.25 cr | `2026-11-09 02:38:44Z` (`2b0b92435b55...`) |
| **Window 2 (`Holdout`)** | IBM | Primary (`C1, C2, C3`) | `ibm_kingston` | `dbp8g6gqtr0c738dueug` | 18 | 18,432 | 1.80 s | 8.44 s | 7.00 s | 2026-11-09 22:27:08-03:00 (`3cea25fe200e...`) |
| **Window 2 (`Holdout`)** | IBM | Alternate (`C4`) | `ibm_fez` | `dbp8g7k4148s739u4gt0` | 6 | 6,144 | 1.15 s | 2.69 s | 4.00 s | 2026-11-09 22:33:18-03:00 (`8ab1e12eb51b...`) |
| **Window 3 (`Holdout`)** | IBM | Primary (`C1, C2, C3`) | `ibm_kingston` | `dbp8g8gou4as73ct6g2g` | 18 | 18,432 | 1.52 s | 5.98 s | 7.00 s | 2026-11-09 22:27:08-03:00 (`3cea25fe200e...`) |
| **Window 3 (`Holdout`)** | IBM | Alternate (`C4`) | `ibm_fez` | `dbp8g9c4148s739u4h20` | 6 | 6,144 | 1.42 s | 2.70 s | 4.00 s | 2026-11-09 22:33:18-03:00 (`8ab1e12eb51b...`) |
| **Window 2 (`Holdout`)** | IQM | Primary (`C1, C2, C3`) | `emerald` | `01a1c363-f3ae-777d-9057-be1a49bd973d` | 18 | 9,216 | 3.60 s | 5.65 s | 5.25 cr | `2026-11-09 05:13:09Z` (`4e3acc2f2fd9...`) |
| **Window 3 (`Holdout`)** | IQM | Primary (`C1, C2, C3`) | `emerald` | `01a1c364-03ce-75e0-9943-37004ef7e062` | 18 | 9,216 | 3.59 s | 4.10 s | 3.75 cr | `2026-11-09 05:13:09Z` (`4e3acc2f2fd9...`) |
| **Window 2 (`Holdout`)** | IQM | Alternate (`C4`) | `garnet` | `01a1c363-dcb1-70a0-bb81-bece74494c55` | 6 | 3,072 | 0.46 s | 1.34 s | 1.50 cr | `2026-11-09 02:38:44Z` (`2b0b92435b55...`) |
| **Window 3 (`Holdout`)** | IQM | Alternate (`C4`) | `garnet` | `01a1c363-e609-702f-9389-418328ddefb3` | 6 | 3,072 | 0.47 s | 1.36 s | 1.50 cr | `2026-11-09 02:38:44Z` (`2b0b92435b55...`) |
| **TOTAL (12 Benchmark Jobs)** | **IBM + IQM** | **0 Aux / 0 Cancelled** | **All 4 QPUs** | **6 IBM + 6 IQM** | **192 PUBs** | **147,456 Shots** | 1.15 - 2,698.49 s | 61.90 s sum | **39.00 s IBM / 24.75 cr IQM** | Historical `T0` hashes (581585..., `705fd2...`, `0e52ca...`, `6116dd...`, `e02c55...`, `7752b7...`) in JSON |

---

## 4. Paired Statistical Contrasts (`Unadjusted` and `Holm-Bonferroni Adjusted` Two-Sided `p`-Values)

### Table 4A: Paired Overlap Contrasts (ΔF_B) on Blind Holdout (`N = 12, df = 11`, `m = 6` Contrasts) and Exploratory Pooled Cohort (`N = 24, df = 23`)

| Provider & Cohort | Contrast (`Arm A` Minus `Arm B`) | Paired `N` (`df`) | Accepted Delta `workloads` | Paired ΔF_B *(mean ± SE)* (↑) | 95% Paired `t`-Interval (ΔF_B) | Paired `t` (ΔF_B) | **Unadjusted** Two-Sided `p` (ΔF_B) | **Holm (`m=6` Holdout / `m=4` Pooled) Adjusted** `p` |
| :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **IBM Holdout** (`W2+W3`) | `C2` (Placement Remap) vs `C1` (Stale) | 12 (11) | 0 (10 vs 10) | `+0.00156 ± 0.00328` | [-0.00567, +0.00879] | +0.4742 | 0.6447 | 1.0000 |
| **IBM Holdout** (`W2+W3`) | `C3` (Refresh+Search) vs `C1` (Stale) | 12 (11) | 0 (10 vs 10) | `+0.00582 ± 0.00441` | [-0.00389, +0.01553] | +1.3198 | 0.2137 | 1.0000 |
| **IBM Holdout** (`W2+W3`) | `C3` vs `C2` (Incremental Resynthesis) | 12 (11) | 0 (10 vs 10) | `+0.00426 ± 0.00258` | [-0.00142, +0.00994] | +1.6523 | 0.1267 | 0.7602 |
| **IBM Holdout** (`W2+W3`) | `C4` (`ibm_fez` Switch) vs `C1` (Stale) | 12 (11) | -1 (9 vs 10) | `-0.00266 ± 0.00419` | [-0.01187, +0.00656] | -0.6343 | 0.5388 | 1.0000 |
| **IBM Holdout** (`W2+W3`) | `C_Triggered` vs `C1` (Stale) | 12 (11) | 0 (10 vs 10) | `0.00000 ± 0.00000` | [0.00000, 0.00000] | 0.0000 | 1.0000 | 1.0000 |
| **IBM Holdout** (`W2+W3`) | `C_Triggered` vs `C3` (Always Recompile) | 12 (11) | 0 (10 vs 10) | `-0.00582 ± 0.00441` | [-0.01553, +0.00389] | -1.3198 | 0.2137 | 1.0000 |
| **IQM Holdout** (`W2+W3`) | `C2` (Placement Remap) vs `C1` (Stale) | 12 (11) | **+2** (7 vs 5) | `+0.01559 ± 0.01019` | [-0.00683, +0.03802] | +1.5303 | 0.1542 | 0.6123 |
| **IQM Holdout** (`W2+W3`) | `C3` (Refresh+Search) vs `C1` (Stale) | 12 (11) | **+2** (7 vs 5) | `+0.01586 ± 0.01033` | [-0.00688, +0.03859] | +1.5348 | 0.1531 | 0.6123 |
| **IQM Holdout** (`W2+W3`) | `C3` vs `C2` (Incremental Resynthesis) | 12 (11) | 0 (7 vs 7) | `+0.00026 ± 0.00388` | [-0.00827, +0.00879] | +0.0680 | 0.9470 | 1.0000 |
| **IQM Holdout** (`W2+W3`) | `C4` (`garnet` Switch) vs `C1` (Stale) | 12 (11) | 0 (5 vs 5) | `-0.03847 ± 0.01584` | **[-0.07332, -0.00361]** | -2.4288 | **0.0335** (favors `C1`) | 0.2009 |
| **IQM Holdout** (`W2+W3`) | `C_Triggered` vs `C1` (Stale) | 12 (11) | +1 (6 vs 5) | **`+0.01724 ± 0.00981`** | [-0.00435, +0.03883] | +1.7578 | 0.1065 | 0.5327 |
| **IQM Holdout** (`W2+W3`) | `C_Triggered` vs `C3` (Always Recompile) | 12 (11) | -1 (6 vs 7) | `+0.00139 ± 0.00349` | [-0.00628, +0.00906] | +0.3983 | 0.6980 | 1.0000 |
| **IQM Pooled Exploratory** (`W1+W2+W3`) | `C2` (Placement Remap) vs `C1` (Stale) | 24 (23) | +2 (14 vs 12) | `+0.01562 ± 0.00773` | [-0.00038, +0.03162] | +2.0198 | 0.0552 | 0.1656 |
| **IQM Pooled Exploratory** (`W1+W2+W3`) | `C3` vs `C2` (Incremental Resynthesis) | 24 (23) | 0 (14 vs 14) | `+0.00073 ± 0.00415` | [-0.00786, +0.00932] | +0.1766 | 0.8614 | 0.8614 |
| **IQM Pooled Exploratory** (`W1+W2+W3`) | `C4` (`garnet` Switch) vs `C1` (Stale) | 24 (23) | -2 (10 vs 12) | **`-0.04463 ± 0.01045`** | **[-0.06625, -0.02300]** | -4.2690 | **0.0003** (favors `C1`) | **0.0012** |

---

## 5. Key Operational Takeaways & Cumulative Phase 3 Campaign Reconciliation

1. **Learning When Refreshing Adds No Value Prevents Unnecessary Recompilation (`IBM Heron`):**
   - Across `1.8d - 6.8d` calibrations on `ibm_kingston`, physical drift remained within the locked retention regime (`[REDACTED_PROPRIETARY_THRESHOLD]`). On Blind Holdout (`W2 + W3`), keeping cached `T0` placements (`C1_Keep_Original_Stale` / `C_Triggered_Refresh_Policy`) achieved **10/12 (83.33%) target attainment**, tying both `C2` (10/12) and full recompilation `C3` (10/12) while eliminating 100% of live resynthesis (75.6% total CPU reduction even including `C2`'s drift check, or 100% when `T0` cache is reused directly). None of the IBM refresh contrasts were statistically distinguishable from zero (`p >= 0.1267` unadjusted; 1.0000 / 0.7602 Holm-adjusted).

2. **Fast Isomorphic Placement Remapping (~0.17 s) Captures Virtually All Refresh Value Over Full Resynthesis (~2.95 s) on `IQM Crystal 54`:**
   - Where calibration drift was 3.7x larger (`emerald`), `C2_Update_Placement_Only` (0.17 s *(mean)* / 0.08 s *(median)* live CPU time, zero resynthesis) raised Blind Holdout acceptance from **5/12 (41.67%) to 7/12 (58.33%)** and mean F_B from 0.8485 to 0.8641 (+0.0156, replicated across both Dev +0.0157 and Holdout +0.0156; exploratory pooled `N = 24` unadjusted `p = 0.0552`).
   - Crucially, **`C3` vs `C2` (incremental value of full resynthesis beyond placement-only refresh) was +0.00026 on Holdout (`unadjusted p = 0.9470`, 7/12 vs 7/12) and +0.00073 across all 24 workloads (`unadjusted p = 0.8614`)**, despite `C3` spending **17.6x more live CPU time** (2.95 s vs 0.17 s *(mean)*). Meanwhile, `C_Triggered_Refresh_Policy` (`5 C1, 5 C2, 2 C3`) achieved the highest mean Holdout overlap (0.8658, `6/12 = 50.00%` accepted) and avoided switching to `garnet` (`C4`, which degraded overlap by -0.04463 pooled, Holm-adjusted `p = 0.0012`, and incurred a 45.0-minute queue wait in Window 1).

### Table 5A: Cumulative Phase 3 Allowance & Job Ledger Through Experiment 4 (54 Total Provider Job Records)

| Module & Category | Provider Job Records | Executed PUBs | Executed Physical Shots | IBM Settled `s` | IQM Settled `cr` | Classification |
| :--- | :---: | :---: | :---: | :---: | :---: | :--- |
| **Prior Completed Modules (`Stage 3.0` + `Exp 3` + `Exp 1` Scheduled)** | 38 (20 IBM + 18 IQM) | 776 | 598,528 | 150.00 s | 118.50 cr | 36 Benchmark + 2 Scheduled Warm-Ups |
| **Prior Troubleshooting Records Retained (`Exp 1` IQM)** | 4 (3 Completed + 1 Cancelled) | 3 | 1,536 | 0.00 s | 12.00 cr | Non-Benchmark Troubleshooting (Retained) |
| **Experiment 4 (`W1 Dev` + `W2 & W3 Holdout`)** | **12 (6 IBM + 6 IQM)** | **192** | **147,456** | **39.00 s** | **24.75 cr** | **12 Benchmark Jobs (0 Aux / 0 Cancelled)** |
| **CUMULATIVE CAMPAIGN TOTAL (`Against 379.00 s / 293.00 cr Ceilings`)** | **54 Total Records** (48 Benchmark + 2 Warm-Up + 4 Troubleshoot) | **971 PUBs** | **747,520 Shots** | **189.00 s** (**190.00 s rem.**) | **155.25 cr** (**137.75 cr rem.**) | Verified in [`phase3_allowance_reservation_ledger.json`](../data/phase3_allowance_reservation_ledger.json) |
