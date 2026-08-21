# Notebook to paper mapping

Every quantitative result in the paper is produced by one of the notebooks in
`notebooks/`, run in numerical order. This document is the authoritative map.

Absolute values differ modestly between tables because the sections were added over
successive revision rounds and regenerate the topology for different purposes.
Appendix A (Table A.1) of the paper records the seed count and generator
configuration behind each table; this document records which notebook produced it.

## By notebook

### `01_graphs_and_features.ipynb`

**Paper sections:** 4.2  
**Tables:** none  
**Figures:** none

Graph generators (SBM, LFR) and loaders for the three SNAP topologies (ego-Facebook, Twitter-ego BFS sample, email-Eu-core); private/public node labelling, node features, ground-truth neighbourhood N(t).

### `02_recommender_training.ipynb`

**Paper sections:** 4.3, 4.4  
**Tables:** none  
**Figures:** none

LightGCN and FeatureGNN training with BPR loss, link-prediction AUC competence check, and the cold-to-warm gated Recommender scorer.

### `03_multiseed_and_sweeps.ipynb`

**Paper sections:** 5.11, 5.12, 5.13, 5.14  
**Tables:** 13, 14, 15, 16  
**Figures:** 14, 15, 16, 17

Multi-seed leakage bands, one-factor-at-a-time parameter sweeps, scoring-formula variants re-scored without retraining, the graph-propagation-free MF-BPR recommender, and the n=15,000 scale test. Supersedes the earlier 02b notebook now in archive/.

### `04_attacker_metrics.ipynb`

**Paper sections:** 4.5, 5.1-5.6, 5.10  
**Tables:** 2, 3, 12  
**Figures:** 2, 3, 4, 5, 12, 13

Cold versus warm precision and recall, attacker advantage over random, popularity and same-community baselines, observer-history dose-response, structural versus feature-fused ablation, depth-1 versus depth-2 signal, confirmed-relation type mix, and the re-run on each real topology.

### `05_static_defenses.ipynb`

**Paper sections:** 5.7, 5.8  
**Tables:** 4, 5  
**Figures:** 6, 7, 8

Output noise, broadened candidate pool and diversification against a static attacker, plus the calibrated Gaussian differential-privacy mechanism (analytic calibration, zCDP budget accounting) that replaced the earlier ad hoc noise levels.

### `06_adaptive_attacker.ipynb`

**Paper sections:** 5.7, 5.8  
**Tables:** 6, 7, 8, 9  
**Figures:** 9, 10

Each defence re-evaluated against an attacker that aggregates Q queries, the adaptivity gap, the rate-limit interaction, and cumulative privacy-budget accounting.

### `07_inductive_utility.ipynb`

**Paper sections:** 5.9  
**Tables:** 10, 11  
**Figures:** 11

Train-only (inductive) evaluation: transductive AUC inflation, honest utility on held-out edges, and confirmation that cold-start leakage is undiminished on the identical model.

## By table

| Table | Produced by | Caption |
|---|---|---|
| 1 | `see note below` | Main graph-based methods for friend/account recommendation. |
| 2 | `04_attacker_metrics.ipynb` | Cold vs. warm leakage (per-target, k = 20). Measured outputs. |
| 3 | `04_attacker_metrics.ipynb` | Ablation: structural vs. feature-fused embeddings (cold precision@20). Measured outputs. |
| 4 | `05_static_defenses.ipynb` | Formal ε implied by the original ad hoc noise levels of Section 5.7 (conservative m=n sensitivity, SBM, n=700) |
| 5 | `05_static_defenses.ipynb` | Calibrated differential-privacy Pareto sweep (SBM, single seeded run, same protocol as Section 5.7’s sweep). L |
| 6 | `06_adaptive_attacker.ipynb` | Defenses against an adaptive attacker (10 seeds; cold precision@20, mean ± 95% CI). Static = single-query atta |
| 7 | `06_adaptive_attacker.ipynb` | Output noise under a rate-limit (10 seeds; adaptive cold precision@20, mean). Each cell is the leakage an adap |
| 8 | `06_adaptive_attacker.ipynb` | Differentially private noise vs. the three defenses of Table 6, all at a matched Q=40 adaptive-attacker budget |
| 9 | `06_adaptive_attacker.ipynb` | Differentially private noise under a rate-limit (10 seeds, mean; adaptive cold precision@20), paired with the  |
| 10 | `07_inductive_utility.ipynb` | Transductive vs. inductive utility (10 seeds; mean ± 95% CI). 15% of edges are excluded from training. Transdu |
| 11 | `07_inductive_utility.ipynb` | Cold-start leakage for the train-only model (10 seeds; mean ± 95% CI), showing leakage persists without transd |
| 12 | `04_attacker_metrics.ipynb` | Cold vs. warm leakage on two additional real graphs (single seeded run, same protocol as Section 5.10). |
| 13 | `03_multiseed_and_sweeps.ipynb` | Multi-seed leakage band (10 seeds; precision/recall@20; mean ± 95% CI). Measured outputs. |
| 14 | `03_multiseed_and_sweeps.ipynb` | Formula-sensitivity re-scoring (seed=0, no retraining). Upper block: cold precision/recall are identical acros |
| 15 | `03_multiseed_and_sweeps.ipynb` | Leakage for a graph-propagation-free matrix-factorization recommender (10 seeds; mean ± 95% CI). Compare to Ta |
| 16 | `03_multiseed_and_sweeps.ipynb` | Cold vs. warm leakage at n=15,000 (SBM, 5 seeds; mean ± 95% CI). Five seeds rather than the ten used in Sectio |

