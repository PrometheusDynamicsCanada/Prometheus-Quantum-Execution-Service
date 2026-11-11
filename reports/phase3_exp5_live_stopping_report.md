# Prometheus Phase 3 - Priority 4 (Experiment 5): Sequentially Valid Live Stopping at Application Target Report

**Campaign Module:** Priority 4 - Experiment 5 (`S1_Fixed_2048`, `S2_Adaptive_1x`, `S3_Adaptive_4x` + Independent 1,024-Shot Confirmation Jobs)  
**Pre-Registered Manifest:** [`phase3_submission_manifest_v3.json`](../manifests/phase3_submission_manifest_v3.json) (`File SHA-256 = 86968d1471af9228dd1902b0266569ac95b143e237027718cffaa383ab03cfba`, `Canonical Payload SHA-256 = 3dcdd9ff4d36ecc12cb76ec1409090f43554829d35ff43a7e4d666cd815d8c0f`)  
**Pre-Submission Amendment Log:** [`phase3_amendment_log_v3_1.json`](../manifests/phase3_amendment_log_v3_1.json) (`SHA-256 = d2303277634adb9e1433d6f7a9e34f60d8cb7d6ad37ba1c6b5de1baeab08e692`)  
**Pre-Execution Frozen Stopping Plan:** [`phase3_exp5_preexecution_frozen_plan.json`](../data/phase3_exp5_preexecution_frozen_plan.json) (`SHA-256 = 236d28a63a879c421fd3f6e79dcb08577fbd4004b394453643414673cd0dc584`, frozen `2026-11-10T03:15:07.093114+00:00` prior to any Experiment 5 QPU submission)  
**Complete Hardware Results Ledger:** [`phase3_exp5_live_stopping_results.json`](../data/phase3_exp5_live_stopping_results.json) (`SHA-256 = 5174359a652e8efd7b1d4af221a303120ef48dfdd54eec337c6c32aec7912b07`, completed `2026-11-10T06:07:17.641105+00:00`)  
**Cohort Isolation Audit:** Executed strictly on `Cohort_Module_Holdout_60` (`W_P3_ModHold_049..060`, 12 disjoint application workloads across 4 families x 3 widths `5Q, 7Q, 9Q`). `Cohort_Customer_Queue_Holdout_72` (`W_P3_CustHold_001..072`) remains **100% untouched** for Priority 5 (Experiment 2).

---

## 1. Executive Summary & Principal Comparative Benchmark Tables

Experiment 5 prospectively tested whether Prometheus can halt QPU measurement in real time once an application workload is certified to meet both its distribution-overlap threshold (τ_FB) and its domain observable threshold (τ_obs) under the frozen whole-shot **`Bootstrap-SE Normal Bound`** (`evaluate_bitstring_stopping_rung`, B = 2,000 whole-shot resamples per rung, z_0.9875 = 2.2414027, α_step = 0.0125 across R = 4 rungs [256, 512, 1024, 2048] physical shots). For `S3_Adaptive_4x` (4x simultaneous buffered spatial replicas at pairwise graph distance d_G >= 2), each whole-shot bootstrap resample draws physical shots t in {1, ..., S_r} and aggregates all 4 replicas jointly via `w_boot @ shot_counts_matrix`, preserving any within-shot cross-replica correlation before computing SE_boot.

Every arm was audited against an **independent 1,024-shot hardware confirmation job** (`S1_Confirm_1024`, `S2_Confirm_1024`, `S3_Confirm_1024`) to measure the **Empirical Unconfirmed False-Stop Rate** against the pre-registered `<= 5.0%` sequential validity criterion.

* **Sample Sizes & Hardware Totals:**
  * **Application Workloads (`N = 12` per provider):** `W_P3_ModHold_049..060` (4 families: `QAOA_MaxCut`, `QFT_Phase`, `Trotter_Ising`, `VQE_Heisenberg` x 3 widths: `5Q`, `7Q`, `9Q`).
  * **IBM Quantum (`ibm_kingston`, `156Q` Heron r2):** **12 benchmark jobs** (0 auxiliary jobs), **94 executed PUBs**, **88,576 physical shots**, **38.9734 s active QPU execution time**, **49.00 s settled billed runtime** across three load-balanced accounts (`Account_A`, `Account_B`, `Account_C`).
  * **IQM Resonance (`emerald`, `54Q` Crystal 54):** **12 benchmark jobs** (119 benchmark PUBs, 121,344 benchmark physical shots, 51.9492 s active QPU execution time, 57.00 settled benchmark credits under live calibration set `e50d4bbf-a3d6-4786-8760-cf36945e6388`) + **1 scheduled 1-PUB station warm-up job** (`01a1c374-7d58-746c-97f3-98cbd7a37f70`, 1 PUB, 256 physical shots, 0.75 credits) = **13 total provider jobs, 120 total PUBs, 121,600 total physical shots, 57.75 total settled credits**.
  * **Combined Experiment 5 Totals:** **24 benchmark QPU jobs** (`+ 1` IQM warm-up job = 25 provider jobs), **213 benchmark PUBs** (214 total PUBs), and **209,920 benchmark physical shots** (210,176 total physical shots).

> [!IMPORTANT]
> **Metric & Statistical Reporting Conventions (`GEMINI.md` Compliance):**
> 1. **Classical Bhattacharyya Distribution Overlap (F_B)** is defined as F_B = Σ_x √(P_ideal(x) · P_meas(x)) (`(↑)` higher is better), and **Squared Hellinger Fidelity (F_H)** is F_H = F_B² (`(↑)` higher is better). Note that Studio UI screenshots label F_B as `"State Fidelity"`.
> 2. **Total Variation Distance (`TVD`)** is TVD = 0.5 · Σ_x |P_ideal(x) - P_meas(x)| (`(↓)` lower is better), and **Shannon Entropy (H)** is H = -Σ_x P_meas(x) · log2 P_meas(x) in bits.
> 3. **Two Acceptance Accounting Definitions Reported Side-by-Side:**
>    * **LCB-Certified Halted & Confirmed Yield (`LCB-Halt & Confirm`):** Workloads where the Stage-1 sequential lower/upper confidence bound triggered `halt == True` (`lcb_fb_9875 >= tau_fb` and `bound_obs_9875` passing `tau_obs`) **AND** the independent 1,024-shot confirmation job passed both point thresholds (`point_accepted == 1`).
>    * **Dual-Point Confirmed Yield (`Stage-1 Point & Confirm`):** Workloads where the Stage-1 terminal point estimate passed both thresholds **AND** the independent 1,024-shot confirmation job passed both point thresholds (`confirmed_point_accepted`).
> 4. **Descriptive Standalone Billing Efficiency vs. Workload Overlap Inference:** Because each rung or batch is billed at the **job level** (1 Stage-1 job for `S1` vs 4 sequential Stage-1 rung jobs for `S2` and `S3`), `Confirmed Accepted / Billed Unit` values are reported as **observed descriptive ratios** across the executed jobs. Paired `t`-tests (`N = 12` workloads, `df = 11`) test differences in per-workload distribution overlap (F_B), with both **unadjusted two-sided `p`-values** and **Holm step-down adjusted `p`-values (`m = 3` pairwise comparisons per provider)** reported explicitly.

### Table 1A: IBM Quantum (`ibm_kingston`) Principal Comparative Benchmark (`N = 12` Unseen Application Workloads `W_P3_ModHold_049..060`, 12 Benchmark Jobs, 94 PUBs, 88,576 Physical Shots)

