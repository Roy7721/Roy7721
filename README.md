# Rana Roy

**Machine Learning & AI Engineer · MSc Statistics**

I build end-to-end ML systems: training pipelines, model registries, CI/CD, APIs, and the evaluation harnesses that verify they work. My current focus is retrieval-augmented generation and LLM applications. My background is statistics, which is where I learned to design the tests before trusting the results.

Open to Data Science, ML Engineer, and AI Engineer roles.

**Three of these are running live right now** — click and try them:

[![Investor Intelligence](https://img.shields.io/badge/Investor_Intelligence-live-0f7b52?style=for-the-badge)](https://investor-intelligence.ashysmoke-d4f578eb.koreacentral.azurecontainerapps.io) [![TripMate AI](https://img.shields.io/badge/TripMate_AI-live-0f7b52?style=for-the-badge)](https://tripmate-ai.ashysmoke-d4f578eb.koreacentral.azurecontainerapps.io) [![Sentiment API](https://img.shields.io/badge/Sentiment_Model-live-0f7b52?style=for-the-badge)](https://yt-sentiment-api.ashysmoke-d4f578eb.koreacentral.azurecontainerapps.io)

*All three scale to zero, so the first request after an idle spell waits a few seconds for the container to wake.*

[![Portfolio](https://img.shields.io/badge/Portfolio-roy7721.github.io-1A4D8F?logo=github&logoColor=white)](https://roy7721.github.io) [![LinkedIn](https://img.shields.io/badge/LinkedIn-%230077B5.svg?logo=linkedin&logoColor=white)](https://www.linkedin.com/in/rana-roy-4771b5282/) [![email](https://img.shields.io/badge/Email-D14836?logo=gmail&logoColor=white)](mailto:ranaroy4007@gmail.com)

---

## Projects

### AI-Powered Investor Intelligence Platform
**RAG system for SEC annual reports — ask questions about a company's financials in plain English.**

Ingests 10-K filings and answers with figures pulled directly from the financial statements, each one traceable to the table row it was read from. Built and verified across Apple, Microsoft, and Tesla FY2024 filings.

- Full pipeline: PDF → markdown → structure-aware chunking → table-aware indexing → vector store → company-and-year-filtered retrieval → answer generation. **3,213 chunks**, with 0 characters lost, 0 duplicated, and 0 of 3,213 table rows altered.
- **18/18 numeric KPIs correct**, and **18/18 traceable** — an automated `looks_wrong()` check reformats every extracted number and asserts it appears inside its own source quote, which catches a value the model inferred rather than read. A second check subtracts liabilities from assets and asserts the remainder is plausible, catching two individually believable figures that contradict each other.
- **The negative case is the one I care about.** Microsoft's filing is an annual report, not a 10-K, and has no Item 1A. An earlier version matched on the phrase *"Risk Factors"* — which appears three times in that document, every one a cross-reference — declared the section present, and let the model substitute market-risk categories in its place. Matching on `Item 1A`, a structural identifier rather than prose, made the system report the section as absent instead of inventing it.
- **Retrieval, measured before and after.** `operating_income` was returning the wrong chunk. Rank of the correct chunk improved from 4→1 (Microsoft), 10→2 (Tesla 2024) and 26→3 (Tesla 2025) after pairing tables with their captions and querying several income-statement line items together.
- Deployed to Azure Container Apps behind FastAPI; cold start cut **11×**, from 231s to 20.5s, by baking the vector store and KPI cache into the image.

`Python` · `FastAPI` · `ChromaDB` · `Google Gemini` · `OpenRouter` · `Docker` · `Azure Container Apps` · `PyMuPDF`

[▶ Try it live](https://investor-intelligence.ashysmoke-d4f578eb.koreacentral.azurecontainerapps.io) · [Repository →](https://github.com/Roy7721/Investor_intelligence_bot)

---

### TripMate AI — multi-agent travel planner with human-in-the-loop approval
**A supervisor routes each request to only the specialists it needs, and the draft itinerary pauses for your approval before the final plan is written.**

- **Supervisor routing, not a fixed pipeline.** One LLM call returns strict JSON naming which specialists a request needs — flights, hotels, weather, budget — along with the trip constraints it extracted. Conditional edges then walk only those agents, so a hotel-only question never triggers a flight lookup. If that JSON fails to parse it falls back to running everything: slow beats broken.
- **Human-in-the-loop that survives a restart.** LangGraph's `interrupt()` pauses the graph on the draft itinerary and writes the paused run to PostgreSQL, so it outlives the process. `POST /api/travel/approve` resumes it — approve it, or send it back with feedback the final agent applies.
- **Three kinds of MCP server, one written from scratch.** A hosted HTTP server (Tavily), a third-party stdio server launched with `uvx` (AviationStack), and a weather server I built with `FastMCP` over OpenWeatherMap. An input guardrail refuses off-topic and harmful requests before any specialist runs — a bank-hacking prompt is refused in under 2s with zero agents invoked.
- **The bug I learned the most from.** `TravelState` declared `selected_agent`; the code wrote `selected_agents`. LangGraph **silently drops keys that are not in the state schema**, so the supervisor's choice was discarded and every request skipped all four specialists. I found it by running the real graph against a fake LLM and an in-memory checkpointer — no API calls, no cost, runs in a second.
- **Deployed** to Azure Container Apps with the image pinned to the commit SHA rather than `latest`, API keys as Container Apps secrets, and scale-to-zero (~6s cold start). A full plan takes **66s** to the draft and **5s** after approval — faster than on my own machine, because the checkpoint database sits a short hop away rather than across the Pacific.

`Python` · `LangGraph` · `Model Context Protocol` · `FastAPI` · `PostgreSQL` · `Groq` · `Docker` · `Azure Container Apps`

**[▶ Try it live](https://tripmate-ai.ashysmoke-d4f578eb.koreacentral.azurecontainerapps.io)** · [Repository →](https://github.com/Roy7721/TripMate_AI) · [How I built it, version by version →](https://github.com/Roy7721/TripMate_AI/blob/master/JOURNEY.md)

---

### YouTube Comment Sentiment — Chrome Extension + MLOps Pipeline
**A browser extension that shows live sentiment analysis on any video's comment section, served by an automated ML pipeline.**

- **Model selection as an engineering decision.** Compared seven algorithms with Optuna-tuned hyperparameters, testing under/over-sampling and ADASYN for class imbalance. TF-IDF with elastic-net Logistic Regression won — **0.885 accuracy, 0.875 macro F1** — not for the leaderboard, but for its accuracy-to-latency trade-off: fast inference and a small memory footprint, which is what a responsive browser plugin needs. The model is deliberately the smallest part of this project; swapping in a transformer is a data and compute question, and the pipeline around it would not change.
- **Pipeline:** a 5-stage reproducible DVC pipeline. GitHub Actions runs `dvc repro` → automated model tests as a pre-promotion gate (valid labels, accuracy ≥ 0.80) → MLflow registry. A regressed model never reaches the plugin.
- **Deployment:** the same pipeline builds the image and deploys to Azure Container Apps on every green run, pinned to the commit SHA rather than `latest` — so every live deploy traces back to an exact commit.
- **Serving:** Flask prediction API behind Waitress, consumed by a Manifest V3 Chrome extension that analyses the comment section in place.
- **Known limitation, stated honestly:** trained on Reddit comments, used on YouTube. It reads explicit sentiment well and understated negativity ("not helpful", "clickbait") less well — a vocabulary gap from the training data, which I found by probing the deployed model rather than by reading the test score.

`Python` · `scikit-learn` · `spaCy` · `Optuna` · `MLflow` · `DVC` · `Flask` · `Docker` · `GitHub Actions` · `Azure` · `JavaScript`

**[▶ Try the model live](https://yt-sentiment-api.ashysmoke-d4f578eb.koreacentral.azurecontainerapps.io)** — paste in your own comments, no install needed. Or hit the API directly:

```bash
curl -X POST https://yt-sentiment-api.ashysmoke-d4f578eb.koreacentral.azurecontainerapps.io/predict   -H "Content-Type: application/json"   -d '{"comments":["this is amazing","worst video ever","it was okay"]}'
```

The Chrome extension is the intended front end and loads unpacked (`chrome://extensions` → Developer mode → Load unpacked).

[Repository →](https://github.com/Roy7721/yt_comment_analysis) · [Extension repo →](https://github.com/Roy7721/Chrome_plugin)

---

### AskTheVid — YouTube Video Chatbot
**Ask questions about any YouTube video and get answers with timestamped citations.**

- RAG over video transcripts, with per-video caching so repeat sessions skip re-embedding
- Every answer cites the exact timestamp in the source video, so claims are checkable against the original
- Two-path design: one-time ingestion through a timestamp-preserving chunker, then per-query retrieval answering in ~1s, with embeddings run locally on CPU

`Python` · `LangChain` · `ChromaDB` · `FastAPI` · `Streamlit` · `Groq`

[Repository →](https://github.com/Roy7721/Yt_chatbot)

---

## Tech Stack

**Languages & Core**

![Python](https://img.shields.io/badge/python-3670A0?style=for-the-badge&logo=python&logoColor=ffdd54) ![NumPy](https://img.shields.io/badge/numpy-%23013243.svg?style=for-the-badge&logo=numpy&logoColor=white) ![Pandas](https://img.shields.io/badge/pandas-%23150458.svg?style=for-the-badge&logo=pandas&logoColor=white) ![scikit-learn](https://img.shields.io/badge/scikit--learn-%23F7931E.svg?style=for-the-badge&logo=scikit-learn&logoColor=white)

**LLM & AI Engineering**

![LangChain](https://img.shields.io/badge/LangChain-1C3C3C?style=for-the-badge&logo=langchain&logoColor=white) ![LangGraph](https://img.shields.io/badge/LangGraph-1C3C3C?style=for-the-badge&logo=langgraph&logoColor=white) ![ChromaDB](https://img.shields.io/badge/ChromaDB-FFCE44?style=for-the-badge&logoColor=black) ![HuggingFace](https://img.shields.io/badge/Hugging%20Face-FFD21E?style=for-the-badge&logo=huggingface&logoColor=black) ![spaCy](https://img.shields.io/badge/spaCy-09A3D5?style=for-the-badge&logo=spacy&logoColor=white) ![MCP](https://img.shields.io/badge/MCP-000000?style=for-the-badge&logo=modelcontextprotocol&logoColor=white)

**Deep Learning**

![PyTorch](https://img.shields.io/badge/PyTorch-%23EE4C2C.svg?style=for-the-badge&logo=PyTorch&logoColor=white) ![Keras](https://img.shields.io/badge/Keras-%23D00000.svg?style=for-the-badge&logo=Keras&logoColor=white)

**MLOps & Deployment**

![MLflow](https://img.shields.io/badge/mlflow-%23d9ead3.svg?style=for-the-badge&logo=numpy&logoColor=blue) ![DVC](https://img.shields.io/badge/DVC-13ADC7?style=for-the-badge&logo=dvc&logoColor=white) ![Docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white) ![GitHub Actions](https://img.shields.io/badge/github%20actions-%232671E5.svg?style=for-the-badge&logo=githubactions&logoColor=white) ![Azure](https://img.shields.io/badge/Azure-0078D4?style=for-the-badge&logo=microsoftazure&logoColor=white) ![Kubernetes](https://img.shields.io/badge/kubernetes-%23326ce5.svg?style=for-the-badge&logo=kubernetes&logoColor=white)

**Backends & APIs**

![FastAPI](https://img.shields.io/badge/FastAPI-005571?style=for-the-badge&logo=fastapi) ![Flask](https://img.shields.io/badge/flask-%23000.svg?style=for-the-badge&logo=flask&logoColor=white) ![Streamlit](https://img.shields.io/badge/Streamlit-FF4B4B?style=for-the-badge&logo=streamlit&logoColor=white) ![Postman](https://img.shields.io/badge/Postman-FF6C37?style=for-the-badge&logo=postman&logoColor=white)

**Databases**

![PostgreSQL](https://img.shields.io/badge/postgresql-4169E1.svg?style=for-the-badge&logo=postgresql&logoColor=white) ![MySQL](https://img.shields.io/badge/mysql-4479A1.svg?style=for-the-badge&logo=mysql&logoColor=white) ![MongoDB](https://img.shields.io/badge/MongoDB-%234ea94b.svg?style=for-the-badge&logo=mongodb&logoColor=white)

**Analytics & Statistical Tools**

![Plotly](https://img.shields.io/badge/Plotly-%233F4F75.svg?style=for-the-badge&logo=plotly&logoColor=white) ![Power BI](https://img.shields.io/badge/power_bi-F2C811?style=for-the-badge&logo=powerbi&logoColor=black) ![R](https://img.shields.io/badge/r-%23276DC3.svg?style=for-the-badge&logo=r&logoColor=white) ![STATA](https://img.shields.io/badge/STATA-1a5f8a?style=for-the-badge&logoColor=white) ![SPSS](https://img.shields.io/badge/SPSS-C8102E?style=for-the-badge&logoColor=white)

---

## Research

**MSc, Department of Statistics — University of Rajshahi** (CGPA 3.83/4.00)

Thesis: *Determinants of Life Expectancy in Developing and Emerging Asian Economies* — Panel ARDL with PMG and MG estimators and unit-root pre-testing, 2000–2022, stratified by income group.

Two working papers (Roy & Sabbiruzzaman) from this work, presented at the 2nd **ICRAST**, University of Rajshahi (oral) and **ICASDS 2025**, ISRT, University of Dhaka (poster).

---

## GitHub

![](https://github-readme-stats.shion.dev/api?username=Roy7721&theme=dark&hide_border=true&count_private=false)
![](https://github-readme-stats.shion.dev/api/top-langs/?username=Roy7721&theme=dark&hide_border=true&layout=compact)

---

Outside of work I play guitar. During university I was part of the bands **Jebel** and **Onkuron**.
