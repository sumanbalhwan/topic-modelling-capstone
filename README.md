# Topic Modelling on India News Headlines (2001–2023)

Capstone project applying **modern (embedding-based) topic modelling** — BERTopic
(Sentence-Transformer embeddings → UMAP → HDBSCAN → c-TF-IDF) — to ~3.8 million
Times of India news headlines.

## Dataset
- **Source:** [India News Headlines Dataset](https://www.kaggle.com/datasets/therohk/india-headlines-news-dataset) (Kaggle)
- **Size:** ~3.8M rows, 2001–2023
- **Columns:** `publish_date` (YYYYMMDD), `headline_category`, `headline_text`

## Project structure
```
topic-modelling-capstone/
├── README.md
├── requirements.txt
├── .gitignore
├── data/
│   ├── raw/                 # gitignored — downloaded fresh via kagglehub, not committed
│   └── processed/           # small sampled/cleaned subsets — safe to commit
├── notebooks/
│   ├── 01_data_ingestion_eda.ipynb
│   ├── 02_preprocessing.ipynb
│   ├── 03_modelling_bertopic.ipynb
│   └── 04_evaluation_results.ipynb
├── src/                      # reusable helper functions imported into notebooks
├── outputs/
│   ├── figures/               # saved plots
│   └── models/                # gitignored if large — saved BERTopic models
└── reports/
    └── final_report.docx
```

## How to run (Google Colab)

**All storage is local to the Colab session — no Google Drive needed.** Because of this, **notebooks 01 through 04 must be run in one continuous Colab session** (local `/content/` storage does not persist across separate sessions the way Drive did). If you need to stop partway, re-run from notebook 01 in a fresh session rather than trying to resume a later notebook alone.

1. Open notebook 01 in Colab (`File → Open notebook → GitHub`, paste this repo URL, or `File → Upload notebook`).
2. Run its setup cell — creates the local working folder and (in notebook 01) authenticates with Kaggle via `kagglehub` (one-time browser login).
3. Run notebooks 01 → 02 → 03 → 04 in order, all in the same session/tab.
4. Notebook 04's final cell packages every result (processed data, figures, trained models) into one zip, sized to stay well under GitHub's 100MB-per-file limit. Download it from the Colab file browser (left sidebar), extract into your local repo root, then `git add`, `commit`, `push` — results now live directly in the repo, no Drive link needed for teammates or graders.
5. To reproduce on Kaggle Notebooks instead: create a new notebook, add the dataset via "Add Input" search (skip the kagglehub cell), and adjust `BASE_DIR` to a Kaggle working path.

**Note on model files:** saved BERTopic models exclude the underlying sentence-Transformer's weights (`save_embedding_model=False`) specifically to keep them under GitHub's size limit — this only matters if you want to embed brand-new, never-seen headlines later; it doesn't affect any analysis this project does on the existing dataset.

## Reproducibility notes
- Random seeds are fixed (`RANDOM_STATE = 42`) everywhere sampling/UMAP/HDBSCAN is used.
- `requirements.txt` pins package versions used during development.
- Full 3.8M-row experiments are flagged separately from the working sample so the notebooks
  stay runnable on a laptop-grade Colab session; the same code scales to the full dataset with GPU.

## Team workflow (git)
```bash
git clone <your-repo-url>
cd topic-modelling-capstone
git checkout -b <your-name>/<feature>
# ... edit notebooks ...
nbstripout notebooks/*.ipynb   # strip outputs before committing (keeps diffs clean)
git add .
git commit -m "Describe what changed"
git push origin <your-name>/<feature>
# open a PR into main for teammates to review
```
