# 📄 AI Resume ATS Checker

Upload a resume (PDF, DOCX or TXT) and get an **ATS score out of 100** plus specific, prioritized improvements. Built with **Streamlit** and **Google Gemini**.

## Features

- Overall ATS score with a clear breakdown
- **Format & structure checks** (rule-based, deterministic): contact info, standard section headings, length, bullet points, quantified achievements, action verbs, dates, text-extraction quality, first-person wording
- **AI content review** (Gemini): strengths, weaknesses, prioritized improvements, rewritten bullet examples
- **Optional job-description matching**: paste a job posting to get a keyword match score and missing keywords
- "Extracted text" view showing what an ATS parser actually sees
- Downloadable Markdown report

## How the score works

| Situation | Formula |
|---|---|
| No job description | 40% format & structure + 60% AI content quality |
| With job description | 30% format & structure + 40% AI content quality + 30% keyword match |

The format score is fully deterministic (same resume, same score). The AI parts are an expert opinion and can vary slightly between runs. This is an estimate, not the output of any real employer's ATS.

## Project structure

```
.
├── app.py             # Streamlit app (UI + scoring + Gemini calls)
├── requirements.txt   # Python dependencies
└── README.md
```

## Run locally

1. Install Python 3.10+.
2. Create and activate a virtual environment (recommended):
   ```bash
   python -m venv .venv
   source .venv/bin/activate        # Windows: .venv\Scripts\activate
   ```
3. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```
4. Get a free API key at <https://aistudio.google.com/apikey>.
5. Provide the key in **one** of these ways:
   - Paste it into the app's sidebar, or
   - Set an environment variable: `export GEMINI_API_KEY="your-key"` (Windows PowerShell: `$env:GEMINI_API_KEY="your-key"`), or
   - Create `.streamlit/secrets.toml`:
     ```toml
     GEMINI_API_KEY = "your-key"
     ```
6. Start the app:
   ```bash
   streamlit run app.py
   ```

### Choosing the Gemini model

The default is `gemini-3.7-flash`. (`gemini-2.5-flash` was shut down by Google on 16 Oct 2026 and now returns a 404 "model not found".) If that name ever fails, try `gemini-3.6-flash` or `gemini-3.5-flash`. You can change it in the sidebar, or set `GEMINI_MODEL` (env var or secret). Google retires and releases models regularly. If you see a "model not found" error, check the current list at <https://ai.google.dev/gemini-api/docs/models> and use any current Flash model name.

## Push to GitHub

1. Create a `.gitignore` so secrets never get committed:
   ```
   .venv/
   __pycache__/
   .streamlit/secrets.toml
   .env
   ```
2. Create an empty repository on GitHub (no README/license) named e.g. `ats-resume-checker`.
3. In your project folder:
   ```bash
   git init
   git add .
   git commit -m "Initial commit: AI Resume ATS Checker"
   git branch -M main
   git remote add origin https://github.com/<your-username>/ats-resume-checker.git
   git push -u origin main
   ```
   If GitHub asks for a password, use a Personal Access Token (GitHub → Settings → Developer settings → Personal access tokens) or sign in via GitHub CLI / Git Credential Manager.

> ⚠️ Never commit your API key. If you do, revoke it in Google AI Studio and create a new one.

## Deploy on Streamlit Community Cloud (free)

1. Go to <https://share.streamlit.io> and sign in with GitHub.
2. Click **Create app** → **Deploy a public app from GitHub**.
3. Select your repository, branch `main`, and main file path `app.py`.
4. Open **Advanced settings**:
   - Choose Python 3.11 or 3.12.
   - In **Secrets**, add:
     ```toml
     GEMINI_API_KEY = "your-key"
     ```
5. Click **Deploy**. The first build takes a few minutes; later pushes to `main` redeploy automatically.

**Heads-up:** if you put *your* key in Secrets on a public app, every visitor spends *your* quota. Options: leave the secret out so visitors paste their own key in the sidebar, set a spending/quota limit on your key, or make the app private.

## Troubleshooting

| Problem | Fix |
|---|---|
| "Very little text could be extracted" | The PDF is probably a scanned image. Export a text-based PDF or DOCX. |
| "Gemini rejected the API key" | Re-copy the key; make sure the Gemini API is enabled for it. |
| "Rate limit or quota reached" | Wait a minute, or use a key with higher limits. |
| "Model not found" | Change the model name in the sidebar to a current Flash model. |
| Build fails on Streamlit Cloud | Confirm `requirements.txt` is in the repo root and `app.py` is the main file. |

## Limitations & privacy

- Resume text is sent to Google's Gemini API for analysis. The app itself does not store files or text.
- Multi-column layouts, tables, text boxes and graphics can break real ATS parsers. Use the "Extracted text" tab to spot problems.
- Scores are guidance, not a guarantee of how any employer's system will rank you.
