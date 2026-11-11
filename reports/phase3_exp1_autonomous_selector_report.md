# Phase 3 - Priority 2 (Experiment 1): Prospective Autonomous Policy Selector Benchmark (26 Benchmark Jobs + 2 Scheduled Warm-Ups = 28 Scheduled Slots; 4 Troubleshooting Records Retained Separately)

## 1. Executive Summary & Principal Comparative Benchmark Tables

This report documents the completed hardware execution of **Priority 2 - Experiment 1** from [`phase3_submission_manifest_v3.json`](../manifests/phase3_submission_manifest_v3.json) (`SHA-256 = 86968d1471af9228dd1902b0266569ac95b143e237027718cffaa383ab03cfba`, preserved unmodified alongside dated amendment log [`phase3_amendment_log_v3_1.json`](../manifests/phase3_amendment_log_v3_1.json), `SHA-256 = d2303277634adb9e1433d6f7a9e34f60d8cb7d6ad37ba1c6b5de1baeab08e692`; intermediate post-Exp 1 snapshot `1f368e96f1bad4d68d24b707c8471e0bc346beddcfdd4997fd3873a0c5e9cef3`):

- **Benchmark vs. Auxiliary/Troubleshooting Job Separation:**
  - **Benchmark Policy & Counterfactual Jobs (26 Standalone Jobs):** 24 competing standalone policy jobs (`A1..A6`: 12 IBM on `ibm_kingston` + 12 IQM on `emerald`) + 2 simultaneous alternate-QPU counterfactual benchmark jobs (`A7_AltQPU_Counterfactual_Fez` on `ibm_fez`), executing **486 benchmark PUBs** (247 IBM + 239 IQM) and **375,296 benchmark physical shots** (252,928 IBM + 122,368 IQM) for **100.00 s IBM** and **68.25 cr IQM**.
  - **Scheduled IQM Station Warm-Up Jobs (2 Jobs, Completing the 28 Scheduled Manifest Slots):** 1 1-PUB station warm-up ping per split (`01a1c1f9-e28c-738f-93e2-e35367d755c8` at 2.25 cr in Part A; `01a1c224-cea1-75ae-8ef9-aef08bc5925f` at 3.75 cr in Part B = 2 PUBs, 1,024 shots, **6.00 cr**) so Slot #1 wake-up overhead never biased any competing policy arm (`26 + 2 = 28` scheduled jobs, 488 PUBs, 376,320 shots, 100.00 s IBM + 74.25 cr IQM).
  - **Additional Non-Benchmark Troubleshooting / Cold-Wakeup Jobs Retained in Campaign Totals (4 IQM Job Records):** 1 server-cancelled zero-cost ping (`01a1c1e3-ecb6-715b-b8b4-b9f8aa611ad0`, 0.00 cr) + 3 completed 1-PUB pings prior to fixing the `iqm-client` `"completed"` status check (`01a1c1f7-41b3-76da-86ce-869e9311fc19` [7.50 cr], `01a1c1f7-8638-702d-b987-0b0a7a8264e3` [2.25 cr], `01a1c1f7-ae26-73ed-8243-3dcb131e9e80` [2.25 cr] = 3 PUBs, 1,536 shots, **12.00 cr** retained in campaign spend -> **86.25 cr all-inclusive IQM spend for Experiment 1**).
- **Strict Chronological Isolation Verified:**
  1. **Part A Development (`Cohort Dev-80`: `W_P3_Dev_045..068`, 24 workloads = 6 groups `G_Dev_01..06`):** Pre-execution choices frozen at `2026-11-09T19:56:27.711351+00:00` ([`phase3_exp1_part_a_preexecution_frozen_choices.json`](../data/phase3_exp1_part_a_preexecution_frozen_choices.json), `SHA-256 = 37db800770c66ad28bce94afc3a6be3c97cbc48197ace5eb3863e12deedd2376`), completed at `2026-11-09T20:50:35.115254+00:00` ([`phase3_exp1_part_a_dev_results.json`](../data/phase3_exp1_part_a_dev_results.json)).
  2. **Selector Lock (Zero Holdout Access):** Locked [`phase3_locked_policy_selector.json`](../data/phase3_locked_policy_selector.json) at `2026-11-09T20:53:30.752092+00:00` (`SHA-256 = 77bb8ac25c55af34993edcdd1335f76f64e65de157e478f5700bf40c22cdb14b`) prior to creating or compiling any blind-holdout workload.
  3. **Part B Blind Holdout (`Cohort Module-Holdout-60`: `W_P3_ModHold_001..036`, 36 untouched workloads = 9 groups `G_Hold_01..09`):** Pre-execution choices frozen at `2026-11-09T21:07:49+00:00` ([`phase3_exp1_part_b_preexecution_frozen_choices.json`](../data/phase3_exp1_part_b_preexecution_frozen_choices.json), `SHA-256 = 1dcd4f4e0d16721353ff5238eb2fa6ca707b11b6af808d687a87bf71c8a2794b`), completed at `2026-11-09T21:53:16.057249+00:00` ([`phase3_exp1_part_b_holdout_results.json`](../data/phase3_exp1_part_b_holdout_results.json) and [`phase3_exp1_complete_results.json`](../data/phase3_exp1_complete_results.json)).

