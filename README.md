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
1. Open a notebook from `notebooks/` in Colab (`File → Open notebook → GitHub`, paste this repo URL).
2. Run the first setup cell — it installs dependencies and authenticates with Kaggle via `kagglehub`
   (first run will prompt a one-time browser login; no manual `kaggle.json` needed).
3. Run cells top to bottom. Each notebook saves its output (processed data / figures / models)
   into `data/processed/`, `outputs/figures/`, `outputs/models/` respectively, so later notebooks
   pick up where earlier ones left off.
4. To reproduce on Kaggle Notebooks instead: create a new notebook, add the dataset via
   "Add Input" search, and skip the kagglehub download cell (data will already be at
   `/kaggle/input/india-headlines-news-dataset/`).

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
