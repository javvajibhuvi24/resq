# 🚨 RESQ — Real-Time Emergency Situation Intelligence

> Turning fragmented emergency reports into one evolving incident context using AI and persistent agent memory.

## 🌍 The Problem

During emergencies, information can arrive from multiple sources:

- 📱 Citizen reports
- 🖼️ Images and evidence
- 📍 Location information
- 👨‍🚒 Responder updates
- 🕐 Incident updates

The challenge is not simply receiving information.

The real challenge is **connecting related reports, understanding how an incident evolves, and giving responders a clear operational picture.**

A single emergency may generate multiple reports describing the same event. Treating each report independently can lead to duplicated incidents and fragmented context.

---

## 💡 Our Solution

**RESQ — Real-Time Emergency Situation Intelligence** uses AI and persistent agent memory to transform fragmented reports into evolving, context-aware incidents.

RESQ can:

- 🧠 Extract structured information from emergency reports
- 🔎 Recall relevant previous context
- 🔗 Correlate related reports
- 📝 Maintain evolving incident memory
- 🚨 Assess incident priority
- 🚑 Recommend appropriate response resources
- 👤 Keep humans in the loop for consequential decisions

---

## 🧠 Why Hindsight?

Traditional AI systems often process each incoming report with limited previous context.

### Without Memory


New Report
    ↓
Analyze
    ↓
Decision


### With Hindsight

New Report
    ↓
Recall Previous Context
    ↓
Correlate Related Reports
    ↓
Update Incident Memory
    ↓
Assess Priority
    ↓
Recommend Response
    ↓
Human Approval

Hindsight acts as the persistent agent memory layer for RESQ, allowing the system to use relevant information from earlier reports when processing new information.

🔄 How RESQ Works

Emergency Report
       ↓
Information Extraction
       ↓
Hindsight Context Recall
       ↓
Incident Correlation
       ↓
Incident Memory Update
       ↓
Priority Assessment
       ↓
Resource Recommendation
       ↓
Human Approval

Example

Report 1

"Fire reported near Building A."

Report 2

"Heavy smoke coming from the main entrance."

Report 3

"People may still be inside."

Instead of treating these as three unrelated events, RESQ uses context and memory to connect them into one evolving incident.

🔥 Before vs After
Without Persistent Memory
Report 1 → Analyze
Report 2 → Analyze
Report 3 → Analyze

Each report is treated independently.

With Hindsight
Report 1
   ↓
Store Incident Context
   ↓
Report 2
   ↓
Recall Previous Context
   ↓
Update Incident
   ↓
Report 3
   ↓
Recall + Correlate
   ↓
Updated Incident Context
🏗️ Architecture

                 ┌─────────────────────┐
                 │   Emergency Report  │
                 │ Text / Image / Data │
                 └──────────┬──────────┘
                            ↓
                 ┌─────────────────────┐
                 │    RESQ AI Layer    │
                 │ Information         │
                 │ Extraction          │
                 └──────────┬──────────┘
                            ↓
                 ┌─────────────────────┐
                 │ Hindsight Memory    │
                 │                     │
                 │ Retain → Recall     │
                 │ Context → History   │
                 └──────────┬──────────┘
                            ↓
                 ┌─────────────────────┐
                 │ Incident Correlation│
                 └──────────┬──────────┘
                            ↓
                 ┌─────────────────────┐
                 │ Priority Assessment │
                 └──────────┬──────────┘
                            ↓
                 ┌─────────────────────┐
                 │ Resource Recommend. │
                 └──────────┬──────────┘
                            ↓
                 ┌─────────────────────┐
                 │  Human Approval     │
                 └─────────────────────┘

🛠️ Technology Stack
Python
Flask / FastAPI
Pydantic
REST APIs
Hindsight
AI / LLM
JSON
Python-dotenv

📂 Project Structure

ResQ/
│
├── README.md
├── requirements.txt
├── .env.example
├── .gitignore
│
├── app.py
│
├── services/
│   ├── hindsight_memory.py
│   ├── incident_analyzer.py
│   ├── incident_correlator.py
│   ├── priority_engine.py
│   └── resource_recommender.py
│
├── models/
│   └── incident.py
│
├── data/
│   ├── sample_reports.json
│   └── sample_resources.json
│
├── frontend/
│
├── tests/
│   ├── test_memory.py
│   └── test_correlation.py
│
├── docs/
│   ├── architecture.md
│   └── demo-flow.md
│
└── assets/
    ├── architecture.png
    ├── dashboard.png
    └── hindsight-memory.png
Update this structure to match the actual files in the repository.

🚀 Getting Started
1. Clone the repository
git clone https://github.com/npsimba/ResQ.git
cd ResQ
2. Create a virtual environment
python -m venv .venv

Activate it:

Windows

.venv\Scripts\activate

Linux / macOS

source .venv/bin/activate
3. Install dependencies
pip install -r requirements.txt
4. Configure environment variables

Create a .env file based on .env.example.

HINDSIGHT_API_KEY=your_api_key_here

Never commit real API keys or secrets to GitHub.

5. Run the application
python app.py

🧠 Hindsight Integration

Hindsight is used as the persistent memory layer of RESQ.

The memory workflow is:
Incoming Report
      ↓
Extract Useful Information
      ↓
Recall Relevant Context
      ↓
Correlate With Existing Incident
      ↓
Update Memory
      ↓
Continue Reasoning
The actual Hindsight integration is implemented in:

services/hindsight_memory.py

📊 Incident Lifecycle
REPORTED
    ↓
AI ANALYZED
    ↓
CORRELATED
    ↓
VERIFIED
    ↓
RESOURCE RECOMMENDED
    ↓
HUMAN APPROVAL
    ↓
DISPATCHED
    ↓
RESOLVED
RESQ is designed so that consequential actions remain subject to human approval.

📸 Demo
Dashboard

Hindsight Memory

Architecture

📝 Article

Read the full article:

👉 {https://medium.com/@javvajibhuvi01/how-hindsight-turned-emergency-noise-into-incident-context-33535f974429?sharedUserId=javvajibhuvi01}

⚠️ Current Limitations

This project is a prototype.

Emergency reports may be simulated.
Response-resource availability may be simulated.
Consequential actions require human approval.
The prototype does not directly dispatch real emergency services.
Real-world emergency infrastructure integrations are outside the current scope.
🔮 Future Improvements
Multimodal emergency evidence processing
Real-time incident feeds
Additional emergency types
Advanced geospatial correlation
Real-time responder coordination
Integration with authorized emergency-response systems
Improved incident timeline reconstruction

📜 License

MIT License 

⭐ Acknowledgements

Built with AI, persistent agent memory, and a focus on improving how fragmented emergency information can be transformed into actionable incident context.