> [!IMPORTANT]
> **Metric, Baseline & Statistical Scope Conventions:**
> - **Classical Bhattacharyya Distribution Overlap** is defined as F_B = Σ_x √(P_ideal(x) · P_meas(x)) (labeled `"State Fidelity"` in some Studio UI panels), and **Squared Hellinger Fidelity** is reported alongside it as F_H = F_B².
> - **Standalone Billed Efficiency vs. Within-Group Statistical Tests:** Every policy arm in Tables 1A, 1B, 2A, and 2B was submitted and billed as a single standalone provider job per split. Consequently, `Qualifying / Billed Unit` and its `% Delta` columns are **observed descriptive job-level billing ratios** (1 standalone bill per arm per split, with no inferential `p`-value claimed). Arrival-group paired `t`-tests (`K = 9, df = 8` in Table 3B) evaluate **within-group distribution overlap (ΔF_B) and within-group target attainment rate only**, and report both **unadjusted** and **Holm-Bonferroni (`m = 3`) adjusted** two-sided `p`-values.

### Table 1A: Part B Untouched Blind Holdout - IBM Quantum Standalone Benchmark Jobs (`N = 7` Benchmark Jobs: 6 Policy + 1 Alternate-QPU Counterfactual; 146 PUBs, 149,504 Physical Shots; 36 Workloads across `K = 9` Arrival Groups)

