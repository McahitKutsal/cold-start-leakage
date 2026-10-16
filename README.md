# Cold-Start Leakage

Companion code for:

> Küçük, D., Kutsal, M., Ertam, F. **Cold-Start Attack: A Simulation Study of Social
> Graph Leakage in Recommender Systems.** Manuscript under review.

A recommender trained only to be relevant, with no rule anywhere that says "suggest
the target's neighbours", still exposes a large fraction of a private account's true
social neighbourhood to an observer with no history, provided that the platform ranks
suggestions by similarity to the viewed profile. This repository reproduces every
quantitative result, table and data figure in the paper: the measurement, the defences
evaluated against it, and the adaptive attacker that defeats most of them.

**Nothing here touches a live platform.** Every result is produced on synthetic graphs
or on open, already-anonymised SNAP datasets. No API is queried, no account is probed,
and no real private profile is accessed. Notebook 01 downloads the SNAP archives from
their canonical location rather than redistributing them.

## Quick start

```bash
git clone https://github.com/McahitKutsal/cold-start-leakage.git
cd cold-start-leakage
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
jupyter lab            # then run notebooks/ in numerical order
```

Notebooks 03, 06 and 07 have a `MODE` switch at the top. `MODE = "quick"` is a smoke
test; the paper reports `MODE = "full"`.

A CPU is sufficient throughout. The full sequence takes roughly two to three hours on
a modern laptop, dominated by the multi-seed sweeps and the n=15,000 scale test in
notebook 03 and by training the 5,000-node Twitter sample in notebook 02.

Notebooks also run unchanged on Google Colab: each one detects Colab, mounts Drive and
uses `MyDrive/cold_start_leakage` as its working root. Outside Colab the same root
resolves to `./cold_start_leakage` at the repository root, so notebooks can be opened
from `notebooks/` without any path editing.

## Layout

```
notebooks/     the analysis, in execution order (01 to 08)
docs/          notebook to paper mapping: which notebook produces which table and figure
requirements.txt
CITATION.cff
LICENSE
```

Generated artefacts (graphs, trained embeddings, sweep outputs, figures) are written to
`cold_start_leakage/` at the repository root and are deliberately **not** committed:
every one is reproducible by running the notebooks in order, and the SNAP source
archives should be downloaded from SNAP rather than mirrored here.

```
cold_start_leakage/
  data/        one directory per graph: edges.csv, nodes.csv, features.npy, graph.pkl, meta.json
  models/      trained embeddings (.npy), state dicts (.pt), per-model meta.json
  sweeps/      multi-seed and parameter-sweep results (results_long.csv, results_large_scale.csv)
  adaptive/    adaptive-attacker results (adaptive_long.csv) and the three-panel figure
  inductive/   train-only (held-out-edge) results (inductive_long.csv)
  figures/     paper Figs. 3-8: Figs. 3, 4 (notebook 04), 5-7 (notebook 08), 8 (notebook 05)
```

## Notebooks

Each notebook is self-contained: it redeclares the small set of shared classes and
functions it needs, so any one can be opened on its own provided the artefacts from
earlier notebooks exist under `cold_start_leakage/`. Section, table and figure numbers
refer to the current version of the paper.

| Notebook | What it produces | Paper |
|---|---|---|
| `01_graphs_and_features.ipynb` | SBM and LFR generators of Configuration A, SNAP loaders (ego-Facebook, Twitter-ego BFS sample, email-Eu-core), privacy labels, node features, ground-truth `N(t)` | Section 4.2 |
| `02_recommender_training.ipynb` | LightGCN and FeatureGNN training (BPR) for Configuration A and the three public datasets, link-prediction AUC; ego-Facebook is trained in its own re-seeded cell | Sections 4.3, 5.1 |
| `03_multiseed_and_sweeps.ipynb` | Configuration B: ten-seed bands for LightGCN, FeatureGNN and MF-BPR, one-factor-at-a-time sweeps, scoring-rule variants (warm-path and cold-path); Configuration C: n=15,000 scale test | Sections 5.5, 5.7; Table 4, Table B.1 |
| `04_attacker_metrics.ipynb` | Cold versus warm precision and recall, baselines, dose-response, structural versus feature-fused ablation, depth-2 precision, relation mix, verifiable (public-only) leakage, warm confidence intervals, real-topology replication, parameter-free shared-neighbour baseline | Sections 5.2-5.4, 5.6; Tables 3, 5; Figs. 3, 4 |
| `05_static_defenses.ipynb` | Output noise, temperature-based candidate pool, diversification, the calibrated Gaussian mechanism and differentially private top-k selection against a static attacker; rank-only (Borda) multi-query aggregation | Sections 5.9, 5.10; Table 6; Fig. 8 |
| `06_adaptive_attacker.ipynb` | Every defence against a rank-only (Borda) attacker and against a score-averaging or re-ranking attacker, the adaptivity gap, the rate-limit grid, zCDP accounting of repeated Gaussian releases | Section 5.10; Tables 7, 8, B.3 |
| `07_inductive_utility.ipynb` | Train-only (held-out-edge) evaluation for LightGCN, FeatureGNN and MF-BPR, the split of leakage into training and held-out neighbours, and a weight-decay and training-length sweep | Section 5.8; Tables B.2, B.4 |
| `08_paper_figures.ipynb` | Paper Figs. 5-7 drawn from the CSV outputs of notebooks 03 and 07 | Figs. 5, 6, 7 |

