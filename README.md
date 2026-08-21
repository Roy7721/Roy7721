# Rana Roy

**Machine Learning & AI Engineer · MSc Statistics**

I build end-to-end ML systems: training pipelines, model registries, CI/CD, APIs, and the evaluation harnesses that verify they work. My current focus is retrieval-augmented generation and LLM applications. My background is statistics, which is where I learned to design the tests before trusting the results.

Open to Data Science, ML Engineer, and AI Engineer roles.

[![LinkedIn](https://img.shields.io/badge/LinkedIn-%230077B5.svg?logo=linkedin&logoColor=white)](https://www.linkedin.com/in/rana-roy-4771b5282/) [![email](https://img.shields.io/badge/Email-D14836?logo=gmail&logoColor=white)](mailto:ranaroy4007@gmail.com)

---

## Projects

### AI-Powered Investor Intelligence Platform
**RAG system for SEC annual reports — ask questions about a company's financials in plain English.**

Ingests 10-K filings and answers questions with figures pulled directly from the financial statements. Built and verified across Apple, Microsoft, and Tesla filings.

- Full pipeline: PDF → markdown → structure-aware chunking → LLM table-to-text conversion → vector store → retrieval → answer generation
- Diagnosed a retrieval failure where financial tables were indexed without their captions and truncated at the embedding model's 512-token limit, dropping entire rows before they ever reached the database. Resolved with caption pairing, heading propagation, and a table-guaranteed retrieval merge.
- Built a 34-question evaluation set with hand-verified ground truth from the source filings. One third of the questions are unanswerable by design (wrong company, non-existent segment, false premise) to measure hallucination and refusal behaviour.
- Retrieval and generation scored separately, since an end-to-end accuracy number cannot identify which layer failed. Current score: 31/34.

`Python` · `LangChain` · `ChromaDB` · `FastAPI` · `Streamlit` · `Groq` · `OpenRouter` · `PyMuPDF`

<!-- add repo link -->

---

### AskTheVid — YouTube Video Chatbot
**Ask questions about any YouTube video and get answers with timestamped citations.**

- RAG over video transcripts, with per-video caching so repeat sessions skip re-embedding
- Every answer cites the exact timestamp in the source video, so claims are checkable against the original
- FastAPI backend, Streamlit frontend, local sentence-transformer embeddings

`Python` · `LangChain` · `ChromaDB` · `FastAPI` · `Streamlit` · `Groq`

[Repository →](https://github.com/Roy7721/Yt_chatbot)

---

### YouTube Comment Sentiment — Chrome Extension + MLOps Pipeline
**A browser extension that shows live sentiment analysis on any video's comment section, served by an automated ML pipeline.**

- **Model:** Logistic Regression with custom spaCy features, hyperparameters tuned with Optuna — macro F1 **0.876**
- **Evaluation:** built a ~400-comment held-out set from the YouTube Data API, with a pilot annotation round to check labelling reliability first (Cohen's κ = **0.822**)
- **Pipeline:** GitHub Actions running DVC reproduction → automated model tests as a pre-promotion gate → MLflow registry → Docker image build and publish. Models reach the registry only after passing the gate.
- **Serving:** Flask prediction API, containerised and served with Waitress

`Python` · `scikit-learn` · `spaCy` · `Optuna` · `MLflow` · `DVC` · `Flask` · `Docker` · `GitHub Actions` · `JavaScript`

<!-- add repo link -->

---

## Tech Stack

**Languages & Core**

![Python](https://img.shields.io/badge/python-3670A0?style=for-the-badge&logo=python&logoColor=ffdd54) ![NumPy](https://img.shields.io/badge/numpy-%23013243.svg?style=for-the-badge&logo=numpy&logoColor=white) ![Pandas](https://img.shields.io/badge/pandas-%23150458.svg?style=for-the-badge&logo=pandas&logoColor=white) ![scikit-learn](https://img.shields.io/badge/scikit--learn-%23F7931E.svg?style=for-the-badge&logo=scikit-learn&logoColor=white)

**LLM & AI Engineering**

![LangChain](https://img.shields.io/badge/LangChain-1C3C3C?style=for-the-badge&logo=langchain&logoColor=white) ![ChromaDB](https://img.shields.io/badge/ChromaDB-FFCE44?style=for-the-badge&logoColor=black) ![HuggingFace](https://img.shields.io/badge/Hugging%20Face-FFD21E?style=for-the-badge&logo=huggingface&logoColor=black) ![spaCy](https://img.shields.io/badge/spaCy-09A3D5?style=for-the-badge&logo=spacy&logoColor=white)

**Deep Learning**

![PyTorch](https://img.shields.io/badge/PyTorch-%23EE4C2C.svg?style=for-the-badge&logo=PyTorch&logoColor=white) ![Keras](https://img.shields.io/badge/Keras-%23D00000.svg?style=for-the-badge&logo=Keras&logoColor=white)

**MLOps & Deployment**

![MLflow](https://img.shields.io/badge/mlflow-%23d9ead3.svg?style=for-the-badge&logo=numpy&logoColor=blue) ![DVC](https://img.shields.io/badge/DVC-13ADC7?style=for-the-badge&logo=dvc&logoColor=white) ![Docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white) ![GitHub Actions](https://img.shields.io/badge/github%20actions-%232671E5.svg?style=for-the-badge&logo=githubactions&logoColor=white) ![GitLab CI](https://img.shields.io/badge/gitlab%20CI-%23181717.svg?style=for-the-badge&logo=gitlab&logoColor=white)

**Backends & APIs**

![FastAPI](https://img.shields.io/badge/FastAPI-005571?style=for-the-badge&logo=fastapi) ![Flask](https://img.shields.io/badge/flask-%23000.svg?style=for-the-badge&logo=flask&logoColor=white) ![Streamlit](https://img.shields.io/badge/Streamlit-FF4B4B?style=for-the-badge&logo=streamlit&logoColor=white) ![Postman](https://img.shields.io/badge/Postman-FF6C37?style=for-the-badge&logo=postman&logoColor=white)

**Databases**

![MySQL](https://img.shields.io/badge/mysql-4479A1.svg?style=for-the-badge&logo=mysql&logoColor=white) ![MongoDB](https://img.shields.io/badge/MongoDB-%234ea94b.svg?style=for-the-badge&logo=mongodb&logoColor=white)

**Analytics & Statistical Tools**

![Plotly](https://img.shields.io/badge/Plotly-%233F4F75.svg?style=for-the-badge&logo=plotly&logoColor=white) ![Power BI](https://img.shields.io/badge/power_bi-F2C811?style=for-the-badge&logo=powerbi&logoColor=black) ![R](https://img.shields.io/badge/r-%23276DC3.svg?style=for-the-badge&logo=r&logoColor=white) ![STATA](https://img.shields.io/badge/STATA-1a5f8a?style=for-the-badge&logoColor=white) ![SPSS](https://img.shields.io/badge/SPSS-C8102E?style=for-the-badge&logoColor=white)

---

## Research

**MSc, Department of Statistics — University of Rajshahi**

Two papers in progress from my thesis work:

- **Public health** — statistical modelling of health outcome data
- **Econometrics** — quantitative frameworks for economic and behavioural trends

---

## GitHub

![](https://github-readme-stats.shion.dev/api?username=Roy7721&theme=dark&hide_border=true&count_private=false)
![](https://github-readme-stats.shion.dev/api/top-langs/?username=Roy7721&theme=dark&hide_border=true&layout=compact)

---

Outside of work I play guitar. During university I was part of the bands **Jebel** and **Onkuron**.
