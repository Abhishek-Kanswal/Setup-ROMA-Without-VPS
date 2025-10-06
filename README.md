# Setup-ROMA-Without-VPS
Set up ROMA without VPS 24/7 without rate limit error .

Here’s a clean, simple, and **GitHub-ready `README.md`** version of your ROMA installation guide — rewritten in clear, beginner-friendly language while keeping it professional and easy to follow 👇

---

````markdown
# 🚀 Install ROMA (Sentient AGI Project)

This guide will help you install **ROMA** using **Docker**.  
Follow the steps below carefully.

---

## 🧩 Step 1: Clone the Repository

First, clone the ROMA project from GitHub and open the folder:

```bash
git clone https://github.com/sentient-agi/ROMA.git
cd ROMA
````

You can set up ROMA in two ways 👇

### Option 1 — Using the setup script (recommended)

```bash
./setup.sh --docker
```

### Option 2 — Manual setup

```bash
git clone https://github.com/sentient-agi/ROMA.git
cd ROMA
cp .env.example .env
cd docker
docker compose up -d
```

---

## ⚠️ Common Docker Error

If you see an error like this while installing:

```
=> ERROR [backend  9/13] COPY emergency_backups/ ./emergency_backups/
=> ERROR [backend 10/13] COPY project_results/ ./project_results/
```

Don’t worry — it just means the folders don’t exist yet.

Fix it by running:

```bash
mkdir emergency_backups project_results
cd docker
docker compose up -d
```

---

## ⚙️ Step 2: Configure the Environment

Go back to the main **ROMA** folder and open the `.env` file.

Add your API keys for the services you plan to use.
You don’t need to fill them all — just the ones you have.
For example, you can only set `OPENROUTER_API_KEY` and `EXA_API_KEY`.

Example `.env`:

```env
# ROMA Environment Configuration

# ===== LLM Provider Keys =====
# OpenRouter (main LLM provider)
OPENROUTER_API_KEY=your_openrouter_key_here

# OpenAI (optional)
OPENAI_API_KEY=your_openai_key_here

# Google Gemini (optional)
GOOGLE_GENAI_API_KEY=your_google_genai_key_here

# Anthropic Claude (optional)
ANTHROPIC_API_KEY=your_anthropic_key_here
```

---

