# Python API — IBM Watson Speech to Text & Language Translator

[![Python](https://img.shields.io/badge/Python-3.8%2B-blue?logo=python&logoColor=white)](https://www.python.org/)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-orange?logo=jupyter&logoColor=white)](https://jupyter.org/)
[![IBM Watson](https://img.shields.io/badge/IBM-Watson-054ADA?logo=ibm&logoColor=white)](https://www.ibm.com/watson)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

A hands-on, mentor-guided project that teaches you how APIs work by building a real audio-transcription and translation pipeline using IBM Watson cloud services.

---

## Table of Contents

- [What You Will Learn](#what-you-will-learn)
- [How It Works](#how-it-works)
- [Prerequisites](#prerequisites)
- [Getting IBM Watson API Keys](#getting-ibm-watson-api-keys)
- [Installation](#installation)
- [Configuration](#configuration)
- [Project Structure](#project-structure)
- [Notebook Walkthrough](#notebook-walkthrough)
- [Key Concepts Explained](#key-concepts-explained)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)
- [License](#license)

---

## What You Will Learn

By the end of this project you will be able to:

| Concept | Details |
|---|---|
| **What an API is** | How two pieces of software communicate through a defined interface |
| **REST API fundamentals** | Client/server model, HTTP methods (GET, POST), request/response cycle |
| **API authentication** | What API keys are, why they exist, and how to use them safely |
| **IBM Watson Speech to Text** | Convert an audio file (.mp3) into a text transcript via REST |
| **IBM Watson Language Translator** | Translate text between languages (English ↔ Spanish ↔ French) |
| **Working with API responses** | Parse JSON responses and extract meaningful data with pandas |
| **Best practices** | Never hard-code credentials — load them from environment variables |

---

## How It Works

```
┌──────────────────────────────────────────────────────────┐
│                     Your Python Code                     │
│                       (the client)                       │
└───────────────────┬──────────────────────────────────────┘
                    │  HTTP POST  (audio file)
                    ▼
        ┌───────────────────────┐
        │ IBM Watson            │
        │ Speech to Text API    │  ──▶  "in this video we discuss..."
        └───────────────────────┘
                    │  English transcript
                    ▼
        ┌───────────────────────┐
        │ IBM Watson            │
        │ Language Translator   │  ──▶  Spanish / French translation
        └───────────────────────┘
```

1. A sample MP3 lecture file is downloaded from IBM's object storage.
2. The audio bytes are **POST**ed to the Watson Speech to Text API.
3. The JSON response contains a plain-English transcript.
4. The transcript is **POST**ed to the Watson Language Translator API.
5. Translations are returned in Spanish and French.

---

## Prerequisites

- **Python 3.8 or higher** — [Download](https://www.python.org/downloads/)
- **Jupyter Notebook** — bundled with [Anaconda](https://www.anaconda.com/) or install via `pip install notebook`
- **Free IBM Cloud account** — [Sign up here](https://cloud.ibm.com/registration) (no credit card required for lite tier)

---

## Getting IBM Watson API Keys

You need two separate Watson service instances. Both are free on the Lite plan.

### 1. Speech to Text

1. Log in to [IBM Cloud](https://cloud.ibm.com/).
2. Click **Create resource** → search for **Speech to Text** → select it.
3. Choose the **Lite** plan → click **Create**.
4. Go to **Manage** → **Credentials** and copy:
   - `API key`
   - `URL` (the service endpoint, e.g. `https://api.us-south.speech-to-text.watson.cloud.ibm.com`)

### 2. Language Translator

1. Click **Create resource** → search for **Language Translator** → select it.
2. Choose the **Lite** plan → click **Create**.
3. Go to **Manage** → **Credentials** and copy:
   - `API key`
   - `URL`

> **Security note:** Treat API keys like passwords. Never commit them to Git. This project uses a `.env` file (listed in `.gitignore`) so your secrets stay local.

---

## Installation

```bash
# 1. Clone the repository
git clone https://github.com/mbjamshidi/python-api-ibm.git
cd python-api-ibm

# 2. (Recommended) Create and activate a virtual environment
python -m venv venv
source venv/bin/activate        # macOS / Linux
venv\Scripts\activate           # Windows

# 3. Install dependencies
pip install -r requirements.txt

# 4. Launch Jupyter
jupyter notebook
```

---

## Configuration

```bash
# Copy the example env file
cp .env.example .env
```

Open `.env` in any text editor and replace the placeholder values with your real credentials:

```ini
SPEECH_TO_TEXT_API_KEY=abc123...
SPEECH_TO_TEXT_URL=https://api.us-south.speech-to-text.watson.cloud.ibm.com

LANGUAGE_TRANSLATOR_API_KEY=xyz789...
LANGUAGE_TRANSLATOR_URL=https://api.us-south.language-translator.watson.cloud.ibm.com
```

The notebook reads these values automatically using `python-dotenv`. You will never need to paste keys directly into the code.

---

## Project Structure

```
python-api-ibm/
├── Python-API-IBM.ipynb   # Main tutorial notebook
├── requirements.txt        # Python dependencies
├── .env.example            # Template for credentials (safe to commit)
├── .env                    # Your actual credentials  ← DO NOT COMMIT
├── .gitignore              # Excludes .env and other sensitive files
└── LICENSE                 # MIT License
```

> The `.gitignore` already excludes `.env`. Never remove that line.

---

## Notebook Walkthrough

The notebook is divided into four progressive sections:

### Part 1 — APIs Through pandas (Cells 1–2)

Before touching any web service, you explore the **concept of an API** using pandas — a library you may already know. Calling `df.mean()` is itself an API call: you send data in, a defined interface processes it, and you receive results back.

**Key takeaway:** An API is just a contract between two pieces of software. Location (local vs. remote) is an implementation detail.

---

### Part 2 — REST APIs (Cell 3)

You learn the vocabulary of REST:

| Term | Meaning |
|---|---|
| **Client** | Your code making the request |
| **Resource / Server** | The web service providing data or computation |
| **Endpoint** | The URL that represents a specific operation |
| **HTTP Method** | `GET` (fetch) · `POST` (send data) · `PUT` (update) · `DELETE` (remove) |
| **Request body** | Data sent to the server (e.g. your audio file) |
| **Response body** | Data returned by the server (e.g. the transcript JSON) |

---

### Part 3 — Watson Speech to Text (Cells 4–7)

You initialize the IBM Watson SDK client using your API key and endpoint URL loaded from `.env`, download a sample MP3 file, and call `recognize()`. The response is a nested JSON object; you use `pandas.json_normalize()` to flatten it into a readable DataFrame.

**What the response looks like:**

```json
{
  "results": [
    {
      "alternatives": [
        {
          "confidence": 0.96,
          "transcript": "in this video we discuss polynomial regression and pipelines"
        }
      ],
      "final": true
    }
  ]
}
```

---

### Part 4 — Watson Language Translator (Cells 8–12)

You initialize a second Watson client and call `translate()` with a `model_id` that specifies the language pair:

| `model_id` | Direction |
|---|---|
| `en-es` | English → Spanish |
| `es-en` | Spanish → English |
| `en-fr` | English → French |

You also call `list_identifiable_languages()` to see all ~70 supported languages at once.

---

## Key Concepts Explained

### Why environment variables for API keys?

Hard-coding credentials directly in a notebook is a common beginner mistake:

```python
# BAD — never do this
api_key = "abc123secretkey"
```

If you push this to GitHub (even once), bots scan public repos within seconds and can use your key, potentially billing your account.

The safe pattern used in this project:

```python
# GOOD — key stays out of source code
import os
from dotenv import load_dotenv
load_dotenv()
api_key = os.getenv("SPEECH_TO_TEXT_API_KEY")
```

### What is `IAMAuthenticator`?

IBM Watson uses IAM (Identity and Access Management) for authentication. Instead of sending your API key with every request in the URL (which is insecure), the SDK uses your key to obtain a short-lived **bearer token** that is passed in the HTTP `Authorization` header automatically.

### What does `json_normalize()` do?

Watson responses are deeply nested JSON. `pandas.json_normalize()` flattens nested dictionaries and lists into a flat DataFrame — making it easy to inspect results without writing recursive parsing code.

---

## Troubleshooting

| Problem | Likely cause | Fix |
|---|---|---|
| `AuthenticationError: Unauthorized` | Wrong or blank API key | Check `.env`; ensure no leading/trailing spaces |
| `ServiceUnavailableError` | Incorrect service URL | Copy the exact URL from IBM Cloud credentials panel |
| `ModuleNotFoundError: ibm_watson` | Dependencies not installed | Run `pip install -r requirements.txt` |
| `FileNotFoundError: PolynomialRegressionandPipelines.mp3` | wget cell not run | Run Cell 5 before Cell 6 |
| `KeyError: 'results'` | Transcription returned an error | Check audio format; the file must be valid MP3 |
| `.env` not loading | `load_dotenv()` not called | Make sure Cell 1 (the setup cell) ran before API cells |

---

## Contributing

Contributions, bug reports, and suggestions are welcome.

1. Fork the repository.
2. Create a feature branch: `git checkout -b feature/your-feature-name`
3. Commit your changes with a clear message.
4. Open a pull request describing what you changed and why.

Please follow these guidelines:
- Do not commit `.env` or any file containing real credentials.
- Keep notebook cells focused and well-commented for learners.
- Test that the notebook runs end-to-end before submitting a PR.

---

## License

Distributed under the **MIT License**. See [LICENSE](LICENSE) for details.

---

*Built with IBM Watson — © 2020 Mohammad (Behdad) Jamshidi*
