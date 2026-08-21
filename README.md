# cold-start-leakage

Companion code for:

> Küçük, D., Kutsal, M., Ertam, F. **Cold-Start Attack: A Simulation Study of Social
> Graph Leakage in Recommender Systems.** *Computers & Security* (under review).

A recommender trained only to be relevant, with no rule anywhere that says "suggest
the target's neighbours", still exposes a large fraction of a private account's true
social neighbourhood to an observer with no history. This repository reproduces every
quantitative result, table and figure in the paper: the measurement, the defences
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

A CPU is sufficient throughout. The full sequence takes roughly two to three hours on
a modern laptop, dominated by the multi-seed sweeps in notebook 03 and the adaptive
attacker in notebook 06.

Notebooks also run unchanged on Google Colab: each one detects Colab, mounts Drive and
uses `MyDrive/cold_start_leakage` as its working root. Outside Colab the same root
resolves to `./cold_start_leakage` at the repository root, so notebooks can be opened
from `notebooks/` without any path editing.

## Layout

```
notebooks/     the analysis, in execution order (01 to 07)
docs/          notebook to paper mapping: which notebook produces which table and figure
archive/       superseded notebooks, kept for provenance only
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
  inductive/   transductive versus inductive utility results
```

Which of these exist locally depends on which notebooks have been run. Notebooks 01 and
02 populate `data/` and `models/`; the later notebooks write into `sweeps/`, `adaptive/`
and `inductive/` respectively.

## Notebooks

Each notebook is self-contained: it redeclares the small set of shared classes and
functions it needs, so any one can be opened on its own provided the artefacts from
earlier notebooks exist under `cold_start_leakage/`.

| Notebook | What it produces | Paper sections |
|---|---|---|
| `01_graphs_and_features.ipynb` | SBM and LFR generators, SNAP loaders (ego-Facebook, Twitter-ego BFS sample, email-Eu-core), node labelling and features, ground-truth `N(t)` | 4.2 |
| `02_recommender_training.ipynb` | LightGCN and FeatureGNN training (BPR), link-prediction AUC competence check, cold-to-warm gated `Recommender` | 4.3, 4.4 |
| `03_multiseed_and_sweeps.ipynb` | Multi-seed bands, parameter sweeps, formula variants, MF-BPR recommender, n=15,000 scale test | 5.11 to 5.14 |
| `04_attacker_metrics.ipynb` | Cold versus warm precision and recall, attacker advantage, dose-response, ablations, real-topology replications | 4.5, 5.1 to 5.6, 5.10 |
| `05_static_defenses.ipynb` | Output noise, broadened pool, diversification, and the calibrated Gaussian DP mechanism | 5.7, 5.8 |
| `06_adaptive_attacker.ipynb` | Every defence against a query-aggregating attacker, the adaptivity gap, cumulative privacy budget | 5.7, 5.8 |
| `07_inductive_utility.ipynb` | Train-only (inductive) evaluation and the honest utility frontier | 5.9 |

`docs/notebook_to_paper_map.md` gives the full table-by-table and figure-by-figure
mapping, including which results are schematics rather than computed output.

## What the measurement actually is

- **Target.** A private account `t`. Ground truth is its true neighbourhood `N(t)`,
  the union of its followers and the accounts it follows.
- **Observer.** An account that queries the recommender for suggestions. A *cold*
  observer has no interaction history; a *warm* observer has `h` prior contacts.
- **Metric.** Precision@20 and recall@20 of the returned list against `N(t)`, averaged
  over every private target with degree at least 4.
- **Baselines.** Random, popularity-ranked and same-community candidate selection,
  which is what separates a target-specific signal from generic homophily.

The leakage arises because a cold-start query has no personal history to rank against,
so the recommender falls back on graph context around the queried target. Nothing in
the pipeline is adversarial by design; the attack is the ordinary output surface read
in an unintended direction.

## Reproducibility notes

- Random seeds are fixed throughout. Notebooks that report a mean and confidence
  interval regenerate the topology per seed, so `n_targets` varies slightly between
  seeds.
- `sweeps/results_long.csv`, `sweeps/results_large_scale.csv` and
  `adaptive/adaptive_long.csv` are the long-format sources behind the paper's result
  tables. Recomputing the static-attacker cells of Table 6 from `adaptive_long.csv`
  agrees to within 0.004, which is inside the seed-to-seed variation those cells'
  confidence intervals span. The undefended LFR value in that file is 0.321, which is
  what Tables 6 to 8 report.
- Notebook outputs are cleared in version control so that diffs show code changes
  rather than re-execution noise.
- `archive/02b_multiseed_param_sweep.ipynb` is the predecessor of
  `notebooks/03_multiseed_and_sweeps.ipynb`. It is kept for provenance and is not part
  of the reproduction path; the successor contains its analysis plus the formula-variant,
  MF-BPR and scale experiments, and writes the same `results_long.csv`.

## Ethics

The work measures a privacy weakness in a class of deployed system and proposes
defences against it. The measurement is deliberately confined to simulation and to open
anonymised datasets: probing a live platform would itself harm the users whose exposure
is being quantified, and would breach the platforms' terms of service. No attempt is
made to identify any individual, and no data beyond the public SNAP releases is used.

## Citation

```bibtex
@article{kucuk2026coldstart,
  title   = {Cold-Start Attack: A Simulation Study of Social Graph Leakage in
             Recommender Systems},
  author  = {K\"u\c{c}\"uk, D\"uzg\"un and Kutsal, M\"ucahit and Ertam, Fatih},
  journal = {Computers \& Security},
  year    = {2026},
  note    = {Under review}
}
```

## License

MIT, see `LICENSE`.
