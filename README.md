# RESQ — Real-Time Emergency Situation Intelligence

> Turning fragmented emergency reports into one evolving incident context using AI and Hindsight agent memory.

## 🚨 Problem

During emergencies, information arrives from multiple sources such as
citizen reports, images, field teams, and other channels.

The challenge is not simply receiving information. The challenge is
connecting related reports, understanding how an incident evolves,
and giving responders a clear operational picture.

## 💡 Solution

RESQ uses AI to:

- Extract information from incoming reports
- Identify related reports
- Combine evidence across reports
- Maintain incident context using Hindsight
- Assess incident priority
- Recommend appropriate response resources
- Keep humans in the loop for consequential actions

## 🧠 Why Hindsight?

Traditional approach:

Report → Analyze → Decision

RESQ with Hindsight:

Report → Recall Context → Correlate → Update Incident → Decision

Hindsight provides persistent agent memory so the system can use
relevant information from earlier reports when processing new ones.

## 🔄 How It Works

1. A citizen submits an emergency report
2. RESQ extracts structured information
3. Hindsight retrieves relevant previous context
4. Related reports are correlated
5. The incident context is updated
6. Priority is assessed
7. A response resource is recommended
8. A human responder approves the action

## 🏗️ Architecture

![RESQ Architecture](assets/architecture.png)

## 🧪 Example

### Report 1

"Fire near Building A."

### Report 2

"Heavy smoke coming from the main entrance."

### Report 3

"People may still be inside."

Instead of treating these as three independent events, RESQ uses
Hindsight to connect them into one evolving incident.

## 🔥 Before vs After

### Without Memory

Report → Analyze → Decision

### With Hindsight

Report → Recall → Correlate → Update → Decision

## 🛠️ Tech Stack

- Python
- AI/LLM
- Hindsight
- Flask/FastAPI
- REST API
- JSON
- [Your frontend technology]

## 📁 Project Structure

[brief explanation]

## 🚀 Installation

```bash
git clone YOUR_REPOSITORY_URL
cd RESQ
pip install -r requirements.txt
