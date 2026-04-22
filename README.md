# Student GitHub Reviewer

An AI-powered portfolio reviewer that analyzes a GitHub user's profile and repositories, then delivers personalized mentorship feedback — built with LangGraph, Groq (Llama 3.3), FastAPI, and Streamlit.

---

## Overview

Student GitHub Reviewer accepts a GitHub username, fetches the user's recent repositories and activity via the GitHub API, and passes that data through an agentic LangGraph workflow. A code mentor powered by Llama 3.3-70b generates encouraging, actionable feedback tailored to the student's apparent skill level and project history.

---

## Features

- **GitHub portfolio extraction** — fetches user info, recent repos, languages, and activity stats
- **AI code mentorship** — Llama 3.3-70b (via Groq) produces personalized, constructive feedback
- **Agentic workflow** — LangGraph orchestrates data extraction → analysis → response in a clean graph
- **Two-layer architecture** — FastAPI backend + Streamlit frontend, independently runnable
- **Deployed on Render** — production backend ready to use

---

## Tech Stack

| Layer | Technology |
|---|---|
| LLM | Groq API — `llama-3.3-70b-versatile` |
| Agent orchestration | LangGraph + LangChain |
| Backend | FastAPI + Uvicorn |
| Frontend | Streamlit |
| Data source | GitHub REST API |
| Config | python-dotenv |

---

## Project Structure

```
student-github-reviewer/
├── agent/
│   ├── graph.py        # LangGraph workflow definition
│   ├── nodes.py        # Data extractor + code mentor nodes
│   └── state.py        # ReviewState TypedDict
├── ui/
│   └── app.py          # Streamlit frontend
├── main.py             # FastAPI entry point
├── requirements.txt
└── .env.example
```

---

## Getting Started

### Prerequisites

- Python 3.10+
- [Groq API key](https://console.groq.com)
- [GitHub personal access token](https://github.com/settings/tokens) (optional, increases API rate limit)

### Installation

```bash
# 1. Clone the repository
git clone https://github.com/your-username/student-github-reviewer.git
cd student-github-reviewer

# 2. Create and activate a virtual environment
python -m venv venv
source venv/bin/activate        # macOS / Linux
# venv\Scripts\activate         # Windows

# 3. Install dependencies
pip install -r requirements.txt

# 4. Configure environment variables
cp .env.example .env
```

Open `.env` and add your keys:

```env
GROQ_API_KEY="your_groq_api_key"
GITHUB_TOKEN="your_github_personal_access_token"
```

### Running Locally

**Backend (FastAPI)** — in one terminal:

```bash
python main.py
# Server running at http://localhost:8000
```

**Frontend (Streamlit)** — in a second terminal:

```bash
streamlit run ui/app.py
# UI available at http://localhost:8501
```

Enter any GitHub username in the UI to generate a review.

---

## How It Works

```
User inputs GitHub username
        │
        ▼
Streamlit UI  ──POST /review──►  FastAPI Backend
                                        │
                                        ▼
                               LangGraph Workflow
                                ┌───────────────┐
                                │ data_extractor │  ← GitHub API
                                └──────┬────────┘
                                       │
                                ┌──────▼────────┐
                                │  code_mentor  │  ← Groq / Llama 3.3
                                └──────┬────────┘
                                       │
                               JSON response returned
                                        │
                                        ▼
                               Streamlit displays feedback
```

1. The **data extractor node** calls the GitHub API to collect the user's profile info and their 5 most recently updated repositories (name, description, languages, star count).
2. The **code mentor node** sends the extracted data to Llama 3.3-70b with a prompt designed to produce encouraging, specific feedback suited to a student developer.
3. The FastAPI backend returns both the raw extracted data and the AI-generated feedback as JSON.
4. The Streamlit frontend renders the results in a readable format.

---

## API Reference

### `GET /`
Health check.

**Response:**
```json
{ "message": "GitHub Reviewer API is running" }
```

### `POST /review?username={github_username}`
Runs the full review workflow for the given GitHub user.

**Response:**
```json
{
  "extracted_data": "...",
  "mentor_feedback": "..."
}
```

---

## Deployment

The backend is deployed on [Render](https://render.com). To deploy your own instance:

1. Push the repo to GitHub.
2. Create a new **Web Service** on Render pointing to your repo.
3. Set the start command to `python main.py`.
4. Add `GROQ_API_KEY` and `GITHUB_TOKEN` as environment variables in Render's dashboard.

---

## Requirements

```
fastapi
uvicorn
langchain
langchain-groq
langgraph
requests
python-dotenv
streamlit
```

Install with:

```bash
pip install -r requirements.txt
```

---

## License

MIT
