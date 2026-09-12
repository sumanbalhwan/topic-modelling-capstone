# Topic Modelling on India News Headlines (2001–2023)

Capstone project applying **modern (embedding-based) topic modelling** — BERTopic
(Sentence-Transformer embeddings → UMAP → HDBSCAN → c-TF-IDF) — to a stratified sample of
Times of India news headlines spanning 2001–2023.

**Everything needed to review this project is already in this repository** — no execution
required. `reports/` has the full write-up with all figures and tables; `data/` and `outputs/`
have the actual committed data, trained models, and results. Running the notebooks yourself is
only necessary if you want to independently verify the pipeline end-to-end.

## Dataset
- **Source:** [India News Headlines Dataset](https://www.kaggle.com/datasets/therohk/india-headlines-news-dataset) (Kaggle)
- **Full size:** ~3.8M rows, 2001–2023 — a stratified-by-year sample of 300,000 was used for
  preprocessing and modelling (see the report's Procedure section for why)
- **Columns:** `publish_date` (YYYYMMDD), `headline_category`, `headline_text`

## Repository structure
```
topic-modelling-capstone/
├── README.md
├── requirements.txt
├── .gitignore
├── data/
│   ├── raw/                    # empty — raw dataset re-downloaded fresh via kagglehub if run
│   └── processed/              # sample, preprocessed data, and topic-assignment CSVs
├── notebooks/
│   ├── 01_data_ingestion_eda.ipynb
│   ├── 02_preprocessing.ipynb
│   ├── 03_modelling_bertopic.ipynb
│   └── 04_evaluation_results.ipynb
├── outputs/
│   ├── figures/                # all charts referenced in the report
│   ├── models/                 # trained BERTopic models (safetensors format) + result tables
│   └── embeddings/              # gitignored — too large to commit, recomputed if run fresh
├── reports/
│   └── Topic_Modelling_Capstone_Report_Final_ver2.docx
└── combined_full_pipeline_with_results.ipynb   # optional — see note at the bottom
```

All four notebooks in `notebooks/` already contain **real, executed output** — every table,
chart, and printed result reflects an actual run, not placeholder text.

## How to run (only needed for independent verification)

**Important — this project uses local Colab session storage, not Google Drive.** This means
notebooks 01 → 02 → 03 → 04 must be run **in order, within one continuous Colab session** —
closing the tab or opening a notebook in a separate session breaks the handoff between stages,
since local `/content/` storage does not persist across separate sessions.

### Option A — quick verification (a few minutes, recommended)
Clone this repository into a fresh Colab session (brings the already-computed data and models
onto the session's disk), then run `notebooks/04_evaluation_results.ipynb`. It loads the
already-trained model directly and reproduces the reported results without recomputing anything.

```python
from getpass import getpass
username = "sumanbalhwan"
token = getpass("Enter your GitHub Personal Access Token: ")
!git clone https://{username}:{token}@github.com/sumanbalhwan/topic-modelling-capstone.git
```
(Only needed if the repository is private — a public repo can just be cloned directly with
`!git clone https://github.com/sumanbalhwan/topic-modelling-capstone.git`.)

### Option B — full reproduction from scratch (several hours)
Open `notebooks/01_data_ingestion_eda.ipynb` in a fresh Colab session and run notebooks
01 → 02 → 03 → 04 **in that order, in the same session, without closing the tab in between**.

**What to expect, stage by stage:**
| Stage | Approx. time | Notes |
|---|---|---|
| 01 — Data Ingestion & EDA | ~5–10 min | Downloads dataset via Kaggle (one-time browser login) |
| 02 — Preprocessing | ~2–5 min | No heavy computation |
| 03 — Modelling | **Several hours** | Embedding computation is the slowest step; the 22-config grid search and the final full-dataset model refit follow |
| 04 — Evaluation & Results | ~5–10 min | Loads the model produced by notebook 03 |

**GPU is optional, not required.** It only speeds up notebook 03's embedding step; everything
else (UMAP, HDBSCAN, grid search) runs on CPU regardless of runtime type.

### Resilience features built into notebook 03
Given the length of a full run, notebook 03 includes:
- **Chunked, resumable embedding computation** — an interruption partway through only costs the
  current chunk (~20,000 documents), not the whole computation.
- **A lightweight Drive checkpoint** for the grid search's results specifically (optional,
  defaults on) — if the session is interrupted mid-grid-search, completed configurations are
  restored automatically rather than redone.
- **Memory-conservative settings** for the final full-dataset model fit (UMAP `low_memory=True`;
  HDBSCAN `algorithm='boruvka_kdtree'`) — this step is the most memory-intensive in the pipeline
  and caused RAM exhaustion during development before these settings were added.

None of this is required reading to review the project — it's documented here so that anyone
attempting a full from-scratch run understands what these mechanisms do if encountered.

## On reproducibility and run-to-run variation

This pipeline was run three times independently, from scratch, in separate sessions, with all
three producing genuinely coherent, real-world-interpretable topics and the same overall
conclusions (see the report's Reproducibility section, 4.4, for full details):

| Run | Topics | Outlier rate | Notes |
|---|---|---|---|
| 1 | 45 | 47.6% | Original successful run |
| 2 | 45 | 46.3% | **This is the run committed to this repository and reported in the write-up** |
| 3 | 43 | 45.4% | Verification run using added memory-safety settings |

Exact numbers vary modestly across runs due to (a) floating-point non-determinism in the
embedding step's multi-threaded CPU computation, and (b) for Run 3, the deliberate memory-safety
configuration change. This is expected, and does not indicate an error — the consistency of
topics and conclusions across all three runs is itself evidence of the pipeline's stability.

## A note on the combined notebook

`combined_full_pipeline_with_results.ipynb`, at the repository root, concatenates all four
notebooks into one file with Run 3's real results embedded throughout. It exists purely as a
convenience for running the entire pipeline in a single Colab session without manually managing
four separate files — it is **not** a replacement for the four individual notebooks in
`notebooks/`, which remain the project's canonical, organized deliverables.

## Dependencies
See `requirements.txt`. Key libraries: `bertopic`, `sentence-transformers`, `umap-learn`,
`hdbscan`, `gensim`, `kagglehub`, `pandas`, `matplotlib`, `seaborn`, `wordcloud`.
