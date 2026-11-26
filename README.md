# Automated Queue Batching & Early Measurement Stopping

*Portfolio Hub: [Prometheus Quantum Overview](https://github.com/PrometheusDynamicsCanada/prometheus-quantum-overview) · [Phase 3 Capstone Report](reports/prometheus_phase3_capstone_evidence_release.md) · [Data Dictionary](DATA_DICTIONARY.md)*

Teams operating shared quantum queues must decide when to pack circuits together, when to refresh calibration data, and when enough measurement shots have been collected. We tested Prometheus's automated queue policy against the strongest fixed 4-way baseline across 72 unseen customer-style requests on 156-qubit IBM Kingston and 54-qubit IQM Emerald.

On IBM Kingston, Prometheus accepted 30 of 36 requests versus 27 of 36 for the fixed baseline (+11.1% accepted per billed second) at the exact same 18.00 billed seconds, including 12 of 12 versus 8 of 12 on strict-tier requests. On IQM Emerald, Prometheus raised accepted requests from 12 of 36 to 22 of 36 (+83.3% yield), and across sequential shot-stopping tests (256 to 2,048 shots), 0 of 31 early stops failed independent confirmation while cutting initial IBM shot usage by 68.8%.

On IQM Emerald, adding a selective retry wave raised total accepted requests (19 to 22) while increasing total credit spend (25.50 to 33.00 credits), matching the baseline's 0.67 accepted-per-credit ratio overall.

This repository contains the results, raw measurements, and offline verification tools from 149 completed benchmark jobs (157 total provider records, 2.09 million measurement shots).

![Automated Queue Workflow and Separated IBM vs. IQM Queue Economics](assets/fig0_opening_queue_workflow_and_economics.png)
*Summary figure: Queue workflow and separated billing outcomes across 72 unseen requests on IBM Kingston and IQM Emerald.*

**[Read the detailed results](#detailed-results) · [Verify the data](#verify-the-data) · [Request an evaluation](https://prometheusdynamics.ca/connect/) · [Book a demonstration](mailto:william@prometheusdynamics.ca?subject=Prometheus%20Evaluation)**

---

<a id="detailed-results"></a>
## Executive Summary (`Experiment 2` Simulated Customer Queue on Live Hardware, `72` Unseen Requests)

*Hardware IDs:* IBM `dbpfuvk4148s739ui3l0`..`dbpg0p8ou4as73ctjs1g` (`12` jobs); IQM `01a1c539-e6bc..`..`01a1c546-d98d..` (`15` jobs). *(mean)* observables shown:

| Provider & Service Policy | Accepted `(↑)` | Billed Spend `(↓)` | Acc / Billed `(↑)` | Output Agreement $F_B$ `(↑)` | Squared Hellinger $F_H$ `(↑)` | `TVD` `(↓)` | Entropy `(↓)` | `2Q` Gates / Depth `(↓)` |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **IBM Kingston: Fixed `4-Way` Baseline** | `27 / 36` (`75.0%`) | **`18.00 s`** | `1.50 Acc/s` | `0.9197` | `0.8520` | `0.2240` | `3.06` | `63.58` / **`121.33`** |
| **IBM Kingston: Prometheus Service** | **`30 / 36` (`83.3%`)** | **`18.00 s`** | **`1.67 Acc/s` (`+11.1%`)** | **`0.9265`** | **`0.8653`** | **`0.2090`** | **`3.02`** | **`61.89`** / `131.06` |
| **IQM Emerald: Fixed `4-Way` Baseline** | `12 / 36` (`33.3%`) | **`18.00 cr`** | `0.67 Acc/cr` | `0.8268` | `0.7074` | `0.3861` | `3.81` | `61.25` / `103.03` |
| **IQM Emerald: Prometheus (SubWave A+B)** | `19 / 36` (`52.8%`) | `25.50 cr` | **`0.75 Acc/cr` (`+11.8%`)** | `0.8648` | `0.7662` | `0.3194` | `3.54` | **`58.42` / `98.72`** |
| **IQM Emerald: Prometheus (Full Queue)** | **`22 / 36` (`61.1%`)** | `33.00 cr` | `0.67 Acc/cr` (`+83.3%` yield) | **`0.8685`** | **`0.7721`** | **`0.3122`** | **`3.52`** | **`58.42` / `98.72`** |

*Metric Key:* Output-distribution agreement is Classical Bhattacharyya Overlap $F_B = \sum_x \sqrt{P_{\text{ideal}}(x)P_{\text{meas}}(x)}$ (labeled `"State Fidelity"` in Studio UIs); $F_H = F_B^2$. Cluster-robust IQM $\Delta F_B = +0.0416$ (`K=12` `CRVE p = 0.000012`).

---

<a id="verify-the-data"></a>
## Inspect the Evidence & Reproduce Supported Checks Offline

- **Command:** `python scripts/verify_execution_service_campaign.py`
- **Dependencies & measured runtime:** Python 3 + `numpy` + `scipy` (`0` cloud credentials, **`~0.75 s`** measured runtime; audits `157` provider records, sequential stopping ladders, `CRVE` p-values, and SHA-256 manifests).
- **Request an evaluation or book a demonstration:** [prometheusdynamics.ca/connect](https://prometheusdynamics.ca/connect/) | [william@prometheusdynamics.ca](mailto:william@prometheusdynamics.ca?subject=Prometheus%20Evaluation)

---

## Full Phase 1-3 Module Tables, Medians, Job IDs & Scientific Figures

<details>
<summary><strong>1. All Phase 3 Blind Holdout Modules (212 Workloads) & Cumulative 157-Record Accounting - Click to expand</strong></summary>

| Module & Holdout Scope | IBM Kingston Outcome | IQM Emerald Outcome | Statistical / Operational Evidence |
| :--- | :--- | :--- | :--- |
| **Stage 3.0 Pilot** (`2` jobs, `16` PUBs, `12,288` shots) | `4/4` accepted (`F_B = 0.9462` mean / `0.9585` med; `4.0 s`) | `3/4` accepted (`F_B = 0.8753` mean / `0.9036` med; `6.0 cr`) | Calibrated provider billing models |
| **Exp 3 Packing Calibration** (`8` jobs, `272` PUBs, `209,920` shots) | **`k=8` `F_B = 0.9642` (`48/48` accepted)** | **`k=4` `F_B = 0.9085` (`35/48`)**; `7Q-9Q` stepped `k=6 -> k=3` (`18/24`) | Policy frozen prior to holdouts |
| **Exp 1 Policy Selector** (`26` jobs, `491` PUBs, `377,856` shots) | **`5.20 Acc/s` (`2.40x` vs. SABRE, `2.08x` vs. TKET)** | **`3.11 Acc/cr` (`+8.89%` vs. fixed `A4`)** | IQM vs. SABRE Holm `p = 0.0096` |
| **Exp 4 Telemetry Refresh** (`12` jobs, `192` PUBs, `147,456` shots) | Retained cached `T_0` (`10/12` acc, **`-75.6%` CPU time**) | Placement remap lifted yield **`5/12 -> 7/12`** | Selective refresh without blind recompile |
| **Exp 5 Live Shot Stopping** (`24` jobs, `214` PUBs, `210,176` shots) | **`-68.75%` Stage-1 shots**, `-50.6%` active `s`, **`11/12` conf** | `-43.33%` Stage-1 shots vs. `46k` max, `6/12` conf | **`0/31` false early stops (`0.0%`)** |
| **Phase 1 + Phase 2 Foundations** (`50` jobs, `1,246` PUBs, `1,080,832` shots) | `270.0 s` settled across `96` unseen workloads | `132.75 cr` settled across routing, failover & shot ladders | [Phase 1+2 Report](reports/prometheus_phase1_phase2_campaign_report.md) |

</details>

<details>
<summary><strong>2. Multi-Panel Campaign Figures & Stage Reports - Click to expand</strong></summary>

![Figure 1: Accepted Customer Requests vs. Billed Hardware Cost Across Operating Policies](assets/fig1_phase3_accepted_requests_vs_billed_cost.png)

![Figure 2: Classical Bhattacharyya Overlap F_B and Acceptance Across Packing, Refresh and Service Policies](assets/fig2_phase3_quality_vs_execution_policy.png)

![Figure 3: Complete Phase 3 Campaign Accounting and Disjoint Workload Cohort Isolation](assets/fig3_phase3_complete_campaign_accounting.png)

</details>