| Metric / Observable | `S1_Fixed_2048` (1x Fixed 2,048 Shots) | `S2_Adaptive_1x` (1x Sequential Rungs 256..2048) | `S3_Adaptive_4x` (4x Buffered d_G >= 2 Rungs 256..2048) | Winner / Operational Interpretation |
| :--- | :---: | :---: | :---: | :--- |
| **Stage-1 QPU Job IDs (`ibm_kingston`)** | `dbpaqok4148s739u9hf0` (1 job) | `dbpb87gou4as73ctc850`, `dbpb8es4148s739uafng`, `dbpb8ltt7fqc73894mb0`, `dbpb8t5t7fqc73894ms0` (4 jobs) | `dbpb8ac4148s739uafcg`, `dbpb8j5t7fqc73894m1g`, `dbpb8qdt7fqc73894ml0`, `dbpb92oou4as73ctc9t0` (4 jobs) | All 9 Stage-1 jobs completed (0 errors) |
| **Confirm-1024 QPU Job ID (`ibm_kingston`)** | `dbpb1ult7fqc738947vg` (1 job) | `dbpb60gou4as73ctc3cg` (1 job) | `dbpb82s4148s739uaesg` (1 job) | Independent 1,024-shot confirmation per arm |
| **Stage-1 Executed PUBs `(↓)`** | **12** | 29 | 17 | `S3` cut adaptive PUBs by **-41.38%** vs `S2` (-64.58% vs 48 max) |
| **Total Executed PUBs (`Stage-1 + Confirm`) `(↓)`** | **24** | 41 | 29 | `S1` fewest total PUBs; `S3` **-29.27%** vs `S2` |
| **Stage-1 Physical Shots Used `(↓)`** | 24,576 (2,048.0 *(mean)* / 2,048.0 *(median)*) | 19,456 (1,621.3 *(mean)* / 1,280.0 *(median)*) | **7,680 (640.0 *(mean)* / 256.0 *(median)*)** | **`S3_Adaptive_4x` (-68.75% vs `S1`; -83.33% vs 46,080 max)** |
| **Total Physical Shots (`Stage-1 + Confirm`) `(↓)`** | 36,864 | 31,744 | **19,968** | **`S3_Adaptive_4x` (-45.83% vs `S1`; -37.10% vs `S2`)** |
| **Early Halts Before 2,048 Shots (`Rungs 1..3`) `(↑)`** | 0 / 12 (0.0%) | 9 / 12 (75.0%) | **11 / 12 (91.7%)** | **`S3_Adaptive_4x` (9 at Rung 1 [256], 2 at Rung 2 [512])** |
| **LCB-Certified Halts (`Rungs 1..4` / `@ 2,048`) `(↑)`** | 8 / 12 (66.7%) | 10 / 12 (83.3%) | **11 / 12 (91.7%)** | **`S3_Adaptive_4x` (+3 certified over `S1`, +1 over `S2`)** |
| **Unconfirmed False Stops (`Matched Confirm-1024`) `(↓)`** | **0 / 8 (0.0%)** | **0 / 10 (0.0%)** | **0 / 11 (0.0%)** | **All arms `0.0% <= 5.0%` sequential validity bound** |
| **Unconfirmed False Stops (`Cross 1x Confirm-1024`) `(↓)`** | **0 / 8 (0.0%)** | **0 / 10 (0.0%)** | 1 / 11 (9.09%, `W_P3_ModHold_051`) | `W_P3_ModHold_051` passes 4x (`0.0457 <= 0.08`), misses 1x (0.0825) |
| **LCB-Certified & Confirmed Yield `(↑)`** | 8 / 12 (66.7%) | 10 / 12 (83.3%) | **11 / 12 (91.7%)** | **`S3_Adaptive_4x` (11/12 LCB-halted & confirmed)** |
| **Dual-Point Confirmed Yield (`Stage-1 & Confirm`) `(↑)`** | 9 / 12 (75.0%) | 10 / 12 (83.3%) | **11 / 12 (91.7%)** | **`S3_Adaptive_4x` (+2 confirmed workloads over `S1`)** |
| **Stage-1 Active QPU Execution Time `(↓)`** | 11.7692 s | 8.8236 s | **5.8106 s** | **`S3_Adaptive_4x` (-50.63% active QPU time vs `S1`)** |
| **Total Active QPU Execution Time (`+ Confirm`) `(↓)`** | 15.9150 s | 13.0071 s | **10.0513 s** | **`S3_Adaptive_4x` (-36.84% active QPU time vs `S1`)** |
| **Stage-1 Standalone Billed Runtime (`s`) `(↓)`** | **9.00 s** | 14.00 s | 11.00 s | **`S1_Fixed_2048`** (1 job vs 4 standalone rung jobs with min floor) |
| **Total Standalone Billed Runtime (`+ Confirm`) `(↓)`** | **14.00 s** | 19.00 s | 16.00 s | **`S1_Fixed_2048`** (2 jobs vs 5 jobs) |
| **Stage-1 Billed Efficiency (`Conf. Acc / Stage-1 s`, obs. ratio) `(↑)`** | **1.0000 Acc/s** (9 / 9.00 s) | 0.7143 Acc/s (10 / 14.00 s) | **1.0000 Acc/s** (11 / 11.00 s) | **Tie: `S3_Adaptive_4x` & `S1_Fixed_2048` (1.0000 Acc/s)** |
| **Total Billed Efficiency (`Conf. Acc / Total s`, obs. ratio) `(↑)`** | 0.6429 Acc/s (9 / 14.00 s) | 0.5263 Acc/s (10 / 19.00 s) | **0.6875 Acc/s** (11 / 16.00 s) | **`S3_Adaptive_4x` (+6.94% vs `S1`, +30.63% vs `S2`)** |
| **Stage-1 Overlap F_B *(mean / median)* `(↑)`** | 0.9226 / 0.9659 | 0.9126 / 0.9659 | **0.9355 / 0.9686** | **`S3_Adaptive_4x` (+0.0129 vs `S1`, +0.0229 vs `S2`)** |
| **Stage-1 Squared Hellinger F_H = F_B² *(mean / median)* `(↑)`** | 0.8616 / 0.9329 | 0.8408 / 0.9330 | **0.8799 / 0.9382** | **`S3_Adaptive_4x` (+0.0183 vs `S1`, +0.0391 vs `S2`)** |
| **Stage-1 Total Variation Distance `TVD` *(mean / median)* `(↓)`** | 0.2109 / 0.1711 | 0.2238 / 0.1676 | **0.1836 / 0.1497** | **`S3_Adaptive_4x` (-0.0273 vs `S1`, -0.0402 vs `S2`)** |
| **Stage-1 Shannon Entropy H (bits) *(mean / median)* `(↓)`** | 3.4922 / 3.1428 | 3.3446 / **2.9892** | **3.3412** / 3.0820 | **`S3_Adaptive_4x` *(mean)*** / **`S2_Adaptive_1x` *(median)*** |
| **Confirm-1024 Overlap F_B *(mean / median)* `(↑)`** | 0.9154 / 0.9545 | 0.9146 / 0.9614 | **0.9423 / 0.9770** | **`S3_Adaptive_4x` (+0.0269 vs `S1`, +0.0277 vs `S2`)** |
| **Confirm-1024 Squared Hellinger F_H = F_B² *(mean / median)* `(↑)`** | 0.8476 / 0.9112 | 0.8471 / 0.9242 | **0.8927 / 0.9546** | **`S3_Adaptive_4x` (+0.0451 vs `S1`, +0.0456 vs `S2`)** |
| **Confirm-1024 `TVD` *(mean / median)* `(↓)`** | 0.2262 / 0.1954 | 0.2283 / 0.1715 | **0.1725 / 0.1413** | **`S3_Adaptive_4x` (-0.0537 vs `S1`, -0.0558 vs `S2`)** |
| **Confirm-1024 Shannon Entropy H (bits) *(mean / median)* `(↓)`** | 3.4902 / **3.0850** | 3.5150 / 3.0953 | **3.4035** / 3.1474 | **`S3_Adaptive_4x` *(mean)*** / **`S1_Fixed_2048` *(median)*** |
| **Compiled Two-Qubit Gates per Replica *(mean / median)* `(↓)`** | **48.83** / 33.00 | **48.83** / 33.00 | 48.85 / **32.25** (195.42 total 4x) | Virtually identical per-replica 2Q gate count (~48.84) |
| **Compiled Circuit Depth *(mean / median)* `(↓)`** | **103.83 / 70.00** | **103.83 / 70.00** | 107.50 / **70.00** | **`S1`/`S2` *(mean)*** (103.83 vs 107.50), tied *(median)* (70.00) |

---

### Table 1B: IQM Resonance (`emerald`) Principal Comparative Benchmark (`N = 12` Unseen Application Workloads `W_P3_ModHold_049..060`, 12 Benchmark Jobs + 1 Aux Station Warm-Up Job, 120 Total PUBs, 121,600 Total Physical Shots)

