# Notebook to paper mapping

Every quantitative result in the paper is produced by one of the notebooks in
`notebooks/`, run in numerical order. This document is the authoritative map. Section,
table and figure numbers refer to the current version of the manuscript.

The paper uses three synthetic configurations (Table 2 of the paper):

| Configuration | Purpose | Generator | Seeds | Epochs | Notebooks |
|---|---|---|---|---|---|
| A | Single-seed analyses and static defences | SBM with communities of 180/160/140/120/100 nodes, `p_in=0.06`, `p_out=0.0015`; LFR with `tau1=2.7`, `tau2=1.4`, `mu=0.18`, mean degree 12 | graph seed 42, training seed 0 | 60 | 01, 02, 04, 05 |
| B | Ten-seed robustness, adaptive attacker, held-out-edge evaluation | SBM with seven communities of 100 nodes, `p_in=0.06`, `p_out=0.006`; LFR with `tau1=3`, `tau2=1.5`, `mu=0.10`, mean degree 10 | 0-9 | 45 | 03, 06, 07 |
| C | Scale test | SBM with 15,000 nodes, 150 communities of 100 nodes, `p_out` scaled to keep the expected degree | 0-4 | 45 | 03 |

The public datasets (ego-Facebook, Twitter-ego BFS sample, email-Eu-core) are processed
like Configuration A.

## By notebook

### `01_graphs_and_features.ipynb`

**Paper sections:** 4.2
**Tables:** none (inputs to Table 2)
**Figures:** none

Graph generators of Configuration A (SBM, LFR) and loaders for the three SNAP datasets;
privacy labels, node features and the ground-truth neighbourhood `N(t)`.

### `02_recommender_training.ipynb`

**Paper sections:** 4.3, 5.1
**Tables:** none (link-prediction AUC values quoted in Section 5.1 and Table 5)
**Figures:** none

LightGCN and FeatureGNN trained with the BPR loss for Configuration A and for the
public datasets, with the link-prediction AUC competence check. The ego-Facebook model
is trained in its own re-seeded cell.

### `03_multiseed_and_sweeps.ipynb`

**Paper sections:** 5.5, 5.7
**Tables:** 4, B.1
**Figures:** data for 5 and 6 (drawn by notebook 08)

Configuration B: ten-seed bands for LightGCN, FeatureGNN and MF-BPR; one-factor-at-a-time
parameter sweeps; scoring-rule variants re-scored without retraining (gate shape, gate
scale, fusion rule and the two cold-path variants). Configuration C: the n=15,000 scale
test (last row of Table 4).

### `04_attacker_metrics.ipynb`

**Paper sections:** 5.2, 5.3, 5.4, 5.5 (single-seed ablation), 5.6
**Tables:** 3, 5
**Figures:** 3, 4

Cold versus warm precision and recall with bootstrap confidence intervals and the
Wilcoxon test; random, popularity and same-community baselines; observer-history
dose-response; structural versus feature-fused ablation; depth-2 precision;
confirmed-relation type mix; verifiable (public-only) leakage; real-topology
replication on the three public datasets; a parameter-free baseline that ranks
candidates by the cosine similarity of their neighbour sets, with the average
clustering coefficient of each graph (Section 5.6). Figs. 3 and 4 are written to
`cold_start_leakage/figures/` (`fig_dose.pdf`, `fig_baselines.pdf`).

### `05_static_defenses.ipynb`

**Paper sections:** 5.9, 5.10
**Tables:** 6
**Figures:** 8

Output noise, temperature-based candidate pool and diversification against a static
attacker (privacy-utility trade-off), the calibrated Gaussian mechanism and the
differentially private top-k selection (one-shot Gumbel mechanism) over a range of
privacy budgets, and the rank-only (Borda) multi-query aggregation against output noise
on all private targets. The two
panels of Fig. 8 are written to `cold_start_leakage/figures/fig_defenses.pdf`.

### `06_adaptive_attacker.ipynb`

**Paper sections:** 5.10
**Tables:** 7, 8, B.3
**Figures:** none

Every defence re-evaluated against adaptive attackers: a rank-only attacker that
aggregates the lists with a Borda count, and stronger attackers that average noisy
scores or re-rank an expanded list by relevance (upper bounds). Also the adaptivity
gap, the noise-by-query-limit grid and the zCDP accounting of repeated Gaussian
releases. Node features are drawn before privacy labels, as in notebook 03.

### `07_inductive_utility.ipynb`

**Paper sections:** 5.8
**Tables:** B.2, B.4
**Figures:** data for 7 (drawn by notebook 08)

Train-only (held-out-edge) evaluation for LightGCN, FeatureGNN and MF-BPR: AUC on
training and held-out edges, held-out recall and NDCG, and cold-start leakage on the
same model, split into training and held-out neighbours. Section 7 of the notebook
sweeps the weight decay and the training length for LightGCN and MF-BPR
(`inductive/regularization_long.csv`, Table B.4).

### `08_paper_figures.ipynb`

**Paper sections:** 5.7, 5.8
**Tables:** none
**Figures:** 5, 6, 7

Draws Figs. 5-7 from `sweeps/results_long.csv` and `inductive/inductive_long.csv`.

## By table

| Table | Produced by | Content |
|---|---|---|
| 1 | literature summary | Threat models of related studies |
| 2 | configuration key | Experimental configurations |
| 3 | `04_attacker_metrics.ipynb` | Cold-start versus warm leakage, Configuration A |
| 4 | `03_multiseed_and_sweeps.ipynb` | Leakage across architectures and graph sizes, Configurations B and C |
| 5 | `04_attacker_metrics.ipynb` (models from `02`) | Leakage on the public datasets |
| 6 | `05_static_defenses.ipynb` | Gaussian mechanism and differentially private top-k selection: leakage and utility versus privacy budget |
| 7 | `06_adaptive_attacker.ipynb` | Static and adaptive attackers at the nominal operating points |
| 8 | `06_adaptive_attacker.ipynb` | Adaptive leakage under output noise and a query limit |
| A.1 | this document | Notebook-to-paper mapping |
| B.1 | `03_multiseed_and_sweeps.ipynb` | Scoring-rule sensitivity |
| B.2 | `07_inductive_utility.ipynb` | Held-out-edge evaluation |
| B.3 | `06_adaptive_attacker.ipynb` | Cumulative privacy budget and adaptive leakage of the Gaussian mechanism |
| B.4 | `07_inductive_utility.ipynb` | Regularization and training length for the train-only models |

## By figure

| Figure | Produced by | Content |
|---|---|---|
| 1 | drawn by hand (schematic) | Disclosure process for a cold-start observer |
| 2 | drawn by hand (schematic) | Threat model |
| 3 | `04_attacker_metrics.ipynb` | Precision@20 versus observer history |
| 4 | `04_attacker_metrics.ipynb` | Cold-start precision versus baselines |
| 5 | `08_paper_figures.ipynb` (data from `03`) | Parameter sweeps |
| 6 | `08_paper_figures.ipynb` (data from `03`) | Scoring-rule sensitivity |
| 7 | `08_paper_figures.ipynb` (data from `07`) | Held-out-edge evaluation |
| 8 | `05_static_defenses.ipynb` | Heuristic defences: (a) privacy-utility trade-off, (b) rank-only aggregation against output noise |
