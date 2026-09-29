# ResQ
# RESQ — Real-Time Emergency Situation Intelligence

RESQ transforms fragmented emergency reports into
context-aware incidents using AI and persistent agent memory.

## Core Problem

A single emergency can generate multiple reports:

- Citizen text reports
- Images
- Responder updates
- Location information
- Incident updates

Instead of treating every report as a separate event,
RESQ correlates them into an evolving incident.

## Hindsight Integration

RESQ uses Hindsight as its persistent agent memory layer.

The system:

1. Receives an emergency report
2. Extracts structured information
3. Recalls relevant previous context
4. Correlates the report with existing incidents
5. Updates incident memory
6. Assesses priority
7. Recommends a response resource
8. Requests human approval

### Without memory

Report → Analyze → Decision

### With Hindsight

Report → Recall → Correlate → Update Memory → Decision
