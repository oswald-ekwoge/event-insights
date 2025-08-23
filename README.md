# 🚀 Event-Driven Insights API

A portfolio project demonstrating how to **design and deploy** a scalable, **event-driven architecture** on **Microsoft Azure**.  
The system **ingests business events in real time**, **applies lightweight machine learning models in Python**, stores enriched results in **cloud databases**, and exposes them through a **FastAPI** service.

---

## 📌 Project Overview

- **Event ingestion** via Azure Functions (later with Service Bus).
- **Real-time scoring** using Python (scikit-learn placeholder model for now).
- **Data persistence** in Cosmos DB (hot) and optional Azure SQL (reporting).
- **REST API** with FastAPI to query insights.
- **Infrastructure as Code** with Bicep (to provision cloud resources).
- **CI/CD pipelines** with Azure DevOps.
- **Observability** through Application Insights.

This project shows how to combine **Cloud**, **DevOps/IaC**, **Data Engineering**, and **Machine Learning** skills into a real, production-style system.

---
## Some Definitions
---
- **Azure Functions:**
  Azure Functions is a **serverless compute service** provided by Microsoft Azure. That means you can **run small pieces of code ("functions") in the cloud** **without managing** servers or infrastructure.
  Key features include:
  - **Event-driven**: Functions are triggered by events (your code only runs when something happens) such as:
    - HTTP requests (APIs, webhooks)
    - Database changes
    - File uploads to storage
    - Messages from queues/service buses
    - Scheduled timers (like cron jobs)
 - **Serverless**: You don’t worry about provisioning or scaling servers. Azure automatically takes care of it.
 - **Pay-per-use**: You only pay for the execution time and resources consumed, not for idle server time.
 - **Supports multiple languages**: Python, C#, Java, JavaScript/TypeScript, PowerShell, etc.
 - **Scalable**: Automatically scales up to handle high loads and scales down when idle.

So instead of running 24/7, your code sleeps until an event wakes it up.

**NB**: A webhook is basically a way for one system to n**otify another system automatically** when something happens.
👉 Instead of you asking all the time “Has anything changed?”, **the other system calls you immediately when an event happens**.

---
- **FastAPI:**
  A modern, high-performance web framework for building APIs with Python. It is designed to be fast, easy to use, and developer-friendly, especially for creating RESTful APIs and microservices.
  Key Features include:
  - **Speed**: Built on top of **Starlette** (for web handling) and **Pydantic** (for data validation). It’s **one of the fastest Python frameworks**, comparable to Node.js and Go.
  - **Automatic validation**: Input data is automatically validated based on Python type hints, thanks to Pydantic.
  - **Auto-generated docs**: It automatically generates **interactive API documentation** (Swagger UI and ReDoc).
  - **Asynchronous support**: Fully supports Python’s **async/await** for high-performance APIs.

  🌟 **Starlette (Web Handling):**
  - Think of Starlette as the **traffic controller of a web app**. It decides how requests (cars) come in and where they should go.
  - For example:
    - A user types myapi.com/hello → Starlette makes sure your hello function is called.
    - A user sends data → Starlette delivers it to the right part of your app.
   
  ✅ **Pydantic (Data Validation):**
  - Think of Pydantic as the security guard for your data.
  - It checks if the data coming in is in the right shape and type.
  - Example:
    - If your app expects an age as a number, and someone sends "twenty" (a word), Pydantic says ❌ "Invalid data".
    - If they send 20 (a number), Pydantic says ✅ "Okay, good data".
    - It also automatically converts data when possible:
      - Input: "20" (string)
      - Pydantic: converts it → 20 (integer)

- So in simple terms:
👉 Pydantic makes sure the data your app receives is correct, safe, and clean before you use it.

🏎️ How they work together in FastAPI
- Starlette = handles the web request/response part (getting the car to the gate).
- Pydantic = makes sure the data inside the request is valid (checking the passenger’s ticket).

---

## 🛠️ Tech Stack

- **Language:** Python 3.11+
- **Frameworks:** FastAPI, Azure Functions
- **Cloud (target):** Microsoft Azure (Cosmos DB, Service Bus, Container Apps, App Insights)
- **Infrastructure as Code:** Bicep
- **CI/CD:** Azure DevOps Pipelines
- **Machine Learning:** scikit-learn (lightweight demo)

---

## 📖 How to Replicate This Project

Follow these steps to get the project running locally.  
You’ll end with:
- A running **FastAPI service** on `http://127.0.0.1:8000`
- A stub **Azure Function** ingestion endpoint you can call with events

---

### ✅ Prerequisites