`docs/notebook_to_paper_map.md` gives the full table-by-table and figure-by-figure
mapping, including which figures are schematics rather than computed output.

## What the measurement actually is

- **Target.** A private account `t`. Ground truth is its true neighbourhood `N(t)`,
  the union of its followers and the accounts it follows.
- **Observer.** An account that receives suggestions on the target's profile. A *cold*
  observer has no interaction history; a *warm* observer has `h` prior contacts.
- **Serving rule.** For a cold observer the score of a candidate is its cosine
  similarity to the target's embedding. This context-anchored fallback is the central
  assumption of the study; if a platform does not anchor suggestions on private
  profiles, the channel does not exist.
- **Metric.** Precision@20 and recall@20 of the returned list against `N(t)`, averaged
  over every private target with degree at least 4.
- **Baselines.** Random, popularity-ranked and same-community candidate selection,
  which is what separates a target-specific signal from generic homophily.

## Reproducibility notes

- Random seeds are fixed throughout. Notebooks that report a mean and confidence
  interval regenerate the topology per seed, so `n_targets` varies slightly between
  seeds.
- The paper uses three synthetic configurations (A: single seed, notebooks 01, 02, 04,
  05; B: ten seeds, notebooks 03, 06, 07; C: n=15,000, notebook 03). They differ in
  generator parameters and training length, so absolute values should only be compared
  within one configuration. The paper's Table 2 lists all settings.
- LightGCN and MF-BPR results are bit-identical across re-runs on the same hardware
  with the package versions in `requirements.txt`. FeatureGNN results can differ by up
  to about 0.01 between runs, and models trained on the larger public graphs can differ
  in the third decimal across hardware and thread settings, because multi-threaded
  floating-point sums are not deterministic.
- `sweeps/results_long.csv`, `sweeps/results_large_scale.csv`,
  `adaptive/adaptive_long.csv`, `inductive/inductive_long.csv` and
  `inductive/regularization_long.csv` are the long-format sources behind the paper's
  result tables. Re-running notebook 06 reproduces Tables 7, 8 and B.3 of the paper.
- Multi-seed results are reported as mean +- the half-width of a 95% confidence interval
  based on the t distribution (m - 1 degrees of freedom for m seeds).
- Notebook 06 draws node features before privacy labels, like notebook 03, so its
  undefended leakage equals the Configuration B values of Table 4. Notebook 07 draws
  the labels first, so its private targets differ from those of notebook 03.
- The n=15,000 scale test (last cell of notebook 03) builds the normalized adjacency
  as a sparse tensor (`norm_adj_sparse`). The values are identical to the dense matrix
  used for the 700-node graphs; only the summation order differs. With the dense matrix,
  a single seed would take hours on a CPU.
- The email-Eu-core loader keeps the 642 self-loops of the original file. Because a
  target is never suggested to itself, this slightly lowers the attainable recall on
  that graph and does not affect precision.
- Notebook outputs are cleared in version control so that diffs show code changes
  rather than re-execution noise.

## Ethics

The work measures a privacy weakness in a class of deployed system and proposes
defences against it. The measurement is deliberately confined to simulation and to open
anonymised datasets: probing a live platform would itself harm the users whose exposure
is being quantified, and would breach the platforms' terms of service. No attempt is
made to identify any individual, and no data beyond the public SNAP releases is used.

## Citation

```bibtex
@unpublished{kucuk2026coldstart,
  title  = {Cold-Start Attack: A Simulation Study of Social Graph Leakage in
            Recommender Systems},
  author = {K\"u\c{c}\"uk, D\"uzg\"un and Kutsal, M\"ucahit and Ertam, Fatih},
  year   = {2026},
  note   = {Manuscript under review}
}
```

## License

MIT, see `LICENSE`.
