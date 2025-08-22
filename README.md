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
```bash
git clone https://github.com/<your-username>/event-insights.git
cd event-insights

Expected result:
You are inside the new event-insights folder and git status shows a clean working tree.