Table 1 is a literature summary rather than a computed result, so no notebook
produces it. Table A.1 in Appendix A is a configuration key, also not computed.

## By figure

| Figure | Produced by | Caption |
|---|---|---|
| 1 | `drawn by hand (schematic)` | Conceptual cold-start leakage mechanism. The chain is composed entirely of legitimate, documented behaviors; t |
| 2 | `04_attacker_metrics.ipynb` | Attacker advantage (SBM). Cold-start precision@20 dwarfs random and popularity baselines and is ≈5.4× the same |
| 3 | `04_attacker_metrics.ipynb` | Dose-response. Leakage (precision@20) declines monotonically as the observer history budget h grows from 0 to  |
| 4 | `04_attacker_metrics.ipynb` | Signal degrades with depth (SBM). Depth-1 cold precision@20 (0.321) far exceeds depth-2 (0.127): the attacker’ |
| 5 | `04_attacker_metrics.ipynb` | Confirmed-relation type mix (SBM). Roughly half of confirmed relations are mutual, with the remainder split be |
| 6 | `05_static_defenses.ipynb` | Privacy–utility Pareto trade-off (SBM). Noise and broader-pool defenses trace smooth trade-off curves toward t |
| 7 | `05_static_defenses.ipynb` | Observation-budget erosion (SBM). With output noise active (σ = 0.2), a patient attacker who aggregates Q repe |
| 8 | `05_static_defenses.ipynb` | Calibrated differential privacy (SBM). Leakage and utility fall together as ε shrinks; reaching conventionally |
| 9 | `06_adaptive_attacker.ipynb` | Defenses against an adaptive attacker. (a) Output noise: leakage approaches the undefended level as queries in |
| 10 | `06_adaptive_attacker.ipynb` | Differentially private noise under the adaptive attacker. (a) DP is eroded by query aggregation like any outpu |
| 11 | `07_inductive_utility.ipynb` | Honest utility and the privacy–utility tradeoff. Transductive AUC overstates recommendation quality most sever |
| 12 | `04_attacker_metrics.ipynb` | Cross-topology external validity. Cold ≫ warm precision@20 across all graph types indicates topology-independe |
| 13 | `04_attacker_metrics.ipynb` | Cold vs. warm leakage across three real topologies. The cold ≫ warm pattern replicates on Twitter-ego and emai |
| 14 | `03_multiseed_and_sweeps.ipynb` | Multi-seed robustness: (a) cold vs. warm precision@20 (10 seeds), (b) leakage decreases with feature homophily |
| 15 | `03_multiseed_and_sweeps.ipynb` | Formula sensitivity. Cold precision is identical across every gate/fusion (warm-path) variant by construction  |
| 16 | `03_multiseed_and_sweeps.ipynb` | Leakage for a graph-propagation-free matrix-factorization recommender vs. LightGCN and FeatureGNN (10-seed mea |
| 17 | `03_multiseed_and_sweeps.ipynb` | Leakage at n=15,000 vs. the n=700 baseline (SBM). The cold/warm ratio grows, not shrinks, at 21× larger scale. |
| 18 | `drawn by hand (schematic)` | Threat model and attack surface. Four actor classes exploit a layered recommendation pipeline, leading to grap |

Figures 1 and 18 are schematics of the leakage mechanism and the threat model,
drawn rather than computed.

