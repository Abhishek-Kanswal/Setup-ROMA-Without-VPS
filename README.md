# 🚀 Setup ROMA Without VPS

Run **ROMA** without a VPS and host it **free 24/7** using **Render** + **UptimeRobot**.

---

## 🧩 Step 1: Clone ROMA

```bash
git clone https://github.com/sentient-agi/ROMA.git
cd ROMA
```

### Option 1 — Use setup script (recommended)

```bash
./setup.sh --docker
```

### Option 2 — Manual setup

```bash
cp .env.example .env
cd docker
docker compose up -d
```

---

## ⚠️ Fix Common Docker Error

If you see:

```
COPY emergency_backups/ ./emergency_backups/
COPY project_results/ ./project_results/
```

Run:

```bash
mkdir emergency_backups project_results
cd docker
docker compose up -d
```

---

## ⚙️ Step 2: Setup Environment

1. Open `.env` in ROMA root.
2. Add your API keys (only what you have):

```env
OPENROUTER_API_KEY=your_openrouter_key_here
FIREWORKS_AI_API_KEY=your_fireworks_ai_api_key_here
EXA_API_KEY=your_exa_api_key_here
```

3. Open `sentient.yaml` and set:

```yaml
llm:
  provider: "fireworks_ai"
  api_key: "${FIREWORKS_AI_API_KEY}"
```

---

## ⚙️ Step 3: Update Models & Agents

Edit this file:
ROMA/src/sentientresearchagent/hierarchical_agent_framework/agent_configs/agents.yaml
Replace it with the version I attached in the repository.

### Add Fireworks AI

Edit `ROMA/src/sentientresearchagent/config/config.py`:

```python
valid_providers = ['openai','anthropic','azure','custom','openrouter','fireworks_ai']
```

---

### Disable Unsupported Agents

Edit `ROMA/src/sentientresearchagent/hierarchical_agent_framework/agent_configs/agents.yaml` and **replace these blocks** exactly as below:

```yaml
- name: "OpenAICustomSearcher"
  type: "custom_search"
  adapter_class: "OpenAICustomSearchAdapter"
  description: "Direct API-based search using OpenAI's search capabilities"
  adapter_params:
    # model_id: "gpt-4o"
    search_context_size: "high"
    use_openrouter: true
    model_id: "fireworks_ai/accounts/fireworks/models/gpt-oss-120b"
  registration:
    action_keys:
      - action_verb: "execute"
        task_type: "SEARCH"
    named_keys: ["OpenAICustomSearcher", "default_openai_searcher"]
  enabled: false
```

```yaml
- name: "GeminiCustomSearcher"
  type: "custom_search"
  adapter_class: "GeminiCustomSearchAdapter"
  description: "Direct API-based search using Google Gemini's search capabilities"
  adapter_params:
    model_id: "fireworks_ai/accounts/fireworks/models/llama4-maverick-instruct-basic"
  registration:
    action_keys:
      - action_verb: "execute"
        task_type: "SEARCH"
    named_keys: ["GeminiCustomSearcher", "default_gemini_searcher", "gemini_searcher"]
  enabled: false
```

```yaml
- name: "ExaComprehensiveSearcher"
  type: "custom_search"
  adapter_class: "ExaCustomSearchAdapter"
  description: "Comprehensive search using Exa API with LiteLLM processing - prioritizes reliable sources"
  adapter_params:
    model_id: "fireworks_ai/accounts/fireworks/models/llama4-maverick-instruct-basic"
    num_results: 5
  registration:
    action_keys:
      - action_verb: "execute"
        task_type: "SEARCH"
    named_keys: ["ExaComprehensiveSearcher", "exa_searcher", "comprehensive_searcher"]
  enabled: false
```

---

### Replace Searcher in Profiles

In these files:

```
ROMA/src/sentientresearchagent/hierarchical_agent_framework/agent_configs/profiles/deep_research_agent.yaml
ROMA/src/sentientresearchagent/hierarchical_agent_framework/agent_configs/profiles/crypto_analytics_agent.yaml
ROMA/src/sentientresearchagent/hierarchical_agent_framework/agent_configs/profiles/general_agent.yaml
ROMA/src/sentientresearchagent/hierarchical_agent_framework/agent_configs/profiles/opensourcegeneralagent.yaml
```

Replace:

```yaml
executor_adapter_names:
  SEARCH: "OpenAICustomSearcher"
```

with:

```yaml
executor_adapter_names:
  SEARCH: "SmartWebSearcher"
```

---

### Disable Unsupported Parameters

Edit `ROMA/src/sentientresearchagent/hierarchical_agent_framework/agent_configs/agents.yaml` to comment out unsupported sections:

#### BasicReasoningExecutor

```yaml
- name: "BasicReasoningExecutor"
  type: "executor"
  adapter_class: "ExecutorAdapter"
  description: "Performs Analysis and Synthesis of information"
  model:
    provider: "litellm"
    model_id: "fireworks_ai/accounts/fireworks/models/gpt-oss-120b"
  # agno_params:
  #   reasoning: true
  prompt_source: "prompts.executor_prompts.REASONING_EXECUTOR_SYSTEM_MESSAGE"
  tools:
  #  - name: "PythonTools"
  #    params:
  #      save_and_run: false
  #  - "ReasoningTools"
  registration:
    action_keys:
      - action_verb: "execute"
        task_type: "THINK"
    named_keys: ["BasicReasoningExecutor"]
  enabled: true
```

#### SmartWebSearcher

```yaml
- name: "SmartWebSearcher"
  type: "executor"
  adapter_class: "ExecutorAdapter"
  description: "Intelligent searcher that combines AI-powered search with Wikipedia"
  model:
    provider: "litellm"
    model_id: "fireworks_ai/accounts/fireworks/models/llama4-maverick-instruct-basic"
  # agno_params:
  #   reasoning: true
  prompt_source: "prompts.executor_prompts.SMART_WEB_SEARCHER_SYSTEM_MESSAGE"
  tools: ["web_search"]
  registration:
    action_keys:
      - action_verb: "execute"
        task_type: "SEARCH"
    named_keys: ["SmartWebSearcher"]
  enabled: true
```

---

## ⚙️ Step 4: Start ROMA Locally

```bash
cd docker
docker compose up -d
```

---

## 🧱 Step 5: Build & Push Docker Image

FIrst go to ROMA root folder

### 1️⃣ Build locally

```bash
docker build -t roma-app .
docker run -p 5000:5000 roma-app
```

### 2️⃣ Tag image

```bash
docker tag roma-app your-dockerhub-username/roma-app:latest
```

### 3️⃣ Push to Docker Hub

```bash
docker login
docker push your-dockerhub-username/roma-app:latest
```

Check here: https://hub.docker.com/repositories/your-dockerhub-username

---

## 🌐 Step 6: Deploy on Render

1. Go to [Render](https://render.com) → **New + → Web Service**  
2. Deploy existing image from registry  
3. Image name:

```
docker.io/your-dockerhub-username/roma-app:latest
```

4. Choose:
   - **Environment:** Docker  
   - **Port:** 5000  
   - **Instance Type:** Free  

---

## ⚙️ Step 7: Add Environment Variables on Render

1. Render Dashboard → Your app → **Environment**  
2. Add all keys from `.env`  
3. Save and redeploy

---

## 🕒 Step 8: Keep ROMA Online 24/7

Render free apps sleep after 15 min. Use **UptimeRobot**:

1. Get app URL: `https://roma-app.onrender.com`  
2. Sign up at [UptimeRobot](https://uptimerobot.com)  
3. Add new HTTP(s) monitor:
   - Name: ROMA App  
   - URL: your app URL  
   - Check every 5 minutes  
   - Alert: your email  
4. Click **Create Monitor**

---

## 🎯 Done!

- ROMA is running online  
- Free 24/7 uptime  
- No VPS needed  

Endpoint: `https://roma-app.onrender.com`

---

### 🧠 Credits

Guide by **@immyglow**