| Metric / Observable | `S1_Fixed_2048` (1x Fixed 2,048 Shots) | `S2_Adaptive_1x` (1x Sequential Rungs 256..2048) | `S3_Adaptive_4x` (4x Buffered d_G >= 2 Rungs 256..2048) | Winner / Operational Interpretation |
| :--- | :---: | :---: | :---: | :--- |
| **Stage-1 QPU Job IDs (`emerald`)** | `01a1c3f1-c625-7581-a572-7845ea90e304` (1 job) | `01a1c3f3-191b-726f-8292-8c8d3a16c2c5`, `01a1c3f3-9170-718f-a152-cd373afaaa42`, `01a1c3f4-1825-7439-ab33-1c913921cce1`, `01a1c3f4-a500-7168-8d9b-84ba83cb30d2` (4 jobs) | `01a1c3f3-4e5d-7472-a85b-0e8ea6bc35d1`, `01a1c3f3-c34c-77a8-81f3-42b1d0477333`, `01a1c3f4-4bb5-7448-a667-b99be4d09893`, `01a1c3f4-e5a2-77cd-b68c-4c1ca47d5f13` (4 jobs) | All 9 Stage-1 jobs completed on calibration set `e50d4bbf-...` |
| **Confirm-1024 QPU Job ID (`emerald`)** | `01a1c3f2-1c9e-7175-8324-098eafb53ae8` (1 job) | `01a1c3f2-8757-73ac-bac9-5653bbdafc54` (1 job) | `01a1c3f2-cea4-763b-bcd3-14155f77bc6b` (1 job) | Plus 1 station warm-up `01a1c374-7d58-746c-97f3-98cbd7a37f70` (0.75 cr) |
| **Stage-1 Executed PUBs `(↓)`** | **12** | 39 | 32 | **`S1_Fixed_2048`** (`S3` saved -33.33% vs 48 no-early-stop max) |
| **Total Executed PUBs (`Stage-1 + Confirm`) `(↓)`** | **24** | 51 | 44 | **`S1_Fixed_2048`** (24 PUBs); `S3` used 7 fewer PUBs than `S2` |
| **Stage-1 Physical Shots Used `(↓)`** | **24,576 (2,048.0 *(mean)* / 2,048.0 *(median)*)** | 33,792 (2,816.0 *(mean)* / 3,840.0 *(median)*) | 26,112 (2,176.0 *(mean)* / 2,816.0 *(median)*) | **`S1_Fixed_2048`** (`S3` -43.33% vs 46,080 max, +6.25% vs `S1`) |
| **Total Physical Shots (`Stage-1 + Confirm`) `(↓)`** | **36,864** | 46,080 | 38,400 | **`S1_Fixed_2048`** (6 noisy circuits ran all 4 rungs on `S3`) |
| **Early Halts Before 2,048 Shots (`Rungs 1..3`) `(↑)`** | 0 / 12 (0.0%) | 4 / 12 (33.3%) | **6 / 12 (50.0%)** | **`S3_Adaptive_4x` (5 at Rung 1 [256], 1 at Rung 3 [1,024])** |
| **LCB-Certified Halts (`Rungs 1..4` / `@ 2,048`) `(↑)`** | 5 / 12 (41.7%) | 4 / 12 (33.3%) | **6 / 12 (50.0%)** | **`S3_Adaptive_4x` (6/12 certified vs 5/12 `S1`, 4/12 `S2`)** |
| **Unconfirmed False Stops (`Matched Confirm-1024`) `(↓)`** | **0 / 5 (0.0%)** | **0 / 4 (0.0%)** | **0 / 6 (0.0%)** | **All arms `0.0% <= 5.0%` sequential validity bound** |
| **Unconfirmed False Stops (`Cross 1x Confirm-1024`) `(↓)`** | **0 / 5 (0.0%)** | **0 / 4 (0.0%)** | **0 / 6 (0.0%)** | **0.0% false stops under both 4x and 1x confirmations** |
| **LCB-Certified & Confirmed Yield `(↑)`** | 5 / 12 (41.7%) | 4 / 12 (33.3%) | **6 / 12 (50.0%)** | **`S3_Adaptive_4x` (6/12 LCB-certified & confirmed)** |
| **Dual-Point Confirmed Yield (`Stage-1 & Confirm`) `(↑)`** | **6 / 12 (50.0%)** | **6 / 12 (50.0%)** | **6 / 12 (50.0%)** | **Three-way tie (6/12 confirmed workloads across all arms)** |
| **Stage-1 Active QPU Execution Time `(↓)`** | 10.7201 s | 15.9546 s | **9.5539 s** | **`S3_Adaptive_4x` (-10.88% vs `S1`, -40.12% vs `S2`)** |
| **Total Active QPU Execution Time (`+ Confirm`) `(↓)`** | 15.1957 s | 22.3608 s | **14.3927 s** | **`S3_Adaptive_4x` (-5.28% vs `S1`, -35.63% vs `S2`)** |
| **Stage-1 Standalone Billed Credits (`cr`) `(↓)`** | **10.50 cr** | 16.50 cr | 12.00 cr | **`S1_Fixed_2048`** (1 job vs 4 standalone rung jobs) |
| **Total Standalone Billed Credits (`+ Confirm`) `(↓)`** | **15.75 cr** | 23.25 cr | 18.00 cr | **`S1_Fixed_2048`** (15.75 cr vs 18.00 cr `S3`, 23.25 cr `S2`) |
| **Stage-1 Billed Efficiency (`Conf. Acc / Stage-1 cr`, obs. ratio) `(↑)`** | **0.5714 Acc/cr** (6 / 10.50 cr) | 0.3636 Acc/cr (6 / 16.50 cr) | 0.5000 Acc/cr (6 / 12.00 cr) | **`S1_Fixed_2048`** (multi-rung overhead on 6 unhalted circuits) |
| **Total Billed Efficiency (`Conf. Acc / Total cr`, obs. ratio) `(↑)`** | **0.3810 Acc/cr** (6 / 15.75 cr) | 0.2581 Acc/cr (6 / 23.25 cr) | 0.3333 Acc/cr (6 / 18.00 cr) | **`S1_Fixed_2048`** (motivates single-pass + selective escalation) |
| **Stage-1 Overlap F_B *(mean / median)* `(↑)`** | 0.8766 / **0.9217** | **0.8792** / 0.9175 | 0.8619 / 0.9160 | **`S2_Adaptive_1x` *(mean)*** / **`S1_Fixed_2048` *(median)*** |
| **Stage-1 Squared Hellinger F_H = F_B² *(mean / median)* `(↑)`** | 0.7890 / **0.8499** | **0.7907** / 0.8420 | 0.7686 / 0.8394 | **`S2_Adaptive_1x` *(mean)*** / **`S1_Fixed_2048` *(median)*** |
| **Stage-1 Total Variation Distance `TVD` *(mean / median)* `(↓)`** | 0.3011 / 0.2382 | **0.2990 / 0.2359** | 0.3192 / 0.2587 | **`S2_Adaptive_1x` (0.2990 *(mean)* / 0.2359 *(median)*)** |
| **Stage-1 Shannon Entropy H (bits) *(mean / median)* `(↓)`** | 3.9638 / 3.8091 | **3.9202 / 3.7633** | 4.2393 / 3.9645 | **`S2_Adaptive_1x` (3.9202 *(mean)* / 3.7633 *(median)*)** |
| **Confirm-1024 Overlap F_B *(mean / median)* `(↑)`** | **0.8715** / 0.9148 | 0.8681 / 0.9090 | 0.8622 / **0.9206** | **`S1_Fixed_2048` *(mean)*** / **`S3_Adaptive_4x` *(median)*** |
| **Confirm-1024 Squared Hellinger F_H = F_B² *(mean / median)* `(↑)`** | **0.7789** / 0.8376 | 0.7740 / 0.8268 | 0.7700 / **0.8476** | **`S1_Fixed_2048` *(mean)*** / **`S3_Adaptive_4x` *(median)*** |
| **Confirm-1024 `TVD` *(mean / median)* `(↓)`** | **0.3102 / 0.2398** | 0.3106 / 0.2486 | 0.3196 / 0.2562 | **`S1_Fixed_2048` (0.3102 *(mean)* / 0.2398 *(median)*)** |
| **Confirm-1024 Shannon Entropy H (bits) *(mean / median)* `(↓)`** | 3.9924 / 3.8283 | **3.9576 / 3.8027** | 4.2416 / 3.9787 | **`S2_Adaptive_1x` (3.9576 *(mean)* / 3.8027 *(median)*)** |
| **Compiled Two-Qubit Gates per Replica *(mean / median)* `(↓)`** | 48.50 / 34.00 | 48.50 / 34.00 | **41.79 / 29.50** (167.17 total 4x) | **`S3_Adaptive_4x`** (joint 4-subgraph routing found shorter paths) |
| **Compiled Circuit Depth *(mean / median)* `(↓)`** | **83.67** / 58.50 | **83.67** / 58.50 | **83.67 / 54.50** | **Tied *(mean)* (83.67), `S3_Adaptive_4x` *(median)* (54.50)** |

---

## 2. Key Empirical Findings & Economic Mechanism Analysis

### 2.1 Sequential Validity Confirmed on Hardware (0.0% Matched-Policy False Stops Across All 48 Adaptive Trajectories)
1. **Zero Unconfirmed False Stops Under Matched Confirmation (`0.0% <= 5.0%` Criterion):**
   * Across all four adaptive hardware ladders (`IBM S2`: 10 halts, `IBM S3`: 11 halts, `IQM S2`: 4 halts, `IQM S3`: 6 halts = 31 live halts across 48 adaptive workload trajectories), **every single workload that halted under `evaluate_bitstring_stopping_rung` passed its matched independent 1,024-shot confirmation job (0/10, 0/11, 0/4, 0/6 unconfirmed false stops = 0.0%)**.
   * Even for the non-adaptive baseline `S1_Fixed_2048`, evaluating `evaluate_bitstring_stopping_rung` at 2,048 shots correctly separated true qualifiers from borderline noise: on IBM `W_P3_ModHold_051` (`Trotter_Ising 5Q`, τ_obs = 0.0800) and `W_P3_ModHold_060` (`VQE_Heisenberg 9Q`, τ_obs = 0.0900), Stage-1 point estimates barely passed (0.0757 and 0.0803), failed on `S1_Confirm_1024` (0.0855 and 0.0934), and were **correctly rejected by the 98.75% bootstrap confidence bound (`stage1_lcb_halt_pass = False`)**, yielding 0/8 false claims on IBM and 0/5 on IQM.