- [Git](https://git-scm.com/downloads)
- [Python 3.11+](https://www.python.org/downloads/)
- (Optional for Functions) [Azure Functions Core Tools](https://learn.microsoft.com/azure/azure-functions/functions-run-local)

---

### Step 1 — Create & Clone the Repository

**Objective:**  
Establish a shared, version-controlled home for the code so anyone can reproduce the project.

**Commands:**
'''bash
git clone https://github.com/<your-username>/event-insights.git
cd event-insights




---
### Step 2 — Scaffold the Project (Folders + Minimal Files)

**Objective:**
Set up a clean architecture from day one, separating business logic (Domain/Application) from cloud and framework code (Infrastructure). This ensures the project is easy to maintain and extend.

**Create folders (Windows PowerShell):**

mkdir src\ingestion_fn,src\insights_api,src\domain,src\application,src\infrastructure,src\ml `
,tests\unit,tests\integration,tests\e2e,infra\bicep,pipelines `
,docs\adr,docs\api-contracts,docs\sequence-diagrams,tools\loadgen,tools\local-emulators


**Add** .gitignore

__pycache__/
*.pyc
.env
.venv/
dist/
.coverage
htmlcov/
.azurefunctions/
local.settings.json


**Add** pyproject.toml

[project]
name = "event-insights"
version = "0.1.0"
requires-python = ">=3.11"
dependencies = [
  "fastapi",
  "uvicorn",
  "pydantic>=2",
  "scikit-learn",
  "azure-functions",
]

[tool.pytest.ini_options]
pythonpath = ["src"]



**Minimal FastAPI app** → src/insights_api/main.py

from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI(title="Event Insights API", version="0.1.0")

class ScoreOut(BaseModel):
    user_id: str
    event_id: str
    score: float
    model: str

@app.get("/v1/health")
def health():
    return {"status": "ok"}

@app.get("/v1/users/{user_id}/scores", response_model=list[ScoreOut])
def get_user_scores(user_id: str, limit: int = 10):
    # stub response
    return [ScoreOut(user_id=user_id, event_id="demo", score=0.42, model="v0")]


**Stub Azure Function** → src/ingestion_fn/__init__.py

import json
from datetime import datetime
from uuid import uuid4
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:
    try:
        body = req.get_json()
    except ValueError:
        return func.HttpResponse("Invalid JSON", status_code=400)

    event_id = body.get("event_id") or str(uuid4())
    user_id = body.get("user_id", "unknown")
    amount = float(body.get("amount", 0))
    score = min(0.99, max(0.01, amount / 1000.0))  # placeholder model

    doc = {
        "event_id": event_id,
        "user_id": user_id,
        "occurred_at": body.get("occurred_at") or datetime.utcnow().isoformat() + "Z",
        "type": body.get("type", "unknown"),
        "amount": amount,
        "score": score,
        "model": "v0",
    }
    return func.HttpResponse(json.dumps({"ok": True, "saved": doc}),
                             status_code=200, mimetype="application/json")

### Step 3 — Run the Local MVP

Objective:
Prove the setup works end-to-end locally before adding cloud services.
This gives confidence that our architecture and tooling are correct.

Create virtual environment + install deps

python -m venv .venv
. .\.venv\Scripts\Activate.ps1
py -m pip install --upgrade pip
pip install -e .


Run FastAPI

uvicorn src.insights_api.main:app --reload --port 8000


Check in browser: http://127.0.0.1:8000/v1/health

Expected:

{"status":"ok"}


Test scores endpoint

Invoke-RestMethod http://127.0.0.1:8000/v1/users/u_123/scores


(Optional) Run the ingestion function
If you installed Azure Functions Core Tools:

func start


Send a sample event:

$body = @{ user_id="u_123"; type="checkout"; amount=129.9 } | ConvertTo-Json
Invoke-RestMethod -Method Post -Uri http://127.0.0.1:7071/api/ingest -Body $body -ContentType "application/json"


Expected JSON (truncated):

{"ok": true, "saved": {"user_id":"u_123","score":0.1299,"model":"v0", ...}}


### 🎯 Outcomes after Step 3

A reproducible repo with clean architecture scaffolding.

A running FastAPI service and stub Azure Function locally.

A clear event contract and ADR placeholders for documenting design decisions.

A working local MVP proving the foundation is solid before wiring Azure resources.


### 🔜 Next Steps

Fill in ADRs with design decisions.

Add Bicep templates to provision Cosmos DB, Service Bus, and App Insights.

Wire the ingestion function to write to Cosmos DB.

Update the API to read real data from Cosmos DB.

Add CI/CD with Azure DevOps.
