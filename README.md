# LLM Nexus — Unified LLM Gateway

**One API. Multiple LLM providers. Intelligent routing, caching, and cost control.**

LLM Nexus is a Python-based, provider-agnostic gateway built with FastAPI. It provides a unified API for interacting with multiple Large Language Model (LLM) providers through an OpenAI-compatible Chat Completions interface.

The project brings together model routing, prompt caching, cost tracking, budget enforcement, provider fallback, and monitoring in a single gateway.

## ✨ Features

- **Multi-provider integration:** Connect to OpenAI, Groq, and Anthropic through a unified API.
- **Intelligent model routing:** Route requests to providers based on the requested model.
- **Exact-match caching:** Use SHA-256 request hashes to identify repeated prompts.
- **Semantic caching:** Use sentence-transformer embeddings and cosine similarity to identify similar prompts.
- **Cost tracking:** Estimate token usage costs using model-specific pricing tables.
- **Budget enforcement:** Check user spending against configured budget limits.
- **Automatic provider fallback:** Retry requests through alternative providers when a provider fails.
- **Monitoring and observability:** Expose latency, cache, provider, and cost metrics.
- **Live dashboard:** View gateway monitoring information through a dashboard.
- **Load testing:** Test gateway behavior under simulated concurrent traffic using Locust.

## 🏗️ Architecture

```text
Client / OpenAI SDK
        |
        v
FastAPI Gateway
        |
        v
Exact-Match Cache (Redis)
        |
        v
Semantic Cache (Embeddings)
        |
        | Cache miss
        v
Model Router
   /     |      \
Groq   OpenAI   Anthropic
   \     |      /
    Response Normalizer
            |
            v
Provider Fallback
            |
            v
Cost Calculation & Budget Check
            |
            v
Database Logging & Metrics
```

## 🛠️ Tech Stack

| Technology | Purpose |
|---|---|
| Python | Core application logic |
| FastAPI | REST API framework |
| Redis | Prompt caching |
| SQLAlchemy | Database access |
| SQLite | Local data persistence |
| Sentence Transformers | Semantic embeddings |
| OpenAI, Groq, Anthropic SDKs | LLM provider integration |
| Prometheus Client | Metrics collection |
| Locust | Load testing |
| Docker Compose | Supporting services |

## 🚀 Getting Started

### Prerequisites

- Python 3 installed
- Docker Desktop
- API keys for the providers you want to use
- Git

### 1. Clone the repository

```bash
git clone https://github.com/abhishekkothawade-08/LLM-Nexus.git
cd LLM-Nexus
```

### 2. Create a virtual environment

**macOS / Linux**

```bash
python3 -m venv venv
source venv/bin/activate
```

**Windows**

```bash
python -m venv venv
venv\Scripts\activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Configure environment variables

Create your local environment file:

```bash
cp .env.example .env
```

Add the provider API keys and any other required configuration to `.env`.

**Security:** Never commit your real `.env` file, API keys, or provider credentials to GitHub.

### 5. Start Redis

Make sure Docker Desktop is running, then execute:

```bash
docker compose up -d redis
```

### 6. Start the gateway

```bash
uvicorn app.main:app --reload
```

The gateway should now be available locally on port `8000`, provided that the required configuration and services are working.

## 🔌 API Usage

LLM Nexus exposes an OpenAI-compatible chat completions endpoint.

**Endpoint**

```http
POST /v1/chat/completions
```

### Python example

```python
from openai import OpenAI

client = OpenAI(
    base_url="http://127.0.0.1:8000/v1",
    api_key="not-needed"
)

response = client.chat.completions.create(
    model="llama-3.1-8b-instant",
    messages=[
        {
            "role": "user",
            "content": "Explain machine learning simply."
        }
    ],
    extra_headers={"X-User": "testuser"}
)

print(response.choices[0].message.content)
```

The `X-User` header identifies the user for cost tracking in the current implementation.

## 📊 Local Monitoring

After starting the application, open the following URLs in your browser.

| Feature | Local URL | Description |
|---|---|---|
| API Documentation | [127.0.0.1:8000/docs](http://127.0.0.1:8000/docs) | Explore and test API endpoints |
| Live Dashboard | [127.0.0.1:8000/dashboard](http://127.0.0.1:8000/dashboard) | View gateway monitoring information |
| Prometheus Metrics | [127.0.0.1:8000/metrics](http://127.0.0.1:8000/metrics) | Inspect application metrics |

**About port 8000:** These are local development URLs, not public website links. They work on your own machine while the application is running. The command above uses port `8000` by default.

## 🧪 Load Testing

Install Locust if necessary:

```bash
pip install locust
```

Run the load-testing script:

```bash
locust -f locustfile.py --host http://127.0.0.1:8000
```

Open [http://localhost:8089](http://localhost:8089) to configure and run a load test.

### Reported test results

The original project documentation reports the following results from a local Locust run:

| Metric | Reported result |
|---|---:|
| Simulated concurrent users | 100 |
| Total requests | 480 |
| Failure rate | 0% |
| Throughput | Approximately 32.8 requests/second |
| Median latency | 63 ms |
| 95th-percentile latency | 2.1 seconds |
| 99th-percentile latency | 2.4 seconds |

These figures are environment- and workload-dependent. Run the test yourself to reproduce and validate them before presenting them as independently verified results.

## ⚠️ Known Limitations

- **Database:** SQLite is used for local persistence; PostgreSQL is a possible extension.
- **Provider testing:** Live end-to-end testing has primarily been performed with Groq. Other providers may require funded accounts for complete verification.
- **Semantic cache:** The current capped-list implementation uses brute-force cosine similarity and may not scale to large workloads.
- **Authentication:** User identification relies on an `X-User` header rather than a complete API-key authentication system.
- **Monitoring:** Prometheus-compatible metrics are available, but a full Grafana deployment is not included.
- **Production readiness:** The project is a learning implementation and requires additional security, scalability, and reliability work before production use.

## 🔮 Future Improvements

- Add API-key authentication and role-based access control.
- Integrate a vector database for scalable semantic caching.
- Implement provider-specific rate limiting and retry policies.
- Add PostgreSQL support for persistent deployments.
- Integrate Grafana dashboards and distributed tracing.
- Add automated tests and continuous integration.

## 👨‍💻 Author

**Abhishek Kothawade**

- GitHub: [abhishekkothawade-08](https://github.com/abhishekkothawade-08)




