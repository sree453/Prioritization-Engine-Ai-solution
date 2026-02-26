# Prioritization-Engine-Ai-solution
System architecture and decision logic for an AI-driven notification engine—handling high-volume events with Now/Later/Never classification.
[cite_start]This repository contains the system architecture and design for a Notification Prioritization Engine, developed for the **Cyepro Solutions** Round 1 Solution Crafting Test[cite: 1, 3]. [cite_start]The engine classifies incoming notification events into **Now, Later, or Never** to reduce alert fatigue and improve user experience[cite: 7, 11].

## 1. High-Level Architecture
[cite_start]The system is designed for high event volume and low latency[cite: 29, 30].

```mermaid
graph TD
    classDef client fill:#f9f,stroke:#333,stroke-width:2px;
    classDef service fill:#fff,stroke:#333,stroke-width:2px;
    classDef storage fill:#e1f5fe,stroke:#01579b,stroke-width:2px;
    classDef engine fill:#fff9c4,stroke:#fbc02d,stroke-width:2px;
    classDef outcome fill:#dcedc8,stroke:#33691e,stroke-width:2px;

    Start((Client Services)):::client --> Gateway[API Gateway]:::service
    Gateway --> Ingest[Ingestion Service]:::service
    Ingest --> Kafka[(Message Queue: Kafka)]:::storage
    
    subgraph Processing_Layer [Core Processing Logic]
        Kafka --> Dedupe[Deduplication Layer: Redis]:::storage
        Dedupe --> AI[Prioritization Engine: Rules + AI]:::engine
        AI --> Log[Explanation Logger]:::service
    end

    Log --> Router{Decision Router}:::engine

    Router -->|High Priority| Now[NOW: Dispatcher]:::outcome
    Router -->|Medium/Low| Later[LATER: Scheduler]:::outcome
    Router -->|Redundant/Low Value| Never[NEVER: Suppressed]:::outcome

    Now --> User((User Notification)):::client
    Later --> DB[(Storage for Digest)]:::storage