| Standalone Policy Arm | QPU & Slot | IBM Job ID | PUBs (↓) | Physical Shots (↓) | Standalone Billed `s` (↓) | Active Span `s` (↓) | Qualifying Accepted (↑) | Target Attainment `%` (↑) | Qualifying / Billed `s` (↑) | `% Delta` vs Dev-Selected & Best Fixed (`A4`) (↑) | F_B *(mean / median)* (↑) | F_H = F_B² *(mean / median)* (↑) | `TVD` *(mean / median)* (↓) | Shannon Entropy `bits` *(mean / median)* | `2Q` Gates *(mean)* (↓) | Compiled Depth *(mean)* (↓) |
| :--- | :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| `A1_SABRE_Isolated` (`k=1`) | `ibm_kingston` (#4) | `dbp3cs0qtr0c738dmth0` | 36 | 36,864 | 12.00 | 10.69 | 26 / 36 | 72.22% | 2.1667 | -59.88% | 0.9307 / 0.9528 | 0.8701 / 0.9079 | 0.2034 / 0.1834 | 3.9138 / 4.1228 | 68.06 | 122.58 |
| `A2_TKET_Isolated` (`k=1`) | `ibm_kingston` (#6) | `dbp3ct8ou4as73csv08g` | 36 | 36,864 | 12.00 | 10.72 | **30 / 36** | **83.33%** | 2.5000 | -53.70% | 0.9308 / 0.9486 | 0.8704 / 0.8999 | **0.2030** `/ 0.1776` | 3.9229 / 4.0385 | 66.94 | 120.50 |
| `A3_Prometheus_Isolated` (`k=1`) | `ibm_kingston` (#1) | `dbp3cpk4148s739tsqrg` | 36 | 36,864 | 12.00 | 10.65 | **30 / 36** | **83.33%** | 2.5000 | -53.70% | **0.9310** `/ 0.9564` | **0.8707** `/ 0.9147` | 0.2036 / **0.1731** | 3.9215 / 4.0810 | 65.69 | 110.69 |
| `A4_Fixed_4Way_Prometheus` (`k=4`, Dev-Selected & Best Fixed) | `ibm_kingston` (#3) | `dbp3crlt7fqc7388n6ig` | **9** | **9,216** | **5.00** | **3.38** | 27 / 36 | 75.00% | **5.4000** | **0.00%** | 0.9181 / 0.9458 | 0.8495 / 0.8946 | 0.2259 / 0.1787 | 4.0295 / 4.0795 | 65.64 | **110.64** |
| `A5_Best_Phase2_Fixed_4Way` (`TKET`, `k=4`) | `ibm_kingston` (#5) | `dbp3csoou4as73csv07g` | **9** | **9,216** | **5.00** | 3.41 | 26 / 36 | 72.22% | 5.2000 | -3.70% | 0.9175 / 0.9456 | 0.8482 / 0.8942 | 0.2273 / 0.1790 | 4.0184 / 4.0151 | 66.92 | 120.19 |
| **`A6_Prometheus_Autonomous_Selector` (Locked)** | `ibm_kingston` (#2) | `dbp3cqk4148s739tsqtg` | 11 | 11,264 | **5.00** | 3.96 | 26 / 36 | 72.22% | 5.2000 | -3.70% (0.00% vs `A5`) | 0.9212 / **0.9565** | 0.8547 / **0.9148** | 0.2178 / 0.1903 | 4.0013 / 4.1444 | 65.58 | 111.25 |
| `A7_AltQPU_Counterfactual_Fez` (`k=4`) | `ibm_fez` (#7) | `dbp3cu0qtr0c738dmtj0` | **9** | **9,216** | **5.00** | 3.65 | 23 / 36 | 63.89% | 4.6000 | -14.81% | 0.9014 / 0.9442 | 0.8227 / 0.8916 | 0.2559 / 0.2194 | 4.2831 / 4.3146 | **64.72** | 119.19 |

### Table 1B: Part B Untouched Blind Holdout - IQM Resonance Standalone Policy Benchmark Jobs (`N = 6` Policy Benchmark Jobs on `emerald`, 140 PUBs, 71,680 Physical Shots; Plus 1 Scheduled Station Warm-Up Job `01a1c224-cea1-75ae-8ef9-aef08bc5925f` [1 PUB, 512 Shots, 3.75 cr])

*(Baseline Distinction on IQM Holdout: `A4_Fixed_4Way_Prometheus` is the legitimate pre-registered **Development-Selected Fixed Baseline**. On Holdout, **`A3_Prometheus_Isolated`** achieved higher observed efficiency [2.9333 vs 2.8571 Acc/cr] and the highest overall attainment [`22/36 = 61.11%`] among fixed Prometheus arms, while **`A5_Best_Phase2_Fixed_4Way`** [`SABRE`, `k=4`] achieved the highest observed standalone efficiency overall [3.4667 Acc/cr] at the lowest attainment [`13/36 = 36.11%`]. Both `% Delta` columns are shown below.)*

| Standalone Policy Arm | QPU & Slot | IQM Job UUID | PUBs (↓) | Physical Shots (↓) | Standalone Billed `cr` (↓) | Active Span `s` (↓) | Qualifying Accepted (↑) | Target Attainment `%` (↑) | Qualifying / Billed `cr` (↑) | `% Delta` vs Dev-Selected Fixed (`A4`) (↑) | `% Delta` vs Best Observed Holdout Fixed (`A5`) (↑) | F_B *(mean / median)* (↑) | F_H = F_B² *(mean / median)* (↑) | `TVD` *(mean / median)* (↓) | Shannon Entropy `bits` *(mean / median)* | `2Q` Gates *(mean)* (↓) | Compiled Depth *(mean)* (↓) |
| :--- | :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| `A1_SABRE_Isolated` (`k=1`) | `emerald` (#3) | `01a1c226-92cb-75f1-b8d0-ca04ee587838` | 36 | 18,432 | 9.75 | 11.62 | 19 / 36 | 52.78% | 1.9487 | -31.79% | -43.79% | 0.8742 / **0.9354** | **0.7819** `/` **0.8749** | **0.2933** `/` **0.2344** | 4.2192 / 4.3335 | 52.39 | 89.33 |
| `A2_TKET_Isolated` (`k=1`) | `emerald` (#1) | `01a1c226-100a-7236-bd24-5b556884d154` | 36 | 18,432 | 6.75 | 8.45 | 18 / 36 | 50.00% | 2.6667 | -6.66% | -23.08% | 0.8388 / 0.8831 | 0.7258 / 0.7801 | 0.3505 / 0.3143 | 4.3764 / 4.3630 | 57.64 | 106.00 |
| `A3_Prometheus_Isolated` (`k=1`, Best Fixed Prom on Holdout) | `emerald` (#4) | `01a1c228-192a-7766-89ab-4029030d9910` | 36 | 18,432 | 7.50 | 8.40 | **22 / 36** | **61.11%** | 2.9333 | +2.67% | -15.39% | **0.8750** `/ 0.9280` | 0.7793 / 0.8612 | 0.2958 / 0.2389 | 4.2641 / 4.2864 | 49.75 | 82.03 |
| `A4_Fixed_4Way_Prometheus` (`k=4`, Dev-Selected Fixed) | `emerald` (#6) | `01a1c228-a338-76f1-8f97-142d6e42aaa3` | **9** | **4,608** | 5.25 | 5.81 | 15 / 36 | 41.67% | 2.8571 | 0.00% | -17.58% | 0.8274 / 0.8703 | 0.7045 / 0.7574 | 0.3757 / 0.3450 | 4.4783 / 4.4842 | 49.89 | 80.17 |
| `A5_Best_Phase2_Fixed_4Way` (`SABRE`, `k=4`, Highest Obs. Eff.) | `emerald` (#5) | `01a1c228-66c3-754e-b870-54e2e95abe5f` | **9** | **4,608** | **3.75** | **3.09** | 13 / 36 | 36.11% | **3.4667** | **+21.34%** | **0.00%** | 0.8105 / 0.8732 | 0.6822 / 0.7626 | 0.3928 / 0.3554 | 4.5528 / 4.5204 | 52.83 | 87.92 |
| **`A6_Prometheus_Autonomous_Selector` (Locked)** | `emerald` (#2) | `01a1c226-576a-7503-b4ac-4429067f194f` | 14 | 7,168 | 4.50 | 4.84 | 14 / 36 | 38.89% | 3.1111 | +8.89% (+6.06% vs `A3`) | -10.26% | 0.8436 / 0.8667 | 0.7272 / 0.7513 | 0.3571 / 0.3619 | 4.4325 / 4.4009 | **49.67** | **79.03** |

---

## 2. Part A Development Split Tables (`Cohort Dev-80`: `W_P3_Dev_045..068`)

### Table 2A: Part A Development - IBM Quantum Standalone Benchmark Jobs (`N = 7` Benchmark Jobs: 6 Policy + 1 Alternate-QPU Counterfactual; 101 PUBs, 103,424 Physical Shots; 24 Workloads across `K = 6` Arrival Groups)

| Standalone Policy Arm | QPU & Slot | IBM Job ID | PUBs (↓) | Physical Shots (↓) | Standalone Billed `s` (↓) | Active Span `s` (↓) | Qualifying Accepted (↑) | Target Attainment `%` (↑) | Qualifying / Billed `s` (↑) | `% Delta` vs Best Fixed (`A4`) (↑) | F_B *(mean / median)* (↑) | F_H = F_B² *(mean / median)* (↑) | `TVD` *(mean / median)* (↓) | Shannon Entropy `bits` *(mean / median)* | `2Q` Gates *(mean)* (↓) | Compiled Depth *(mean)* (↓) |
| :--- | :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| `A1_SABRE_Isolated` (`k=1`) | `ibm_kingston` (#2) | `dbp2bjs4148s739trihg` | 24 | 24,576 | 9.00 | 7.56 | **19 / 24** | **79.17%** | 2.1111 | -53.09% | 0.9231 / 0.9566 | 0.8589 / 0.9151 | 0.2177 / 0.1894 | 4.0101 / 4.1478 | 71.88 | 127.54 |
| `A2_TKET_Isolated` (`k=1`) | `ibm_kingston` (#4) | `dbp2bl8qtr0c738dll4g` | 24 | 24,576 | 9.00 | 7.47 | 18 / 24 | 75.00% | 2.0000 | -55.56% | **0.9303** `/ 0.9490` | **0.8704** `/ 0.9006` | **0.2046** `/` **0.1770** | 3.9470 / 3.7696 | 70.42 | 124.58 |
| `A3_Prometheus_Isolated` (`k=1`) | `ibm_kingston` (#5) | `dbp2bm0qtr0c738dll5g` | 24 | 24,576 | 9.00 | 7.40 | **19 / 24** | **79.17%** | 2.1111 | -53.09% | 0.9250 / **0.9584** | 0.8616 / **0.9186** | 0.2160 / 0.1792 | 4.0138 / 3.8338 | 69.42 | **116.88** |
| `A4_Fixed_4Way_Prometheus` (`k=4`) | `ibm_kingston` (#3) | `dbp2bkoqtr0c738dll20` | **6** | **6,144** | **4.00** | 4.64 | 18 / 24 | 75.00% | **4.5000** | **0.00%** | 0.9140 / 0.9405 | 0.8420 / 0.8846 | 0.2406 / 0.2155 | 4.1320 / 3.9354 | 69.42 | 116.96 |
| `A5_Best_Phase2_Fixed_4Way` (`TKET`, `k=4`) | `ibm_kingston` (#6) | `dbp2bmoqtr0c738dll70` | **6** | **6,144** | **4.00** | 2.80 | 18 / 24 | 75.00% | **4.5000** | **0.00%** | 0.9078 / 0.9391 | 0.8310 / 0.8819 | 0.2530 / 0.2485 | 4.1975 / 3.9680 | 70.33 | 124.42 |
| **`A6_Prometheus_Autonomous_Selector` (Dev v0)** | `ibm_kingston` (#1) | `dbp2bigou4as73csto30` | 11 | 11,264 | 5.00 | 4.08 | **19 / 24** | **79.17%** | 3.8000 | -15.56% | 0.9197 / 0.9450 | 0.8518 / 0.8930 | 0.2267 / 0.2066 | 4.0682 / 3.9041 | 69.25 | 117.67 |
| `A7_AltQPU_Counterfactual_Fez` (`k=4`) | `ibm_fez` (#7) | `dbp2bn8ou4as73csto9g` | **6** | **6,144** | **4.00** | **2.73** | 14 / 24 | 58.33% | 3.5000 | -22.22% | 0.8921 / 0.9464 | 0.8126 / 0.8957 | 0.2689 / 0.2150 | 4.3476 / 4.4032 | **67.88** | 125.96 |

### Table 2B: Part A Development - IQM Resonance Standalone Policy Benchmark Jobs (`N = 6` Policy Benchmark Jobs on `emerald`, 99 PUBs, 50,688 Physical Shots; Plus 1 Scheduled Station Warm-Up Job `01a1c1f9-e28c-738f-93e2-e35367d755c8` [1 PUB, 512 Shots, 2.25 cr]; 4 Troubleshooting Records Accounted Separately in Table 5A)

| Standalone Policy Arm | QPU & Slot | IQM Job UUID | PUBs (↓) | Physical Shots (↓) | Standalone Billed `cr` (↓) | Active Span `s` (↓) | Qualifying Accepted (↑) | Target Attainment `%` (↑) | Qualifying / Billed `cr` (↑) | `% Delta` vs Best Fixed (`A4`) (↑) | F_B *(mean / median)* (↑) | F_H = F_B² *(mean / median)* (↑) | `TVD` *(mean / median)* (↓) | Shannon Entropy `bits` *(mean / median)* | `2Q` Gates *(mean)* (↓) | Compiled Depth *(mean)* (↓) |
| :--- | :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| `A1_SABRE_Isolated` (`k=1`) | `emerald` (#2) | `01a1c1fa-432f-7474-9088-47bce8443a15` | 24 | 12,288 | 6.75 | 5.87 | 13 / 24 | 54.17% | 1.9259 | -34.34% | 0.8702 / **0.9337** | 0.7795 / **0.8718** | 0.2960 / 0.2377 | 4.2602 / 4.3329 | 55.46 | 94.29 |
| `A2_TKET_Isolated` (`k=1`) | `emerald` (#5) | `01a1c1fa-e6f3-75d5-871d-5dfefba972b6` | 24 | 12,288 | 5.25 | 5.89 | 11 / 24 | 45.83% | 2.0952 | -28.57% | 0.8339 / 0.9073 | 0.7222 / 0.8232 | 0.3579 / 0.3045 | 4.4422 / 4.2383 | 61.83 | 112.00 |
| `A3_Prometheus_Isolated` (`k=1`) | `emerald` (#1) | `01a1c1fa-0477-7247-83db-949ecce3326a` | 24 | 12,288 | 6.75 | 6.21 | **14 / 24** | **58.33%** | 2.0741 | -29.29% | **0.8804** `/ 0.9301` | **0.7893** `/ 0.8650` | **0.2895** `/` **0.2287** | 4.2418 / 4.0488 | **53.21** | 86.08 |
| `A4_Fixed_4Way_Prometheus` (`k=4`) | `emerald` (#3) | `01a1c1fa-8394-702a-8790-c16f84df3a68` | **6** | **3,072** | **3.75** | **2.59** | 11 / 24 | 45.83% | **2.9333** | **0.00%** | 0.8326 / 0.8896 | 0.7133 / 0.7914 | 0.3739 / 0.3454 | 4.5146 / 4.4203 | 54.08 | **85.75** |
| `A5_Best_Phase2_Fixed_4Way` (`SABRE`, `k=4`) | `emerald` (#4) | `01a1c1fa-b13a-74c5-8c25-674d7c0774a0` | **6** | **3,072** | **3.75** | 2.61 | 10 / 24 | 41.67% | 2.6667 | -9.09% | 0.8138 / 0.9010 | 0.6903 / 0.8119 | 0.3885 / 0.3133 | 4.5345 / 4.4888 | 57.00 | 94.88 |
| **`A6_Prometheus_Autonomous_Selector` (Dev v0)** | `emerald` (#6) | `01a1c1fb-1b7a-73f6-8047-8c6d8e660762` | 15 | 7,680 | 4.50 | 4.64 | 13 / 24 | 54.17% | 2.8889 | -1.51% (+8.33% vs `A5`) | 0.8527 / 0.8921 | 0.7479 / 0.7958 | 0.3359 / 0.2833 | 4.4247 / 4.3372 | 54.62 | 90.33 |

---

## 3. Post-Hoc Oracle Regret Upper Bounds & Arrival-Group Cluster Inference

> [!NOTE]
> **Exact Scope of `t`-Tests and `p`-Values in Table 3B:** All `t`-statistics and `p`-values in Table 3B test **within-group Classical Bhattacharyya Distribution Overlap (ΔF_B) and within-group target attainment rate (ΔAttainment)** across the `K = 9` four-workload arrival clusters (`df = 8`). They do **not** test the observed standalone billing-efficiency percentage differences (e.g., +8.89% vs `A4` on IQM), which are single-job descriptive ratios (1 standalone job bill per arm on Holdout). Both **unadjusted** two-sided `p`-values and **Holm-Bonferroni (`m = 3` distinct contrasts per provider) adjusted** two-sided `p`-values are reported explicitly.

### Table 3A: Post-Hoc Hindsight Oracle Regret Audit (`Part A Dev` vs. `Part B Blind Holdout`)

| Split & Provider | Post-Hoc Oracle Benchmark | Equivalent PUBs | Allocated Spend | Oracle Qualifying Accepted (↑) | Oracle Attainment `%` (↑) | Oracle Qualifying / Unit (↑) | Oracle F_B *(mean)* (↑) | `A6` Qualifying / Unit | `A6` Attainment of Oracle Efficiency `%` (↑) | `A6` Efficiency Shortfall `%` (↓) | `A6` Yield Shortfall `workloads` (↓) |
| :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **Part B Blind Holdout - IBM** (`N=36`, `K=9`) | `A_Oracle_MaxEfficiency` | 9 | 4.39 s | 27 / 36 | 75.00% | 6.1448 / s | 0.9196 | 5.2000 / s | **84.62%** | **15.38%** | 1 workload (26 vs 27) |
| **Part B Blind Holdout - IBM** (`N=36`, `K=9`) | `A_Oracle_MaxYield` | 18 | 6.93 s | 30 / 36 | 83.33% | 4.3294 / s | 0.9290 | 5.2000 / s | 120.11% (higher eff.) | 0.00% (beats Oracle eff.) | 4 workloads (26 vs 30) |
| **Part B Blind Holdout - IQM** (`N=36`, `K=9`) | `A_Oracle_MaxEfficiency` | 19 | 5.21 cr | 22 / 36 | 61.11% | 4.2192 / cr | 0.8543 | 3.1111 / cr | 73.74% | 26.26% | 8 workloads (14 vs 22) |
| **Part B Blind Holdout - IQM** (`N=36`, `K=9`) | `A_Oracle_MaxYield` | 22 | 5.63 cr | 23 / 36 | 63.89% | 4.0846 / cr | 0.8592 | 3.1111 / cr | 76.17% | 23.83% | 9 workloads (14 vs 23) |
| **Part A Development - IBM** (`N=24`, `K=6`) | `A_Oracle_MaxEfficiency` | 7 | 3.61 s | 19 / 24 | 79.17% | 5.2689 / s | 0.9170 | 3.8000 / s | 72.12% | 27.88% | **0 workloads** (19 vs 19) |
| **Part A Development - IQM** (`N=24`, `K=6`) | `A_Oracle_MaxEfficiency` | 13 | 3.83 cr | 13 / 24 | 54.17% | 3.3987 / cr | 0.8589 | 2.8889 / cr | **85.00%** | **15.00%** | **0 workloads** (13 vs 13) |

### Table 3B: Part B Blind Holdout Arrival-Group Paired Cluster Comparisons (`K = 9` Groups, `df = 8`; Unadjusted & Holm-Bonferroni `m = 3` Adjusted Two-Sided `p`-Values)

| Provider | Contrast (`A6` Minus Reference Baseline) | Clusters `K` (`df`) | Observed Standalone Efficiency Delta `%` *(Descriptive, No `p`-Value)* | Attainment Delta `pp` *(mean)* | Unadjusted Two-Sided `p` (ΔAttainment) | Group Paired ΔF_B *(mean ± SE)* (↑) | 95% Group `t`-Interval (ΔF_B) | Paired `t` (ΔF_B) | **Unadjusted** Two-Sided `p` (ΔF_B) | **Holm (`m=3`) Adjusted** Two-Sided `p` (ΔF_B) |
| :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **IBM Quantum** | `A6` vs `A4_Fixed_4Way_Prometheus` (Dev-Selected & Best Fixed) | 9 (8) | -3.70% | -2.78 pp | 0.3466 | `+0.00311 ± 0.00149` | [-0.00032, +0.00654] | +2.0921 | 0.0698 | 0.1396 |
| **IBM Quantum** | `A6` vs `A5_Best_Phase2_Fixed_4Way` (`TKET`, `k=4`) | 9 (8) | **0.00%** | **0.00 pp** | 1.0000 | `+0.00364 ± 0.00265` | [-0.00248, +0.00975] | +1.3719 | 0.2073 | 0.2073 |
| **IBM Quantum** | `A6` vs `A7_AltQPU_Counterfactual_Fez` (`ibm_fez`, `k=4`) | 9 (8) | **+13.04%** | **+8.33 pp** | 0.0805 | **`+0.01982 ± 0.00743`** | **[+0.00268, +0.03696]** | +2.6666 | **0.0285** | 0.0855 |
| **IQM Resonance** | `A6` vs `A4_Fixed_4Way_Prometheus` (Legitimate Dev-Selected Fixed) | 9 (8) | **+8.89%** | -2.78 pp | 0.5943 | **`+0.01615 ± 0.00620`** | **[+0.00185, +0.03044]** | +2.6050 | **0.0314** | 0.0627 |
| **IQM Resonance** | `A6` vs `A5_Best_Phase2_Fixed_4Way` (`SABRE`, `k=4`, Highest Obs. Eff.) | 9 (8) | -10.26% | **+2.78 pp** | 0.7287 | **`+0.03303 ± 0.00796`** | **[+0.01468, +0.05138]** | +4.1515 | **0.0032** | **0.0096** |
| **IQM Resonance** | `A6` vs `A3_Prometheus_Isolated` (`k=1`, Best Fixed Prom on Holdout) | 9 (8) | **+6.06%** | -22.22 pp | **0.0207** (favors `A3`) | `-0.03145 ± 0.01582` | [-0.06793, +0.00503] | -1.9882 | 0.0820 | 0.0820 |

> [!WARNING]
> **Audit Revision Erratum (`v1 -> v2` Recomputed Group Contrasts):** In the initial draft of Table 3B (`v1`), `A6 vs A7_AltQPU_Counterfactual_Fez` had not yet been emitted by `run_phase3_exp1_part_b_holdout.py` and used an approximate `SE = 0.00412` (`p = 0.0013`). Recomputing directly from the 9 arrival-group paired differences in [`phase3_exp1_part_b_holdout_results.json`](../data/phase3_exp1_part_b_holdout_results.json) yields exact `SE = 0.007433`, `t = +2.6666`, **unadjusted `p = 0.0285`**, and **Holm (`m = 3`) adjusted `p = 0.0855`**. Similarly, after Holm (`m = 3`) multiplicity adjustment across each provider's three distinct contrasts, `A6`'s overlap gain over `A4` on IQM has adjusted `p = 0.0627` (0.0314 unadjusted), whereas `A6`'s overlap gain over packed `SABRE` (`A5`) on IQM remains statistically significant at **adjusted `p = 0.0096`** (0.0032 unadjusted).

---

## 4. Technical & Operational Findings Across Both Splits (60 Workloads)

1. **IBM Quantum Holdout: 2.08x - 2.40x Observed Billed Efficiency Over Isolated Baselines, While Simpler Fixed Packing (`A4`) Remained Slightly Ahead:**
   - `A6_Prometheus_Autonomous_Selector` (5.2000 Acc/s, `26/36 = 72.22%`, `F_B = 0.9212`) delivered **2.40x** the observed standalone efficiency of isolated `SABRE` (2.1667 Acc/s) and **2.08x** that of isolated `TKET` and isolated `Prometheus` (2.5000 Acc/s), matched `A5_Best_Phase2_Fixed_4Way` (5.2000 Acc/s), and outperformed counterfactual `ibm_fez` (`A7`: 4.6000 Acc/s, `23/36 = 63.89%`; group overlap Δ F_B = +0.01982, unadjusted `p = 0.0285`, Holm `m=3` adjusted `p = 0.0855`).
   - Simpler fixed four-way `Prometheus` packing (`A4`: 5.4000 Acc/s, `27/36 = 75.00%`) remained +3.85% (1 accepted workload) ahead of `A6` in observed standalone efficiency, while `A6` had +0.00311 higher group-mean F_B (unadjusted `p = 0.0698`).

2. **IQM Resonance Holdout: Distinct Operating Regimes for Cost Efficiency vs. Quality-Target Attainment:**
   - **Vs. Development-Selected Fixed Baseline (`A4`):** `A4_Fixed_4Way_Prometheus` (2.8571 Acc/cr, `15/36 = 41.67%`) was the legitimate pre-registered baseline selected from Part A Dev (2.9333 Acc/cr). On Holdout, `A6` (3.1111 Acc/cr, `14/36 = 38.89%`) achieved an **observed +8.89% standalone billing-efficiency improvement** over `A4`, alongside a within-group overlap gain of Δ F_B = +0.01615 (unadjusted `p = 0.0314`, Holm `m=3` adjusted `p = 0.0627`).
   - **Vs. Isolated `Prometheus` (`A3`) and Packed `SABRE` (`A5`):** Among fixed `Prometheus` arms on IQM Holdout, **`A3_Prometheus_Isolated` (2.9333 Acc/cr, `22/36 = 61.11%`) exceeded `A4` (2.8571 Acc/cr) in observed efficiency and achieved the highest target attainment of any arm (+22.22 pp above `A6`, unadjusted `p = 0.0207`)**, demonstrating that full `k = 1` isolation remains substantially superior on Crystal 54 when target attainment takes priority over packing density. Conversely, **`A5_Best_Phase2_Fixed_4Way` (`SABRE + k=4`: 3.4667 Acc/cr, `13/36 = 36.11%`, 3.75 cr) achieved the highest observed IQM standalone efficiency overall**, at the cost of the lowest attainment (36.11%) and significantly lower distribution overlap than `A6` (Δ F_B = +0.03303 favoring `A6`, unadjusted `p = 0.0032`, Holm `m=3` adjusted `p = 0.0096`).

---

## 5. Job & Spend Reconciliation: Benchmark Jobs vs. Scheduled Warm-Ups vs. Retained Troubleshooting Jobs

### Table 5A: Complete Phase 3 Job & Expenditure Reconciliation Through Experiment 1 (42 Total Provider Job Records)

| Campaign Module & Job Category | Provider Job Records | Executed PUBs | Executed Physical Shots | IBM Settled `s` | IQM Settled `cr` | Audit Classification |
| :--- | :---: | :---: | :---: | :---: | :---: | :--- |
| **Stage 3.0: Measured-Cost Pilot** (`dbov9rk4148s739tmah0`, `01a1c124-caab-777b-a398-113197810942`) | 2 (1 IBM + 1 IQM) | 16 | 12,288 | 4.00 s | 6.00 cr | Scheduled Pilot Benchmark |
| **Experiment 3: Packing Calibration** (4 IBM + 4 IQM) | 8 (4 IBM + 4 IQM) | 272 | 209,920 | 46.00 s | 38.25 cr | Scheduled Calibration Benchmark |
| **Experiment 1: Competing Policy Benchmark Arms (`A1..A6`)** (12 IBM + 12 IQM) | 24 (12 IBM + 12 IQM) | 471 (232 IBM + 239 IQM) | 359,936 | 91.00 s | 68.25 cr | Primary Standalone Policy Benchmark |
| **Experiment 1: Alternate-QPU Counterfactual (`A7_AltQPU_Fez`)** (`dbp2bn8ou4as73csto9g`, `dbp3cu0qtr0c738dmtj0`) | 2 (2 IBM) | 15 | 15,360 | 9.00 s | 0.00 cr | Counterfactual Benchmark (`24 + 2 = 26` Exp 1 Benchmark Jobs) |
| **Experiment 1: Scheduled IQM Station Warm-Up Pings (`A0`)** (`01a1c1f9...`, `01a1c224...`) | 2 (2 IQM) | 2 | 1,024 | 0.00 s | 6.00 cr | Scheduled Warm-Up Slots (`26 + 2 = 28` Exp 1 Manifest Slots) |
| **SUBTOTAL: SCHEDULED MANIFEST JOBS (`Stage 3.0` + `Exp 3` + `Exp 1`)** | **38 Scheduled Jobs** (36 Benchmark + 2 Warm-Up) | **776 PUBs** | **598,528 Shots** | **150.00 s** | **118.50 cr** | Matches `2 + 8 + 28 = 38` Scheduled Manifest Slots |
| **Experiment 1: Additional Non-Benchmark IQM Troubleshooting / Wakeup Records** (`01a1c1e3...` cancelled 0.00 cr; `01a1c1f7-41b3...` 7.50 cr; `01a1c1f7-8638...` 2.25 cr; `01a1c1f7-ae26...` 2.25 cr) | 4 IQM Records (3 Completed + 1 Cancelled) | 3 | 1,536 | 0.00 s | **12.00 cr** | Non-Benchmark Troubleshooting (Retained in Full in Campaign Spend) |
| **CUMULATIVE CAMPAIGN LEDGER TOTAL (`Against 379.00 s / 293.00 cr Ceilings`)** | **42 Total Records** | **779 PUBs** | **600,064 Shots** | **150.00 s** (229.00 s rem.) | **130.50 cr** (162.50 cr rem.) | Verified in [`phase3_allowance_reservation_ledger.json`](../data/phase3_allowance_reservation_ledger.json) |

---

## 6. Dated Pre-Submission Amendment Record for Priority 3 - Experiment 4 ([`phase3_amendment_log_v3_1.json`](../manifests/phase3_amendment_log_v3_1.json))

- **Original Manifest Preserved Unmodified:** [`manifests/phase3_submission_manifest_v3.json`](../manifests/phase3_submission_manifest_v3.json) (`File SHA-256 = 86968d1471af9228dd1902b0266569ac95b143e237027718cffaa383ab03cfba`).
- **Dated Amendment Log (`SHA-256 = d2303277634adb9e1433d6f7a9e34f60d8cb7d6ad37ba1c6b5de1baeab08e692`; post-Exp 1 snapshot `1f368e96f1bad4d68d24b707c8471e0bc346beddcfdd4997fd3873a0c5e9cef3`):**
  - **`AMEND-P3-001`:** Formalizes the separation of the 26 Experiment 1 benchmark jobs, 2 scheduled IQM warm-up pings (6.00 cr), and 4 non-benchmark IQM wakeup/troubleshooting records (12.00 cr retained in total spend).
  - **`AMEND-P3-002` (Recorded Prior to Any Experiment 4 Submission):** Preserves **all four original frozen Experiment 4 hardware arms** from [`prometheus_phase3_autonomous_policy_and_service_plan.md`](../PUBLIC_DISCLOSURE.md) (12 benchmark jobs = 6 IBM + 6 IQM across 3 temporal windows; 192 PUBs = 96 IBM + 96 IQM; `W_P3_Dev_069..080` in Window 1 Dev and `W_P3_ModHold_037..048` in Windows 2 & 3 Blind Holdout, all widths in `{5Q, 7Q, 9Q} <= 20Q`):
    1. **`C1_Keep_Original_Stale`** (primary QPU `ibm_kingston` / `emerald`: executes frozen `T0` compilation & subgraph placement without refresh),
    2. **`C2_Update_Placement_Only`** (primary QPU `ibm_kingston` / `emerald`: lightweight subgraph placement update onto live `T_now` calibration without full circuit resynthesis),
    3. **`C3_Recompile_Fresh_Same_QPU`** (primary QPU `ibm_kingston` / `emerald`: full fresh compilation on live `T_now` primary QPU telemetry),
    4. **`C4_Switch_QPU_Live`** (separate standalone job on alternate QPU `ibm_fez` on IBM and `garnet` `20Q` on IQM),
    while evaluating **`C_Triggered_Refresh_Policy`** as the pre-execution prospective selector overlay calibrated on Window 1 (`data/phase3_locked_refresh_policy.json`) and frozen prior to submitting Windows 2 and 3 (`data/phase3_exp4_holdout_preexecution_frozen_choices.json`). Any 1-PUB IQM station wake-up ping on `emerald` or `garnet` will be logged in the separate auxiliary ledger with full cost retained.
