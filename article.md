# How Hindsight Turned Emergency Noise Into Incident Context

In an emergency system, the hardest problem is not receiving another report. It is understanding that the fifth report may actually be about the same incident as the first.

That distinction changed how I designed RESQ.

## The Problem: Reports Are Not Incidents

Emergency information rarely arrives as a clean event stream.

One person might send:

> “There’s a fire near the main entrance. Smoke everywhere.”

A few minutes later, another person uploads a photograph and writes:

> “Building on fire. People may still be inside.”

Then a field responder reports:

> “Heavy smoke observed from the east side.”

A naive system can treat those as three independent records.

Operationally, they may represent one incident.

That creates a subtle problem. Deduplication based only on location or timestamp is not enough. Reports can arrive at different times, use different language, contain different evidence, and come from different sources.

I wanted RESQ to reason about the **history surrounding an incident**, not just the latest message.

That is where I introduced [Hindsight, an open-source agent memory system](https://github.com/vectorize-io/hindsight).

Hindsight is designed around persistent agent memory rather than simply replaying conversation history. Its core operations are **retain, recall, and reflect**, giving an agent a way to store information, retrieve relevant memories, and derive higher-level observations from previous experience.

## How RESQ Fits Together

RESQ starts with unstructured emergency reports and turns them into operational information.

At a high level, the flow looks like this:

```text
Citizen / Responder Reports
          |
          v
   Information Extraction
          |
          v
    Incident Candidates
          |
          v
   Hindsight Memory Layer
          |
          v
  Context + Evidence Retrieval
          |
          v
 Incident Correlation
          |
          v
 Priority Assessment
          |
          v
 Resource Recommendation
          |
          v
 Human Approval
          |
          v
      Dispatch
```

The important part is the memory layer.

I don't want an AI model deciding whether two reports are related using only whatever happens to be inside its current context window.

Instead, I want the system to retrieve relevant historical information before making that decision.

That sounds like a small architectural change. It isn't.

It changes what the system considers an incident.

## I Stopped Treating Every Report as a New Event

The first version of the mental model was simple:

```python
incident = create_incident(report)
```

That works until multiple sources describe the same event.

The real question becomes:

```python
context = retrieve_related_context(report)

incident = correlate(
    report=report,
    context=context
)
```

The difference is important.

The first implementation asks:

> “What incident does this report create?”

The second asks:

> “What does this report mean given what I already know?”

That is the reason I consider agent memory different from ordinary application state.

Application state tells me what is true **now**.

Memory helps me reason about **what happened before**.

The distinction is also central to how [agent memory is described by Vectorize](https://vectorize.io/what-is-agent-memory): the goal is not simply to retain conversation history, but to give agents useful long-term context for future decisions.

## What I Store in Memory

I don't want to dump every incoming message into a memory system.

That creates another problem: noisy memory.

Instead, I treat useful incident information as structured context.

For example:

```python
report = {
    "source": "citizen",
    "timestamp": "2026-09-29T10:14:00",
    "location": {
        "lat": 17.493,
        "lon": 78.391
    },
    "description": "Heavy smoke coming from the building entrance",
    "evidence": ["image"]
}
```

After extraction, the system can associate facts with an incident:

```python
memory = {
    "incident_id": "INC-1042",
    "facts": [
        "fire reported near main entrance",
        "heavy smoke observed",
        "possible occupants inside"
    ],
    "location": "building entrance",
    "sources": [
        "citizen-report-1",
        "citizen-report-2",
        "field-report-7"
    ]
}
```

The exact production representation can evolve, but the design principle stays the same:

**Store information that will be useful when a future report arrives.**

That sounds obvious. In practice, deciding what deserves to become memory is one of the harder parts of the system.

Hindsight's `retain` operation is designed for this kind of ingestion: information is passed into a memory bank, where it can later be retrieved through `recall`. The system also supports `reflect` for deriving broader observations from accumulated memories. See the [Hindsight documentation](https://hindsight.vectorize.io/) for the underlying API and architecture.

## Retrieval Comes Before Reasoning

This is the part I care about most.

Suppose a new report arrives:

> “Smoke has increased. People are gathering outside.”

Without memory, the model sees one message.

With memory, I can retrieve something like:

```text
INC-1042

- Fire reported 8 minutes ago
- Second citizen reported flames
- Image evidence attached
- Possible occupants inside
- Field responder confirmed heavy smoke
```

Now the new report isn't interpreted in isolation.

It becomes another piece of evidence associated with an existing situation.

The pipeline becomes:

```python
context = memory.recall(
    bank_id="emergency-incidents",
    query="smoke increased people gathering outside"
)

decision = analyze(
    report=new_report,
    context=context
)
```

This is where Hindsight becomes more than a storage component.

The value isn't simply that information was saved.

The value is that previously observed information becomes available when it changes the interpretation of new information.

## Correlation Is More Than Similarity

A common temptation is to solve incident correlation with embeddings alone.

Take a report, embed it, search for similar reports, and merge anything above a similarity threshold.

That is useful, but I don't think it is sufficient for emergency workflows.

Consider these two reports:

> “Fire reported at Building A.”

and:

> “Fire reported at Building A yesterday.”

Semantically, they're extremely similar.

Operationally, they may be completely different incidents.

That means correlation needs multiple signals:

```python
correlation = {
    "semantic_similarity": similarity_score,
    "location_match": location_score,
    "time_proximity": time_score,
    "incident_history": retrieved_context
}
```

The important signal here is the last one.

Memory gives the system access to the existing narrative around a location or incident instead of forcing every decision to start from zero.

I would rather have several imperfect signals supporting one correlation decision than pretend that one similarity score represents the whole situation.

## From Reports to Evidence

Once reports are correlated, I can combine their evidence.

Imagine three reports:

```text
Report A
"Smoke coming from second floor."

Report B
"Fire visible through east window."

Report C
"Two people may still be inside."
```

Individually, each report is incomplete.

Together, they tell a much stronger story.

The system can maintain an incident context such as:

```python
incident_context = {
    "hazard": "building_fire",
    "location": "Building A",
    "severity_signals": [
        "active flames",
        "heavy smoke",
        "possible occupants"
    ],
    "evidence_count": 3,
    "last_update": "10:22:41"
}
```

That context can then feed the next stage of the system.

This is where I found the distinction between **retrieval** and **memory** useful.

Retrieval answers:

> “What information is similar to this query?”

Memory answers a more useful question for an agent:

> “What previously learned information should influence what I do now?”

Those aren't identical problems.

## Priority Should Be Explainable

After correlation, RESQ needs to determine how urgently an incident requires attention.

I deliberately don't want a mysterious:

```text
priority = 0.97
```

with no explanation.

Instead, the system should be able to surface the evidence behind its recommendation:

```text
Priority: CRITICAL

Reasons:
- Active fire reported
- Visual evidence indicates flames
- Multiple independent reports agree
- Possible occupants inside
- Field responder confirmed heavy smoke
```

The decision can still be model-assisted, but the operational interface should expose the reasoning signals.

This matters because emergency response is not a domain where I want an AI system quietly making irreversible decisions.

RESQ can recommend.

A human responder remains responsible for consequential actions such as dispatch.

## Memory Also Helps With Incident Evolution

Another reason I wanted persistent memory was that an incident is not static.

At 10:05:

```text
Fire reported.
```

At 10:11:

```text
Flames visible.
```

At 10:15:

```text
Possible occupants inside.
```

At 10:20:

```text
Evacuation underway.
```

At 10:31:

```text
Fire contained.
```

The current state tells me what is happening now.

The historical context tells me how the incident developed.

That distinction becomes important when responders need to understand why an incident was classified as critical even if the latest update looks less severe.

Instead of losing earlier evidence when the state changes, the system retains the chain of observations.

That is the core reason I chose Hindsight as part of the architecture rather than treating memory as an afterthought.

## Resource Recommendation Becomes Context-Aware

Once the incident has enough context, RESQ can recommend an available response resource.

For example:

```text
Incident: INC-1042
Type: Building fire
Priority: Critical

Recommended:
Fire Response Unit 03

Reasons:
- Fire capability required
- Unit currently available
- Closest available fire-response resource
- Incident severity requires immediate response
```

Again, the recommendation is not the same thing as dispatch.

The system can move the incident through a state machine:

```text
REPORTED
    ↓
AI_ANALYZED
    ↓
CORRELATED
    ↓
VERIFIED
    ↓
RESOURCE_RECOMMENDED
    ↓
HUMAN_APPROVAL
    ↓
DISPATCHED
    ↓
RESOLVED
```

This makes the boundary between AI assistance and human authority explicit.

## What Changed After Adding Memory

The most important change wasn't a new UI component.

It was the unit of reasoning.

Before persistent context, the system naturally revolved around:

```text
report → prediction
```

With memory, it becomes:

```text
report
   ↓
retrieve context
   ↓
interpret report
   ↓
update incident memory
   ↓
make recommendation
```

That is a fundamentally different architecture.

The agent no longer needs to rediscover the entire situation every time another message arrives.

It can build on previous observations.

For a system like RESQ, that matters because emergencies are sequences of events, not isolated prompts.

## Lessons I Took Away

### 1. Memory is not the same thing as a database

A database answers questions about stored records.

An agent memory layer needs to help retrieve information that changes the interpretation of a new situation.

Those goals overlap, but they aren't interchangeable.

### 2. Don't store everything

If every message becomes permanent memory, retrieval eventually becomes noisy.

I would rather store fewer pieces of useful context than create a giant archive that an agent cannot reliably navigate.

### 3. Correlation needs multiple signals

Semantic similarity is useful.

It is not enough by itself.

Time, location, source, evidence, and historical context all matter when deciding whether two reports describe the same incident.

### 4. Historical context can be operational evidence

A current message may look harmless in isolation.

When combined with previous reports, it may become significant.

That is one of the strongest arguments for persistent agent memory in systems where situations evolve over time.

### 5. Keep humans in the consequential loop

I don't want an AI system silently turning uncertain observations into irreversible actions.

RESQ can organize information, correlate reports, explain urgency, and recommend resources.

The final operational decision remains explicit.

## The Design Principle I Would Keep

The biggest lesson from building this system is simple:

**An emergency report is rarely the whole story.**

The useful information is often distributed across messages, timestamps, locations, images, responders, and previous decisions.

If an agent only sees the latest message, it is constantly starting over.

Persistent memory changes that.

With Hindsight, I can give the agent a mechanism for retaining relevant experience and retrieving it when a new observation arrives. Hindsight also provides `reflect`, which can derive higher-level observations from accumulated memories rather than treating every memory as an isolated fact.

The result isn't simply a system that remembers more.

It is a system that can interpret new information in the context of what already happened.

That is the distinction I would carry into any agent that operates over long-running, evolving workflows:

**Don't just give the model more context. Give it a way to remember the context that will matter later.**

**Published article:**  
[Read the full article](https://medium.com/@javvajibhuvi01/how-hindsight-turned-emergency-noise-into-incident-context-33535f974429?sharedUserId=javvajibhuvi01)