2. **Why `W_P3_ModHold_051` Passed 4x Confirmation (`0.0457 <= 0.0800`) While Missing 1x Single-Subgraph Confirmation (`0.0825 > 0.0800`) on IBM:**
   * On `ibm_kingston`, `W_P3_ModHold_051` (`Trotter_Ising 5Q`) requires single-site magnetization MAE <= 0.0800. Isolated 1x execution on a single `5Q` subgraph experienced localized readout/coherent Z bias (`MAE = 0.0757` on `S1_Main_2048`, 0.0855 on `S1_Confirm_1024`, 0.0728 on `S2` Rung 4, 0.0825 on `S2_Confirm_1024`).
   * By contrast, `S3_Adaptive_4x` packs 4 disjoint `5Q` subgraphs separated by d_G = 3 on `ibm_kingston`. Averaging Z_i across 4 physically distinct subgraphs canceled localized single-qubit bias, reducing Magnetization MAE to **0.0441** (`bound_obs_9875 = 0.0639 <= 0.0800`) at Rung 1 (256 physical shots) and confirming at **`0.0457 <= 0.0800`** on `S3_Confirm_1024` (`dbpb82s4148s739uaesg`).
   * Thus, under its own deployed 4x execution policy, `S3_Adaptive_4x` had **0/11 (0.0%) unconfirmed false stops**; only if cross-audited against a single-subgraph 1x confirmation job (`S2_Confirm_1024`) does `W_P3_ModHold_051` register as 1/11 (9.09%), demonstrating that spatial ensemble averaging genuinely raises physical target attainment on bias-sensitive observables.

### 2.2 Physical-Shot & Active-Execution Savings vs. Standalone Per-Job Billing Floor
Experiment 5 reveals an essential distinction between **physical shot / active QPU pulse savings** and **standalone multi-job cloud billing**:

