# 📄 AI Resume Assistant

An AI-powered resume-to-job-description analyzer built with **Streamlit** and the **Gemini API**.

Upload a resume (`PDF`, `DOCX`, `TXT`, or `MD`), paste a job description, and receive:

- ATS-style compatibility score out of 100
- Six-part score breakdown
- Matched keywords
- Partially matched keywords
- Missing keywords
- Job-relevant strengths
- Detailed shortcomings with evidence, impact, and fixes
- Section-by-section resume feedback
- Prioritized improvements
- Truth-preserving rewritten bullet suggestions
- ATS formatting/readability warnings
- A final action plan
- Full structured JSON output for debugging/reuse

> **Important:** The score is an AI-generated compatibility estimate. It is not an official score from a particular ATS vendor, and it cannot guarantee an interview or job outcome.

## Tech Stack

- Python
- Streamlit
- Google Gemini API via the official `google-genai` SDK
- PyPDF for PDF text extraction
- python-docx for DOCX extraction
- GitHub + Streamlit Community Cloud for deployment

Gemini's Files API is used for PDF analysis so the model can also receive the original PDF, which can help when formatting or extraction matters. Google documents that uploaded Files API objects are stored temporarily and are automatically deleted after 48 hours. citehttps://ai.google.dev/gemini-api/docs/files

## Project Structure

```text
ai-resume-assistant/
├── app.py
├── requirements.txt
├── README.md
└── .gitignore
```

## 1. Get a Gemini API Key

Create a Gemini API key from Google AI Studio.

Do **not** put the key directly inside `app.py`, and do **not** commit it to GitHub.

## 2. Run Locally

Create and activate a virtual environment:

### Windows

```powershell
python -m venv .venv
.venv\Scripts\activate
```

Install dependencies:

```powershell
pip install -r requirements.txt
```

Create:

```text
.streamlit/secrets.toml
```

Add:

```toml
GEMINI_API_KEY = "YOUR_GEMINI_API_KEY"
GEMINI_MODEL = "gemini-3.8-flash"
```

Then run:

```powershell
streamlit run app.py
```

Streamlit supports local `secrets.toml` files and deployment-time secrets; keep the secrets file out of Git. citehttps://docs.streamlit.io/develop/concepts/connections/secrets-management

## 3. Push to GitHub

From the project folder:

```bash
git init
git add .
git commit -m "Initial AI Resume Assistant"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/ai-resume-assistant.git
git push -u origin main
```

Create `.gitignore` before pushing:

```text
.venv/
__pycache__/
*.pyc
.streamlit/secrets.toml
.env
```

## 4. Deploy with Streamlit Community Cloud

1. Push the repository to GitHub.
2. Open Streamlit Community Cloud.
3. Connect your GitHub account.
4. Select the repository.
5. Select `app.py` as the main file.
6. In the deployment settings, open **Advanced settings / Secrets**.
7. Add:

```toml
GEMINI_API_KEY = "YOUR_GEMINI_API_KEY"
GEMINI_MODEL = "gemini-3.8-flash"
```

8. Deploy.

Streamlit Community Cloud can deploy directly from GitHub repositories, and its Secrets interface keeps credentials outside the repository. citehttps://docs.streamlit.io/deploy/streamlit-community-cloud/get-started/connect-your-github-account citehttps://docs.streamlit.io/deploy/streamlit-community-cloud/deploy-your-app/secrets-management

## 5. How the App Works

```text
Resume Upload
     │
     ├── PDF ──> PyPDF extraction
     │             + original PDF sent to Gemini
     │
     ├── DOCX ─> python-docx extraction
     │
     └── TXT/MD -> text extraction
     │
     ▼
Resume Content + Job Description
     │
     ▼
Gemini
     │
     ├── keyword comparison
     ├── skill comparison
     ├── experience relevance
     ├── education alignment
     ├── achievement analysis
     ├── ATS readability review
     └── improvement generation
     │
     ▼
Structured JSON
     │
     ▼
Streamlit Dashboard
```

## 6. ATS Score Method

The app asks Gemini to estimate six dimensions:

| Dimension | Weight |
|---|---:|
| Keyword match | 25% |
| Experience relevance | 20% |
| Skills match | 20% |
| Education match | 10% |
| Achievement impact | 15% |
| Format/readability | 10% |

The six component scores are 0–100, and the overall score is expected to be consistent with these weights.

This is intentionally presented as an **ATS-style estimate**, not as a claim that every ATS uses these exact weights.

## 7. Resume Truthfulness

The model is instructed not to invent:

- Skills
- Work experience
- Certifications
- Education
- Employers
- Dates
- Metrics

If the job description asks for a skill that the resume does not demonstrate, the app reports it as a gap rather than telling the user to falsely add it.

## 8. Supported Files

Currently supported:

- `.pdf`
- `.docx`
- `.txt`
- `.md`

For PDF files, the app extracts text locally and also sends the original PDF to Gemini. Gemini's documentation supports PDF files through its Files API. citehttps://ai.google.dev/gemini-api/docs/document-processing

For scanned/image-only PDFs, text extraction may be empty. Gemini may still be able to interpret the original PDF, but results can depend on the document.

## 9. Security

Never commit:

```text
.streamlit/secrets.toml
.env
```

The Gemini API key belongs in Streamlit Secrets or an environment variable.

## 10. Testing

Before deployment, run:

```bash
python -m py_compile app.py
```

Then:

```bash
streamlit run app.py
```

For the full AI analysis, a valid Gemini API key is required.

## 11. Possible Future Features

Good next upgrades for a portfolio version:

- Downloadable optimized resume
- Resume version history
- Multiple job descriptions
- Job-description keyword heatmap
- Resume section scoring
- Side-by-side original vs improved bullets
- PDF report generation
- LinkedIn profile comparison
- Cover-letter generation
- Job-specific resume tailoring
- Local deterministic keyword scoring alongside the Gemini score