* **Massive Physical Shot and Active QPU Execution Reductions on Qualifying Workloads:**
  * On IBM `ibm_kingston`, `S3_Adaptive_4x` halted **9/12 workloads immediately at Rung 1 (256 physical shots)** and **2/12 at Rung 2 (512 physical shots)**, cutting Stage-1 physical shots to **7,680** (**-68.75% vs `S1_Fixed_2048`'s 24,576 shots** and **-83.33% vs the 46,080 no-early-stop ladder**) and cutting Stage-1 active QPU execution time by **-50.63%** (5.8106 s vs 11.7692 s).
  * `S2_Adaptive_1x` on IBM halted 4/12 at Rung 1, 2/12 at Rung 2, 3/12 at Rung 3, and 1/12 at Rung 4, cutting Stage-1 physical shots by **-20.83% vs `S1`** (19,456 vs 24,576) and active QPU execution time by **-25.03%** (8.8236 s vs 11.7692 s).
* **Why Standalone Multi-Rung Job Billing Cost 11.00 s (`S3`) and 14.00 s (`S2`) vs 9.00 s (`S1`) on IBM (and 12.00 - 16.50 cr vs 10.50 cr on IQM):**
  1. **Per-Job Minimum Billing & Control-Electronics Initialization Floor:** Because 1 hard 9Q workload on IBM `S3` (`W_P3_ModHold_058: QFT_Phase 9Q`, which reached `F_B = 0.7528 < 0.78` and `Top2_Peak_Mass = 0.5857 < 0.65`) did not qualify, `S3` still had to submit standalone 1-PUB jobs at Rung 3 (`dbpb8qdt7fqc73894ml0`, 1,024 shots, 1.2165 s active -> **2.00 s billed**) and Rung 4 (`dbpb92oou4as73ctc9t0`, 2,048 shots, 1.4942 s active -> **3.00 s billed**). Thus, 5.00 s of `S3`'s 11.00 s Stage-1 bill (45.5%) was spent chasing a single non-qualifying `9Q` circuit across Rungs 3 and 4! Even with that penalty, `S3`'s higher confirmed yield (11/12 vs 9/12) matched `S1` on Stage-1 billed efficiency (**1.0000 Conf. Acc/s**) and beat `S1` on total billed efficiency (**0.6875 vs 0.6429 Conf. Acc/s, +6.94%**).
  2. **Absence of a Futility (Upper-Confidence-Bound) Early Abort on Noisy Hardware (`IQM emerald`):** On `emerald`, 6/12 workloads (`7Q` and `9Q` `QFT_Phase`, `Trotter_Ising`, and `VQE_Heisenberg`) had hardware noise ceilings well below target thresholds (e.g., `W_P3_ModHold_058` had Rung-1 F_B = 0.4115 ≪ 0.78). Without a **futility stopping rule** (UCB_0.9875 < τ => abort early), those 6 impossible workloads marched through all 4 upward rungs (`256 + 512 + 1024 + 2048 = 3,840` physical shots each), consuming 26,112 shots (12.00 cr) on `S3` and 33,792 shots (16.50 cr) on `S2` versus 24,576 shots (10.50 cr) on single-job `S1_Fixed_2048`.
  3. **Direct Operational Lesson for Experiment 2 (`Service_Arm_1: Prometheus_Autonomous_Service`):** Submitting 4 separate upward QPU jobs for an entire batch is suboptimal when some circuits cannot pass. Instead, in **Priority 5 - Experiment 2**, `Service_Arm_1` executes each arrival sub-wave in a packed primary batch job and uses `evaluate_bitstring_stopping_rung` to **certify completed requests immediately while triggering a selective shot-escalation/retry job (`Job 4`) ONLY when a `Strict_FB_090` request lands in the borderline statistical zone** (point or UCB near threshold, LCB just below threshold).

---

## 3. Stopping-Rung Trajectories, Within-Shot Replica Correlation & Statistical Tests

### Table 2: Stopping-Rung Distribution & Within-Shot 4-Replica Agreement Summary (`N = 12` Workloads per Provider)

| Provider & Adaptive Arm | Rung 1 Halts (256 Shots) | Rung 2 Halts (512 Shots; 768 Cum.) | Rung 3 Halts (1,024 Shots; 1,792 Cum.) | Rung 4 Halts (2,048 Shots; 3,840 Cum.) | Unhalted After Rung 4 (3,840 Cum. Shots) | Stage-1 Physical Shots Used (`vs 46,080` No-Stop Max) | Within-Shot 4-Replica Pairwise Agreement *(mean / median [min, max])* |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **IBM `S2_Adaptive_1x`** | 4 / 12 (33.3%) | 2 / 12 (16.7%) | 3 / 12 (25.0%) | 1 / 12 (8.3%) | 2 / 12 (16.7%) | 19,456 (**-57.78%**) | `0.0000 / 0.0000 [0.0000, 0.0000]` (1x isolated single replica) |
| **IBM `S3_Adaptive_4x`** | **9 / 12 (75.0%)** | 2 / 12 (16.7%) | 0 / 12 (0.0%) | 0 / 12 (0.0%) | **1 / 12 (8.3%)** | **7,680 (-83.33%)** | `0.3270 / 0.3099 [0.0111, 0.7201]` (17 evaluated 4x PUBs) |
| **IQM `S2_Adaptive_1x`** | 2 / 12 (16.7%) | 1 / 12 (8.3%) | 1 / 12 (8.3%) | 0 / 12 (0.0%) | 8 / 12 (66.7%) | 33,792 (**-26.67%**) | `0.0000 / 0.0000 [0.0000, 0.0000]` (1x isolated single replica) |
| **IQM `S3_Adaptive_4x`** | **5 / 12 (41.7%)** | 0 / 12 (0.0%) | 1 / 12 (8.3%) | 0 / 12 (0.0%) | **6 / 12 (50.0%)** | **26,112 (-43.33%)** | `0.1729 / 0.1694 [0.0026, 0.5853]` (32 evaluated 4x PUBs) |

> [!NOTE]
> **Why Whole-Shot Bootstrap Resampling Was Essential for `S3_Adaptive_4x`:** Across evaluated 4x PUBs, mean within-shot pairwise bitstring agreement across the 6 replica pairs C(4,2) = 6 ranged from 0.0111 (for high-entropy `9Q QAOA_MaxCut`) up to 0.7201 (for peaked `5Q QFT_Phase`), combining distribution peaking with any common-mode readout/clock fluctuations. Because `evaluate_bitstring_stopping_rung` resamples whole physical shots (`w_boot @ shot_counts_matrix`) rather than assuming 4 S_r independent multinomial draws, its standard errors automatically account for within-shot covariance, preserving 0.0% false stops on hardware.

### Table 3: Paired Workload Distribution Overlap (F_B) Statistical Tests (`N = 12` Workloads, `df = 11`, Two-Sided Paired `t`-Tests with Holm Step-Down Adjustment `m = 3`)

| Provider & Evaluation Stage | Pairwise Comparison (`Arm A vs Arm B`) | Mean Paired ΔF_B (`A - B`) | Paired Standard Error (`SE`) | Paired t-Statistic (`df = 11`) | Unadjusted Two-Sided p-Value | Holm-Adjusted p-Value (`m = 3`) | Statistical & Practical Interpretation |
| :--- | :--- | :---: | :---: | :---: | :---: | :---: | :--- |
| **IBM Stage-1 Terminal** | `S3_Adaptive_4x` vs `S1_Fixed_2048` | **+0.01290** | 0.01208 | +1.0678 | 0.3085 | 0.6069 | `S3` preserves/slightly improves overlap using -68.75% fewer shots |
| **IBM Stage-1 Terminal** | `S3_Adaptive_4x` vs `S2_Adaptive_1x` | **+0.02291** | 0.00871 | +2.6303 | 0.0234 | 0.0702 | Unadjusted p = 0.0234 (`Holm p = 0.0702`) in favor of 4x ensemble |
| **IBM Stage-1 Terminal** | `S2_Adaptive_1x` vs `S1_Fixed_2048` | -0.01000 | 0.00926 | -1.0796 | 0.3034 | 0.6069 | Negligible 0.0100 finite-sample plug-in difference at early halts |
| **IBM Confirm-1024** | `S3_Adaptive_4x` vs `S1_Fixed_2048` | **+0.02687** | 0.01122 | +2.3954 | 0.0355 | 0.1066 | Unadjusted p = 0.0355 (`Holm p = 0.1066`) higher 4x confirmation F_B |
| **IBM Confirm-1024** | `S3_Adaptive_4x` vs `S2_Adaptive_1x` | **+0.02771** | 0.01182 | +2.3433 | 0.0389 | 0.1066 | Unadjusted p = 0.0389 (`Holm p = 0.1066`) higher 4x confirmation F_B |
| **IBM Confirm-1024** | `S2_Adaptive_1x` vs `S1_Fixed_2048` | -0.00083 | 0.00286 | -0.2915 | 0.7761 | 0.7761 | Identical 1x hardware confirmation distributions (0.9146 vs 0.9154) |
| **IQM Stage-1 Terminal** | `S3_Adaptive_4x` vs `S1_Fixed_2048` | -0.01478 | 0.00669 | -2.2077 | 0.0494 | 0.1483 | 4x packing on `54Q` crystal incurs -0.0148 overlap on wide `7Q/9Q` circuits |
| **IQM Stage-1 Terminal** | `S3_Adaptive_4x` vs `S2_Adaptive_1x` | -0.01734 | 0.00901 | -1.9250 | 0.0805 | 0.1609 | Not significant after Holm adjustment (`p = 0.1609`); identical 6/12 yield |
| **IQM Stage-1 Terminal** | `S2_Adaptive_1x` vs `S1_Fixed_2048` | **+0.00256** | 0.00417 | +0.6146 | 0.5513 | 0.5513 | Indistinguishable Stage-1 overlap (0.8792 vs 0.8766) |
| **IQM Confirm-1024** | `S3_Adaptive_4x` vs `S1_Fixed_2048` | -0.00932 | 0.00868 | -1.0727 | 0.3064 | 0.6127 | No statistically significant difference on 1,024-shot confirmation |
| **IQM Confirm-1024** | `S3_Adaptive_4x` vs `S2_Adaptive_1x` | -0.00587 | 0.00796 | -0.7375 | 0.4762 | 0.6127 | No statistically significant difference on 1,024-shot confirmation |
| **IQM Confirm-1024** | `S2_Adaptive_1x` vs `S1_Fixed_2048` | -0.00345 | 0.00202 | -1.7102 | 0.1152 | 0.3457 | Both 1x confirmation jobs reproduce within 0.0035 overlap |

---

## 4. Per-Workload Hardware Outcomes (`W_P3_ModHold_049..060`)

### Table 4A: IBM Quantum (`ibm_kingston`) Per-Workload Stopping & Confirmation Ledger (`N = 12`)

| Workload ID | Family & Width | Target Thresholds (τ_FB, τ_obs) | Compiled Structural (`1x 2Q/d` vs `4x rep2Q/d/dG`) | `S1_Fixed_2048` (`Stage-1 / Confirm` F_B, Obs, LCB, Conf. Acc) | `S2_Adaptive_1x` (Halt Rung, Shots, `Stage-1 / Confirm` F_B, Obs, Conf. Acc, False) | `S3_Adaptive_4x` (Halt Rung, Shots, `Stage-1 / Confirm` F_B, Obs, Conf. Acc 4x/1x, False 4x/1x) |
| :--- | :--- | :---: | :---: | :---: | :---: | :---: |
| **`W_P3_ModHold_049`** | `QAOA_MaxCut` (`5Q`) | F_B >= 0.85, Cut >= 0.72 (ideal 0.8894) | 41 / 112 vs 47.8 / 116 / 3 | 0.9681 / 0.9625, Cut 0.8255 / 0.8286, `LCB=Pass`, **Acc=1** | **R1 (256)**, 0.9628 / 0.9726, Cut 0.8184 (`LCB=0.7865`), **Acc=1, False=0** | **R1 (256)**, 0.9764 / 0.9776, Cut 0.8387 (`LCB=0.8235`), **Acc=1/1, False=0/0** |
| **`W_P3_ModHold_050`** | `QFT_Phase` (`5Q`) | F_B >= 0.85, Top2 >= 0.65 (ideal 1.0000) | 28 / 77 vs 29.5 / 77 / 3 | 0.9388 / 0.9375, Top2 0.9067 / 0.9023, `LCB=Pass`, **Acc=1** | **R1 (256)**, 0.9079 / 0.9297, Top2 0.8359 (`LCB=0.7828`), **Acc=1, False=0** | **R1 (256)**, 0.9202 / 0.9183, Top2 0.8682 (`LCB=0.8439`), **Acc=1/1, False=0/0** |
| **`W_P3_ModHold_051`** | `Trotter_Ising` (`5Q`) | F_B >= 0.85, MAE <= 0.08 (ideal 0.0000) | 22 / 59 vs 17.5 / 59 / 3 | 0.9868 / 0.9823, MAE 0.0757 / 0.0855, `LCB=Fail`, **Acc=0** | **Unhalted (3,840)**, 0.9873 / 0.9781, MAE 0.0728 (`UCB=0.0867`), **Acc=0, False=0** | **R1 (256)**, 0.9909 / 0.9947, MAE 0.0441 (`UCB=0.0639`), **Acc=1/0, False=0/1** |
| **`W_P3_ModHold_052`** | `VQE_Heisenberg` (`5Q`) | F_B >= 0.85, MAE <= 0.09 (ideal 0.0000) | 9 / 36 vs 8.2 / 36 / 3 | 0.9830 / 0.9696, MAE 0.0532 / 0.0776, `LCB=Pass`, **Acc=1** | **R4 (3,840)**, 0.9785 / 0.9783, MAE 0.0606 (`UCB=0.0754`), **Acc=1, False=0** | **R2 (768)**, 0.9845 / 0.9843, MAE 0.0605 (`UCB=0.0744`), **Acc=1/1, False=0/0** |
| **`W_P3_ModHold_053`** | `QAOA_MaxCut` (`7Q`) | F_B >= 0.82, Cut >= 0.72 (ideal 0.8488) | 86 / 175 vs 79.0 / 175 / 2 | 0.9397 / 0.9267, Cut 0.7894 / 0.7832, `LCB=Pass`, **Acc=1** | **R1 (256)**, 0.8827 / 0.9210, Cut 0.7852 (`LCB=0.7630`), **Acc=1, False=0** | **R1 (256)**, 0.9476 / 0.9621, Cut 0.7879 (`LCB=0.7763`), **Acc=1/1, False=0/0** |
| **`W_P3_ModHold_054`** | `QFT_Phase` (`7Q`) | F_B >= 0.82, Top2 >= 0.65 (ideal 1.0000) | 70 / 146 vs 68.0 / 155 / 2 | 0.8541 / 0.8495, Top2 0.7480 / 0.7295, `LCB=Pass`, **Acc=1** | **R3 (1,792)**, 0.8466 / 0.8327, Top2 0.7432 (`LCB=0.7128`), **Acc=1, False=0** | **R1 (256)**, 0.8472 / 0.8564, Top2 0.7393 (`LCB=0.7096`), **Acc=1/1, False=0/0** |
| **`W_P3_ModHold_055`** | `Trotter_Ising` (`7Q`) | F_B >= 0.82, MAE <= 0.08 (ideal 0.0000) | 24 / 33 vs 27.0 / 59 / 2 | 0.9836 / 0.9815, MAE 0.0615 / 0.0486, `LCB=Pass`, **Acc=1** | **R2 (768)**, 0.9749 / 0.9806, MAE 0.0559 (`UCB=0.0780`), **Acc=1, False=0** | **R2 (768)**, 0.9847 / 0.9850, MAE 0.0609 (`UCB=0.0728`), **Acc=1/1, False=0/0** |
| **`W_P3_ModHold_056`** | `VQE_Heisenberg` (`7Q`) | F_B >= 0.82, MAE <= 0.09 (ideal 0.0000) | 12 / 46 vs 12.5 / 51 / 2 | 0.9836 / 0.9823, MAE 0.0577 / 0.0603, `LCB=Pass`, **Acc=1** | **R1 (256)**, 0.9698 / 0.9783, MAE 0.0529 (`UCB=0.0898`), **Acc=1, False=0** | **R1 (256)**, 0.9852 / 0.9862, MAE 0.0474 (`UCB=0.0662`), **Acc=1/1, False=0/0** |
| **`W_P3_ModHold_057`** | `QAOA_MaxCut` (`9Q`) | F_B >= 0.78, Cut >= 0.72 (ideal 0.8734) | 122 / 199 vs 129.0 / 199 / 2 | 0.8907 / 0.8629, Cut 0.7985 / 0.7951, `LCB=Pass`, **Acc=1** | **R2 (768)**, 0.8285 / 0.8751, Cut 0.7947 (`LCB=0.7807`), **Acc=1, False=0** | **R1 (256)**, 0.8989 / 0.9376, Cut 0.8136 (`LCB=0.8048`), **Acc=1/1, False=0/0** |
| **`W_P3_ModHold_058`** | `QFT_Phase` (`9Q`) | F_B >= 0.78, Top2 >= 0.65 (ideal 1.0000) | 117 / 241 vs 116.2 / 241 / 2 | 0.6092 / 0.6211, Top2 0.3833 / 0.3945, `LCB=Fail`, **Acc=0** | **Unhalted (3,840)**, 0.6728 / 0.6060, Top2 0.4692 (`LCB=0.4446`), **Acc=0, False=0** | **Unhalted (3,840)**, 0.7528 / 0.7487, Top2 0.5857 (`LCB=0.5742`), **Acc=0/0, False=0/0** |
| **`W_P3_ModHold_059`** | `Trotter_Ising` (`9Q`) | F_B >= 0.78, MAE <= 0.08 (ideal 0.0000) | 38 / 59 vs 35.0 / 59 / 2 | 0.9696 / 0.9567, MAE 0.0701 / 0.0761, `LCB=Fail`, **Acc=1** | **R3 (1,792)**, 0.9690 / 0.9598, MAE 0.0519 (`UCB=0.0658`), **Acc=1, False=0** | **R1 (256)**, 0.9692 / 0.9800, MAE 0.0590 (`UCB=0.0733`), **Acc=1/1, False=0/0** |
| **`W_P3_ModHold_060`** | `VQE_Heisenberg` (`9Q`) | F_B >= 0.78, MAE <= 0.09 (ideal 0.0000) | 17 / 63 vs 16.5 / 63 / 2 | 0.9636 / 0.9524, MAE 0.0803 / 0.0934, `LCB=Fail`, **Acc=0** | **R3 (1,792)**, 0.9700 / 0.9630, MAE 0.0681 (`UCB=0.0835`), **Acc=1, False=0** | **R1 (256)**, 0.9680 / 0.9765, MAE 0.0655 (`UCB=0.0809`), **Acc=1/1, False=0/0** |

### Table 4B: IQM Resonance (`emerald`) Per-Workload Stopping & Confirmation Ledger (`N = 12`)

| Workload ID | Family & Width | Target Thresholds (τ_FB, τ_obs) | Compiled Structural (`1x 2Q/d` vs `4x rep2Q/d/dG`) | `S1_Fixed_2048` (`Stage-1 / Confirm` F_B, Obs, LCB, Conf. Acc) | `S2_Adaptive_1x` (Halt Rung, Shots, `Stage-1 / Confirm` F_B, Obs, Conf. Acc, False) | `S3_Adaptive_4x` (Halt Rung, Shots, `Stage-1 / Confirm` F_B, Obs, Conf. Acc 4x/1x, False 4x/1x) |
| :--- | :--- | :---: | :---: | :---: | :---: | :---: |
| **`W_P3_ModHold_049`** | `QAOA_MaxCut` (`5Q`) | F_B >= 0.85, Cut >= 0.72 (ideal 0.8894) | 50 / 98 vs 33.0 / 76 / 2 | 0.9416 / 0.9404, Cut 0.7927 / 0.7928, `LCB=Pass`, **Acc=1** | **R1 (256)**, 0.9326 / 0.9329, Cut 0.7962 (`LCB=0.7642`), **Acc=1, False=0** | **R1 (256)**, 0.9536 / 0.9566, Cut 0.8014 (`LCB=0.7866`), **Acc=1/1, False=0/0** |
| **`W_P3_ModHold_050`** | `QFT_Phase` (`5Q`) | F_B >= 0.85, Top2 >= 0.65 (ideal 1.0000) | 30 / 65 vs 26.0 / 56 / 2 | 0.8783 / 0.8761, Top2 0.8223 / 0.8262, `LCB=Pass`, **Acc=1** | **R1 (256)**, 0.8927 / 0.8733, Top2 0.8555 (`LCB=0.8049`), **Acc=1, False=0** | **R1 (256)**, 0.8733 / 0.8814, Top2 0.8145 (`LCB=0.7885`), **Acc=1/1, False=0/0** |
| **`W_P3_ModHold_051`** | `Trotter_Ising` (`5Q`) | F_B >= 0.85, MAE <= 0.08 (ideal 0.0000) | 16 / 26 vs 16.0 / 26 / 2 | 0.9875 / 0.9852, MAE 0.0640 / 0.0796, `LCB=Pass`, **Acc=1** | **Unhalted (3,840)**, 0.9878 / 0.9819, MAE 0.0706 (`UCB=0.0850`), **Acc=1 (Pt), False=0** | **R1 (256)**, 0.9893 / 0.9902, MAE 0.0542 (`UCB=0.0742`), **Acc=1/1, False=0/0** |
| **`W_P3_ModHold_052`** | `VQE_Heisenberg` (`5Q`) | F_B >= 0.85, MAE <= 0.09 (ideal 0.0000) | 8 / 26 vs 8.0 / 26 / 2 | 0.9755 / 0.9722, MAE 0.0834 / 0.0893, `LCB=Fail`, **Acc=1** | **Unhalted (3,840)**, 0.9751 / 0.9688, MAE 0.0878 (`UCB=0.1028`), **Acc=1 (Pt), False=0** | **R1 (256)**, 0.9763 / 0.9745, MAE 0.0700 (`UCB=0.0898`), **Acc=1/1, False=0/0** |
| **`W_P3_ModHold_053`** | `QAOA_MaxCut` (`7Q`) | F_B >= 0.82, Cut >= 0.72 (ideal 0.8488) | 76 / 132 vs 54.2 / 123 / 2 | 0.9017 / 0.8893, Cut 0.7473 / 0.7374, `LCB=Pass`, **Acc=1** | **R2 (768)**, 0.9023 / 0.8845, Cut 0.7606 (`LCB=0.7427`), **Acc=1, False=0** | **R1 (256)**, 0.8963 / 0.9130, Cut 0.7386 (`LCB=0.7264`), **Acc=1/1, False=0/0** |
| **`W_P3_ModHold_054`** | `QFT_Phase` (`7Q`) | F_B >= 0.82, Top2 >= 0.65 (ideal 1.0000) | 68 / 133 vs 57.8 / 132 / 2 | 0.7201 / 0.7160, Top2 0.5742 / 0.5713, `LCB=Fail`, **Acc=0** | **Unhalted (3,840)**, 0.7258 / 0.6967, Top2 0.5801 (`LCB=0.5562`), **Acc=0, False=0** | **Unhalted (3,840)**, 0.6663 / 0.6505, Top2 0.5067 (`LCB=0.4945`), **Acc=0/0, False=0/0** |
| **`W_P3_ModHold_055`** | `Trotter_Ising` (`7Q`) | F_B >= 0.82, MAE <= 0.08 (ideal 0.0000) | 30 / 47 vs 24.8 / 49 / 2 | 0.9695 / 0.9696, MAE 0.0945 / 0.0974, `LCB=Fail`, **Acc=0** | **Unhalted (3,840)**, 0.9737 / 0.9691, MAE 0.0930 (`UCB=0.1058`), **Acc=0, False=0** | **Unhalted (3,840)**, 0.9671 / 0.9703, MAE 0.1101 (`UCB=0.1168`), **Acc=0/0, False=0/0** |
| **`W_P3_ModHold_056`** | `VQE_Heisenberg` (`7Q`) | F_B >= 0.82, MAE <= 0.09 (ideal 0.0000) | 13 / 40 vs 12.2 / 40 / 2 | 0.9720 / 0.9660, MAE 0.0802 / 0.0989, `LCB=Fail`, **Acc=0** | **Unhalted (3,840)**, 0.9666 / 0.9687, MAE 0.1113 (`UCB=0.1260`), **Acc=0, False=0** | **Unhalted (3,840)**, 0.9625 / 0.9564, MAE 0.1107 (`UCB=0.1178`), **Acc=0/0, False=0/0** |
| **`W_P3_ModHold_057`** | `QAOA_MaxCut` (`9Q`) | F_B >= 0.78, Cut >= 0.72 (ideal 0.8734) | 116 / 152 vs 106.8 / 167 / 2 | 0.8465 / 0.8346, Cut 0.7657 / 0.7702, `LCB=Pass`, **Acc=1** | **R3 (1,792)**, 0.8296 / 0.8302, Cut 0.7663 (`LCB=0.7560`), **Acc=1, False=0** | **R3 (1,792)**, 0.8140 / 0.8139, Cut 0.7339 (`LCB=0.7282`), **Acc=1/1, False=0/0** |
| **`W_P3_ModHold_058`** | `QFT_Phase` (`9Q`) | F_B >= 0.78, Top2 >= 0.65 (ideal 1.0000) | 120 / 186 vs 111.2 / 206 / 2 | 0.4667 / 0.4780, Top2 0.2427 / 0.2529, `LCB=Fail`, **Acc=0** | **Unhalted (3,840)**, 0.5063 / 0.4698, Top2 0.2822 (`LCB=0.2599`), **Acc=0, False=0** | **Unhalted (3,840)**, 0.4115 / 0.4095, Top2 0.2064 (`LCB=0.1966`), **Acc=0/0, False=0/0** |
| **`W_P3_ModHold_059`** | `Trotter_Ising` (`9Q`) | F_B >= 0.78, MAE <= 0.08 (ideal 0.0000) | 38 / 47 vs 35.0 / 50 / 2 | 0.9671 / 0.9459, MAE 0.0765 / 0.0988, `LCB=Fail`, **Acc=0** | **Unhalted (3,840)**, 0.9574 / 0.9559, MAE 0.0919 (`UCB=0.1023`), **Acc=0, False=0** | **Unhalted (3,840)**, 0.9325 / 0.9283, MAE 0.1479 (`UCB=0.1540`), **Acc=0/0, False=0/0** |
| **`W_P3_ModHold_060`** | `VQE_Heisenberg` (`9Q`) | F_B >= 0.78, MAE <= 0.09 (ideal 0.0000) | 17 / 52 vs 16.5 / 53 / 2 | 0.8930 / 0.8848, MAE 0.1606 / 0.1667, `LCB=Fail`, **Acc=0** | **Unhalted (3,840)**, 0.9003 / 0.8850, MAE 0.1491 (`UCB=0.1619`), **Acc=0, False=0** | **Unhalted (3,840)**, 0.8995 / 0.9018, MAE 0.1698 (`UCB=0.1763`), **Acc=0/0, False=0/0** |

---

## 5. Complete Hardware Job Provenance & Cumulative Allowance Ledger

### Table 5A: IBM Quantum (`ibm_kingston`) Experiment 5 Job Telemetry Ledger (12 Benchmark Jobs, 94 PUBs, 88,576 Physical Shots, 49.00 s Settled)

| Job Label | IBM Job ID | Account | Executed PUBs | Shots / PUB | Physical Shots | Queue Delay (`s`) | Active QPU Exec (`s`) | Wall-Clock Turnaround (`s`) | Settled Billed (`s`) | Created Timestamp (UTC) | Finished Timestamp (UTC) |
| :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **`S1_Main_2048`** | `dbpaqok4148s739u9hf0` | `Account_A` | 12 | 2,048 | 24,576 | 219.31 s | 11.7692 s | 910.33 s | **9.00 s** | `2026-11-10T05:36:34.172Z` | `2026-11-10T05:51:44.498Z` |
| **`S1_Confirm_1024`** | `dbpb1ult7fqc738947vg` | `Account_B` | 12 | 1,024 | 12,288 | 494.00 s | 4.1458 s | 506.89 s | **5.00 s** | `2026-11-10T05:51:55.049Z` | `2026-11-10T06:00:21.936Z` |
| **`S2_Confirm_1024`** | `dbpb60gou4as73ctc3cg` | `Account_C` | 12 | 1,024 | 12,288 | 2.37 s | 4.1835 s | 252.52 s | **5.00 s** | `2026-11-10T06:00:34.756Z` | `2026-11-10T06:04:47.275Z` |
| **`S3_Confirm_1024`** | `dbpb82s4148s739uaesg` | `Account_A` | 12 | 1,024 | 12,288 | 1.96 s | 4.2407 s | 9.55 s | **5.00 s** | `2026-11-10T06:04:59.770Z` | `2026-11-10T06:05:09.316Z` |
| **`S2_Rung1_256`** | `dbpb87gou4as73ctc850` | `Account_B` | 12 | 256 | 3,072 | 2.01 s | 1.7623 s | 6.56 s | **3.00 s** | `2026-11-10T06:05:18.582Z` | `2026-11-10T06:05:25.138Z` |
| **`S3_Rung1_256`** | `dbpb8ac4148s739uafcg` | `Account_C` | 12 | 256 | 3,072 | 1.98 s | 1.7572 s | 6.96 s | **3.00 s** | `2026-11-10T06:05:29.485Z` | `2026-11-10T06:05:36.446Z` |
| **`S2_Rung2_512`** | `dbpb8es4148s739uafng` | `Account_A` | 8 | 512 | 4,096 | 2.23 s | 2.0159 s | 7.12 s | **3.00 s** | `2026-11-10T06:05:47.229Z` | `2026-11-10T06:05:54.353Z` |
| **`S3_Rung2_512`** | `dbpb8j5t7fqc73894m1g` | `Account_B` | 3 | 512 | 1,536 | 1.76 s | 1.3427 s | 6.30 s | **3.00 s** | `2026-11-10T06:06:04.746Z` | `2026-11-10T06:06:11.048Z` |
| **`S2_Rung3_1024`** | `dbpb8ltt7fqc73894mb0` | `Account_C` | 6 | 1,024 | 6,144 | 2.49 s | 2.5458 s | 8.21 s | **4.00 s** | `2026-11-10T06:06:15.675Z` | `2026-11-10T06:06:23.885Z` |
| **`S3_Rung3_1024`** | `dbpb8qdt7fqc73894ml0` | `Account_A` | 1 | 1,024 | 1,024 | 1.54 s | 1.2165 s | 5.71 s | **2.00 s** | `2026-11-10T06:06:33.606Z` | `2026-11-10T06:06:39.313Z` |
| **`S2_Rung4_2048`** | `dbpb8t5t7fqc73894ms0` | `Account_B` | 3 | 2,048 | 6,144 | 2.10 s | 2.4996 s | 13.40 s | **4.00 s** | `2026-11-10T06:06:44.693Z` | `2026-11-10T06:06:58.097Z` |
| **`S3_Rung4_2048`** | `dbpb92oou4as73ctc9t0` | `Account_C` | 1 | 2,048 | 2,048 | 1.87 s | 1.4942 s | 6.01 s | **3.00 s** | `2026-11-10T06:07:08.033Z` | `2026-11-10T06:07:14.046Z` |
| **IBM Total (12 Jobs)** | **12 Benchmark Jobs** | **3 Accounts** | **94 PUBs** | **256..2,048** | **88,576** | **733.62 s** | **38.9734 s** | **1,739.56 s** | **49.00 s** | `05:36:34Z` | `06:07:14Z` |

### Table 5B: IQM Resonance (`emerald`) Experiment 5 Job Telemetry Ledger (12 Benchmark Jobs + 1 Aux Station Warm-Up Job = 13 Jobs, 120 PUBs, 121,600 Physical Shots, 57.75 cr Settled)

| Job Label & Category | IQM Job ID | Calibration Set ID | Executed PUBs | Shots / PUB | Physical Shots | Queue Delay (`s`) | Station Compile (`s`) | Active QPU Exec (`s`) | Billed Runtime (`s`) | Settled Credits (`cr`) | Created Timestamp (UTC) | Completed Timestamp (UTC) |
| :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **`Aux_Station_Warmup_1PUB`** *(Aux)* | `01a1c374-7d58-746c-97f3-98cbd7a37f70` | `47baedfc-27b6-4c13-b58f-39be7cd3e47d` | 1 | 256 | 256 | 8,204.34 s | 0.1644 s | 0.1317 s | 1.0 s | **0.75 cr** | `2026-11-10T03:15:53.824Z` | `2026-11-10T05:32:38.550Z` |
| **`S1_Main_2048`** *(Benchmark)* | `01a1c3f1-c625-7581-a572-7845ea90e304` | `e50d4bbf-a3d6-4786-8760-cf36945e6388` | 12 | 2,048 | 24,576 | 4.65 s | 2.4449 s | 10.7201 s | 14.0 s | **10.50 cr** | `2026-11-10T05:32:44.463Z` | `2026-11-10T05:33:00.221Z` |
| **`S1_Confirm_1024`** *(Benchmark)* | `01a1c3f2-1c9e-7175-8324-098eafb53ae8` | `e50d4bbf-a3d6-4786-8760-cf36945e6388` | 12 | 1,024 | 12,288 | 16.65 s | 2.1186 s | 4.4756 s | 7.0 s | **5.25 cr** | `2026-11-10T05:33:06.601Z` | `2026-11-10T05:33:28.701Z` |
| **`S2_Confirm_1024`** *(Benchmark)* | `01a1c3f2-8757-73ac-bac9-5653bbdafc54` | `e50d4bbf-a3d6-4786-8760-cf36945e6388` | 12 | 1,024 | 12,288 | 2.79 s | 1.9943 s | 6.4062 s | 9.0 s | **6.75 cr** | `2026-11-10T05:33:33.923Z` | `2026-11-10T05:33:43.523Z` |
| **`S3_Confirm_1024`** *(Benchmark)* | `01a1c3f2-cea4-763b-bcd3-14155f77bc6b` | `e50d4bbf-a3d6-4786-8760-cf36945e6388` | 12 | 1,024 | 12,288 | 7.83 s | 2.3905 s | 4.8388 s | 8.0 s | **6.00 cr** | `2026-11-10T05:33:52.196Z` | `2026-11-10T05:34:05.392Z` |
| **`S2_Rung1_256`** *(Benchmark)* | `01a1c3f3-191b-726f-8292-8c8d3a16c2c5` | `e50d4bbf-a3d6-4786-8760-cf36945e6388` | 12 | 256 | 3,072 | 3.58 s | 1.8525 s | 1.7224 s | 4.0 s | **3.00 cr** | `2026-11-10T05:34:11.239Z` | `2026-11-10T05:34:17.391Z` |
| **`S3_Rung1_256`** *(Benchmark)* | `01a1c3f3-4e5d-7472-a85b-0e8ea6bc35d1` | `e50d4bbf-a3d6-4786-8760-cf36945e6388` | 12 | 256 | 3,072 | 10.92 s | 2.2440 s | 1.0353 s | 4.0 s | **3.00 cr** | `2026-11-10T05:34:24.882Z` | `2026-11-10T05:34:37.343Z` |
| **`S2_Rung2_512`** *(Benchmark)* | `01a1c3f3-9170-718f-a152-cd373afaaa42` | `e50d4bbf-a3d6-4786-8760-cf36945e6388` | 10 | 512 | 5,120 | 2.79 s | 2.0315 s | 2.6284 s | 5.0 s | **3.75 cr** | `2026-11-10T05:34:42.044Z` | `2026-11-10T05:34:47.784Z` |
| **`S3_Rung2_512`** *(Benchmark)* | `01a1c3f3-c34c-77a8-81f3-42b1d0477333` | `e50d4bbf-a3d6-4786-8760-cf36945e6388` | 7 | 512 | 3,584 | 15.31 s | 0.7918 s | 1.1120 s | 2.0 s | **1.50 cr** | `2026-11-10T05:34:54.815Z` | `2026-11-10T05:35:11.628Z` |
| **`S2_Rung3_1024`** *(Benchmark)* | `01a1c3f4-1825-7439-ab33-1c913921cce1` | `e50d4bbf-a3d6-4786-8760-cf36945e6388` | 9 | 1,024 | 9,216 | 1.07 s | 0.2694 s | 4.3150 s | 5.0 s | **3.75 cr** | `2026-11-10T05:35:16.528Z` | `2026-11-10T05:35:22.322Z` |
| **`S3_Rung3_1024`** *(Benchmark)* | `01a1c3f4-4bb5-7448-a667-b99be4d09893` | `e50d4bbf-a3d6-4786-8760-cf36945e6388` | 7 | 1,024 | 7,168 | 14.85 s | 0.5607 s | 2.6142 s | 4.0 s | **3.00 cr** | `2026-11-10T05:35:29.737Z` | `2026-11-10T05:35:47.804Z` |
| **`S2_Rung4_2048`** *(Benchmark)* | `01a1c3f4-a500-7168-8d9b-84ba83cb30d2` | `e50d4bbf-a3d6-4786-8760-cf36945e6388` | 8 | 2,048 | 16,384 | 0.96 s | 0.2536 s | 7.2888 s | 8.0 s | **6.00 cr** | `2026-11-10T05:35:52.590Z` | `2026-11-10T05:36:02.403Z` |
| **`S3_Rung4_2048`** *(Benchmark)* | `01a1c3f4-e5a2-77cd-b68c-4c1ca47d5f13` | `e50d4bbf-a3d6-4786-8760-cf36945e6388` | 6 | 2,048 | 12,288 | 11.46 s | 0.7272 s | 4.7924 s | 6.0 s | **4.50 cr** | `2026-11-10T05:36:09.138Z` | `2026-11-10T05:36:25.927Z` |
| **IQM Benchmark (12 Jobs)** | **12 Benchmark Jobs** | `e50d4bbf-...` | **119 PUBs** | **256..2,048** | **121,344** | **92.86 s** | **17.6790 s** | **51.9492 s** | **76.0 s** | **57.00 cr** | `05:32:44Z` | `05:36:25Z` |
| **IQM Total (13 Jobs incl. Aux)** | **12 Bench + 1 Aux** | 2 Cal Sets | **120 PUBs** | **256..2,048** | **121,600** | **8,297.20 s** | **17.8434 s** | **52.0809 s** | **77.0 s** | **57.75 cr** | `03:15:53Z` | `05:36:25Z` |

---

### Table 6: Cumulative Phase 3 Campaign Execution & Allowance Ledger Reconciliation (`Stage 3.0 + Exp 3 + Exp 1 + Exp 4 + Exp 5`)

| Campaign Stage / Module | IBM Benchmark Jobs | IQM Benchmark Jobs (`+ Aux / Troubleshoot`) | Total Provider Records | Executed PUBs (`IBM + IQM`) | Executed Physical Shots (`IBM + IQM`) | IBM Settled Spend (`s`) | IQM Settled Spend (`cr`) |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **Stage 3.0: Measured-Cost Pilot** | 1 | 1 (`+ 0` aux) | 2 | `8 + 8 = 16` | `8,192 + 4,096 = 12,288` | 4.00 s | 6.00 cr |
| **Priority 1 - Experiment 3: Packing & Fallback** | 4 | 4 (`+ 0` aux) | 8 | `136 + 136 = 272` | `139,264 + 70,656 = 209,920` | 46.00 s | 38.25 cr |
| **Priority 2 - Experiment 1: Policy Selector (`Part A + Part B`)** | 14 | 12 (`+ 2` warm-ups, `+ 4` troubleshoot incl. 1 cancelled) | 32 | `247 + 244 = 491` | `252,928 + 124,928 = 377,856` | 100.00 s | 86.25 cr |
| **Subtotal Through Experiment 1** | **19** | **17 (`+ 2` warm-ups, `+ 4` troubleshoot)** | **42** | **`391 + 388 = 779` PUBs** | **`400,384 + 199,680 = 600,064` Shots** | **150.00 s** | **130.50 cr** |
| **Priority 3 - Experiment 4: Telemetry Refresh (`W1 + W2/W3`)** | 6 | 6 (`+ 0` aux) | 12 | `96 + 96 = 192` | `98,304 + 49,152 = 147,456` | 39.00 s | 24.75 cr |
| **Subtotal Through Experiment 4** | **25** | **23 (`+ 2` warm-ups, `+ 4` troubleshoot)** | **54** | **`487 + 484 = 971` PUBs** | **`498,688 + 248,832 = 747,520` Shots** | **189.00 s** | **155.25 cr** |
| **Priority 4 - Experiment 5: Live Stopping (`S1, S2, S3 + Confirm`)** | 12 | 12 (`+ 1` warm-up) | 25 | `94 + 120 = 214` | `88,576 + 121,600 = 210,176` | 49.00 s | 57.75 cr |
| **Cumulative Settled Total (`Through Exp 5`)** | **37 Jobs** | **35 Benchmark (`+ 3` Warm-Up, `+ 4` Troubleshoot) = 42 Records (41 Billed)** | **79 Records (78 Billed)** | **`581 + 604 = 1,185` PUBs** (`779 + 192 + 214`) | **`587,264 + 370,432 = 957,696` Shots** (1,010,944 nominal `@ 1,024`/PUB) | **238.00 s** | **213.00 cr** |
| **Remaining Provider Allowance vs. Ceiling (379.00 s IBM / 293.00 cr IQM)** | **Up to 18 Jobs Needed for Exp 2** | **Up to 18 Jobs Needed for Exp 2** | **Up to 36 Exp 2 Jobs** | **Up to 216 Exp 2 PUBs** | **Up to ~221,184 Exp 2 Shots** | **141.00 s Remaining** (`Exp 2 Worst-Case = 48.00 s`) | **80.00 cr Remaining** (`Exp 2 Worst-Case = 38.50 cr`) |

---

## 6. Readiness & Hardware Execution Gate for Final Capstone (`Priority 5 - Experiment 2: Dynamic Multi-Wave Customer Queue Service`)

All four upstream Phase 3 policy components are now hardware-validated and cryptographically locked:
1. **Locked Packing & Fallback Rule (`Exp 3`):** [`phase3_locked_packing_fallback_rule.json`](../data/phase3_locked_packing_fallback_rule.json) (`SHA-256 = 2979d15e3fa88df9315229140df6f1582680015b75fa2fb69dc25db309a3b973`).
2. **Locked Prospective Policy Selector (`Exp 1`):** [`phase3_locked_policy_selector.json`](../data/phase3_locked_policy_selector.json) (`SHA-256 = 77bb8ac25c55af34993edcdd1335f76f64e65de157e478f5700bf40c22cdb14b`, locking `A4_Fixed_4Way_Prometheus` as `Best_Dev_Selected_Fixed_Policy` for `Service_Arm_2`).
3. **Locked Triggered Telemetry-Refresh Policy (`Exp 4`):** [`phase3_locked_refresh_policy.json`](../data/phase3_locked_refresh_policy.json) (`SHA-256 = c8fa9126a5daa6304160e82e3b800db586e0a92ed327fb9cc7395803563ad5b0`).
4. **Validated Sequential Stopping & Certification Rule (`Exp 5`):** `evaluate_bitstring_stopping_rung` (0.0% matched-policy unconfirmed false stops across 48 hardware trajectories), informing single-pass batch execution with selective `Job 4` shot escalation for borderline `Strict_FB_090` customer requests.

**Hardware Execution Gate (`GEMINI.md` Rule 3):**  
No live QPU jobs for **Priority 5 - Experiment 2: Dynamic Multi-Wave Customer Queue Service** (`W_P3_CustHold_001..072`, 72 unseen customer requests across 3 operating windows per provider) have been submitted. Please confirm (`yes`) when you are ready to proceed to the Experiment 2 dry-run and hardware execution.
